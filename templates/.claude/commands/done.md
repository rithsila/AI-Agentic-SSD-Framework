Mark a task as ✅ done in `ImplementPlan.md`, but only after verifying the work actually meets the spec.

Task ID: `$ARGUMENTS` (e.g. `BACKEND-03`, `FRONTEND-12`)

## Steps
1. Read the task in `ImplementPlan.md` by ID. If not found, STOP and report.
2. **Run `/verify` (the gate)** — lint, typecheck, build, and any tests the stack defines (see `verify.md`). If the result is **FAIL**, STOP and report the failing gate. Do NOT mark done — a task that doesn't build is not done. This gate is objective; it runs before the self-reported checks below, not after.
3. **Security gate** — if the task touched auth/authz, user input, secrets, payments, or a new endpoint (see `security.md`), confirm `/security-check` was run against this task's diff and every 🔴 CRITICAL finding is resolved. If it was never run, or a CRITICAL is still open, STOP — run `/security-check` first. Do NOT mark done.
4. Verify the task is genuinely complete:
   - Every file in `Deliverables` exists with non-trivial content.
   - Every `Acceptance Criteria` item passes (run the check it implies — boot, endpoint hit, table exists, etc.).
5. If any criterion fails, STOP and report what's missing. Do NOT mark done.
6. If `/verify` passed, the security gate passed, and all criteria pass, edit `ImplementPlan.md`:
   - Prefix the task title with ✅
   - Add a one-line `Completed YYYY-MM-DD` note under the task.

## Required output (always)
- ✅ Files created/modified in this task (full paths)
- 📋 Summary of changes (modules, tables, columns, endpoints, components)
- 🧪 Verify — lint / typecheck / build / tests, pass/fail per gate (from `/verify`)
- ⚠️ Anything that had to be assumed during the task (should be rare; if anything substantive was assumed, that's a sign a `/clarify` was missed)
- ✓ Acceptance criteria — list each verbatim from the task and mark **yes/no**
- Status: **done** OR **blocked** (with reasons)

## Rules
- Never mark a task done if `/verify` reports FAIL or any acceptance criterion is unmet.
- Never mark a security-touching task done without `/security-check` having run clean (no open 🔴 CRITICAL).
- One task per `/done` call — never mark adjacent tasks done together.
- During `/done`, modify only `ImplementPlan.md`. Do not touch code.
- If the task hasn't actually been built yet, STOP — `/done` is the closing step, not the building step.

Usage:
/done SETUP-01
/done BACKEND-03B
