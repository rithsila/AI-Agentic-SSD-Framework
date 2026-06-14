Onboard an existing codebase into the SDD framework.

For brownfield projects — you have working code (inherited, bought, or pre-existing) and want to make changes through the SDD loop. This reverse-documents what exists so later specs target only your changes, not a rebuild. It is the brownfield alternative to `/spec prd`.

It produces a filled `.claude/rules/stack.md` and a baseline `PRD.md`, both derived by reading the code — never invented. It writes no code.

## Before writing
- Ask 3–5 clarifying questions: what you intend to change or add first; which areas are off-limits (must not be touched); whether the project already builds/tests and how it's run; anything you already know is fragile or undocumented. Wait for answers before writing.

## Read the code first — do not guess
- Detect the stack from the repo itself: manifest and lockfiles (e.g. `package.json`, `*.csproj`/`*.sln`, `go.mod`, `requirements.txt`, `Cargo.toml`, `composer.json`, `Gemfile`), the language, the framework, and the exact versions actually pinned.
- Find how it builds, runs, tests, and migrates by reading the build files, CI config, Makefile, or task runner — not from assumptions.
- Map the real directory layout: where source, routes/controllers, models/schema, config, and tests live.
- Note external integrations, the auth strategy, and the env keys the code expects (read `.env.example`/config — never values).
- Anything the code does not reveal becomes an Open Question. Do not invent it.

## Output 1 — fill `.claude/rules/stack.md`
Start from the TEMPLATE at `templates/.claude/rules/stack.md` and fill every slot from what you READ:
- Tech stack (locked): the exact versions already in the lockfile — record them as-is, do NOT upgrade or "modernize".
- Repos and file organization: the real directories, not an idealized layout.
- Commands: the actual build / run / test / migrate / install commands the repo uses.
- Environment variables: the keys the code and config reference.
- Conventions: the patterns the existing code already follows (validation, error shape, data fetching) — describe what IS, not what you would prefer.
Keep it under ~80 lines. Pure facts; rationale stays in `PRD.md`.

## Output 2 — baseline `PRD.md`
Write a `PRD.md` describing the system AS IT IS TODAY, with a header marking its provenance:
- Header: "Reconstructed by `/spec onboard` from the existing codebase — current-state baseline, not a forward spec."
- Problem & Goal — what the app does today, in one paragraph.
- Scope — the current feature set, recorded as the v1 baseline (what already exists).
- Data Model — the entities/tables the code actually defines.
- Tech Stack — point at `stack.md`; do not duplicate it.
- Architecture Overview — how the pieces fit, external integrations, auth strategy.
- Open Questions — anything the code did not reveal. Do NOT fill these in.
This is a description of what exists, not a wishlist. Do not invent features the code does not have.

## After writing
Output:
- 🔴 Risks found in the inherited code — no tests, unpinned or stale dependencies, hardcoded secrets, dead config. Flag them; do not fix them here.
- ⚠️ Parts of the stack or architecture you could not determine from the code and need confirmed.
- 💡 Next step: add your first change to `PRD.md` as new scope, then run `/spec rules` to generate `security.md` and `design.md` (keep the `stack.md` this step already filled), then `/spec plan` — it amends, so it will not re-scaffold what exists.

Usage:
/spec onboard
/spec onboard "add a loyalty-points feature to this store"
