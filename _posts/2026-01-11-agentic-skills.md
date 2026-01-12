---
layout: post
title: "Getting Started with Agent Skills"
subtitle: "Reusable instructions for AI coding tools"
tags: [ai, coding-assistants, skills]
cover-img: /assets/img/agentic-skills-cover.png
thumbnail-img: /assets/img/agentic-skills-cover.png
---

The latest generation of AI coding assistants supports **skills** - reusable instructions that teach agents how to perform tasks in a repeatable way. Skills aren't limited to just programming related tasks, they can be used to create PRs, read JIRA tickets, and much more. I'll explain how to create skills and how they can be chained together to complete complex workflows.

## Skills Support Comparison

Skills are supported by most coding agents now with notable exceptions including Visual Studio Copilot and Antigravity.

| Tool | Skills Support | Location | Note |
|------|---------------|----------|------------|
| Claude Code | Yes | `.claude/skills/` | - |
| OpenAI Codex CLI | Yes | `.codex/skills/` | - |
| GitHub Copilot (CLI, VS Code & Web) | Yes | `.claude/skills/`/`.github/skills/` | Enable `chat.useAgentSkills` |
| GitHub Copilot (VS) | No | - | - |
| Google Antigravity | No | - | - |

## How It Works

Each tool takes a similar approach: skills are folders containing a markdown file with metadata (name and description) plus optional scripts and resources. The AI loads skill descriptions at startup and automatically applies relevant skills when your request matches.

Only the front matter (summary) is loaded on startup which dramatically reduces the amount of context used compared to MCP servers. Most skills will use less than 100 tokens compared to MCP tools which can often use thousands of tokens. If the agent decides that the skill is relevant then the entire `skill.md` gets loaded into context. This makes skills useful for information that the coding agent only sometimes needs to know.

## When to use

Skills are really great for:

* Repetitive actions - Creating PRs, updating JIRA tickets or anything that you do multiple times a day. Skills can help you automate these processes
* Guidance on code style/structure - If an agent often struggles to follow pre-existing conventions such as when writing tests

## When not to use

* Always-needed context - If the agent always needs the context then consider using an AGENTS.md/CLAUDE.md instead
* Build/lint commands - These should go into the AGENTS.md instead of a skill as the instructions are short and will almost always be needed
* Project specific instructions - These should be placed in nested AGENTS.md instead of a skill. This allows reading by tools such as CodeRabbit and the agent will more reliably load the instructions

## How to create a skill

