# Anchor Us Design System

Brand v2 tokens, components, sprite, and brand marks. Consumed by:

- `family-hub-app` -- customer PWA
- `anchorusapp-control` -- operator console
- `anchorusapp-site` -- marketing site

## What this is

A small, static-asset Composer package. No JS framework, no CSS framework,
no build step, no runtime. The package ships plain CSS, plain SVG, and a
JSON token catalog. Consumers reference the files directly from
`vendor/anchorusapp/design/dist/`.

The canonical brand specification lives in
`family-hub-app/docs/BRAND_V2.md`. This package is the *implementation* of
that spec; the doc is the source of truth.

## Installing in a consumer

Add to the consumer's `composer.json`:

```json
{
    "repositories": [
        {
            "type": "vcs",
            "url": "git@github.com:steengle/anchorusapp-design.git"
        }
    ],
    "require": {
        "anchorusapp/design": "^1.0"
    }
}
```

Then `composer install` (or `composer require anchorusapp/design:^1.0`).

In the consumer's Dockerfile, copy the dist directory into the public path
so the static assets are served by Apache:

```dockerfile
RUN cp -r /var/www/html/vendor/anchorusapp/design/dist /var/www/html/ds
```

**Note:** Composer flattens the path. The vendor directory will be
`vendor/anchorusapp/design/dist/`, not nested under another folder.

In the consumer's `index.php`, link the four CSS files in this order
(tokens first, then base, components, responsive, then the consumer's own
overlay):

```html
<link rel="stylesheet" href="ds/tokens.css?v=<?= $bust ?>">
<link rel="stylesheet" href="ds/base.css?v=<?= $bust ?>">
<link rel="stylesheet" href="ds/components.css?v=<?= $bust ?>">
<link rel="stylesheet" href="ds/responsive.css?v=<?= $bust ?>">
<link rel="stylesheet" href="css/app.css?v=<?= $bust ?>"><!-- consumer overlay -->
```

For the icon sprite, replace any reference to a local `icons/sprite.svg`
with the package version:

```php
<?php readfile(__DIR__ . '/ds/sprite.svg'); ?>
```

The brand mark is at `ds/brand/mark-glyph.svg`; reference it from
`<img>` tags or `<link rel="icon">`.

## Customising for a consumer

Two patterns are supported.

### 1. Surface overrides via CSS variables

For approved divergences, override the surface variables in the
consumer's local `css/app.css`:

```css
/* anchorusapp-control overrides: operator surface uses sage-700 ground
   for sidebar and auth screen so the operator console reads as a
   different room of the same house. */
:root {
    --sidebar-bg: var(--sage-700);
    --sidebar-fg: rgba(255, 253, 248, .85);
    --sidebar-fg-strong: var(--linen);
    --auth-bg: var(--sage-700);
}
```

Available surface variables (defaults match `family-hub-app`):

| Variable              | Default                | Effect                          |
|-----------------------|------------------------|---------------------------------|
| `--sidebar-bg`        | `var(--linen-shade)`   | Sidebar ground                  |
| `--sidebar-fg`        | `var(--ink-muted)`     | Sidebar text (default)          |
| `--sidebar-fg-strong` | `var(--ink)`           | Sidebar text (active/strong)    |
| `--sidebar-border`    | `var(--divider)`       | Sidebar right edge              |
| `--auth-bg`           | `var(--linen-inset)`   | Auth screen ground              |
| `--tab-bar-h`         | `0`                    | Bottom tab bar height (FHA only)|

### 2. Consumer-specific components

Components that are app-specific (operator-only `.cp-table`, customer-only
`.chore-card`, etc.) live in the consumer's `css/app.css`. The package
ships only the *shared* component vocabulary. New shared components need
to prove out in one consumer first; they graduate to the package via a
minor release.

## Local development

To iterate on the design system without a tag-and-bump cycle, add a
Composer path repository to each consumer's `composer.json` (or to a
gitignored `composer.local.json`):

```json
{
    "repositories": [
        {
            "type": "path",
            "url": "../anchorusapp-design",
            "options": { "symlink": true }
        }
    ]
}
```

Now edits in your local design-system checkout are immediately visible
in both consumers (after the deploy script's `cp -r` runs, which most
local dev setups do on file change). The path is relative; if your local
folder is named differently from the GitHub repo, point the `url` at
your local folder.

## Versioning

- **Patch (1.0.x):** typo, comment, lint fix. No selector or token change.
- **Minor (1.x.0):** additive. New component, new utility, new icon, new
  token, non-breaking changes to existing components.
- **Major (x.0.0):** rename, removal, contract change, breaking visual
  change.

**Both consumers bump together for any minor or major release.** Branching
the design system is forbidden -- if `family-hub-app` needs `^1.3` and
`anchorusapp-control` isn't ready, the work is wrong-shaped; rethink.

## Layout

```
dist/
  tokens.css       ← :root variables, no selectors
  base.css         ← reset, typography utilities, .icon, atomic utilities
  components.css   ← shared components
  responsive.css   ← @media queries
  tokens.json      ← token catalog as JSON
  sprite.svg       ← canonical icon sprite (27 symbols)
  brand/
    mark-glyph.svg ← brand mark (copper gradient anchor-heart)
    mark-full.svg  ← wordmark variant
```

## License

Proprietary. See `LICENSE`.
