# Shopify Integration Troubleshooting

| Symptom | Likely cause | Fix |
|---------|--------------|-----|
| Blank product popup / no title or price | Wrong `storeDomain` in `shopify-config.mjs` | Verify domain matches live store URL with trailing slash |
| Blank product popup | Wrong `productHandle` on entity | Check handle in Shopify admin; set `shopifyProductHandler.productHandle` |
| `placeholder-product` in console warnings | `productHandle` not set on entity | Set attribute in PlayCanvas editor |
| Web Components not loading | CDN blocked or script not injected | Confirm `injectShopifyWebComponents` entity is enabled; check Network tab for `web-components.js` |
| 401/403 errors | Usually N/A for Web Components | This integration does not use Storefront API tokens; if you added raw API calls, remove client-side tokens |
| Products not returned | Product not published or wrong handle | Publish product; verify handle on storefront URL |
| CORS / network errors | Store domain typo or offline store | Fix `storeDomain`; test store in browser |
| Variant unavailable / disabled button | Variant out of stock or unavailable for sale | Check inventory in Shopify admin |
| Checkout opens blank | Popup blocked or Shopify checkout issue | Allow popups; test buy now in normal storefront first |
| Popup blocked | Browser popup blocker | Use cart checkout flow; allow popups for your domain |
| Product image missing | Variant has no image | Add image to variant in Shopify |
| Wrong currency/price | Country selector or Markets config | Check `supportedCountries` and Shopify Markets settings |
| Duplicate cart / cart not updating | `shopify-cart.html` injected more than once | Only one entity should have cart HTML asset |
| Cart modal hidden / not visible | Shadow DOM styling issue | Check console for cart dialog warnings; see `ShopifyHtmlHandler` cart modal styling |
| HTML not appearing | `#canvas-wrapper` not found | Ensure Viverse wrapper exists; handler falls back to `document.body` after retries |
| Country change does nothing | Country selector event not wired | Country changes are handled by `ShopifyHtmlHandler` document delegation (inline HTML scripts do not run when injected via innerHTML) |
| Price not syncing to 3D text | `priceTextEntity` not set or popup not ready | Set `priceTextEntity` on `shopifyProductHandler`; check console for binding logs |
| Wrong product or price after duplicate | Entity references still point to original product | Expand duplicate hierarchy; reassign Html Handler Entity, Price Text Entity — see [Product Entity Setup §8](playcanvas-setup-checklist.md#8-duplicating-product-entities) |
| Popup opens right after closing cart | Cart-close cooldown | Expected 300ms cooldown; wait before entering trigger again |
| Modal scroll not working (Viverse) | Canvas steals wheel events | Ensure `PopupWheelBlocker` and modal scroll handlers are active |
| `shopify-config.mjs` import error | Config file missing | Copy from `shopify-config.example.mjs` |
| Config changes not reflected | HTML cached in PlayCanvas assets | Re-upload HTML assets or republish after editing placeholders |

## Console messages to watch

| Message | Meaning |
|---------|---------|
| `[Shopify] Web Components script injected` | CDN load OK |
| `[ShopifyHtmlHandler] Fired shopifyHtmlReady` | HTML injection complete |
| `[ShopifyHtmlHandler] shopify-cart.html already injected` | Singleton guard working (expected if duplicate) |
| `[ShopifyProductHandler] productHandle is still placeholder-product` | Set product handle on entity |
| `[ShopifyHtmlHandler] canvas-wrapper not found` | DOM target missing; check platform wrapper |

## Quick diagnostic steps

1. Verify `shopify-config.mjs` exists and `storeDomain` is correct.
2. Open devtools → Elements → search for `<shopify-store>` and `<shopify-context>`.
3. Confirm `handle` on `shopify-context` matches a real product.
4. Confirm only one `#shopify-cart-root` with `data-shopify-singleton="true"`.
5. Test in published build with cache disabled.
