(add-metadata-descriptions)=
# Add metadata descriptions

Parses through all documentation pages. If the page doesn't already have one, adds a short summary in the form of a metadata description to the header of the page.

Can be used on either markdown (`.md`) or reStructuredText (`.rst`) pages, or both.

Created with:
: LLM: Claude Sonnet 4.5
: Agent: GitHub Copilot

-----


    # Prompt: Add SEO Metadata Descriptions to Documentation

    Add metadata descriptions to every documentation page in this repository for SEO optimization.

    ## Objective

    Add HTML meta description tags to all documentation files (`.md` or `.rst`) in the `docs/` directory. Each description should be 120-160 characters, SEO-optimized, and accurately describe the page's content.

    ## Requirements

    1. **Survey the documentation structure**
      - Find all documentation files in `docs/` directory
      - Identify the markup format (Markdown/MyST or reStructuredText)
      - Determine the existing metadata format (if any)
      - Count total files to process

    2. **Determine the correct metadata format**
      - For MyST Markdown: Use YAML frontmatter with `html_meta` field
      - For reStructuredText: Use appropriate RST metadata directives
      - Examine existing files to match the project's conventions
      - Ensure metadata generates `<meta name="description" content="...">` tags in HTML

    3. **Write descriptions systematically**
      - Process files section by section (tutorial, how-to, explanation, reference, contributing, etc.)
      - Base each description on the page's actual content (read the file)
      - Keep descriptions 120-160 characters for optimal SEO
      - Use action-oriented language: "Learn how to...", "Understand...", "Complete reference for..."
      - Include relevant keywords naturally
      - Make descriptions useful for both search engines and users

    4. **Handle edge cases**
      - Preserve existing reference labels and formatting
      - Account for blank lines after labels where present
      - Skip files that already have metadata
      - Handle both landing pages and content pages appropriately

    5. **Track progress**
      - Use the todo list tool to track which sections are complete
      - Process files in efficient batches (10-20 at a time)
      - Report progress periodically

    6. **Quality assurance**
      - After completion, verify all documentation files have metadata
      - Test that the documentation builds successfully
      - Check that metadata appears correctly in generated HTML (look for `<meta name="description">` tags)
      - Confirm there are no new build errors or warnings caused by the changes

    7. **Final reporting**
      - Report total files processed by section
      - List any pages where the purpose was unclear
      - Provide SEO assessment noting:
        - Pages that are well-scoped for SEO
        - Pages that might benefit from splitting for better SEO
        - Overall documentation structure strengths/weaknesses
        - Any recommendations for improving SEO

    ## Approach

    - Work methodically through the documentation structure
    - Use multi-file editing tools for efficiency when processing batches
    - Use subagents if helpful for large-scale file discovery or analysis
    - Continue until ALL documentation files have metadata
    - Do not stop or ask for confirmation between sections - complete the entire task
    - If you encounter pages where the purpose is unclear, note them but continue processing

    ## Success Criteria

    - Every documentation file has an appropriate metadata description
    - All descriptions are 120-160 characters
    - Documentation builds without new errors
    - Metadata tags appear correctly in generated HTML output
    - Complete summary provided with any recommendations

    ## Example Output Formats

    ### MyST Markdown

    ```markdown
    ---
    myst:
     html_meta:
       description: "Learn how to install and configure Apache web server on Ubuntu Server with SSL/TLS support."
    ---

    (apache-installation)=
    # Install Apache
    ```

    ### reStructuredText

    ```rst
    :html_meta:
     description: Learn how to install and configure Apache web server on Ubuntu Server with SSL/TLS support.

    .. _apache-installation:

    Install Apache
    ==============
    ```

    Begin by surveying the documentation structure, then proceed to add metadata descriptions to every file systematically.


