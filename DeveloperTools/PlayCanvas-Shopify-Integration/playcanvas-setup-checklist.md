# PlayCanvas Setup Checklist

Start-to-finish checklist for adding this Shopify integration to a **fresh PlayCanvas project**.

**Architecture & config:** [shopify-integration-guide.md](shopify-integration-guide.md)

**Shopify admin first:** [shopify-setup-checklist.md](shopify-setup-checklist.md)

---

## Phase 1 — Copy and upload files

### Required runtime scripts — `Scripts/Shopify/`

- [ ] `InjectShopifyWebComponents.mjs`
- [ ] `ShopifyHtmlHandler.mjs`
- [ ] `ShopifyProductHandler.mjs`
- [ ] `shopify-config.example.mjs`
- [ ] `shopify-cart.html`
- [ ] `popup-menu-shopify.html`
- [ ] `Shopify_CSS.css`
- [ ] `cart-icon.html`
- [ ] `cart-icon.mjs` *(loaded by `cart-icon.html`)*
- [ ] `cart-icon.css`

### Required supporting scripts — `Scripts/HtmlInjection/`

- [ ] `HtmlHandler.mjs`
- [ ] `CssInjector.mjs`
- [ ] `OnClickShowPopup.mjs` *(or `OnTriggerEnterShowPopup.mjs`)*
- [ ] `PopupWheelBlocker.mjs` *(recommended for Viverse)*
- [ ] `PopupCameraController.mjs` *(recommended for Viverse)*
- [ ] `popup-news-css.css` *(popup shell + `.cart-button` styling for `popup-menu-shopify.html`)*
- [ ] `global-modal-backdrop.html` *(injects `#ui-modal-backdrop` for popup input blocking)*
- [ ] `global-modal-backdrop.css` *(styles `#ui-modal-backdrop`)*

### Viverse SDK (required for click/trigger scripts in this project)

- [ ] `@viverse/create-sdk.mjs`
- [ ] `@viverse/create-extensions-sdk.mjs`

---

## Phase 2 — Config file

- [ ] Copy `shopify-config.example.mjs` → `shopify-config.mjs` (same folder)
- [ ] Set `storeDomain` (with trailing slash, e.g. `https://yourstore.com/`)
- [ ] Set `fallbackProductHandle`
- [ ] Set `defaultCountryCode` and `defaultLanguageCode`
- [ ] Review `supportedCountries`
- [ ] Confirm **no Admin API token** added
- [ ] Confirm **no Storefront API token required** for Web Components

---

## Phase 3 — Global scene entities

### `ShopifyBootstrap`

- [ ] Create entity
- [ ] Attach script: **`injectShopifyWebComponents`**
- [ ] No attributes required
- [ ] Script enabled at scene start

### `ShopifyCart` (singleton cart + cart icon)

- [ ] Create entity
- [ ] Attach script: **`shopifyHtmlHandler`**
- [ ] Set attribute **HTML Assets (Array)**: `[shopify-cart.html, cart-icon.html, global-modal-backdrop.html]`
- [ ] Confirm no other entity assigns `shopify-cart.html`
- [ ] `ShopifyHtmlHandler` recognizes `cart-icon.html` and injects it with the same handler (`isShopifyHtml` checks for `cart-icon`)

*Alternative:* use a separate entity with **HTML Assets (Array)**: `[cart-icon.html]` only — both patterns work.

### `ShopifyStyles`

- [ ] Create entity
- [ ] Attach script: **`cssInjector`**
- [ ] Set attribute **CSS Assets**: `[Shopify_CSS.css, cart-icon.css, popup-news-css.css, global-modal-backdrop.css]`

### `ShopifyUX` (Viverse recommended)

- [ ] Create entity (or use existing persistent entity)
- [ ] Attach script: **`popupWheelBlocker`** (no attributes)
- [ ] Attach script: **`popupCameraController`** (no attributes)

---

## Phase 4 — Per-product entities

