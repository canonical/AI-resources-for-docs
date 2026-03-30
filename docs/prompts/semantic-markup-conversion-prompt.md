# Semantic Markup Conversion Prompt

Use this prompt to convert documentation links to semantic markup roles for manpages, Launchpad source packages, and Launchpad bugs.

---

## Prompt

I need you to survey the documentation and convert all links to use semantic markup roles for better maintainability and consistency.

### Task Overview

1. **Check configuration**: Verify if `{manpage}`, `{lpsrc}`, and `{lpbug}` roles are defined in `docs/conf.py`
2. **Add roles if needed**: If not present, add the `extlinks` configuration
3. **Convert links**: Replace all matching URLs with semantic markup
4. **Test**: Run `make linkcheck` to verify all changes work correctly

### Semantic Markup Formats

Convert the following link types:

#### Manpages
- **From**: `[text](https://manpages.ubuntu.com/manpages/<release>/en/man<N>/<name>.<N>.html)`
- **From**: `[text](https://manpages.debian.org/...)`
- **From**: `[text](https://man7.org/...)`
- **To**: `{manpage}\`name(N)\`` or `{manpage}\`description <name(N)>\``
- **Example**: `{manpage}\`dpkg(1)\`` or `{manpage}\`PAM config <pam.conf(5)>\``
- **Note**: Only convert specific manpage links. Do NOT convert generic links to manpages.ubuntu.com (without a specific page)

#### Launchpad Source Packages
- **From**: `[text](https://launchpad.net/ubuntu/+source/<package>)`
- **To**: `{lpsrc}\`package\`` or `{lpsrc}\`description <package>\``
- **Example**: `{lpsrc}\`bash\`` or `{lpsrc}\`DPDK Launchpad Area <dpdk>\``
- **Note**: Do NOT convert `git.launchpad.net` URLs (these point to specific files/patches)
- **Note**: Do NOT convert PPA URLs (these are specific package archives)

#### Launchpad Bugs
- **From**: `[text](https://bugs.launchpad.net/ubuntu/+source/<package>/+bug/<number>)`
- **From**: `[text](https://bugs.launchpad.net/bugs/<number>)`
- **To**: `{lpbug}\`number\`` or `{lpbug}\`description <number>\``
- **Example**: `{lpbug}\`1852389\`` or `{lpbug}\`provided special machine-types <1776189>\``
- **For comments**: `{lpbug}\`1872175/comments/21\``
- **Note**: Do NOT convert `/+filebug` URLs (these are bug filing forms, not specific bugs)

### Configuration Required in `docs/conf.py`

If these are not already present, add to the `extensions` list:
```python
extensions = [
    # ... other extensions ...
    "sphinx.ext.extlinks",
]
```

Add or update the `extlinks` configuration:
```python
extlinks = {
    "lpsrc": ("https://launchpad.net/ubuntu/+source/%s", "%s"),
    "lpbug": ("https://bugs.launchpad.net/bugs/%s", "LP: #%s"),
}
```

For manpages, ensure this is configured (adjust `stable_distro` as needed):
```python
stable_distro = "plucky"  # or "noble", "jammy", etc.

manpages_url = (
    "https://manpages.ubuntu.com/manpages/"
    + stable_distro
    + "/en/man{section}/{page}.{section}.html"
)
```

### Search Patterns

Use these patterns to find links to convert:

```bash
# Find manpage links
grep -r "manpages\.ubuntu\.com\|manpages\.debian\.org\|man7\.org" docs/**/*.md

# Find Launchpad package links
grep -r "launchpad\.net/ubuntu/+source" docs/**/*.md

# Find Launchpad bug links
grep -r "bugs\.launchpad\.net" docs/**/*.md
```

### Conversion Guidelines

1. **Preserve link text**: If the original link has descriptive text, use the `{role}\`text <target>\`` format
2. **Inline references**: When text like "bug #1234567" or "LP: #1234567" is used, convert to `{lpbug}\`1234567\``
3. **Tables**: When converting links in tables, ensure the pipe characters are escaped: `\\|`
4. **Context preservation**: Keep surrounding text and formatting intact
5. **Batch operations**: Use `multi_replace_string_in_file` for multiple edits across files

### Special Cases to Avoid

- Generic manpage site links (e.g., "visit manpages.ubuntu.com") - keep as regular links
- Git repository links (`git.launchpad.net/ubuntu/+source/...`) - these point to source code/patches
- PPA links (`launchpad.net/~user/+archive/...`) - these are personal package archives
- Bug filing links (`/+filebug`) - these are forms, not specific bug numbers
- Example command output containing URLs - keep as-is if they're part of literal output

### Testing

After making changes:
```bash
cd docs
make linkcheck
```

Verify:
- No broken links for the semantic markup
- No warnings about missing roles
- Links resolve to correct destinations

### Expected Outcomes

- All specific manpage URLs converted to `{manpage}` roles
- All Launchpad source package URLs converted to `{lpsrc}` roles  
- All Launchpad bug URLs converted to `{lpbug}` roles
- Documentation builds without warnings
- Linkcheck passes (except unrelated issues)
- Links render correctly with proper auto-generated URLs

---

## Additional Notes

This conversion improves documentation by:
- Making links shorter and more readable in source
- Allowing centralized URL pattern changes
- Providing consistent formatting across the documentation
- Enabling auto-generated link text when appropriate
