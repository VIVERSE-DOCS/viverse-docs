# Shopify Setup Checklist

Use this checklist on the **Shopify admin** side before wiring PlayCanvas.

## Store and products

- [ ] Shopify store is active and on a plan that supports Online Store sales
- [ ] Products are **published** and visible on the Online Store
- [ ] Each product you want in the 3D experience has a known **handle** (URL slug)
  - Admin → Products → select product → handle is in the URL or SEO section
  - Example: `https://admin.shopify.com/store/.../products/ladies-gtjd-tank` → handle is `ladies-gtjd-tank`

## Store domain

- [ ] Note your public store URL:
  - Custom domain: `https://yourstore.com/`
  - Or myshopify: `https://your-store.myshopify.com/`
- [ ] Enter this as `storeDomain` in `Scripts/Shopify/shopify-config.mjs`

## Access token (not required for this integration)

- [ ] **No Storefront access token needed** — this project uses Shopify Storefront Web Components
- [ ] Do **not** create a custom app token for PlayCanvas client scripts
- [ ] If you later need raw Storefront API, use a server-side proxy (not documented here)

## Sales channel / availability

- [ ] Products are available on the storefront channel Web Components use (typically Online Store)
- [ ] Test a product URL loads in a normal browser: `https://yourstore.com/products/YOUR-HANDLE`

## Values to record

| Value | Your value | Goes in |
|-------|------------|---------|
| Store domain | _______________ | `shopify-config.mjs` → `storeDomain` |
| Default product handle | _______________ | `shopify-config.mjs` → `fallbackProductHandle` |
| Product handle (each item) | _______________ | PlayCanvas entity → `shopifyProductHandler.productHandle` |
| Default country | e.g. `US` | `shopify-config.mjs` → `defaultCountryCode` |

## Optional: multi-currency

- [ ] Confirm Shopify Markets / currency support for regions in `supportedCountries`
- [ ] Adjust `supportedCountries` in `shopify-config.mjs` if you need different regions

## Verify before PlayCanvas testing

- [ ] At least one product handle returns data when viewed on your live storefront
- [ ] Store is not password-protected in a way that blocks Web Components (test in incognito)
