# SDD Prompt — Generate the `.claude/rules/` files

Run after `PRD.md` and `DESIGN.md` are settled and `ImplementPlan.md` exists. Produces the thin,
auto-loaded rule files from the specs — so rules stay in sync with the specs instead of being hand-written.

## Output (rule files in `.claude/rules/`)
Start from the BASE/TEMPLATE versions in `templates/.claude/rules/` and fill the project slots:
- `stack.md` — fill the TEMPLATE with this project's locked facts from `PRD.md §5` and the repos: exact versions, repo layout, file organization, conventions, env vars, commands.
- `security.md` — keep the universal BASE rules; fill the "Project-specific" section from `PRD.md §9`, the data model, and the payment/auth design.
- `design.md` — **only if the project has a web frontend** (a `DESIGN.md` exists). Keep the universal BASE rules; fill the "Tokens (project)" section from `DESIGN.md` (exact tokens, component inventory, breakpoints, accent-usage rule, decoration policy). If there's no `DESIGN.md`, the project has no UI — delete `.claude/rules/design.md` so it doesn't auto-load and tax the token budget, and note the deletion.
- `workflow.md` — copy the generic file as-is; it is portable across all projects.

## Rules
- Each rule file stays under ~80 lines. Rules are prescriptive and load every task — no prose, no rationale, no examples. Detail lives in the specs.
- Pull concrete facts verbatim from the specs. Do NOT paraphrase a specific version/path/token into a vaguer one.
- Use the fixed filenames (`PRD.md`, `DESIGN.md`, `ImplementPlan.md`) and reference them by section.
- If a fact a rule needs isn't in the specs, STOP and ask — don't invent a default.
- Keep commands generic; project specifics live ONLY in these rule files, never hardcoded into a command.

## After writing
Output, per file: which spec sections it drew from, and any fact you had to ask about.

Usage:
  /spec rules
