# CONCEPT · Event-Store PII Erasure (append-only ⋂ DPPA right-to-erasure)

**Type:** Architecture / Compliance concept
**Tier:** Foundational (touches the Event Store — every domain writes here)
**Status:** **Proposed** (design space; not law until an ADR disposes)
**Authority:** Subordinate to ZukaBibleV4. Feeds V4 via ADR. Does **not** amend
ADR-015, SOUL, or Vol 23 — it proposes how to *honour all three at once*.
**Provenance:** Authored by review **R002** auditing session **S002**
(`supabase/migrations/20260630120001_event_store.sql`, commit `2462c44`), on
2026-07-03. Evidence: `domain_events.payload/metadata` are `JSONB NOT NULL`; the
three append-only triggers block UPDATE/DELETE/TRUNCATE; Bible
`bible-v4/23_Legal_Compliance_Monetization.md:14` ("DATA IS SACRED… no deletion");
CLAUDE.md §8 (DPPA 2019 applies **from the first user**).

---

## 1 · The tension (why this exists)

Two ZUKA red lines point in opposite directions the moment personal data enters
an event payload:

- **ADR-015 / SOUL red line 3 — the Event Store is append-only.** UPDATE, DELETE,
  and TRUNCATE raise. Corrections *append* a compensating event; history is never
  rewritten. This is enforced structurally by triggers in S002's migration.
- **Uganda DPPA 2019 (CLAUDE.md §8) — data-subject erasure.** A Person may demand
  deletion of their personal data. DPPA applies from the first user, not at scale.

`domain_events.payload` and `.metadata` are free-form `JSONB`. As soon as domains
emit events about a Person (registration, ticketing, check-in, trust seeding),
personal data lands **inside immutable rows**. A literal erasure request then
cannot be honoured by deletion without violating ADR-015 — and cannot be refused
without violating DPPA. **Today there is no live breach** (no PII flows at S002),
but the collision must be resolved *before* Person data first enters an event —
i.e. before/at S003–S005 — not after.

## 2 · Design principle: keep PII **out** of the immutable row

The append-only guarantee is about **facts and their ordering**, not about being
the system of record for a Person's *content*. So: an event may record *that*
something happened and *to which aggregate*, while the erasable personal content
lives where it can be lawfully erased. Options, MVP-first:

1. **PII-by-reference (preferred MVP cut).** Event payloads carry
   `aggregate_id` / `person_id` references and non-identifying facts only — never
   raw name, phone, email, MoMo number. The mutable operational tables (e.g.
   `person`) hold the erasable PII, protected by RLS. Erasure = redact/delete in
   the operational table; the event row (a reference + fact) survives intact and
   ADR-015 holds. **Lowest cost, no crypto, ships with the schema.**
2. **Crypto-shredding (when payloads must embed PII).** Encrypt any PII-bearing
   field with a per-Person key; store keys in a mutable keyring. Erasure = destroy
   the key ("shred"); ciphertext remains immutable but becomes permanently
   undecipherable. Honours erasure *and* append-only. Heavier; defer past MVP.
3. **Pseudonymisation-at-write.** Events store a stable pseudonym; the
   pseudonym→identity map is erasable. Similar effect to (2) without encryption.

## 3 · MVP cut-line (what to actually do, and when)

- **Now / harden-before-PII (S003–S005):** adopt **Option 1** as an *invariant on
  what may be written to `domain_events`* — a documented "no raw PII in event
  payloads" rule, ideally guarded by a review checklist and, later, a test that
  scans payload keys against a PII denylist. This is the cheap, correct default.
- **Defer (post-MVP, metric/《feature》-triggered):** Options 2–3 only if a concrete
  event genuinely must embed PII that cannot be held by-reference.
- **Erasure runbook (legal, S-later):** define the operational flow — when a DPPA
  erasure request arrives, which tables are redacted, and the *compensating event*
  (`PersonDataErased`) appended to the store to record that erasure occurred
  (the fact of erasure is itself an immutable, non-PII event — ADR-015 preserved).

## 4 · New entities this adds (nothing redefined)

- A **write-time constraint/convention** on the existing `domain_events` table
  ("no raw PII in payload/metadata") — not a new table.
- A future `PersonDataErased` **event type** (compensating event) — additive.
- (Option 2 only) a mutable **key/pseudonym map** table — deferred.

References existing entities; does **not** redefine `domain_events`, `person`,
ADR-015, or the DPPA posture. Their owners remain the migrations and Vol 23.

## 5 · Disposition

This is a **proposal**. Before anything here becomes binding it must pass
`zuka-reality-checker` and be ratified by a founder **ADR** (candidate:
"ADR-025 — PII handling in the append-only Event Store"). Until then, treat
Option 1 as the *recommended default* to code against, because it costs nothing
and prevents the tension from ever materialising.

---
*R002 · one idea, one canonical home. The review report cites this doc; this doc's
provenance cites the review.*
