# Stonemaps — Data & State Requirements

**Doc 4 of the IA/UXD documentation set · Phase 3 (pulled forward)**
Status: Draft v1 · 2026-07-02
Backend-agnostic: this describes what data must exist, where it lives, and who can see it — not schemas. The build-vs-reuse decision (Doc 7) implements this; it must not reshape it.

---

## 1. The privacy ladder

Every piece of content in the product sits on exactly one rung. This is the single most important table in the doc set — the copy has already made these promises to users.

| Rung | Content | Lives | Visible to | Survives handoff? |
|---|---|---|---|---|
| 1 · Device-private | PE Conversation transcript | **device only** (local storage) | current holder | No — never uploaded; cleared post-gift |
| 2 · Tenure-private | PE Journal entries (a tenure); My Journal entries (default) | server, pairing-scoped | that tenure's holder only | Yes → claimed holder's archive only |
| 3 · Consented-to-team | Conversations w/ share-consent ON; journal entries shared "Project Team" | server | holder + project team | Per consent |
| 4 · Stone-scoped | Gifts chain; Commons posts marked "Stone"; journal entries shared "The Stone" | server, stone-scoped | all holders of that stone (current + past) | Yes — this *is* the stone's memory |
| 5 · Public | Commons posts marked "Public" | server | all stoneholders | Yes |

**Hard rules derived from copy:**
- R1. The conversation transcript never leaves the device unless the PE-Intro consent box is checked — and even then what's shared is defined as the transcript copy to the team, **not** a change of rung for the live conversation.
- R2. PE Journal entries are *about* a holder; they belong to that holder's tenure. A new holder never sees a previous tenure's PE Journal. Opt-out stops generation, per stone, per tenure.
- R3. Anonymity in shared spaces: rungs 4–5 content is attributed to the **stone/holder-position** (*"Pierrot's third holder"* at most), never to an account identity. Accounts are private infrastructure, not social identity.

---

## 2. Entities (conceptual model)

```
STONE ──1:1── EMISSARY (persona: name*, traits, voice config)
  │  *name holder-editable per tenure? → F1, decide
  │
  ├─< PAIRING (the spine)
  │     stone_id, person_ref, started_at, ended_at
  │     consent_flags (team-share, pe-journal-opt-out)
  │     claim: none | pending(token, expiry) | claimed(account_id)
  │     ordinal (1st, 2nd… holder — powers anonymous attribution)
  │
  ├─< GIFT (one per pairing, the chain)
  │     pairing_id, text, media[], created_at
  │
  ├─< COMMONS_POST
  │     pairing_id (attribution via ordinal), text, media[]
  │     scopes: {stone, public} (≥1)
  │     reply: none | {pairing_id, text, media, created_at}   ← one, ever
  │
  ├─< PE_JOURNAL_ENTRY (pairing-scoped, rung 2)
  │     pairing_id, text, created_at
  │
  └─< MY_JOURNAL_ENTRY (F8 module; pairing-scoped)
        pairing_id, text, media[], created_at
        shares: {stone?, commons?, team?}          ← additive, per entry

ACCOUNT (Phase 2)
  email (verified via magic link), created_at
  ─< PAIRING (claimed)                 ← the collection
  notification_prefs {new_gift, reply, digest} × {email, push}
  ─< SEEN_CURSOR per pairing           ← powers the activity dot

OPERATOR SIDE (out of user-facing scope; needed for field test)
  STONE provisioning, campaign/team grouping, team-shared content review
```

**Modeling notes**
- **Pairing is the join everything hangs off.** Gifts, posts, journals reference pairing (not account) — so all content works anonymously, and claiming a pairing retroactively connects it to an account without rewriting content. This is the mechanism that makes "accounts optional, added later" true in the data model, not just the UX.
- A **gift is not a commons post**. Same shape, different chain (resolves F6): The Gifts is the pairing-ordered spine; Commons is the ambient feed. A gift *may additionally* be cross-published to Commons (the "Mirror Mirror" card on screen 5 suggests this) — if so, it's one content record surfaced in two collections, not two records.
- **Anonymous person_ref pre-claim:** a device-generated id stored locally + on the pairing. If never claimed, the pairing stays anonymous forever — fine, the chain doesn't care.

---

## 3. Client-side state (PWA)

| Store | Contents | Lifecycle |
|---|---|---|
| Local (IndexedDB) | conversation transcript; draft gift/post/entry; device person_ref; active stone_id | transcript cleared N days after pairing end (grace period for "wait, I wanted to save that" — recommend 7); drafts cleared on publish |
| Session/auth | long-lived session token post-claim | survives PWA restarts; Doc 6 covers iOS eviction |
| Server-synced | everything rung 2+ | on-demand fetch; offline shell caches last-viewed (Doc 6) |

**Conversation persistence question the prototype leaves open:** transcripts survive app restarts within a tenure (Flow B requires it) → IndexedDB, not memory. iOS *can evict* IndexedDB for storage pressure after long disuse — acceptable for MVP (conversation loss = graceful Emissary "we've talked before, though I hold it loosely"), but state it in the field-test guide. **[accepted risk, document]**

---

## 4. State machines

### Pairing
```
(stone idle) ──scan/first-open──▶ ACTIVE
ACTIVE ──gift published──▶ ENDING (claim offer shown)
ENDING ──email entered──▶ ENDED+CLAIM_PENDING ──link clicked──▶ ENDED+CLAIMED
ENDING ──skipped──▶ ENDED
ENDED+CLAIM_PENDING ──7 days──▶ ENDED (token expires; content persists, unclaimable*)
```
\* Recommend: expired claims recoverable by support during field test (email match).

### Stone
```
IDLE (no active pairing) ──first scan──▶ HELD ──gift──▶ IDLE …repeat
```
Current-holder binding: on IDLE→HELD, bind to the scanning device's person_ref; subsequent devices see bystander view (IA §1). Handoff race (two scans post-gift): first write wins, second gets bystander view. **[verify acceptable for field test]**

### Reply slot
```
OPEN ──reply posted──▶ CLOSED (terminal)
```

### Commons post scope matrix (resolves Flow C gap)
| stone | public | Appears in |
|---|---|---|
| ✓ | ✗ | Stone feed of that stone only |
| ✗ | ✓ | Public feed only |
| ✓ | ✓ | Both (one record) |

---

## 5. What claiming actually transfers

At claim, **nothing moves and nothing changes rung.** The pairing gains an `account_id`. The account thereby gains *access* to: that pairing's gift, commons posts, My Journal entries, PE Journal entries — all already server-side at rungs 2–5. The transcript (rung 1) is **not** part of the archive: it stayed on the device and dies with the grace period. The claim copy must not imply otherwise.

> Optional post-MVP: "export my conversation" as a local download offered during the grace period. Not in scope now; noted because users may ask.

---

## 6. Retention & deletion

| Data | Retention | Deletion path |
|---|---|---|
| Transcript | device only, cleared post-grace | automatic |
| Rung 2 content | life of product | account deletion removes claimed pairings' rung-2 content |
| Rung 4–5 content | life of stone/product — it's the communal artifact | takedown-by-request only (field test: via project team); full self-serve deletion would tear holes in the chain — **product call, defaulting to takedown-request in MVP** |
| Account | until deleted | delete = unlink pairings (they revert to anonymous), erase email + prefs |
| Claim tokens | 7 days | automatic |

GDPR-shaped note for the field test: rungs 4–5 are effectively contributions to a collective work; the consent/fineprint (M3 screens) must say so plainly *before* first contribution. This is why M3 is not cosmetic backlog.
