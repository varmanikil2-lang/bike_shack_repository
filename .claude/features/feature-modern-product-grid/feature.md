# [Feature Name]

## Brief



Hey Claude, our client has a new collection of full-face mountain bike helmets. On a competitor website they saw a very nice product grid that they kind of want to recreate on their own website. This should be built as a section that can be included on collection templates.

Please also take a look at the reference folder because we've uploaded four different screenshots that show you how the grid looks on desktop, what happens in terms of the outline color if one of the elements gets into focus. Then also, if we hover over one of these elements there's a secondary lifestyle image that gets reviewed and the last screenshot shows the mobile layout so please make sure to study these carefully.

Now, in terms of the columns and grid layout, on desktop we want four columns and on mobile just two. You can also reference this from the screenshots. And also make sure that on desktop the second and fourth row have a small offset by about half a card. So it's not like all the cards are on the same height. They do have some offset and that makes the grid look really premium.

In terms of section settings, I think it would be nice if we could control how many products are visible per page. And if it's too many, we obviously want pagination. So as a meaningful step size, I think it would make sense if it was a multiple of four. So four products per page, eight products per page or 12 products. And I don't think we need a setting to select the specific collection because that can just be inferred by looking at the collection template or the collection we're currently viewing.

Now, if you take a close look at the screenshots, you can see that the outline for all our grid elements is not a perfect rectangle. It's also not a perfect square because it has a small notch in the lower right corner. And this is where the product title sits. And please make sure that the product title doesn't overlap the outline. There should be some space reserved for that.

In terms of colors, obviously we want to follow the current theme's conventions. So take a look at some of the settings, look at the colors that are currently configured or just use the theme's color schemes to make it consistent with how the store currently feels and looks.

If you hover over one of the grid elements, there is a swipe transition from the top. It's not too fast. It looks very premium. I would even say there's a small elliptical curve to it and that reveals a secondary image, which is pretty much a lifestyle image where the product is being used. We can just use the secondary product image for now because we've already uploaded these images. Once you hover away from the element, the product image is revealed again. So the transition kind of happens in reverse and the lifestyle image becomes hidden again.

---

## Scoping Questions

Generated: 2026-06-26
Chosen approach: Approach A — standalone custom section (`section-modern-product-grid.liquid`) added to the collection template, reading the current collection automatically. No edits to Dawn base files.

Answer by checking a box (`[x]`) and/or writing in the **Notes** line. Write `needs client clarification` if you can't decide yet.

### Q1: Filtering & sorting

The screenshots show a clean grid with no filter/sort UI. Dawn's collection page normally has a facet sidebar + sort dropdown (from `main-collection-product-grid`). Our custom section won't include those unless we deliberately build them in.

- [x] a) Clean grid, no filtering/sorting (Recommended — matches the design, simplest, fewest moving parts)
- [ ] b) Include a sort-by dropdown only
- [ ] c) Include full filtering + sorting (significantly more work — rebuilds Dawn's facets)

**Notes:**

### Q2: Where it's used / collection source

Brief says the collection is inferred from the collection template. That works on collection pages only. We can optionally add a collection picker so the same section could be reused on the homepage or other pages.

- [x] a) Collection pages only — auto-detect the current collection (Recommended — matches brief, keeps it simple)
- [ ] b) Auto-detect on collection pages, but also add an optional collection picker so it can be reused on home/other pages

**Notes:**

### Q3: Pagination style

Brief says we need pagination when there are too many products.

- [x] a) Numbered pagination (Recommended — Dawn-native `{% paginate %}`, no JS, accessible, matches theme)
- [ ] b) "Load more" button (needs JS, keeps users on one page)
- [ ] c) Infinite scroll (needs JS, can hurt SEO/footer access)

**Notes:**

### Q4: What text sits in the notch (IMPORTANT)

In the screenshots each card shows a name + a value in the accent color (e.g. "Season **2025**"). I need to know what those two pieces of text map to on real products, since "2025" isn't standard product data.

