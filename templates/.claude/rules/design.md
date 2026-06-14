# Design Rules — apply on every frontend task

> BASE — universal discipline below. The "Tokens (project)" section is filled by `/spec rules` from
> `DESIGN.md`, which is the source of truth for this project's tokens, components, and responsive behavior.
> See a filled instance in `examples/ali-store/.claude/rules/design.md`. Keep under ~80 lines.

## Tokens — non-negotiable
- ALWAYS use the named tokens defined from `DESIGN.md`. NEVER write arbitrary hex/rgb or off-token utilities (`bg-[#abc123]`, `bg-zinc-700`). If a value isn't in `DESIGN.md`, STOP and ask before adding one.
- NEVER use a framework's default color ramps (`bg-slate-*`, `text-zinc-*`) when the project has its own tokens.
- Spacing follows the project's grid (see Tokens). NEVER arbitrary spacing (`gap-[7px]`, `p-[13px]`).

## Components — reuse, don't reinvent
- ALWAYS use the project's existing primitives and components. NEVER write a custom button/card/chip inline when one exists.
- If a needed component doesn't exist, STOP and ask — a new component is its own task.
- NEVER add a third-party UI library when the project has its own primitives mapped to its tokens.

## Typography
- ALWAYS use the typography classes derived from `DESIGN.md`'s scale. NEVER raw `text-[14px]` or off-scale weights.
- Stay within the project's declared font families and weights.

## Layout & responsive
- ALWAYS use the breakpoints declared in `DESIGN.md`.
- ALWAYS mobile-first — write the smallest layout first, then add breakpoint variants. Test the smallest width before declaring a frontend task done.

## Decoration & colour mode
- Match the project's decoration policy from `DESIGN.md` (flat vs. shadowed; light-only vs. dark-mode). Do NOT introduce gradients, shadows, blur, or `dark:` variants unless the design declares them.
- Use the project's divider/border convention from `DESIGN.md`; don't improvise multi-pixel or dashed borders.

## Tokens (project)
> Filled per project by `/spec rules` from `DESIGN.md`: the exact color/spacing/type tokens, the
> component inventory, the breakpoints, the accent-usage rule, and the decoration policy.

## Before declaring a frontend task complete — self-check
- [ ] Every color used is a named token. No arbitrary hex.
- [ ] Every spacing value is on the grid. No arbitrary px.
- [ ] No new component invented — only reused or extended.
- [ ] No third-party UI library added.
- [ ] Layout tested at the smallest breakpoint.
- [ ] Decoration matches the design's policy (no stray shadows/gradients/dark mode).