Use a skill to create new skills! You can install the [official Anthropic skill creator skill](https://claude-plugins.dev/skills/@anthropics/skills/skill-creator). This skill also works with copilot and simplifies the skill creation significantly. You may have to restart your agent after installing the skill as hot reload isn't universally supported yet (Claude just recently added it). You can then create a skill with a prompt similar to

> use the skill creator skill to create a skill that.....

It's important to iterate over a skill when creating it. Make sure to exit the agent or clear the context after each attempt of using the skill.

## Example of creating a skill

### Create a PR

This example shows how skills for well-known tools can focus on workflow rather than tool usage. The GitHub CLI is well known so you don't have to specify how to use it in the skill as the agent will have that knowledge in its training data. It's important to list out all the steps in the skill. Providing a template on how the agent should format its output will improve its outcomes.

> Use the skill creator skill to create a skill that can use the gh cli to create PRs. The PRs should use the template at: .github/pull_request_template.md. All PRs and branches must include the ticket number if specified. PRs must be forked from the master branch. If not on the master branch then stash the changes, change to master, create the branch and then unstash the changes.

### Read, create and edit JIRA tickets

This example demonstrates working with a lesser-known tool that requires more explicit guidance. The [Atlassian CLI](https://developer.atlassian.com/cloud/acli/guides/introduction/) (ACLI) was released less than a year ago and so we'll need to help the agent by providing more examples on how to call this tool. I had to iterate a few times before the skill would work consistently. Whenever the tool failed I'd use a prompt similar to this to correct the error `You failed to call the acli correctly in the last request. What changes do we need to make to the jira ticket skill to help you call the acli correctly next time`.

> Use the skill creator skill to create a skill that can use the acli to read, create and edit jira tickets. We should use the acli https://developer.atlassian.com/cloud/acli/guides/introduction. Use the --help flag to understand how to call the tool 


### Create a Playwright component test

This example shows how skills can capture project-specific patterns that aren't in the agent's training data. Sometimes agents have trouble following specific testing patterns. You could load the testing patterns into the AGENTS.md but then you'd be using up your context for instructions you might not use. Instead use a skill so that the instructions are only loaded when they're needed.

> Use the skill creator skill to create a skill for creating playwright component tests. Review the existing tests to understand how the tests should be structured

## Common mistakes and errors

* The skill isn't being loaded - adjust the summary to include keywords that are present in the prompt. The agent can help adjusting the summary with a prompt similar to `You didn't load SKILL-NAME to complete the last request. Why didn't you load the skill and what changes to the skill can we make to ensure you load the skill?`. Be careful the summary doesn't become too long or specific
* The skill is calling a cli tool with incorrect arguments - The skill is lacking specific examples of how it should use the tool. You can either add some examples or use a prompt similar to: `You failed to call TOOL_NAME correctly in the last request. Review the attempt and update the skill SKILL_NAME with information on how to call the tool correctly`
* The skill made an incorrect change, couldn't complete the action or had to make multiple attempts - This is common for the first few iterations of a complex workflow. If you can guide the agent to the correct answer by prompting it then a follow up prompt similar to this can be used to improve the skill `You had issues with completing the previous task using skill SKILL_NAME. Can you list some improvements for this skill based on your previous attempt?`.  


## Chaining skills together

Agents can dynamically load multiple skills in a single request. The agent reads your prompt, identifies which skills are relevant and loads them as needed throughout the request. You don't need to explicitly specify which skills the request should use (although it can help), the agent can use context to load a skill.

For instance I might use a prompt like:

> Read ticket XXX-123, implement a test to demonstrate the bug (ensure it fails), fix the bug, verify the test passes, then create a draft PR.

This chains together multiple skills:

1. **JIRA skill** → fetches the bug ticket details
2. **Playwright skill** → creates a failing test that reproduces the bug
3. *(Agent fixes the code until the test passes)*
4. **PR skill** → creates a draft PR from our template, populated with ticket and change details

**Tip:** Instructing the agent to write a failing test *before* fixing the bug gives it a feedback loop - it knows the fix worked when the test passes.

All I need to do is review the generated code and mark the PR as ready.

By investing time in a few well-crafted skills, the agent has enough context to handle bug fixes almost entirely on its own.

## Conclusion

Start with one workflow that frustrates you, build a skill for it, and iterate until it works reliably. You might be surprised at how many repetitive tasks the agent can automate for you.

## Further reading

### Official documentation

* [Agent Skills - Claude Code](https://code.claude.com/docs/en/skills)
* [About Agent Skills - GitHub Copilot](https://docs.github.com/en/copilot/concepts/agents/about-agent-skills)
* [Agent Skills - OpenAI Codex](https://developers.openai.com/codex/skills/)

### Skills repositories

* [anthropics/skills](https://github.com/anthropics/skills) - Anthropic's official skills collection
* [openai/skills](https://github.com/openai/skills) - OpenAI's skills catalog for Codex
* [github/awesome-copilot](https://github.com/github/awesome-copilot) - Community skills, agents and prompts

### Additional reading

* [Equipping Agents for the Real World with Agent Skills](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills) - Anthropic engineering blog on the design behind skills
* [Claude Skills are awesome, maybe a bigger deal than MCP](https://simonwillison.net/2025/Oct/16/claude-skills/) - Simon Willison's analysis