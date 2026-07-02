# Stonemaps — Screen & Component Inventory

**Doc 1 of the IA/UXD documentation set · Phase 0**
Status: Draft v1 · 2026-07-02
Source: the 10 HTML prototype screens + assets/ in this folder, cross-checked against PE-FT-workflow-V5.jpg. Terms per [00-concept-glossary.md](00-concept-glossary.md).

Purpose: a complete, factual record of what the prototype actually contains — screens, navigation model, components, design tokens — plus flagged inconsistencies. This is the baseline both UI design and development build from.

---

## 1. Screen inventory

| # | File | Screen | Type | Nav state | In workflow V5? |
|---|---|---|---|---|---|
| 1 | `1-landing.html` | Field Test Landing ("what is this?") | Scroll page, own design language | No app nav | Yes ("PE Field Test Intro") |
| 2 | `2-welcome-image.html` | Welcome (previous holder's gift) | App frame | All greyed | Yes ("Welcome") |
| 3 | `3-planetary-emissary.html` | PE Intro (what am I? + disclosures) | App frame | All greyed | Yes ("PE Intro") |
| 4 | `4-pe-conversation.html` | PE Conversation (chat) | App frame | Emissary active | Yes |
| 5 | `5-the-commons.html` | The Commons (feed) | App frame | Commons active | Yes |
| 5b | `5b-contribute.html` | Contribute to the Commons (compose) | App frame, modal-style (✕ close) | Commons active | Yes |
| 6 | `6-pe-journal.html` | PE Journal (stone's entries) | App frame, modal-style (✕ close) | none active | Yes |
| 6b | `6b-journal-entry.html` | My Journal — New Entry (compose) | App frame | Journal active | **No — post-V5 addition** |
| 6c | `6c-my-journal.html` | My Journal (holder's entries) | App frame | Journal active | **No — post-V5 addition** |
| 7 | `7-gift-the-stone.html` | Gift the Stone (compose parting gift) | App frame, modal-style (✕ close) | Gift icon active | Yes |

**Not yet designed (referenced but missing):**

- **The Gifts** — chain view of all gifts ("view all of them" link on Welcome; "The Gifts" pill on Commons). Both link to `#`.
- **Commons card expanded** + **reply slot** ("leave something in return") — described in workflow notes, no screen.
- **Terms / Fineprint / FAQ** — linked from PE Intro, all `#`.
- **Rename / Edit** Emissary — linked from Conversation header, `#`.
- All **Phase 2 account screens** (claim, sign-in, holder profile/collection, notification settings) — to be specified in Doc 2/3.

---

## 2. Navigation model

Persistent bottom nav (`nav-base.svg` housing), identical structure on all app-frame screens:

```
[ Gift icon ] [ Emissary | Commons | Journal ] [ Pre-sale icon ]
   left            three center tabs               right
```

- **Left icon — Gift the Stone.** States: greyed (pre-conversation), resting, active (on screen 7). Opens screen 7.
- **Center tabs.** States: active / resting / greyed. Pre-conversation (screens 2–3) all three are greyed and non-links (`<span>`).
- **Right icon — Pre-sale.** Always enabled, external link to `stonemaps.org/exec-sum/es-presale.html` (new tab). Present even in the greyed pre-conversation state.
- **Floating pill(s)** above the nav carry the screen's primary action (Start a Conversation, Continue, View PE Journal, Contribute, Publish, Save/Publish, Gift Stone, New Entry).

### Nav routing facts (as coded)

- **Journal tab → `6b-journal-entry.html` (New Entry), not the journal list.** Compose-first entry into My Journal. ⚠️ Verify intentional; convention would be list-first (6c) with New Entry as the pill action — as it is, "View Journal" is a secondary pill on the compose screen.
- **PE Journal is not on the nav** — reachable only via "View PE Journal" pill on the Conversation screen.
- **Screen 6 (PE Journal) has no active tab** — it sits outside the tab structure, consistent with modal-style presentation.
- **Gift Stone pill on screen 7 → `1-landing.html`** — prototype shorthand for "reset and hand off"; the real flow needs a confirmation + post-gift state (Doc 2), since this is also the designated **claim moment** for accounts.
- Close (✕) buttons use `javascript:history.back()` — real app needs defined back-targets.

---

## 3. Two design languages

The prototype contains **two deliberate-looking but unreconciled visual systems**:

### A. Landing / artifact language (`1-landing.html` only)

| Token | Value |
|---|---|
| Background | `#3a3733` (warm dark grey) |
| Accent | `--amber #c8862a` |
| Text | `--white #f0ede8` |
| Display face | Cormorant Garamond (300, italic) |
| Body/label face | IBM Plex Mono (300/400) |
| Layout | full-page scroll, max-width 480px, staggered fadeIn (with `prefers-reduced-motion` support) |
| CTA style | rectangular amber block button, uppercase mono |

### B. App frame language (screens 2–7)

| Token | Value |
|---|---|
| `--map-ground` | `#3c1912` (deep umber) + layered map-hatch gradients |
| `--gold` | `#d9a441` |
| `--cream` | `#f1e9da` |
| `--panel` | `#2a2019` |
| `--ink` | `#181410` |
| `--rule` | `rgba(241,233,218,.18)` |
| Heading/label face | Jost (200–500, uppercase, tracked) |
| Body face | Source Sans 3 (300/400, italic for quoted/gift content) |
| Layout | fixed 390×844 device frame, 34px radius, 7px bezel |
| Buttons | pill (999px radius), metallic gradient states |

**Assessment:** plausibly intentional — the landing is the "physical artifact" page a QR code on the stone resolves to (poetic, print-like), while screens 2–7 are the app product. But amber `#c8862a` vs gold `#d9a441` and the two unrelated font pairings should be an explicit decision, not drift. **Recommendation:** keep two languages, but derive both from one token set (shared hue anchors, one scale). Decide in Phase 1.

⚠️ **CSS bug in landing:** `--white-dim` and `--white-faint` are both `rgba(240,237,232,1.5)` — alpha 1.5 is invalid (clamps to 1), so "dim" and "faint" text renders at full opacity. Intended values presumably ~0.75 / ~0.5.

---

## 4. Component inventory (app frame)

| Component | Where used | States / variants | Notes |
|---|---|---|---|
| **Device frame** | all app screens | — | 390×844, bezel, map-ground layered background |
| **Top bar** | 5b, 6, 6b, 6c, 7 | with/without ✕ close | Jost 14px gold uppercase title; `···` separator for context ("PE Journal ··· Pierrot") |
| **Screen header** (name + trait + edit) | 4 | — | Emissary name 24px gold, trait italic, Rename/Edit underlined link |
| **Pill button** | all | `resting`, `active`, `greyed`, `outline` | 999px radius; resting = grey metallic gradient, active = gold metallic, outline = transparent w/ border |
| **Tab** | nav | `active`, `resting`, `greyed` | same gradient system as pills, 10px Jost |
| **Nav bar** | all app screens | per-screen state matrix (§2) | SVG base + 2 icon slots + 3 tabs |
| **Floating action row** | all app screens | 1–2 pills | primary action above nav; 6b stacks two rows (Save/Publish + View Journal) |
| **Gift box / card** | 2 (Welcome) | `image` (circular framed photo), `text` | expand icon top-right; circular 8px `#d8d3c9` border on image variant |
| **Commons card** | 5 | photo + caption; expandable | 4:3 photo, italic caption; expanded state **not designed** |
| **Compose box** | 5b, 6b, 7 | filled / placeholder | panel bg (7, 5b) vs outlined translucent (6b) — two styles for same component, unify |
| **"Add files…" affordance** | 5b, 6b, 7 | — | gold text link, `+` prefix |
| **Checkbox row** | 3, 5b, 6b, 6 | checked / empty / disabled | 20px SVG box + label (+sub-label in share lists); disabled = .4 opacity ("PE — coming soon") |
| **Toggle** | 5 (Commons) | left position only designed | Stone/Public filter; right position + interaction not designed |
| **Journal entry** | 6, 6c | with/without "Shared with:" footer | date label (gold, 11px) + note; 6c adds share attribution |
| **Chat bubble** | 4 | stone message only | holder message style **not designed**; cream chat sheet with 24px top radius |
| **Chat input bar** | 4 | — | attach icon + rounded field + mic icon |
| **Disclosure row** | 3 | checked (journal notice) / empty (share consent) | ⚠️ the *pre-checked* box is the informational one; the *consent* one is empty — correct pattern, keep it |
| **Empty state** | 6c (`.empty`, unused in markup) | — | styled but not instantiated |

### Assets

`nav-base.svg`, `gift-stone-button-{active,resting,greyed}.svg`, `pre-sale-button-resting.svg`, `card-expand.svg`, `chat-attach.svg`, `chat-mic.svg`, `checkbox-{checked,empty}.svg`, `12stones.jpg`, `sri-lanka.png`. Two Unsplash photos are hot-linked in Commons (`images.unsplash.com`) — replace with local/owned assets before any deploy. Fonts load from Google Fonts CDN — for a PWA, self-host (offline shell requirement, Doc 6).

---

## 5. Copy & content system observations

- **Voice:** lowercase-intimate on landing ("what is this?"), spare and non-coercive in-app ("Doesn't ask for the story. Sits with what's carried."). Consent copy is plain-language, not legalese. This voice is a design asset — Doc 2 flows must specify copy in this register, especially for the account claim ask.
- **Privacy commitments made in copy** (these are product constraints now):
  1. "Your conversations are private and remain on your device and browser." (landing)
  2. "The stone keeps a quiet journal… You can opt out on the journal screen." (Welcome, PE Intro, PE Journal)
  3. "Entries remain private by default." (My Journal)
  4. Conversation-sharing with the project team is opt-in (PE Intro, unchecked by default).
- **Placeholder inconsistency:** Gift compose (7) shows filled sample text ("Mirror Mirror…") styled identically to 5b's *placeholder* styling; visually indistinguishable filled-vs-empty states. Unify compose component states.
- The "Mirror Mirror…" gift text on screen 7 reappears as a Commons card caption on screen 5 — implies **a gift may also surface in the Commons/Gifts chain**; the content-type relationship between Gifts and Commons posts needs explicit definition in Doc 3.

---

## 6. Flagged decisions carried into later docs

| # | Issue | Where resolved |
|---|---|---|
| F1 | Koozhankal vs Pierrot stone identity; is Emissary name holder-editable? | Glossary §naming / Doc 2 |
| F2 | Journal tab compose-first vs list-first | Doc 2 (IA/flows) |
| F3 | Two design languages — formalize or unify | Doc 5 tokens work, Phase 1 |
| F4 | Missing screens: The Gifts, Commons expanded card + reply slot, Terms/FAQ | Doc 2 scope list |
| F5 | Gift → landing reset shortcut; claim moment lives here | Doc 2 (gift/claim flow) |
| F6 | Gifts vs Commons posts content-model overlap | Doc 3 (data & state) |
| F7 | `--white-dim`/`--white-faint` alpha bug; unify compose states; replace hot-linked Unsplash images; self-host fonts | Doc 5 / build backlog |
| F8 | "My Journal scope TBD" — confirm it ships in MVP or is eval-only | Product call before Doc 2 finalizes |
