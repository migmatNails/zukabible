# ZUKA — Discovery Feed Design Document
# Version 1.0 · 2026-07-01 · Pre-S008 Design Session

## Status: PROPOSED — pending founder review and confirmation

This is a design document, not a build artifact. No SQL, no migrations were
written in this session. It is grounded in a read of the live schema
(`supabase/migrations/`), the current `discovery_feed()` RPC, ADR-021, and
ZukaBibleV4 Vol 01 / 13 / 15 / 04 — not assumption.

**Reality check before the design starts (two corrections to this session's
own briefing):**
1. `gathering` has **no `geo` column**. Only `venue.geo` exists
   (`GEOGRAPHY(Point,4326)`, GIST-indexed, S002/S003). Proximity is only
   computable for gatherings with a non-null `venue_id`. S008's own session
   prompt (`docs/sessions/NEXT.md`) already anticipated this and scopes
   adding a geo column if needed — this design assumes venue-based proximity
   and treats a gathering geo column as an S008 implementation detail, not a
   design decision.
2. There is **no quality/verification signal anywhere in the schema** —
   no `is_verified` on `venue` or `gathering`. Vol 15 Ch 4's "Quality"
   dimension is not partially available, it is currently zero-signal. This
   is called out explicitly in §2 and §6 rather than quietly assumed away.

---

## 1. Geo-Radius Decisions

**Default radius: 5km.** This isn't invented — it reuses the radius Vol 13
Ch 7 already established for the Crowd Meter's Go Live notification. Reusing
it keeps "how far is local" consistent across the app rather than introducing
a second, unexplained number. Kampala's density means 5km reaches from most
inner neighbourhoods to the CBD without over-fetching on a 3G connection.

**Fallback rule (never-blank, Vol 15 Ch 10):** if the 5km result set falls
below a floor of **5 results**, the query widens in fixed steps —
5km → 10km → city-wide (no cap) — server-side, inside the same RPC call,
until either the floor is met or city-wide is exhausted. This is a design
proposal, not a Bible mandate.

**Dynamic vs. fixed radius — recommendation: fixed, with server-side
auto-widen only, no client "search wider" gesture at MVP.** The auto-widen
above already satisfies Ch 10's never-blank rule without adding client
complexity or a second round-trip. A user-controlled "search wider" gesture
is a real UX improvement but is client work and an extra affordance to design
and test on a 2GB Android — deferred (see §5). Rationale for rejecting full
dynamic-expand-by-default instead of a fixed default: an unbounded expand
could silently return a gathering 40km away with no visual cue why, which
reads as broken on a first open. Fixed default + bounded widen steps keeps
the behaviour explainable (Ch 2's "Explainable Discovery" principle applies
to radius behaviour too, not just ranking).

**PostGIS surface needed at minimum:** `ST_DWithin` for the radius filter,
and `ST_Distance` for both the proximity ranking score (§2) and the
distance-widening logic. Both are cheap against a GIST-indexed geography
column at MVP's gathering volume. Deferred: `ST_ClusterKMeans` / spatial
clustering — that belongs to the City Pulse heat map (Vol 13 Ch 7), a
different surface from the ranked list this document specs.

---

## 2. Ranking Model (DRL 1-2)

Vol 15 Ch 11 gates discovery to DRL 1-2 at MVP (rules + basic ranking;
personalisation is DRL 3, unlocked after 3-5 real check-ins). Checking the
full Ch 5 formula against what the schema can actually compute today:

```
RELEVANCE   — requires a taste profile.            NOT AVAILABLE (DRL 3+)
PROXIMITY   — ST_Distance from venue.geo.           AVAILABLE (venue-linked gatherings only)
RECENCY     — starts_at + lifecycle status.         AVAILABLE
SOCIAL      — requires the relationship graph.      NOT AVAILABLE (DRL 3+)
QUALITY     — verification/reviews.                 NOT AVAILABLE — zero signal, not partial
                                                     (no is_verified column exists; see §6)
```

**Actual DRL 1-2 formula (2 of 6 dimensions are real; everything else is an
honest zero, not silently dropped):**

```
score = proximity × 0.55 + freshness × 0.45
```

The 0.55:0.45 split preserves the *ratio* Vol 15 Ch 5 assigns proximity
(0.25) to recency (0.20) in the full formula, renormalised to sum to 1 now
that relevance/social/quality contribute 0. `boost_modifier` (Ch 8, the paid
promotion cap) is not wired in — there's no Campaign/monetisation RPC yet —
so it's 0 for every row today, not removed from the model; it activates the
moment a boost mechanism ships, still bounded by the existing anti-drain cap
(max 2 positions, never displaces the organic top 3).

