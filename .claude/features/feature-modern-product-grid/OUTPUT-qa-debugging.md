# QA: Modern Product Grid

Feature spec: `.claude/features/feature-modern-product-grid/feature.md`
Implementation plan: `.claude/features/feature-modern-product-grid/OUTPUT-implementation-plan.md`

## How to Use This File

1. Run through the checklist after implementation — check items that pass, add notes on items that fail
2. Tell AI to read this file — it will fix the issues and reset the checklist for another round
3. Repeat until everything passes
4. Previous rounds are kept as a log below the current round

## Current Round (Round 1)

Status: Testing

### Core behavior
- [y] Section can be added to a collection template from the theme customizer ("Modern Product Grid")
- [y] Grid shows the products of the collection it's placed on (no collection picker needed)
- [y] Desktop shows 4 columns
- [y] Mobile shows 2 columns
- [y] Even columns (2nd & 4th) are offset down ~half a card on desktop
- [y] Each card shows the product's primary image and its title (title only — no price/year)
- [y] Clicking anywhere on the card (or the title) navigates to the correct product page

### Card outline / notch
- [The card should have a continuous custom outline instead of a simple rounded rectangle. The outline must not stop, fade, or appear broken near the lower-right area. Create an intentional bottom-right notch in the container. The bottom border should continue smoothly, curve into a small rounded notch area, then curve back and continue into the right-side border. The notch should look like part of the card shape, not like a missing section of the border as found in Debugging round 1 - Product titles. Compare to the correct image Desktop 5 - Border Closeup. and provide a fix to the outline] Each card has the rounded outline with a notch cut from the lower-right corner
- [y] The product title sits in/below the notch and never overlaps the outline
- [y] Outline renders cleanly with no rough edges (Chrome, Safari, Firefox)

### Hover reveal
- [Yes, but I also want the border color to change to the focus accent color on hover also. Also, currently, the downward elliptical sweep stops too early and leaves the bottom portion of the card unfilled. This is incorrect. At the end of the hover animation, the secondary image should fully cover the card area inside the custom outline, including the lower curved/notched area.] Hovering a card reveals the secondary image with a downward elliptical sweep
- [y] The transition feels premium (not too fast) and has the curved/elliptical leading edge
- [y] Moving the mouse away reverses the sweep and restores the primary image
- [Needs to be tested later.] Products with only ONE image do not reveal anything on hover (stay on primary, no glitch)

### Settings (customizer)
- [y] Products-per-page select offers 4 / 8 / 12 and defaults to 8
- [Products-per-page setting must work correctly for 4 / 8 / 12 products and default to 8. The 4 and 8 options are currently acceptable, but the 12-products option needs correction: pagination must remain visible below the grid, and the final row of product cards must not be cut off or hidden by the footer at any desktop resolution. Add sufficient responsive bottom spacing/clearance after the staggered grid so the footer always starts below the complete product grid and pagination.] Changing products-per-page updates the grid and pagination correctly
- [y] Accent color setting changes the focus-outline color live
- [y] Optional heading / subheading appear when filled and are hidden when left empty
- [y] Color scheme setting changes the section background/text per the chosen scheme
- [Padding top/bottom sliders must correctly adjust section spacing on desktop and mobile, including the 0.75 mobile multiplier. Current issue: on mobile with 12 products selected, the bottom spacing is not sufficient. Pagination is not visible, and at some resolutions the footer begins before the product grid/pagination area has fully cleared. Add enough responsive bottom padding/clearance so the final product card, pagination, and footer never overlap or crowd each other.] Padding top/bottom sliders adjust spacing (with the 0.75 mobile multiplier)

### Pagination
- [When products exceed the selected products-per-page count, numbered pagination must appear clearly below the full product grid. Current issue: pagination is not positioned below the grid for 4, 8, or 12 products per page. It appears inside or too close to the product card area, and in some layouts it becomes difficult to see. Move the pagination outside the staggered grid flow and add sufficient vertical spacing so it always sits clearly underneath the last row of product cards.] When products exceed the per-page count, numbered pagination appears below the grid
- [y] Pagination links navigate between pages correctly
- [The staggered/offset last row currently crowds the pagination. Add more bottom spacing below the final row so the pagination sits clearly underneath the grid and does not feel squeezed between product cards.] The staggered/offset last row does not overlap or crowd the pagination

### Edge cases
- [ ] An empty collection (or the section on a non-collection template) shows a graceful message, not an error
- [ ] Very long product titles wrap/clamp within the notch without breaking the outline
- [ ] Odd numbers of products (partial last row) still render cleanly with the stagger

### Mobile / responsive
- [y] 2-column layout holds on mobile with the 2nd column offset
- [y] Tapping a card navigates to the product (no stuck hover state on touch)
- [y] Images are crisp and not distorted at mobile widths
- [y] Layout transitions cleanly across the 750px breakpoint

### Accessibility
- [y] Tabbing with the keyboard moves focus through the cards
- [y] A focused card shows the accent-colored outline (visible focus state)
- [ ] The product title is a real link (screen-reader announces it as a link to the product)
- [ ] With "reduce motion" enabled, the hover reveal is instant/cross-fade (no sweep)
- [y] Color contrast of title text against the background is sufficient

### Coexistence / no regressions
- [ ] Collections still using Dawn's default product grid look and behave exactly as before
- [y] No console errors on the collection page
- [y] No new global CSS/JS leaking into other sections (styles scoped to this section only)

## Previous Rounds

[Empty on first generation. After each QA round, AI moves the completed round here with results preserved.]
