# Theme Analysis — Dawn (Shopify Reference Theme)

Generated: 2026-06-25

## Summary

This is **stock Shopify Dawn, version 15.4.1**, theme author "Shopify" (confirmed via `config/settings_schema.json` → `theme_info`). No custom sections, snippets, or naming patterns were found that deviate from the official Dawn release — every file in `sections/`, `snippets/`, and `assets/` matches Dawn's standard file set. This is effectively a fresh, unmodified starting point. No features have been built yet (`.claude/features/` is empty) and no reference designs exist yet (`.claude/context/reference/` is empty).

## File Structure Overview

- **54 sections** (incl. 2 JSON section groups: `header-group.json`, `footer-group.json`) — all stock Dawn names (`image-banner.liquid`, `multicolumn.liquid`, `main-product.liquid`, `cart-drawer.liquid`, etc.)
- **37 snippets** — stock Dawn snippets (e.g. `card-product.liquid`, `meta-tags.liquid`, `icon-*.liquid`)
- **185 asset files** — CSS components (`component-*.css`), section-specific CSS (`section-*.css`), JS modules, and SVG icons. All stock Dawn.
- **14 templates**, **2 layout files** (`theme.liquid`, `password.liquid`), **51 locale files**, standard `config/settings_schema.json` + `settings_data.json`.
- Nothing custom or unusual was found. This is a clean baseline — any future feature work will be the first customization layer on top of Dawn.

## CSS Conventions

### Grid System

Flexbox-based, not CSS Grid (except a few isolated grid utility classes like `.utility-bar__grid` and `.header`). Core layout grid: `.grid` (flex, `flex-wrap: wrap`) with `.grid__item` children sized via `calc()` percentage widths. Column count controlled by modifier classes: `.grid--2-col`, `.grid--3-col`, `.grid--{n}-col-desktop`, `.grid--{n}-col-tablet`, `.grid--{n}-col-tablet-down`. Slider behavior layered on via `.slider`, `.slider--tablet`, `.slider--desktop`, `.grid--peek` (peek-style carousels), driven by the `<slider-component>` web component.

### Breakpoints

- `max-width: 749px` → mobile (`.small-hide` hides above this)
- `min-width: 750px` → tablet/small-desktop (most common breakpoint)
- `750px–989px` → tablet-only (`.medium-hide`)
- `min-width: 990px` → desktop (`.large-up-hide` hides above this)

These three values (`750px`, `990px`, plus the implicit mobile default) are the only breakpoints used theme-wide. Always follow this exact set — don't introduce new breakpoints.

### Color Variables

Color is scheme-based, not a flat palette. Each `color_scheme` (`scheme-1`, `scheme-2`, etc., defined in `settings_data.json` under `color_schemes`) generates a `.color-{scheme.id}` class in `theme.liquid` with CSS vars scoped to that class:
- `--color-background`, `--color-foreground`, `--color-background-contrast`, `--color-shadow`
- `--color-button`, `--color-button-text`, `--color-secondary-button`, `--color-secondary-button-text`, `--color-link`
- `--color-badge-foreground`, `--color-badge-background`, `--color-badge-border`

All are stored as `r,g,b` triples (no `rgb()` wrapper in the var itself) so usage is always `rgba(var(--color-foreground), <alpha>)` or `rgb(var(--color-background))`. To theme a section, apply a `color-{{ section.settings.color_scheme }}` class — never hardcode hex colors.

Other global custom properties set in `theme.liquid` `:root`: typography (`--font-body-family`, `--font-heading-scale`, etc.), spacing (`--spacing-sections-desktop`, `--grid-desktop-horizontal-spacing`), and per-component radius/border/shadow tokens (`--buttons-radius`, `--inputs-radius`, `--product-card-corner-radius`, etc.) — all driven by `config/settings_schema.json` theme settings, not hardcoded.

### Naming Convention

BEM-style with Dawn's specific dialect: `.block`, `.block__element`, `.block--modifier`. Examples: `.banner__media-half`, `.rich-text__wrapper--left`, `.multicolumn-card__image-wrapper--half-width`. Section-scoped dynamic styles use the section-id pattern: `.section-{{ section.id }}-padding`, `#Banner-{{ section.id }}`.

