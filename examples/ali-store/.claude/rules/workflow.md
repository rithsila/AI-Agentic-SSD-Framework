# Workflow Rules — Spec-Driven Development

> GENERIC — portable across all projects; copy as-is (see `FRAMEWORK.md`). Names the fixed spec files and
> the task loop, never a stack. Stack/design/security specifics live in the other three rule files.
> A project's filled instance is identical to this file. Keep under ~80 lines.

This project follows three documents and an explicit task list. Read them before any code.

## Before any code task
- ALWAYS read `PRD.md` first — product, scope (`§2`), data model (`§4`), API contracts (`§7`), non-functional requirements (`§9`), and the "Resolved decisions (locked)" block.
- ALWAYS read `DESIGN.md` for the frontend tokens and components (colors, typography, spacing, component inventory, responsive grid). Use named tokens, not improvised values.
- ALWAYS read `ImplementPlan.md` to find the exact task spec by ID (`SETUP-XX` / `BACKEND-XX` / `FRONTEND-XX` / `INTEGRATION-XX` / `TEST-XX`).
- ALWAYS implement only what the task's `Requirements` and `Deliverables` specify — no extra features, no opportunistic refactors.
- If the spec is ambiguous, contradicts PRD/Design, or depends on a `⚠️ CLARIFY` item that's still open, **STOP and ask** before guessing. Never guess on payments, auth, or schema.
- Treat the "Resolved decisions (locked)" section of `ImplementPlan.md` as hard constraints — they outrank a task's wording on conflict.

## During task execution
- Use the relevant skill, tool, or MCP for the project's stack (see `stack.md`) when available.
- **Generate migration files only — never run a migration against a non-dev database from this agent** (the migration command lives in `stack.md`). Production migrations are run by a human after review.
- Never run destructive commands (database reset/drop, `rm -rf`) without explicit per-task approval.
- Do NOT modify files outside the task's declared `Deliverables` list.
- Honor the locked tech stack and pinned versions (see `stack.md`). No new dependencies without explicit approval.
- Apply `security.md` rules at every step — they take precedence over a task's wording if there's a conflict.

## After completing a task — required output
- ✅ Files created/modified (full paths)
- 📋 Summary of changes (modules, tables, columns, endpoints, components)
- 🧪 Verify — run the project's gates (lint/typecheck/build, tests if defined; see `verify.md`) and report pass/fail per gate. If a gate fails, fix it within the task's `Deliverables` or STOP — never report a task whose build fails.
- ⚠️ Anything assumed (should be rare; if anything substantive was assumed, that's a sign you should have asked instead)
- ✓ Acceptance criteria from the task — list each criterion from `ImplementPlan.md` verbatim and mark **yes/no**

## Updating the plan
When (and only when) explicitly asked, mark tasks ✅ done in `ImplementPlan.md`. Never silently mark tasks done.

## Out-of-scope guard
If you find yourself wanting to add: a feature not listed in `PRD.md §2 In scope`, a token not in `DESIGN.md`, a dependency not in `stack.md`, or work not in a current task — stop and ask. v2 items belong in v2.
