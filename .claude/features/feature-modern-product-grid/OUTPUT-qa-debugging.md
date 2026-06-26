# QA: Modern Product Grid

Feature spec: `.claude/features/feature-modern-product-grid/feature.md`
Implementation plan: `.claude/features/feature-modern-product-grid/OUTPUT-implementation-plan.md`

## How to Use This File

1. Run through the checklist after implementation — check items that pass, add notes on items that fail
2. Tell AI to read this file — it will fix the issues and reset the checklist for another round
3. Repeat until everything passes
4. Previous rounds are kept as a log below the current round

## Current Round (Round 3)

Status: Testing

Mark each item: `[y]` = pass, leave `[ ]` = fail (add a note in brackets). Items not re-tested can be left blank — the full re-test happens at the end.

### Card outline / notch  ← REWORKED THIS ROUND (step shape, not a diagonal)
- [The current card shape is still not correct. Please refine only the container outline and text placement so it is closer to the reference file Desktop 5 - Border Closeup.jpg. Focus on the shape of the border, the lower-right notch, and where the title sits. The outline should be a smooth, continuous custom border with more generous rounded outer corners. The bottom edge should not look like a detached or centered cutout; it should stay mostly continuous and then transition into an integrated lower-right notch area. The notch should feel built into the border shape, with a straight lower edge, a softly rounded step/notch section, and a clean continuation toward the right side. The inner notch corner should not be a sharp 90-degree angle; it needs a small rounded curve so the whole shape feels smooth and organic rather than mechanical. The product title should be positioned in the lower-right label area, aligned with the notch, not too low, not pushed into the corner, and not separated from the outline structure. Overall, the container should read as a premium custom card with a continuous rounded outline, an integrated lower-right notch/step, and the title placed neatly within that lower-right notch area.] The bottom outline runs straight, then **steps down** with a short rounded riser into a lower-right shelf, then continues straight to the bottom-right corner — one continuous line, no diagonal. Compare to "Desktop 2 - Outline Accent Color"
- [ ] The step/shelf reads as an intentional part of the card shape
- [ ] The product title sits inside the shelf and does NOT cross/overlap the outline stroke
- [ ] Outline renders cleanly with no rough edges (Chrome, Safari, Firefox)

### Hover reveal
- [This is looking good.] Hovering a card changes the outline color to the accent color
- [ ] The secondary image sweeps down with the elliptical leading edge
- [ ] At the end of the sweep, the secondary image fully covers the card interior down to the stepped bottom (no unfilled strip)
- [ ] The transition feels premium (not too fast)
- [ ] Moving the mouse away reverses the sweep and restores the primary image
- [ ] Products with only ONE image do not reveal anything on hover (stay on primary, no glitch)

### Pagination  ← BUG FIX THIS ROUND
- [ ] With more products than the per-page count, page 1 shows products + pagination
- [ ] Clicking to page 2 shows the next products AND keeps the pagination visible (the page-2-empty bug is fixed)
- [ ] Pagination links navigate correctly across all pages (4 / 8 / 12 per page)
- [ ] Pagination sits clearly below the staggered last row with breathing room
- [ ] At 12 per page (desktop and mobile): last row + pagination clear the footer, no overlap

### Settings (customizer)
- [ ] Products-per-page offers 4 / 8 / 12 and defaults to 8
- [ ] Accent color setting changes the outline color (focus AND hover) live
- [ ] Optional heading / subheading appear when filled and are hidden when left empty
- [ ] Color scheme setting changes the section background/text per the chosen scheme
- [ ] Padding top/bottom sliders adjust spacing (with the 0.75 mobile multiplier)

### Core behavior
- [ ] Section can be added to a collection template from the customizer ("Modern Product Grid")
- [ ] Grid shows the products of the collection it's placed on
- [ ] Desktop 4 columns / mobile 2 columns
- [ ] Even columns (2nd & 4th) offset down ~half a card
- [ ] Clicking anywhere on the card navigates to the correct product page

### Edge cases
- [ ] An empty collection (or the section on a non-collection template) shows a graceful message, not an error
- [ ] Very long product titles clamp within the shelf without crossing the outline
- [ ] Odd numbers of products (partial last row) still render cleanly with the stagger

### Mobile / responsive
- [ ] 2-column layout holds on mobile with the 2nd column offset
- [ ] Tapping a card navigates to the product (no stuck hover state on touch)
- [ ] Images are crisp and not distorted at mobile widths
- [ ] Layout transitions cleanly across the 750px breakpoint

