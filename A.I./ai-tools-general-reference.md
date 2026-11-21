---
title: General-purpose AI tools reference (productivity, research, creative)
tags: [ai-tools, productivity, research, creative, transcription, diagrams, data-analysis, web-scraping]
source: |
  Merged from two Medium listicles:
  (A) "10 More Insanely Useful AI Tools You Should Know" – Arham Ali, Python in Plain English, 2026-11-08
  (B) "25 Free AI Tools That Feel Too Useful to Be Free" – Human Mode, Medium, 2026-11-04
captured: 2026-11-21
---

# General-purpose AI tools reference

Merged and deduplicated from two general AI-tool listicles (not pentest-specific — kept separate from the pentest KB note on purpose). 30 unique tools after removing 5 overlaps (Napkin AI, Recraft, Julius AI, Browse AI, Granola appeared in both).

**Caveats, read once:**
- **Nothing here is verified against the vendors** — these are the articles' own descriptions. Free-tier limits are as stated in the sources (November 2026) and the source itself notes they change frequently. Treat every "free tier" number as a starting point to re-check, not a fact.
- **URLs** are included only where source (A) gave them explicitly. Tools that appeared only in source (B) had no URL printed — search the name.

Tag key: **[A]** = from article A, **[B]** = from article B, **[A+B]** = in both.

---

## Voice & audio → text

| Tool | What it does | Free tier (as stated, Nov 2026) | URL |
|---|---|---|---|
| **AudioPen** [A] | Speak naturally; turns rambling voice into clean, organized text (notes, emails, summaries). | — | audiopen.ai |
| **Voicenotes** [B] | Record memo/thought/meeting → transcribe → searchable text. | Unlimited recordings, 100 transcription min/week, 100+ languages, 30-day history. | — |
| **Auphonic** [A] | Automatic audio post: balances volume, reduces noise, removes hum. For podcasts/voiceovers. | — | auphonic.com |

## Meeting notes

| Tool | What it does | Free tier | URL |
|---|---|---|---|
| **Granola** [A+B] | Runs on your machine (no bot joining the call); expands your rough notes into summaries, decisions, action items; chat across meetings. | Basic: AI notes, limited history, cross-meeting chat, templates, opt-out of model training. | granola.ai |
| **Tactiq** [B] | Browser-based; transcribes calls, generates insights/summaries, extracts follow-ups without a visible bot. | Free forever: 10 transcripts/mo, 5 AI credits. | — |

## Diagrams & visual thinking

| Tool | What it does | Free tier | URL |
|---|---|---|---|
| **Napkin AI** [A+B] | Paste text → diagrams, flowcharts, mind maps; editable afterward. | 500 AI credits/week, unlimited editing + PNG/PDF export; SVG/PPTX export is paid; free visuals keep Napkin branding. | napkin.ai |
| **Whimsical AI** [B] | Flowcharts, mind maps, wireframes from natural language, editable on an infinite canvas. | Unlimited boards, monthly object/block allowance, 10 AI credits/member. | — |
| **Flowgraph Studio** [B] | Clean technical/architecture diagrams with AWS/GCP/Azure/Kubernetes icon sets. | ≤25 saved diagrams across 3 projects, SVG/PNG export, watermark-free monthly allowance; AI drafting is paid. | — |
| **SVGDiagram.ai** [B] | Turns info into editable SVG diagrams from a library of structures. | 10 diagrams/mo, 20 starter designs, unlimited edits, SVG/PNG export; non-commercial only. | — |

## Image / vector / 3D / music generation

| Tool | What it does | Free tier | URL |
|---|---|---|---|
| **Recraft** [A+B] | Design tool that understands vectors: raster + editable SVG, icons, logos, mockups, consistent styles. | 30 generations/edits per day, raster + vector models; free outputs lack commercial rights / may be public. | recraft.ai |
| **Krea** [B] | Real-time creative gen: image/video/3D/lipsync/LoRA/upscaling/workflow nodes; transforms as you type/draw. | 100 compute units/day, no card required. | — |
| **Upscayl** [B] | Free & open-source **desktop** app; AI image upscaling, runs **locally**. Win/macOS/Linux, multiple models, batch. | Free, no usage limits (local processing). | — |
| **Meshy** [B] | Text/image → 3D assets for games, 3D art, printing. | 100 credits/mo; limited untextured downloads; CC BY 4.0 outputs. | — |
| **Udio** [B] | Generate/extend/remix musical ideas from descriptions. | 10 daily credits + 100/mo reserve; limits on longer generations. | — |

