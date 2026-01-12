---
layout: post
title: "Antigravity First Impressions"
subtitle: "Google's AI-powered code editor shows promise but isn't ready for daily use"
tags: [ai, coding-assistants, ide, google]
cover-img: /assets/img/antigravity-cover.png
thumbnail-img: /assets/img/antigravity-cover.png
---

I recently tried Antigravity, Google's new AI-powered code editor, over the weekend. It's very impressive but not ready to be my daily driver yet. I used it with Gemini Pro 3 to refactor a medium-sized project and here's what stood out.

## What I liked

### Planning mode

The planning mode is absolutely the best part of Antigravity. It creates a rendered markdown file including screenshots that you can add comments to. After completing the task it updates the plan to show you what has changed with before and after screenshots. This really feels like the future of agentic planning.

### Browser integration

The browser integration is much stronger than the Playwright MCP server with Claude/Copilot. You install a Chrome extension, and Antigravity can then control your browser and capture screenshots while making UI changes.

### Generous free tier

The current free tier is generous with access to models including Gemini Pro 3, Sonnet 4.5 and Opus 4.5 (thinking). I did end up using my weekly allowance after using it casually over the weekend. The entry level plan is reasonably priced at $33 AUD however it's not clear how many requests you actually get.

### Smooth agent integration

The agent integration felt very smooth. You can enable "Agent Decides" for permissions when running commands or executing plans which auto-approves most commands and simple plans. The agent thoughts are hidden by default and a summary of the action taken is rendered in a card. Overall much nicer than Copilot.

## Why I won't be using it day to day

### Opaque data/training policy

Every other major competitor (Copilot, Claude, etc.) has a plain-English explanation of whether your code is used for training. Google instead points you to multiple terms of service documents. You have to read four separate policies to piece it together. Why it's not a FAQ entry is beyond me.

### Bugs (especially around WSL)

I hit multiple issues when using WSL. Early on, I was being disconnected from my container every ~10 minutes. I also had to set up port forwarding to get the Chrome extension working. I'd expect these to be ironed out over the next few months, but they're noticeable right now.

### Extension ecosystem limitations

Because Antigravity is a VS Code fork it can't use the standard VS Code marketplace. Instead you get an alternative open marketplace with fewer extensions. Most of my regular extensions were available but all the Microsoft-published ones (like C# Dev Kit) were missing.

## Conclusion

Overall it's a very impressive agentic IDE that's let down by bugs and an unclear data retention/training policy. I am very excited to see what it feels like in 6 months once the bugs have been resolved and their data retention policy has been clarified.

Antigravity currently has a generous free tier and can be downloaded at [https://antigravity.dev](https://antigravity.dev).
