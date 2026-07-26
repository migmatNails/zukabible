# ZUKA — Concept Document
# Commercial & Operational Dimensions (Settlement supplement)

> **Type:** Mixed brain-dump (CI/CD ops · monetization strategy · vendor models)
> **Status:** **ADJUDICATED 2026-07-06.** Triaged: **Part I (CI/CD)** → parked as
> backlog fuel for the existing ADR-027 enforcement work (not a product concept);
> **Part II (monetization)** → reference-only, build nothing (splits/policy-engine/
> obligations-ledger are dormant — aggregator risk); **Part III (vendor models)** →
> absorbed into Vendor Marketplace Ch 3H (trackability spectrum). Retained as
> provenance. Decision record:
> `docs/superpowers/specs/2026-07-06-settlement-triage-phase2-fulfilment-design.md`.
> **Authority:** Subordinate to ZukaBibleV4 and SOUL.md. Proposes; the ADR
> process disposes.
> **Provenance:** Companion supplement to the Settlement brain-dump pasted
> 2026-07-06 (titled *"Chapter: Missing Constitutional Dimensions"*). Organized
> here for reference. **The source was truncated mid-Part III** (see §Part III).
> **Companion:** `CONCEPT_Settlement_Capability.md`.
> **Note on framing:** the source presents aspirational tables/pipelines (e.g. a
> `code_analysis` / `capability_registry` / `readiness_rules` SQL layer) **as if
> they exist**. They do not. Everything here is *proposed*, and several items
> conflict with locked rules — flagged inline.

---

## Contents
- **Part I** — CI/CD & the "Constitution Pipeline"
- **Part II** — Monetization & Economic Model
- **Part III** — Vendor Interaction Models *(truncated in source)*

---

# Part I — CI/CD & the "Constitution Pipeline"

## I.1 The idea

A CI/CD philosophy that asks *"does it violate architecture?"* before *"does it
compile?"* — a first-stage **Constitution Pipeline** that runs before build and
checks constitutional compliance.

```
Push → CONSTITUTION (ADR compliance, dormant-capability rules, naming, session
       metadata, readiness rules, doc/registry consistency, architecture/domain/
       AI drift, constitution verification)
     → BUILD (flutter analyze, format, lint, codegen, compile android/ios/web,
       unit tests)
     → BACKEND (fresh Postgres, supabase reset, apply migrations, SQL + RLS + RPC
       + edge-fn tests, perf smoke)
     → DEPLOY (integration, security scan, package, dev→staging→[manual gate]→prod,
       health checks, rollback-ready, post-deploy monitoring)
```

## I.2 Signature idea: constitutional drift checks

Proposed drift detectors (source expressed them as SQL over tables that **do not
exist yet** — treat as intent, not schema):
- **Architecture drift** — Flutter reaching past RPCs into tables; business logic
  in UI widgets.
- **Readiness drift** — a `DORMANT` capability suddenly used; readiness rules
  changed mid-session.
- **Documentation drift** — ADR references to nonexistent items; registries out of sync.
- **Domain drift** — e.g. `vendor_*` columns leaking into a `person` table.
- **AI drift** — generated code violating naming / layering / authority / security.

## I.3 Proposed implementation priority

`P1` ADR-027 enforcement (pre-commit hook + GitHub Actions + local constitution
verify) · `P2` Flutter pipeline · `P3` backend pipeline (auto supabase migrate +
SQL tests every push) · `P4` staging pipeline · `P5` production pipeline (manual
approval, post-deploy health, auto-rollback).

## I.4 Release philosophy

Release by **constitutional readiness**, not version numbers:
`Capability ready → Security reviewed → Tests passing → Constitution verified →
Operationally safe → DEPLOY`. ("Settlement capability ready", not "v1.2.3".)

## I.5 Constitutional notes

- ✅ **Aligned** with the existing ADR-027 enforcement direction (recent commit
  `9e50558` already references ADR-027 enforcement + a compendium brainstorm).
- ⚠️ **Forward-only migrations** are locked — a CI stage that "applies all
  migrations to a fresh DB" is fine; a **rollback** stage is not (correct forward).
- ⚠️ The drift-detector SQL implies a metadata/registry substrate that must be
  **built** before any of this is real; today it is a wishlist, not a pipeline.

---

# Part II — Monetization & Economic Model

## II.1 Core insight

> ZUKA monetizes **participation infrastructure**, not tickets. Platform revenue
> appears *last*, as an emergent property of participation.

```
Participation → Audience → Commerce → Trust → Opportunity → Revenue
```

This is consistent with SOUL / the "participation-first" identity.

## II.2 The seven monetization layers

| # | Layer | Model | Priority |
|---|-------|-------|----------|
| 1 | Ticketing commission | ~10% via Flutterwave split | MVP essential |
| 2 | Vendor monetization | commission on vendor sales | High |
| 3 | Organizer tools | SaaS (analytics/reports/marketing) | Medium |
| 4 | Discovery | featured/sponsored listings | Medium |
| 5 | Business services | venues/security/photography etc. | Medium |
| 6 | API economy | usage fees | Long-term |
| 7 | Business intelligence | SaaS insights | Long-term |

## II.3 Stakeholder value framework

