# SDD Prompt — Generate `DESIGN.md`

Run this **after** `PRD.md` is settled and **before** `ImplementPlan.md`. Produces the project's visual source of truth.

---

Based on `PRD.md` (and any uploaded brand references — screenshots of an existing product, logo, current site), create `DESIGN.md` — the project's design system. This is the spec that `ImplementPlan.md` will reference for every frontend task, and that `.claude/rules/design.md` will enforce at runtime.

## When to skip
If `PRD.md` describes a project with no user-facing web UI (CLI tool, API-only service, library, data pipeline, background worker), there is no design system to build. STOP and tell the user to skip straight to `/spec plan` — do not create a `DESIGN.md`. Only continue when the product has a web frontend.

## Approach — library-first

Browse https://github.com/VoltAgent/awesome-design-md/tree/main/design-md and pick the best-fit brand design system from the ~70 there. The collection is an external resource and may move or be unavailable — if you can't reach it, fall back to writing `DESIGN.md` fresh (see below). Only write a fresh DESIGN.md if no library item plausibly fits the product type.

If picking from the library:
- Read the README + section headers of 3–5 candidates that match the product *category* (apparel e-commerce, SaaS dashboard, content platform, fintech, marketplace, etc.).
- Score against the PRD's key pages, content density, and audience.
- Justify the pick concretely: which patterns map to which PRD requirements. Name the runners-up and why they lost.
- Save the chosen file verbatim into the project root as **`DESIGN.md`** (fixed name across all projects — brand provenance goes in a header at the top of the file, not in the filename).

If writing fresh (only when no library item fits):
- Establish tokens (colors, type, spacing, radius), 3–5 core components, responsive behavior.
- Use one or two library DESIGN.md files as format references.

## Rules

- **Ask 2–4 clarifying questions BEFORE picking/writing** if any of these are unclear:
  - What brand signals exist (logo, colors, screenshots of the current product, references the user wants to emulate)?
  - Is the product image-heavy (commerce, gallery) or text-heavy (docs, dashboard)?
  - What's the density target — minimalist hero (Apple), dense catalog (Amazon-style), editorial (Pinterest)?
  - Any locked brand colors, fonts, or visual non-negotiables already decided?
  - Which languages/scripts must render at launch?
- **Do not silently adopt brand IP.** If the picked system relies on proprietary fonts (Nike Futura, Apple SF) or logos, *explicitly* substitute free near-equivalents (e.g., Inter, Bebas Neue) and call it out.
- **Do not pick a system whose density model contradicts the PRD** — a low-density gallery design is wrong for a 1000-SKU catalog; a dense dashboard system is wrong for a single-product landing.
- **If the PRD specifies an accent or existing brand color that conflicts with the chosen system's accent, keep the project's accent and replace the system's.** Document the substitution.
- **Languages**: if any non-Latin script is required, pick fonts that render those scripts and note the fallback strategy. If the PRD defers a script to v2, note the v1/v2 font split.
- **Don't pad.** A DESIGN.md is read on most frontend tasks — keep it tight, no decorative prose, no aspirational language not backed by tokens.

## Tailoring decisions to lock explicitly

Surface these as a list in your response (not buried inside the file):
- Accent color (with hex), and which token in the source system it replaces.
- Font substitutions (proprietary → free equivalent), per script.
- Language coverage and v1/v2 split if any.
- Tokens removed because they don't apply (e.g., dark-mode tokens if the product is light-only, ramps the project will never use).
- Any component in the source system that's out-of-scope (be aggressive — cut anything not in PRD §2 In scope).

## Validate before committing

Before declaring the design "picked", produce **two visual mockups inline**:
1. The most-visited page (catalog / dashboard / feed).
2. The most-critical conversion flow (PDP / checkout / sign-up).

Use placeholders for images and real PRD content for text (product names, copy, currencies, languages). Confirm with the user before saving.

## Output

After sign-off:
- ✅ `DESIGN.md` saved in project root, with a provenance header at the top crediting the source system (verbatim copy of the library file below the header if library-picked).
- 📋 Tailoring decisions (accent, fonts, substitutions, scope cuts) — explicit list.
- ⚠️ IP / proprietary-asset risks the implementation must avoid (logos, proprietary typefaces, copyrighted imagery).
- 💡 Simplifications this choice enables (e.g., "low-density catalog → no filter sidebar needed in v1", "monochrome chrome → no dark-mode work").
- 🔴 Risks where the chosen design and the PRD pull in different directions.

Usage:
  /spec design
