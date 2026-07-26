# ZUKA — Concept Document
# Venue Knowledge Graph & Place Claiming ("ZUKA knows *where*, and owns only what it earns")

> **Type:** Feature Concept & Design Brief · **Tier:** 2 (post-MVP graph; a thin
> curate + Venue-Editor-claim slice is MVP-adjacent) · **Status:** Proposed —
> **PARKED** (reframe accepted 2026-07-05; **no build session queued** — deferred
> behind the vendor/product pivot).
> **Authority:** Subordinate to ZukaBibleV4. This document explores a feature; it
> does **not** amend the Bible and it is **not** a new "Place OS" or a 26th volume
> (V4 is a fixed 25). `venue` is already a **Tier-1 operational entity** (Vol 04
> Ch2); re-tiering it to a Tier-0 OS would be a founder ADR against Vol 04, not a
> concept's prerogative. If built, the canonical result graduates into Vol 04
> (entities) / Vol 15 (discovery) via the normal ADR process — and **not** before
> `zuka-legal` clears the source posture.
> **Shared entities (do not redefine — reference the owner):** the
> provenance / confidence-as-state / decay / claim-graduation model is **owned by
> [`CONCEPT_Signal_Gatherings.md`](CONCEPT_Signal_Gatherings.md)**. This concept is
> that same model applied to **places** instead of gatherings, and reuses its
> `signal_source` registry + `legal_clearance_ref` gate wholesale. The Venue
> Historian timeline is **Memory OS** (Vol 10, ADR-009); "organizer history" /
> place reputation is **Trust OS** (Vols 08–12); geo discovery is **Vol 15 +
> PostGIS**. None of these are redefined here.
> **Provenance:** ZUKA nine-agent **Place Intelligence brainstorm, 2026-07-05**
> (founder idea: a multi-source "Place Intelligence System" / "Place OS" that
> auto-discovers and syncs venues and builds proprietary "gathering intelligence"
> on top). Routed through all nine ZUKA agents (reality-checker · reviewer · legal ·
> researcher · backend · security · designer · growth · tester); they converged
> unanimously on the reframe below. Evidence: `supabase/migrations/20260630130001_venue.sql`
> (+ `20260630160003_rls_venue.sql`), `supabase/migrations/20260701090002_fn_discovery_feed_nearby.sql`.
> **Working name:** Venue Knowledge Graph (final naming → zuka-growth, Brand Vol 25).

---

## 1. The idea in one paragraph

ZUKA should understand *where* participation happens as richly as it understands
*who* and *what* — a canonical, de-duplicated record of every real Kampala venue,
discoverable, claimable by its owner, and accruing history as gatherings happen
there. The founder's instinct — **multi-source, never a single provider; ZUKA owns
its own understanding, not Google's** — is correct and worth building toward. But
it must be reframed on four hard constraints the agents surfaced (§10): ZUKA cannot
legally *own* a graph built from Google/OSM content; "gathering intelligence"
(busy hours, demographics, repeat-visitors) is DRL-1-forbidden prediction on data
that does not exist yet; the graph's core invariant (dedup) is un-testable without
a labelled corpus that does not exist; and "claim → sell tickets" mints financial
authority over an unowned record. The reframe: **a live Discovery Layer that never
persists third-party content, and an owned Participation Graph built only from what
ZUKA can stand behind — steward contributions and ZUKA's own first-party
participation data — keyed by the `venue.id` that already exists.**

## 2. Why it's constitutional (and where the lines are)

- **Extend, don't invent.** `venue.id` is already the canonical UUID — the FK target
  of `gathering`, the discovery RPCs, and RLS. The whole graph is an **additive
  extension** of that Tier-1 entity (Vol 04 Ch2), not a new OS. ADR-016: an "OS" in
  ZUKA is a bounded schema/module, never a deployable service — no extraction
  trigger is cited or implied.
- **Own only what you earn (the legal spine, §7).** Third-party map data is a
  *display layer*, never a *storable asset*. The proprietary graph is built from
  **steward-contributed** + **ZUKA-first-party** data only.
- **Readiness gates intelligence.** "Gathering intelligence" (busy hours, average
  age, repeat-visitor patterns, "good for birthdays") is prediction/profiling —
  **DRL-1 forbidden**, and DPPA-2019-exposed. It is **dormant** (Vol 24), not MVP.
  Immature data ships no intelligence.
- **Terminology.** A `venue` is a place; its accrued timeline is **Memory**, not
  Media (ADR-009). Any "place reputation" is a **state, never a number** and is an
  RLS-non-exposure surface (§6, §8) — the same invariant as raw trust dimensions.
