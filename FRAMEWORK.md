# SDD Framework — structure & principles

A lean, opinionated spec-driven-development framework for AI coding agents. One person can understand every file. Stack-agnostic core; project specifics live in generated rules and a worked example.

## Principles (the mistakes this framework refuses to repeat)

1. **Specs vs rules are separate, always.** Specs (`PRD.md`, `DESIGN.md`, `ImplementPlan.md`) are long, descriptive, at project root, read on demand. Rules (`.claude/rules/*`) are short, prescriptive, auto-loaded every task. Never collapse a spec into a rule file — it taxes every task's token budget.
2. **Commands generic, rules carry specifics.** A command never hardcodes a stack assumption (no Supabase RLS, no Next.js paths in the command itself). It references `.claude/rules/`, which is where per-project truth lives.
3. **Fixed filenames.** `PRD.md`, `DESIGN.md`, `ImplementPlan.md` — never brand-prefixed. Provenance goes in a file header, not the filename, so commands/rules stay portable across projects.
4. **Pin exact, adopt deliberately.** No `^`/`~`. Cooldown before adopting fresh releases. Vendor security-critical small packages instead of importing them live.
5. **Ask before guessing.** Every generation prompt asks clarifying questions first. Never guess on payments, auth, or schema.
6. **Restraint over coverage.** Few files, each understood. The value is sharp opinion, not breadth.

## Directory layout

```
sdd-framework/
├── README.md                       teaching doc — philosophy first, then how to use
├── FRAMEWORK.md                    this file — structure & principles
├── INSTALL.md                      how to drop into a fresh project
├── templates/                      copied into a new project
│   ├── CLAUDE.md                   declares the rules authoritative; SDD process wins over global defaults
│   └── .claude/
│       ├── rules/                  thin, auto-loaded every task (<80 lines each)
│       │   ├── workflow.md         GENERIC — copy as-is, portable across all projects
│       │   ├── stack.md            TEMPLATE — fill per project (pure project facts)
│       │   ├── security.md         BASE — universal rules + a project-specific section
│       │   └── design.md           BASE — universal rules + a token section
│       ├── commands/               9 commands, bare names (namespace under sdd: only if tooling needs it)
│       │   ├── task.md  verify.md  done.md  status.md  clarify.md
│       │   ├── commit.md  security-check.md  security-audit.md
│       │   └── spec.md             routes to the right generation prompt
│       └── agents/                 model-routing layer (3 tiers)
│           ├── orchestrator.md     opus  — plans, delegates, never cheap
│           ├── implementer.md      sonnet — routine build tasks
│           └── committer.md        haiku — commits, trivial edits
├── prompts/                        spec generators (run rarely → prompts, not commands)
│   ├── prd-prompt.md
│   ├── design-prompt.md
│   ├── plan-prompt.md
│   ├── rules-prompt.md             generates stack/security/design from the specs
│   └── docs-prompt.md
└── examples/
    └── ali-store/                  one fully worked instance — the headline proof
        ├── PRD.md  DESIGN.md  ImplementPlan.md
        └── .claude/...             the filled-in rules, as reference
```

## Counts (deliberately small)

- **Skills: 0 in the core.** Skills are stack-specific procedural knowledge; they belong in a project, not the portable framework. Document how to add them; ship none.
- **Slash commands: 9.** Bare names by default (`/spec`, `/task`, …). Namespace them under `sdd:` only if your tooling needs collision-avoidance with other plugins (ECC etc.) — see `INSTALL.md`.
- **Agents: 3** — risk-tiered for model routing (opus / sonnet / haiku). `/task` delegates routine tasks to `implementer`; payments/auth/schema stay with the orchestrator.
- **Rules: 4** — workflow (generic), stack (template), security (base+slot), design (base+slot).
- **Prompts: 5** — the generation flow.

## Token strategy (why it stays cheap)

1. Thin rules, fat specs — rules auto-load every task, so keep each <80 lines; specs hold detail and load on demand.
2. Progressive disclosure — rules point to spec sections; read only what the task needs.
3. Fresh-context subagents per task — don't carry conversation history into each task.
4. Model tiering — cheap models for cheap tasks.
5. `/compact` at milestones, not at 95% auto.
6. No examples/prose in rules — teaching lives in README.
7. One concept per file, no duplication across rules.

## The flow

```
/spec prd  →  /spec design  →  /spec plan  →  /spec rules  →  build loop
  (PRD.md)      (DESIGN.md)      (ImplementPlan)  (.claude/rules/)   /task → /verify → /security-check → /commit → /done
```

Each generation step asks clarifying questions before writing. Rules are generated from the specs, not hand-written per project.

`/spec design` is conditional — skip it for non-UI projects (CLI, API-only, library); `/spec rules` then omits `design.md`.
