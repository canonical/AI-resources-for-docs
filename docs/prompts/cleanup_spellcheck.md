# Prompt: Clean Up `.custom_wordlist.txt` Spelling Exception List

You are tasked with cleaning up the spelling exception list in `/home/sally/src/ubuntu-server-documentation/docs/.custom_wordlist.txt`. This file contains words that should be exempted from spell-checking in a Sphinx-based Ubuntu Server documentation project that uses **US English** (`en-US`).

## Your Task

Perform the following operations **in a single pass** using the `multi_replace_string_in_file` tool or equivalent batch operation. Read the entire file once, process all changes in memory, then write the complete cleaned result back in one operation.

### Processing Steps (in order):

1. **Sort alphabetically** (case-insensitive, A-Z)
2. **Remove exact duplicates** (case-sensitive - keep only first occurrence)
3. **Remove GB English spellings** that differ from US English (e.g., "colour" vs "color", "organise" vs "organize") - these don't need exceptions since the project uses US English
4. **Remove words that are valid in standard US English dictionary** (common words like "adapter", "backend", "config" that don't need special exceptions)
5. **Remove invalid/erroneous spellings** (typos, malformed words, garbage entries)

### Critical Exclusions - DO NOT REMOVE:

- **Acronyms** (e.g., ABI, ACL, API, DNS, LDAP, UUID, VM)
- **Product names** (e.g., Apache, MySQL, Nginx, OpenStack, QEMU, Ubuntu)
- **Software/package names** (e.g., systemd, rsync, nginx, apparmor, netplan)
- **Technical jargon** specific to Linux/Ubuntu/networking/systems (e.g., multipath, syslog, journald, bootloader, netboot, cgroup)
- **Domain-specific terms** (e.g., DPDK, LXD, Netplan, Subiquity, LVM, DRBD)
- **File/command names** (e.g., sshd, dhcpd, multipathd, systemctl)
- **Technical compound words** (e.g., autoinstall, multicast, passthrough, filesystem)
- **Person names** (e.g., Danika, ArrayBolt, Stephane)
- **Organization names** (e.g., Canonical, Launchpad, GitHub)

### Examples of What TO REMOVE:

- Common English words: "adapter", "backend", "config", "lookup", "runtime", "utils"
- GB spellings if US is correct: (check for -ise/-ize, -our/-or, -re/-er patterns)
- Clearly wrong: random letter combinations, obvious typos
- Words already correct in US English dictionaries

### Output Format:

The cleaned file should:
- Be sorted alphabetically (case-insensitive)
- Have no duplicates
- Contain only technical terms, acronyms, and product names that genuinely need spell-check exceptions
- End with exactly one blank line (as noted in the file header comment)
- Preserve the header comment: `# Leave a blank line at the end of this file to support concatenation`

### Implementation:

1. Read the entire `.custom_wordlist.txt` file
2. Parse into a list, excluding the comment line
3. Apply all five transformations in sequence
4. Sort the final result
5. Use `replace_string_in_file` to replace the entire content (from first word to last word) with the cleaned, sorted list
6. Verify the blank line at end is preserved

**Execute this task now.** Provide a brief summary of what was removed and the final count.