- **Revocation is human (ADR-014).** Claim → steward promotion, contested-claim
  resolution, de-claiming, and venue-record suppression are all human decisions,
  logged append-only (ADR-015). Auto-import may *create*; only a human *promotes*.

## 3. The two layers (the legal reframe — hold this line)

The single most important structural decision, from the `zuka-legal` lens:

```
┌─ Discovery Layer  (LIVE, never persisted) ─────────────────────────────┐
│  Google Places, OSM tiles, directories used only to HELP a human find   │
│  and place a venue. Google: only place_id storable; lat/lng ≤30 days;    │
│  NEVER rendered on the Mapbox map. OSM: ODbL — attribution + separable.  │
│  Nothing here becomes a source of record. It is a picker, not a store.   │
└─────────────────────────────────────────────────────────────────────────┘
                                   │  a human confirms / places a venue
                                   ▼
┌─ Participation Graph  (OWNED, persisted, ZUKA's proprietary asset) ─────┐
│  Built ONLY from: steward-contributed venue facts + ZUKA's own          │
│  first-party participation (gatherings held, check-ins, ticket scans).  │
│  Keyed by venue.id. This — and only this — is the graph ZUKA owns.      │
└─────────────────────────────────────────────────────────────────────────┘
```

"Google helps you discover; ZUKA builds its own understanding" survives — but
"builds its own understanding" means **from ZUKA's own participation data**, not
from persisting Google's. That is the only version that is both the founder's
intent and legally defensible.

## 4. New entities it adds (reuse first; these are the ONLY additions)

Reuses `venue`, `gathering`, `person`, `domain_events`, the discovery RPCs +
PostGIS (Vol 15), `signal_source` + `legal_clearance_ref` (Signal Gatherings),
Smile Identity (already in stack). Adds:

- **`venue_source`** — multi-source provenance satellite, one row per
  `(source, source_id)`: `venue_id` (always points at the **canonical**, unmerged
  row), `source` (`owner`·`user`·`registry`·`osm`·`google` — enum-checked),
  `source_id` (external stable ref — `place_id`, OSM node/way id, reg no),
  `source_payload` JSONB (minimised, DPPA), `confidence` (**server-only, never
  client-exposed as a number**), `last_synced`. `UNIQUE (source, source_id)`.
- **`venue.merged_into_id`** — a new nullable self-FK on `venue`. Non-null ⇒ this
  row is a **tombstone that repoints** to the canonical winner. Merges are
  **soft** (repoint, never `DELETE`) so cached FKs / deep links / discovery caches
  never dangle (ADR-015 append posture). Plus a **`pg_trgm` GIN index** on
  `venue.name` for candidate matching.
- **`venue_claim`** — the claim state machine + audit:
  `UNCLAIMED → PENDING → VENUE_EDITOR → VENUE_STEWARD_COMMERCE → REVOKED`
  (see §5). Every transition emits an append-only `domain_event`.
- **Two roles, never auto-coupled (the security split):** **Venue Editor** (low
  bar — a verified Person may correct listing facts) and **Venue
  Steward–Commerce** (high bar — may publish gatherings + sell tickets + manage
  staff). These are distinct grants behind distinct gates.

## 5. Claiming — two independently-gated tiers (never one bundle)

`zuka-security` verdict: the danger is not *claiming*, it is claiming that
auto-confers the right to **collect money** in a venue's name (payout is
vendor-direct — an impostor's Flutterwave account, un-clawable). So the grant is
split, and the second tier is dormant behind a hard bar:

- **Venue Editor** (ship-first, MVP-adjacent): claimant is a **verified Person**
  (Smile Identity) → may correct address/hours/photos of a venue. Low blast radius;
  human-reviewed (ADR-014). Auto-imported records are **quarantined** —
  un-claimable and un-publishable — until a human promotes them (untrusted external
  data, CLAUDE.md §7).
- **Venue Steward–Commerce** (dormant until its gate is met): requires **KYC +
  out-of-band proof-of-control** (a channel the attacker cannot self-issue — a
  callback to a registry/listed number, or a code delivered to the physical venue;
  a claimant-supplied email/URL does **not** count) **+ human approval + an
  independently-verified payout account** (payout destination verified *against the
  venue*, not freely set). Claim-velocity limits per Person. Ticket money stays
  **vendor-direct** (NPS posture) so a bad claimant can never route funds through
  ZUKA. De-claiming is human **and** carries a defined incident path (funds already
  routed, attendee notification) — revocation stops future harm, not past.

