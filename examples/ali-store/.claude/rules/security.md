# Security Rules — apply on every task

Project = Medusa v2 backend + Next.js 15 storefront, Bakong KHQR + COD payments.
**Authorization lives in Medusa's API layer for all commerce data.** Postgres RLS applies *only* to tables created directly in Supabase outside Medusa — for this project, every custom table is in a Medusa module, so RLS is not the authz model. Don't confuse the two.

## Authorization
- NEVER expose the Medusa **admin JWT or admin API key** in client code. Storefront uses only the `publishable key`.
- ALWAYS protect routes under `src/api/admin/...` with Medusa's admin auth middleware. No admin endpoint ships without it.
- ALWAYS scope storefront routes (`src/api/store/...`) to the current session/cart/order — never accept ids from the client without ownership checks.
- If a table IS created directly in Supabase outside Medusa: enable RLS, never `USING (true)`, scope by the right identifier, validate foreign keys not just identity.

## Payments (critical — biggest risk surface)
- NEVER trust the client for payment status. An order becomes `paid` only after server-side verification against Bakong via the proxy, using `reference`/`md5`.
- NEVER call the Bakong API directly from the client. All Bakong traffic goes through the in-Cambodia proxy from the backend.
- NEVER log full Bakong request/response bodies; redact tokens and references in any log line that includes them.
- ALWAYS use the **vendored** `src/modules/bakong-payment/` logic. Do not re-introduce `bakong-khqr` as a live dependency in the payment path.
- NEVER handle, store, or transmit card data anywhere in this codebase. v1 is KHQR + COD only — no PCI scope.
- ALWAYS keep payment status checks idempotent and time-bounded; expired reservations must release stock.

## API routes (Medusa + Next.js)
- NEVER trust client input — validate every request body with zod on the server before touching the service layer.
- NEVER return full error stacks to the client. Log server-side with a correlation id; return a generic shape.
- NEVER include secrets in client bundles. Only `NEXT_PUBLIC_*` for truly public values (Medusa backend URL, publishable key).
- ALWAYS check authentication before any data write or admin read.
- ALWAYS rate-limit endpoints that send Telegram messages, write orders, call Bakong, or initiate Facebook OAuth.
- ALWAYS keep the order-token check on `GET /store/orders/:id/invoice`. Invoices must not be accessible by id alone.

## Secrets
- NEVER commit `.env`, `.env.local`, R2 keys, Bakong tokens, Telegram tokens, or Facebook app secrets.
- NEVER hardcode any key in source — not even temporarily, not even in tests.
- NEVER log request bodies that may contain phone numbers, addresses, tokens, or Bakong payloads.
- ALWAYS use env vars. Backend required keys: `DATABASE_URL`, `REDIS_URL`, `BAKONG_TOKEN`, `BAKONG_PROXY_URL`, `BAKONG_ACCOUNT`, `TELEGRAM_BOT_TOKEN`, `TELEGRAM_CHAT_ID`, `FB_APP_ID`, `FB_APP_SECRET`, `USD_KHR_RATE`, `LOW_STOCK_THRESHOLD`, `DELIVERY_FEE`, `FREE_DELIVERY_THRESHOLD`, plus R2 keys.

## Frontend (storefront)
- NEVER use `dangerouslySetInnerHTML` without sanitization. The invoice template is the only legitimate HTML render path; sanitize at the source.
- NEVER trust URL parameters as identifiers without validation (order ids, variant ids, category handles).
- ALWAYS escape user-generated content (names, addresses, notes) in pages.
- ALWAYS check auth state before showing protected UI.
- NEVER read or persist payment status from the client; poll the server's `/store/payments/khqr/status` endpoint and let the backend decide.

## Customer data
- v1 collects only: full name, phone, delivery address, optional note, optional Facebook identity. No more.
- NEVER add new PII fields without explicit approval.
- ALWAYS treat phone numbers as sensitive: not in logs, not echoed in error messages. Telegram alerts go to the configured private chat only.

## Dependencies
- ALWAYS pin exact versions in `package.json` (no `^`/`~`). Commit lockfiles. Install with `npm ci`, never `npm install`, in CI and deploy.
- NEVER add a package published in the last ~7–14 days for backend or payment paths — cooldown reduces supply-chain risk.
- NEVER install packages with under 100 weekly downloads without reading the source.
- NEVER auto-accept package suggestions — verify the exact name (typosquatting).
- Medusa core stays pinned at `2.15.2` until a deliberate review-and-bump.

## Before declaring a task complete — self-check
- [ ] Does this expose user data without auth?
- [ ] Is every new endpoint behind the right auth middleware (store/admin)?
- [ ] If a new non-Medusa Supabase table was created: is RLS enabled and scoped?
- [ ] Any hardcoded secrets, any new `NEXT_PUBLIC_*` that shouldn't be public?
- [ ] Any unvalidated input (missing zod) or unsanitized output?
- [ ] For payment changes: is `paid` set only after server-side Bakong verification?
- [ ] Versions pinned (no `^`/`~`), lockfile updated?

If any answer is wrong: fix before reporting done.
