# Stonemaps — PWA Constraints & Technical Notes

**Doc 6 of the IA/UXD documentation set**
Status: Draft v1 · 2026-07-02
Purpose: the platform realities that constrain the IA and flows. Short by design — each item is a constraint dev must design around, with the decision it forces.

---

## 1. Magic links × PWA — the one that breaks products

**Problem:** magic-link emails open in the default browser tab, **not** in an installed PWA. On iOS especially, the installed PWA and Safari **do not share storage** — a session established in the Safari tab does not exist in the home-screen app.

**Reality check for Stonemaps:** most field-test users will *not* have installed the PWA — they scanned a QR and used it in the browser. For them, magic links just work. The failure case is the engaged user who installed the app *and* clicks a claim/notification link.

**Required behavior (decision, not option):**
1. `/auth/verify` establishes the session **in whatever context it opens** — browser tab included. The afterlife experience must be fully functional in-browser; installation is an enhancement, never a requirement.
2. After verify in a browser tab, if a related installed app might exist, show a low-key line — *"If you've added Stonemaps to your home screen, it's signed in there too the next time it asks"* — implemented via one of:
   - a short-lived, single-use **handoff code** shown post-verify that the installed app can accept ("enter the 4 words from your email tab"), or
   - simply re-requesting a link from inside the installed app (cheapest; recommended for MVP).
3. Never rely on the link opening in the PWA. No custom-scheme tricks in MVP.

**MVP decision:** magic links are a browser-first flow; the installed PWA offers "sign in" which sends a fresh link — the user who installed the app taps their email on the same device and iOS opens… Safari, where they're signed in, and the app offers the re-request path. Accept the mild clunk; document it; revisit with passkeys post-field-test (magic-link-verified accounts upgrade cleanly to passkeys later — natural roadmap item).

## 2. Sessions & storage eviction

- Long-lived cookie sessions (≥90 days, sliding). Passwordless users must not face re-auth walls — a logged-out past holder is a lost past holder.
- iOS can evict IndexedDB/localStorage of *browser-tab* (non-installed) usage after ~7 days of Safari inactivity (ITP). Consequences:
  - **Conversation transcripts (rung 1) may not survive a long gap for browser-tab holders.** Accepted risk (Doc 4 §3) — Emissary handles gracefully.
  - **Session cookies survive** if HttpOnly server cookies (not JS-set) — use server-set cookies for auth. This mostly neutralizes eviction for auth.
  - The device `person_ref` (anonymous holder binding) can also be evicted → holder looks like a bystander. Mitigation: `person_ref` also lives server-side keyed to the active pairing; recovery flow = "are you still holding <Stone>?" re-bind prompt on the Landing page during an active pairing. **[design this small screen]**

## 3. Offline & installation

- **Offline scope for MVP:** app shell + last-viewed content caches. Composing (gift/post/entry) should hold drafts locally and retry. Conversation requires the network (LLM) — offline Emissary states needed: *"I'm out of range of myself right now."* **[copy + state to design]**
- **Install prompt:** never interrupt Stone Context with install prompts. The only sanctioned moment: after claim, on My Stones — *"add this to your home screen to keep the thread"*. iOS has no programmatic prompt; show the share-sheet instruction pattern there.
- Self-host fonts (currently Google CDN) and replace Unsplash hot-links (F7) — offline shell and privacy both require it.

## 4. QR / URL binding

- `/s/:stoneId` is printed on physical objects — immutable for the stone's life, opaque ids, HTTPS, no redirects through third-party shorteners.
- QR should encode the canonical URL directly (no interstitial), because scan-to-first-paint time *is* the first impression of a "talking stone."
- Landing must render meaningfully even while JS loads (SSR/static first paint) — the scan moment happens outdoors, on cell connections.

## 5. Media capture & upload

"Add files…" (gift, commons, journal) on mobile web: `<input type=file accept="image/*,audio/*,video/*" capture>` covers photo/audio/video including direct camera. Constraints: client-side image downscale before upload (cell connections), size caps per rung-4/5 content (moderation surface), background-retry for uploads mid-gift — a failed upload must never eat a parting gift. **Draft-preservation rule: the gift text is committed before media upload begins.**

## 6. Concrete MVP decisions this doc fixes

| # | Decision |
|---|---|
| P1 | Afterlife/auth fully functional in plain browser; PWA install is enhancement only |
| P2 | Magic-link handoff to installed app = re-request from inside app (no scheme hacks) |
| P3 | Auth via server-set HttpOnly cookies, ≥90-day sliding |
| P4 | `person_ref` mirrored server-side on the pairing; re-bind prompt screen exists |
| P5 | QR encodes canonical `/s/:stoneId`; landing SSR/static-first |
| P6 | Push notifications deferred entirely (Doc 5 §3) |
| P7 | Gift text commits before media upload; drafts local-first |
