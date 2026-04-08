(docs-homepage-pattern)=
# Docs homepage pattern

This prompt instructs a client/agent to:

1. Study the docs in the repo (under `docs/` or `doc/`).
1. Study the current homepage and use its content where appropriate.
1. Study the homepage ["lorem ipsum" template](../templates/standard-hp.md).
1. Ensure all docs pages have proper heading anchors.
1. Draft a homepage based on the findings and these instructions.
1. Complete a final checklist.
1. Perform an audit based on [homepage audit prompt](https://gist.githubusercontent.com/evildmp/3a33ef3da7c0c37331eb7e723a00038e/raw/ec695f766f29f7b8f606e54c96c4017a5682b175/Documentation%2520home%2520page%2520report%2520guidelines.md) and adjust based
on results.

Created with:
: * LLM: Claude Sonnet 4.6
  * Agent: GitHub Copilot

-----

````md
# Canonical Documentation Homepage Pattern — Instructions

## Context and purpose

Canonical documentation projects use the [Diátaxis](https://diataxis.fr/)
structure (always refer to Diátaxis using this exact spelling) and are built
with Sphinx. The homepage is the primary entry point to the docs set.

This document instructs an agent to implement a standardized homepage pattern.
The result is a structured, topic-oriented homepage that lets users navigate
directly to any topic without relying solely on the sidebar hierarchy.


---

## Markup format

Canonical docs sets use MyST Markdown (`.md`), reStructuredText (`.rst`), or a
combination of both. Before doing anything else, inspect the docs set to
determine which format is in use. Apply the corresponding markup throughout.

All examples in these instructions use MyST Markdown. Where the docs set uses
rST instead, apply the equivalent rST syntax. The key equivalences are:

| Element | MyST Markdown | reStructuredText |
|---|---|---|
| Homepage filename | `docs/index.md` | `docs/index.rst` |
| Page-level anchor | `(anchor-name)=` (line before H1) | `.. _anchor-name:` (line before title) |
| Cross-reference | `` {ref}`Display text <anchor>` `` | `` :ref:`Display text <anchor>` `` |
| Toctree directive | `` ```{toctree} `` / `:::{toctree}` MyST block | `.. toctree::` rST directive |
| Bold text | `**bold**` | `**bold**` |

For docs sets that mix both formats, follow the format of the homepage file
itself, and match the format of each source file when adding anchors to it.

The homepage has four top-level sections:

1. Introductory paragraphs
2. "In this documentation" — a topic-oriented link map
3. "How this documentation is organized" — Diátaxis section descriptions
4. "Project and community" — support, governance, commercial links


---

## Link-text rules

Link text must only describe the destination resource and must omit
instructional verbs, which should instead be placed in the surrounding unlinked
prose. The following examples show some bad practices that must be avoided, and
accompanying suggestion for better ohrasing:

- "Report issues in this documentation" must be changed to a descriptive noun
phrase, such as "Issue tracker".
- "Contribute to this documentation" must be changed to a descriptive noun
phrase, such as "Documentation repository" or "Contribution guide".
- "Matrix: Discuss Ubuntu" must be changed to remove the verb, such as "Matrix
channel".
- "Get it." must be changed to describe the resource, such as "Commercial
support options".

"Ask Ubuntu" functions as a proper noun for a specific platform, but any
accompanying instructional context should remain in the unlinked prose.

## Phase 1: Analyze before writing

Read the actual documentation in this repo (usually under `docs/` or `doc/`)
thoroughly. Do not write a single line of the homepage until you understand the
following:

- **Product**: its name, purpose, and what it does
- **Audience**: who uses it and why, as stated or implied in the docs
- **Content inventory**: every major page across all sections (tutorials,
how-to, reference, explanation), including its title and what it covers
- **Existing homepage** (`index.md` or `index.rst`): the current homepage may
contain accurate content worth preserving; reuse it where still relevant rather
than reinventing
- **Anchor inventory**: every existing page-level anchor across all source files
- **Homepage template**: review the provided "lorem ipsum"
[template](./standard-hp) to understand the desired homepage structure.

Collect this information systematically. Do not proceed until you have a
complete picture.


---

## Phase 2: Anchor management

Before editing the homepage, ensure every page that will be linked from it has a
page-level anchor.

**How to identify missing anchors:** A page-level anchor appears on the line
immediately before the H1 heading. If no `(anchor-name)=` line precedes `# Page
Title`, the page lacks one.

**How to add a missing anchor:**

```markdown
(anchor-name)=
# Page Title
```

**Anchor naming rules:**

- Use kebab-case.
- Derive the name from the page's filename or its primary subject, whichever is
more descriptive and consistent with existing anchors in the project.
- Study several existing anchors in the project to identify the convention in
use before adding new ones.
- Do not use Diataxis-type prefixes (e.g., `how-`, `ref-`, `exp-`) unless the
project already uses that convention.
- Typical pattern: filename-derived slugs such as `install-ubuntu`, `packaging`,
`ides`, `debugging-with-dotnet`.

Add anchors to source files before writing the homepage. The homepage must use
only `{ref}` links to named anchors — never `{doc}` links.


---

## Phase 3: Write the homepage

### 3.1 Page anchor and title

```markdown
(product-slug)=
# Product Name
```

Use the existing page anchor if present; otherwise derive a kebab-case slug from
the product name.

---

### 3.2 Toctree

```markdown
:::{toctree}
---
maxdepth: 2
hidden: true
---
tutorials/index
howto/index
reference/index
explanation/index
:::
```

Adapt entries to match the actual top-level sections of this docs set. Preserve
any additional toctree entries from the existing homepage (e.g., a contribution
guide).

---

### 3.3 Introductory paragraphs

Write exactly four paragraphs. Each opens with a **bold sentence** that states
the core point; the remainder of the paragraph (if any) expands on it in plain
text.

| Paragraph | Bold opener covers | Remainder covers |
|---|---|---|
| 1 | What the product is | (none required — one bold sentence suffices) |
| 2 | What the product does | Specifics: key capabilities, components, or integrations |
| 3 | What need it meets / what the docs cover | Scope of the documentation |
| 4 | Who the documentation is for | Audience qualifications; what it does and does not teach |

**Rules:**
- Present simple tense throughout.
- No slang, no flowery language, no clichés ("powerful", "seamless", "deep
dive").
- Every claim must be directly grounded in the actual docs — do not invent.
- Paragraphs 2–4 are one to three sentences each; prefer brevity.

---

### 3.4 "In this documentation"

This is the most important section. It provides a topic-oriented map of the
entire docs set — organized by subject, not by Diataxis type — so users can
navigate directly to any area of interest.

```markdown
## In this documentation

<intro sentence>


### <Subsection heading>

<intro sentence>

* **<Topic label>**: {ref}`Link text <anchor>` • {ref}`Link text <anchor>`
* **<Topic label>**: {ref}`Link text <anchor>`


### <Subsection heading>

<intro sentence>

* **<Topic label>**: {ref}`Link text <anchor>`

```

#### Intro sentence under `## In this documentation`

Write one sentence explaining that this section organizes pages by subject
rather than by documentation type. It must be insightful — not a restatement of
the heading. It should articulate *why* this view is useful (direct access,
topic coherence, alternative to sidebar navigation).

#### Subsections

Design 3–5 subsections whose headings reflect the dominant activities or topic
clusters in the docs set. Derive headings from the actual content structure; do
not invent categories. Examples for toolchain-focused docs: "Getting to 'Hello,
world!'", "Installing and configuring toolchains", "Toolchain availability",
"Background and context". Other docs sets will have different natural groupings.

**Each subsection gets one intro sentence** that adds meaning beyond the
heading: the purpose of the content, what it helps users do, or a key constraint
or scope note. Do not repeat the heading in paraphrase.

#### Rows within each subsection

Each bullet is a **row**. The bold label before the colon is the topic name; the
links after the colon cover that topic.

```markdown
* **Topic**: {ref}`Page title <anchor>` • {ref}`Page title <anchor>`

```

**Row design rules:**

- **Organize rows by concept or subject** — a component, a toolchain, a
technology, an activity — not by Diataxis type. The label "Tutorial" or "How-to"
is never an appropriate row label.
- **Cross Diataxis boundaries within a row.** A single row may link a tutorial,
a how-to guide, a reference page, and an explanation page if they all address
the same topic. This is the key mechanism that makes the section topic-oriented
rather than structure-oriented.
- **Cover all major pages.** Every significant page in the docs set should
appear in at least one row. Do not omit important content.
- **Do not pad.** Do not create rows for minor or supporting pages (reuse
fragments, auto-generated pages, stub pages).
- **Separate links within a row** with ` • ` (space–bullet–space). A row with a
single link needs no separator.
- **Row labels** are short, concrete nouns or noun phrases drawn from the actual
subject matter.

---

### 3.5 "How this documentation is organized"

```markdown
## How this documentation is organized

This documentation uses the [Diátaxis documentation structure](https://diataxis.fr/).

* {ref}`Tutorials <tutorials>` ...
* {ref}`How-to guides <howto>` ...
* {ref}`Reference <reference>` ...
* {ref}`Explanation <explanation>` ...

```

One bullet per Diataxis section. Each bullet links the section index and then
describes in one clause what *this specific docs set's* section actually
contains — not generic Diátaxis definitions. Name specific content: what the
tutorials teach, what the how-to guides install, what the reference covers, what
the explanation discusses.

---

### 3.6 "Project and community"

```markdown
## Project and community

<one sentence placing the project in its broader ecosystem>


### Get involved

* [Support channel](url)
* [Chat channel](url)
* [Report issues](url)
* {ref}`Contribute to documentation <contribute-docs>`


### Governance and policies

* [Code of conduct](https://ubuntu.com/community/ethos/code-of-conduct)


### Commercial support

<one sentence with a call to action and link>
```

- Use URLs and link text from the existing homepage wherever available.
- Omit subsections (e.g., Releases, Roadmap) only if no corresponding content or
link exists in the project.
- The opening paragraph is one sentence; it should say something specific about
the project's relationship to its ecosystem, not just "it is open source".

---

## Writing style

Apply these rules to every line of text on the homepage:

1. **Present simple tense.** Not "you will be able to" — "this section covers".
2. **Omit needless words.** Cut every adjective and adverb that carries no
information.
3. **No clichés or marketing language.** Forbidden: "powerful", "robust",
"seamless", "comprehensive", "cutting-edge", "dive into", "out of the box".
4. **Concrete over abstract.** Name specific technologies, pages, and tools
rather than speaking in generalities.
5. **Consistent terminology.** Use the same names for the product and its
components as the docs themselves use.
6. **Intro sentences must add insight.** Every intro sentence (under `## In this
documentation` and under each `###`) must say something the heading alone does
not. It should explain purpose, scope, a key constraint, or the reason to
consult the section. Rewordings of the heading are not acceptable.


---

## Quality checklist

Before finishing, verify each item:

- [ ] Every `{ref}` anchor used in `index.md` exists in a source file.
- [ ] Every major page in the docs set appears in the "In this documentation"
link table.
- [ ] No content is invented — every claim traces back to the actual docs.
- [ ] Each intro sentence (section-level and subsection-level) adds meaning
beyond its heading.
- [ ] All text is in present simple tense.
- [ ] No `{doc}` links are used — only `{ref}` links to named anchors.
- [ ] Pages that had no anchor before this task now have one, added to their
source file.
- [ ] The existing homepage's accurate, reusable content has been preserved or
incorporated where relevant.


---

## Final audit

After successfully completing the "Quality checklist" above, do a quality audit:
study [How to audit a documentation home page](https://gist.githubusercontent.com/evildmp/3a33ef3da7c0c37331eb7e723a00038e/raw/ec695f766f29f7b8f606e54c96c4017a5682b175/Documentation%2520home%2520page%2520report%2520guidelines.md) and based on it
perform an audit of the homepage. Analyze the findings from the audit and adjust
the homepage to address the found problems. Rinse and repeat until you achieve
an outcome that yields an all-clear result from the audit.
````