### Accessibility
- [ ] Tabbing with the keyboard moves focus through the cards
- [ ] A focused card shows the accent-colored outline (visible focus state)
- [ ] The product title is a real link (screen-reader announces it as a link to the product)
- [ ] With "reduce motion" enabled, the hover reveal is instant/cross-fade (no sweep)
- [ ] Title text stays legible over the secondary (lifestyle) image

### Coexistence / no regressions
- [ ] Collections still using Dawn's default product grid look and behave exactly as before
- [ ] No console errors on the collection page
- [ ] No new global CSS/JS leaking into other sections (styles scoped to this section only)

## Previous Rounds

### Round 2 — Status: Failed (fixes applied, see Round 3)

Note: user actively tested the outline/notch, hover reveal, and pagination; other items were left blank and deferred to a final pass.

#### Card outline / notch
#### [FAIL] The outline is one continuous line with a rounded notch at the lower-right
**User feedback:** The concave/diagonal notch was wrong. Wanted (per "Desktop 2 - Outline Accent Color") the bottom border to run straight, then a **step** (short rounded riser down) into a lower-right shelf, then straight again to the bottom-right corner — no diagonal.
**Fix:** Replaced the concave-arc path with a **stepped silhouette**: straight bottom edge → convex down-corner → short riser → concave inner corner → lower-right shelf → bottom-right corner. Updated both the inline outline SVG (viewBox now 200×220) and the matching objectBoundingBox clip-path. Card aspect ratio changed to 200/220 to make room for the shelf.
#### [FAIL] The notch reads as an intentional part of the card shape
**User feedback:** Same as above — looked like a diagonal cut, not an intentional step.
**Fix:** Same stepped-shape rework.
#### [FAIL] The product title sits within the notch area and does not cross/overlap the outline
**User feedback:** "You made a mistake here" — the title overlapped the outline.
**Fix:** Title repositioned into the shelf with padding (right 5%, bottom 3.5%, max-width 38%) so it stays clear of the stroke.
#### [SKIPPED] Outline renders cleanly with no rough edges — not re-tested this round

#### Hover reveal
#### [PASS] Hovering a card changes the outline color to the accent color
#### [PASS] The secondary image sweeps down with the elliptical leading edge
#### [FAIL] At the end of the sweep, the secondary image fully covers the card interior including the lower notched area
**User feedback:** Still left the lower area unfilled.
**Fix:** Media now fills the full card silhouette (clipped to the stepped shape) and the reveal ends at `inset(0 0 0 0)`, so the secondary image fills the entire interior down to the stepped bottom.
#### [PASS] The transition feels premium (not too fast)
#### [PASS] Moving the mouse away reverses the sweep and restores the primary image
#### [SKIPPED] Products with only ONE image do not reveal — deferred again

#### Settings / Pagination
#### [SKIPPED] Products-per-page offers 4 / 8 / 12 and defaults to 8 — not re-tested
#### [FAIL] At 12 products: pagination stays visible / page navigation works
**User feedback:** "Pagination is broken now. Second page displays no products and no pagination."
**Fix:** Root cause was the empty-state guard testing `collection.products.size` *outside* the `{% paginate %}` tag — on `?page=2` that returns an empty set, so the section fell into the "empty" branch (no products, no pagination). Changed the guard to `collection.products_count` (the stable total), which is correct on every page.
#### [SKIPPED] Accent color / heading / color scheme / padding settings — not re-tested
#### [SKIPPED] Pagination position/spacing items — blocked by the pagination bug, re-test in Round 3

#### Other sections (core, edge cases, mobile, accessibility, coexistence)
#### [SKIPPED] Deferred by user — verified in Round 1, to be re-tested at the final pass

### Round 1 — Status: Failed (fixes applied, see Round 2)

#### Core behavior
#### [PASS] Section can be added to a collection template from the theme customizer
#### [PASS] Grid shows the products of the collection it's placed on
#### [PASS] Desktop shows 4 columns
#### [PASS] Mobile shows 2 columns
#### [PASS] Even columns offset down ~half a card on desktop
#### [PASS] Each card shows the product's primary image and its title (title only)
#### [PASS] Clicking anywhere on the card navigates to the correct product page

