# SDD Framework

A lean, stack-agnostic spec-driven-development workflow for AI coding agents. You write specs, the agent
writes code, you stay in control of every decision. No vibe coding, no 200-page documents — the smallest
amount of process that keeps a project from falling apart.

**Status:** working · **Version:** 2.0 · **For:** solo developers (or 1–3 people) using Claude Code

This is the teaching doc: the philosophy, then how to use it. For the authoritative structure and the
invariants the framework refuses to break, see `FRAMEWORK.md`. To drop it into a project, see `INSTALL.md`.

## What this is

A pipeline that turns a rough idea into shipped code through four short specs and a tight build loop. The
agent interviews you, you decide, and each step is a checkpoint you approve before the next one runs. The
framework is deliberately small — 9 commands, 3 agents, 4 rules, 5 prompts — so one person can read every
file in an afternoon.

It is stack-agnostic. Nothing in the commands, agents, or prompts assumes a database, a UI library, or a
host. The per-project truth lives in four generated rule files and in the specs. The worked example in
`examples/ali-store/` happens to be Medusa v2 + Next.js 15 + Bakong KHQR, but that is example content, not
a requirement.

## The problem it solves

Two failure modes kill AI-assisted projects:

- **Vibe coding** — prompting feature by feature with no spec. The agent guesses, scope drifts, and by week
  three nothing fits together.
- **Process bloat** — a 40-page PRD and a rules file so long it eats the token budget on every task.

The fix is a split that runs through the whole framework.

## Specs vs rules — the core split

| | Specs | Rules |
|---|---|---|
| Files | `PRD.md`, `DESIGN.md`, `ImplementPlan.md` | `.claude/rules/{workflow,stack,security,design}.md` |
| Length | Long, descriptive | Short (<80 lines each), prescriptive |
| Location | Project root | `.claude/rules/` |
| Loaded | On demand, when a task needs them | Auto-loaded every task |
| Voice | "Here is what we're building and why" | "ALWAYS do X. NEVER do Y." |

Specs hold the detail. Rules are the thin, always-on guardrails generated *from* the specs. Never fold
spec-length detail into a rule file — it taxes every task's token budget. That single discipline is what
keeps the framework cheap to run.

## The principles

The mistakes this framework refuses to repeat (full text in `FRAMEWORK.md`):

1. **Specs vs rules stay separate, always.**
2. **Commands stay generic; rules carry specifics.** No stack assumption is ever hardcoded into a command,
   agent, or prompt — they point at `.claude/rules/`.
3. **Fixed filenames.** `PRD.md`, `DESIGN.md`, `ImplementPlan.md` — never brand-prefixed. Provenance goes
   in a file header, not the filename, so the workflow stays portable.
4. **Pin exact, adopt deliberately.** No `^`/`~`. Cooldown before adopting fresh releases. Vendor
   security-critical small packages instead of importing them live.
5. **Ask before guessing.** Every generation prompt asks clarifying questions first. Never guess on
   payments, auth, or schema.
6. **Restraint over coverage.** Few files, each understood. The value is sharp opinion, not breadth.

## The pipeline

```
/spec prd → /spec design → /spec plan → /spec rules → build loop
 (PRD.md)    (DESIGN.md)   (ImplementPlan) (.claude/rules/)  /task → /verify → /security-check → /commit → /done
```

Every arrow is a checkpoint. The agent does not advance to the next phase without your approval. Each
generation step asks clarifying questions before it writes. Rules are generated from the specs, so they
stay in sync instead of being hand-maintained per project.

## What's in the box

Copied into a project under `.claude/` (plus a root `CLAUDE.md` and the `prompts/` folder):

**Commands (9)** — bare names by default; namespace under `sdd:` only if your tooling needs it.

| Command | Does |
|---|---|
| `/spec <step>` | Routes to a generation prompt: `prd`, `design`, `plan`, `rules`, `docs` |
| `/task <ID>` | Reads a task from `ImplementPlan.md` and executes only that task |
| `/verify [ID]` | Runs the project's lint/typecheck/build/test gates; reports pass/fail, fixes nothing |
| `/security-check` | Reviews the last task's changes against `security.md` |
| `/security-audit <layer>` | Senior-engineer audit of one of seven layers, before deploy |
| `/commit` | Stages and writes a Conventional Commits message, waits for approval |
| `/done <ID>` | Verifies acceptance criteria, then marks the task ✅ in the plan |
| `/status` | Read-only progress report and the next ready tasks |
| `/clarify <ID>` | Resolves one `⚠️ CLARIFY` item and propagates it to dependent tasks |

**Agents (3)** — model-tiered for cost. `orchestrator` (opus) plans and delegates; `implementer` (sonnet)
runs routine build tasks; `committer` (haiku) handles trivial edits and pre-approved commits. `/task` routes
routine work to `implementer` automatically and keeps payments/auth/schema with the orchestrator; `/commit`
runs interactively at the committer tier.

