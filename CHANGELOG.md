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

## [1.0.5] -- 2026-05-04

Minor release promoting the conversation-thread message bubbles to the
package.

### Added

- `dist/components.css` -- `.msg-row` (with `.msg-bubble-self` modifier
  flipping `flex-direction` to `row-reverse`), `.msg-bubble` (with
  `.msg-bubble-self` modifier swapping the paper background for
  `--sage-100`), `.msg-meta`, `.msg-when`, `.msg-body`.

  Pairs with the existing `.avatar-sm` chrome for chat-style threads.
  The `.msg-bubble-self` modifier is applied to both the row and the
  bubble; on the row it flips avatar position to the right, on the
  bubble it picks up the sage tint that reads as "this is us".

  Promoted from family-hub-app's local `css/app.css` (where the
  selectors had been the canonical chrome for feedback-triage and
  support threads) and anchorusapp-control's local `css/app.css`
  (where they were ported verbatim in CP PR #91 to bring the operator
  /support thread into parity). Both consumers now share a single
  source of truth.

### Migration

Consumers on v1.0.4 can upgrade to v1.0.5 with `composer update
anchorusapp/design`. No markup change required for consumers that
already render `.msg-row` / `.msg-bubble`. Consumers that previously
kept the selectors in their local CSS should remove them in the same
PR that bumps the dep, to avoid duplicate-rule cascade ambiguity.


## [1.0.4] -- 2026-05-04

Minor release promoting the bottom tab bar to the package.

### Added

- `dist/responsive.css` -- `.bottom-tabs` (and its children
  `.tab-item`, `.tab-icon`, `.tab-label`). Hidden by default; activates
  inside the existing `@media (max-width: 768px)` block as a fixed bar
  at the viewport bottom, sized by `--tab-bar-h`, with iOS
  `env(safe-area-inset-bottom)` padding for the home indicator.

  Consumers opt in by setting `--tab-bar-h` on `:root` (e.g. `56px`)
  and rendering a `<nav class="bottom-tabs">` with `.tab-item` children
  in their shell HTML. The existing `.content` `padding-bottom` and
  `.install-banner` offset rules already read `--tab-bar-h` and default
  to `0px` for consumers that don't opt in.

  Promoted from family-hub-app's local `css/app.css`, where the
  selectors lived as a "shippable in two repos" duplicate. Both
  consumers now share a single source of truth -- `anchorusapp-control`
  (operator console) had stub markup with the comment "DS responsive.css
  unhides .bottom-tabs below 768px" that wasn't actually true; this
  release makes the comment accurate.

### Migration

Consumers on v1.0.3 can upgrade to v1.0.4 with `composer update
anchorusapp/design` -- no markup change required for consumers that
already render `.bottom-tabs`. Consumers that previously kept the
selectors in their local CSS should remove them in the same PR that
bumps the dep, to avoid duplicate-rule cascade ambiguity.


## [1.0.3] -- 2026-05-04

Minor release adding the Help Center component family.

### Added

- `dist/components.css` -- Help Center drawer + triggers. Selectors:
  `.help-scrim`, `.help-panel`, `.help-panel-header`, `.help-back`,
  `.help-close`, `.help-category`, `.help-title`, `.help-body` (with
  prose typography for `h1`/`h2`/`h3`/`p`/`ul`/`ol`/`li`/`a`/`code`/
  `img`), `.help-empty`, `.help-topbar-btn` (route-aware `?` button),
  `.help-trigger-btn` (inline `?` icon next to a form label or modal
  title), plus the two media queries (`max-width: 600px` for full-
  width mobile drawer; `prefers-reduced-motion` to drop the slide +
  fade).

  Promoted from family-hub-app's local `css/app.css`. Both consumers
  now ship the same Help Center -- `anchorusapp-control` (operator
  console) gains it for operator runbooks; `family-hub-app` (customer
  app) drops the duplicate from its local CSS. Each consumer brings
  its own JS module + corpus + endpoint; the CSS is the only shared
  surface.

### Migration

Consumers on v1.0.2 can upgrade to v1.0.3 with `composer update
engle/design-system` (or `anchorusapp/design`). No breaking changes.
Consumers carrying local copies of any `.help-*` selectors can delete
those after upgrading.

## [1.0.2] -- 2026-05-03

Patch release fixing one issue surfaced minutes after v1.0.1 published.

### Fixed

- `dist/components.css` -- `.modal-share-btn[hidden] { display: none }`.
  Without this, the class-based `display: inline-flex` on
  `.modal-share-btn` beat the UA-stylesheet `[hidden] { display: none }`
  on specificity, so the share button rendered on every modal
  regardless of whether the JS gate (`#modal-share` `hidden` attribute,
  toggled by `setModalShareTitle` in family-hub-app's `js/app.js`)
  intended it to be visible. The button is now correctly scoped to
  the deep-link-shareable detail modals (Recipes, Lists items, Notes
  items, etc.) and stays hidden on every other modal.

This bug also affected family-hub-app `main` before the design-system
extraction, but only became visible during PR-FHA-1's visual review.

## [1.0.1] -- 2026-05-03

Patch release closing four items surfaced during the family-hub-app
canary adoption (PR-FHA-1).

### Added

- `dist/sprite.svg` -- new symbol `icon-user-plus`. Used by Lists and
  Notes module headers for the "share with someone" affordance. Was
  added to FHA's local sprite during the deep-links work after v1.0.0
  was cut; promoted here so all consumers have it.
- `dist/components.css` -- new components `.modal-header-actions`
  (right-aligned share + close pair) and `.modal-share-btn` (inline
  share affordance in the modal header). Same provenance as the icon
  above.
- `dist/tokens.css` -- new surface-indirection variable
  `--sidebar-fg-dim` (defaults to `var(--ink-dim)`). Lets consumers
  retint the sidebar sub-label without rewriting the `.brand-sub` /
  `.brand-family` rule. Operator console (anchorusapp-control) will
  set this to a translucent linen for legibility on the sage-700
  ground.

### Changed

- `dist/components.css` -- `.topbar` and `.content` padding changed
  from `var(--space-5)` (24 px) to `1.25rem` (20 px). The 24 px value
  was operator-console drift that crept into v1.0.0 during extraction;
  the 20 px value matches family-hub-app's pre-adoption rendering and
  is the canonical brand value.
- `dist/components.css` -- `.sidebar-brand .brand-sub` / `.brand-family`
  re-aligned to 10 px / .08 em / `var(--sidebar-fg-dim)` (defaults to
  `--ink-dim`). v1.0.0 used 11 px / .04 em / `--sidebar-fg`, also
  operator-console drift.

### Migration

Consumers on v1.0.0 can upgrade to v1.0.1 with `composer update
anchorusapp/design`. No breaking changes; no consumer-side rule changes
required. Consumers that carried local drift-compensation overrides
matching the values above can delete those overrides after upgrading.

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
