# Practice guide — using the SDD framework on an existing C# codebase

A hands-on walkthrough for adopting this framework on a brownfield project: a working C#/.NET
codebase you inherited or bought, where you want to add or change features without vibe-coding.
This is a usage guide, not part of the framework core — the authoritative docs are `README.md`,
`FRAMEWORK.md`, and `INSTALL.md`.

The examples use .NET commands because that's the target project, but the flow is stack-agnostic.

## The one rule for brownfield

You are **describing what exists, then changing one piece at a time** — not rebuilding. Two habits
make or break it:

1. Let `/spec onboard` read the real code. Don't hand it assumptions; let it record what's actually
   pinned and how the project actually builds.
2. Keep every task small and scoped. The framework's "do only the task, no opportunistic refactors,
   no edits outside `Deliverables`" discipline is exactly what protects code you didn't write.

## Step 0 — install the framework into the C# project

From this framework's root, copy into your project root:

```bash
cp -r templates/.claude  <your-csharp-project>/.claude
cp -r prompts            <your-csharp-project>/prompts
cp -n templates/CLAUDE.md <your-csharp-project>/CLAUDE.md   # if it already has one, merge the
                                                            # "Authoritative rules" section in
```

If your tooling already defines `/verify`, `/commit`, `/status`, `/done` (an ECC setup does), install
the SDD commands under an `sdd:` namespace to avoid collisions, and call them as `/sdd:verify` etc.

## Step 1 — onboard the existing code

```text
/spec onboard "the first change you want to make"
```

It will ask a few questions (what you're changing first, what's off-limits, how it builds/tests),
then read the repo and produce:

- `.claude/rules/stack.md` — the .NET version, packages, and real commands found in the code.
- `PRD.md` — a current-state baseline describing what the app does today.
- A list of 🔴 risks it found in the inherited code.

**Check `stack.md` afterward.** For a .NET project the Commands block should end up roughly:

```
## Commands
- Build:   dotnet build -c Release
- Test:    dotnet test
- Run:     dotnet run --project <path/to/App>
- Restore: dotnet restore           # CI: --locked-mode if packages.lock.json exists
- Format:  dotnet format
- EF migration:  dotnet ef migrations add <Name>
- EF apply (dev): dotnet ef database update
```

If onboard couldn't find a real command (e.g. no test project exists), it leaves a note — fill it in
or leave the gate empty. An empty test gate is fine; a wrong command is not.

## Step 2 — security pass on the bought code (do this early)

Code bought off the internet is exactly where this earns its keep. Before building on top of it:

```text
/sdd:security-audit dependencies      # supply-chain: unpinned/stale/abandoned packages
/sdd:security-audit secrets           # hardcoded keys, connection strings, tokens
```

Also run the .NET vulnerability scan and record the result:

```bash
dotnet list package --vulnerable --include-transitive
```

Fix or consciously accept each 🔴 before you start adding features. Don't build on an unknown base.

## Step 3 — define your first change

Open `PRD.md` (the current-state baseline onboard wrote) and **add your change as new scope** — a
short section describing the feature or fix, its data/API touchpoints, and what "done" means. Don't
rewrite the baseline; append to it. Keep v1 small.

Then regenerate the rules and the plan:

```text
/sdd:spec rules     # fills security.md + design.md; keeps the stack.md onboard already wrote
/sdd:spec plan      # amends — it will NOT re-scaffold what already exists
```

When `/spec plan` runs against an existing `PRD.md`, it amends: it adds tasks only for your new
scope. If it tries to emit `SETUP-*` tasks for things that already exist, delete those — they're the
greenfield reflex, not your reality.

## Step 4 — run the build loop, one task at a time

```text
/sdd:task BACKEND-01        # implements exactly that task — nothing else
/sdd:verify BACKEND-01      # runs dotnet build + dotnet test (the gates from stack.md)
/sdd:security-check         # reviews the diff against security.md (you read it too — 30s)
/sdd:commit                 # Conventional Commits message, waits for your approval
/sdd:done BACKEND-01        # re-verifies + checks acceptance criteria, then marks it done
```

`/done` now enforces `/security-check` for any task touching auth, input, secrets, payments, or a new
endpoint — it won't mark such a task done while a 🔴 CRITICAL is open.

Run `/sdd:status` any time for progress and the next ready tasks.

## When the spec changes later

1. Edit `PRD.md` (or `DESIGN.md` if you have UI).
2. `/sdd:spec plan` — amends: done tasks survive, new scope is appended, invalidated work is flagged
   `⚠️ REWORK`.
3. `/sdd:spec rules` — re-derives the rules. Safe to re-run.

## C# brownfield gotchas

- **Don't let it upgrade packages.** Onboard and `stack.md` pin what's already there. Bumping a
  dependency is its own deliberate task, never a side effect.
- **No tests yet?** The verify gate runs `dotnet build` and whatever tests exist. Add a `TEST-XX`
  task when you want coverage on the code you're changing — don't try to backfill the whole app.
- **EF migrations:** generate the migration file in a task; never run `database update` against a
  shared/prod database from the agent. Humans apply to prod after review (this is already in
  `workflow.md`).
- **Lean on your environment's C# reviewers.** If you run ECC, `/security-check` and `/verify` pair
  well with the `csharp-reviewer` and `csharp-build-resolver` agents — they're not part of this
  framework but complement it on .NET.
- **One branch, one developer.** This framework tracks task state in a single `ImplementPlan.md`. If
  two people work it at once they'll conflict on that file — coordinate, or work separate branches.

## Command cheat sheet

| Command | When | What it does |
|---|---|---|
| `/spec onboard` | Once, at the start | Reads the existing code → `stack.md` + baseline `PRD.md` |
| `/spec rules` | After editing `PRD.md` | Fills `security.md` + `design.md` |
| `/spec plan` | After editing `PRD.md` | Adds/ amends tasks for your changes only |
| `/task <ID>` | Per change | Implements exactly one task |
| `/verify [ID]` | After `/task` | Runs build + test gates; fixes nothing |
| `/security-check` | After `/task` | Reviews the diff against `security.md` |
| `/commit` | After verify passes | Conventional Commits message, your approval |
| `/done <ID>` | To close a task | Re-verifies + acceptance criteria, then marks ✅ |
| `/status` | Any time | Progress and next ready tasks |
| `/clarify <ID>` | When a task is blocked | Resolves one `⚠️ CLARIFY` and propagates it |
| `/security-audit <layer>` | Before deploy / on bought code | Senior-level audit of one layer |

## The mental model

Greenfield asks "what should this be?" Brownfield asks "what is this, and what's the smallest safe
change?" `/spec onboard` answers the first half so the rest of the loop can stay focused on the
second. Describe honestly, change narrowly, verify every step, and read the bought code's security
before you trust it.
