# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this project is

**Susan Boutique** is a single-file vanilla web catalog for a luxury boutique (Dominican Republic). There is no build system, no framework, no npm — the entire app runs by opening `index.html.html` directly in a browser.

## Files

- `index.html.html` — The complete application (~5 MB). Contains all HTML, CSS (in `<style>`), and JavaScript (in `<script>`) in one file. Images are embedded as base64 data URIs.
- `productos (2) - copia.json` — Product data in JSON format with base64 images. Currently **not fetched** by the app; it is a backup/export. The app reads from `EMBEDDED_DATA` inside the HTML.

## How to run

Open `index.html.html` in any browser. No server required.

## Architecture

All JavaScript is a single `<script>` block at the bottom of `<body>`. Global state:

```js
var products = [];        // loaded from EMBEDDED_DATA at parse time
var settings = { storeName, tagline, whatsapp, instagram };
var activeSection;        // '__ALL__' or a category name
var modalProduct;         // currently open product
var modalImgIdx;          // current image index in modal
```

Startup flow: `EMBEDDED_DATA` → `products` → `init()` → `renderSectionTabs()` + `renderCatalog()`

Key functions:

| Function | Purpose |
|---|---|
| `init()` | Sets store name/tagline, renders tabs and catalog |
| `renderCatalog()` | Filters by `activeSection`, groups by category, renders cards |
| `renderCard(p)` | Returns HTML string for a product card |
| `setSection(c)` | Changes active category filter and re-renders |
| `openModal(id)` | Opens product detail modal |
| `buildWaUrl(p)` | Builds WhatsApp link with product name and price |

## Product data schema

```json
{
  "id": "p001",
  "category": "Sandalias",
  "brand": "Hermès",
  "name": "Sandalias Oran Camel",
  "price": "3500",
  "desc": "Modelo Oran · Color Camel",
  "sizes": ["38", "39"],
  "images": ["data:image/jpeg;base64,..."]
}
```

- `price` — empty string means "Consultar precio" (no price shown)
- `sizes` — empty array hides the sizes section
- `images` — array of base64 data URIs; first image is the card thumbnail

## CSS design tokens

Defined in `:root` at the top of `<style>`:

```css
--verde: #2C4A35      /* header background */
--dorado: #B8923A     /* logo, accents */
--crema: #F7F3EE      /* page background */
--crema-osc: #EDE8E0  /* tabs bar */
```

## Categories

Fixed list: `Carteras, Billeteras, Sandalias, Bikinis, Vestidos, Tops, Pantalones, Shorts, Lentes, Zapatos`

Defined in `var CATEGORIES` at the top of the script. Tabs only render for categories that have at least one product.

## How to add or edit products

Edit the `EMBEDDED_DATA` variable inside `index.html.html`. The JSON file `productos (2) - copia.json` is not loaded by the app — keep it in sync manually if you use it as a source of truth.

## How to configure WhatsApp / Instagram

Set `whatsapp` and `instagram` inside the `settings` object in `EMBEDDED_DATA` (or directly in the `var settings` declaration):

```js
var settings = {
  storeName: 'Susan Boutique',
  tagline: 'Luxury · Femenino · Atemporal',
  whatsapp: '18091234567',   // number only, no +
  instagram: 'susanboutique'
};
```
