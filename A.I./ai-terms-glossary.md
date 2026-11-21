---
title: AI terms glossary — harness, agents, MCP, skills, computer use, AGI
tags: [ai, agents, mcp, harness, skills, computer-use, agi, geo-aeo, glossary]
source: "8 New AI Terms You Should Know to Be Ahead of 90% of People" – Jim Clyde Monge, Generative AI (Medium), 2026-10-02
captured: 2026-11-21
---

# AI terms glossary

Condensed from the article above — a plain-language glossary of 8 current AI terms. Non-technical framing; included here as quick reference. The article attributes several definitions to vendor docs (OpenAI, Anthropic, MCP, DeepMind), noted inline.

| # | Term | One-line definition |
|---|---|---|
| 1 | **Harness** | The software around a model that gives it tools, data, permissions, and control over when to retry/stop/ask. `Agent = Model + Harness`. Two apps on the same model differ because of their harness. |
| 2 | **Agent** | A system that takes multiple steps and uses tools to complete a task: plan → act → check → decide next. Capability depends on its tools and permissions. |
| 3 | **MCP (Model Context Protocol)** | "USB-C for AI" — a standard way for AI apps to connect to tools/data. An MCP server exposes specific tools or data to a compatible client, instead of each app building bespoke integrations. |
| 4 | **Loop engineering** | Designing the agent's act→check→retry cycle: what counts as done, retry limits, time/cost budget, when to ask for help, who checks the result. |
| 5 | **Skills** | A saved "recipe" (instructions) an agent loads when a task matches. In the Agent Skills format the instructions live in `SKILL.md`, optionally with scripts/templates. MCP gives access to tools; skills say how to do a job with them. |
| 6 | **GEO / AEO** | Being found *by* AI, not building agents. GEO (Generative Engine Optimization) = whether AI answers mention/cite/recommend you. AEO (Answer Engine Optimization) = making content usable as a direct answer. "SEO = rank on page one; GEO/AEO = be the source the AI quotes." |
| 7 | **Computer use** | AI interacts with a screen: screenshot → decide → click/type → re-check. Useful when no API exists. Can misclick/misread — review purchases, submissions, account changes. |
| 8 | **AGI / Superintelligence** | AGI = broadly capable AI at ~human level across many intellectual tasks (definitions disputed; DeepMind's "Levels of AGI" is one framework). Superintelligence = broadly exceeds human performance. Being great at one task proves neither. |

Note: the article ends with a claim that "the US government officially renamed AI to 'Superintelligence'" — this reads as satire/opinion and is not verified; ignore for reference purposes.
