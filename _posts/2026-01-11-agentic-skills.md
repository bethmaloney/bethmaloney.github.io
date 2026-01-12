---
layout: post
title: "Getting Started with Agent Skills"
subtitle: "Reusable instructions for AI coding tools"
tags: [ai, coding-assistants, skills]
---

The latest generation of AI coding assistants supports **skills** - reusable instructions that teach agents how to perform tasks in a repeatable way. Skills aren't limited to just programming related tasks, they can be used to create PRs, read JIRA tickets and much more. I'll explain how to create skills and how they can be chained together to complete complex workflows.

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

Only the front matter (summary) is loaded on startup which dramatically reduces the amount of context used compared to MCP servers. Most skills will use less than 100 tokens compared to MCP tools which can often use thousands of tokens. If the agent decides that the skill is relevant then the entire skill.md gets loaded into context. This makes skills useful for information that the coding agent only sometimes needs to know.

## When to use

Skills are really great for two main use cases:

* Repetitive actions - Creating PRs, updating JIRA tickets or anything that you do multiple times a day. Skills can help you automate these processes
* Guidance on code style/structure - If an agent often struggles to follow pre-existing conventions such as when writing tests

## When not to use

* Always-needed context - If the agent always needs the context then consider using an AGENTS.md/CLAUDE.md instead
* Build/lint commands - These should go into the AGENTS.md instead of a skill as the instructions are short and will almost always be needed
* Project specific instructions - These should be placed in nested AGENTS.md instead of a skill as it allows for reading by tools such as CodeRabbit and the agent will more reliably load the instructions

## How to create a skill

Use a skill to create new skills! You can install the [official Anthropic skill creator skill](https://claude-plugins.dev/skills/@anthropics/skills/skill-creator). This skill will work with other agents and simplifies the skill creation significantly. You may have to restart your agent after installing the skill as hot reload isn't universally supported yet (Claude just recently added it). You can then create a skill with a prompt similar to `use the skill creator skill to create a skill that.....`.

It's important to iterate over a skill when creating it. Make sure to exit the agent or clear the context after each attempt of using the skill.

## Example of creating a skill

TODO: Example of how to create a skill to read and create Jira tickets. Another example for creating and reading Github PRs. And then another for creating tests

## Common mistakes and errors

* The skill isn't being loaded - adjust the summary to include keywords that are present in the prompt. The agent can help adjusting the summary with a prompt similar to `You didn't load SKILL-NAME to complete the last request. Why didn't you load the skill and what changes to the skill can we make to ensure you load the skill?`. Be careful the summary doesn't become too long or specific
* The skill is calling a cli tool with incorrect arguments - The skill is lacking specific examples of how it should use the tool. You can either add some examples or use a prompt similar to: `You failed to call TOOL_NAME correctly in the last request. Review the attempt and update the skill SKILL_NAME with information on how to call the tool correctly`
* The skill made an incorrect change, couldn't complete the action or had to make multiple attempts - This is common for the first few iterations of a complex workflow. If you can guide the agent to the correct answer by prompting it then a follow up prompt similar to this can be used to improve the skill `You had issues with completing the previous task using skill SKILL_NAME. Can you list some improvements for this skill based on your previous attempt?`.  


## Chaining them together

TODO: Example of reading a JIRA ticket, creating a test to confirm the bug and then automatically creating the PR