Repeat the [Product Entity Setup](#product-entity-setup) workflow for each product in the scene.

- [ ] One product entity per Shopify product (e.g. `Popup-Menu-Trigger10`, `Popup-Menu-Trigger11`, `Popup-Menu-Trigger12`…)
- [ ] Each entity has its own **Product Handle** and **Price Text Entity** reference
- [ ] Launch and verify price + popup for each duplicate

---

## Product Entity Setup

To make a Shopify product appear in the 3D scene, create or duplicate a `Popup-Menu-Trigger` entity. The parent entity handles the trigger and Shopify product logic. The `PriceTag` child displays the floating product label. The `3D Screen` child contains the UI elements for the label: background image, tag icon, and runtime price text.

**Important:** `Popup-Menu-Trigger10` is not just a visual object. It is the product trigger, Shopify product handler, popup HTML handler, and VIVERSE trigger container. The visible price tag is a child hierarchy under it.

### Example hierarchy

```text
Popup-Menu-Trigger10
  PriceTag
    3D Screen
      Image          (background — PopupBG.webp)
      Image          (tag icon — Tag.webp)
      Text           (runtime price — referenced by shopifyProductHandler)
```

Each product entity can be duplicated for additional products, such as:

- `Popup-Menu-Trigger11`
- `Popup-Menu-Trigger12`
- `Popup-Menu-Trigger13`
- `Popup-Menu-Trigger14`

Each duplicate should update:

- Product handle
- Product display / price text target
- Position
- Optional visual tag / label

---

### 1. Parent product trigger entity: `Popup-Menu-Trigger10`

**Purpose:** This is the triggerable product entity. It owns the Shopify product behavior and VIVERSE trigger behavior.

**Components:**

- **Collision**
  - Type: Cylinder
  - Radius: 1
  - Height: 2

**Scripts:**

- `shopifyProductHandler`
- `shopifyHtmlHandler`
- `onTriggerEnterShowPopup`

**`shopifyProductHandler` attributes:**

- **Product Handle:** Shopify product handle for this entity.
  - Example: `dark-gable-ladies-relaxed-long-sleeve-tee`
  - The handle comes from the Shopify product URL slug (see [Product handle setup](#7-product-handle-setup) below).
- **Html Handler Entity:** `Popup-Menu-Trigger10` *(this entity)*
- **Price Text Entity:** `Text` *(the Text child under `3D Screen`)*
- **Price Format:** `{price}`

**`shopifyHtmlHandler` attributes:**

- **HTML Assets (Array):** 1
  - `popup-menu-shopify.html`

**`onTriggerEnterShowPopup` attributes:**

- **Html Handler Entity:** `Popup-Menu-Trigger10` *(this entity)*

**VIVERSE Extension:**

- Plugin: **TriggerAndAction**

**Triggers:**

| Trigger | Type | Sync the trigger | Throttle in ms | Tags to filter |
|---------|------|------------------|----------------|----------------|
| Trigger 1 | `EntitySubscribeTriggerEnter` | unchecked | 0 | `local-player` |
| Trigger 2 | `EntitySubscribeTriggerLeave` | unchecked | 0 | `local-player` |

**Important:** The `local-player` tag filter is **required** on **both** `EntitySubscribeTriggerEnter` and `EntitySubscribeTriggerLeave`. Without this tag filter, the trigger may respond to other users or unexpected entities in a multiplayer / VIVERSE scene.

---

### 2. `PriceTag` child entity

**Purpose:** `PriceTag` is the visible 3D label attached to the product. It contains the UI screen, background image, tag icon, and price text hierarchy.

**Components:**

- **Collision**
  - Type: Box
  - Half Extents: 0.433, 0.159, 0.294

- **Render**
  - **Disabled** *(leave the component on the entity, but unchecked in the editor)*

**Note:** The Render component must be present on `PriceTag` for the VIVERSE Media extension to work, even though it is disabled. The visible label comes from the `3D Screen` UI hierarchy and Media plugin behavior, not from the Render mesh.

**VIVERSE Extension:**

- Plugin: **Media**
- Module: **Image**

**Media > Image settings:**

- billboard: enabled
- enable hover background mask: enabled

**Note:** The Media plugin is used for the visible price tag behavior / hover presentation in VIVERSE. The Shopify popup behavior still comes from the parent product trigger and Shopify scripts.

---

### 3. `3D Screen` child entity

**Purpose:** The 3D Screen is the UI container for the price tag elements.

**Components:**

- **Screen**
  - Screen Space: disabled
  - Resolution: 128 x 128
  - Priority: 0

**Transform:**

- Position: 0, 0, 0
- Rotation: 90, 0, 180
- Scale: 0.01, 0.01, 0.01

**Note:** Because Screen Space is disabled, this is a world-space screen attached to the product label.

---

### 4. First `Image` child under `3D Screen`

**Purpose:** This image is the price tag background.

**Components:**

- **Element**
  - Type: Image
  - Width: 100
  - Height: 100
  - Margin: -50, -50, -50, -50
  - Color: white
  - Opacity: 0.735
  - Texture: `PopupBG.webp`

This is the larger rounded / background image behind the price tag UI.

---

### 5. Second `Image` child under `3D Screen`

**Purpose:** This image is the tag icon.

**Components:**

- **Element**
  - Type: Image
  - Width: 12
  - Height: 12
  - Color: white
  - Opacity: 1
  - Rect: 0, 0, 1, 1
  - Texture: `Tag.webp`

This is the small tag icon displayed inside the price label.

---

### 6. `Text` child under `3D Screen`

**Purpose:** This entity displays the Shopify product price. It is referenced by **`shopifyProductHandler` → Price Text Entity**.

**Components:**

- **Element**
  - Type: Text
  - Preset: Center Anchor
  - Anchor: 0.5, 0.5, 0.5, 0.5
  - Pivot: 0, 0.5
  - Auto Width: enabled
  - Auto Height: enabled
  - Font: `Roboto.ttf`
  - Text: leave blank initially
  - Font Size: 9.5

**Note:** The Text field can be blank in the editor because the Shopify script updates it at runtime using the selected product handle and **Price Format**.

---

### 7. Product handle setup

1. Open the product in Shopify.
2. Copy the product URL handle.
3. Paste the handle into **`shopifyProductHandler` → Product Handle**.

**Example:**

Shopify URL:

```text
https://darkgableclothing.com/products/dark-gable-ladies-relaxed-long-sleeve-tee
```

Product Handle:

```text
dark-gable-ladies-relaxed-long-sleeve-tee
```

**Rules:**

- The handle must match the Shopify product handle exactly.
- One product entity = one Shopify product handle.
- Duplicated product entities must each get their own handle.
- The global `shopify-config.mjs` **`fallbackProductHandle`** is only a fallback, not the normal per-product setup.

---

### 8. Duplicating product entities

To create another product:

1. Duplicate `Popup-Menu-Trigger10`.
2. Rename it, for example `Popup-Menu-Trigger11`.
3. Move it to the new product location.
4. Update **`shopifyProductHandler` → Product Handle**.
5. Confirm **Html Handler Entity** points to the duplicated parent (or correct popup handler entity).
6. Confirm **Price Text Entity** points to the **Text** child inside that duplicated product label.
7. Confirm **`onTriggerEnterShowPopup` → Html Handler Entity** points to the correct entity.
8. Confirm VIVERSE **TriggerAndAction** still has:
   - `EntitySubscribeTriggerEnter` with **tags to filter:** `local-player`
   - `EntitySubscribeTriggerLeave` with **tags to filter:** `local-player`
9. Launch and test that the correct product opens.

**Warning:** When duplicating, the most common mistake is leaving **Price Text Entity** or **Html Handler Entity** pointed at the original product entity. Each duplicate should reference its own child **Text** entity unless intentionally sharing a handler.

**After duplicating a product entity, expand the duplicate hierarchy and manually verify that every entity reference points inside the duplicate hierarchy, not the original `Popup-Menu-Trigger10` hierarchy.** PlayCanvas entity references often stay linked to the original after duplication depending on how the template / entity was copied. Check especially:

- **`shopifyProductHandler` → Html Handler Entity**
- **`shopifyProductHandler` → Price Text Entity**
- **`onTriggerEnterShowPopup` → Html Handler Entity**

---

### 9. Product entity testing checklist

- [ ] Parent entity has Collision set to Cylinder.
- [ ] Parent entity has `shopifyProductHandler`.
- [ ] Parent entity has `shopifyHtmlHandler`.
- [ ] Parent entity has `onTriggerEnterShowPopup`.
- [ ] **`shopifyProductHandler` → Product Handle** matches the Shopify product URL handle.
- [ ] **`shopifyProductHandler` → Html Handler Entity** is assigned.
- [ ] **`shopifyProductHandler` → Price Text Entity** points to the Text child.
- [ ] **`shopifyProductHandler` → Price Format** is `{price}`.
- [ ] **`shopifyHtmlHandler` → HTML Assets** includes `popup-menu-shopify.html`.
- [ ] **`onTriggerEnterShowPopup` → Html Handler Entity** is assigned.
- [ ] VIVERSE TriggerAndAction has **Trigger Enter** (`EntitySubscribeTriggerEnter`).
- [ ] VIVERSE TriggerAndAction has **Trigger Leave** (`EntitySubscribeTriggerLeave`).
- [ ] Both VIVERSE triggers include **`local-player`** in tags to filter.
- [ ] PriceTag has the Media plugin configured.
- [ ] 3D Screen is world-space, not screen-space.
- [ ] Image background uses `PopupBG.webp`.
- [ ] Tag icon uses `Tag.webp`.
- [ ] Text child uses `Roboto.ttf`.
- [ ] Runtime price appears on the Text child.
- [ ] Entering the trigger opens the Shopify popup.
- [ ] Leaving the trigger closes or hides the popup if that is the intended behavior.
- [ ] Clicking View Product opens the Shopify product modal.
- [ ] Add to cart works.

**Asset note:** `PopupBG.webp`, `Tag.webp`, and `Roboto.ttf` are scene / editor assets (not in the script sync). Upload them to your PlayCanvas project or substitute equivalent assets.

---

## Phase 5 — Interaction requirements (Viverse)

The documented product entity pattern uses **`onTriggerEnterShowPopup`** with VIVERSE **TriggerAndAction** (`EntitySubscribeTriggerEnter` / `EntitySubscribeTriggerLeave`, **`local-player`** tag filter). See [Product Entity Setup](#product-entity-setup).

For click-based interaction instead of triggers:

- [ ] Viverse entity picking enabled for project
- [ ] `onClickShowPopup` uses `NotificationCenterSubscribeEntityPicking` event
- [ ] Clicking product entity calls `shopifyProductHandler.showProduct()` when handler is present
- [ ] For non-Viverse: plan alternative script to call `showProduct()` on pick

---

## Phase 6 — Cart singleton verification

After Launch, in browser devtools → Elements:

- [ ] Exactly one `#shopify-cart-root`
- [ ] One `data-shopify-singleton="true"`
- [ ] `#ui-modal-backdrop` present in DOM (from `global-modal-backdrop.html`)
- [ ] `<shopify-store store-domain="...">` shows your config domain (not placeholder text)

If multiple cart roots: only one entity should inject `shopify-cart.html`.

---

## Phase 7 — Shopify admin verification

Complete [shopify-setup-checklist.md](shopify-setup-checklist.md), then confirm:

- [ ] Product handle matches PlayCanvas **Product Handle** on each entity
- [ ] `storeDomain` in config matches live storefront

---

## Phase 8 — First launch test (PlayCanvas Launch)

### Console

- [ ] No missing `shopify-config.mjs` import error
- [ ] `[Shopify] Web Components script injected`
- [ ] `[ShopifyHtmlHandler] Fired shopifyHtmlReady`

### Network

- [ ] `web-components.js` loads (200)

### DOM

- [ ] `#shopify-cart-root` present once
- [ ] `shopify-context` handle = your product (not `placeholder-product`)

### Interaction

- [ ] Enter product trigger → popup opens *(or click, if using `onClickShowPopup`)*
- [ ] Title, image, price load
- [ ] "View product" → modal opens
- [ ] Variant selector works
- [ ] Add to cart → badge updates
- [ ] View cart → cart modal opens
- [ ] Buy now → Shopify checkout opens
- [ ] Country selector changes currency display
- [ ] After country change: "View product" still works *(watch for known `open-modal` mismatch)*

---

## Phase 9 — Published / VIVERSE test

- [ ] Publish project to hosted URL
- [ ] Hard refresh / disable cache
- [ ] Shopify CDN not blocked
- [ ] HTML overlays in `#canvas-wrapper`
- [ ] Cart opens in published build
- [ ] Popup scroll does not move 3D camera
- [ ] Camera zoom disabled while popup open
- [ ] Checkout opens (popup blocker not blocking)

---

## Script attribute reference (exact names from code)

| Script name | Attribute title in editor | Type | Required |
|-------------|---------------------------|------|----------|
| `shopifyHtmlHandler` | HTML Assets (Array) | Asset[] | Yes |
| `shopifyProductHandler` | Product Handle | string | Yes |
| `shopifyProductHandler` | Html Handler Entity | Entity | Yes* |
| `shopifyProductHandler` | Price Text Entity | Entity | No |
| `shopifyProductHandler` | Price Format | string | No (default `{price}`) |
| `onClickShowPopup` | Html Handler Entity | Entity | Yes** |
| `onTriggerEnterShowPopup` | Html Handler Entity | Entity | Yes** |
| `cssInjector` | CSS Assets | Asset[] | Yes |
| `injectShopifyWebComponents` | — | — | — |

\* Can resolve from same entity if `shopifyHtmlHandler` is co-located.

\** When `shopifyProductHandler` is on the same entity, click path uses `showProduct()`; still set **Html Handler Entity** for fallback/HTML path.

---

## Known limitations

- One global cart per page (`shopify-cart.html` once)
- Each product needs its own entity + `popup-menu-shopify.html` injection
- Scene entity wiring not in script sync — configure manually in editor
- `shopify-global.html` referenced in code but unused
- Click scripts require Viverse SDK in this project
