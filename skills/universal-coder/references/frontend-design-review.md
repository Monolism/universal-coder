# Frontend / UI Design Review

Use for: UI critique, redesigns, "make this look better," design systems, accessibility passes,
responsive layout fixes, and motion/micro-interaction polish.

## When to use

- A visual interface (web, mobile, desktop shell) needs to be designed, redesigned, or audited.
- The request is about hierarchy, spacing, color, typography, layout, theming, or motion rather
  than pure backend logic.
- "Bland" designs that need boldness, or "loud" designs that need calming down, both apply here.

## Core review axes

Work through these in order — each builds on the previous one:

1. **Information architecture** — Is content grouped by what the user is trying to do, not by
   what the database schema looks like? Is there one clear primary action per screen?
2. **Visual hierarchy** — Size, weight, and color should encode importance. If everything is
   bold, nothing is. Check that the eye lands on the right element first.
3. **Spacing and rhythm** — Use a consistent spacing scale (4/8px multiples are the common
   default). Inconsistent gaps are the single most common "this looks unpolished" tell.
4. **Typography** — No more than 2 font families. Line-length under ~75 characters for body
   text. Check contrast ratio (WCAG AA: 4.5:1 for body text, 3:1 for large text).
5. **Color** — Define semantic tokens (`--color-accent`, `--color-danger`) rather than literal
   hex values scattered through code. Verify dark-mode variants exist and aren't just inverted.
6. **Responsive behavior** — Test at phone width (~375px) explicitly, not just by shrinking a
   desktop layout. Side gutters, not edge-to-edge content, at narrow widths.
7. **Motion** — Micro-interactions should communicate state change (loading, success, error),
   not decorate. Respect `prefers-reduced-motion`.
8. **Edge cases** — Empty states, loading states, error states, and extremely long content
   (names, numbers) need explicit designs, not just the happy path.

## Anti-patterns to flag

- Walls of unbroken text with no visual grouping.
- Low-contrast gray-on-gray text that fails accessibility checks.
- Buttons that don't look clickable, or links that look like buttons.
- Forms with no inline validation feedback until submit.
- Animations longer than ~300ms for routine UI feedback (feels sluggish).

## Workflow for a review or redesign

1. Capture the current state (screenshot or read the rendered DOM/accessibility tree).
2. Run through the 8 axes above and list concrete, file-and-line-anchored findings — not vague
   "improve the UX" statements.
3. Propose the smallest change that fixes the highest-impact issue first; don't redesign the
   whole screen to fix a contrast bug.
4. After applying changes, re-render and verify at both desktop and mobile widths before calling
   the work done.

## Escalating beyond this file

This is a manual review checklist, not an automated detector. If a dedicated frontend-design
skill with real browser-based antipattern/contrast/font-fingerprint detection is installed and
available, run that for anything beyond what can be verified by eye in a screenshot or DOM
read — see `escalation-and-scope.md`.
