Act as a senior security engineer performing a focused security audit.

## Layer to audit

Audit this layer: **$ARGUMENTS**

Valid values (descriptions are stack-agnostic — read `.claude/rules/stack.md` for the actual paths in this project):

- `LAYER 1: Database & data layer` — schema, migrations, RLS policies (if used), data-access modules
- `LAYER 2: API & Server` — server routes, route handlers, server actions, custom backend modules
- `LAYER 3: Auth & Session` — login flows, middleware, session handling, OAuth callbacks
- `LAYER 4: Frontend & Client` — components, forms, client-side logic, public bundles
- `LAYER 5: Config & Deploy` — env files, deploy configs, build output, secrets handling
- `LAYER 6: Dependencies` — `package.json`, lockfile review, supply-chain hardening, pinning policy
- `LAYER 7: External integrations & Payments` — payment providers, webhooks/callbacks, proxies, third-party APIs (only if `stack.md` lists these)

If `$ARGUMENTS` doesn't match one of these, STOP and ask which layer. Do NOT pick one yourself.

State the layer you're auditing at the very start of your output.

## Tasks

Find issues that are EXPLOITABLE in **this** codebase, not theoretical. For each finding, describe a concrete attack scenario.

Check for:
- Injection (SQL, command, path, template, deserialization)
- Broken authentication or authorization
- Trust boundary violations (client input trusted as truth)
- Secrets exposure (keys in code, client bundle, logs, URLs)
- Insecure defaults and missing validation
- Privilege escalation paths
- Information leakage via errors, logs, responses
- Logic flaws that can be abused even if code "works"
- Race conditions and TOCTOU issues
- Missing rate limiting on sensitive endpoints

## Project-specific & universal checks

ALWAYS apply the checks in `.claude/rules/security.md` (authoritative for this project) PLUS the universal checks below.

Universal (any stack):
- No secrets in client bundles or any `PUBLIC_*` / client-imported file
- Server-side input validation (the project's schema validator) on every endpoint that accepts user data
- No writes to a raw-HTML sink (`dangerouslySetInnerHTML`, `v-html`, `innerHTML`, …) without sanitization
- Auth/authz verified before every data operation
- Middleware correctly protects routes
- Idempotency on state-changing endpoints that may retry
- Lockfile committed; exact-version pins (no `^`/`~`); known-vulnerable versions absent

Layer 7 — Payments / external integrations (only if the project has them):
- Payment status set ONLY after server-side verification with the provider
- Provider API never called directly from the client
- Webhook / callback signatures verified server-side
- Tokens, references, and PII redacted in logs
- Reservations release on failed/expired payment
- No card data handled, stored, or transmitted (if the project's design avoids PCI scope)

## Severity calibration

- **CRITICAL** — Direct path to data breach, auth bypass, RCE, or financial loss. Exploitable without special access.
- **HIGH** — Exploitable with low complexity. Significant impact but requires some condition.
- **MEDIUM** — Exploitable but requires specific context or chaining.
- **LOW** — Defense-in-depth issue. Not directly exploitable but weakens posture.

If you find more than 3 CRITICAL issues, double-check — over-rating hurts more than under-rating.

## Output format

Use exactly this structure:

```
## Security Audit — Layer [N]: [layer name]

### Summary
- X critical, Y high, Z medium, W low
- Top 3 issues to fix first: [ranked list]

### CRITICAL findings

#### [C-01] Brief title
- **Location**: `path/to/file.ts:line`
- **Attack**: How an attacker exploits this. Concrete steps.
- **Impact**: What they can do.
- **Fix**: Specific code change. Show before/after if helpful.
- **Verification**: How to confirm the fix worked.

[repeat for each finding, numbering H-01, M-01, L-01, etc.]

### HIGH findings
[same format]

### MEDIUM findings
[same format]

### LOW findings
[same format]

### NOT issues (false-positive checks I ran)
List patterns that LOOK suspicious but are actually safe in this context, with reasoning.

### Fix in this order
Ranked list with effort estimate (S = under 30 min, M = 30 min–2 hrs, L = half day or more).
1. [C-01] Title — S
2. [H-01] Title — M
... etc.
```

## Rules

- Be CONCRETE. Every finding must include a real attack scenario.
- Verify exploitability before flagging. If you can't describe how to exploit it, it's not a finding.
- Do NOT flag style issues, naming conventions, or code quality.
- If the codebase is small, audit ALL files in the layer. If large, audit the most security-relevant first and tell me which you skipped.
- If a finding requires assumptions about deployment environment, state the assumption explicitly.
- Output the "NOT issues" section even if empty — proves you considered them.
- Do NOT modify any code. Audit only. Fixes happen in separate tasks.