- [ ] a) Product title + price (price shown in accent color) — most conventional for a shoppable grid
- [ ] b) Product title + a custom metafield (e.g. a "year"/"edition" field you'd fill per product) — matches the mockup literally, needs a metafield set up
- [x] c) Product title only (no second value)

**Notes:**  _(If b: tell me the metafield namespace.key, or ask me to create one.)_ The screenshots I shared is just the example from the competitor website or from the inspiration website. In our case, we won't even have a year so product title is enough for us.

### Q5: Card interaction

The mockups show no "add to cart" button on the cards.

- [x] a) Whole card links to the product page, no add-to-cart (Recommended — matches design)
- [ ] b) Add a quick-add / add-to-cart button on each card

**Notes:**

### Q6: Accent color source

The neon/lime accent (focus outline + the accent text) is specific and may not exist in the current Dawn color scheme.

- [x] a) Dedicated "Accent color" setting on the section (Recommended — gives the client direct control of that exact green)
- [ ] b) Pull it from the selected color scheme's existing variables (no new control, but less precise)

**Notes:**

### Q7: Secondary image fallback

The hover reveal uses the product's 2nd image. Some products may only have one image.

- [x] a) No reveal — card just stays on the primary image (Recommended — clean, no awkward duplicate)
- [ ] b) Reveal a duplicate of the primary image (hover still "does something")

**Notes:**

### Q8: Mobile column offset

Desktop staggers the even columns down ~half a card. The mobile screenshot appears to keep the 2nd column offset too.

- [x] a) Keep the offset on mobile (2nd column lower) — matches the mobile screenshot
- [ ] b) Flat on mobile (no offset) — both columns aligned

**Notes:**

---

### Recommendations

As your technical lead, a few proactive calls and flags:

- **Settings from the start (not hardcoded).** Since this is a reusable, CMS-driven section, I'd build the schema settings in from day one rather than prototyping with hardcoded values. The only non-product text is the optional heading and the accent color — everything else is product data.
- **Optional section heading.** I'd add optional heading + subheading settings (left empty by default) above the grid so the client *can* add intro copy, but the section looks clean with them blank. The "memorable helmets." text in the mobile shot looks like the site header, so the grid itself stays heading-light. Shout if you'd rather have no heading settings at all.
- **Products-per-page default.** Options 4 / 8 / 12 as you specified. I'd default to **8** (two full desktop rows). Tell me if you'd prefer 12.
- **The column offset + notch are CSS-only** — no JS needed for those. The hover reveal is also pure CSS (transition + clip-path/mask for the elliptical curve). I only foresee needing a tiny web component if we add "Load more" (Q3b) — otherwise this section may need **no custom JS at all**, which is the safest outcome.
- **Notch + title spacing.** Per your brief, I'll reserve dedicated space below the outline's notch so the title never overlaps the border. The notch is a CSS mask on the card; the title sits in the cleared corner.
- **Accessibility.** Whole-card links will be built so the product title is the real `<a>` (good for screen readers / SEO), with the card surface made clickable around it — not a nest of divs with a click handler. Focus styles drive the accent outline (Q6), which is also a keyboard-accessibility win.
- **Risk flag — `paginate` + theme editor.** Pagination via `{% paginate %}` works on collection pages. If you later choose Q2b (collection picker for the homepage), pagination there behaves differently (uses `?page=` on that URL) — usually fine, just noting it.

## Extended Brief

Generated: 2026-06-26

### Chosen Approach

Approach A — a standalone custom section (`section-modern-product-grid.liquid` + `section-modern-product-grid.css`, no custom JS anticipated) added to the collection template. It auto-reads the current collection. Chosen because the design is too bespoke to reuse Dawn's `card-product`, and a self-contained section keeps the styling fully isolated with zero edits to Dawn base files.

### Requirements

