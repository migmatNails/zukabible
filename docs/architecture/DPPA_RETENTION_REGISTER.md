# ZUKA — DPPA Data-Retention Register

> **Type:** Compliance artifact (canonical). **Authority:** subordinate to
> ZukaBibleV4; owned jointly by Vol 23 (Legal, Compliance, Risk & Monetization)
> and the migrations that create each store. **Status:** living register.
>
> **What this is.** A single place that records, per data store, **which personal-
> data reference it retains, for how long, on what lawful basis, and how a DPPA
> erasure request is honoured against it.** It exists because ZUKA has two red
> lines that pull in opposite directions the moment a Person is referenced:
> **ADR-015** makes the Event Store (and some read-models) append-only, while
> **Uganda DPPA 2019** (CLAUDE.md §8) grants data-subject erasure **from the first
> user**. Where a store retains a Person reference permanently, that tension has to
> be *written down and reconciled*, not carried as tribal knowledge.
>
> **What this is NOT.** Not a legal conclusion and not a compliance certification.
> Every entry here is an **input for qualified Ugandan counsel** (Vol 23 Ch 5 —
> the founder's standing action item), never final advice. "Reconciled" below means
> *the engineering posture is coherent and defensible on its face* — counsel
> confirms whether it satisfies the DPPA.

---

## How to read an entry

Each entry answers the same five questions, so adding the next store is cheap:

| Field | Meaning |
|---|---|
| **Store** | The table / event / file that holds the reference (`file:line`). |
| **Retained personal-data reference** | *What* about a Person is kept — ideally a bare `person(id)` UUID (a pseudonym), never raw PII. |
| **Lawful basis** | Why it may be retained (consent, contract, legal obligation, legitimate interest — for counsel to confirm against the DPPA). |
| **Retention** | How long (fixed period, event-lifetime, or **permanent by design**). |
| **Erasure posture** | How a DPPA erasure request is honoured **without** violating the store's own invariant (append-only / `ON DELETE RESTRICT`). |

**The governing principle** (from `docs/concepts/CONCEPT_EventStore_PII_Erasure.md`,
Option 1 — *PII-by-reference*): immutable / restricted stores carry **references and
non-identifying facts only**; the erasable identifying PII lives in the mutable
operational table (`public.person`, RLS-protected). Erasure is then a **`person`-row
action** — redact/crypto-shred the identifying columns there — after which the
retained UUID is an **orphaned pseudonym**: it still proves *that* an event/consent
happened and *to which aggregate*, but no longer resolves to a living person's PII.
The immutable row survives intact and ADR-015 holds.

---

## Entries

### 1 · Vendor terms acceptance (the consent pair)

When an organiser's vendor affirmatively accepts per-event terms, ZUKA records the
consent in **two** places that both retain a Person reference permanently:

| Field | Value |
|---|---|
| **Store (a) — read-model** | `public.vendor_terms_acceptance.accepted_by_person_id` — `supabase/migrations/20260706100000_vendor_organiser_setup.sql:172-186` (FK at `:179`). |
| **Store (b) — immutable event** | `VendorTermsAccepted` DomainEvent in `domain_events`, emitted by `accept_vendor_terms()` — `supabase/migrations/20260706100001_fn_vendor_organiser_setup.sql:183-198` (payload `accepted_by` at `:195`, column `recorded_by` = same person UUID at `:198`). The migration header states the ADR-015-protected consent record is **this event, not the table** (`20260706100000_vendor_organiser_setup.sql:16-17, 38-45`). |
| **Retained personal-data reference** | A bare **`person(id)` UUID foreign key** (store a) / the same UUID in the event **twice** — `payload.accepted_by` **and** the `recorded_by` column (store b). **No raw PII** — no name, phone, email, or MoMo number is written into either store. |
| **Lawful basis** | **Retention purpose: proof-of-consent.** The record's whole purpose is to evidence *who agreed to what terms, when* — "updating the template never silently changes what a past vendor agreed to" (`CONCEPT`/DESIGN_Vendor_Marketplace Ch 3F). **Candidate DPPA lawful basis: legal obligation / legitimate interest** in retaining contractual-acceptance evidence (proof-of-consent is the *purpose*, not itself a statutory basis) — **for qualified counsel to confirm** against DPPA retention limits. Distinct from `person.consent_data_processing` (the DPPA processing-consent flag). |
| **Retention** | **Permanent, by design.** Store (a) pins `(template_id, terms_version)` and is INSERT-only by grant (explicit `REVOKE UPDATE, DELETE` — founder ruling, not convention); its FK is `accepted_by_person_id … REFERENCES public.person(id) ON DELETE RESTRICT`. Store (b) is ADR-015 append-only (no UPDATE/DELETE/TRUNCATE). |
| **Erasure posture** | **`person`-row PII action, not a row delete here.** `ON DELETE RESTRICT` is **intentional**: it forbids destroying the consent evidence by cascade. A DPPA erasure request is honoured per `CONCEPT_EventStore_PII_Erasure.md` **Option 1** — redact / erase the identifying columns in `public.person` (the exact mechanism — redaction vs. crypto-shred — is ratified by the future erasure ADR, not pre-picked here). The `accepted_by_person_id` UUID (store a) and the event UUIDs (store b) then survive as **orphaned pseudonyms**: the consent fact and its ordering are preserved (ADR-015), but the UUID no longer resolves to a living person's PII. **No schema change is needed for this posture to hold** — it is a property of keeping PII by reference. |

**Cross-references:** ADR-015 (append-only Event Store) ·
`docs/concepts/CONCEPT_EventStore_PII_Erasure.md` (the erasure pattern, Option 1) ·
Vol 23 Ch 4 (Three-Layer Data Protection Model) · Uganda DPPA 2019.

**Origin:** S020 `zuka-legal` HARD finding — the tension had no home to be recorded
in until this register (S043).

---

## Future entries (named, not yet recorded)

The register's format makes each of these a cheap add when its session comes. Listed
by name only — **not** audited or reconciled here (that is out of S043's scope):

- **Ticketing** — `ticket` / purchase records referencing the buyer `person(id)`.
- **B2B credential-fee collection** — the vendor-fee payer's PII (email / phone /
  name) forwarded to Flutterwave at `initiate-flutterwave-payment` for a
  `ticket_type='vendor'` charge. Distinct purpose from attendee ticketing: the
  payer is the **vendor Person** and the lawful basis is **contract-with-vendor**,
  not contract-with-attendee (S065 addendum §E,
  `ADR-026-COLLECTION-ADDENDUM-S065.md`). Minimisation posture (minimal fields, no
  raw-body logging — S007) already applies; entry recorded here so the differing
  lawful basis is explicit when reconciled.
- **Orders** — vendor-order records (Phase 2) referencing buyer/vendor `person(id)`.
- **Notifications** — delivery records referencing recipient contact (SMS/FCM).
- **Trust seeding** — `TrustProfileSeeded` and related events referencing `person(id)`.
- **Any other append-only aggregate** that emits an event carrying a `person_id`
  reference — the standing posture (Option 1) is expected to apply uniformly; each
  gets its own entry when built so the lawful basis and retention are explicit.

---

## What this register deliberately does NOT do (scope guard)

- It does **not** ratify the erasure *mechanism*. A `PersonDataErased` compensating
  event, the crypto-shred keyring, and an operational **erasure runbook** are a
  separate, larger session and a candidate **ADR** ("PII handling in the append-only
  Event Store") — see `CONCEPT_EventStore_PII_Erasure.md` §3, §5. This register only
  records the *tension and the intended reconciliation*.
- It does **not** assert DPPA compliance status. It surfaces retention facts for
  counsel; the legal conclusion is theirs (Vol 23 Ch 5).

---

*One store, one row; the reconciliation written down instead of remembered.*
*Subordinate to SOUL.md and ZukaBibleV4. Legal outputs are inputs for qualified*
*Ugandan counsel, never final advice (CLAUDE.md §8).*
