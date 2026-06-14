Run the project's own quality gates against the current working tree and report pass/fail. **Do NOT modify code** — this is a gate, not a fixer.

The point: catch code that doesn't lint, typecheck, or build *before* `/commit` and `/done`, instead of trusting the agent's self-report. A task that doesn't build is not done.

## Which gates to run
Read the `Commands` block in `.claude/rules/stack.md` and run the ones that exist, in this order:
1. **Lint** — the project's lint command.
2. **Typecheck / build** — the project's build (or typecheck) command. For a multi-repo project, run it in each repo the last task touched.
3. **Tests** — the project's test command, **only if** the task under check defines tests or the repo already has a passing suite. Do not invent tests; `/verify` runs the existing gate, it doesn't author one.

If a command isn't defined in `stack.md` (e.g. no test command yet), skip it and say so — a skipped gate is reported, never silently passed.

## Scope
- `$ARGUMENTS` is optional. With a task ID (`/verify BACKEND-03`), focus the report on that task's `Deliverables` but still run the whole-tree lint/build — integration breakage is the main thing this catches, and it only shows up tree-wide.
- Without arguments, verify the whole working tree.

## Steps
1. Read `stack.md` for the actual commands. If `stack.md` has no `Commands` block, STOP and report that the stack rules aren't generated yet (suggest `/spec rules`).
2. Run each applicable gate. Capture pass/fail and the first real error per failing gate (not the whole log).
3. Never run dev servers, migrations, seeds, or any destructive/long-running command — gates only (lint, typecheck, build, test).
4. Report. Do not edit source to make a gate pass — that's a follow-up `/task` fix, run separately.

## Output
```
## Verify — <task ID or "working tree">
- Lint:       PASS | FAIL | skipped (reason)
- Typecheck:  PASS | FAIL | skipped (reason)
- Build:      PASS | FAIL | skipped (reason)
- Tests:      PASS | FAIL | skipped (reason)

Result: PASS | FAIL
```
- On FAIL: under each failing gate, the file:line and the first error message — enough to fix, not the full dump.
- On PASS: one line confirming which gates ran and which were skipped.

## Rules
- Read-only on source. Reports findings; never fixes.
- A skipped gate is stated explicitly — never counted as a pass.
- Overall result is FAIL if any gate that ran failed.

Usage:
  /verify
  /verify BACKEND-03
