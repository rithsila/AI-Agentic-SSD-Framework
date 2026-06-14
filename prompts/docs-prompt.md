# SDD Prompt — Generate project documentation

Run after the spec phase is settled and as soon as setup tasks have produced real commands and paths.

Based on `PRD.md`, `DESIGN.md`, `ImplementPlan.md`, and `.claude/rules/stack.md`, write project documentation. Default output is a single `README.md` at project root. Add companion docs only when they earn their place (see end).

## Who reads it
1. Future-you (or a teammate) opening the repo: how to run it locally, where things live, which env vars matter.
2. You at 3am six months later, trying to remember why something was decided.

Not for investors, marketing, recruiters, or padding. No pitch language anywhere.

## Required structure — in this order, no extras

1. **One-line description.** Concrete and specific. What this is, who it's for.
2. **Stack.** One paragraph, prose, not bullets. Pull verbatim from `stack.md`.
3. **Repo layout.** Short tree. Show folders that matter; skip obvious ones.
4. **Local setup.** Exact commands in the order someone would actually run them. Prose only when a command genuinely needs explanation.
5. **Environment variables.** One row per var: name, ≤5 words of purpose. No "secrets strategy" essay.
6. **Common commands.** The 5–8 commands run weekly. Not every command that exists.
7. **Deployment.** One short paragraph per environment (dev, prod). Where it runs, how it gets there.
8. **Specs.** Links to `PRD.md`, `DESIGN.md`, `ImplementPlan.md` with one line each describing what's inside.

No "About," "Features," "Roadmap," "Contributing," "Acknowledgements," or "License" sections unless the project genuinely has them.

## Voice rules — these matter most

This is where docs go wrong. Documentation should read like a competent engineer left themselves notes. Specifics:

**Never use these adjectives:** robust, comprehensive, seamless, powerful, scalable, cutting-edge, modern, elegant, intuitive, blazing-fast, lightweight, simple yet flexible.

**Never use these phrases:** "This README...", "This document...", "It's worth noting that", "It's important to understand", "Let's dive into", "Under the hood", "Out of the box", "Pro tip", "Note that", "Simply", "Easily", "In conclusion", "To summarize".

**Never:** Title Case headers (sentence case only). Decorative emoji. Bullet a single sentence (write the sentence). Section header followed by a paragraph that restates the header. Empty "Overview" sections. Tables for content that's already short prose.

**Voice to aim for:**
- Short declarative sentences. "The backend runs on Proxmox in Cambodia."
- Specific over abstract. "Postgres 16 on Supabase free tier" not "managed Postgres database."
- Direct when needed. "Run `npm ci` — never `npm install` in deploy."
- Cut anything you'd remove on edit. If a sentence isn't load-bearing, delete it.

## Length

120–180 lines total. If you pass 200, you're padding — re-read and cut.

## Rules

- Pull facts from `stack.md` and `PRD.md` verbatim. Don't paraphrase concrete things into vaguer ones.
- If a fact isn't in the specs, STOP and ask. Don't fill gaps with plausible defaults.
- Skip sections that aren't earned. A one-week-old project doesn't need "Deployment" — say so plainly or omit.
- Ask 2–3 clarifying questions before writing if any are unclear:
  - Is anything in production yet, or build-in-progress?
  - Will anyone besides the author work on this repo?
  - Public-facing repo or private?

## Output

- ✅ `README.md` at project root.
- 📋 Sections omitted, one line each (e.g. "Deployment: skipped, nothing deployed yet").
- ⚠️ Anything that required asking the user.

## Companion docs — generate only if explicitly requested

- `docs/RUNBOOK.md` — only after prod is real. Failure modes, restart procedures, backup locations.
- `docs/ADR/` — only when decisions have evolved beyond what `PRD.md`'s "Resolved decisions" can track.
- `docs/admin-guide.md` — only when non-technical operators will use the system.

Do not write these speculatively.

Usage:
  /spec docs
