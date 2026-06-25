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

# ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# OUTPUT-project-log.md — v1.0

# AI Shopify Developer Bootcamp

# by Coding with Jan

# https://codingwithjan.com

# ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
