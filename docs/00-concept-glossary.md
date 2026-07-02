# Stonemaps — Concept Glossary

**Doc 0 of the IA/UXD documentation set · Phase 0**
Status: Draft v1 · 2026-07-02
Source of truth: the 10 prototype screens in this folder + PE-FT-workflow-V5.jpg. Backend-agnostic — terms borrowed from the existing stone-maps API are marked *(prior art)* and are reference patterns, not dependencies.

Every other document in this set uses these terms exactly as defined here. If a term needs to change, change it here first.

---

## Core entities

### Stone
A physical (or virtual) object circulating among people. Carries a **name**, a **place of origin**, and a **traits list** (personality description, e.g. "Unhurried. Doesn't push. Has seen worse."). Twenty-four stones circulate in the field test; "some are real, some are virtual" (landing copy). A stone accumulates memory, place, and provenance as it moves through hands.

> ⚠️ Open question: the landing screen names the stone **Koozhankal** (Sri Lanka) while the conversation screen names it **Pierrot**. Either these are two different stones used across prototype screens, or the name drifted between sessions. Every stone-scoped screen must render the *same* stone identity; docs assume one stone per journey.

### Planetary Emissary (PE / Emissary)
The stone's conversational voice — the AI persona the holder talks to. Self-describes as unformed: "I'm not sure yet. A talking stone? A map, a voice, something else?" The Emissary opens each new holder's conversation by referencing the previous holder's parting message. The Emissary is per-stone: each stone has one Emissary identity, renameable by the holder ("Rename / Edit" on the conversation screen).

### Holder
The person currently in possession of a stone. Holdership is exclusive and sequential — one holder at a time per stone, forming a chain. In the current prototype a holder is **anonymous** (no account); identity is implicit in device + browser.

### Holder Chain / Provenance
The ordered sequence of holders a stone has passed through. Surfaced to users through Gifts (each holder leaves one) and Emissary references to "the last person who held me." Not yet surfaced as an explicit provenance view.

### Account (new — Phase 2 concept)
A durable, global identity for a person across all stones they have ever held. Established via **passwordless magic link / OTP** (decided 2026-07-02). Not required to begin using the app; offered **after gifting a stone** as the way to keep following it. An account aggregates: pairing history, preserved private archives, notification preferences.

### Pairing (prior art: `self_pairs`)
The relationship between a person and a stone, with a start and (after gifting) an end. The existing stone-maps backend models this as "self-pairs"; whatever backend is chosen, *pairing* is the canonical term for the user↔stone relationship record. A person's set of pairings is their **collection**.

### Gift
A parting artifact one holder leaves for the next — text plus optional rich media (photo, audio, video). Created on the **Gift the Stone** screen. The chain of gifts is **The Gifts**: "a linear chain of posts by sequential holders" (workflow notes). The next holder sees the latest gift on **Welcome** and can "view all of them."

### Gifting / Handoff
The act of passing the stone to a new person. In the current prototype this **exits and resets conversation history** (Gift the Stone screen). With accounts, the reset applies to the *device/next holder's view*, not to the giver's preserved archive (see Data & State Requirements, Doc 3).

---

## Spaces & content

### PE Conversation
The private chat between the current holder and the Emissary. Marked *(PRIVATE)* in the workflow. Landing copy commits: "Your conversations are private and remain on your device and browser." One conversation context per holdership; reset at handoff.

### The Commons
A shared feed of posts contributed by stoneholders. Two-position filter toggle: **Stone** (posts scoped to this stone's holders) / **Public** (ambient feed, all stoneholders). Cards expand in place; each card offers a one-time **"Leave something in return"** reply slot — one-level reply from the stone's holder, slot closes once used, no threading (workflow notes).

### Contribute (to the Commons)
The compose screen for a Commons post. Publish targets are checkboxes, not either/or: **Stone** ("Visible to this stone's holders, always") and/or **Public** ("Enters the ambient feed, visible to all stoneholders").

### PE Journal
The **stone's own journal** — anonymized entries the Emissary writes after each conversation, "summarizing what it noticed." Scoped to the current holder's sessions; visible to the current holder; holder can **opt out** per stone (checkbox on the PE Journal screen, also disclosed on Welcome and PE Intro). Workflow marks it "(current holder)" — entries about *you* are yours to see.

### My Journal
The **holder's own journal** — entries the person writes. Private by default, with per-entry share scopes: **The Stone** (this stone's holders, always), **The Commons** (ambient feed), **Project Team** (shares with Stonemaps), **PE** (coming soon — would feed the entry to the Emissary).

> ⚠️ The prototype itself flags this: "My Journal scope TBD. Active for evaluation." My Journal does not appear in the workflow diagram — it is an addition made in the HTML prototypes after V5 of the diagram.

### Journal (nav tab)
The nav tab labeled "Journal" opens **My Journal — New Entry** (6b). PE Journal is reached from the conversation screen ("View PE Journal"). Two different journals share one word in the UI — the glossary distinction (PE Journal vs My Journal) must be kept everywhere in documentation to avoid confusion.

---

## Field-test / operational terms

### Field Test
The current release phase: 24 stones circulating with invited participants. Landing frames it explicitly: "This is a field test. You're invited into the conversation."

### Project Team
Stonemaps, the operating organization. Appears to users in two consent surfaces: the PE Intro checkbox ("Share your PE conversations with the project team to help us improve") and the My Journal "Project Team" share scope.

### Pre-sale
External marketing surface (`stonemaps.org/exec-sum/es-presale.html`), reachable from the persistent nav (right icon). Out of scope for app IA except as an exit link.

### Campaign / Team *(prior art)*
Operator-side grouping concepts in the existing backend (stones provisioned under campaigns run by teams). No user-facing surface in the prototype. Retained in the glossary because the "Stone/Public" and "Project Team" visibility scopes will need an operator-side counterpart in any backend. Not a user-facing term.

---

## Interaction & state vocabulary

| Term | Meaning |
|---|---|
| **First-time gate** | Welcome → PE Intro sequence shown only on a holder's first visit ("FIRST TIME" branch in workflow); returning holders go straight to PE Conversation. |
| **Greyed nav** | Pre-conversation state: all tabs + Gift icon disabled until the first conversation begins (Welcome and PE Intro screens show this). |
| **Reply slot** | The one-time, one-level "leave something in return" response attached to a Commons card. |
| **Opt-out** | Holder disabling PE Journal entries for a stone. Per-stone, reversible. |
| **Claim** (new) | The moment an anonymous holder attaches an account (magic link) to their pairing — expected at/after gifting. Defined fully in Doc 2 (Flows). |
| **Reset** | Clearing conversation history at handoff so the next holder starts fresh. |

---

## Naming decisions to make (carried forward)

1. **Koozhankal vs Pierrot** — one stone journey per doc; confirm intended stone identity model (stone name fixed vs holder-renameable — "Rename / Edit" suggests the *Emissary* name is holder-editable, which interacts with stone identity).
2. **"Journal" tab label** ambiguity (My Journal vs PE Journal) — recommend distinct labels or a merged journal hub screen.
3. **"Holder" vs "Stoneholder"** — copy uses both; recommend **holder** in docs, **stoneholder** only in user-facing copy where it already appears.
4. **Account naming in-product** — "account" may be too transactional for the product's tone; candidates: *keeper profile*, *your thread*, *holder record*. Decide in Phase 2.
