# Installing the SDD Framework into a project

The framework is content, not a package. "Installing" means copying the template `.claude/` tree and the
generation prompts into your project, then generating the specs and rules with the `/spec` command.

## 1. Copy the template into your project
From this framework's root, copy into the target project root:

```bash
cp -r templates/.claude  <your-project>/.claude
cp -r prompts            <your-project>/prompts
cp -n templates/CLAUDE.md <your-project>/CLAUDE.md   # skip if the project already has one
```

This gives the project:
- `CLAUDE.md` — declares the four rule files authoritative and SDD process precedence over global/plugin defaults. If the project already has a `CLAUDE.md`, merge the "Authoritative rules" section in rather than overwriting.
- `.claude/rules/` — `workflow.md` (generic, ready to use); `stack.md`, `security.md`, `design.md` (BASE/TEMPLATE — filled in step 3)
- `.claude/commands/` — the 9 commands: `task`, `verify`, `done`, `status`, `clarify`, `commit`, `security-check`, `security-audit`, `spec`
- `.claude/agents/` — `orchestrator` (opus), `implementer` (sonnet), `committer` (haiku)
- `prompts/` — the 5 generation prompts that `/spec` routes to

If your tooling namespaces commands, install them under an `sdd:` namespace so they don't collide with other plugins.

## 2. Generate the specs (each step asks questions first — answer them)

```text
/spec prd "<one-line idea>"   → PRD.md
/spec design                  → DESIGN.md
/spec plan                    → ImplementPlan.md
```

## 3. Generate the rules from the specs

```text
/spec rules                   → fills .claude/rules/{stack,security,design}.md
```

## 4. Build loop

```text
/task <TASK-ID>  →  /verify  →  /security-check  →  /commit  →  /done <TASK-ID>
```

Run `/status` any time for progress and the next ready tasks.

## 5. (Optional) docs

```text
/spec docs                    → README.md, once setup tasks have produced real commands and paths.
```

## Reference
- `examples/ali-store/` — one fully worked instance: filled rules, `ImplementPlan.md`, `PRD.md`, `DESIGN.md`.
- `FRAMEWORK.md` — the principles the framework refuses to break, and the token strategy behind the thin-rules / fat-specs split.
