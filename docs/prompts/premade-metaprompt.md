(premade-metaprompt)=
# Premade metaprompt

Feed this into an LLM alongside the shorter, natural language prompt you want to ask. The LLM will combine both into a full and detailed prompt that you can feed into any other LLM to achieve good results with minimal (or no) followup prompting required.

-----

    # Project Instruction: Generate High-Quality Autonomous LLM Agent Prompts from Minimal Hints (with Copyable Sample Blocks)

    ## Objective

    Build a system that converts short, informal user hints into **fully-structured, agentic prompts**, and always outputs them inside **copyable code blocks** (Markdown fenced blocks) so users can paste them directly into an autonomous LLM agent.

    ---

    ## Core Principles

    ### 1. Expand hints into full, rigorous agent instructions

    * IMPORTANT: **When unclear, ask the user questions and don't proceed until you have all the details**.
    * Translate short cues into explicit, detailed task descriptions.
    * Write as if preparing instructions for a fully autonomous agent with no prior context.

    ### 2. Every output MUST be a copyable sample block

    All final prompts must be wrapped in Markdown fenced blocks:

    ````markdown
    ```text
    ... agent prompt here ...
    ```
    ````

    * Never omit the block.
    * Never split output across multiple blocks unless the user requests it.
    * The block must contain the *entire* agent prompt.

    ### 3. Prompts must be agentic

    Generated prompts must instruct the autonomous agent to:

    * reason, plan, and act independently
    * validate assumptions
    * verify correctness with practical steps, not just your judgement
    * proactively detect inconsistencies
    * follow constraints without user intervention

    ### 4. Include structured elements

    Each generated agent prompt must follow this structure unless the user requests otherwise:

    * **Title**
    * **Mission**
    * **Context** (optional, when needed)
    * **Task Requirements**
    * **Process / Steps**
    * **Constraints & Guardrails**
    * **Output Specification**
    * **Quality Checks**

    ### 5. Conserve user intent

    * Expand minimally, but fully.
    * Do not invent goals or details absent in the hint.
    * Respect constraints like “conservative changes,” “preserve formatting,” etc.

    ### 6. Professional, technical tone

    * No fluff, no conversational filler.
    * Clear operational directives only.
    * Minimal footprint: all intermediate files and helper scripts should be removed afterwards on user's confirmation.
    * Concise reporting: do not overproduce the reporting documents and output.

    ---

    ## How the Generator Should Interpret User Hints

    Given a hint like:

    > “make sure that page titles and headings correctly represent page content; highlight discrepancies, suggest conservative edits”

    The system must:

    1. Detect the real task (audit headings).
    2. Infer default scope (likely “docs/” or a set of `.rst` files) and ask follow-up questions to confirm or adjust.
    3. Add constraints (conservative editing suggestions).
    4. Expand into a complete agent prompt.
    5. Format it inside a single fenced code block.

    Example output (structure only):

    ````markdown
    ```text
    Title: Review and Correct Page Titles and Headings

    Mission:
    Ensure all page titles and section headings in the docs/ directory accurately represent the content...

    ...
    ```
    ````

    ---

    ## Expected Output

    Whenever the user gives any short task hint, the system must return a **single copyable code block** containing a polished, agent-ready, action-oriented prompt.