- Render the current collection's products as a premium grid: **4 columns on desktop, 2 columns on mobile**.
- **Even columns (2nd & 4th) offset downward by ~half a card** — staggered look on both desktop and mobile (2nd column lower on mobile).
- Each card has a **notched, rounded outline** — a notch cut out of the lower-right corner. The **product title** sits in/below that notch, with reserved space so it never overlaps the border.
- **Hover:** reveal the product's secondary image via a downward swipe with an elliptical/curved leading edge; not too fast (premium feel); reverses on mouse-out.
- **Focus state:** card outline switches to the accent color (keyboard accessible).
- **Whole card links to the product page**; the product title is the real anchor. No add-to-cart.
- **Numbered pagination** (Dawn-native `{% paginate %}`) when products exceed the per-page setting.

### Where It Lives

- Collection template(s) only, added via the theme customizer. Reads the ambient `collection` object — no collection picker.
- Section appears in the customizer for any template (unconfigured preset included per project rules), but is designed/intended for collection pages.

### Data Sources

- **Products:** the current collection (`collection.products`), paginated.
- **Per card:** product title, featured (primary) image, and the second media image for the hover reveal. No price, no year/edition value.

### User Interaction

- **Default:** primary image shown, notched outline, title in the notch.
- **Hover (pointer):** secondary image swipes down over the primary with an elliptical edge; reverses on mouse-out.
- **Focus (keyboard):** outline turns the accent color.
- **Click/activate:** navigates to the product page.

### Customizer Settings

- `products_per_page` — select: **4 / 8 / 12** (default **8**).
- `accent_color` — dedicated color setting (the neon/lime) for focus outline + any accent usage.
- `heading` + `subheading` — optional, empty by default.
- `color_scheme` — Dawn standard.
- `padding_top` / `padding_bottom` — Dawn standard range (0–100, step 4, px), with the `0.75` mobile multiplier.
- **Not configurable:** column counts, offset amount, notch geometry, hover-animation timing/curve (these are design constants, kept out of the customizer per the CMS-not-Webflow philosophy).

### Decisions Made

- **Q1 — Clean grid, no filtering/sorting.** Matches the design; avoids rebuilding Dawn's facets. (Trade-off: collection page loses Dawn's filter/sort sidebar where this section is used.)
- **Q2 — Collection pages only**, auto-detect current collection. No picker.
- **Q3 — Numbered pagination** via `{% paginate %}`. No JS, accessible, SEO-friendly.
- **Q4 — Product title only.** The "2025" in the mockup was competitor inspiration; the client has no year value, so no second text element and no metafield.
- **Q5 — Whole card links to PDP**, no add-to-cart.
- **Q6 — Dedicated accent color setting** for precise control of the neon green.
- **Q7 — Single-image products: no hover reveal** (stays on primary image).
- **Q8 — Keep the column offset on mobile** (2nd column lower), matching the mobile screenshot.
- **Settings built in from the start** (not hardcoded), since it's a reusable CMS-driven section.
- **No custom JS anticipated** — offset, notch, and elliptical reveal are all pure CSS (transition + clip-path/mask).

### Edge Cases to Handle

- **Empty collection / used on a non-collection template** → graceful empty or onboarding state, no errors.
- **Single-image products** → no hover reveal.
- **Odd product counts** → staggered offset still renders cleanly; no broken trailing row.
- **Long product titles** → clamp/wrap within the notch area without overlapping the outline.
- **Mobile** → 2-column stagger holds; tap navigates to PDP (no hover state on touch).
- **Accessibility** → real anchor on title, visible accent focus outline, sufficient contrast.

### Out of Scope

- Filtering and sorting (facets).
- Add-to-cart / quick-add.
- Collection picker / use on non-collection pages as a primary use case.
- Year/edition metafield or any second text value on cards.
- Load-more / infinite scroll.
- Configurable column counts or animation tuning in the customizer.

### Dependencies

- Dawn color schemes + global tokens (color scheme class, page-width, padding pattern).
- `{% paginate %}` (collection-page context).
- Products must have a **second image** for the hover reveal to show (otherwise falls back per Q7).

### Notes

- Optional heading/subheading kept empty by default; the grid is heading-light since the site header carries page context.
- Default products-per-page: 8 (two full desktop rows).

### Needs Client Clarification

None — all scoping questions resolved.

# ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# feature.md — v1.0

# AI Shopify Developer Bootcamp

# by Coding with Jan

# https://codingwithjan.com

# ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
