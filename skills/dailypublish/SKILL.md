---
name: dailypublish
description: "When the user wants to generate a full day of marketing content pieces in one run - brainstorm topics, draft every piece, and audit it before anything goes out. Also use when the user mentions '/dailypublish', 'daily publish', 'generate content for today', 'content pieces for each day', 'fill my content calendar', 'batch content creation', 'a day of posts', 'daily content batch', 'prep tomorrow's content', or 'day-n folder' (day1, day2). Orchestrates the other marketing skills (social, copywriting, image, video, copy-editing) plus optional research connectors (exa, firecrawl, apify) into a brainstorm -> draft -> audit pipeline that saves all pieces for a given day into a day-n folder. Produces drafts for human review; it never publishes anything itself."
metadata:
  version: 1.0.0
---

# Daily Publish

You run a **daily content pipeline**: given a day and a set of channels, you brainstorm the day's content pieces, draft each one using the relevant marketing skills, audit every draft, and save the finished set into a `day-n` folder. The output is a complete, reviewed batch of content for one day, staged for the user's approval - never published automatically.

This skill is an orchestrator. It does not replace the individual marketing skills; it calls them in the right order and keeps their output organized by day.

## Inputs to gather first

Ask only for what is missing:

1. **Day number** - which day this batch is for (produces folder `day1`, `day2`, ... `day-n`). Default to the next unused `day-n` folder if the user doesn't say.
2. **Channels and piece count** - which channels (LinkedIn, X, Instagram, blog, newsletter, etc.) and roughly how many pieces for the day. If the user gives none, propose a small slate (e.g. 3-5 pieces across their active channels) and confirm it.
3. **Brand guidelines** - voice, tone, topics to avoid, claims policy, visual rules. Check for existing context first (e.g. `.agents/product-marketing.md`, a brand doc, or prior approved pieces) before asking the user to restate it.
4. **Topic direction** (optional) - a theme, launch, or campaign the day should support.

## Stage 1 - Brainstorm

For each planned content piece, generate topic candidates and pick the strongest.

- Start from `marketing-ideas` and `content-strategy` for angles tied to the user's positioning and goals.
- **Research connectors, when available:** if the environment has connectors or MCP tools such as **exa** (web/topic search), **firecrawl** (page/site scraping), or **apify** (structured scrapers), use them to ground each piece in current sources - trending discussions, competitor posts, support questions, news in the niche. Record the source URLs used per piece.
- **When no connector is available:** do not pretend to have used one. Use whatever search/browse capability the environment natively offers, or ask the user for source links. State plainly in the piece's notes which research path was used.
- Deliver 2-3 candidate topics per piece with a one-line rationale, then select one (ask the user when the choice is close or brand-sensitive).

## Stage 2 - Draft

Draft each selected piece with the skill that matches its format:

| Piece type | Skill to use |
|---|---|
| Social post, thread, carousel, caption | `social` |
| Long-form copy, landing sections, email, ad copy | `copywriting` |
| Blog/SEO article | `content-strategy` + `copywriting` |
| Image, thumbnail, visual asset brief | `image` |
| Video script, short-form video, reel | `video` |

Apply each skill's own guidance fully (platform mechanics, hooks, formatting rules). Every draft gets: working title, channel, target audience, the draft itself, and the brainstorm sources it used.

## Stage 3 - Audit

Nothing enters the day folder unaudited. For each draft, run the checklist in [references/audit-checklist.md](references/audit-checklist.md):

1. **Fact check** - every factual claim, statistic, quote, and date must trace to a source captured in Stage 1, or be cut/reworded. Unverifiable claims are removed, not softened.
2. **Brand guidelines** - voice, tone, terminology, banned topics, claims policy. Run `copy-editing` for the language pass.
3. **Channel optimization** - correct length, format, hashtags, hooks, and platform conventions for the specific channel it will publish to (see `social`'s platform references).

A draft that fails any check is revised and re-audited once. If it still fails, it is marked `BLOCKED` in the summary with the reason, and the day ships without it.

## Output - the day-n folder

Write all pieces for the specified day into `day-n/` (e.g. `day1/`, `day2/`) in the working directory:

```
day3/
  01-linkedin-post.md
  02-x-thread.md
  03-instagram-reel-script.md
  04-hero-image-brief.md
  SUMMARY.md
```

- One file per content piece, numbered in publish order, named `<nn>-<channel>-<format>.md`.
- `SUMMARY.md` lists every piece: title, channel, status (`READY` or `BLOCKED`), sources used, and what changed during audit.
- All content is a **draft staged for review**. Show the user the summary, get explicit approval, and only then hand pieces off to whatever publishing step they use. This skill never posts, schedules, or sends anything.

## Guardrails

- **Human approval before publication.** Generating and auditing drafts is the job; publishing is a separate, user-approved action.
- **No assumed tools or accounts.** Connectors (exa, firecrawl, apify) and generation tools are used only if actually available in the environment. Never invent their output, and never sign up for or purchase a tool without the user asking.
- **Honest provenance.** Each piece records where its facts came from. "Researched" means a source was actually read.
- **Rights and claims.** No unlicensed assets, no invented testimonials or metrics, no competitor claims that can't be sourced.

## Example run

User: "Run /dailypublish for day 2 - LinkedIn and X, three pieces."

1. Brainstorm: pull current niche discussions (exa search if connected), propose 3 topics per channel, user picks 3.
2. Draft: 2 LinkedIn posts via `social`, 1 X thread via `social` + `copywriting`.
3. Audit: fact-check each against captured sources, brand voice pass via `copy-editing`, platform length/format checks.
4. Write `day2/` with the 3 pieces + `SUMMARY.md`, present the summary, wait for approval.
