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
- [ ] Section can be added to a collection template from the theme customizer ("Modern Product Grid")
- [ ] Grid shows the products of the collection it's placed on (no collection picker needed)
- [ ] Desktop shows 4 columns
- [ ] Mobile shows 2 columns
- [ ] Even columns (2nd & 4th) are offset down ~half a card on desktop
- [ ] Each card shows the product's primary image and its title (title only — no price/year)
- [ ] Clicking anywhere on the card (or the title) navigates to the correct product page

### Card outline / notch
- [ ] Each card has the rounded outline with a notch cut from the lower-right corner
- [ ] The product title sits in/below the notch and never overlaps the outline
- [ ] Outline renders cleanly with no rough edges (Chrome, Safari, Firefox)

### Hover reveal
- [ ] Hovering a card reveals the secondary image with a downward elliptical sweep
- [ ] The transition feels premium (not too fast) and has the curved/elliptical leading edge
- [ ] Moving the mouse away reverses the sweep and restores the primary image
- [ ] Products with only ONE image do not reveal anything on hover (stay on primary, no glitch)

### Settings (customizer)
- [ ] Products-per-page select offers 4 / 8 / 12 and defaults to 8
- [ ] Changing products-per-page updates the grid and pagination correctly
- [ ] Accent color setting changes the focus-outline color live
- [ ] Optional heading / subheading appear when filled and are hidden when left empty
- [ ] Color scheme setting changes the section background/text per the chosen scheme
- [ ] Padding top/bottom sliders adjust spacing (with the 0.75 mobile multiplier)

### Pagination
- [ ] When products exceed the per-page count, numbered pagination appears below the grid
- [ ] Pagination links navigate between pages correctly
- [ ] The staggered/offset last row does not overlap or crowd the pagination

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
- [ ] Color contrast of title text against the background is sufficient

### Coexistence / no regressions
- [ ] Collections still using Dawn's default product grid look and behave exactly as before
- [ ] No console errors on the collection page
- [ ] No new global CSS/JS leaking into other sections (styles scoped to this section only)

## Previous Rounds

[Empty on first generation. After each QA round, AI moves the completed round here with results preserved.]
