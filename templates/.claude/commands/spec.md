Route to the right SDD generation prompt and run it.

`$ARGUMENTS` selects the step (first word), with any remaining text passed through as the prompt's input:

- `prd`      → `prompts/prd-prompt.md`        — start `PRD.md` from a rough idea
- `design`   → `prompts/design-prompt.md`     — pick or write `DESIGN.md` (run after PRD)
- `plan`     → `prompts/plan-prompt.md`       — generate `ImplementPlan.md` from PRD + DESIGN
- `rules`    → `prompts/rules-prompt.md`      — generate `.claude/rules/{stack,security,design}.md` from the specs
- `docs`     → `prompts/docs-prompt.md`       — generate `README.md`

## Steps
1. If `$ARGUMENTS` is empty or its first word isn't one of the above, STOP and list the valid steps.
2. Read the matching prompt file in full.
3. Execute it exactly — including its clarifying-questions step. Do NOT skip the questions.
4. Pass any text after the step name through to the prompt as its input.

## Rules
- One step per `/spec` call. Run them in order: prd → design → plan → rules.
- Never guess on payments, auth, or schema — the prompts ask first by design.
- The prompts live at the project root `prompts/` (copied in during install — see `INSTALL.md`).

Usage:
  /spec prd "online clothing store with KHQR + COD for Cambodia"
  /spec design
  /spec plan
  /spec rules
