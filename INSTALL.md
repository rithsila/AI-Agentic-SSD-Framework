# Installing the SDD Framework into a project

The framework is content, not a package. "Installing" means copying the template `.claude/` tree and the
generation prompts into your project, then generating the specs and rules with the `/spec` command.

It works two ways. **New project (greenfield)** starts from an idea. **Existing code (brownfield)** —
inherited, bought, or pre-existing — starts from the code that's already there. Step 1 and the build loop
are the same for both; only the spec-generation step differs.

## 1. Copy the template into your project
From this framework's root, copy into the target project root:

```bash
cp -r templates/.claude  <your-project>/.claude
cp -r prompts            <your-project>/prompts
cp -n templates/CLAUDE.md <your-project>/CLAUDE.md   # skip if the project already has one
```

This gives the project:
- `CLAUDE.md` — declares the four rule files authoritative and SDD process precedence over global/plugin defaults. If the project already has a `CLAUDE.md`, `cp -n` skips it — open the template and merge its "Authoritative rules" section in by hand, or the precedence won't take effect.
- `.claude/rules/` — `workflow.md` (generic, ready to use); `stack.md`, `security.md`, `design.md` (BASE/TEMPLATE — filled in step 2)
- `.claude/commands/` — the 9 commands: `task`, `verify`, `done`, `status`, `clarify`, `commit`, `security-check`, `security-audit`, `spec`
- `.claude/agents/` — `orchestrator` (opus), `implementer` (sonnet), `committer` (haiku)
- `prompts/` — the 6 generation prompts that `/spec` routes to

If your tooling already defines commands like `/verify`, `/commit`, `/status`, or `/done` (an ECC setup
does), install these under an `sdd:` namespace to avoid collisions. The command bodies reference each other
by bare name (e.g. `/done` calls `/verify`), so once namespaced, invoke them all with the prefix —
`/sdd:task`, `/sdd:verify`, `/sdd:done`.

## 2. Generate the specs and rules

Each `/spec` step asks clarifying questions first — answer them, don't let it guess. Pick the track that
matches your project.

### Track A — new project (greenfield)

```text
/spec prd "<one-line idea>"   → PRD.md
/spec design                  → DESIGN.md          (skip for non-UI: CLI, API-only, library)
/spec plan                    → ImplementPlan.md
/spec rules                   → fills .claude/rules/{stack,security,design}.md
```

For a non-UI project, skip `/spec design`; `/spec rules` then writes three rule files (no `design.md`) and
the build loop runs the same.

### Track B — existing code (brownfield)

```text
/spec onboard "<first change>"  → reads the code → fills stack.md + a current-state PRD.md
                                  (edit PRD.md: add your change as new scope)
/spec design                    → DESIGN.md   (only if you're touching UI — reverse-document the
                                               existing tokens; skip otherwise)
/spec rules                     → fills security.md (+ design.md if UI); KEEP the stack.md onboard wrote
/spec plan                      → amends: adds tasks for your change only, no re-scaffolding
```

Brownfield notes:
- `/spec onboard` replaces `/spec prd` — don't run both.
- `onboard` already filled `stack.md` from the real code. At the `/spec rules` step, fill only `security.md`
  (and `design.md` if there's UI) and **keep** that `stack.md` — don't let it be overwritten with
  PRD-derived guesses.
- Before building on code you didn't write, run a security pass: `/security-audit dependencies` and
  `/security-audit secrets`, plus your stack's vulnerability scan. See `PRACTICE-GUIDE.md` for the
  step-by-step (it uses a C#/.NET example).

## 3. Build loop

Same for both tracks — one task at a time:

```text
/task <TASK-ID>  →  /verify  →  /security-check  →  /commit  →  /done <TASK-ID>
```

Run `/status` any time for progress and the next ready tasks. `/done` re-runs `/verify`, checks the task's
acceptance criteria, and (for security-touching tasks) requires `/security-check` to have run clean before
marking the task ✅.

## 4. (Optional) docs

```text
/spec docs                    → README.md, once setup tasks have produced real commands and paths.
```

## When the spec changes later

Edit `PRD.md` (or `DESIGN.md`), then re-run `/spec plan` (it amends — done tasks survive, new scope is
appended, invalidated work is flagged `⚠️ REWORK`) and `/spec rules` (safe to re-run). Then resume the loop.

## Reference
- `PRACTICE-GUIDE.md` — a hands-on walkthrough for adopting the framework on an existing codebase (C#/.NET example).
- `examples/ali-store/` — one fully worked instance: filled rules, `ImplementPlan.md`, `PRD.md`, `DESIGN.md`.
- `FRAMEWORK.md` — the principles the framework refuses to break, and the token strategy behind the thin-rules / fat-specs split.
