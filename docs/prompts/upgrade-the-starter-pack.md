(upgrade-the-starter-pack)=
# Upgrade the Starter Pack

This is a tested, exhaustive prompt for upgrading the [Docs Starter Pack](https://github.com/canonical/sphinx-docs-starter-pack) installation in a repo that employs standard docs-build logic. I.e., running `make html` and co. must work from the `docs/` directory.

**General notes:**

- Format (rST/MyST) agnostic.
- Strict instructions to never lose any repo-specific config.
- Presents a report of upstream changes and local customizations.
- Provides a plan of action before changing anything, allowing for adjustments.

**Linting and checks:**

- Includes instructions for working around current `vale` rules bugs and limitations (to be updated when fixed rule set available).
- Similarly, workarounds are included for `pymarkdownlint`, which has no MyST support.
- Final checklist requires passing all checks and lints; remove any (except `make html`) that you don't want to deal with during the upgrade.
- Requires the use of `ruff` and `black` (Python linter and formatter) on `conf.py`; this is stricter than what upstream Starter Pack uses. Remove if not desired.

Created with:
: * LLM: Claude Sonnet 4.6
  * Agent: GitHub Copilot

-----

````md
# AI Instructions: Upgrade the Sphinx Docs Starter Pack

## Task

Use these instructions to upgrade a Canonical documentation repository from one
version of the [Sphinx Docs Starter Pack](https://github.com/canonical/sphinx-docs-starter-pack)
(SP) to the latest available version. The instructions are repo-agnostic; adapt
specifics to the repo at hand.

Study the instructions and linked Starter Pack resources carefully, and proceed when ready.

## Markup format

A docs set may use MyST Markdown, reStructuredText (rST), or a combination of
both. These instructions are format-agnostic. Where markup syntax appears in
examples or grep patterns, MyST notation is used, but the same guidance applies
to rST equivalents:

| Element | MyST | rST |
|---|---|---|
| Role | `` {role}`content` `` | `` :role:`content` `` |
| Directive | `` ```{name} `` (fenced block) | `.. name::` |

**When editing any source file, always use the markup format that file already
uses.** When grepping for directive or role usage, search for both patterns.

---

## Pre-flight

1. Identify the current SP version (`docs/.sphinx/version`).
2. Identify the latest SP version from the upstream repo or its changelog.
3. Run `docs/.sphinx/update_sp.py` and capture its output — it provides a
   diff-based starting point but does not cover everything.
4. Read the upstream upgrade guide:
   https://canonical-starter-pack.readthedocs-hosted.com/stable/how-to/update-starter-packs/new-starter-pack/
5. Study the upstream `conf.py`, `Makefile`, `requirements.txt`, and
   `.github/workflows/` to understand what changed between versions.
6. Catalogue all custom content in the repo: custom extensions, custom roles,
   `extlinks`, `intersphinx_mapping`, any custom Python in `conf.py`, custom
   Makefile targets, etc. Document these explicitly before proceeding — they
   must all survive the upgrade.
7. **IMPORTANT:** Before starting with implementation -- before touching
   anything -- provide a complete plan of action, and ask for explicit
   confirmation. List all planned modifications, additions, removals, and any
   other identified steps. Structure your plan as follows:

   - New things in upstream SP
   - Custom local settings to be preserved
   - Questions that need user decision

---

## Analysis: what to check in each file

### `requirements.txt`

- SP v1.4+ uses **atomic extensions** instead of `canonical-sphinx[full]`.
  The full list of upstream-managed packages lives in the upstream
  `requirements.txt`; replicate structure exactly.
- **Audit which extensions are actually used in the docs set before deciding
  what to keep.** Do not assume an extension is used just because it is in
  `requirements.txt`. Do not assume it is unused just because grep finds no
  directive usage — some extensions provide roles (`:pkg:`, `:lpsrc:`,
  `:spellexception:`, etc.) that may be used widely but are easy to miss.
- Grep for: directive names (e.g. `{terminal}` / `.. terminal::`, `{mermaid}` /
  `.. mermaid::`, `{tabs}` / `.. tabs::`), roles (`` {pkg}` `` / `:pkg:`,
  `` {lpsrc}` `` / `:lpsrc:`, `` {spellexception}` `` / `:spellexception:`,
  etc.), and intersphinx references (`{external+...}` / `:external+...:`).
- Check for SVG images — they require `sphinxcontrib.cairosvgconverter`.
- Keep project-specific packages in a clearly commented section at the bottom.
- **`sphinx-markdown-builder`**: this package is a *transitive* dependency of
  `sphinx-llm` (used internally for `llms.txt` generation). It does **not**
  need to appear in `requirements.txt` explicitly, but its presence in the venv
  means `_build/` will contain generated `.md` files. Exclude `_build` from
  `CHECK_PATH` (see Makefile section) to prevent Vale false positives.

### `conf.py`

- **Copyright**: SP v1.4+ uses `f"{datetime.date.today().year}, Canonical Ltd"`.
- **Extensions list**: Sort upstream extensions first; project-specific below
  with a comment separator. See "Known gotchas" below for `canonical_sphinx`
  loading behaviour — certain extensions must be listed explicitly even if
  `canonical_sphinx` nominally loads them.
- **`exclude_patterns`**: Must include `".venv"` (see "Known gotchas").
- **`rst_prolog`**: Upstream may add new substitution roles (`h2`, `woke-ignore`,
  `vale-ignore`); add them without removing existing custom ones.
- **`ogp_image`**: Upstream default is no custom image. Remove if the repo has
  no specific need; ask the user if unsure.
- **`sitemap_url_scheme`**: Upstream default is no custom scheme. Remove unless
  the repo has a specific reason for it.
- **`redirects` / `rediraffe_redirects`**: `sphinx_reredirects` used `redirects = {}`;
  `sphinx_rerediraffe` uses `rediraffe_redirects`. See "Known gotchas".
- **`extlinks`**, **`intersphinx_mapping`**, **custom roles/functions**: These are
  always repo-specific. Never remove them without explicit confirmation.
  Restore immediately if a build reveals they are needed.
- **Python formatting**: Run `ruff` and `black` on `conf.py` after any edits.
  Many repos enforce stricter formatting than upstream. If not available
  globally, run from the venv: `.venv/bin/ruff check docs/conf.py` and
  `.venv/bin/black docs/conf.py`.

### `Makefile`

- SP v1.4+ renames variables (`VENVDIR→DOCS_VENVDIR`, `TARGET→CHECK_PATH`,
  `SPHINXDIR→SPHINX_DIR`, etc.) and removes the `allmetrics` target.
- `html` target: `-W` becomes `--fail-on-warning --keep-going`.
- If the repo's `Makefile` has no custom targets, replace it wholesale with
  the upstream version. If it does have custom targets, merge carefully.
- **`CHECK_PATH`**: The upstream default `$(filter-out $(DOCS_VENVDIR),$(wildcard *))`
  includes `_build/`. If the repo uses `sphinx-markdown-builder` or any other
  builder that outputs `.md` files under `_build/`, Vale will flag errors in
  those generated files. Exclude `_build` explicitly:
  ```make
  CHECK_PATH ?= $(filter-out $(DOCS_VENVDIR) _build,$(wildcard *))
  ```

### `.gitignore`

- Venv path changed from `.sphinx/venv/` to `.venv/`; update accordingly.

### GitHub Actions (`.github/workflows/`)

- **Audit all workflow files**, not just `automatic-doc-checks.yml`. Repos
  commonly also have `check-removed-urls.yml` and `markdown-style-checks.yml`.
- **`automatic-doc-checks.yml`**: add `fetch-depth: 0` to the checkout step
  (required by `sphinx_last_updated_by_git`); scope the `push`/`pull_request`
  trigger to `docs/**` if not already done.
- **`check-removed-urls.yml`**: the "Build docs" step may hard-code the old
  venv activation path (`. .sphinx/venv/bin/activate`). With SP v1.5+, the
  venv moves to `.venv/` and `make html` activates it automatically; remove
  any explicit `make install` + `. .sphinx/venv/bin/activate` lines and
  replace the entire step with just `make html`.
- **`markdown-style-checks.yml`**: update the runner version to match upstream
  (e.g. `ubuntu-22.04` → `ubuntu-24.04`).
- Diff every workflow against its upstream counterpart carefully; preserve any
  repo-specific environment variables, secrets, or deployment steps.

### `.readthedocs.yaml`

- Compare `os:` and `python:` versions against upstream — the repo may
  intentionally run a newer OS/Python than upstream specifies; confirm before
  changing.
- The `post_checkout` cancel-on-no-diff block and `sphinx:` / `formats:` /
  `python:` sections are otherwise stable across SP versions; verify they
  match upstream structure.
- Do not overlook this file — it is easy to miss because it lives at the repo
  root rather than under `docs/`.

### `.sphinx/` update files

After running `update_sp.py`, copy the resulting updated files:
- `update_sp.py` → `docs/.sphinx/update_sp.py`
- `get_vale_conf.py` → `docs/.sphinx/get_vale_conf.py`
- Update `docs/.sphinx/version` to the new SP version string.

---

## Known gotchas (version-specific)

### `canonical_sphinx` v0.6: explicit extension loading required

`canonical_sphinx` v0.6 auto-loads several extensions using **old module names**
that no longer match the installed package names. This means extensions that
appear to be loaded by `canonical_sphinx` may silently fail to load.

**Always list these explicitly in `extensions`**, regardless of what
`canonical_sphinx` claims to load:
- `sphinx_roles` — provides `:spellexception:` and other roles
- `sphinx_terminal` — provides the `{terminal}` directive

Extensions that `canonical_sphinx` v0.6 does load correctly:
`myst_parser`, `sphinx_design`, `sphinx_tabs.tabs`, `sphinx_copybutton`,
`sphinxext.opengraph`, `sphinxcontrib.jquery`, `notfound.extension`.

If the build emits "Unknown directive type" or "Unknown role" for anything
that was supposedly provided by `canonical_sphinx`, add it explicitly.

### `sphinx-terminal` v1.0.x: breaking directive syntax change

The `terminal` directive syntax changed in `sphinx-terminal` v1.0 (applies to
both MyST and rST usage):

| | Old (pre-1.0) | New (1.0+) |
|---|---|---|
| Command | `:input: COMMAND` option | First non-empty line of body |
| Output | Entire body | Body after blank line following command |
| Separator | None needed | Blank line between command and output |

Example migration:
```
# Old
{terminal}
:input: sudo apt install foo
Package installed.

# New
{terminal}
sudo apt install foo

Package installed.
```

Valid options in v1.0+: `class`, `copy`, `dir`, `host`, `output-only`,
`scroll`, `user`. The `input` option is gone.

Scan all source files for `terminal` directives (`` {terminal} `` in MyST,
`.. terminal::` in rST) that use the old `:input:` option and migrate them.
A Python script is the most reliable approach for bulk migration.

### `sphinx_rerediraffe`: no-redirects warning breaks build

`sphinx_rerediraffe` (which replaced `sphinx_reredirects`) emits a `WARNING`
if loaded when no `rediraffe_redirects` are configured. With
`--fail-on-warning`, this breaks the build.

**Rule**: Do not add `sphinx_rerediraffe` to `extensions` unless the repo
actually has redirects to configure. Instead, leave it commented out:

```python
# Uncomment and populate when redirects are needed:
# rediraffe_redirects = {}
```

**If the repo has existing redirects** defined in `redirects = {}` (the
`sphinx_reredirects` format), they must be converted — the two extensions use
different mapping conventions:

| | `sphinx_reredirects` | `sphinx_rerediraffe` |
|---|---|---|
| Variable | `redirects` | `rediraffe_redirects` |
| Key | source path (no extension) | source file (with extension, e.g. `.md` or `.rst`) |
| Value | target URL or HTML path | target source file (with extension) |

Convert every entry, add `sphinx_rerediraffe` to `extensions`, and verify the
redirects still resolve correctly in a build. Do not silently drop existing
redirects.

### `.venv` inside `docs/`: Sphinx scans venv templates

When the venv lives at `docs/.venv/` (SP v1.4+ default), Sphinx's source
scanner will descend into it and attempt to parse Jinja2/autosummary template
files as RST, producing ~90 warnings that fail the build under `--fail-on-warning`.

Fix: add `".venv"` to `exclude_patterns` in `conf.py`:

```python
exclude_patterns = ["_build", "**.ipynb_checkpoints", ".venv"]
```

---

## Vale: known false positives

After the upgrade, run `make vale` and expect to address pre-existing content
errors (unrelated to the SP upgrade itself). Several Canonical Vale rules produce
non-obvious false positives:

- **`Canonical.003`** (`Ubuntu \d?[02468]\.04(?! LTS)`): any even-year Ubuntu
  LTS release number must be followed by ` LTS`. Add the suffix wherever missing.
  Common oversights: Ubuntu 22.04, Ubuntu 24.04, Ubuntu 26.04 without "LTS".
  Known limitation: the rule does not handle point releases like "Ubuntu 24.04.4
  LTS" (the lookahead sees `.4` not ` LTS`). Use vale suppress comments for
  point-release paragraphs: `<!-- vale Canonical.003-Ubuntu-names-versions = NO -->`.

- **`Canonical.004`** (`(?<=Ubuntu )Pro`, nonword, ignorecase): matches "pro"
  anywhere in a word after "Ubuntu". False-positive triggers include:
  "Ubuntu **pro**ject", "Ubuntu **pro**posed", "Ubuntu **pro**vides", etc.
  Fix by rephrasing so the word following "Ubuntu" does not start with "pro".
  Cannot be suppressed with `TokenIgnores` when the adjacency is created by
  Markdown link-stripping (e.g. `[...ubuntu](#anchor) process`); rephrase instead.

- **`Canonical.004` for `launchpad`**: "launchpad" in URLs, inline code spans
  (`` `https://bugs.launchpad.net/...` ``), and `` [`text`](url) `` backtick-link
  combos triggers this rule even with `TokenIgnores = (launchpad\.net)`. Vale only
  applies `TokenIgnores` once per token match, so when two `launchpad.net` links
  appear in the same parsed sentence the second one still fires. These are
  unresolvable false positives with current Vale tokenization; accept or suppress.

- **`_build/` false positives**: if `sphinx-markdown-builder` is in use,
  `_build/` contains generated `.md` files that may include content from
  `reuse/` directories not directly scanned by Vale. These errors are
  misleading. The fix is to exclude `_build` from `CHECK_PATH` (see Makefile
  section above). Verify errors are build artifacts before editing source.

### Recommended vale.ini TokenIgnores (for `[*.md]` section)

```ini
TokenIgnores = (https?://\S+)
TokenIgnores = (launchpad\.net)
TokenIgnores = (`[^`\n]+`)
TokenIgnores = (launchpadlib)
TokenIgnores = (?i:Ubuntu pro\w+)
```

### Recommended vale.ini TokenIgnores (for `[*.rst]` section)

```ini
TokenIgnores = (\`[^\`\n]+\`)
TokenIgnores = (launchpad\.net)
TokenIgnores = (launchpadlib)
TokenIgnores = (?i:Ubuntu pro\w+)
```

## pymarkdownlnt: known rule conflicts

- **MD022 (`blanks-around-headings`)**: the project style can use 2 blank lines above
  headings (not 1), and MyST anchors `(name)=\n# Heading` produce 0 blank lines
  above the heading. Both patterns conflict with `lines_above_heading: 1`. Disable
  the rule entirely: set `"blanks-around-headings": {"enabled": false}` in
  `.pymarkdown.json`.

- **MD014 (`commands-show-output`)**: fires on any `$ command` shell block that
  doesn't also show output. Projects that conventionally use `$` to indicate shell
  prompts should disable this rule: `"commands-show-output": {"enabled": false}`.

## Post-upgrade spelling wordlist

After upgrading, run `make spelling` and add project-specific technical terms to
`docs/.custom_wordlist.txt`. Common terms in ubuntu-project-docs that are not in
the Canonical accept list include:

`armhf`, `Dreamwidth`, `ics`, `motu`, `openstack`, `openssh`, `pbuilder`,
`reportbug`, `rmadison`, `rustc`, `Subteam`, `wnpp`

(The Canonical list already includes `MOTUs`, `Snapcraft`, etc.)

---

## Post-upgrade checklist

Do **not** commit anything before this checklist is complete!

- [ ] `make html` — clean build, zero warnings
- [ ] `make vale` — zero errors (see false-positive notes above)
- [ ] `make spelling` — zero errors (add new terms to `.custom_wordlist.txt`)
- [ ] `make woke` — zero errors
- [ ] `make lint-md` — zero errors
- [ ] `ruff` and `black` pass on `conf.py` (run from `.venv/bin/` if not available globally)
- [ ] All custom extensions, roles, and settings verified intact
- [ ] `docs/.sphinx/version` reflects the new SP version
- [ ] `python docs/.sphinx/update_sp.py` reports no pending updates

---

## Commit guidance

Squash all upgrade work into a single atomic commit. The commit message should:

- State the version transition in the subject line:
  `Upgrade Sphinx Docs Starter Pack from vX.Y to vZ.W`
- Use bullet groups in the body: one group for SP structural changes, one for
  content/Vale fixes if any.
- Be specific but not exhaustive — name the files and changes, not the
  reasoning.
- Include the `Co-authored-by` trailer if applicable.

---

## When to ask the user

Before implementing, ask for a decision on any of the following:

- Settings present in this repo but absent in upstream (e.g. `ogp_image`,
  `sitemap_url_scheme`, custom `extlinks` keys): keep, remove, or modify?
- Whether to adopt upstream defaults for things that diverge
  (e.g. copyright format, venv location, redirect extension).
- Any case where upstream and repo custom content are in direct conflict.

Do not remove **any** custom setting without explicit confirmation.
````
