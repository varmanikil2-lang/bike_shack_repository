# QA: Modern Product Grid

Feature spec: `.claude/features/feature-modern-product-grid/feature.md`
Implementation plan: `.claude/features/feature-modern-product-grid/OUTPUT-implementation-plan.md`

## How to Use This File

1. Run through the checklist after implementation — check items that pass, add notes on items that fail
2. Tell AI to read this file — it will fix the issues and reset the checklist for another round
3. Repeat until everything passes
4. Previous rounds are kept as a log below the current round

## Current Round (Round 2)

Status: Testing

We've provided the most important feedback for the next round and everything that's not answered here, we've kind of tested in the first run already. We will test this in the end again just to make sure everything is working, but let's do the next round of revisions.

Mark each item: `[y]` = pass, leave `[ ]` = fail (add a note in brackets). All items are reset so fixes from Round 1 can be re-verified alongside everything else.

### Core behavior
- [ ] Section can be added to a collection template from the theme customizer ("Modern Product Grid")
- [ ] Grid shows the products of the collection it's placed on (no collection picker needed)
- [ ] Desktop shows 4 columns
- [ ] Mobile shows 2 columns
- [ ] Even columns (2nd & 4th) are offset down ~half a card on desktop
- [ ] Each card shows the product's primary image and its title (title only — no price/year)
- [ ] Clicking anywhere on the card (or the title) navigates to the correct product page

### Card outline / notch  ← FIXED THIS ROUND
- [Okay, I feel I need to describe this differently because I don't want this diagonal at all. Please take a look back at our reference images, specifically Desktop 2 Outline Accent, because that makes it very visible. And then take a look at the outline. You can see that the bottom border is not cut off or anything. It's more like there's a step in the middle of that. So we have a straight line, then there's a step, and then a straight line again to the bottom right corner. So this is more what I mean. Please take a look and refine this. The product title must not overlap the outline.] The outline is one continuous line — it runs along the bottom, curves smoothly into a rounded notch at the lower-right, then curves back up into the right edge (no gap or broken section). Compare to "Desktop 5 - Border Closeup"
- [Refer to previous point] The notch reads as an intentional part of the card shape, not a missing piece of border
- [You made a mistake here] The product title sits within the notch area and does not cross/overlap the outline
- [ ] Outline renders cleanly with no rough edges (Chrome, Safari, Firefox)

### Hover reveal  ← FIXED THIS ROUND
- [y] Hovering a card changes the outline color to the accent color (same as the focus color)
- [y] The secondary image sweeps down with the elliptical leading edge
- [ ] At the end of the sweep, the secondary image FULLY covers the card interior, including the lower notched area (no unfilled strip at the bottom)
- [y] The transition feels premium (not too fast)
- [y] Moving the mouse away reverses the sweep and restores the primary image
- [ ] Products with only ONE image do not reveal anything on hover (stay on primary, no glitch)

### Settings (customizer)
- [ ] Products-per-page offers 4 / 8 / 12 and defaults to 8
- [Pagination is broken now. Second page displays no products and no pagination for some reason. Please fix this so that pagination functionality is maintained.] At 12 products on desktop: pagination stays visible and the last row is never cut off / hidden behind the footer at any desktop width  ← FIXED THIS ROUND
- [ ] Accent color setting changes the outline color (focus AND hover) live
- [ ] Optional heading / subheading appear when filled and are hidden when left empty
- [ ] Color scheme setting changes the section background/text per the chosen scheme
- [ ] Padding top/bottom sliders adjust spacing (with the 0.75 mobile multiplier)

### Pagination + spacing  ← FIXED THIS ROUND
- [ ] When products exceed the per-page count, numbered pagination appears clearly BELOW the full grid (not inside/among the cards)
- [ ] There is clear vertical space between the last (staggered) row and the pagination — it does not feel squeezed
- [ ] On mobile at 12 products: pagination is visible and the footer starts below the grid + pagination (no overlap/crowding)
- [ ] Pagination links navigate between pages correctly

### Edge cases
- [ ] An empty collection (or the section on a non-collection template) shows a graceful message, not an error
- [ ] Very long product titles wrap/clamp within the notch without breaking the outline
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
