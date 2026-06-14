# PRD — Ali Store

> Provenance: worked-example spec for the SDD Framework. Reconstructed from `ImplementPlan.md` and the
> filled `.claude/rules/` so the example is complete. Treat `ImplementPlan.md`'s "Resolved decisions
> (locked)" as the authority where they overlap.

## 1. Problem & Goal
A small Cambodian clothing seller takes orders manually over Facebook and Telegram. The goal is a
self-serve storefront where customers browse the catalog, pay by Bakong KHQR or choose cash on delivery,
and the team gets notified automatically — without the seller handling card data or a heavy ops stack.

Success criteria for v1:
- A customer can go catalog → product → cart → checkout → paid (KHQR) or pending (COD) unaided.
- Every placed order pushes a Telegram alert to the team.
- Stock decrements only after a payment is verified server-side (KHQR) or an order is confirmed (COD).

## 2. Scope

**In scope (v1)**
- Catalog: categories, product cards, PDP with color/size variants and live stock.
- Cart + checkout with delivery details (name, phone, address, note).
- Payments: Bakong KHQR (Individual, via in-Cambodia proxy) + cash on delivery.
- Dual currency display (USD default, KHR via env rate).
- Optional Facebook login (guest checkout is the default path).
- Custom stock ledger (`stock_movement`) and admin stock-in; sales + stock reports.
- Telegram order alerts; printable invoice (VAT line wired but off).
- English-only UI.

**Out of scope (defer to v2)**
- Khmer UI/copy and Khmer font stack.
- Admin-editable exchange rate (env var only in v1).
- Card payments / any PCI scope.
- Customer accounts beyond optional FB identity link; order history UI.
- Promotions/discount engine beyond per-variant sale price.
- Multi-warehouse, returns, RMA.

## 3. User Flows
1. **Browse → buy (KHQR).** Home → category/PDP → pick color+size (out-of-stock sizes disabled) → Add to bag → cart → checkout (KHQR) → KHQR screen shows QR + deeplink + countdown → app polls status → on verified `paid`, confirmation + invoice link.
2. **Browse → buy (COD).** Same up to checkout → choose COD → order placed `pending_confirmation` → "our team will contact you" + Telegram/Facebook buttons + invoice link.
3. **Optional FB login.** At checkout, "Continue with Facebook" → OAuth → name prefilled, identity linked.
4. **Admin stock-in.** Admin posts a stock movement (`type=in`) → inventory level rises, ledger row written.

## 4. Data Model
Medusa core supplies `Product`, `ProductVariant`, `Customer`, `Order`, `Region`. Two custom modules add:

```
ProductVariant 1───* stock_movement        Customer 1───* customer_social_identity
   (color, size,        (id, variant_id,        (Medusa)        (id, customer_id,
    SKU, USD/KHR,         type in|out|adjust,                     provider=facebook,
    inventory)            quantity, reason,                       provider_user_id,
                          order_id?, created_by,                  created_at)
Order ──────────────────  created_at)
 status: pending_payment | pending_confirmation | paid | cancelled
```

- **stock_movement** — `id`, `variant_id`, `type` (`in|out|adjust`), `quantity` (int), `reason` (text), `order_id` (nullable), `created_by` (text), `created_at`.
- **customer_social_identity** — `id`, `customer_id`, `provider` (default `facebook`), `provider_user_id`, `created_at`.
- **Region/currency** — one Cambodia region exposing `usd` (store default) + `khr`.

## 5. Tech Stack
See `.claude/rules/stack.md` for the locked, pinned list. In short: Medusa **v2.15.2** + Node 20 + TS
backend (Proxmox VM, Cambodia); Next.js **15** App Router + React 19 + Tailwind **v4** storefront (Vercel);
Postgres (Supabase dev / Proxmox prod); Redis; Cloudflare R2 + CDN; Bakong KHQR via vendored module +
proxy; Telegram Bot API; Facebook OAuth.

## 6. Architecture Overview
Storefront (Next.js, Server Components by default) talks to Medusa via the JS SDK with the **publishable
key only** — never the admin API. Payment status is owned by the backend: the storefront polls
`/store/payments/khqr/status` and never decides `paid` itself. All Bakong traffic leaves the backend
through the in-Cambodia HTTP proxy. Order-placed events fan out to a Telegram subscriber. A scheduled job
releases reservations for unpaid KHQR orders past their `expires_at`. Authorization is Medusa's API-layer
auth (admin middleware for `/admin/*`; session/cart/order scoping for `/store/*`).

## 7. API Design
Custom endpoints (standard Medusa store reads — regions, products, product-categories, carts — used as-is):

| Method & path | Auth | Body / query | Response | Errors | Task |
|---|---|---|---|---|---|
| `POST /store/payments/khqr/start` | session | `{cart_id, currency}` | `{qr, deeplink, reference, expires_at}` | 409 out-of-stock, 502 proxy/Bakong | BACKEND-03 |
| `GET /store/payments/khqr/status` | session | `?reference=` | `{status: pending\|paid\|expired}` | — | BACKEND-03B |
| `POST /store/orders/cod` | session | `{cart_id, phone, name, address, note}` | `{order_id, status:"pending_confirmation"}` | 409 out-of-stock | BACKEND-04 |
| `GET /store/auth/facebook` | none | — | 302 → Facebook OAuth | — | BACKEND-05 |
| `GET /store/auth/facebook/callback` | none | `?code=` | session + `{customer}` | 401 | BACKEND-05B |
| `GET /store/orders/:id/invoice` | order-token | — | printable HTML (VAT line off by default) | 403, 404 | BACKEND-06 |
| `POST /admin/stock-movements` | admin | `{variant_id, type, quantity, reason}` | adjusted level + ledger row | — | BACKEND-07 |
| `GET /admin/reports/sales` | admin | `?from=&to=` | `{orders, revenue, top_variants[]}` | — | BACKEND-08 |
| `GET /admin/reports/stock` | admin | `?low_threshold=5` | `{levels[], low_stock[]}` | — | BACKEND-08B |

## 8. UI/UX Requirements
Editorial, flat, photography-led commerce system (see `DESIGN.md`). Light-mode only, English only.
Key pages: **Home** (hero + category tabs + product grid); **Category** (filtered grid + sidebar/drawer);
**PDP** (gallery + variant picker + buy box with KHQR CTA); **Cart**; **Checkout** (delivery form + payment
choice); **KHQR pay** (QR + countdown + poll); **Order confirmation** (paid vs COD variants).

## 9. Non-Functional Requirements
Security and conventions are enforced by `.claude/rules/security.md` and `stack.md`. Highlights:
payments verified server-side only; no card data; secrets in env; zod validation at every boundary;
rate-limit message/order/Bakong/OAuth endpoints; order-token-gated invoices; exact-pinned deps with
committed lockfiles and `npm ci`; phone numbers treated as sensitive (never logged). Mobile-first;
test layouts at 360px. Browsers: current Chrome/Safari/Firefox + mobile Safari/Chrome.

## 10. Open Questions
Tracked as `⚠️ CLARIFY` items in `ImplementPlan.md`. Still pending at v1 build start: delivery fee +
free-delivery threshold values (CLARIFY-04), Bakong credentials/proxy URL (CLARIFY-05 creds), Telegram
bot token + chat id (CLARIFY-06), final domain + subdomains (CLARIFY-08), product/stock CSV format
(CLARIFY-09). Each builds against env placeholders or sandbox until provided.