Presentation (`zuka-designer` / `zuka-growth`): the claim entry is a **quiet**
line ("Manage this place?" / "This is my venue"), never scarcity framing ("claim
before someone else"). An unclaimed page is **passive and neutral** — no reviews,
no ratings, no ticketing, no implied endorsement, labelled "Not yet claimed" — with
a **frictionless one-tap "Remove this listing"** (Vol 16 anti-obstacle; honoured
instantly).

## 6. Dedup, merge & precedence (the un-testable core — gate it)

- **Candidate match** = `pg_trgm` fuzzy name (`similarity > ~0.4`) **AND** PostGIS
  `ST_DWithin(geo, incoming, ~150m)`. **Both axes required** — name-only merges
  "Cafe Javas" branches city-wide; geo-only merges a mall food court.
- **Auto-ingest may freely create + attach sources** (low stakes). A **merge is
  soft and human-gated** — sets `merged_into_id` on the loser (row retained,
  sources repoint) only above a high confidence bar *or* on human confirm (ADR-014).
  Reads resolve `merged_into_id` transitively to the live canonical.
- **Field precedence** is a bounded server-side function:
  `owner > user > registry > google > osm`; a low-trust auto-source **never**
  overwrites a human-set field. Provenance is additive — `venue` holds chosen
  canonical values, `venue_source` holds every source's claim.
- **The gate (`zuka-tester`):** dedup false-merge/false-split is a
  precision/recall problem, not a boolean — you cannot `RAISE EXCEPTION` your way
  to correctness. **No auto-dedup ships without a labelled Kampala venue corpus +
  agreed precision/recall thresholds.** "No corpus" is an automatic block on the
  graph. Cheap-to-verify now (claim authorization, steward RLS scope,
  non-exposure) vs. expensive (dedup, precedence, multi-source replay) — the fact
  that the *hardest* invariant is the *core* is the over-build tell.

## 7. Source posture — the legal spine (per-source, `zuka-legal`-gated)

Reuses Signal Gatherings' **no-scraping** rule and per-source `legal_clearance_ref`.
Storability verdicts (from the `zuka-legal` + `zuka-researcher` lenses):

| Source | Verdict |
|---|---|
| **Google Places** | **Live-fetch only, never persist.** Only `place_id` storable; lat/lng ≤30 days; **never** render on the Mapbox map (ToS forbids non-Google-map display + caching + competing-DB). Rich but unusable as a source of record. |
| **OpenStreetMap** | The only source both **licence-clean and Mapbox-native** (Mapbox tiles are OSM/ODbL). Persistable **only** under ODbL — attribution + share-alike; keep it a **separable** layer, not fused into the proprietary asset. Decent for landmarks/worship/schools/hotels; sparse/stale for bars/clubs/halls. |
| **Business/tourism directories** (URSB, UTB) | **Do not scrape/persist** absent written permission — ToS/DB-rights + PII. Weak for discovery anyway (URSB = names, no GPS; UTB = ~800 accommodation-only, no GPS). |
| **Steward-submitted** | **Safe to own** — contributed under ZUKA ToS with a data-licence grant. |
| **ZUKA first-party** (gatherings, check-ins, scans) | **Safe to own** — the only genuinely proprietary substrate; still DPPA-bound (consent, rights, no unlawful profiling). |

`zuka-legal` rules **per source before its first ingest** on ToS, copyright/DB
rights, DPPA-2019 (venue-owner PII on import; attendee profiling), and the Mapbox
rendering constraint. **Counsel-reviewed** (Google ToS interpretation, ODbL "made
available" trigger for a multi-tenant app, DPPA profiling lawful basis).

## 8. Architecture — Supabase-native, ADR-gated

No new runtime dependency beyond a source connector, which is itself an **ADR**
(source + ToS basis + clearance ref — exactly the Signal Gatherings pattern):

```
signal_source registry ─(pg_cron cadence)─▶ Edge Function puller (per source kind)
   (SHARED with Signal Gatherings —             │  fetch → normalise to a common
    a venue place-record and a venue            ▼  PlaceClaim shape → enqueue
    calendar-feed are two facets of        pgmq 'venue_ingest' queue
    ONE permitted-source list)                  │
                                                ▼
                        ingest worker = SECURITY DEFINER RPC match_or_create_venue()
                                pg_trgm + PostGIS candidate search → upsert
                                venue_source → maybe create venue (never auto-merge)
                                                │
                                                ▼
                     append-only domain_events (ADR-015):
                     VenueSpotted · VenueSourceAttached · VenueMerged ·
                     VenueClaimed · VenueEditorGranted · VenueStewardGranted ·
                     VenueFieldPromoted · VenueDeclaimed
   (Outbox: venue/venue_source write + event co-commit in one tx — Vol 20)
```

Pure Postgres + PostGIS + pg_trgm + Edge Functions + pgmq + pg_cron. Trust-state
non-exposure holds: `confidence`, any "place reputation", and repeat-visitor
rollups are **readable by no client role** (incl. stewards) — a steward is a client
role, not a privileged analyst; per-venue "who came back" is a de-anonymisation
engine (k-anonymity k≥50 + DP egress question), so it stays server-side and, at
MVP, **does not exist**.

## 9. MVP cut lines / phases — with explicit activation triggers

- **Phase 0 — MVP-adjacent slice (ship if/when scheduled):** hand-curate ~30
  (up to 60–80) real Kampala venues **directly on Mapbox** (a half-day, higher
  quality than any import) + a `source` provenance tag + **Venue Editor** claiming
  on the existing `venue` table. Optional **one-time OSM extract** (Geofabrik/
  MapTiler Uganda) purely as a manual-entry **pick-list** — the only Mapbox-licence-
  compatible source; human-confirmed before canonical. **No live sync. Google out
  of the persistent store entirely.** UX: a **place sheet** (venue name on a
  gathering card → bottom sheet: "what's on here + directions"), **not** a rival
  browse mode; a venue with zero live/upcoming gatherings does not surface.
- **Phase 1 — one cleared connector (post-MVP · ADR + `zuka-legal`):** a single
  OSM/opt-in connector end-to-end; `venue_source` provenance; create-only (no
  auto-merge). **Trigger:** self-serve organiser onboarding begins to strain manual
  curation.
- **Phase 2 — dedup + soft merge (dormant):** the `pg_trgm`+PostGIS matcher and
  human-gated `merged_into_id` merge. **Trigger (hard gate):** a **labelled Kampala
  venue dedup corpus with agreed precision/recall thresholds** exists (§6). No
  corpus, no dedup.
- **Phase 3 — Venue Steward–Commerce (dormant):** claim→sell-tickets behind the
  full §5 bar. **Trigger:** the payments identity/proof-of-control + payout-
  verification machinery is specced and `zuka-security`-reviewed.
- **Phase 4 — Venue Historian + gathering intelligence (dormant, DRL-gated):**
  the timeline (Memory OS projection over `domain_events`) and any busy-hours/
  demographic/repeat-visitor intelligence. **Trigger:** **DRL-2+** *and* a
  `zuka-legal` DPPA profiling review. **Do not build ahead of this gate** — it is
  the DRL-1 violation the whole reframe exists to avoid.

## 10. The four hard walls (why the reframe, in one place)

1. **Legal** — you cannot *own* a graph built from Google Places (persistence/
   non-Google-map/competing-DB ToS) or OSM (ODbL share-alike infects it). → §3, §7.
2. **Readiness** — "gathering intelligence" is prediction on non-existent data:
   DRL-1-forbidden + DPPA-exposed. → §2, Phase 4.
3. **Verification** — dedup (the graph's core) is un-testable without a labelled
   corpus that does not exist → make the corpus the entry gate. → §6, Phase 2.
4. **Security** — claim-jacking mints vendor-direct financial authority → split
   Editor vs Steward-Commerce; quarantine imports. → §5.

## 11. Open questions

- **Out-of-band proof-of-control** for Steward-Commerce — registry callback vs.
  venue-delivered code vs. both? (Anti-hijack: an impostor must not sell tickets in
  a real venue's name.) → `zuka-security` + payments spec.
- **OSM ODbL "made available" trigger** — does a multi-tenant app exposing venue
  records count as distributing a derivative DB, forcing share-alike on the whole
  graph? → counsel. (Drives whether OSM stays a separable layer or is avoided.)
- **The labelled dedup corpus** — who builds it, what's the ground-truth source,
  what precision/recall gate ships it? → `zuka-tester` + `zuka-researcher`.
- **First curated set → Signal Gatherings seeding** — do the 30 pre-launch venues'
  own opt-in calendars become the first `signal_source`, tying place-claiming to the
  Signal growth loop? → `zuka-growth`.
- **De-claim incident path** — beyond human revocation (ADR-014), the defined
  remediation when a bad steward already sold tickets (money routed, attendees
  notified). → `zuka-security` + `zuka-legal`.

---

> **Venue Knowledge Graph — Concept (Tier 2, PARKED).** ZUKA learns *where* the
> city gathers — but owns only what it earns: its own participation, and what
> stewards give it. Third-party maps help people find places; they are never the
> store. A proposal subordinate to ZukaBibleV4; it reuses
> [`CONCEPT_Signal_Gatherings.md`](CONCEPT_Signal_Gatherings.md)'s provenance model,
> extends `venue` (Vol 04 Ch2), and graduates — if ever — via `zuka-reality-checker`
> + ADR + `zuka-legal`. Not a new OS. Not Volume XXXVI.
