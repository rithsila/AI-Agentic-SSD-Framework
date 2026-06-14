Resolve a single ⚠️ CLARIFY item in `ImplementPlan.md` by folding the user's answer in and propagating it to dependent tasks.

## Inputs
- `$ARGUMENTS` — the CLARIFY-ID (e.g. `CLARIFY-04`)
- The resolved answer — given in the same message or the previous user turn

If either is missing, STOP and ask. Never guess the answer.

## Steps
1. Read `ImplementPlan.md` and locate the CLARIFY item by ID. If it doesn't exist or is already resolved, STOP and report.
2. Update the file:
   - Move the item into a "Resolved decisions (locked)" section near the top (create the section if it doesn't exist).
   - Write the resolution as a concise bullet noting what was decided and which tasks it affects.
   - Strip the CLARIFY-ID from the `Dependencies` line of every task that referenced it.
   - If the answer leaves part of the question pending (e.g. "I'll provide the value later"), move that remainder into a "Still pending (provide later — non-blocking)" section.
3. If the answer creates a NEW decision (e.g. enabling KHR creates an exchange-rate question), add it as a new `CLARIFY-XX` at the top — do not guess through it.
4. Preserve the rest of the plan exactly — do not re-order or re-word unrelated tasks.

## Rules
- Modify only `ImplementPlan.md`.
- Never silently resolve a CLARIFY — every change must be visible in the file diff.
- Report the diff summary at the end (which tasks lost a CLARIFY dependency, which new CLARIFY items appeared).

Usage:
/clarify CLARIFY-04
