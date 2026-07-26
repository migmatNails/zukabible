# ZUKA — Concept Document
# The Poster Acquisition Engine ("Scan the City")

> **Type:** Feature Concept & Growth-Mechanism Brief · **Status:** Proposed (pre-build)
> **Authority:** Subordinate to ZukaBibleV4. Explores a feature; does not amend the
> Bible. Canonical entities graduate via ADR only if ratified for build.
> **Relationship:** The poster *presence* mechanic is shared with the In-Event
> Vendor Marketplace concept (which consumes it for vendor activation). THIS
> document owns the poster as a general ACQUISITION + PRESENCE mechanism; the
> vendor doc owns what to do with the presence once proven.
> **Working name:** "Scan the City" / Poster Check-In (final naming → zuka-growth)

---

## 1. The Idea in One Paragraph

ZUKA generates a printable QR poster for an event. The organiser prints and
places copies at the entrance and around the venue. Anyone scans it with their
phone camera. **Users** are checked in as present (activating the in-event
experience). **Non-users** hit a fast, read-only mobile web preview of the live
event — they *see what they're missing* before being invited to install. Every
scan and every resulting install is attributed to that event, poster, and zone,
turning each poster into a measured acquisition channel — and giving organisers
who drive installs real platform credit and status. The poster is a physical-
world front door into ZUKA's live layer, and the cheapest user-acquisition
surface the platform has.

---

## 2. Why This Matters (the strategic case)

A poster scan is the **highest-intent acquisition moment ZUKA will ever get**: a
person physically at an event, curious, phone in hand, in a social mood. That
beats any ad. For a pre-launch product whose scarcest resource is users, a free,
self-replicating, organiser-distributed acquisition channel is strategically
larger than the vendor feature that first motivated it.

```
It fits ZUKA's core:
- REALITY-FIRST: acquisition triggered by a real person at a real place.
- THE LIVE SYSTEM AS BRAND: the preview shows "the city, alive" to a newcomer
  in the one moment they can feel it (Vol 25 Ch 7).
- NO LOGIN WALL: the preview is genuinely useful without installing (Vol 16).
- TRUST-NATIVE GROWTH: the gift-loop principle (Vol 14) applied to free events —
  people pulled in by a real moment, not an ad.
```

---

## 3. The Scan Fork (the decision everything hangs on)

```
WHO SCANS              WHAT HAPPENS
─────────────────────────────────────────────────────────────────────────
Has ZUKA installed     App opens → checked in as PRESENT at this event →
                       the in-event experience activates (vendor discovery,
                       crowd meter, Live Wall, etc. — see §8).
No ZUKA                Fast mobile WEB PREVIEW (read-only) of the live event →
                       sees the value → one CTA to install → installs → app
                       opens back into this event (best-effort, §6).
```

The principle: **show, then ask.** A newcomer who has seen "6 vendors here, next
set in 20 minutes, 340 checked in" has a reason to install. A newcomer staring
at an app-store listing has only faith. The web preview is the conversion
surface; the install is the upgrade.

---

## 4. The Web Preview (read-only teaser — scoped decision)

The preview is a **read-only teaser**: it shows enough to create desire, but
doing anything (ordering, saving, joining, reviewing) requires the app. This
keeps a clear, honest reason to install while still respecting the no-login-wall
principle (browsing is free; acting needs the app).

### What it shows
```
ZUKA · <Event Name> — LIVE NOW
<n> here · next set <time> · <n> vendors

🍢 Kampala Bites — skewers · open
🥤 Cold Box — drinks · open
   …more (read-only list)

[ Get the app to order & save your night ]   ← ONE primary CTA
(small) Just looking? Keep browsing →          ← never forces the install
```

### Hard rules for the preview
```
- LOADS < 2s on event 3G. Text-first; lazy/така minimal imagery.
- USEFUL even if they never install (read the vibe, see who's here). Respect
  the no-login-wall proof — do not gate the teaser behind signup.
- EXACTLY ONE primary CTA. Multiple asks kill conversion.
- READ-ONLY: no ordering, no saving, no reviewing in the web layer. Those are
  the install payoff. (Keeps the "why install?" answer crisp.)
- HONEST: never implies live-when-not; never fakes counts (Vol 25 voice).
- NO DARK PATTERNS: no countdown pressure, no fake "selling fast", no nag-wall.
```

### Why read-only (the reasoning, kept visible)
A fully-interactive web app would blur the line on why anyone installs, double the
build, and pull money/identity flows into an un-app'd surface. Read-only is the
disciplined choice: it converts on *desire* ("I want in on this"), not on feature
parity. The app remains where participation actually happens.

---

## 5. Attributed Acquisition (the part that becomes a business asset)

Every scan carries `event_id`, `poster_id`, and `zone` (§7 of the vendor doc's
zone idea, if adopted). So every install traceable to a scan is attributable.

```
"Nyege Nights drove 84 new ZUKA users tonight."
"Bar poster: 51 · Entrance poster: 33."
```

