# Security Rules — apply on every task

> BASE — the universal rules below apply to any stack. The "Project-specific" section is filled by
> `/spec rules` from `PRD.md §9` + `stack.md`. Security rules outrank a task's wording on conflict.
> See a filled instance in `examples/ali-store/.claude/rules/security.md`. Keep under ~80 lines.

## Authorization (universal)
- ALWAYS verify auth/authz before every data read or write, using the project's authz model (API-layer middleware, Postgres RLS, etc. — see Project-specific).
- NEVER expose admin or privileged keys in client code. Clients use only public/publishable keys.
- ALWAYS scope user-facing routes to the current session/owner — never trust ids from the client without an ownership check.

## API routes (universal)
- NEVER trust client input — validate every request body with the project's server-side schema validator (named in `stack.md`; e.g. zod, Pydantic) before the service layer.
- NEVER return stack traces to the client. Log server-side with a correlation id; return a generic shape.
- ALWAYS rate-limit endpoints that send messages, write records, call paid/external APIs, or start OAuth.

## Secrets (universal)
- NEVER commit `.env*`, keys, tokens, or connection strings. NEVER hardcode a key in source — not even in tests.
- NEVER log request bodies that may contain PII or tokens. Redact.
- ALWAYS read secrets from env vars (see `stack.md` for the required keys).

## Frontend (universal)
- NEVER write to a raw-HTML sink (`dangerouslySetInnerHTML`, `v-html`, `innerHTML`, …) without sanitization.
- ALWAYS escape user-generated content; never trust URL params as identifiers without validation.

## Dependencies (universal)
- ALWAYS pin exact versions (no `^`/`~`); commit lockfiles; install with the CI/deploy lockfile command.
- NEVER add a package published in the last ~7–14 days to backend/payment paths (supply-chain cooldown).
- NEVER install packages with negligible download/usage counts on the registry without reading the source. Verify exact names (typosquatting).

## Payments (universal — only if the project takes payments)
- NEVER trust the client for payment status. A record becomes `paid` only after server-side verification with the provider.
- NEVER call the payment provider directly from the client. NEVER handle card data unless the design explicitly accepts PCI scope.
- ALWAYS keep status checks idempotent and time-bounded; expired reservations release stock.

## Project-specific
> Filled per project by `/spec rules`: the exact authz model, which tables (if any) use RLS, the payment
> provider's verification rules, the customer-data fields collected in v1, and any pinned core versions.

## Before declaring a task complete — self-check
- [ ] Auth/authz verified before every data op?
- [ ] Every new endpoint behind the right middleware?
- [ ] No hardcoded secrets; no wrongly-public client vars?
- [ ] All input validated with the project's validator, all output escaped?
- [ ] Payments: `paid` set only after server-side verification?
- [ ] Versions pinned (no `^`/`~`), lockfile updated?
