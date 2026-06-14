Read `$ARGUMENTS` from `ImplementPlan.md` and execute that task.

Authoritative rules live in `.claude/rules/workflow.md`, `.claude/rules/security.md`, and `.claude/rules/stack.md`. Treat them as constraints — if a task and a rule conflict, the rule wins.

## Delegation (model tiering)
1. Read the task by ID and see what it touches.
2. If it touches payments, auth, schema, or architecture — or resolving it needs a judgment call — handle it here, in the orchestrator (opus) session. Do NOT delegate.
3. Otherwise delegate execution to the `implementer` subagent (sonnet), passing the task ID. It runs with fresh context, applies the rules below, and returns the output block.
4. The rules and output format below apply whoever runs the task.

Rules for this run:
- Implement ONLY what the task's Requirements and Deliverables specify. No extras, no opportunistic refactors, no out-of-scope edits.
- If anything is ambiguous, contradicts PRD/Design, or depends on an unresolved ⚠️ CLARIFY item, STOP and ask. Never guess on payments, auth, or schema.
- Use any relevant skills, tools, or MCP for conventions when available.
- File creation/editing only. Do NOT run migrations or destructive commands against any non-dev environment.

After completing, output exactly this format (matches `workflow.md`):
- ✅ Files created/modified (full paths)
- 📋 Summary of changes (modules, tables, columns, endpoints, components)
- 🧪 Verify — run lint/typecheck/build (+ tests if defined; see `verify.md`); report pass/fail per gate. Never report a task whose build fails.
- ⚠️ Anything I had to assume (should be rare; if anything substantive, that's a sign to have asked instead)
- ✓ Acceptance criteria — list each criterion from the task verbatim and mark **yes/no**

Usage:
  /task SETUP-01
  /task BACKEND-03
  /task FRONTEND-07
