# Stonemaps — Site Map & Information Architecture

**Doc 2 of the IA/UXD documentation set · Phase 1**
Status: Draft v1 · 2026-07-02
Terms per [00-concept-glossary.md](00-concept-glossary.md); screen facts per [01-screen-inventory.md](01-screen-inventory.md). Backend-agnostic.

---

## 1. The two contexts

The app has exactly two top-level contexts, and one rule decides which you're in:

> **If the visitor has an active pairing (currently holds a stone) → Stone Context.
> Otherwise, if they have an account → Collection Context.
> Otherwise → the stone's Landing page (via QR/link) is the only way in.**

- **Stone Context** — the existing prototype: everything is scoped to *the stone you hold now*. Anonymous by default; an account is never required here.
- **Collection Context** (Phase 2, account layer) — for people *between or after* holdings: their stones, their archives, the afterlife of stones they've released.

This preserves the MVP promise: accounts add a second context; they don't gate the first.

### Device model (foundational assumption)

Each holder uses **their own phone**; the stone carries a QR that resolves to its Landing URL. Conversation privacy ("remains on your device and browser") is therefore natural — holder B's phone never contained holder A's conversation. "Reset" at gifting is a **stone-state event** (pairing ends, next holder starts fresh), not a device wipe.

> ⚠️ Open: how a **virtual stone** hands off (no QR to scan — likely a one-time handoff link), and how the system binds "current holder" to a device (recommendation: first device to open the stone URL after a gifting event claims the active pairing; subsequent scanners get a "this stone is with someone right now" bystander view — bystander view TBD, could simply be the Landing page without conversation access).

---

## 2. Site map

```
STONE CONTEXT (anonymous-first, per-stone)
│
├── 0. Landing  — "what is this?"                        [1-landing.html]
│      · stone identity, traits, field-test frame
│      · CTA: Talk to the stone
│      · quiet link: "Held a stone before?" → Sign in    [NEW · A8]
│
├── First-time gate (once per pairing)
│   ├── 1. Welcome — previous holder's gift              [2-welcome-image.html]
│   │      └── The Gifts (chain view)                    [MISSING · M1]
│   └── 2. PE Intro — disclosures, consent, T&C links    [3-planetary-emissary.html]
│          └── Terms / Fineprint / FAQ                   [MISSING · M3]
│
├── Tab: EMISSARY
│   └── 3. PE Conversation                               [4-pe-conversation.html]
│          ├── Rename / Edit emissary                    [MISSING · M4]
│          └── 4. PE Journal (modal)                     [6-pe-journal.html]
│
├── Tab: COMMONS
│   └── 5. The Commons (feed, Stone/Public toggle)       [5-the-commons.html]
│          ├── The Gifts (chain view)                    [MISSING · M1, same as above]
│          ├── Expanded card + one-time reply slot       [MISSING · M2]
│          └── 6. Contribute (modal)                     [5b-contribute.html]
│
├── Tab: JOURNAL  (module: My Journal — removable, F8)
│   └── 7. My Journal (list)                             [6c-my-journal.html]
│          └── 8. New Entry (compose + share scopes)     [6b-journal-entry.html]
│
├── Nav-left: GIFT
│   └── 9. Gift the Stone (modal)                        [7-gift-the-stone.html]
│          ├── 10. Keep Following — email capture        [NEW · A1]
│          ├── 11. Handoff confirmation / stone released [NEW · A2]
│          └── → stone resets for next holder
│
└── Nav-right: PRE-SALE (external, stonemaps.org)

COLLECTION CONTEXT (account layer, Phase 2)
│
├── 12. Sign in — email → magic link                     [NEW · A8]
├── 13. Check your email / link sent                     [NEW · A9]
├── 14. My Stones (collection home)                      [NEW · A4]
│      └── 15. Stone afterlife view (per past pairing)   [NEW · A5]
│             ├── Gifts chain since your time
│             ├── Stone-scoped Commons activity
│             ├── Provenance / where it's been
│             └── 16. My archive from that pairing       [NEW · A6]
│                    · my gift, my contributions,
│                    · my journal entries,
│                    · PE journal entries from my tenure
└── 17. Settings — notifications, email, data & privacy  [NEW · A7]
```

**Screen count:** 10 built · 4 missing from designed flows (M1–M4) · 8 new for account layer (A1–A9, where A1/A2 fold into the gift flow).

---

## 3. Navigation architecture

### Stone Context — unchanged

The 4-slot nav from the prototype stays exactly as built: `[Gift] [Emissary | Commons | Journal] [Pre-sale]`, with the greyed pre-conversation state. Account features deliberately get **no nav slot** in Stone Context — the claim offer lives inside the gift flow (its natural moment), keeping the holding experience account-free.

**Change from prototype (resolves F2):** Journal tab → **My Journal list (6c)**, with New Entry as its pill action. Compose-first entry (current: tab → 6b) buries a holder's existing entries behind a compose screen; list-first matches the PE Journal pattern and every journal convention. If the team wants compose-first deliberately, reverse this and record why.

### Collection Context — top bar, no bottom nav

