[Do not write this manually. AI maintains this file automatically after every meaningful action.]

This is the project's persistent memory. AI reads it at the start of every session to restore context. It will contain:

- **Timestamped entries** — What was done and when
- **Decisions made** — What was decided and why, including alternatives that were considered
- **Architectural choices** — Technical decisions that affect the project long-term
- **Risks & open questions** — Things to watch out for in future development

## 2026-06-25 — Theme onboarding

Ran `/onboard-theme`. Confirmed this is **stock Shopify Dawn v15.4.1** — no custom code, no features built yet, no reference designs uploaded. Full analysis written to `OUTPUT-initial-theme-analysis.md`.

**Key takeaways for future work:**
- Section padding mobile multiplier is `0.75` (not the `0.5` used in the generic CLAUDE.md example) — match Dawn's actual convention.
- No CSS utility-class system exists; spacing is per-component via schema range settings.
- ~35 existing web components already cover sliders, modals, disclosures, deferred media — check before building new ones.
- Pub/sub event names are centralized in `assets/constants.js` (`PUB_SUB_EVENTS`).
- Translation discipline (`t:` keys) is total — zero hardcoded text found anywhere.

**Open question:** None yet — clean baseline. First feature will set precedent for naming/schema conventions going forward.

## 2026-06-26 — Feature scoped: modern-product-grid

Set up and scoped the first feature (branch `feat-modern-product-grid`). Standalone custom collection-grid section — **Approach A** chosen over extending Dawn's `card-product`/`main-collection-product-grid` (rejected to avoid ripple effects on shared snippets) and over a third-party app (no app does this design).

**Key decisions:** clean grid (no facets), auto-detect current collection (no picker), Dawn-native numbered pagination, product **title only** on cards (the mockup "2025" was competitor inspiration; client has no year), whole-card link (no add-to-cart), dedicated accent-color setting, no hover reveal for single-image products, column stagger kept on mobile. Settings built in from the start; **no custom JS anticipated** (offset/notch/elliptical reveal all pure CSS). Products-per-page 4/8/12, default 8.

**Risks to watch:** using this section on a collection page means losing Dawn's filter/sort sidebar (accepted trade-off); hover reveal depends on products having a 2nd image; elliptical reveal will lean on `clip-path`/`mask` — verify cross-browser during QA.

Full spec in `.claude/features/feature-modern-product-grid/feature.md`.

**Plan done (same day):** 8-step build plan + QA checklist written. Standalone section (`sections/section-modern-product-grid.liquid` + `assets/section-modern-product-grid.css`), **no JS** (hover swap is pure CSS via `:hover`/`:focus-within`). Reuses Dawn `paginate` + `pagination` snippet; deliberate deviation = CSS Grid (not Dawn's flex `.grid`) for the column stagger, scoped to the component. Coexists with Dawn's default grid (no base-file/shared-snippet edits); placement via customizer, dedicated-template decision deferred. Notch outline flagged as the finickiest CSS (mask approach, SVG-stroke fallback). Next: build.

**Built (same day):** Created `sections/section-modern-product-grid.liquid` + `assets/section-modern-product-grid.css`; added schema `t:` keys under `sections.modern-product-grid` in `locales/en.default.schema.json`. No JS. Decisions during build: `products_per_page` implemented as a **range** (min 4 / max 12 / step 4, default 8) instead of a select — yields 4/8/12 but returns a true integer for `{% paginate %}`. Notch = `radial-gradient` mask on the outline pseudo-element; hover reveal = animated `clip-path inset()` with rounded bottom for the elliptical edge (reduced-motion → cross-fade). Stagger = `translateY` on even columns (40% desktop / 32% mobile) + compensating grid bottom-padding. Whole-card click via stretched `::after` on the real title anchor. `accent_color` default `#caff00`. Empty/non-collection state reuses `sections.collection_template.empty`. JSON validated. **Open for QA Round 1** — notch geometry, reveal feel, offset amount, and `object-fit: cover` (may crop helmets) are the likely tuning points.

**QA Round 1 → fixes (same day):** The `radial-gradient` border-mask left a *gap* in the outline (looked broken, not a notch). Replaced it with a **continuous inline SVG `<path>` outline** (stroke `currentColor`, `vector-effect: non-scaling-stroke`) tracing a rounded rect + concave bottom-right notch (arc sweep-flag 1). Media now fills the full square card and is clipped to the *same* silhouette via an SVG `clipPath` (`clipPathUnits="objectBoundingBox"`, referenced through a per-section `--mpg-clip` var) — so the image fills inside the outline incl. the notch without bleeding. Reveal end-state → full `inset(0)` (was leaving an unfilled strip). Outline turns accent on **hover** too (was focus-only). Stagger switched from %-based to **fixed-length `--mpg-offset`** with exact bottom-padding compensation + pagination gap, fixing pagination/footer crowding at 12-per-page on desktop and mobile. **Risk:** SVG-clip notch geometry assumes a **square card** (aspect-ratio 1/1) — if the card ratio ever changes, the SVG path + clip + notch vars must be updated together. Now open for QA Round 2.

# ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# OUTPUT-project-log.md — v1.0

# AI Shopify Developer Bootcamp

# by Coding with Jan

# https://codingwithjan.com

# ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
