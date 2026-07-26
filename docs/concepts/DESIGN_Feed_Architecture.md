# DESIGN — Feed Architecture (Three Pages, Destinations, Promotion)

> **Status:** PROPOSED — founder decisions captured, pending final review before
> any build session.
> **Authority:** Vol 15 (Discovery & Attention Economy) — especially Ch 4-8,
> 11-12; DESIGN_Discovery_Feed.md (confirmed geo/ranking design);
> CONCEPT_In_Event_Vendor_Marketplace.md + DESIGN_Vendor_Marketplace.md;
> ADR-021 (Caching); the master inequality (manifestation_depth ≤
> min(DRL, BRL, CRL)).
> **Date:** 2026-07-02 · **Version:** 1.0
> **Founder decisions captured:** three pages · payment never overrides
> relevance · Live page shows Starting Soon · vendors pre-listed on upcoming
> gatherings · toggleable ticket-sales counter.

---

## 0 · The Governing Principle

ZUKA has two irreconcilable content logics, and this architecture keeps them
separate rather than blending them into mush:

```
THE PULSE   what is happening NOW — ranked by reality (proximity, liveness,
            crowd momentum). Perishable. Location-bound. Works at DRL 1.

THE FEED    what is relevant to THIS PERSON — ranked by taste + history.
            Durable. Identity-bound. Cannot exist before DRL 3.

THE SCENE   what this person explicitly chose to follow — venues, organisers,
            communities. Subscription-bound. Works whenever following exists.
```

The three pages activate at different maturity stages. The Pulse ships at MVP.
The Scene ships when following exists (Phase 2). The Feed earns its way into
existence only after a person has genuine attendance history (DRL 3). This is
not a UX preference — it is the readiness doctrine expressed as navigation.

---

## 1 · PAGE ONE — LIVE (The Pulse)

**Tagline:** *The city, right now.*
**Activates:** MVP (built — extends discovery_feed_nearby()).
**DRL:** 1 (rules + proximity + freshness; no personalisation required).

### 1.1 · Content, top to bottom

```
┌─ CITY PULSE STRIP ────────────────────────────────┐
│  "47 gatherings live in Kampala right now"        │
│  live activity bars · the emotional hook          │
├─ LIVE NOW ────────────────────────────────────────┤
│  Gatherings currently LIVE, ranked                │
│  proximity 0.55 + freshness 0.45 (confirmed)      │
│  + crowd_momentum once check-in data flows        │
├─ STARTING SOON ───────────────────────────────────┤
│  Gatherings going LIVE in the next 60-90 min      │
│  (founder-confirmed: Live page shows Starting Soon)│
├─ DIGITAL / STREAMING (venue_id IS NULL) ──────────┤
│  Always included regardless of radius             │
└───────────────────────────────────────────────────┘
```

### 1.2 · The Crowd Meter as a live ranking signal

Vol 13 Ch 7 defines Quiet → Picking Up → Packed → At Capacity. On the Live
page this is not just a per-card badge — it becomes a **ranking input** once
check-in data flows:

```
crowd_momentum = rate of check-ins in the last N minutes
```