Collection Context is shallow (list → detail → archive) and used occasionally, not habitually. It uses simple top-bar navigation (back arrows, settings icon on My Stones) rather than inventing a second tab system. If Collection Context grows later, revisit.

### Entry points into Collection Context

| From | Mechanism |
|---|---|
| Post-gift | Keep Following email capture → magic link → lands on My Stones with the just-released stone pinned |
| Notification email | Deep link → the relevant afterlife view (see Doc 6 for PWA deep-link constraints) |
| Landing page | Quiet "Held a stone before?" text link — must stay visually subordinate to "Talk to the stone" |
| PWA icon, no active pairing | My Stones (if signed in) or Landing of last-seen stone (if not) |

### Context switching

A signed-in user who currently holds a stone lives in Stone Context; their collection is reachable from **My Journal / settings surface only after MVP** — for the field test, keep the two contexts separate and simple: holding = stone context, released = collection context. Flag for post-field-test: persistent avatar/profile affordance.

---

## 4. Access & gating matrix

| Capability | Anonymous holder | Signed-in holder | Signed-in past holder | Bystander (scans held stone) |
|---|---|---|---|---|
| Landing page | ✓ | ✓ | ✓ | ✓ |
| Welcome / gate / PE Conversation | ✓ (current holder) | ✓ | — | — |
| PE Journal (this tenure) | ✓ | ✓ | via archive (their tenure only) | — |
| Commons: read Stone feed | ✓ | ✓ | ✓ (stones they've held) | — |
| Commons: read Public feed | ✓ | ✓ | ✓ | ⚠️ TBD |
| Commons: contribute | ✓ | ✓ | ⚠️ TBD — recommend read-only afterlife in MVP | — |
| Reply slot ("leave something in return") | ✓ | ✓ | ⚠️ TBD | — |
| My Journal | ✓ (device-local until claimed) | ✓ (synced) | via archive | — |
| Gift the Stone | ✓ | ✓ | — | — |
| Keep Following / claim | ✓ (the conversion moment) | n/a | n/a | — |
| Afterlife views, collection | — | ✓ | ✓ | — |
| Notifications | — | ✓ | ✓ | — |

The two ⚠️ TBDs (past-holder contribution rights, bystander Public feed) are product calls with real social consequences — recommend **read-only afterlife** for MVP: past holders watch, current holders speak. It keeps "one voice per stone at a time" and makes holding feel distinct from following.

---

## 5. URL / route structure (PWA)

Routes are the contract between IA and development; deep-linkability matters for magic links and notifications.

```
/                          → context resolver (active pairing? account? → redirect)
/s/:stoneId                → Landing (QR target — this URL is printed/bound to the stone)
/s/:stoneId/welcome        → first-time gate step 1
/s/:stoneId/intro          → first-time gate step 2
/s/:stoneId/emissary       → PE Conversation
/s/:stoneId/emissary/journal → PE Journal
/s/:stoneId/commons        → The Commons (?feed=stone|public)
/s/:stoneId/commons/new    → Contribute
/s/:stoneId/gifts          → The Gifts chain
/s/:stoneId/journal        → My Journal
/s/:stoneId/journal/new    → New Entry
/s/:stoneId/gift           → Gift the Stone flow
/auth                      → Sign in (email entry)
/auth/sent                 → check-your-email
/auth/verify?token=…       → magic-link target → session established
/me                        → My Stones
/me/pairings/:pairingId    → Stone afterlife view
/me/pairings/:pairingId/archive → My archive
/me/settings               → Settings & notifications
```

Notes:
- `:stoneId` in the printed QR is permanent for the stone's life — choose an opaque, unguessable id (stone URLs will be shared and photographed).
- Guard every `/s/:stoneId/*` route below Landing behind the current-holder check (§1 device model).
- `/auth/verify` must work when opened in a browser tab that is *not* the installed PWA — see Doc 6 before implementing.

---

## 6. IA decisions recorded (and their rationale)

| # | Decision | Rationale |
|---|---|---|
| D1 | Two contexts, one resolver rule | Keeps anonymous MVP intact; accounts additive, never gating |
| D2 | Claim moment = inside gift flow, pre-reset | Email must be captured before the pairing ends; it's also the emotional peak ("keep following it") |
| D3 | Journal tab → list-first (reverses prototype routing) | F2; convention + parity with PE Journal |
| D4 | No account affordance in Stone Context nav | Protects the artifact feel; conversion happens at gifting, per product direction |
| D5 | Collection Context = top-bar nav, no tabs | Shallow hierarchy; avoid second nav system |
| D6 | Past holders read-only in afterlife (MVP recommendation) | "One voice per stone at a time"; simplifies moderation and reply-slot semantics |
| D7 | Stone URL permanent + opaque | QR is physically printed; ids will leak into photos |

## 7. Open items feeding Doc 3+

- Virtual-stone handoff mechanics (no QR) — flow needed before dev.
- Bystander experience when scanning a held stone.
- Whether The Gifts chain is globally visible or holder/past-holder-only.
- My Journal module in/out (F8) — carried as removable; routes reserved either way.