**LIVE weighting — recommendation: a separate "Live Now" section above the
ranked list, not a score multiplier or fixed boost.** Vol 13 Ch 7 already
describes a "Live Now feed" as a City Pulse concept distinct from the ranked
discovery list — reusing that existing mental model avoids inventing a new
one, and avoids the fragility of tuning a multiplier against only two real
signals. PUBLISHED/ACTIVE gatherings get the composite score below the Live
Now section. Alternative considered: a fixed rank-boost inside one flat list
— rejected because it makes the composite score's meaning fuzzier for no
real MVP benefit, given how few gatherings exist to rank.

**Fairness-of-exposure floor (Vol 15 Ch 7) — design now, do not activate.**
At MVP's real scale (a handful of organisers, dozens of gatherings, not
thousands), there isn't yet a long tail to starve — the floor would be
solving a problem that doesn't exist yet at this volume, and Ch 7 itself
frames this as a governed, Architecture-Council-reviewed trade-off, not a
default-on mechanism. Proposed (not yet founder-approved — see §6) DRL
activation trigger: turn it on once the feed regularly carries **≥20
concurrent discoverable gatherings across ≥10 distinct organisers**, the
point where a rich-get-richer dynamic becomes structurally possible rather
than hypothetical.

---

## 3. Offline and Connectivity

**Offline state — recommendation: last-cached feed (labelled stale) as
primary, explicit "offline, last seen" as the fallback when no cache
exists.** Concretely: if the client has ever successfully fetched the feed,
show that cached result with a visible "Last updated 12 min ago — you're
offline" label (Option A) rather than discarding it for a generic message.
Only on a genuine first-ever open with no connectivity — no cache exists at
all — fall back to an explicit offline empty state (Option B) rather than a
blank screen. **Option C (a curated static "happening this weekend"
seed list) is rejected for S008**: it needs a content curation pipeline,
which is a growth/content-ops concern (candidate for `zuka-growth`), not a
backend ranking decision, and Options A+B already satisfy Vol 15 Ch 10's
never-blank rule without it.

**Performance budget:** the existing `discovery_feed()` migration already
carries a written constraint — "feed must render ≤ 3s on 3G" (CLAUDE.md
Africa-first, cited directly in
`20260630140004_fn_discovery_feed.sql`). This design keeps that budget as
the binding number rather than inventing a new one. **Page size: 20** —
this matches the current RPC's existing `p_limit_n` default exactly, so no
change is proposed here.

At MVP's real gathering volume, a `ST_DWithin` query against the
GIST-indexed `venue.geo` column stays well inside that budget without an
additional index. **Flag:** `gathering` itself has no geo index because it
has no geo column at all (see the reality check at the top of this doc) —
proximity necessarily joins through `venue`, so **gatherings with
`venue_id IS NULL` (digital-only, Vol 13 Ch 2) cannot be radius-filtered or
distance-ranked.** Recommendation: digital-only gatherings are always
included regardless of radius, in their own section, ranked by freshness
only. This is a design proposal, not a settled Bible position — flagged in
§6.

**Go Live during the 30s Cloudflare TTL (ADR-021):** ADR-021 already
specifies the mechanism — Realtime triggers a cache-purge webhook on
LIVE/ENDED transitions, bounding worst-case staleness to ~1-2s for exactly
that transition, while everything else rides the 30s TTL. This design adds
one client-side detail ADR-021 didn't need to cover: the client should hold
a lightweight Supabase Realtime subscription (locked stack, no new
dependency) scoped to gatherings currently in view, purely to flip a
gathering's badge to "LIVE" instantly and move it into the Live Now section
client-side — without re-fetching or re-ranking the whole feed. This is
separate from Vol 13 Ch 6's 5km push notification on Go Live, which is a
different, already-specified mechanism; this design doesn't touch it.

---

## 4. Empty State and First-Open