A venue filling fast is more socially magnetic than a quiet one and should
rank higher — but capped, so a "Packed → At Capacity" venue that can't admit
more is de-emphasised (never send people to a door they can't enter). This
signal is DORMANT at MVP (no check-in volume yet) and activates when a
gathering has meaningful live check-in throughput.

### 1.3 · Why "Starting Soon" belongs here (founder decision)

Kampala live-event decisions are frequently made 30-90 minutes before leaving
the house. A strictly-live page is philosophically clean but practically thin
on a quiet weeknight. "Starting Soon" bridges the gap without polluting the
page with next-week content — it stays within the Pulse's time horizon
(tonight, imminent), not the Feed's (whenever).

### 1.4 · Ranking formula (confirmed, from DESIGN_Discovery_Feed.md)

```
LIVE NOW section:      score = proximity × 0.55 + freshness × 0.45
                       (+ crowd_momentum modifier when data exists)
STARTING SOON section: ranked by time-to-live ascending, then proximity
DIGITAL section:       freshness only (proximity is undefined)
Radius:                5km → 10km → city-wide auto-widen (server-side)
```

---

## 2 · PAGE TWO — FOR YOU (The Feed)

**Tagline:** *Your kind of night.*
**Activates:** DRL 3 (after 3-5 real check-ins produce taste signal).
**Before DRL 3:** the tab either doesn't appear, or shows a graceful
"we're still learning your taste — here's what's popular near you" state that
mirrors the Live page. It must NEVER show fake personalisation (Vol 15 Ch 11).

### 2.1 · The full ranking model (Vol 15 Ch 5)

At maturity, the Feed uses the complete composite:

```
score = relevance  × 0.30    (taste from attendance history — DRL 3+)
      + proximity  × 0.25
      + recency    × 0.20
      + social     × 0.15    (co-presence graph — who you've been with)
      + quality    × 0.10
      + boost_modifier        (promoted, capped — see §4)
```

Each dimension activates only when its data exists. At DRL 3, relevance and
social switch on. Below DRL 3, the Feed collapses toward proximity + freshness
(i.e. it degrades gracefully into the Pulse rather than faking taste).

### 2.2 · Content types the Feed blends

```
ORGANIC
  • Upcoming gatherings matching taste (Amapiano lover → Amapiano nights)
  • Gatherings your co-attendees are attending (social signal)
  • Traditions you belong to ("this venue's Friday, you've hit it 4×")
  • Memory prompts (you attended, no memory attached yet)
  • Vendors you engaged with, appearing at a new gathering
  • Trust-state changes ("you're now a Trusted Member")

PROMOTED (paid — see §4 for the constitution)
  • Boosted gathering posters (organiser paid for reach)
  • Venue ads (a club promoting its season)
```

### 2.3 · The "why am I seeing this" affordance (constitutional)

Vol 15 Ch 2 (Explainable Discovery) requires every ranked card to be able to
say why it's there:

```
"Because you went to 3 Amapiano nights"
"Because it's 800m away and starts in an hour"
"Because 4 people you've been out with are going"
"Promoted by Guvnor Kampala"
```

This is not optional polish — it is a constitutional commitment and a trust
mechanism. Every card carries its reason.

### 2.4 · Negative signals (constitutional — Vol 15 negative-relevance)

A person can mark "not my scene" / "not interested," and it MUST stick.
Negative-relevance suppression is what keeps the Feed from decaying into noise.
This is a first-class interaction, not a hidden setting.

---

## 3 · PAGE THREE — MY SCENE (Following)

**Tagline:** *The venues and people you follow.*
**Activates:** Phase 2 (when following exists).
**DRL:** Not gated by DRL — it is explicit subscription, not inference.

### 3.1 · Why this page exists separately from For You

For You is what the algorithm *infers*. My Scene is what the person *chose*.
People want both: the algorithm's surprises AND a reliable place to see the
specific venues, organisers, and communities they love. Separating them:
- gives organisers a reason to build a following (a growth flywheel)
- gives people a no-surprises, chronological home for their commitments
- keeps the For You algorithm honest (following isn't the same as taste)

### 3.2 · Content

```
• Chronological (not algorithm-ranked) posts from followed entities
• Upcoming gatherings from followed venues/organisers
• Community activity (Phase 3+)
• New-follower-relevant memory shares (Phase 3+)
```

My Scene is deliberately NOT heavily ranked — it is closer to a subscription
timeline. Predictability is the feature.

---

## 4 · THE PROMOTED-CONTENT CONSTITUTION

**Founder decision, locked: payment never overrides relevance.**
This reconciles with and tightens Vol 15 Ch 8 (Sponsored Content Constitution).

### 4.1 · The three hard rules (Vol 15 Ch 8 + founder decision)

```
1. CLEARLY LABELED       Every promoted card marked "Promoted by [X]".
                         Never disguised as organic.

2. CAPPED LOAD           Maximum 1 promoted card per 10 organic cards
                         (the ZUKA.md anti-drain rule — Vol 15 Ch 8).
                         NOTE: this is the constitutional number. Do not
                         loosen it without a Bible amendment.

3. REACH WITHIN RELEVANCE   Payment buys AMPLIFICATION within relevance,
                            never relevance OVERRIDE. A paid gospel-concert
                            poster is NOT shown to the techno crowd just
                            because it was paid for. The promoted card must
                            still clear a minimum relevance threshold for
                            the person before it is eligible to be shown.
```

### 4.2 · How "reach within relevance" works mechanically

```
A promoted gathering enters the candidate pool like any other.
Its organic relevance score is computed normally.
IF relevance_score < min_threshold_for_person → the promotion is NOT shown
    to that person (payment cannot force an irrelevant match).
IF relevance_score ≥ min_threshold → the boost_modifier amplifies its rank
    within the eligible set, and it occupies a capped promoted slot.
```

This is both ethical (no manipulation, Vol 16) and better product: irrelevant
ads train people to ignore the feed, destroying the value the organiser paid
for. Relevance-gated promotion protects the advertiser from wasting spend and
the person from spam.

### 4.3 · Fairness interaction (Vol 15 Ch 7)

Promoted slots are SEPARATE from the fairness-of-exposure floor for new/small
organisers. Paid reach does not consume the visibility floor that guarantees
emerging organisers a baseline. The two systems are independent: fairness
protects the small; promotion amplifies the paying; neither cannibalises the
other.

---

## 5 · DESTINATION PAGES (the tap-through experience)

**Design principle:** *The card is a promise; the destination fulfils it in
the mode the content demands.* A live event promises immediacy — its page
delivers immediacy. An upcoming event promises a plan — its page delivers
planning tools. Each post type routes to a page shaped for its job.

### 5.1 · LIVE GATHERING → the "Happening Now" page

```
JOB: get me there, now.
  • Crowd meter front and centre ("Packed · 340 here now")
  • "X people checked in" live counter
  • Directions + SafeBoda / Zuka Ride button (Vol 20 Ch 8)
  • Fastest path to a ticket if they don't hold one (3-tap, pre-filled)
  • Live audio board-out (Phase 2 — "hear the room before you commit")
  • If they hold a ticket: their QR, one tap away
```

### 5.2 · UPCOMING GATHERING → the "Plan to Go" page

```
JOB: help me decide and commit ahead of time.
  • Full lineup / programme
  • Date + time with add-to-calendar
  • Ticket tiers and prices
  • Social proof — who else is going (co-attendee signal)
  • Venue info, map, capacity
  • TOGGLEABLE TICKET-SALES COUNTER (founder decision — see §6)
  • PRE-LISTED VENDORS (founder decision — see §7)
  • Follow-the-organiser affordance (feeds My Scene)
```

### 5.3 · PROMOTED AD / BOOSTED POSTER → the "Conversion" page

```
JOB: convert the interest the organiser paid to generate.
  • Richer media allowed — video, gallery, artist bios
  • "Promoted by [X]" marker carried through from the card (honesty)
  • The SAME real ticket flow (no separate/sketchy checkout)
  • Clear organiser identity (who paid for this)
```

### 5.4 · POSTER (self-scan / free event) → the "Scan to Join" page

```
JOB: convert a physical poster in the real world into a digital participant.
  • Ties into CONCEPT_Poster_Acquisition_Engine.md
  • Works for NON-users (read-only web preview — no wall)
  • Scan-to-check-in flow (GPS-fenced, time-bound, one-per-device)
  • Gentle "install ZUKA to keep this" for non-users
  BLOCKED ON: Poster Acquisition Engine's DPPA consent question (its own
  top blocker). Route B / free-event dependency — defer until scheduled.
```

### 5.5 · VENDOR LISTING → the "What's For Sale Here" page

```
JOB: let attendees find vendors at an event.
  • Phase 2. Attendance-gated (only proven attendees see it live).
  • BUT: see §7 — vendors with confirmed tickets are PRE-LISTED on the
    upcoming gathering page BEFORE the event (founder decision), which is
    a discovery surface distinct from the live attendance-gated listing.
```

### 5.6 · MEMORY → the "Relive It" page (Phase 3)

```
JOB: let a person relive a night they were verifiably at.
  • The Live Wall of a past gathering — photos, who was there, the moment
  • Only accessible to verified attendees of that gathering
  • Memory ≠ Media (constitutional) — meaning, not just files
```

---

## 6 · TOGGLEABLE TICKET-SALES DISPLAY (founder decision)

The organiser chooses, per gathering, whether the ticket-sales counter is:

```
PUBLIC       Attendees see "142 going" / "38 tickets left" — social proof,
             scarcity signal (honest scarcity, real numbers — NOT manufactured
             urgency, which Vol 16 forbids).

PRIVATE      Only the organiser sees sales figures. The public sees no counter
             at all (not "0", not "hidden" — simply absent).
```

### 6.1 · Design rules

```
• Default: PUBLIC-COUNT-ONLY at a coarse granularity, or PRIVATE — founder
  to pick the default; recommend defaulting to PRIVATE (an organiser opts
  IN to public visibility, never surprised into exposing weak sales).
• The toggle is a gathering-level setting (new column: sales_visibility
  ENUM('public','private') DEFAULT 'private').
• When PUBLIC, show real numbers only. Never fabricate or inflate.
  "38 left" must mean 38 left. Honest scarcity is allowed; fake urgency
  (Vol 16 dark-pattern prohibition) is not.
• "Sold out" is always shown publicly regardless of the toggle — it is
  operationally necessary information (don't send people to a closed door),
  and it reveals no weakness.
```

### 6.2 · Why default to PRIVATE

An organiser with slow early sales should not be forced to broadcast weakness —
that creates a death spiral (low count → looks dead → nobody buys). Opt-in
public visibility lets confident organisers use social proof while protecting
everyone else. This respects the anti-manipulation constitution from both
directions: no fake-high numbers, and no forced-low exposure.

---

## 7 · VENDOR PRE-LISTING ON UPCOMING GATHERINGS (founder decision)

**The decision:** vendors whose ticket purchase for a gathering is CONFIRMED
are listed on that gathering's upcoming/event page — before the event, before
the scan.

### 7.1 · The important distinction this creates

This introduces a SECOND vendor visibility surface, distinct from the live
attendance-gated marketplace:

```
PRE-EVENT PRE-LISTING (new — this decision)
  • Trigger: vendor's ticket purchase for the gathering is CONFIRMED (paid)
  • Visibility: shown on the upcoming gathering page to anyone viewing it
  • Purpose: "these vendors will be here" — helps attendees anticipate,
    helps vendors attract pre-commitment
  • State: CONFIRMED-BUT-NOT-YET-PRESENT (paid, not yet scanned in)

LIVE ATTENDANCE-GATED LISTING (existing vendor design)
  • Trigger: vendor physically scans in (the Reality Doctrine activation)
  • Visibility: only proven attendees at the event see it
  • Purpose: "these vendors are here NOW, come find them"
  • State: PRESENT (scanned in, listing LIVE)
```

### 7.2 · Reconciling with the Reality Doctrine

This is subtle and important. ZUKA's core doctrine is "presence is proven, not
claimed." Pre-listing a paid-but-not-present vendor seems to bend that. It does
not, IF the two states are clearly distinguished:

```
Pre-event page:  "Vendors expected: [list]"  (CONFIRMED — they've committed
                 and paid, a real economic fact, but marked as EXPECTED)
Live page:       "Vendors here now: [list]"   (PRESENT — scanned in, the
                 Reality-Doctrine-activated live listing)
```

The pre-listing is honest because it represents a REAL fact (a confirmed, paid
vendor commitment) — not a claim of presence. The word matters: "expected" /
"confirmed," never "here." Presence still requires the scan. The doctrine holds.

### 7.3 · Schema implication (for the build session)

```
• vendor_listing gains a state distinction: EXPECTED (paid, pre-event) vs
  PRESENT (scanned in, live) — extends the DORMANT→ACTIVATING→LIVE lifecycle
  already in the vendor design.
• The upcoming gathering page queries vendor_listings WHERE gathering_id = X
  AND state = 'EXPECTED' (paid, not yet present).
• On scan-in, EXPECTED → PRESENT (the existing check_in activation, now also
  flipping the pre-listing to live).
```

---

## 8 · CACHING POSTURE (per ADR-021)

```
LIVE page (Pulse)    Cacheable at Cloudflare edge, short TTL (30s),
                     invalidated on LIVE/ENDED and on crowd-state changes.
FOR YOU page (Feed)  Per-person — NOT edge-cacheable at MVP. Candidate for
                     application-level cache (Redis) at DRL 3+ scale (the
                     migration target named in the Distributed Systems
                     Register §2).
MY SCENE page        Per-person subscription — same as For You.
Ticket-sales counter NEVER stale-cached when PUBLIC — must reflect real
                     remaining capacity (a cached "38 left" that's actually
                     0 sends people to a closed sale). Short TTL or realtime.
Promoted slots       Never cached across people (relevance-gated per person).
```

---

## 9 · MATURITY SEQUENCING

```
MVP (now)     LIVE page (Pulse) — ships with the Flutter app.
              Destination pages: LIVE gathering, UPCOMING gathering.
              Ticket-sales toggle (simple column + UI).

PHASE 2       MY SCENE page (following).
              PROMOTED content (the ad system + relevance gate).
              Vendor pre-listing + live attendance-gated listing.
              Destination pages: Promoted, Vendor.
              Live audio board-out on the Happening Now page.

PHASE 3       FOR YOU page (the Feed) — activates at DRL 3.
              Crowd-momentum ranking signal (needs check-in volume).
              Memory destination page + memory prompts in-feed.
              Social / co-presence ranking dimension.

PHASE 4       Feed carries opportunity matches, community activity,
              full participation-feed (not just events).
```

---

## 10 · OPEN DECISIONS FOR FOUNDER

Resolve these before the build session:

```
1. Ticket-sales counter default: recommend PRIVATE (opt-in to public).
   Confirm or override.

2. Public counter granularity: exact ("38 left") or coarse ("Filling fast" /
   "Almost full")? Coarse is softer on both scarcity-anxiety and
   competitive-intelligence leakage. Recommend coarse bands as an option
   the organiser can choose alongside public/private.

3. For You page below DRL 3: hidden entirely, or shown as a graceful
   "still learning" proxy of the Live page? Recommend shown-as-proxy so the
   navigation is stable and the page "fills in" as taste develops.

4. Promoted content at MVP or Phase 2? Recommend Phase 2 — the ad system
   needs the relevance gate, which needs some taste signal to be meaningful.
   Shipping ads before relevance exists risks violating "reach within
   relevance" by default.

5. Vendor pre-listing — does the EXPECTED listing show the vendor's offerings
   (menu/products) pre-event, or just their name/logo until they're PRESENT?
   Recommend name/logo + category pre-event; full offerings only when PRESENT
   (keeps the live listing the richer reward for actually showing up).
```

---

## 11 · IMPLEMENTATION SCOPE (derived — for the build sessions)

```
BUILD SESSION 1 (MVP feed)
  • Live page RPC: extend/wrap discovery_feed_nearby() with the three
    sections (Live Now / Starting Soon / Digital)
  • sales_visibility column on gathering + the public/private counter logic
  • Destination page data contracts: live_gathering_page(), upcoming_gathering_page()

BUILD SESSION 2 (Phase 2 — following + promotion + vendor surfaces)
  • follow relationships + My Scene feed RPC
  • promoted-content candidate injection + relevance gate + 1:10 cap
  • vendor pre-listing (EXPECTED state) on upcoming page

BUILD SESSION 3 (Phase 3 — the personalised Feed)
  • For You RPC with the full 6-dimension composite (DRL-3 gated)
  • crowd_momentum signal · negative-relevance suppression · explainability
```

---

*Subordinate to Vol 15 and the readiness doctrine. Payment amplifies within
relevance; it never overrides it. Presence is proven, not claimed — even when
a vendor is expected. Honest scarcity, never manufactured urgency.*
*ZUKA — Human Participation Infrastructure. The city just came alive.*
