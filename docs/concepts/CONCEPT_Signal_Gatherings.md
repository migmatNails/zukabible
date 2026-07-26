# ZUKA — Concept Document
# Signal Gatherings ("the city tells ZUKA before ZUKA is told")

> **Type:** Feature Concept & Design Brief · **Tier:** 2 (post-MVP engine; feed
> seam is MVP) · **Status:** Proposed (pre-build)
> **Authority:** Subordinate to ZukaBibleV4. This document explores a feature; it
> does not amend the Bible. If built, the canonical entities graduate into Vol 04
> (entities) / Vol 15 (discovery) via the normal ADR process — but NOT before, and
> NOT before `zuka-legal` clears the source posture.
> **Provenance:** ZUKA feed-system brainstorm, 2026-07-04 (founder idea: "a system
> that auto-updates itself about a gathering no user posted, from other platforms,
> with a weighted and measured impact"). Sits under the feed system specified in
> `docs/design/ZUKA_DESIGN_LIVING_DOC.md` §Feed System.
> **Working name:** Signal Gatherings (final naming → zuka-growth, Brand Vol 25).

---

## 1. The idea in one paragraph

ZUKA should know a gathering exists **before** any organiser posts it — by pulling
from where events already announce themselves (venue calendars, ticketing sites,
public event feeds) — and surface it in the feed **provisionally**: clearly marked
unconfirmed, ranked strictly beneath real human-published Gatherings, and unable
to distort the honest feed. A **Signal Gathering** is a *claim about the world*,
not a verified ZUKA Gathering. It earns its place two ways, both human-in-the-loop
(ZUKA's soul: systems flag, **people** verify — ADR-014): the real organiser
**claims** it (and it becomes a first-class Gathering), or people **corroborate**
it (confidence rises). If neither happens, its confidence **decays** and it fades.
This turns cold-start city coverage into a seeding + acquisition loop without ZUKA
ever asserting something it cannot stand behind.

## 2. Why it's constitutional (and where the lines are)

- **Human Participation Infrastructure.** A Signal is provisional until a human
  acts. ZUKA never presents unverified external data as truth.
- **Attention integrity (Vol 15).** A Signal **can never out-rank a live real
  Gathering.** It lives in a floored provenance tier; confidence orders cards only
  *within* that tier. This is the founder's "weighted and measured impact" made
  structural — the exact anti-drain posture Vol 15 Ch 8 already mandates for
  sponsored content, applied to provenance.
- **Terminology.** A Signal is a *Gathering*-shaped claim, not an "Event". A
  corroboration/comment is chatter, **not a Memory** (Memory ≠ Media, ADR-009).
- **DRL discipline.** Rules-based confidence + floor-ranking is **DRL-2** (MVP-safe
  *if* built). *Learning which sources to trust* is **DRL-3+** — dormant, deferred.
  The ingestion **engine itself is post-MVP**; only the feed **seam** is MVP.

## 3. Provenance as a first-class dimension (the general model)

Signal Gatherings are one value of a general axis the feed carries on every item:

```
provenance = { source, verified: bool, confidence_state }
  organiser-verified   real Gathering (organiser_id present, published)   — first-class
  external-unverified  Signal Gathering (no organiser, auto-sourced)      — floored tier
  [future] partner-fed / friend-recommended / AI-suggested                — same governed model
```

Everything below is the **external-unverified** instance of this model. Any future
content source enters under the *same* floor-ranking + confidence + graduation
rules — no new special-case. (See `ZUKA_DESIGN_LIVING_DOC.md` §Feed Architecture,
contract 5.)

## 4. New entities it adds (reuse first; these are the ONLY additions)

Reuses `gathering`, `venue`, `person`, `domain_events`, the discovery RPCs, Vol 15
ranking. Adds:

- **`signal_gathering`** — a provisional listing: `source_id`, external ref, parsed
  `{title, venue_guess, starts_at, url}`, `confidence` (stored numeric, **never
  client-exposed as a number** — surfaced as a state), `status` (SPOTTED ·
  CORROBORATED · CLAIMED · GRADUATED · DECAYED · SUPPRESSED), timestamps. **No
  organiser** — that absence is the whole point; it cannot be a `gathering` row
  (organiser FK) until claimed.
- **`signal_source`** — the registry of *permitted* sources: kind (ICS · venue-feed
  · API), base trust weight, `legal_clearance_ref` (a `zuka-legal` sign-off record),
  poll cadence, active flag. A source ingests **nothing** without a clearance ref.
- **`signal_corroboration`** — one row per corroboration: `signal_id`, actor
  (person or device-hash for anon), weight, `at`. Deduped/rate-limited per actor
  (Vol 15 Ch 9 anti-brigade).
- **Graduation link** — when claimed, a `signal_gathering` spawns a real `gathering`
  (organiser = the claimant, ownership proven) and records the lineage; the Signal
  moves to GRADUATED and leaves the floored tier.

## 5. The two graduation paths (human-in-the-loop)

- **Claim** — the organiser sees "your event is already on ZUKA — claim it in 60s."
  Ownership proof (domain/handle match to the source, or a code at the venue), then
  `create_gathering`-equivalent seeded from the parsed fields. **This is the growth
  loop**: ZUKA seeds the city; organisers arrive to claim their turf. High-stakes →
  always authenticated + proof.
- **Corroborate** — a one-tap "Real? / I'm going" raises confidence. Zero-login but
  **device-rate-limited**; a signed-in corroboration weighs more. Enough corroboration
  lifts a Signal's *visibility within its tier* — **never to verified status, never
  above a real Gathering.**

## 6. Confidence & decay — "weighted and measured impact"

```
confidence = base_source_weight
           + Σ corroboration_weight (deduped, fraud-discounted)
           − decay(time_to_start unconfirmed)        -- self-healing
seed floor: a fresh Signal enters LOW confidence, bottom of the floored tier.
decay:      as start-time nears with no claim/corroboration, confidence falls;
            past-start unconfirmed → DECAYED → drops from the feed.
source dies / event vanishes upstream → SUPPRESSED.
```

Confidence is **rules-based arithmetic (DRL-2)** at MVP — *not* a learned model.
It surfaces to clients only as a **human state** ("unconfirmed" · "a few people
confirm" · "widely confirmed"), never a number (trust is a state, never a number).

## 7. Source posture — opt-in/official feeds, NOT scraping (the legal spine)

**Hard rule: no HTML scraping.** Ingest only from sources that structurally permit
it and carry a `zuka-legal` clearance ref:

- **ICS / iCal** public calendar feeds a venue publishes.
- **Venue-submitted feeds** (a venue opts in — also the cleanest DPPA basis).
- **Official APIs** whose Terms explicitly allow this use.

`zuka-legal` must rule **per source**, before its first ingest, on: the platform's
**Terms of Service**; **content copyright**; **DPPA 2019** (ingesting other people's
personal data — lawful basis, minimisation, erasure); and **misinformation /
defamation exposure** (showing a wrong/cancelled event as real — mitigated by the
"unconfirmed" framing + decay + a report path, but counsel-reviewed). No source
goes live without this. (CLAUDE.md §8: legal output is an input for qualified
Ugandan counsel.)

## 8. Architecture — Supabase-native, ADR-gated

No new runtime dependency beyond a source integration, which is itself an **ADR**:

```
signal_source registry ──(pg_cron cadence)──▶ Edge Function puller (per source kind)
        │                                            │  parse → normalise → dedupe
        ▼                                            ▼
   pgmq ingest queue ───────────────────────▶ ingest worker (SECURITY DEFINER RPC)
                                                     │  upsert signal_gathering
                                                     ▼
                              append-only domain_events (ADR-015):
                              SignalSpotted · SignalCorroborated · SignalClaimed ·
                              SignalGraduated · SignalDecayed · SignalSuppressed
        prune sweep (pg_cron) for DECAYED/SUPPRESSED rows (cf. S009 pattern)
```

Pure Postgres + Edge Functions + pgmq + pg_cron. Every new `signal_source` kind =
a proposed ADR (the source, its ToS basis, its clearance ref).

## 9. MVP cut lines / phases

- **Phase 0 — feed seam (MVP, build with the feed):** the provenance dimension, the
  Signal card variant ("Unconfirmed · spotted on [source]"), the floored tier,
  confidence-as-state, and the **corroborate + claim action hooks wired to stubs**.
  The feed is provenance-aware; **no ingestion runs.**
- **Phase 1 — one cleared source (post-MVP, needs ADR + `zuka-legal`):** a single
  venue ICS/opt-in feed end-to-end; claim + corroborate live; rules-based confidence
  + decay; prune sweep.
- **Phase 2 — source registry + a few cleared sources.** Same governed model.
- **Phase 3 (DRL-3, parked):** learned per-source trust weighting, learned
  corroboration fraud-scoring. **Do not build ahead of the DRL-3 gate.**

## 10. Open questions

- Ownership proof for **Claim** — domain/handle match vs. a venue-issued code vs.
  both? (Anti-hijack: a bad actor must not claim someone else's event.)
- Anon corroboration weight & dedupe key — device-hash raises DPPA questions; can a
  fully-anon corroboration count at all, or only signed-in? (`zuka-legal`.)
- Where does the **first** cleared source come from — the 30-venue pre-launch
  venues opting in their own calendars? (Ties Signal seeding to `zuka-growth`.)
- Suppression authority — auto-SUPPRESS on upstream disappearance vs. human review
  (ADR-014 says revocation is human; is auto-decay a revocation? — argue not, it's a
  time-based confidence fall, like the S013 throttle auto-clear).

---

> **Signal Gatherings — Concept (Tier 2, feed seam MVP).** The city announces
> itself; ZUKA listens humbly — provisional, floored, decaying, claimable — and
> never says something it cannot stand behind. A proposal subordinate to
> ZukaBibleV4; graduates via `zuka-reality-checker` + ADR + `zuka-legal`.
