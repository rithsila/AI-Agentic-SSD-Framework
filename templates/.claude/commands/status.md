Read-only progress report for the project. **Do NOT modify any files.**

## Steps
1. Read `ImplementPlan.md`.
2. Compute:
   - Tasks done per phase (look for ✅ prefix): SETUP X/Y, BACKEND X/Y, FRONTEND X/Y, INTEGRATION X/Y, TEST X/Y
   - Total done: X/Y
3. Identify the next 3 tasks ready to start — not yet ✅ done, and every `Dependencies` entry is either already ✅ or refers to a CLARIFY listed under "Resolved decisions (locked)".
4. Identify blocked tasks — not done and waiting on something specific (which CLARIFY or which prior task).
5. List remaining ⚠️ CLARIFY items (open ones, not the resolved section).
6. If `PRD.md` exists and flags 🔴 risks, surface the top 3 at the end.

## Output format

```
## Status — YYYY-MM-DD

### Progress
- SETUP: X/Y ✅
- BACKEND: X/Y ✅
- FRONTEND: X/Y
- INTEGRATION: X/Y
- TEST: X/Y
- Total: X/Y done

### Ready next
1. <TASK-ID> — <title>
2. <TASK-ID> — <title>
3. <TASK-ID> — <title>

### Blocked
- <TASK-ID> — waiting on <CLARIFY-XX or prior task>

### Open CLARIFY items
- <CLARIFY-XX> (what it blocks)
- ...

### Top risks (from PRD)
- ...
```

## Rules
- Report only. No file edits, no code changes.
- If `ImplementPlan.md` is missing, STOP and report that the plan hasn't been generated yet (suggest `/plan`).

Usage:
/status
