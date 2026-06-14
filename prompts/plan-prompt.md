Generate `ImplementPlan.md` from `PRD.md` and `DESIGN.md` (if present).

## Mode: fresh vs. amend
- **If `ImplementPlan.md` does NOT exist** — generate it fresh, per the rest of this prompt.
- **If `ImplementPlan.md` already exists** — do NOT regenerate from scratch. Amend it to match the current specs:
  - Preserve every `✅`-done task and its `Completed YYYY-MM-DD` note verbatim. Preserve the "Resolved decisions (locked)" block verbatim.
  - For new scope in the specs, append tasks with fresh IDs (continue the numbering; never renumber existing tasks — their IDs are referenced elsewhere).
  - For scope removed from the specs, mark the matching not-done tasks `~~superseded~~` with a one-line reason. Do NOT delete them — git holds the history.
  - If a spec change invalidates an already-`✅`-done task (a schema, contract, or token it built changed), do NOT silently rewrite it. Add a `⚠️ REWORK-XX` task describing what must be redone and which done task it supersedes.
  - If a change is ambiguous or conflicts with a locked decision, add a `⚠️ CLARIFY-XX` at the top — never guess.
  - Report a diff summary: tasks added, superseded, flagged for rework, new CLARIFY items.

## Goal
Break the project into small, independent tasks. Each task must be small enough to complete in one Claude Code session — roughly 1 file or 1 feature per task, ≤30 minutes.

## Task ordering — by phase, in dependency order
1. SETUP-XX — env, db, schema, infra
2. BACKEND-XX — APIs, services, auth (keep IDs aligned with PRD §7 if assigned there)
3. FRONTEND-XX — UI components, pages, forms
4. INTEGRATION-XX — wire frontend to backend
5. TEST-XX — testing and verification

Within each phase, order tasks so later tasks only depend on earlier ones.

## Task format
### [TASK-ID]: [Short title]
- **Objective**: One sentence — what this task accomplishes
- **Requirements**: Specific implementation details. Reference exact file paths, table/column names, and function names from `PRD.md`. Specific enough that the task can be done without re-reading the PRD.
- **Dependencies**: list task IDs that must be done first, or "None"
- **Deliverables**: exact file paths to create or modify
- **Acceptance Criteria**: binary checks — "table exists", "endpoint returns 200", "form submits successfully". No vague criteria.

## Rules
- MUST cover everything in `PRD.md` (and the design file). Do not skip features.
- MUST NOT invent features not in `PRD.md` / design file.
- If something is unclear, add a `⚠️ CLARIFY-XX` task at the TOP asking the question. Do NOT guess.
- Use the migration convention of the project's stack (timestamped for Medusa, sequential numbered for Supabase, etc. — read `.claude/rules/stack.md` if present).
- Recommend the SIMPLEST approach that meets the spec.

## After writing
Output:
- Total task count per phase
- Any ⚠️ CLARIFY items flagged
- Estimated total tasks

Save to `ImplementPlan.md`.

Usage:
/spec plan                     # fresh, or amends an existing ImplementPlan.md
