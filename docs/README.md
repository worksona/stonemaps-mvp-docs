# Stonemaps — IA/UXD Documentation Set

Field-test MVP documentation, grounded in the 10 HTML prototype screens and PE-FT-workflow-V5.jpg in the parent folder. Drafted 2026-07-02.

**Purpose:** enable UI design and development of the field-test MVP, plus the account layer (create accounts so holders can return after gifting a stone — offered post-gift, never required to start).

## The set

| Doc | What it is | Primary audience |
|---|---|---|
| [00 · Concept Glossary](00-concept-glossary.md) | Canonical vocabulary — Stone, Emissary, Holder, Pairing, Gift, Claim, the privacy rungs. Change terms here first. | everyone |
| [01 · Screen & Component Inventory](01-screen-inventory.md) | What the prototype actually contains: screens, nav states, components, both design-token sets, flagged inconsistencies (F1–F8). | UI design, dev |
| [02 · Site Map & IA](02-site-map-ia.md) | Two contexts (Stone / Collection), full site map incl. missing (M1–M4) and new account screens (A1–A9), gating matrix, URL routes, IA decisions D1–D7. | UI design, dev |
| [03 · Core User Flows](03-core-user-flows.md) | Flows A–G: first touch, return, commons, gift+claim, magic-link verify, afterlife, notifications. Build-order suggestion. | UI design, dev |
| [04 · Data & State Requirements](04-data-state-requirements.md) | The privacy ladder (rungs 1–5), entity model around Pairing, client-side state, state machines, what claiming transfers, retention. | dev, product |
| [05 · Notifications & Re-engagement](05-notifications-reengagement.md) | Trigger matrix T1–T6, anti-triggers, email-first channel strategy, opt-in surface, success metrics. | dev, product |
| [06 · PWA Constraints](06-pwa-constraints.md) | Platform realities: magic-link×PWA session split, iOS storage eviction, offline scope, QR binding, media capture. Decisions P1–P7. | dev |
| [07 · Backend Brief](07-backend-brief.md) | Requirements checklist any backend must meet; build-vs-reuse evaluation of the existing stone-maps backend (currently an option, not a given). | dev, product |
| [08 · Holder Journey Map](08-holder-journey-map.html) | Visual six-stage journey diagram (Encounter → Threshold → Converse → Participate → Gift → Follow) with doing/thinking/touchpoints, emotion curve, and the Stone-Context → Claim → Collection-Context band. | everyone |

## How to use it

- **Designing a screen?** 01 (components/tokens) + 02 (where it sits) + 03 (the flow it serves).
- **Building a feature?** 03 (behavior) + 04 (data rules) + 06 (platform constraints).
- **Backend decision?** 07, scored against 04.
- Cross-references use stable ids: F# (inventory flags), M# (missing screens), A# (account screens), D# (IA decisions), T# (triggers), P# (platform decisions), R# (privacy rules).

## Open product calls (blocking items marked ●)

| Id | Question | Blocks |
|---|---|---|
| ● F8 | My Journal in MVP or eval-only? | final flows, claim-offer copy, backend scope |
| ● 07 | Backend: extend existing vs fresh build (pending schema inspection — DB was down) | all dev sequencing |
| F1 | Stone name vs Emissary name (Koozhankal/Pierrot); holder-renameable? | conversation header, landing |
| F3 | Two design languages: formalize or unify token sets | UI kit |
| D6 | Past holders read-only in afterlife (recommended) — confirm | afterlife screens |
| — | Virtual-stone handoff mechanics (no QR) | flow D variant |
| — | Bystander view when scanning a held stone | small screen + gating |
| — | Rung 4–5 deletion policy (takedown-request default recommended) | fineprint (M3) |

## Phase status

- **Phase 0** (glossary, inventory) — ✅ drafted
- **Phase 1** (IA, flows) — ✅ drafted
- **Phase 2–3** (data/state, notifications, PWA, backend brief) — ✅ drafted
- **Next:** resolve ● items → wireframe the 12 missing/new screens (M1–M4, A1–A9) → UI kit from unified tokens → backend decision → build per the slice order in Doc 3.
