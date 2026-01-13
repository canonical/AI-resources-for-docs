# Reusable prompts

This section contains prompts that you can feed into your LLM of choice to get help with particular tasks.

## To create a reusable prompt

After a successful discussion with an LLM where you are happy with the output, prompt the LLM to:

> "Produce a prompt that would've resulted in the same outcome without the need for my corrections and extra suggestions"

You can save this as a file whose contents can be copy/pasted into an LLM later.

## The reusable prompts in this folder:

Note that the prompts provided here have not been battle-tested or refined. Your mileage may vary.

* `premade-metaprompt.md`
    Feed this into an LLM alongside the shorter, natural language prompt you want to ask. The LLM will combine both into a full and detailed prompt that you can feed into any other LLM to achieve good results with minimal (or no) followup prompting required.

* `linkcheck_optimization.md`
    This prompt should allow you to replicate the documentation linkcheck optimization test.

* `cleanup_spellcheck.md`
    This prompt should assist with cleaning up your spelling exception list. This is likely to need more than one round of prompting.

* `add_metadata_descriptions.md`
    Parses through all documentation pages and adds a short summary in the form of a metadata description to the header of the page.
