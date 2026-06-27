# Implementation Plan: Modern Product Grid

Generated: 2026-06-26
Feature spec: `.claude/features/feature-modern-product-grid/feature.md`

## Summary

A standalone custom collection-grid section (`section-modern-product-grid.liquid` + CSS, no JS) that renders the current collection's products as a premium staggered grid — 4 columns desktop / 2 mobile, with even columns offset down ~half a card. Each card has a notched outline (notch lower-right, product title below it), a CSS-only elliptical hover reveal of the second product image, an accent-colored focus outline, and Dawn-native numbered pagination. Coexists with Dawn's default collection grid (it does not replace it); placement is via the theme customizer for now.

## Human-First Breakdown

### Admin Setup (human tasks in Shopify admin)

No metafields or custom data required. Two practical prerequisites plus placement:

#### Give products a second image (for the hover reveal)
The hover effect reveals each product's **2nd media image** (a lifestyle shot). Products with only one image won't reveal — that's intended (Q7), not a bug.

- [ ] Ensure the helmet products each have a 2nd image uploaded in their media gallery

#### Confirm the collection exists
The section reads whatever collection it's rendered on — no collection picker.

- [ ] Confirm the helmet collection exists and contains the products

#### Place the section (via customizer)
The modern grid **coexists** with Dawn's default grid — it does not replace it globally. For now we add it for testing; final placement (which collections use it, or a dedicated template) is decided later.

- [ ] In the theme editor, open the target collection template
- [ ] Add the "Modern Product Grid" section
- [ ] Hide/remove Dawn's default "Product grid" section on that template if you want only the modern grid
- [ ] (Later, optional) decide whether to create a dedicated `collection.modern-grid` template instead

### Code Preparation (before any visitor touches the page)

1. New section file renders the current collection's products inside a `{% paginate %}` loop.
2. Layout is a CSS Grid: 4 columns desktop, 2 columns mobile.
3. Even columns (2nd & 4th desktop, 2nd mobile) are visually shifted down ~half a card.
4. Each card is a bordered media box with a notch cut from the lower-right corner; the product title sits below in the cleared space, never overlapping the outline.
5. The secondary image is stacked over the primary, hidden by default, and only rendered when the product has a 2nd image.
6. Pagination renders below the grid when product count exceeds the per-page setting.
7. Accent color, products-per-page, optional heading/subheading, color scheme, and padding all come from schema settings.

### Live Behavior (when a user interacts)

1. Page loads → primary images shown, columns staggered, titles in notches.
2. User hovers a card → the secondary image sweeps down over the primary with an elliptical leading edge (smooth, not too fast).
3. User moves the mouse away → the image sweeps back up in reverse, revealing the primary again.
4. User tabs to a card with the keyboard → the card outline turns the accent color (focus state).
5. User clicks/taps a card → navigates to that product's page.
6. Product with only one image → no reveal on hover; everything else behaves identically.
7. User has "reduce motion" enabled → reveal is instant/cross-fade instead of the sweep.

## Files

### New Files
- `sections/section-modern-product-grid.liquid` — markup, schema, asset loading, `{% paginate %}` loop, scoped padding/accent `{% style %}`.
- `assets/section-modern-product-grid.css` — grid layout, column stagger, notched outline, hover reveal, focus state, responsive + reduced-motion rules.

### Modified Files
- `locales/en.default.schema.json` — new `t:` keys for schema labels (section name, settings labels, options).
- `locales/en.default.json` — only if a user-facing rendered string needs a key (e.g. empty-state message). Reuse existing keys where possible.
- `templates/collection.json` — **not edited** (auto-generated). Placement done via customizer.