#### Card outline / notch
#### [FAIL] Each card has the rounded outline with a notch cut from the lower-right corner
**User feedback:** Outline looked broken/missing near the lower-right — like a gap in the border rather than an intentional notch. Wanted a continuous outline that runs along the bottom, curves smoothly into a small rounded notch, then curves back into the right-side border (compare to "Desktop 5 - Border Closeup").
**Fix:** Replaced the radial-gradient border-mask (which deleted the border in that corner, leaving a gap) with a continuous **inline SVG `<path>` outline** stroked with `currentColor` and `vector-effect: non-scaling-stroke`. The path traces the rounded rectangle and a concave bottom-right notch as one unbroken line. The image is now clipped to the exact same silhouette via an SVG `clipPath` (objectBoundingBox), so it fills up to the outline including the notch.
#### [PASS] The product title sits in/below the notch and never overlaps the outline
#### [PASS] Outline renders cleanly with no rough edges

#### Hover reveal
#### [FAIL] Hovering a card reveals the secondary image with a downward elliptical sweep
**User feedback:** (1) Wanted the outline to also turn the accent color on hover (not just on focus). (2) The sweep stopped too early and left the bottom portion of the card unfilled — the secondary image should fully cover the card interior including the lower notched area.
**Fix:** (1) Added `:hover` to the accent-outline rule so the outline turns accent on hover and focus. (2) Changed the reveal end-state from a rounded `clip-path` to a full `inset(0 0 0 0)` so the secondary image fully covers the card; combined with clipping the media to the notched silhouette, it now fills the entire interior including the notch.
#### [PASS] The transition feels premium (not too fast) and has the curved/elliptical leading edge
#### [PASS] Moving the mouse away reverses the sweep and restores the primary image
#### [SKIPPED] Products with only ONE image do not reveal anything on hover
**Note:** User deferred testing this to a later round.

#### Settings (customizer)
#### [PASS] Products-per-page offers 4 / 8 / 12 and defaults to 8
#### [FAIL] Changing products-per-page updates the grid and pagination correctly
**User feedback:** 4 and 8 acceptable. At 12 products the last row was cut off / hidden by the footer and pagination wasn't reliably visible at some desktop widths. Needs responsive bottom clearance so the footer always starts below the full grid + pagination.
**Fix:** Switched the column stagger from a percentage `translateY` to a fixed-length offset (`--mpg-offset`), then added matching bottom padding (`offset + 1rem`) on the grid plus a `margin-top` gap before Dawn's pagination. Because the offset is now a known length, the compensation is exact at any product count.
#### [PASS] Accent color setting changes the focus-outline color live
#### [PASS] Optional heading / subheading appear when filled and are hidden when left empty
#### [PASS] Color scheme setting changes the section background/text per the chosen scheme
#### [FAIL] Padding top/bottom sliders adjust spacing (with the 0.75 mobile multiplier)
**User feedback:** On mobile with 12 products the bottom spacing was insufficient — pagination not visible and the footer began before the grid/pagination cleared.
**Fix:** Same spacing fix as above; the offset-compensating bottom padding and pagination gap are defined per-breakpoint so mobile (2-col) also clears correctly.

#### Pagination
#### [FAIL] When products exceed the per-page count, numbered pagination appears below the grid
**User feedback:** Pagination appeared inside/too close to the card area for 4, 8, and 12 per page, and was hard to see in some layouts.
**Fix:** The staggered even columns (transformed) were overlapping into the pagination zone. Added offset-matched bottom padding on the grid and a margin gap before the pagination so it always sits clearly below the last row.
#### [PASS] Pagination links navigate between pages correctly
#### [FAIL] The staggered/offset last row does not overlap or crowd the pagination
**User feedback:** The offset last row crowded the pagination.
**Fix:** Same spacing fix — bottom padding equal to the stagger offset plus a clear gap before pagination.

#### Edge cases
#### [SKIPPED] Empty collection / non-collection template graceful message — not tested this round
#### [SKIPPED] Long product titles wrap/clamp without breaking the outline — not tested this round
#### [SKIPPED] Odd numbers of products render cleanly with the stagger — not tested this round

#### Mobile / responsive
#### [PASS] 2-column layout holds on mobile with the 2nd column offset
#### [PASS] Tapping a card navigates to the product (no stuck hover state on touch)
#### [PASS] Images are crisp and not distorted at mobile widths
#### [PASS] Layout transitions cleanly across the 750px breakpoint

#### Accessibility
#### [PASS] Tabbing with the keyboard moves focus through the cards
#### [PASS] A focused card shows the accent-colored outline
#### [SKIPPED] The product title is a real link (screen-reader) — not tested this round
#### [SKIPPED] Reduce-motion hover reveal is instant/cross-fade — not tested this round
#### [PASS] Color contrast of title text against the background is sufficient

#### Coexistence / no regressions
#### [SKIPPED] Collections still using Dawn's default grid unaffected — not tested this round
#### [PASS] No console errors on the collection page
#### [PASS] No new global CSS/JS leaking into other sections