**Rules (4)** — `workflow.md` (generic, copy as-is); `stack.md` (template, filled per project);
`security.md` and `design.md` (universal base rules plus a project slot).

**Prompts (5)** — `prd`, `design`, `plan`, `rules`, `docs`. The generation flow, run rarely.

**Skills: 0.** Skills are stack-specific procedural knowledge; they belong in a project, not the portable
framework. See "Adding skills" below.

## Install

```bash
cp -r templates/.claude   <your-project>/.claude
cp -r prompts             <your-project>/prompts
cp -n templates/CLAUDE.md <your-project>/CLAUDE.md   # skip if the project already has one
```

The `CLAUDE.md` declares the four rule files authoritative and tells the agent that SDD process wins over
global or plugin conventions — so a global "always TDD / 80% coverage" default doesn't fight the plan. If
the project already has a `CLAUDE.md`, merge in its "Authoritative rules" section. Full steps in
`INSTALL.md`.

## Generate the specs

Each step asks questions first — answer them, don't let it guess.

```
/spec prd "<one-line idea>"   → PRD.md           problem, scope, data model, API contracts, NFRs
/spec design                  → DESIGN.md         tokens, components, responsive behavior
/spec plan                    → ImplementPlan.md  small tasks (≤30 min each) in dependency order
/spec rules                   → .claude/rules/    fills stack/security/design from the specs
```

`/spec design` is library-first: it picks the best-fit system from the
[awesome-design-md](https://github.com/VoltAgent/awesome-design-md) collection and substitutes free fonts
for any proprietary ones, rather than inventing a design from scratch.

Projects with no web frontend (CLI, API-only, library) skip `/spec design` — `/spec rules` then generates
three rule files instead of four, and the build loop runs the same.

Review the plan before building. Answer every `⚠️ CLARIFY` item with `/clarify`. Ten minutes on the plan
saves hours of wrong code.

## The build loop

```
/task BACKEND-03        # implements exactly that task — no extras, no refactors
/verify BACKEND-03      # runs lint/typecheck/build/test gates — must pass before commit
/security-check         # audits the diff against security.md
                        # (you read the diff yourself — 30 seconds catches what the agent misses)
/commit                 # Conventional Commits message, your approval before it lands
/done BACKEND-03        # re-runs /verify, verifies acceptance criteria, then marks it ✅
```

Run `/status` any time for progress and the next ready tasks. A task stops and asks if its spec is
ambiguous, contradicts the PRD or design, or depends on an open `⚠️ CLARIFY` — it never guesses on
payments, auth, or schema.

Once setup tasks have produced real commands and paths, `/spec docs` writes a `README.md` for the project
itself.

## When the spec changes

Specs evolve. To re-sync without losing progress:

1. Edit the spec (`PRD.md` or `DESIGN.md`).
2. `/spec plan` — with a plan already on disk it **amends** instead of regenerating: completed `✅` tasks
   and locked decisions survive, new scope is appended as fresh task IDs, removed scope is marked
   superseded, and any done task the change invalidates is flagged `⚠️ REWORK`.
3. `/spec rules` — re-derives `stack/security/design.md` from the updated specs. Rules are stateless, so
   this is always safe to re-run.

Then resume the build loop on the new and reworked tasks.

## The worked example

`examples/ali-store/` is one fully built instance — a Medusa v2 + Next.js 15 storefront for a Cambodian
clothing seller (Bakong KHQR + cash on delivery, coral `#C0461F` accent, English-only v1). Read it to see
what each artifact looks like filled in:

- `PRD.md` / `DESIGN.md` / `ImplementPlan.md` — the three specs, with a 40-task plan and a "Resolved
  decisions (locked)" block.
- `.claude/rules/` — the rules generated from those specs. `stack.md`, `security.md`, and `design.md` are
  filled; `workflow.md` is the generic file, copied as-is — it carries no project specifics.

The Medusa/Bakong specifics there are example content. Your project's rules will name your stack.

## Why it stays cheap

1. Thin rules, fat specs — rules auto-load every task, so each stays under ~80 lines.
2. Progressive disclosure — rules point to spec sections; read only what the task needs.
3. Fresh-context subagents per task — don't carry conversation history into each task.
4. Model tiering — cheap models for cheap tasks (haiku commits, sonnet builds, opus plans).
5. `/compact` at milestones, not at 95% auto.
6. No examples or prose in rules — teaching lives here, in this README.

## Adding skills

The core ships zero skills on purpose. When a project needs repeatable procedural knowledge for its stack
(a migration recipe, a payment-provider integration pattern), add it as a project-local skill under the
project's own `.claude/skills/` — not to this framework. Keep skills stack-specific and the framework
portable.

## Further reading

- `FRAMEWORK.md` — structure, principles, counts, and the token strategy.
- `INSTALL.md` — copy the template into a project and generate the specs.
- `prompts/` — the five generation prompts `/spec` routes to.
