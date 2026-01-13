```text
Title: Autonomous Redirect Link Remediation for Markdown Documents

Mission:
Programmatically update all Markdown (`.md`) source files by replacing URLs flagged as 'redirected' with their validated target URLs. The primary goal is to improve link hygiene, eliminate unnecessary HTTP redirects, and generate a pull request (PR) containing only validated, successful changes.

Context:
The project uses a build system that includes a `linkcheck` utility (executed via `make linkcheck`). The input files for correction are exclusively Markdown (`.md`). The parsing must rely on standard Python libraries.

Task Requirements:
1.  Execute the command `make linkcheck > linkcheck.txt` to generate the raw link check report.
2.  Develop a robust Python script using only standard libraries (`re`, `os`, `json`, `pathlib`) to perform all processing.
3.  The script must parse 'linkcheck.txt' to identify all redirected links, extracting the source `.md` file path, the 'from' (redirecting) URL, and the 'to' (target) URL.
4.  Apply a conservative replacement strategy: in the identified source files, globally replace the 'from' URL with the 'to' URL.
5.  Track all successful changes made and output a comprehensive report.
6.  Validate all changes by running a second link check.

Process / Steps:
1.  **Initial Setup:** Create the Python script `update_redirects.py`.
2.  **Report Generation:** Execute `make linkcheck > linkcheck.txt`.
3.  **Data Extraction & Pre-Validation:**
    * The script reads 'linkcheck.txt' and first collects a list of all URLs reported with the status `broken:`.
    * The script then parses lines marked `: redirected:` following the format: `path/to/file.md:LINE_NUM: redirected: FROM_URL -> TO_URL (CODE)`.
    * For each redirect, before storing the change, the script must perform a **Safety Check**: if the `TO_URL` is found in the list of `broken:` URLs, this specific update must be **skipped** and logged as an ignored change.
    * Store all validated updates in a list of dictionaries: `{'file': path, 'from_url': url1, 'to_url': url2}`.
4.  **File Modification:** Iterate over the unique `.md` files in the stored data.
    * Read the file content.
    * For all relevant updates associated with that file, perform global string replacement of `from_url` with `to_url`.
    * Write the modified content back to the file, ensuring original file encoding and metadata are preserved.
5.  **Change Tracking:** Save the list of all successful updates to a summary file, `redirect_changes_summary.json`.
6.  **Validation Check:** Execute a second link check: `make linkcheck > linkcheck_validation.txt`.
7.  **Verification:** The script must analyze `linkcheck_validation.txt` to confirm that all URLs recorded in `redirect_changes_summary.json` (the new `to_url`) are not present in the new report with a `broken:` status.
8.  **Cleanup:** Delete temporary files: `linkcheck.txt` and `linkcheck_validation.txt`.

Constraints & Guardrails:
* **File Scope:** Modifications are strictly limited to files with the **`.md`** extension.
* **Safety Check (Accuracy over Completeness):** A replacement is **FORBIDDEN** if the proposed target URL (`TO_URL`) is reported as `broken:` anywhere in the initial `linkcheck.txt`. This is the primary guardrail against fixing a redirect to an invalid link.
* **Technology:** The script must use only **standard Python libraries**. No external dependencies are permitted.
* **No Interference:** The script must not modify any files outside the defined documentation source directory.

Output Specification:
1.  The primary output is the modified `.md` source files.
2.  A final report, `redirect_update_report.md`, summarizing:
    * The total number of redirects found initially.
    * The number of redirects successfully updated.
    * The number of redirects skipped due to the Safety Check (Target URL was broken).
    * The overall validation status (Pass/Fail) based on the second linkcheck.
    * The full content of `redirect_changes_summary.json` (formatted as a JSON code block).

Quality Checks:
* The script's execution must be fully auditable and reversible (changes are committed to a PR).
* The final report must confirm that the new links (replacements) introduced zero new broken links.
* The script must execute cleanly without error and return a success status only if changes were made and validated.
```