**First-open, no location permission granted:** show a geo-agnostic feed —
Live Now (citywide) first, then soonest-upcoming citywide, both ranked by
freshness only since proximity has nothing to compute against yet — with a
persistent, non-blocking inline prompt ("See what's near you — enable
location") below the fold. Never a gate: Vol 01 Ch 8 Proof 2 is zero-login,
zero-additional-actions, and a location permission modal on first open would
violate that proof's spirit even though it's a device permission, not a
login. This is DRL 0/rules-based per Ch 11 — no assumed taste, no ranking
that requires data the person hasn't generated yet.

**Zero results within radius for 48h:** apply Vol 15 Ch 10's pattern
literally — relax the most restrictive filter first (radius: 5km → city-wide
per §1's widen logic), and if still zero, surface the nearest active
neighbourhood by name using the Bible's own example copy pattern: *"Nothing
live in Ntinda right now. Kabalagala is 2km away and has 8 spots."* If
city-wide Kampala is genuinely quiet (the Tuesday-morning case), fall back to
a "quiet week" state. Draft copy (voice per Vol 01 Ch 9 — direct, warm, never
hype): *"Kampala's quiet right now. Check back soon, or see what's on this
weekend."* — this exact copy is a placeholder; final wording is
`zuka-designer`/`zuka-growth` territory, not settled here.

---

## 5. Deferred Decisions (with DRL activation triggers)

```
Relevance + Social ranking dimensions
  → DRL 3, after a person has 3-5 real check-ins (Vol 15 Ch 11/12)

Fairness-of-exposure floor (Ch 7)
  → proposed trigger: ≥20 concurrent discoverable gatherings across
    ≥10 distinct organisers (needs founder sign-off — see §6)

Client "search wider" gesture / user-driven dynamic radius
  → post-MVP client/UX work; server auto-widen covers MVP's never-blank need

Curated static "this weekend" seed list (offline Option C)
  → growth/content-ops concern; revisit only if Option A+B prove insufficient

Paid boost_modifier activation (Ch 8)
  → whenever a Campaign/monetisation RPC ships; cap logic unchanged

Quality ranking dimension
  → blocked on a founder decision (§6): add is_verified now, or accept
    zero-signal quality until a later session

Map clustering / heat map rendering (Vol 13 Ch 7)
  → City Pulse surface, not this ranked-list design
```

---

## 6. Open Questions for Founder

These are not invented answers — the Bible leaves them open, or this
session found a schema gap that changes the premise of a question the
session prompt assumed was settled:

1. **Quality has zero signal today**, not partial as assumed going in — no
   `is_verified` column exists on `venue` or `gathering`. Two paths: (a) add
   a minimal `is_verified BOOLEAN DEFAULT false` column now as a small S008
   schema addition, giving Quality a real (if crude) signal, or (b) accept a
   2-signal (proximity + freshness) ranking model at MVP and revisit Quality
   in a later session. This design doesn't pick for you — it changes S008's
   scope either way.
2. **Live Now as a separate section vs. a score multiplier** — this doc
   recommends the separate-section approach; confirm or override.
3. **Fairness-of-exposure activation trigger** — proposed at ≥20 concurrent
   gatherings / ≥10 organisers. Ch 7 names this an Architecture-Council-level
   governed trade-off, so the number itself needs your sign-off, not just
   mine.
4. **Digital-only gatherings and the radius filter** — proposed: always
   included, in their own section, ranked by freshness only, since they have
   no venue to measure distance from. Confirm this matches your intent for
   how digital/hybrid gatherings should surface.
5. **Server auto-widen vs. client "search wider" gesture** — recommended
   server-only at MVP; confirm you're comfortable deferring the client
   gesture rather than building both now.

---

## 7. S008 Implementation Scope (derived from this design)

**S008 must build:**
- `ST_DWithin` radius filter on `discovery_feed` (or a new
  `discovery_feed_nearby`, per the existing S008 prompt's own "judge which is
  the smaller diff" framing) — 5km default, server-side widen to 10km then
  city-wide when results < 5, inside one RPC call.
- `ST_Distance`-based proximity score + freshness score, composite
  `score = proximity × 0.55 + freshness × 0.45`, `ORDER BY` on it.
- A `is_live` flag (or equivalent) in the return shape so the client can
  section Live Now above the ranked composite list.
- Digital-only (`venue_id IS NULL`) gatherings always included regardless of
  radius, freshness-ranked, in their own section.
- Return shape carries enough for the client to render distance (`distance_km`,
  nullable for digital-only) and live/freshness badges.
- Whatever schema decision comes out of open question 1 (`is_verified` or
  not) — resolve before writing the ranking SQL, not after.

**S008 must NOT build:**
- Any relevance or social personalisation (DRL 3+, gated on 3-5 check-ins).
- Fairness-of-exposure floor enforcement (design exists in §2/§5; stays
  inactive until the founder-approved trigger fires).
- Client-side caching, the Realtime "Live Now" subscription, or Cloudflare
  Cache Rules config — those are client and production/infra work,
  referenced here for completeness but out of a backend session's scope.
- Quality-based ranking, unless open question 1 is resolved toward adding
  `is_verified`.
- The curated static offline seed list (Option C) — not in scope anywhere
  near S008.

---

*Design only — no SQL was written in this session. Subordinate to SOUL.md
and ZukaBibleV4. The city just came alive, and this is how it decides what
you see first.*
