(prompt-skill-optimization)=
# Prompt and skill optimization

First, let's clean up the terminology around the multiple ways we can feed text to LLMs:

| Term         | Invocation                    | Should be optimized | Reusable   | Notes                                                                                                         |
| ------------ | ----------------------------- | ------------------- | ---------- | ------------------------------------------------------------------------------------------------------------- |
| Prompt       | ad-hoc                        | not always          | usually no | Fundamental LLM concept                                                                                       |
| Command      | `/name`                       | yes                 | yes        | Technically a prompt with metadata                                                                            |
| Skill        | `/name` or autoloaded         | yes                 | yes        | [Recent concept](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills) |
| Instruction  | autoloaded, path-scoped       | yes                 | yes        | `.cursorrules`, `AGENTS.md`, `CLAUDE.md`, `.github/copilot-instructions.md`                                   |
| Agent prompt | `/name` or autoloaded         | yes                 | yes        | Shapes agent persona and default behavior                                                                     |

To confidently operate all these, you can either write and optimize them manually or resort to metaprompting (see below).

## Write

To boost your AI-focused writing, use these practical guides and specifications.

Prompts are the most fundamental part. Everything here applies elsewhere:

- Anthropic: https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/
- OpenAI: https://developers.openai.com/api/docs/guides/prompt-guidance

Skills are predefined reusable prompts that the agent invokes when it needs to (usually when the skill metadata advertise it as a match for the task at hand):

- Anthropic:
  - Original specification: https://agentskills.io/home
  - [Skill building guide](https://resources.anthropic.com/hubfs/The-Complete-Guide-to-Building-Skill-for-Claude.pdf)

- OpenAI:
  - https://openai.com/academy/skills/
  - https://developers.openai.com/codex/skills

- Google: https://developers.googleblog.com/developers-guide-to-building-adk-agents-with-skills/

Instructions are repo-wide prompt files that provide extra details on the whole project or its parts:

- Anthropic:
  - https://code.claude.com/docs/en/best-practices#write-an-effective-claude-md
  - https://code.claude.com/docs/en/claude-directory

- GitHub:
  - https://docs.github.com/en/copilot/tutorials/customize-code-review
  - https://github.blog/ai-and-ml/github-copilot/how-to-write-a-great-agents-md-lessons-from-over-2500-repositories/

- OpenAI: https://developers.openai.com/codex/guides/agents-md

There's no single authority on how prompts, skills, or instructions should be written; anything goes that makes your combination of an LLM and an agent tick the way you want.

## Optimize

Writing a prompt is one thing; ensuring it works as intended and cutting the waste, a.k.a. optimizing, is another.

Basic approaches optimize skills, instructions, and prompts by a set of predefined criteria (e.g., the [initial specification for skills](https://agentskills.io/specification)) but don't actually evaluate the outcome:

- https://dacharycarey.com/2026/02/13/agent-skill-analysis/

Look out for the conjecture happening here:

> If your Agent Skill content does not match the specification, agent platforms may not be able to use it. If it's close, agent platforms may be able to use parts of it, but some types of spec noncompliance may prevent agent platforms from using your Agent Skills entirely.

Maybe they will, maybe they won't. Who knows. LLMs don't care about how closely you follow a spec.

More advanced approaches evaluate skills against a set of criteria at run time, using execution traces, scoring rubrics, and multiple evaluation methodologies:

- https://developers.openai.com/blog/eval-skills
- https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents

However, this requires significantly larger effort.

Finally, there are plenty of opinionated approaches that optimize skills and instructions with AI based on some heuristics inferred from previous experience. Some of them even work.

## Meta approaches

For a brief outline of meta-prompting, see [this IBM article](https://www.ibm.com/think/topics/meta-prompting). The core idea is simple: let the LLM write a new prompt from scratch or optimize a human-written prompt. An example can be found in [this blog post](https://discourse.canonical.com/t/a-bayesian-take-on-prompt-engineering/6752).

Some pieces of [research](https://www.researchgate.net/publication/388575992_A_Comparative_Analysis_of_Human-Written_vs_AI-Generated_Prompts_for_Task_Executions) and empirical [evidence](https://www.computerworld.com/article/1612492/ais-may-be-better-at-prompt-optimization-than-humans.html) say (while some disagree) that AI-written/optimized prompts are generally not that bad and often at least as good as bespoke human-written prompts, but the devil is always in the details.

For one, any prompt that works well with a certain LLM may fail with another one. For two, the quality of an AI-written prompt generally depends on how specific and widely known the domain is; humans tend to outperform LLMs in highly specialized areas where domain expertise is not yet distilled into model weights.

That being said, surely there are meta-skills and prompts to build and improve other skills, instructions, and prompts.

It's probably best to start with the Canonical meta-skills in [copilot-collections](https://github.com/canonical/copilot-collections/):

- [Prompt generation](https://github.com/canonical/copilot-collections/tree/main/skills/generate-prompt)
- [Skill generation](https://github.com/canonical/copilot-collections/tree/main/skills/generate-agent-skills)
- [Agent (prompt) generation](https://github.com/canonical/copilot-collections/tree/main/skills/generate-agent)

There's a myriad of other options out there, e.g.:

- [Skill creator](https://github.com/anthropics/skills/tree/main/skills/skill-creator) in https://github.com/anthropics/skills
- https://github.com/glittercowboy/taches-cc-resources?tab=readme-ov-file#meta-prompting
- https://github.com/glittercowboy/taches-cc-resources?tab=readme-ov-file#audit-extensions

And so on.

Finally, Andrej Karpathy's [autoresearch](https://github.com/karpathy/autoresearch) pattern inspired a family of skills that *optimize other skills* automatically:

- https://zerocopy.blog/2026/03/25/karpathys-autoresearch-improving-agentic-coding-skills/
- https://x.com/itsolelehmann/status/2033919415771713715
- https://dev.to/alireza_rezvani/i-turned-karpathys-autoresearch-into-a-skill-that-optimizes-anything-here-is-the-architecture-57j8

And so on.

However, be careful: automatic optimization may overfit the result to the evaluation criteria, causing brittleness in a wider range of scenarios.

## Provider-specific resources

Copilot skills and per-repo instructions:

- [Skills](https://docs.github.com/en/copilot/concepts/agents/about-agent-skills)
- [Instructions](https://docs.github.com/en/copilot/how-tos/configure-custom-instructions/add-repository-instructions)

Claude skills and scoped instructions (rules); mind that Copilot [auto-discovers `.claude/skills/`](https://github.blog/changelog/2025-12-18-github-copilot-now-supports-agent-skills/):

- [Skills](https://code.claude.com/docs/en/skills)
- [CLAUDE.md](https://code.claude.com/docs/en/memory#claude-md-files)
- [Rules](https://code.claude.com/docs/en/memory#organize-rules-with-claude/rules/)