### Spacing Patterns

No utility spacing classes (no `.mt-4` style utilities). Spacing is component-specific and controlled via section schema range settings (`padding_top` / `padding_bottom`) rendered into a scoped `{% style %}` block per section (see Section Padding Approach below). Section-to-section spacing uses `--spacing-sections-desktop` / `--spacing-sections-mobile` (global theme settings) applied via `.section + .section { margin-top: var(...) }` in `base.css`.

### Page Width / Containers

`.page-width { max-width: var(--page-width); margin: 0 auto; padding: 0 1.5rem; }` (padding increases to `0 5rem` at 750px). `--page-width` itself comes from the theme's global `page_width` setting (`settings.page_width / 10`rem). Related variants: `.page-width--narrow` (max 72.6rem on desktop), `.page-width-desktop` (no padding below 990px, then matches `--page-width`).

## JavaScript Conventions

### Base Files (do not modify)

- `global.js` — Defines most core web components: `quantity-input`, `menu-drawer`, `header-drawer`, `modal-dialog`, `bulk-modal`, `modal-opener`, `deferred-media`, `slider-component`, `slideshow-component`, `variant-selects`, `product-recommendations`, `account-icon`, `bulk-add`. This is the largest, most central file in the theme.
- `pubsub.js` — Minimal pub/sub utility (`subscribe`, `publish`, `unsubscribe`).
- `constants.js` — Shared constants: `ON_CHANGE_DEBOUNCE_TIMER`, `PUB_SUB_EVENTS` (the canonical event-name registry).
- `details-disclosure.js`, `details-modal.js` — Generic `<details>`-based disclosure/modal primitives, base classes for menus.
- `animations.js` — Scroll-reveal animation controller (only loaded if `settings.animations_reveal_on_scroll`).
- `cart.js`, `cart-drawer.js`, `cart-notification.js` — Cart state/UI, loaded conditionally based on `settings.cart_type`.
- `theme-editor.js` — Shopify theme editor (customizer) integration hooks.

Per CLAUDE.md rules, none of these should be edited unless absolutely unavoidable — discuss first if a change seems required.

### Existing Components

Dawn registers ~35 custom elements across the theme. Most relevant reusable ones for future feature work:
- `slider-component` (global.js) — generic carousel/slider wrapper, used by multicolumn, featured-collection, etc. **Reuse this instead of building a new slider.**
- `modal-opener` / `modal-dialog` (global.js) — generic modal open/close pattern.
- `deferred-media` (global.js) — lazy-loads video/model embeds on interaction.
- `details-disclosure` / `details-modal` (details-disclosure.js / details-modal.js) — accordion/dropdown primitives built on native `<details>`.
- `variant-selects` (global.js) — product variant picker logic, fires `variant-change` pub/sub event.
- `quantity-input` (global.js) — quantity stepper used across cart/product forms.
- `cart-drawer` / `cart-drawer-items` (cart-drawer.js), `cart-items` (cart.js), `cart-notification` (cart-notification.js) — cart UI.
- `show-more` (show-more.js) — generic "show more" expand/collapse toggle.
- `share-button` related logic in `share.js`.

Full list (tag → file): `cart-notification` (cart-notification.js), `cart-drawer`/`cart-drawer-items` (cart-drawer.js), `cart-remove-button`/`cart-items` (cart.js), `details-disclosure`/`header-menu` (details-disclosure.js), `details-modal` (details-modal.js), `facet-filters-form`/`price-range`/`facet-remove` (facets.js), `quantity-input`/`menu-drawer`/`header-drawer`/`modal-dialog`/`bulk-modal`/`modal-opener`/`deferred-media`/`slider-component`/`slideshow-component`/`variant-selects`/`product-recommendations`/`account-icon`/`bulk-add` (global.js), `main-search` (main-search.js), media-gallery and localization-form custom elements, `pickup-availability` (pickup-availability.js), `password-modal` (password-modal.js), product-modal/product-model/price-per-item/product-form/product-info/quick-add(-bulk)/quantity-popover/quick-order-list/recipient-form/share (their respective files), `search-form` (search-form.js).

