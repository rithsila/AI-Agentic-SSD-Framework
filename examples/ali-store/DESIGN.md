# DESIGN.md — Ali Store

> Provenance: Nike-style editorial commerce system (from the awesome-design-md library), adapted for Ali
> Store. The system's sale-red `#d30005` is replaced by coral **`#C0461F`** as the project accent.
> Proprietary type substituted for free equivalents (Inter + Bebas Neue). This is the source of truth for
> tokens, components, and responsive behavior; `.claude/rules/design.md` enforces it on every frontend task.
> Reconstructed from `ImplementPlan.md` (FRONTEND-01…22) so the worked example is complete.

## Colour tokens
| Token | Value | Use |
|---|---|---|
| `ink` | `#111111` | text, primary buttons |
| `canvas` | `#ffffff` | page background |
| `soft-cloud` | `#f5f5f5` | product-image background, hero band, search pill |
| `hairline` | `#cacacb` | dividers, chip borders |
| `hairline-soft` | `#e5e5e5` | nav/footer separators |
| `mute` | `#707072` | captions, struck original price, secondary text |
| `success` | `#007d48` | in-stock / positive states |
| `accent` | `#C0461F` (coral) | **exactly two uses**: sale price text, "Pay with KHQR" CTA |

No other colors. No default framework ramps (`slate-*`, `zinc-*`, `red-500`). No gradients, shadows, blur,
or glow — the system is deliberately flat; product photography carries the visual energy.

## Typography
- **Inter** — UI text, weights **400** and **500** only (never 600/700).
- **Bebas Neue** — campaign/display tier: 96px / line-height 0.9 / uppercase.
- Scale (named classes): `heading-xl`, `heading-md`, `body-strong` (16/500), `body` (16/400),
  `button-md` (16/500), `caption-sm` (12), `utility-xs` (9, legal/footer row only).
- Body minimum 12px; 9px only for the utility/legal row. English (Latin) only in v1 — no Khmer font stack.

## Spacing & shape
- 8px grid: `gap-2 / p-3 / p-4 / p-6` etc. No arbitrary spacing (`p-[13px]`).
- Pill radius `999px` (`rounded-pill`) for buttons, chips, search.
- Product images: **always `aspect-ratio: 1/1`** on `soft-cloud`, no radius, driven by the grid (never a fixed px size); `next/image` with explicit `sizes`.
- Separators: a single `hairline` divider. No multi-pixel, dashed, or dotted borders.
- Icons: one outline icon set throughout; never mixed libraries, never filled variants.

## Components
Primitives (`src/components/ui/`): **PillButton** (ink bg / white text / `button-md` / 48px height /
12×24 padding / pill radius / ≥44px touch target), **SearchPill** (soft-cloud), **Chip** (active = ink
fill, inactive = hairline border).

Product (`src/components/product/`): **ProductCard** (1:1 image, ID caption, name `body-strong`, price
block — original struck in `mute`, sale in `accent`, full-price shows ink-only), **ProductGrid**,
**VariantPicker** (color swatch dots with ink ring; size pills; zero-stock size disabled + struck;
"N left"/"sold out"), **FilterSidebar** (220px desktop rail / mobile off-canvas drawer), **Gallery**
(1:1 main + thumbnail strip), **BuyBox** (price + "Add to bag" + outline "Pay with KHQR"), **CategoryTabs**
(scrollable chip row + leading search pill).

Layout (`src/components/layout/`): **TopNav** (wordmark + category links + Sale in accent + icons;
hamburger ≤599px; USD/KHR currency toggle), **Hero** (soft-cloud band, eyebrow, Bebas headline, "Shop now"),
**Footer** (link columns + hairline dividers + `utility-xs` copyright), **BottomBar** (persistent mobile
cart/checkout bar — normal flow, never `position: fixed`).

No third-party UI library (no shadcn/Material/Chakra) — the project owns its primitives.

## Responsive
Product grid: **4-up ≥1440 → 3-up desktop → 2-up ≤1023 → 1-up ≤599**. Mobile-first; build the smallest
layout first and test at 360px before declaring a frontend task done. FilterSidebar is a left rail on
desktop, a drawer ≤1023px. The mobile bottom bar appears ≤599px and is hidden on desktop. Adding a
top-level nav entry means updating `TopNav` and `Footer` together.

## Colour mode
Light-mode only in v1. No `dark:` variants, no `color-scheme` metadata.

## Currency display
USD is store default, 2 decimals. KHR formats as whole riel with `៛` (no decimals), converted via
`USD_KHR_RATE` and driven by the TopNav toggle.
