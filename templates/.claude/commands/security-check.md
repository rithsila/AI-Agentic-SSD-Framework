Review the files changed in the last task for security issues.

**Authoritative checklist: `.claude/rules/security.md`** (project-specific). Apply every rule in that file plus the universal checks below. If the project's security.md defines a stricter version of a universal check, use the stricter one.

Universal checks (apply on any stack):
- Authorization verified before every data operation. The model varies by stack (Postgres RLS, API-layer middleware, etc.) — use whatever `security.md` says is the project's authz model.
- Input validation on every endpoint that accepts user data (the project's server-side schema validator, never client-only).
- No hardcoded secrets, API keys, tokens, or connection strings anywhere in source.
- No privileged keys in client bundles or any client-imported file (admin JWTs, service-role keys, payment-provider secrets, etc.).
- No injection risks: parameterized queries / ORM only; no shell exec on untrusted input.
- No XSS: no writes to a raw-HTML sink (`dangerouslySetInnerHTML`, `v-html`, `innerHTML`, …) without sanitization; escape user-generated content.
- Error responses don't leak stack traces, schema info, or internal paths.
- New endpoints that touch external APIs, send messages, or write data have rate limiting.
- Versions pinned (no `^`/`~`), lockfile updated.

Output:
- 🔴 CRITICAL — must fix before commit (file:path:line, one-line description)
- 🟡 WARNING — should fix soon (file:path:line)
- 🟢 PASS — what was checked and looks good

Do NOT make changes. Report findings only.