**Before building any new interactive feature, check this list first** — Dawn likely already has a component (modal, slider, disclosure) that should be reused or composed rather than rebuilt.

### Event Patterns

Centralized pub/sub via `pubsub.js` + `constants.js`. Event names live in the `PUB_SUB_EVENTS` object: `cartUpdate`, `quantityUpdate`, `optionValueSelectionChange`, `variantChange`, `cartError`. Components `subscribe(PUB_SUB_EVENTS.x, callback)` and `publish(PUB_SUB_EVENTS.x, data)`. New features that need to react to cart/variant state should subscribe to these existing events rather than polling or adding new global events.

### Third-Party Libraries

None. No CDN-hosted libraries (Flickity, Swiper, etc.) — confirmed via repo-wide search. The only external CDN reference is Shopify's own `model-viewer-ui` CSS (`https://cdn.shopify.com/shopifycloud/model-viewer-ui/assets/v1.0/model-viewer-ui.css`), used for 3D product model viewing — this is a first-party Shopify asset, not a third-party dependency. All sliders, modals, and interactive UI are custom vanilla-JS web components.

### Script Loading

Core scripts loaded in `layout/theme.liquid` `<head>` with `defer="defer"`: `constants.js`, `pubsub.js`, `global.js`, `details-disclosure.js`, `details-modal.js`, `search-form.js` (plus `animations.js` conditionally). Feature/section-specific scripts are loaded inline within the relevant section file (e.g. `product-form.js` loaded inside `featured-product.liquid`), also with `defer="defer"`, and only when the section's settings require them (conditional `{% if %}` blocks gate script tags). This "load only what's needed, where it's needed" pattern should be followed for new sections — load section-specific JS from within the section file, not globally.

## Liquid Conventions

### Section Wrapper Pattern

Outer wrapper carries the color scheme class and any full-bleed/isolation classes: `<div class="color-{{ section.settings.color_scheme }} gradient isolate">`. Inner content wrapper applies `.page-width` (or omits it for full-width sections) plus the `.section-{{ section.id }}-padding` class. Example (rich-text.liquid):
```liquid
<div class="isolate{% unless section.settings.full_width %} page-width{% endunless %}">
  <div class="rich-text content-container color-{{ section.settings.color_scheme }} gradient ... section-{{ section.id }}-padding">
```

### Section Padding Approach

Every content section follows this exact pattern (confirmed across rich-text, multicolumn, featured-collection, image-banner):
```liquid
{%- style -%}
  .section-{{ section.id }}-padding {
    padding-top: {{ section.settings.padding_top | times: 0.75 | round: 0 }}px;
    padding-bottom: {{ section.settings.padding_bottom | times: 0.75 | round: 0 }}px;
  }
  @media screen and (min-width: 750px) {
    .section-{{ section.id }}-padding {
      padding-top: {{ section.settings.padding_top }}px;
      padding-bottom: {{ section.settings.padding_bottom }}px;
    }
  }
{%- endstyle -%}
```
Note: Dawn uses a `0.75` multiplier for mobile (not the `0.5` shown in the CLAUDE.md generic example) — match this theme's actual multiplier, not the boilerplate example. Schema always pairs this with a `range` (0–100, step 4, unit px) for `padding_top` and `padding_bottom` under a `t:sections.all.padding.section_padding_heading` header.

### Standard Schema Settings

Nearly every content section includes, in this order: content settings → `color_scheme` (type `color_scheme`, default `scheme-1`) → layout/mobile settings → a final `header` block titled `t:sections.all.padding.section_padding_heading` → `padding_top` / `padding_bottom` ranges. Heading-size selects reuse the shared option set (`h2`, `h1`, `h0`, `hxl`, `hxxl`) keyed to `t:sections.all.heading_size.*`.

### Section Structure

1. Asset loading (CSS via `stylesheet_tag`, conditional JS via `<script defer>`) — always first line(s) of the file.
2. Conditional `{%- style -%}` blocks for section-specific dynamic CSS (aspect ratios, padding, overlay opacity).
3. `{%- liquid ... -%}` blocks for variable assignment/logic, kept above the markup.
4. Markup, wrapped per the Section Wrapper Pattern above.
5. `{% schema %}` JSON block at the end.

