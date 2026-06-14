# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repository is

This is a **content repository, not a code project.** Every file is Markdown — there is no source code, no `package.json`, no build, lint, or test step. Do not look for one; if asked to "run" or "build" anything, there is nothing to run.

The repository holds the **SDD Framework**: a lean, stack-agnostic spec-driven-development workflow for AI coding agents. The files *are* the deliverable. The layout matches the one specified in `FRAMEWORK.md`:

```
.
├── README.md            teaching doc (philosophy + how-to) — the V2 walkthrough
├── FRAMEWORK.md         structure & principles (authoritative spec for this repo's own shape)
├── INSTALL.md           how to drop the framework into a fresh project
├── CLAUDE.md            this file
├── templates/CLAUDE.md  copied to the target project root — declares rules authoritative, SDD wins on conflict
├── templates/.claude/   copied into a target project as its .claude/ dir
│   ├── rules/           thin, auto-loaded every task (<80 lines each)
│   │   ├── workflow.md  GENERIC — portable as-is
│   │   ├── stack.md     TEMPLATE — fill per project
│   │   ├── security.md  BASE — universal rules + a project slot
│   │   └── design.md    BASE — universal rules + a token slot
│   ├── commands/        9 slash-command bodies (task, verify, done, status, clarify, commit,
│   │                    security-check, security-audit, spec)
│   └── agents/          3 model-tiered subagents (orchestrator=opus, implementer=sonnet, committer=haiku)
├── prompts/             6 generation prompts (prd-, onboard-, design-, plan-, rules-, docs-)
└── examples/ali-store/  one fully worked instance (PRD, DESIGN, ImplementPlan + filled .claude/rules/)
```

## The framework's pipeline

```
/spec prd → /spec design → /spec plan → /spec rules → build loop
PRD.md      DESIGN.md       ImplementPlan   .claude/rules/   /task → /verify → /security-check → /commit → /done
```

The `/spec` command (`templates/.claude/commands/spec.md`) routes to the matching prompt in `prompts/`. Each generation step asks clarifying questions before writing. Rules are *generated from* the specs (`/spec rules` → `prompts/rules-prompt.md`), not hand-written per project.

## File roles — know which kind you're editing

- **`templates/.claude/rules/*`** — prescriptive, auto-loaded every task. Keep each **under ~80 lines**. `workflow.md` is generic; `stack/security/design.md` are BASE/TEMPLATE skeletons with project slots filled by `/spec rules`.
- **`templates/.claude/commands/*`** — slash-command bodies. Generic — they must reference `.claude/rules/`, never hardcode a stack.
- **`templates/.claude/agents/*`** — subagent definitions with YAML frontmatter (`name`, `description`, `model`). The model tier is load-bearing: opus plans, sonnet builds, haiku commits.
- **`prompts/*`** — spec generators, run rarely. Also stack-agnostic; they read `.claude/rules/stack.md` for real paths.
- **`examples/ali-store/*`** — the worked example (see below).

## Editing rules — the framework's own invariants

Preserve the principles the framework exists to enforce (`FRAMEWORK.md §Principles`). Violating them in the framework's own files is the main failure mode:

1. **Specs vs rules stay separate.** Specs (`PRD.md`, `DESIGN.md`, `ImplementPlan.md`) are long and load on demand. Rules are short and auto-load every task. Never fold spec-length detail into a rule file. Keep rule files under ~80 lines.
2. **Commands & prompts stay generic; rules carry specifics.** No stack assumptions hardcoded into a command or prompt — they point at `.claude/rules/`, where per-project truth lives.
3. **Fixed filenames.** `PRD.md`, `DESIGN.md`, `ImplementPlan.md` — never brand-prefixed. Provenance goes in a file header, not the filename.
4. **Restraint over coverage.** The framework is deliberately small (9 commands, 3 agents, 4 rules, 6 prompts). Don't add files or sections speculatively. If you change these counts, update `FRAMEWORK.md §Counts` to match.

## The worked example vs. the generic templates

`examples/ali-store/` is **one concrete project** — a Medusa v2 + Next.js 15 e-commerce store for Cambodia (Bakong KHQR + COD payments, coral `#C0461F` accent). Its `.claude/rules/` are the *filled* versions; the Medusa/Next.js/Bakong specifics there are example content, not framework requirements.

The reusable skeletons live in `templates/.claude/rules/` (generic) — keep these two in sync conceptually: the generic BASE/TEMPLATE files and the filled example files share structure, and the generic files point at the example as a reference instance. When generalizing, edit `templates/`; when extending the example, edit `examples/ali-store/` and keep it consistent with `ImplementPlan.md`'s "Resolved decisions (locked)" block.

> Note: `examples/ali-store/PRD.md` and `DESIGN.md` were reconstructed from `ImplementPlan.md` + the filled rules to complete the example (their headers say so). If you find a contradiction, `ImplementPlan.md`'s locked decisions win.

## Conventions when editing prose

The framework has strong opinions about its own writing (see `prompts/docs-prompt.md` for the full list). Match them:

- Sentence-case headers, not Title Case. No decorative emoji in body prose (the status glyphs `✅ ⚠️ 🔴 ✓` are intentional and load-bearing — keep those).
- Avoid filler adjectives (robust, comprehensive, seamless, powerful, modern) and meta-phrases ("This document…", "Under the hood", "Simply", "Note that").
- Short declarative sentences; specific over abstract. Cut any sentence that isn't load-bearing.
- Command/prompt files end with a `Usage:` block of invocation examples — preserve that pattern.
