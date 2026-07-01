# PlayCanvas + Shopify (VIVERSE)

Sell Shopify products inside a PlayCanvas / VIVERSE 3D experience using **Shopify Storefront Web Components**. No Storefront API token or backend server is required.

## Start here

| Step | Document |
|------|----------|
| 1. Shopify admin | [Docs/shopify-setup-checklist.md](Docs/shopify-setup-checklist.md) |
| 2. PlayCanvas wiring | [Docs/playcanvas-setup-checklist.md](Docs/playcanvas-setup-checklist.md) |
| 3. Product entities | [Product Entity Setup](Docs/playcanvas-setup-checklist.md#product-entity-setup) |
| 4. Architecture & config | [Docs/shopify-integration-guide.md](Docs/shopify-integration-guide.md) |
| 5. Problems | [Docs/troubleshooting.md](Docs/troubleshooting.md) |

## Quick config

1. Copy `Scripts/Shopify/shopify-config.example.mjs` → `Scripts/Shopify/shopify-config.mjs`
2. Set `storeDomain` (with trailing slash, e.g. `https://yourstore.com/`)
3. Set `fallbackProductHandle` and review `supportedCountries`
4. Set **Product Handle** on each product entity via `shopifyProductHandler` in the PlayCanvas editor

Per-product handles are **not** set in `shopify-config.mjs` — one PlayCanvas entity = one Shopify product handle.

## Sample project

Fork or duplicate the sample PlayCanvas project for pre-wired global entities (`ShopifyBootstrap`, `ShopifyCart`, `ShopifyStyles`, `ShopifyUX`) and a reference product entity (`Popup-Menu-Trigger10` hierarchy).

Scene-only assets (not in script sync): `PopupBG.webp`, `Tag.webp`, `Roboto.ttf` — included in the sample project or upload your own.

## Repository layout

```text
Scripts/Shopify/          Core integration scripts and HTML/CSS
Scripts/HtmlInjection/    HTML injection, popups, VIVERSE UX helpers
@viverse/                 VIVERSE SDK (required for trigger scripts)
Docs/                     Setup checklists and reference guide
```

## What this integration uses

- Shopify Storefront **Web Components** (`web-components.js` CDN)
- PlayCanvas ESM scripts + DOM HTML/CSS overlays in `#canvas-wrapper`
- VIVERSE **TriggerAndAction** for product triggers (`local-player` tag filter)

## What it does not use

- Storefront API GraphQL from client scripts
- JS Buy SDK / Buy Button
- Admin API tokens in PlayCanvas scripts

See [Docs/shopify-integration-guide.md](Docs/shopify-integration-guide.md) for architecture, runtime flow, and code reference.