### Snippet Patterns

Snippets are heavily reused for repeated UI, e.g. `card-product.liquid` (rendered via `render 'card-product', card_product: product, ...` with named params), `icon-*.liquid` for inline SVGs (though most icons are now loaded via `| inline_asset_content` filter directly from `assets/icon-*.svg`, not snippet files), `meta-tags.liquid` for `<head>` SEO tags. Snippets are named with plain kebab-case, no prefix convention.

### Translation Approach

Consistently uses `t:` keys for every schema label/setting (`"label": "t:sections.image-banner.settings.image.label"`) and `| t` filter for all user-facing strings rendered in markup (`{{ 'general.slider.next_slide' | t }}`). No hardcoded text was found in any section reviewed — this is a strict, well-maintained convention. New sections must follow this same discipline.

### Block Patterns

Blocks are typed via `case block.type` in markup and declared in schema with matching `type`/`name`/`settings`. Common block types seen: `heading`, `text`, `caption`, `button`/`buttons`, `column` (multicolumn). Most content blocks carry a `limit` (e.g. `"limit": 1` for heading/text/buttons in image-banner, `"limit": 3` for repeatable heading/caption/text in rich-text). `{{ block.shopify_attributes }}` is always rendered on the wrapping element of each block for theme-editor support — never omit this.

## Schema Conventions

### Common Settings

`color_scheme` (almost universal), heading-size select, padding_top/padding_bottom range pair, and (for collection/product-grid sections) `columns_desktop` range + `columns_mobile` select + `swipe_on_mobile` checkbox driving a `slider-component`.

### Color Scheme Handling

Defined globally once in `settings_data.json` under `color_schemes`, surfaced per-section via a `color_scheme` schema setting (`type: "color_scheme"`), and applied via a `color-{{ section.settings.color_scheme }}` class on the section's outer wrapper. Sections needing a "has cards" warning (cards have their own color logic) add `"info": "t:sections.all.colors.has_cards_info"` to the setting.

### Padding / Spacing Approach

Range type, min 0, max 100, step 4, unit `px`. Defaults vary per section (e.g. 36px for multicolumn/featured-collection, 40/52px for rich-text) — there's no single universal default, each section's designer chose sensible defaults. New sections should pick defaults that look right at that section's typical content density, following this same range/step/unit convention.

### Preset Patterns

Every section has at least one preset under `"presets": [...]`, with a `name` (translated) and optionally default `blocks` (just `{"type": "..."}`, no settings overrides — defaults come from the block's own schema defaults). Per CLAUDE.md non-negotiables, always include an unconfigured-feeling preset so the section is usable from the customizer on any template.

## Visual Analysis

No reference images or design files exist yet in `.claude/context/reference/` — this section will be filled in once mockups/screenshots are added to that folder.

## Recommendations

- **This is unmodified stock Dawn 15.4.1.** Treat every file as a reference implementation of "how Dawn does X" — when building a new feature, the fastest path to consistency is usually to find the closest existing Dawn section/snippet/component and pattern-match it, rather than inventing new conventions.
- **Don't rebuild what Dawn already has.** Before writing a new web component, check the "Existing Components" list above — sliders, modals, disclosures, and deferred media are already solved problems in this theme.
- **Padding mobile multiplier is `0.75`, not `0.5`** — the example in the global CLAUDE.md system prompt uses `0.5` as a generic illustration; this theme's actual convention is `0.75`. Use `0.75` for new sections to stay visually consistent with existing ones.
- **No CSS utility-class system** (no Tailwind-style spacing/sizing utilities) — spacing is handled per-component via schema-driven range settings, not via global utility classes. Don't introduce a utility-class layer without discussing it first, since it would be a new pattern not native to this theme.
- **Translation discipline is strict and total** — every section reviewed had zero hardcoded text. Maintain this; add new keys to `locales/en.default.json` (and `en.default.schema.json` for schema labels) for any new feature.
- Since no features have been built yet, the next real customization work will set the tone for everything after it — worth being deliberate about file naming and schema settings philosophy (CMS-level controls, not pixel-level) from the very first feature.