### Theme Components Reused
- `{% paginate collection.products by section.settings.products_per_page %}` — exact pattern from `main-collection-product-grid.liquid`.
- `{% render 'pagination', paginate: paginate, anchor: '' %}` — Dawn's pagination snippet (loads `component-pagination.css` itself).
- Section wrapper conventions: `color-{{ section.settings.color_scheme }} gradient`, `.page-width`, `.section-{{ section.id }}-padding` with the **0.75** mobile multiplier.
- Dawn responsive image pattern (`image_url` + `srcset` + `sizes`) adapted from `card-product.liquid`.

### Deliberate Deviation (flagged)
- Uses **CSS Grid** rather than Dawn's flex `.grid` / `.grid__item` classes. The per-column stagger is far cleaner with Grid + `nth-child`. Scoped entirely to this component — no theme-wide impact. All other conventions (color schemes, padding, page-width, images, pagination) follow Dawn exactly.

## Build Steps

### Step 1: Section skeleton

**Do:** Create the section file with asset loading, scoped `{% style %}` (padding), the wrapper structure, an empty/placeholder `{% paginate %}` loop, and a minimal `{% schema %}` with a preset.
**Files:** `sections/section-modern-product-grid.liquid`
**Details:**
- Line 1: `{{ 'section-modern-product-grid.css' | asset_url | stylesheet_tag }}`.
- Scoped `.section-{{ section.id }}-padding` block using the `0.75` mobile multiplier (match `main-collection-product-grid.liquid`).
- Outer wrapper: `<div class="color-{{ section.settings.color_scheme }} gradient section-{{ section.id }}-padding">`, inner `.page-width`.
- `{%- paginate collection.products by section.settings.products_per_page -%}` … `{%- endpaginate -%}`, products listed as plain title text for now.
- Schema: `products_per_page` select (4/8/12, default 8), `color_scheme`, padding range pair, `"presets": [{ "name": "Modern Product Grid" }]`. Use `t:` keys.

**Verify:** Section appears in the customizer on a collection template and lists the collection's product titles.

### Step 2: Static markup + base grid

**Do:** Build the real card markup and the base CSS Grid (4 desktop / 2 mobile).
**Files:** `sections/section-modern-product-grid.liquid`, `assets/section-modern-product-grid.css`
**Details:**
- Card markup per product: an `<li>`/`<article>` containing a media box (primary `<img>` via `image_url`/`srcset`/`sizes`) and a title `<a href="{{ product.url }}">` that is the real, accessible link.
- Grid container `display: grid;` — `grid-template-columns: repeat(2, 1fr)` mobile, `repeat(4, 1fr)` at `min-width: 750px`. Gap consistent with theme spacing.
- Square media aspect ratio (helmets) via `aspect-ratio` or a padded ratio box.

**Verify:** Clean 4-col desktop / 2-col mobile grid; every card image + title links to the correct PDP.

### Step 3: Column stagger

**Do:** Offset even columns downward ~half a card.
**Files:** `assets/section-modern-product-grid.css`
**Details:**
- Desktop (≥750px): shift `:nth-child(4n+2)` and `:nth-child(4n)` down via `transform: translateY(var(--mpg-offset))`.
- Mobile: shift `:nth-child(2n)` down.
- Define `--mpg-offset` as ~half the card height; add compensating `padding-bottom` on the grid so shifted last-row cards don't crowd pagination.
- Use `transform` (not margin) so neighbouring columns aren't pushed and rows stay independent.

**Verify:** Staggered premium look on desktop and mobile; no overlap; pagination has clearance below the shifted cards.

### Step 4: Notched outline

**Do:** Draw the card outline with a notch cut from the lower-right corner; reserve title space below.
**Files:** `assets/section-modern-product-grid.css`
**Details:**
- Draw the border on the media box via a pseudo-element (`::before`) so it's independent of content.
- Cut the lower-right notch with a CSS `mask` (full rectangle minus a corner circle/ellipse) — radius tuned to the design.
- Place the title in the cleared lower-right area with enough padding that text never touches the outline (brief requirement).

