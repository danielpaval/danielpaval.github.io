---
title: "Claude Code - Hello world!"
date: 2025-03-02
tags:
  - Anthropic
  - Claude
  - Claude Code
---

As I heard about Claude Code at the end of this week[^1], I wanted to try it out myself. It took a bit of time to get the CLI going as Claude Code only works on Linux, and my WSL Node.js setup was both old and misconfigured.

The task I put it up to was to help me show the hidden posts of this Hugo site, as I had no clues. Honestly, I was surprised to hit a speed bump already, having to buy some token credits first ($5) without any trial. But then it correctly suggested that I forgot about the `draft: true` front matter field I had copy-pasted in multiple locations.

Next, I asked Claude Code to apply the fix, which it did, but it confused me with this broken diff view of the changes I had to confirm:

![Claude Code diff](/assets/2025-03-01-hugo-vs-jekyll/claude-code-diff.png)

I wonder if it might have been due to the post's Markdown table. It's a bug nonetheless, though understandable in this research preview of the CLI.

As final thoughts, Anthropic's approach to building a CLI tool for code assistance might be unique compared to their competition (Google's Gemini Code Assist, Microsoft's GitHub Copilot, etc.), which targets IDE integrations. I think one of their points was the CLI's ability to be used in CI/CD pipelines and other workflows. That sounds rather unpredictable to rely on at the moment, but I guess we'll eventually get there :)

In conclusion, while Claude Code shows promise, it still has some rough edges to smooth out. I'm looking forward to seeing how it evolves and improves over time.

[^1]: [Anthropic previews Claude Code: agentic coding, capable but costly](https://devclass.com/2025/02/27/anthropic-previews-claude-code-agentic-coding-capable-but-costly/)