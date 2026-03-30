(getting-started)=
# Getting started

It can feel exhausting trying to keep up with the pace of progress in AI, especially if you're only just starting. Double especially if you're not a software engineer.

This guide will help set your feet on the path. It is aimed at Canonical employees in particular, although you do not need to be a Canonical employee to use GitHub Copilot.


## Prerequisites

* A [GitHub](https://github.com/) account
* A super basic understanding of how to navigate the terminal
* A safety copy of your favourite documentation


## Set up your development environment

We recommend you install [Visual Studio Code](https://code.visualstudio.com/download) as your development environment, since it has the GitHub Copilot extension.
You are not obliged to use VS Code, of course, but the tutorial will be referencing it. If you are interacting with GitHub CLI, or using a different IDE, you might need to adjust some steps. Check out the [GitHub Copilot documentation](https://docs.github.com/en/copilot/get-started/quickstart?tool=vscode) for help with your IDE of choice.


### Get a Copilot Business License

For **Canonical employees**, follow these steps to get a GitHub Copilot license:

* Log into the GitHub account associated with your Canonical email
* Create an issue using the [assign_github_copilot](https://github.com/canonical/GitHub-Copilot-license-manager/issues/new?template=assign_github_copilot.yaml) template
* Check the box next to the acknowledgement and submit the issue 

A GitHub bot automatically approves your request.
It also provides a list of links to get you started, and closes the issue.
You will also receive an email informing you that you have been granted access to GitHub Copilot Business.


### Link VS Code to the Copilot Business License

Install the [GitHub Copilot extension](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot) in VS Code. This also installs GitHub Copilot Chat, where we'll interact with the AI models.

If the extension was already installed in your VS Code, restart the extension.

Select the {guilabel}`Toggle chat` button next to the title bar. You will then be asked to log in. 

Log in using the GitHub account you used to request the license, and then you are good to go!

You should now have access to multiple models not available with the standard GitHub Copilot.

```{admonition} Remove Copilot license
If you end up not using Copilot, or try it out but decide not to use it anymore, you must open and submit an issue using the [remove_github_copilot](https://github.com/canonical/GitHub-Copilot-license-manager/issues/new?template=remove_github_copilot.yaml) template to remove the license. 
```


### Create context for the LLMs

If you haven't already, create a safety copy of your documentation (a copy with a different name that you can safely play around in).

The next (and final step) before you start experimenting is to create a context file for the LLMs, called `copilot-instructions.md`. This file applies repository-wide instructions for the LLMs to understand how they should handle requests.

In the VS Code main menu, select {menuselection}`File --> Open folder` and then navigate to the safety copy of your documentation.
This opens the entire contents into the Explorer pane.

Next, open a new chat window ({kbd}`Ctrl` + {kbd}`Alt` + {kbd}`I`) if it's not open by default. Ask in natural language for help, something like: "Can you help me create a `copilot-instructions.md` file". You can choose what model you want to use - I got good results from using Claude Sonnet. Having a clear and comprehensive contributing guide also helps here.

The selected model reviews the entire contents of the repository to determine the structure, configuration, and what guidelines to follow (style guide, contributing guide, etc). It creates a summary in the `copilot-instructions.md` file, so that in future conversations it has all the context it needs.


### Careful review

Once Copilot is done, review the file from start to finish! If there's anything in there you're not happy with, change it.

You can also change this file any time you are interacting with a model and find some aspect of its behaviour unsatisfactory. For instance, if the model continuously makes changes without your approval, you can add a point to the `copilot-instructions.md` file to specifically instruct the model to ask for user approval before making changes.

You can also add an instruction to the file, to instruct the model to update the file with any changes it learns about during conversations!

It's important to review everything Copilot produces in great detail. LLMs are great at generating things that sound plausible (but are wrong). Always double-check their working!
 

## Getting to the fun part

The next part is the fun part - it's where you get to start experimenting and trying things out.


## Where to start?

The best place to start is by just asking Copilot, via the chat window, to review your documentation against various criteria that matter to you.

Try this with all the LLMs available to you through Copilot! Put them to the test with simple tasks, or ask questions you already know the answer to so you can judge the quality of the answers. You will probably find you end up with a clear favourite.

When messing around with these tools, it doesn't take long to start understanding what they're good or bad at, where they fail completely, and where you might need to give some extra help. Sometimes, breaking a task down more into smaller steps can be the difference between success and failure.

### Ready-made prompts

Check out the {ref}`prompts category <reusable-prompts>` in this documentation for reusable prompts.

You can copy and paste these prompts directly into the chat window, and let the LLM handle the task.

Since these prompts were created from other documentation sets, you may need to tweak them first to suit your setup - or run them as they are, and help the LLM to refine the prompt based on your judgement of the results.


### Some pointers

There are two pieces of good advice that I've found incredibly helpful in my interactions with Copilot:

* Since the LLMs love making Python scripts, it's very easy to automate something by asking the LLM to create a Python script for it. This makes the process re-usable without needing to use an LLM directly in the future.

* Any time you complete something that could be useful to you in the future, remember to ask Copilot to "create a reusable prompt from this conversation that would lead to the same outcome but in only one step".

If you do happen to create a script, workflow or prompt that you found really helpful, please consider contributing it here for others to benefit from!


## Next steps

In the next tutorial, we'll look at pushing the capabilities of the LLMs using instructions files, skills, and agents.

-----

Huge thanks to [odadacharles](https://github.com/odadacharles), who wrote the short guide this tutorial is based on, and [akcano](https://github.com/akcano), for sharing his tips and pointers!
