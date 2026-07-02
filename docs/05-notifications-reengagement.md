# Stonemaps — Notification & Re-engagement Spec

**Doc 5 of the IA/UXD documentation set**
Status: Draft v1 · 2026-07-02
Principles fixed in Flow G ([03-core-user-flows.md](03-core-user-flows.md)): event-driven not engagement-bait; email first, push later; every message deep-links to what it describes.

---

## 1. Who gets notified about what

Only **claimed past holders** receive notifications (current holders are *in* the app; anonymous past holders are unreachable by definition). All triggers are scoped to stones in the account's collection.

### Trigger matrix

| # | Event | Default | Channel | Latency | Recipient scope |
|---|---|---|---|---|---|
| T1 | New gift joins the chain of a stone you held | ON | email | ≤1h | all claimed past holders of that stone |
| T2 | Your commons post / gift received its one reply | ON | email | ≤1h | the author's account |
| T3 | Stone-scoped commons activity digest | OFF | email | weekly, fixed day | opted-in past holders |
| T4 | Stone changed hands (provenance hop) | OFF (fold into T1/T3 initially) | — | — | — |
| T5 | Claim link ("keep following") | n/a — transactional | email | immediate | the gifting holder |
| T6 | Claim link expiring reminder | ON | email | day 6 of 7, once | pending claims only |

**Anti-triggers (never send):** inactivity nudges, "you haven't visited", streaks, marketing inside the notification stream. The pre-sale audience is a separate consented list — never merge them.

**Volume guard:** hard cap of 1 non-transactional email per account per day; excess events coalesce into one "a few things happened around <Stone>" message.

---

## 2. Message anatomy (email)

Every notification email:
1. **Subject in the product voice, event-first.** *"A new gift joined Pierrot's chain"* — never *"You have 1 notification"*.
2. **One glimpse of content** (first line of the gift / the reply text) — enough to feel it, not enough to make opening pointless.
3. **One button**, deep-linked: `/me/pairings/:id?focus=gift-:id` etc.
4. Footer: per-trigger unsubscribe (one tap, no login) + link to `/me/settings`.
5. Plain, mostly-text template matching the artifact aesthetic — no marketing chrome.

Attribution in notifications follows R3 (Doc 4): *"the stone's fifth holder"*, never names.

---

## 3. Channel strategy

### Email (MVP)
The claim flow guarantees a verified address — email is the reliable channel and the only one MVP needs. Requires a transactional sender (claim links = transactional; T1–T3 = notification class) with separate sending domains or categories so unsubscribing from notifications never blocks claim/auth mail. **This distinction is a launch blocker if missed.**

### Web push (post-MVP, deliberately deferred)
- iOS: push requires the PWA installed to home screen (16.4+), permission prompt only from a user gesture inside the installed app.
- Rollout rule from Flow G: offer push only after the user has engaged with ≥1 email deep link — evidence they want the loop before we ask for the OS prompt (a denied prompt is near-unrecoverable).
- When added: T1 and T2 only. Digests stay email.

---

## 4. Opt-in surface

Single screen after first My Stones visit (Flow G), then editable at `/me/settings`:

```
Hear when something happens?
[✓] A new holder leaves a gift
[✓] Someone replies to something you left
[ ] Weekly digest of stone commons activity
[Save]   [Not now]
```
"Not now" = all OFF except transactional; never re-prompted unprompted (settings discoverable, a single passive hint allowed on the afterlife view: *"quiet by default — turn on whispers in settings"*).

---

## 5. The full re-engagement loop (system view)

```
holder gifts stone ──▶ claim email (T5) ──▶ account + prefs
        ▼                                        │
next holder's activity: gift (T1) / reply (T2) ──▶ email ──▶ deep link
        ▼                                                       │
   afterlife view (activity dot cleared, SEEN_CURSOR advances) ◀┘
        ▼
   [post-field-test hooks: request-a-stone, pre-sale interest — OUT of MVP]
```

Success metrics for the field test: claim rate at gifting (target: decide with team, suggest ≥40%), email open→deep-link rate, return visits per claimed account per month. Instrument these three from day one; they are the whole argument for the account layer.
