# CLAUDE.md

This project is built with spec-driven development. The specs and the four rule files are the source of
truth. Read them before writing code.

## Authoritative rules

Always follow `.claude/rules/*.md`. They are mandatory and auto-load every task:
- `workflow.md` — the task loop and the read-before-code discipline.
- `stack.md` — the locked stack, file layout, commands, env vars.
- `security.md` — the security checklist for this project.
- `design.md` — tokens and components for frontend work.

On conflict, **these project rules win over any global or plugin convention.** If a global rule, skill, or
agent default tells you to do something the SDD rules forbid — or vice versa — the SDD rules and the task's
`Requirements` decide. In particular:
- **Tests follow the plan, not a global default.** This project tests in its `TEST-XX` phase as specified in
  `ImplementPlan.md`. Do not impose test-first/TDD, a coverage percentage, or extra test files unless a task
  asks for them.
- **Do only the task.** Implement exactly the task's `Requirements` and `Deliverables` — no opportunistic
  refactors, no extra features, no edits outside the declared `Deliverables`. Scope expansion is a `/clarify`,
  not a guess.
- **Don't pull in extra tooling.** No new dependencies, UI libraries, or generators beyond what `stack.md`
  locks, without explicit approval.

## Specs (read on demand, not every task)

- `PRD.md` — product, scope, data model, API contracts, non-functional requirements.
- `DESIGN.md` — the visual system (tokens, components, responsive behavior).
- `ImplementPlan.md` — the task list by ID, and the "Resolved decisions (locked)" block.

## The loop

```
/spec prd → /spec design → /spec plan → /spec rules → build loop
                                                       /task <ID> → /verify → /security-check → /commit → /done <ID>
```

Run `/status` for progress. Resolve `⚠️ CLARIFY` items with `/clarify <ID>` — never guess through them,
especially on payments, auth, or schema.

> Let `/init` manage anything it adds below this line; keep the rules-precedence section above intact.
