# Changelog

All notable changes to the `anchorusapp/design` package are recorded
here. The package follows semver:

- **Patch** (1.0.x) -- typo, comment, lint fix. No selector or token change.
- **Minor** (1.x.0) -- additive: new component, new utility, new icon, new
  token, non-breaking changes to existing components.
- **Major** (x.0.0) -- rename, removal, contract change, breaking visual
  change.

Both consumers (`family-hub-app`, `anchorusapp-control`) bump together
for any minor or major. Branching the design system is forbidden.

## [1.0.0] -- 2026-05-03

Initial extraction from `family-hub-app/css/app.css`. The package is the
new source of truth for Anchor Us Brand v2 tokens, base styles, canonical
components, responsive rules, icon sprite, and brand marks.

### Added

- `dist/tokens.css` -- Brand v2 tokens (sage, gold, copper, linen, ink,
  divider, semantic, event, chart, meal, print, typography, layout,
  radius, shadow, spacing). Plus surface-indirection variables
  (`--sidebar-bg`, `--sidebar-fg`, `--auth-bg`) so consumers can carry
  approved divergences without forking components.
- `dist/base.css` -- reset, typography utility classes (display-lg,
  display, display-sm, title, subtitle, body-sm, caption, eyebrow), bare
  heading defaults, `.icon` family, atomic utility classes (text-*,
  flex, gap-*, mt-*, mb-*, hidden, etc.).
- `dist/components.css` -- canonical shared components: app-shell grid,
  sidebar shell, topbar, content, auth screen, buttons, forms, cards,
  page-header, pills, modals, toasts, segmented control, empty-state,
  skeleton, spinner, dot, avatars, definition list, filter popover,
  settings tabs, install banner, copper accents, notice, sr-only.
- `dist/responsive.css` -- breakpoints at 768/600/480 px, plus
  `display-mode: standalone` (iOS safe-area-inset), `prefers-reduced-motion`,
  and `hover: none` rules.
- `dist/tokens.json` -- token catalog as JSON, for tooling that needs
  programmatic access.
- `dist/sprite.svg` -- canonical icon sprite (27 symbols) with copper
  metallic gradient definition.
- `dist/brand/mark-glyph.svg` -- brand mark (copper-on-transparent
  gradient anchor-heart). Used in auth hero, sidebar, favicons, OG
  cards.
- `dist/brand/mark-full.svg` -- brand wordmark variant.

### Architectural notes

- Pure static-asset package. No PHP autoload, no JS, no build step, no
  tests. Consumers reference assets directly from
  `vendor/anchorusapp/design/dist/`.
- Approved consumer divergences (operator console sage-700 sidebar/auth)
  are expressed via CSS-var override of `--sidebar-bg` and `--auth-bg`,
  not by forking components.
- `body.mode-intimate` toggles the intimate-mode rules (Fraunces hero,
  pill buttons, larger card radius, etc.). Consumers who don't use
  intimate mode simply never add the class.