Value to three parties:
```
ORGANISER  a metric they've never had + a reason to want posters everywhere +
           credit/status for driving installs (§9). They grow ZUKA and look good.
ZUKA       its cheapest acquisition channel, measured precisely: which event
           TYPES convert, which ZONES work, cost-per-install ≈ zero.
NEWCOMER   a genuinely useful first touch, not a cold store listing.
```

### Referral chain through the poster (organic extension)
If person A scans, installs, and shares "I'm at <Event>" — and friend B installs
from A's link — that attributes too. The poster seeds organic sharing.
```
Within existing anti-fraud limits (Vol 14 / Pulse Points): chain depth capped,
never cash-purchasable, velocity-monitored (Vol 11 substrate).
```

---

## 6. Deep-Link Survival (the honest hard problem)

When a non-user taps "Get the app," they leave the web preview → app store →
install → open. **The context of which event they scanned is lost across that
jump** unless engineered for. This breaks quietly for most teams; we name it
plainly.

```
PROBLEM:   web (event X) → store → install → app opens at generic HOME   ✗
WANT:      web (event X) → store → install → app opens INTO event X      ✓
```

### Options, with honest trade-offs
```
A. Deep-link service (Branch / Adjust / AppsFlyer)
   Built exactly for deferred deep linking; handles platform mess.
   Cost: third-party dependency = STACK DRIFT → needs an ADR. Reliable but a vendor.

B. Platform-native (iOS Universal Links / Android App Links + deferred match)
   Keeps the stack clean. Cost: genuinely fiddly; probabilistic matching; iOS has
   tightened this. Real engineering, imperfect.

C. The fallback (RECOMMENDED FOR MVP)
   If context is lost, the freshly-installed app opens a "Which event are you at?"
   screen showing nearby LIVE events (we have GPS). One extra tap, ~90% as good,
   ZERO special infrastructure, NO stack drift.
```

```
RECOMMENDATION (reality-checker lens): ship C (the GPS "which event?" fallback)
at MVP. Add a deep-link service (A) only when scan-to-install VOLUME proves the
dependency is worth it. Do not add a growth-SDK to the locked stack for a
free-event feature before the numbers justify it.
```

---

## 7. The Organiser's Poster Experience

```
GENERATION   ZUKA generates a print-ready PDF: event branding, a clear
             "Scan to enter the night" instruction (English; +Luganda later),
             crop marks, and the QR. Organiser prints at any shop.
PLACEMENT    ZUKA suggests how many + where (entrance + high-traffic spots),
             scaled to expected attendance.
ZONES (opt)  Distinct posters per zone (Entrance / Bar / Stage) — each its own
             scan analytics stream. (Shared with the vendor doc's zone idea;
             optional at MVP.)
LIVE TARGET  The printed poster is static, but what it OPENS is live and can
             change through the night (e.g. flip to "after-party info" late)
             WITHOUT reprinting — the poster points at the event, the event's
             state moves.
```

---

## 8. What the Poster Unlocks Beyond Vendors (the bigger idea)

Once someone has scanned in, they are a **proven-present attendee** — the same
gate that powers much of the in-event experience at FREE events:
```
- Vendor discovery + ordering (the vendor concept doc)
- Crowd meter contribution / City Pulse
- Live Wall (Vibe Drops) eligibility
- In-event announcements from the organiser
- Post-event Memory prompt (Vol 10)
- "Rate this event" eligibility

The poster is therefore the SINGLE ACTIVATION KEY for the in-event experience at
free events — not merely a vendor trigger. (Paid events get the same via staffed
ticket scanning.)
```

---

## 9. The Organiser Incentive Loop (credit + status — scoped decision)

Organisers who drive installs get **real platform credit AND a status badge** —
a genuine two-sided incentive, not just a ZUKA benefit.

```
PLATFORM CREDIT
  Installs attributed to an organiser's event earn credit toward that organiser's
  future ZUKA fees (e.g. their next event's listing/ticketing/vendor-listing
  charges). Acquisition they drive offsets what they owe. Concrete, motivating.

STATUS BADGE
  A human-readable standing (never a raw number, Vol 16): e.g.
  "Community Builder" → "City Builder" as cumulative attributed installs grow.
  Surfaced on their organiser profile. Reputation, not a leaderboard of vanity.

GUARDRAILS
  - Anti-fraud: attributed installs run through the Trust velocity/graph
    substrate (Vol 11). Self-installs, device farms, and chains are discounted.
  - Credit has clear terms (what it offsets, expiry) — disclosed, no surprises
    (anti-drain transparency, Vol 23).
  - Status is earned by REAL attributed installs only; never purchasable.
  - The badge reflects genuine ecosystem contribution (Vol 12 community-builder
    spirit), not spend.
```

---

## 10. Integrity & Abuse (the presence-tier question, shared with vendor doc)

A printed code proves LESS than a staffed scan — it can be photographed and
scanned remotely. The threat is **non-uniform**:
```
- A photo shared with someone ALSO at the event → harmless (they're present).
- A REMOTE scan (person at home) inflating presence → the actual problem.
So the real signal is IN-FENCE vs OUT-OF-FENCE, more than scanned-vs-not.
```

