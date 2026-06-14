Stage and commit the current changes with a professional, Conventional Commits message.

This is committer-tier (haiku) work, but it needs your approval before the commit lands — so run it interactively in the current session: propose, wait, commit. Delegate to the `committer` subagent only for pre-approved or batch commits, where no mid-run approval is needed.

## Steps
1. Run `git status`, `git diff --staged`, and `git diff` to see exactly what changed. If `$ARGUMENTS` is provided, use it as context for what the change is about.
2. Group changes into logical commits. If the working tree mixes unrelated work (e.g. a bug fix + a docs tweak), propose splitting into separate commits rather than one mixed commit.
3. For each commit, propose the message in the format below and WAIT for my approval before committing. Do not commit anything before I approve.

```
type(scope): short imperative subject

Body (only if the change isn't self-evident): what changed and why,
not how. Wrap at 72 characters. Bullets for multiple points:
- point one
- point two

Refs: #ticket   (only if the project uses issue tracking)
```

4. After I approve, create the commit(s).
5. Do NOT push. If I want to push, I'll say "push" — then push to the current branch only, after confirming the branch name with me.

## Message rules
- Conventional Commits types: feat, fix, refactor, perf, docs, style, test, build, ci, chore.
- Subject: imperative mood ("add", not "added"/"adds"), ≤50 chars, no trailing period, lowercase after the colon.
- Body only when the diff isn't self-explanatory; explain the why, wrapped at 72.
- One logical change per commit. Never bundle unrelated work into one commit.
- Match the existing history's conventions if the repo already has a consistent style.

## Do not
- Do NOT add any tool or AI attribution anywhere in the message: no `Co-Authored-By` trailer, no "Generated with…", no mention of Claude, AI, agents, copilots, or automation. The commit describes the change; the author is the repository owner from local git config, same as with any editor.
- Do NOT include emoji unless the existing history already uses them.
- Do NOT push, force-push, rebase, amend published commits, or touch remote branches without an explicit instruction in this session.
- Do NOT commit secrets, `.env*` files, credentials, or large build artifacts. If any are staged, STOP and flag them instead of committing.

## Output
- The proposed commit message(s) for my approval.
- After committing: the short hash + subject of each commit created.
- A note if uncommitted changes remain.

Usage:
  /commit
  /commit "context about what I changed, if the diff is ambiguous"