**Verify:** Notch renders cleanly; title sits in the notch with clear separation from the border; matches the Desktop 5 closeup.

### Step 5: Hover reveal (CSS only — no JS)

**Do:** Reveal the secondary image on hover/focus with an elliptical top-down sweep; reverse on out.
**Files:** `sections/section-modern-product-grid.liquid`, `assets/section-modern-product-grid.css`
**Details:**
- Only render the 2nd `<img>` when `{%- if product.media[1] != null -%}` (Q7 fallback).
- Stack secondary over primary (absolute fill). Hidden by default via a `clip-path` inset from the top.
- On `:hover` and `:focus-within`, animate `clip-path` to full reveal; the bottom edge uses an elliptical curve; `transition` tuned slow-ish for the premium feel.
- Reverse happens automatically when hover/focus ends. **Confirmed no JS needed** — `:hover` + `:focus-within` cover pointer and keyboard; touch falls through to the card link by design.

**Verify:** Smooth premium downward sweep with curved edge; reverses on mouse-out; single-image cards stay static.

### Step 6: Accent color + focus state

**Do:** Wire the accent color setting and the focus outline.
**Files:** `sections/section-modern-product-grid.liquid`, `assets/section-modern-product-grid.css`
**Details:**
- Output `--mpg-accent: {{ section.settings.accent_color }};` in the scoped `{% style %}`.
- Apply accent to the outline on `:focus-within` / `:focus-visible` (keyboard accessibility), matching the Desktop 2 reference.

**Verify:** Tabbing to a card shows the accent-colored outline; mouse users unaffected.

### Step 7: Dynamic data + full schema

**Do:** Finalize all settings and translations.
**Files:** `sections/section-modern-product-grid.liquid`, `locales/en.default.schema.json`, `locales/en.default.json` (if needed)
**Details:**
- Settings: `heading` + `subheading` (optional, empty default), `products_per_page` (select 4/8/12, default 8), `accent_color` (color, default to the neon green), `color_scheme`, `padding_top`/`padding_bottom` (range 0–100 step 4 px).
- Render optional heading/subheading above the grid only when filled.
- All labels via `t:` keys; preset included; no blocks needed.

**Verify:** Every setting works live in the customizer; empty heading hides cleanly; per-page change re-paginates.

### Step 8: Edge cases + polish

**Do:** Handle empty/wrong-template states, long titles, motion, and image loading.
**Files:** `sections/section-modern-product-grid.liquid`, `assets/section-modern-product-grid.css`
**Details:**
- If `collection == nil` or `collection.products.size == 0` → render a graceful message (reuse an existing locale key where possible) instead of erroring.
- Long titles: clamp/wrap within the notch area without breaking the outline.
- `@media (prefers-reduced-motion: reduce)` → replace the sweep with an instant/cross-fade.
- Lazy-load images below the first row (`loading="lazy"`), eager for the first row.

**Verify:** Run the QA checklist in `OUTPUT-qa-debugging.md`.

## Risks & Considerations

- **The notch is the finickiest CSS.** The `mask`-based outline is the primary approach; if cross-browser edges look rough, fallback is an inline SVG stroke outline. Expect a possible QA iteration here.
- **Stagger vs. layout height.** `translateY` doesn't consume layout height, so compensating bottom padding protects the pagination area. Verify with odd product counts and partial last rows.
- **`clip-path` transitions** are well-supported in modern browsers; the reduced-motion path covers edge cases and older browsers.
- **No facets** — using this section on a collection page means losing Dawn's filter/sort there. Accepted in scoping.
- **Coexistence** — the section must be fully self-contained so collections still using Dawn's default grid are completely unaffected. No shared snippet / base-file edits.
- **`collection.json` is auto-generated** — not edited; placement is via the customizer.

## Open Questions

- None blocking. Final placement (which collections use the modern grid, or a dedicated `collection.modern-grid` template) is intentionally deferred — it does not affect building the section now.