## Data & spreadsheets

| Tool | What it does | Free tier | URL |
|---|---|---|---|
| **Julius AI** [A+B] | Upload data, ask in plain language; analysis, charts, stats, reports, presentations. | $0 plan with daily free credits; limited data analysis + presentation/image/video/report creation. | julius.ai |
| **Rows** [B] | Spreadsheet with an AI Analyst: natural-language analysis, clean data, formulas, charts, extract from PDFs/images, no SQL/Python. | Unlimited spreadsheets, 10 integrations, 5 AI Tasks/mo. | — |

## Web data collection

| Tool | What it does | Free tier | URL |
|---|---|---|---|
| **Browse AI** [A+B] | Teach it what to extract from a site → builds a "robot" for structured data; scheduling + change monitoring. | Free forever: 50 credits/mo (~50 pages / 500 rows), robots, monitoring, REST API, webhooks. Respect sites' ToS. | browse.ai |
| **Clay** [B] | Data enrichment + AI research agent (Claygent): build a table, have AI research each row (company/prospect intel). | 500 actions/mo, 100 data credits, unlimited seats/tables, ≤200 rows, BYO API keys. | — |

## Research & literature

| Tool | What it does | Free tier | URL |
|---|---|---|---|
| **Undermind** [A] | Give it a research question; its agent searches scientific literature, explores directions, prepares a report with references. | — | undermind.ai |
| **Elicit** [B] | Built on research literature: search papers, summaries, chat-with-paper, organize evidence. | Unlimited searches over 138M+ papers, unlimited summaries, chat when full text available, Zotero import; advanced agents limited. | — |
| **Consensus** [B] | Answers questions *using* scientific literature; inspect the studies behind a result. | Unlimited basic searches, 10 Pro messages/mo, 3 Deep Reviews, 10 Study Snapshots, small API/MCP allowance. | — |
| **ResearchRabbit** [B] | Literature as an explorable map: start from trusted papers, explore related work/citations/authors; visual citation maps. | "Free Forever": unlimited searches over 310M+ articles, unlimited libraries, citation mapping, Zotero, ≤50 seed papers. | — |

## Document Q&A / personal knowledge base

| Tool | What it does | Free tier | URL |
|---|---|---|---|
| **Recall** [A] | Save articles/YouTube/PDFs; auto-summarize + organize into a searchable library; knowledge graphs, quizzes. | — | recall.it |
| **Humata** [B] | Upload a long doc, ask questions in natural language instead of scanning pages. | ≤60 pages, 10 answers. | — |
| **MyReader AI** [B] | Chat with books/docs/article links/YouTube; OCR for scans; text-to-audio. | 5 questions/day, ≤200 pages uploads, 10 MB files, audiobook, OCR, bulk upload; free books removed after 7 days inactive. | — |

## Productivity / tutorials / app-building / niche

| Tool | What it does | Free tier | URL |
|---|---|---|---|
| **Goblin Tools** [B] | Small tools for everyday cognitive friction: Magic ToDo (break vague tasks into steps), Formalizer (tone rewrite), time estimates. | Core web tools free; optional Pro adds sync/history/templates/custom instructions. | — |
| **Guidde** [A] | Records your on-screen workflow → generates a step-by-step guide with descriptions + AI voiceover. | — | guidde.com |
| **Taskade** [B] | Build small AI apps/agents/automated workflows from natural language (intake tools, internal assistants, light CRM). | ≤3 hosted apps, starter AI credits, AI chat, basic agents, automations, 2 seats. | — |
| **Teal** [B] | AI for the job search: résumé builder, job tracking, keyword matching vs job descriptions, résumé analysis. | Unlimited résumés + job tracking, keyword matching, analysis; small AI allowances for bullets/summaries/cover letters/interview practice. | — |
| **MagicSchool** [B] | Education-specific: 80+ teacher tools (lesson planning, materials, differentiation, feedback, activities). | Free forever for individual teachers; 80+ tools, presentation/image generation. | — |
