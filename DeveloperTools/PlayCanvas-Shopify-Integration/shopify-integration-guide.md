# PlayCanvas Shopify Integration Guide

Architecture, configuration, and code reference for this integration. **For hands-on setup, use [playcanvas-setup-checklist.md](playcanvas-setup-checklist.md).**

---

## Overview

This PlayCanvas project sells products from a Shopify store inside a 3D experience. Commerce runs through **Shopify Storefront Web Components** — a CDN-hosted library that renders product UI as custom HTML elements (`<shopify-store>`, `<shopify-context>`, `<shopify-cart>`, etc.).

PlayCanvas provides the 3D scene and interaction. HTML/CSS overlays are injected into the page DOM (typically `#canvas-wrapper`) on top of the canvas. PlayCanvas scripts bridge 3D triggers/clicks to product popups, cart actions, and checkout.

**This integration does not use:**
- Raw Storefront API GraphQL from PlayCanvas scripts
- JS Buy SDK or Buy Button JS
- A backend/proxy server
- A Storefront access token (Web Components handle storefront auth internally)

---

## Documentation map

| Document | Use when |
|----------|----------|
| [playcanvas-setup-checklist.md](playcanvas-setup-checklist.md) | Copying files, scene entities, launch/publish tests |
| [Product Entity Setup](playcanvas-setup-checklist.md#product-entity-setup) | Building each `Popup-Menu-Trigger` product object |
| [shopify-setup-checklist.md](shopify-setup-checklist.md) | Shopify admin: products, domain, handles |
| [troubleshooting.md](troubleshooting.md) | Symptom → cause → fix |

---

## Supported Commerce Flow

| Feature | Supported |
|---------|-----------|
| Product display (title, image, price, vendor, description) | Yes |
| Product details modal | Yes |
| Variant selection (`<shopify-variant-selector>`) | Yes |
| Compare-at price | Yes |
| Multi-currency country selector (US, CA, GB, AU, EU, JP) | Yes |
| Add to cart | Yes |
| Cart modal | Yes |
| Buy now (Shopify-hosted checkout) | Yes |
| Cart badge count | Yes |
| Sync price to PlayCanvas text entity | Yes |
| Collection browsing / product lists | No |
| Search, customer accounts, order history | No |
| Server-side inventory validation | No |

---

## Architecture

```
PlayCanvas Scene                    DOM Overlay (#canvas-wrapper)
─────────────────                   ──────────────────────────────
injectShopifyWebComponents    →     Loads web-components.js CDN
shopifyHtmlHandler (cart)     →     shopify-cart.html (singleton)
shopifyHtmlHandler (product)  →     popup-menu-shopify.html (per entity)
shopifyProductHandler         →     Binds product handle + price sync
onTriggerEnterShowPopup       →     Opens popup on VIVERSE trigger enter

Shopify Web Components (CDN)    →     Storefront API (internal)
                                    Shopify Checkout (buy now / cart)
```

**Config split:**
- **`shopify-config.mjs`** — store-wide settings (domain, locale, CDN URL)
- **`shopifyProductHandler.productHandle`** — per-product handle on each PlayCanvas entity

---

## Prerequisites

- A Shopify store with products published to the Online Store
- Public store URL (custom domain or `*.myshopify.com`)
- Product **handles** (URL slug from Shopify admin)
- PlayCanvas project with ESM (`.mjs`) script support
- HTML/CSS assets uploaded to the PlayCanvas project
- For VIVERSE builds: `#canvas-wrapper` DOM target for HTML injection

---

## Setup (where to go)

All step-by-step wiring lives in **[playcanvas-setup-checklist.md](playcanvas-setup-checklist.md)** (Phases 1–9):

1. Copy/upload scripts and assets (Phase 1)
2. Create `shopify-config.mjs` from example (Phase 2)
3. Global entities: `ShopifyBootstrap`, `ShopifyCart`, `ShopifyStyles`, `ShopifyUX` (Phase 3)
4. Per-product entities → **[Product Entity Setup](playcanvas-setup-checklist.md#product-entity-setup)** (Phase 4)
5. Launch and publish tests (Phases 8–9)

Shopify admin steps: **[shopify-setup-checklist.md](shopify-setup-checklist.md)**

---

## PlayCanvas quick reference

### Global entities

| Entity (suggested) | Script | HTML/CSS assets |
|------------------|--------|-----------------|
| `ShopifyBootstrap` | `injectShopifyWebComponents` | — |
| `ShopifyCart` | `shopifyHtmlHandler` | `shopify-cart.html`, `cart-icon.html`, `global-modal-backdrop.html` |
| `ShopifyStyles` | `cssInjector` | `Shopify_CSS.css`, `cart-icon.css`, `popup-news-css.css`, `global-modal-backdrop.css` |
| `ShopifyUX` (VIVERSE) | `popupWheelBlocker`, `popupCameraController` | — |

Only **one** entity should include `shopify-cart.html` in **HTML Assets (Array)**.

### Per-product entity (summary)

| Script | Attributes |
|--------|------------|
| `shopifyHtmlHandler` | **HTML Assets (Array):** `[popup-menu-shopify.html]` |
| `shopifyProductHandler` | Product Handle, Html Handler Entity, Price Text Entity, Price Format |
| `onTriggerEnterShowPopup` | Html Handler Entity |

Full hierarchy (`PriceTag`, `3D Screen`, VIVERSE triggers, duplication): **[Product Entity Setup](playcanvas-setup-checklist.md#product-entity-setup)**

### Script load order

`injectShopifyWebComponents` runs early (CDN). Cart singleton injects before or alongside product popups. `shopifyProductHandler` waits on `injectPopupPromise()` from `shopifyHtmlHandler`.

### Cart singleton DOM

After injection, expect exactly one:

```html
<div id="shopify-cart-root" data-shopify-singleton="true">
  <shopify-store store-domain="..."></shopify-store>
  <shopify-cart id="shopify-cart"></shopify-cart>
</div>
```

---

## Configuration

Copy `Scripts/Shopify/shopify-config.example.mjs` → `shopify-config.mjs`. Commit the example; gitignore the live config in shared repos (see project `.gitignore`).

| Setting | Config key | Example | Required | Description |
|---------|------------|---------|----------|-------------|
| Store domain | `storeDomain` | `https://YOUR-STORE.com/` | Yes | `store-domain` on `<shopify-store>` |
| Web Components CDN | `webComponentsCdnUrl` | `https://cdn.shopify.com/storefront/web-components.js` | Yes | Usually leave default |
| Default country | `defaultCountryCode` | `US` | Yes | Initial currency region |
| Default language | `defaultLanguageCode` | `en` | Yes | Store language |
| Fallback product handle | `fallbackProductHandle` | `my-default-product` | Yes | Country-rebuild fallback only |
| Country localStorage key | `localStorageCountryKey` | `shopify-country` | Yes | Persists user's currency choice |
| Supported countries | `supportedCountries` | `[{ code: "US", label: "USD" }, ...]` | Yes | Country selector options |
| Product handle (per entity) | `shopifyProductHandler.productHandle` | `my-product-handle` | Yes | Set on each entity in editor |
| Storefront access token | `storefrontAccessToken` | `null` | No | Not used by Web Components |
| API version | `apiVersion` | `null` | No | Managed by Shopify CDN |

---

## Runtime Flow

1. **App start** — `injectShopifyWebComponents` adds `web-components.js` to `<head>`.
2. **Config exposed** — `ShopifyHtmlHandler` sets `window.__SHOPIFY_CONFIG__` and substitutes placeholders in HTML assets.
3. **Cart inject** — One entity injects `shopify-cart.html` (singleton).
4. **Product inject** — Each product entity injects `popup-menu-shopify.html`; IDs uniquified per entity GUID.
5. **Component upgrade** — `window.Shopify.webComponents.loadComponents()` called after injection.
6. **Product binding** — `ShopifyProductHandler` sets `shopify-context` handle; syncs price to PlayCanvas text if configured.
7. **User opens product** — Trigger enter / click → popup visible → "View product" opens modal.
8. **Variant selection** — `<shopify-variant-selector>` updates price/image.
9. **Add to cart** — `shopify-cart.addLine(event)` → cart modal opens.
10. **Checkout** — `shopify-store.buyNow(event)` or cart checkout → Shopify-hosted checkout.

---

## Interaction (VIVERSE)

**Documented product pattern:** `onTriggerEnterShowPopup` + VIVERSE **TriggerAndAction** with `EntitySubscribeTriggerEnter` / `EntitySubscribeTriggerLeave` and **`local-player`** in tags to filter. See [Product Entity Setup](playcanvas-setup-checklist.md#product-entity-setup).

**Alternative:** `onClickShowPopup` listens for `NotificationCenterSubscribeEntityPicking`. When picked, calls `shopifyProductHandler.showProduct()` if present.

Requirements: product handle set, `popup-menu-shopify.html` injected, cart not open (300ms cooldown after cart close).

For non-Viverse PlayCanvas, call `shopifyProductHandler.showProduct()` from your own pick script.

---

## Code Reference

### `Scripts/Shopify/shopify-config.example.mjs`
Example config with placeholders. Copy to `shopify-config.mjs`.

### `Scripts/Shopify/InjectShopifyWebComponents.mjs`
- **Script name:** `injectShopifyWebComponents` — loads Web Components CDN once.

### `Scripts/Shopify/ShopifyHtmlHandler.mjs`
- **Script name:** `shopifyHtmlHandler`
- **Attributes:** `htmlAssets` (from `HtmlHandler`)
- **Events:** `shopifyHtmlReady`
- **Key methods:** `injectPopupPromise()`, `applyConfigToHtml()`, `getProductLayoutHTML()`

### `Scripts/Shopify/ShopifyProductHandler.mjs`
- **Script name:** `shopifyProductHandler`
- **Attributes:** Product Handle, Html Handler Entity, Price Text Entity, Price Format
- **Methods:** `showProduct()`

### HTML assets
- `shopify-cart.html` — global singleton cart (inject once)
- `popup-menu-shopify.html` — product card + modal (one per product entity)
- `cart-icon.html` + `cart-icon.mjs` — cart badge UI

### Supporting scripts (`Scripts/HtmlInjection/`)
- `OnTriggerEnterShowPopup.mjs` / `OnClickShowPopup.mjs` — open popup from 3D interaction
- `CssInjector.mjs`, `HtmlHandler.mjs` — CSS/HTML injection base
- `popup-news-css.css`, `global-modal-backdrop.html/css` — popup shell and backdrop
- `PopupWheelBlocker.mjs`, `PopupCameraController.mjs` — VIVERSE popup UX

### VIVERSE SDK
- `@viverse/create-sdk.mjs`, `@viverse/create-extensions-sdk.mjs`

---

## Troubleshooting

See [troubleshooting.md](troubleshooting.md).

---

## Security Notes

- Storefront Web Components do not require a client-side access token.
- Never put Admin API tokens in PlayCanvas client scripts or `shopify-config.mjs`.
- Prefer gitignoring `shopify-config.mjs` in shared repos.
- If you migrate to raw Storefront API, use a server-side proxy.

---

## Migration Notes

| Change | Where |
|--------|-------|
| Store domain | `shopify-config.mjs` → `storeDomain` |
| Default/fallback product | `shopify-config.mjs` → `fallbackProductHandle` |
| Supported countries | `shopify-config.mjs` → `supportedCountries` |
| Each product in scene | PlayCanvas editor → `shopifyProductHandler.productHandle` |
| UI labels, styling | `popup-menu-shopify.html`, `Shopify_CSS.css` |
| Cart icon position | `cart-icon.css` |

---

## Appendix: VIVERSE-Specific Notes

- HTML injects into **`#canvas-wrapper`** (fallback: `document.body`).
- Product interaction uses VIVERSE trigger types or entity picking.
- Modal scroll is intercepted (`PopupWheelBlocker`) so the canvas does not steal wheel events.
- Camera zoom disabled while popups open (`PopupCameraController`).
- Cart-close cooldown (300ms) prevents popup opening immediately after closing cart.

For generic PlayCanvas, adjust injection target in `ShopifyHtmlHandler.waitForCanvasUI()` if needed.
