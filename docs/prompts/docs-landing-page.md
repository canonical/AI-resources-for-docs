(doc-landing-pages)=
# Documentation landing pages

Use this prompt to review the landing pages in a Canonical documentation repository. The prompt instructs an agent to study the repository structure, identify the landing pages for each Diátaxis information type, and evaluate them against a standard example and a set of rules. The agent then produces a report of findings with specific suggestions for improvement.

---

## Landing pages: definition and purpose

Canonical documentation projects use the [Diátaxis](https://diataxis.fr/) approach.

The left navigation of the documentation displays all or some of the relevant categories of information (Tutorial, How-to guide, Reference and Explanation). 

**Note:** These four categories are referred to as information types throughout this prompt.

**Definition:** The index pages of these four information types under which other guides containing the information type are organized.

**Purpose:** Guidance for users to understand all the guides available in that information type, grouped according to the product's content by organizing links into logical groups with explanatory text.

## Landing page standard example

The example below contains dummy links. When applying to documentation repositories, use this as an example of how the structure should be and use appropriate links to the link texts.

### Example of good

```md

# How-to guides

These guides accompany you through the complete Example product operations lifecycle.

## Installation and deployment

Installation follows a broadly similar pattern on all platforms, but due to differences in the platforms, configuration and deployment must be approached differently in each case.

* [Install](#)  
* [Deploy to ABC](#)  
* [Deploy to PQR](#)  
* [Deploy to XYZ](#)

## Scaling

As your needs grow, an Example deployment can be scaled to meet increased traffic needs, either by allocating additional resources (CPU, RAM, etc) or by adding entire application instances. See [Approaches to scaling](#) for more discussion of which strategy to adopt.

* [Manage resources](#)  
* [Add instances](#) 

## Monitoring and troubleshooting

For an efficient deployment, you will need to continuously observe the health of your deployment, investigate performance issues, and resolve errors.

* [Monitor performance](#)  
* [Diagnose performance issues](#)  
* [Configure and manage logging](#)  
* [Troubleshooting](#)

```

#### Why is this example good?

* Each section has an opening sentence that explains when users will need the guides listed in that section.
* Sections are created by domains of concern within the product environment.
* Each section provides context to the users.
* The grouping maintains logical segregation of information and aids understanding.

### Example of bad

```md

# How-to guides

* [Install](#)  
* [Deploy to ABC](#)  
* [Deploy to PQR](#)  
* [Deploy to XYZ](#)  
* [Manage resources](#)  
* [Add instances](#)   
* [Monitor performance](#)  
* [Diagnose performance issues](#)  
* [Configure and manage logging](#)  
* [Troubleshooting](#)

```

#### Reasons why the pattern is bad

* Flat hierarchy with no logical grouping
* Long lists that don't serve meaning or .context to the user (only acts as an aggregator).
* Doesn't convey the domains of concern within the product.
* Users are unable to understand how a particular topic is related to another one through the grouping.

## Rules for landing pages

1. Each Diátaxis category in the left navigation should have a landing page with a name index.md

   ```md
   Tutorial
   How-to guides
   Reference
   Explanation
   ```

1. Each of the landing pages must list the guides under that specific Diátaxis information type in groups.

1. The groups must be based and listed according to product concepts and lifecycle:
   - Don't use arbitrary or alphabetical order.
   - Think about the order in which the product user might need it.
   - Show logical progression of the product lifecycle. Example: Installation --> Configuration --> Management --> Troubleshooting

1. Each group must include an introductory explanation (preferable length of 1-3 sentences).

   The explanation text must address the tasks or concepts relevant to that section, when and why users would use those guides, how the guides in those sections relate to each other.

   When writing explanatory text, do not include statements that merely talk about the obvious purpose of the documentation link.

   * Bad example: See [logs](#) to understand how to view logs.
   * Good example: Instance logs help you troubleshoot issues with your application when they have an unexplained error status.

1. Each group must contain links to the guides under the relevant Diátaxis information type and these links should be using an unordered list format as shown in the example. Even when there is a singular link, it is preferable to follow the unordered list format for consistency.

1. When there are too few guides to group meaningfully: Do not force a group structure if the landing page has very few guides or if all guides clearly belong to the same domain of concern. A single group whose heading restates the page heading adds no value to the reader. In these cases, write a strong page-level introduction that orients the user to what the guides cover, and present the links as a flat list. Add brief per-link descriptions where the link text alone is not self-explanatory. Flag this in your response and note that grouping should be revisited as more guides are added to the section.

### Rules specific to Tutorial landing pages

- Ideally, there must be just one tutorial. If there are multiple, suggest a consideration for the users to identify which of the existing topics are really tutorials that a new user needs.
- When there is exactly one tutorial, the tutorial file itself can serve as the landing page. In this case, no separate (`index.md` or `index.rst`) is needed. Check whether the parent toctree (usually the documentation root `index.md` or `index.rst`) points directly to the tutorial file rather than to a `tutorial/index`. If it does, this is the correct and complete pattern. Note this pattern in your review report and move on to the next information type.
- When there is more than one tutorial, ask for user input on learning progression and any optional tutorials.
    - Use that input to organize the mandatory tutorial that is helpful for the new user first.
    - Explain each tutorial's purpose

### Rules specific to How-to landing pages
- Groups should be organized by product lifecycle stages.
- Related operations should be grouped together.

### Rules specific to Reference landing pages
- Groups should be organized by logical categories. Examples: API reference, Configuration parameters.

### Rules specific to Explanation landing pages
- Groups should be organized by concept based on product architecture.
- Optional suggestion: Topics can be differentiated between are foundational vs. advanced.


## Instructions for reviewing landing pages

Follow these steps in order:

1. Locate the documentation root in the repository.

    The documentation source files are not always at the repository root. Look for a directory containing a Sphinx configuration file (`conf.py`) alongside a `Makefile` or `.readthedocs.yaml`. Common locations are the repository root, `/docs`, and `/doc`. Once you have identified the documentation root, treat all landing page paths as relative to it.

1. Identify the markup format of the documentation.

    Canonical documentation uses either MyST Markdown with `.md` files or reStructuredText with `.rst` files. There is a slight possibility that some repositories use a combination of these markup formats.

    * Identify the markup format of the documentation being reviewed.
    * Record the markup format and use the same format in any changes you propose.
    * This prompt's examples use MyST Markdown. The table below shows the RST equivalents for the three elements used in landing pages:

      | Element | MyST Markdown | RST |
      |---|---|---|
      | Section heading | `## Heading text` | `Heading text` followed by a line of `-` characters the same length |
      | Inline link | `* [Link text](target)` or `* {ref}\`label\`` | `- :ref:\`label\`` or `- \`Link text <target>\`_` |
      | Visible toctree | ` ```{toctree} ` with entries on separate lines | `.. toctree::` directive with entries indented below |
      | Hidden toctree | ` ```{toctree}` with `:hidden:` | `.. toctree::` with `:hidden:` option |

    * For documentation that uses a mix of both markups, use the same markup as the existing landing pages.
    * For documentation that uses a mix of both markups and has no landing pages, ask the user which format they would prefer.

1. Understand how links and toctrees appear in raw markup.

    Link text must concisely describe the target guides. Landing pages of a particular information type must contain groups or sections of links that are of the same information type.

    The required pattern for each section is: **heading → explanatory text → links**. Links can be written in two valid ways:

    * **Inline bullet list**: A section heading, a prose paragraph, then a bullet list of links.
    * **Visible toctree**: A section heading, a prose paragraph, then a `toctree` directive without `:hidden:`. The toctree renders as the link list.

    In both cases the heading and explanatory text are regular prose written outside and above the link list or toctree directive. The toctree directive itself only produces the link list — it cannot contain prose.

    A **hidden toctree** (`:hidden:` in both MyST and RST) controls the left-navigation sidebar but produces no visible content on the page. A landing page that contains only a hidden toctree does not satisfy the standard, regardless of how complete its link list is.

    When reviewing raw markup, look beyond the toctree directive to check whether grouped, explained sections exist as visible page content.

1. Identify the landing pages that exist for each information type.

    For the Tutorial type, note that a single tutorial file can serve as its own landing page without a separate index file. Before flagging a missing tutorial landing page, check whether the parent toctree points directly to a tutorial file. If it does, this is the correct pattern — the tutorial file contains the tutorial content itself, not a grouped link structure. Do not evaluate it against the landing page standard. Note this pattern in your review report and move on to the next information type.

1. Identify all the guides that exist within the information type's hierarchy. For example, a how-to guide landing page could be the `index.md` file inside a `how-to/` folder with all the other files within the folder being the guides to be linked on the landing page.

1. Study the "Example of good" and "Example of bad" above and the reasons stated for each before proceeding.

1. Study the "Rules for landing pages" section fully along with the specific rules for different information types.

1. Using the examples and the rules you have studied in this prompt, for each landing page:

    1. Check for completeness:
        * Are all guides of that Diátaxis information type linked from the landing page?
        * Are the links present in user-facing content, either as inline bullet lists or visible toctrees?
    1. Check for format:
        * Does each section have a heading, explanatory text followed by a set of links to guides?
    1. Check for quality:
        * Does the explanatory text meet the quality standard as per the rules defined? Does it explain when, why, or how users would need to use those guides,in the context of the product, rather than restating the obvious purpose of the links?
        * Are there any duplicate links pointing to the same target within the same group?
        * Are all links functional and have appropriate link texts?
    1. Check for logical grouping:
        * Are the groups or sections created based on product logic and user needs?
        * Does each group have enough introductory context indicating what the list of links provide for the user?
        * If the landing page has very few guides or all guides fall under the same domain of concern, check whether the grouping adds meaning or merely adds a heading that restates the page title. If grouping adds no meaning, suggest a flat list with a strong page-level introduction and per-link descriptions instead, and flag that grouping should be reconsidered as the section grows.

1. Compile your findings and suggestions for improvements to the landing pages along with the reasons for your claims.