Every stakeholder must receive value — Consumer (discovery, safety, memories,
identity), Organizer (sales, audience, analytics, reputation), Vendor (customers,
orders, exposure, repeat business), Venue (bookings, traffic, revenue), Community
(culture, employment, economic activity), Platform (commission, subscriptions,
advertising, services, data products). Value must **precede** capture.

## II.4 Economic-constitution principles (proposed, not constitutional)

Value creation → exchange (fair/transparent/voluntary) → preservation (trust as
currency) → distribution (to all who contributed) → capture (fair share) → **the
participation principle: money follows participation, participation never follows
money.**

## II.5 Payment-flow architecture (the constitutionally-correct half)

```
Customer → Flutterwave checkout → Flutterwave SPLITS:
   ├─ Organizer → e.g. UGX 90,000 (direct settlement)
   └─ ZUKA      → e.g. UGX 10,000 (commission)
Why: ZUKA never becomes a bank; faster organizer settlement; less regulatory exposure.
```

✅ This is aligned with the shipped posture (Flutterwave split, S007) and the
`commission_debt_ledger` (S018).

## II.6 Settlement Plans & Commission Policy Engine (proposed — flagged)

- **Settlement Plans** — configurable beneficiary splits per gathering
  (organizer / vendor / venue / platform / partner, with %, priority, conditions)
  instead of hardcoded commissions.
- **Commission Policy Engine** — rule-based policies (percentage / flat / hybrid /
  subscription / dynamic) with conditions + priority.

> ⚠️ **Conflict flag:** a Settlement Plan that routes **vendor or buyer money**
> through a Zuka-defined split re-inserts Zuka into the money flow (NPS Act red
> line). This is only legal where it configures **Zuka's own commission/B2B fee**
> (the `commission_debt_ledger` domain), not where Zuka disburses others' money.
> `zuka-legal` gate. Money must be typed `BIGINT` minor UGX, not `DECIMAL`.

## II.7 Commercial Obligations Ledger (proposed evolution)

Proposes evolving the shipped `commission_debt_ledger` into a broader
**Commercial Obligations Ledger** (platform commission, venue/organizer revenue
share, vendor subscription, advertising, sponsorship, promo credits, penalties,
incentives).

> ⚠️ Big architectural expansion. Must stay **append-only** (ADR-015) and must not
> become a fund-custody surface. Reality-check hard against MVP before touching
> the S018 ledger — it is deliberately narrow ("Zuka↔vendor B2B fee only").

## II.8 Constitutional notes

- ✅ Participation-first economics align with identity.
- ⚠️ Layers 3–7 are largely **dormant/post-MVP**; MVP is layers 1–2 only.
- ⚠️ Any split/plan/policy engine touching third-party money is payment-rails
  gated and DPPA-relevant.

---

# Part III — Vendor Interaction Models *(source truncated here)*

> ⚠️ **The pasted source was cut off inside this part** (message exceeded its
> length limit). Captured below is only what survived; re-paste the remainder to
> complete this section.

## III.1 The three vendor types

| Type | Examples | Characteristics | Flow |
|------|----------|-----------------|------|
| **Product vendors** | food, drinks, merch, crafts, books | orderable digitally, trackable inventory, standard checkout | Customer → order → vendor → payment → delivery |
| **Service vendors** | face painting, makeup, henna, braiding, massage, photography | physical, wait time, negotiated/customized, no inventory | walk-up → sit → service → pay → complete |
| **Free-interaction vendors** | political campaign, NGO, university admissions, tourism info | no transaction, lead-gen/info, different metrics | discovery → interaction → lead capture → follow-up |

## III.2 Interaction-model taxonomy (partial — truncated)

| Model | Examples | Value ZUKA provides |
|-------|----------|---------------------|
| Product commerce | food, drinks, merch | ordering, payment, inventory, fulfilment |
| Queue-based service | face painting, makeup, massage | queue mgmt, wait estimates, reviews |
| Appointment service | tattoo, stylist | booking, deposits, scheduling |
| Walk-up service | shoe cleaning, quick repairs | discovery, reviews, payment recording |
| Experience | VR booth, gaming zone | capacity mgmt, ticket validation, analytics |
| Lead generation | universities, NGOs, brands | QR capture, lead mgmt, engagement analytics |
| Information | tourism, government services | *(truncated in source)* |

## III.3 Constitutional notes

- This overlaps heavily with the existing **In-Event Vendor Marketplace** concept
  — reconcile there, don't fork a parallel vendor taxonomy.
- Free-interaction / lead-gen vendors touch **personal data capture** → DPPA-2019
  consent obligations from the first record.

---

## Cross-cutting: what to do with this supplement

1. **Part I (CI/CD)** — feed into the existing ADR-027 enforcement work as
   backlog ideas; build the metadata substrate before claiming a "pipeline".
2. **Part II (monetization)** — mostly *strategy reference*; only the Flutterwave
   split (II.5) is shipped. Splits/policy/ledger-expansion are ADR + legal work.
3. **Part III (vendor models)** — merge into `CONCEPT_In_Event_Vendor_Marketplace.md`
   rather than standing alone; re-paste the truncated tail first.

---

*Captured 2026-07-06. Subordinate to ZukaBibleV4. Companion:
`CONCEPT_Settlement_Capability.md`.*
