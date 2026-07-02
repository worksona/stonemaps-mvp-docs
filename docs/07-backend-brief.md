# Stonemaps — Backend Brief: Requirements & Build-vs-Reuse

**Doc 7 of the IA/UXD documentation set**
Status: Draft v1 · 2026-07-02
Direction (2026-07-02): the existing stone-maps backend is **an option, not a given**. This doc states what any backend must provide (from Docs 2–6), then evaluates the two paths. The IA/UX docs are deliberately backend-agnostic — nothing upstream changes whichever way this goes.

---

## 1. What any backend must provide

Derived from the data model (Doc 4), flows (Doc 3), and platform constraints (Doc 6). This is the checklist to score any candidate against.

**Core domain**
- [ ] Stones with identity (name, place, traits, media) and state (IDLE/HELD), opaque permanent ids for QR URLs
- [ ] Pairings as first-class records (person_ref ↔ stone, start/end, ordinal, consent flags) — content hangs off pairings, not accounts
- [ ] Anonymous person_ref support: content creation with **no account**, claimable later without rewriting content
- [ ] Gifts as an ordered per-stone chain (one per pairing)
- [ ] Commons posts with dual scope flags {stone, public} + one-time reply slot (OPEN→CLOSED, terminal)
- [ ] PE journal entries, pairing-scoped (rung 2), opt-out per pairing
- [ ] My Journal entries with additive share scopes {stone, commons, team} (F8 module)
- [ ] Media upload (image/audio/video), size caps, client-downscale-friendly

**Emissary**
- [ ] LLM conversation endpoint, per-stone persona (traits → system prompt), opening message generated from previous holder's gift
- [ ] Server generates PE journal entries post-conversation (anonymized summary) unless opted out
- [ ] **Transcript never persisted server-side** unless team-share consent ON (rung 1 rule R1) — this is an architectural property, not a feature

**Auth & accounts (Phase 2)**
- [ ] Passwordless magic link: token issue (claim + sign-in variants), single-use, 7-day expiry, verify endpoint
- [ ] Server-set HttpOnly sliding sessions ≥90 days (P3)
- [ ] Claim = attach account_id to pairing (Doc 4 §5); same flow for new and existing emails
- [ ] Account deletion = unlink pairings to anonymous, erase email/prefs

**Notifications (Doc 5)**
- [ ] Event emission on: gift published, reply posted, pairing ended
- [ ] Email sender with **transactional vs notification classes separated** (launch blocker if missed)
- [ ] Per-trigger prefs, one-tap unsubscribe (no login), daily coalescing cap
- [ ] SEEN_CURSOR per pairing (activity dot)

**Operational (field test)**
- [ ] Provision 24 stones, campaign/team grouping, printable QR export
- [ ] Team review surface for consented content (rung 3)
- [ ] Takedown tooling for rung 4–5 content
- [ ] Metrics: claim rate, email→deep-link rate, return visits (Doc 5 §5)

---

## 2. Option A — extend the existing stone-maps backend

**What it already has** (verified via its MCP tool surface, 2026-07-02; DB was down so schema-level detail is inferred, marked ◐):

| Requirement | Status |
|---|---|
| Stones, details, per-user ownership | ✓ (`list_stones`, `get_stone_details`) |
| User↔stone pairing concept | ✓ `get_self_pairs` — closest existing analog to Pairing ◐ (semantics unverified) |
| Emissary conversations | ✓ (`start_conversation` w/ geo, `list_conversations`) |
| Journal posts w/ visibility `private/public/team` | ✓ (`create_post`, `get_journal_posts`) — but scopes are single-enum, **not** the additive {stone, commons, team} sets the UX needs |
| Geo/map posts, nearby, search | ✓ — beyond current UX scope (provenance later) |
| Teams / campaigns (operator side) | ✓ |
| Personal API tokens | ✓ — developer auth, **not** end-user magic links |
| Anonymous-first content + later claim | ✗ appears account-centric throughout ◐ |
| Gifts chain, reply slots, dual-scope posts | ✗ not visible in API surface |
| Magic-link auth, notification classes, prefs | ✗ not visible |
| Health | ⚠️ DB down at time of writing (Redis, R2 fine) |

**Assessment.** Roughly the right *domain* (stones, pairings, conversations, scoped posts, operator layer already exist) but the wrong *identity assumption*: it looks account-first, and the MVP's defining property is anonymous-first-claim-later. Retrofitting that touches every content path. The post-visibility model (single enum vs additive scope sets) is a second structural mismatch.

**Cost shape:** low on domain CRUD (exists), high on auth rework + scope model migration + the gift-chain/reply-slot features that don't exist anyway. Plus: current instability needs diagnosis before any bet on it.

## 3. Option B — fresh build to this spec

Greenfield service implementing §1 directly. The domain is small (7 entities, 3 state machines) and the docs are effectively the spec. Modern stack of team's choice; the existing backend's R2 media storage and any Emissary prompt work are portable regardless.

**Cost shape:** flat and predictable — everything is built, nothing is fought. The operator layer (provisioning, review, takedown) is the hidden cost people forget; the existing backend already has some of it.

## 4. Recommendation

**Decide on one question: how account-first is the existing data model really?** The tool surface can't answer it (◐ marks); one hour with the actual schema when the DB is back up can.

- If pairings/posts can carry an anonymous person_ref with a nullable account link → **Option A**, extend: add magic-link auth, additive scopes, gifts/replies. You inherit the operator layer and geo for free.
- If identity is woven through content tables → **Option B**, fresh build against this doc, keeping R2 + operator concepts as reference. Retrofitting identity assumptions is the classic underestimated rewrite.

Either way, **the contract is §1** — score the schema against the checklist, not against sunk cost.

## 5. Decision log

| Date | Decision | By |
|---|---|---|
| 2026-07-02 | Existing backend demoted from assumption to option | product |
| — | A vs B: pending schema inspection (DB currently down) | — |