### Controls (MVP-appropriate)
```
GPS FENCE        on scan, check device is within the venue geofence (PostGIS,
                 in-stack). Out-of-fence → counted as "remote/marketing", NOT
                 present. This is the real integrity line.
TIME-BOUND       poster valid only between event start and (scheduled_end +
                 grace) — the required event duration makes this possible. A
                 photo scanned next week does nothing.
ONE-PER-DEVICE   a device counts once per event; re-scans don't inflate.
VELOCITY         improbable scan spread of one code → flagged (Vol 11).
HONEST NAMING    free-event presence is "checked in", never sold as a verified
                 "attendance" headcount. The naming manages the expectation;
                 don't claim turnstile precision a poster can't deliver.
```

> Free events deliberately do NOT offer paid-grade attendance analytics or
> review eligibility from poster scans alone — those need staffed/ticketed
> high-integrity presence. (Two-tier presence, per the vendor doc §3B Design 1.)

---

## 11. New Concepts This Introduces (for eventual ADR, NOT yet canonical)

```
ENTITIES (proposed; some SHARED with the vendor concept — define once):
  EventPoster         printable self-scan QR. (event_id, poster_code, zone,
                      valid_from, valid_to). One per zone if zones used.
  AttendancePresence  (SHARED) person's presence at an event. (person_id,
                      event_id, tier['high_integrity'|'lightweight'],
                      source['staffed_scan'|'poster_scan'], in_geofence,
                      created_at).
  ScanEvent           a single scan (poster_id, event_id, zone, device_hint,
                      in_geofence, is_existing_user, ts). Feeds attribution.
  InstallAttribution  links a new install back to its origin scan. (install_id,
                      origin_scan_id, event_id, poster_id, zone, organiser_id).
  OrganiserCredit     accrued credit from attributed installs. (organiser_id,
                      source_event_id, amount, state, expiry).
  OrganiserStatus     human-readable builder standing. (organiser_id, level,
                      attributed_installs_total).

EVENTS (append-only, ADR-015):
  PosterGenerated · PosterScanned · WebPreviewServed ·
  AttendancePresenceCreated · InstallAttributed ·
  OrganiserCreditAccrued · OrganiserStatusChanged

REUSED: PostGIS geofence, the scanner/credential model, the Trust velocity
substrate, Pulse-Points anti-fraud limits.
```

---

## 12. MVP Cut Lines (scope discipline — reality-checker to ratify)

```
PHASE 1 — POSTER CHECK-IN + WEB PREVIEW + ATTRIBUTION (the real MVP)
  Generate poster PDF · user scan = check-in · non-user scan = read-only web
  preview · one CTA to install · the GPS "which event?" fallback (§6 option C) ·
  basic attribution (scans + installs per event) · GPS fence + time-bound +
  one-per-device integrity. NO deep-link SDK, NO zones required.

PHASE 2 — INCENTIVE LOOP
  Organiser credit + status badge (§9), with the anti-fraud guardrails. Needs a
  credit-terms decision (founder + monetization) and DPPA review.

PHASE 3 — ENHANCEMENTS
  Poster zones + per-zone analytics · referral chains · a deep-link service (§6
  option A) IF volume justifies the stack-drift ADR · richer organiser dashboard.

DORMANT (not now): paid acquisition reports for organisers/sponsors, cross-event
  acquisition trends as a product, multi-language posters beyond +Luganda.
```

---

## 13. Open Questions

```
- CONSENT/DPPA (priority): the web preview + scan collect device + GPS + time
  from NON-users who've agreed to nothing — personal data from the first scan.
  Needs a clear, lightweight consent moment (not a wall). zuka-legal must rule
  before any build. This is the single most important pre-build item here.
- Deep-link approach: confirm MVP = GPS fallback (option C); set the volume
  trigger that would justify a deep-link SDK ADR later.
- Credit terms: what does organiser credit offset, at what rate, expiring when?
  (Founder + monetization; keep anti-drain-transparent.)
- Status ladder: the named levels + thresholds (zuka-growth, per Vol 25 voice).
- Does the web preview need its own lightweight hosting, or is it a Supabase
  Edge Function rendering server-side? (Believed: Edge Function + Cloudflare
  cache — in-stack, no drift.)
- GPS indoors/spoofing: accept as "good enough + honest naming", or invest more?
  (Recommendation: good-enough at MVP.)
```

---

## 14. Relationship to the Vendor Concept & the Bible

```
This doc      OWNS the poster as acquisition + general presence mechanism.
Vendor doc    CONSUMES the presence it produces (to activate vendor listings).
SHARED        EventPoster + AttendancePresence are defined ONCE (here), referenced
              by both. Avoid duplicate/divergent definitions.

Concept (here) → explore freely.
ADR (if ratified) → graduate the shared canonical entities into Vol 04/14/15.
Bible → only the locked, governed result. The Bible does not churn for this.
```

---

> **The Poster Acquisition Engine — Concept Document**
> The place becomes the front door. Show, then ask. Read-only teaser, real
> attribution, organiser credit + status. The cheapest users ZUKA will ever get —
> and the most present. Scan the city; the city, alive.