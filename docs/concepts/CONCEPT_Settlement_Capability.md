# ZUKA — Concept Document
# Settlement Capability: Scan-to-Settle

> **Type:** Feature Concept (economic / payment-rails) · **Tier:** 2-adjacent, but **payment-rails gated**
> **Status:** **ADJUDICATED 2026-07-06 → ABSORBED.** Deep-brainstormed and triaged.
> The one buildable idea (payment-blind order fulfilment) moved to the Vendor
> Marketplace concept **§9B**; wallet / platform-split / payment-states were
> **dropped** (money-posture + locked-rule conflicts). This doc is **retained as
> provenance** of the original brain-dump — it is not a live proposal. Decision
> record: `docs/superpowers/specs/2026-07-06-settlement-triage-phase2-fulfilment-design.md`.
> **Authority:** Subordinate to ZukaBibleV4 and SOUL.md. A concept does not amend
> the Bible; it proposes, the ADR process disposes.
> **Provenance:** Externally-produced brain-dump pasted 2026-07-06 (titled
> *"ZUKA Settlement Capability: A Constitutional Audit & Implementation Proposal"*).
> Organized here for access + future reference. The source declared itself
> *"Approved for Implementation"* — **that self-approval is void** (concepts cannot
> ratify themselves; only the founder amends the Bible, via ADR). See §2.
> **Reviews required before ANY build:** `zuka-legal` + `zuka-security` lead
> (payment-rails gate: careful + freeze `payments/`), then `zuka-reality-checker`.
> **Shared entities:** builds on Smart Bucket (owner: `CONCEPT_Smart_Bucket_Reconciliation.md`),
> Scanner Pro (`CONCEPT_Scanner_Pro_Capability_Model.md`), the In-Event Vendor
> Marketplace (`CONCEPT_In_Event_Vendor_Marketplace.md`), and the commission-debt
> ledger shipped in S018.

---

## 1. The idea in one paragraph

Add a **Settlement capability** to Scanner Pro so an authorized participant can
verify an economic obligation at the "last meter" of an event and initiate its
settlement — for a food order, a service, a cash sale, or an external payment.
Settlement intents live in the same Smart Bucket as entry tokens; the scan
resolves the bucket, surfaces outstanding intents scoped to the scanner's
context, and the server (never the scanner) authorizes. The framing is
**intent-to-pay**: participation is verified first, money settles second.

---

## 2. Constitutional status & conflicts (read before referencing anything below)

This concept has good instincts (reuse Smart Bucket + Scanner Pro, append-only
event trail, context isolation, idempotency). But as pasted it **conflicts with
locked rules**. The conflicts are recorded here so the ideas can be referenced
without the defects being mistaken for approved design.

| # | Conflict | Locked rule | Required fix before build |
|---|----------|-------------|---------------------------|
| C1 | **ZUKA sits in the buyer→vendor money flow**; proposes Wallet settlement + a "Settlement Plan" that splits proceeds to a `platform` beneficiary. | Money posture / NPS Act 2020 (SOUL red line 4). Wallet is **frozen**; vendor order money is **vendor-direct**; Zuka is not an aggregator. The shipped `commission_debt_ledger` explicitly *"NEVER holds vendor money… Zuka does not sit in that flow at any point."* | **Drop Wallet + platform-split modes.** MVP-legal settlement is *recording only* (Cash / External / vendor-direct STK). `zuka-legal` + `zuka-security` must clear any money-flow change via ADR. |
| C2 | Money typed as `DECIMAL(12,2)`. | Money is **UGX integer minor units** (`BIGINT` everywhere in-repo: `price_ugx`, `amount_ugx`). | Use `BIGINT` minor UGX. |
| C3 | Non-canonical terminology: `events`, `persons`, `customer`, `attendee`. | ADR-009/010/011, Vol 03: **Gathering** not event, **Person** not user. | Rename to canonical entities. |
| C4 | In-place mutation: `UPDATE settlement_intents SET status=…, version=version+1`. | ADR-015 append-only; ADR-014 revocation is human. The shipped ledger got this right ("rows never updated after insert"). | State transitions **append** to an event table; the intent row is a projection, not a mutable record. |
| C5 | Self-declared *"Constitutional Volume: SETTLEMENT"* + *"Approved for Implementation."* | Concepts are design space; only an ADR graduates anything, only the founder amends the Bible. | Treat as a **proposal**. No constitutional volume exists or is implied. |
| C6 | §11 "Settlement Intelligence" (fraud ML, predictive settlement, reputation-based terms). | Three-axis readiness — immature data ships no intelligence (DRL-gated). | Register as **dormant**; not MVP. |
| C7 | RPCs carry defects: `WHERE … scanner_identity = scanner_identity` (param compared to itself → always true, auth bypass); no `auth.uid()` guard; auto-`EXECUTE` grant to `anon`/`authenticated` not revoked. | Trust-state / access RLS invariants; SECURITY DEFINER hygiene. | Rewrite RPCs; the SQL in §9 is **illustrative only**. |

Everything below §2 is **recorded content, not endorsed design**. SQL in §9 is
illustrative and known-defective per C2/C4/C7.

---

## 3. Reconciliation with what exists

```
ALREADY IN THE REPO (reuse, don't rebuild):
  - Smart Bucket as the universal credential            (Smart Bucket concept)
  - Scanner Pro capability model + scanner-session bind (Scanner Pro concept, S012+)
  - In-event vendor marketplace + attendance-gating     (Vendor Marketplace concept)
  - commission_debt_ledger — append-only B2B fee ledger (S018) — Zuka↔vendor ONLY,
    never buyer↔vendor money
  - Vendor credential + scan-time activation            (S019)
  - Ticket purchase / confirm / expire flow, Flutterwave (S007)

ADDED BY THIS CONCEPT (all NEW, all payment-rails gated):
  - A settlement-intent lifecycle + append-only settlement event trail
  - A SETTLEMENT scanner capability alongside Entry / Vendor / Security
  - Multi-modal settlement RECORDING (see §5, minus the frozen/aggregator modes)
```

The MVP-legal core is *recording that a settlement happened out-of-band*
(cash/external/vendor-direct), not Zuka moving money.

---

## 4. Settlement-intent lifecycle (proposed state machine)

```
INTENT_CREATED  ──▶ PENDING ──▶ AUTHORIZING ──▶ PROCESSING ──▶ COMPLETE
      │              │             │               │
      └─▶ CANCELLED  ├─▶ FAILED    ├─▶ FAILED       └─▶ FAILED
                     ├─▶ EXPIRED   ├─▶ EXPIRED
                     └─▶ CANCELLED └─▶ CANCELLED
  FAILED ──▶ PENDING (retry) | CANCELLED        COMPLETE ──▶ COMPLETE (idempotent)
```

| Stage | Trigger | State | Actor-facing |
|-------|---------|-------|--------------|
| Creation | order / service done / cash sale recorded | `INTENT_CREATED` | — |
| Pending | scanner scans the Smart Bucket | `PENDING` | "Settlement in progress" |
| Authorizing | provider prompt / confirmation | `AUTHORIZING` | customer confirms |
| Processing | provider confirmation received | `PROCESSING` | verify, update trail |
| Complete | settlement verified | `COMPLETE` | "✅ Fulfil order" |
| Failed / Expired / Cancelled | decline / timeout / cancel | terminal-ish | retry / fallback |

> Per **C4**, these are **appended events**, not mutations of one row.

---

## 5. Multi-modal settlement (with legality flags)

| Mode | Description | MVP status |
|------|-------------|-----------|
| **Cash** | Manual cash payment recorded | ✅ MVP-legal (recording only) |
| **External** | Paid outside ZUKA, recorded | ✅ MVP-legal (recording only) |
| **STK Push (vendor-direct)** | MoMo prompt settling **to the vendor's** collection, Zuka records | ⚠️ Only if vendor-direct; `zuka-legal` must confirm Zuka is not in the flow |
| **Immediate** | Pre-paid via ZUKA checkout, scan verifies | ⚠️ Overlaps existing ticket flow; scope carefully |
| **Wallet** | Deduct from ZUKA wallet | ❌ **BLOCKED** — wallet frozen (NPS Act) |
| **Balance-on-completion** | Deposit + balance | ❌ Deferred (custody-adjacent) |

---

## 6. Scanner workflow (proposed)

```
SCAN → RESOLUTION (server resolves bucket owner + intents, filtered to scanner
       context) → PRESENTATION (pending settlements) → ACTION SELECTION (mode)
     → INITIATION (server validates + routes) → CUSTOMER CONFIRMATION
     → CONFIRMATION (provider webhook) → FULFILMENT (vendor delivers, records)
```

The scanner has **no direct payment authority** — it verifies and displays; the
server authorizes. (Consistent with Scanner Pro's server-side-authority model.)

---

## 7. The intent-to-pay framing

| Traditional | Proposed ZUKA framing |
|-------------|------------------------|
| Order → Payment → Delivery | Order → Intent-to-Pay → Verification → Settlement → Fulfilment |
| Payment confirms existence | Participation confirms value |
| Money-first | Trust-first |

This framing is philosophically aligned with ZUKA; the **implementation** is what
must be constrained to the vendor-direct / no-custody posture (§2).

---

## 8. Proposed settlement principles (proposed, not constitutional)

1. Settlement follows participation.
2. Settlement is append-only (corrections are new events).
3. Settlement is verifiable / auditable.
4. Settlement is context-isolated (scanners see only their own intents).
5. Settlement is idempotent.
6. Settlement is capability-gated.
7. **Settlement never custodies funds** — and in ZUKA this is not optional "unless
   amended"; it is the NPS Act posture. Zuka records, it does not hold.
8. Settlement preserves trust (failures handled transparently).

---

## 9. Illustrative schema & RPCs (ILLUSTRATIVE ONLY — carries known defects)

> ⚠️ **Do not migrate this.** It violates C2 (`DECIMAL`), C3 (`events`/`persons`),
> C4 (in-place `UPDATE`), and C7 (RPC self-comparison / missing auth guard /
> auto-grant). Kept for reference of intent, not as a build artifact.

```sql
-- Settlement intent (projection) + append-only event trail.
-- FIXME(C2): amount must be BIGINT minor UGX, not DECIMAL(12,2).
-- FIXME(C3): events/persons → gathering/person canonical entities.
-- FIXME(C4): status must be derived from the event trail, not UPDATE-d in place.
CREATE TABLE settlement_intents (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    bucket_id UUID NOT NULL,
    vendor_id UUID NOT NULL,
    event_id UUID NOT NULL,            -- FIXME(C3): gathering_id
    amount DECIMAL(12,2) NOT NULL,     -- FIXME(C2): BIGINT amount_ugx
    currency VARCHAR(3) DEFAULT 'UGX',
    status VARCHAR(20) NOT NULL,       -- FIXME(C4): projection, not mutated
    settlement_mode VARCHAR(20) NOT NULL,
    payment_provider VARCHAR(50),
    payment_reference VARCHAR(100),
    created_at TIMESTAMPTZ DEFAULT now()
);

CREATE TABLE settlement_events (       -- append-only trail (the correct half)
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    settlement_intent_id UUID NOT NULL REFERENCES settlement_intents(id),
    event_type VARCHAR(50) NOT NULL,
    event_data JSONB NOT NULL,
    occurred_at TIMESTAMPTZ DEFAULT now()
);
```

State-transition validation, resolution/initiation RPCs, and the provider-webhook
handler from the source are preserved in git history of this brain-dump but are
**omitted as build artifacts** because they carry C7 (e.g.
`WHERE vc.vendor_id = (SELECT vendor_id FROM vendor_assignments WHERE scanner_identity = scanner_identity)`
— a param-compared-to-itself always-true predicate). Any real version is a fresh
`zuka-backend` deliverable under `zuka-security` review.

---

## 10. Security & privacy (proposed threat handling)

| Threat | Proposed mitigation |
|--------|---------------------|
| Duplicate settlement | idempotency + state validation |
| Unauthorized settlement | capability-gated access |
| Wrong bucket | bucket verification + PIN confirmation |
| Accidental scans | confirmation step + idempotency |
| Offline monetary op | explicit network requirement for money moves |

Privacy: context isolation (vendors see only their intents), minimal reveal to
scanner, append-only auditable trail, **no fund custody**.

---

## 11. Operational considerations (proposed)

- **Offline:** verification works offline; settlement requires connectivity.
- **Error recovery:** decline → retry/alt mode; timeout → backoff; duplicate scan
  → idempotent no-op; invalid bucket → refuse.
- **Reconciliation & health:** daily reconciliation report + settlement-health
  metrics (pending, stalled >5min, failure rate, avg settle time). *(Source SQL
  omitted — same illustrative caveat as §9.)*

---

## 12. Proposed roadmap (as-pasted — subject to reality-check)

`Phase 0` constitutional prep · `Phase 1` core schema/RPCs · `Phase 2` scanner
integration · `Phase 3` Flutterwave webhook + idempotency + reconciliation ·
`Phase 4` UI/UX · `Phase 5` testing + staged deploy.

> This 6-week roadmap assumes settlement is MVP. It is **not** yet established
> that it is — see §13.

---

## 13. MVP cut line (house-style requirement)

```
MVP-CANDIDATE (only if reality-checker + legal agree there's launch need):
  - Cash / External settlement RECORDING (no money movement, append-only)
  - SETTLEMENT capability on the existing scanner, context-isolated

DEFERRED (post-MVP, needs its own ADR):
  - Vendor-direct STK-push recording (legal clearance first)
  - Reconciliation dashboards

DORMANT (DRL-gated, do not build):
  - Wallet settlement (frozen), platform-split settlement plans (aggregator risk)
  - Settlement intelligence: fraud ML, predictive settlement, reputation terms
```

The honest default is that most of this is **post-MVP or dormant**. The three
sacred proofs (60-second Go Live, zero-login discovery, 3-tap ticket) do not
require it.

---

## 14. Open questions / required reviews

1. **Is settlement an MVP need at all, or vendor Phase-2?** (`zuka-reality-checker`)
2. **Which modes keep Zuka fully out of the money flow?** (`zuka-legal`, NPS Act)
3. **How does this relate to the existing `commission_debt_ledger`** — is a
   "settlement" here just the vendor-side mirror of a B2B fee accrual, or a
   distinct buyer-side record? (`zuka-backend` + `zuka-security`)
4. DPPA-2019: settlement records contain personal + transactional data — erasure
   posture must reconcile with `CONCEPT_EventStore_PII_Erasure.md`.

---

*Captured 2026-07-06. Subordinate to ZukaBibleV4. Companion: see
`CONCEPT_Commercial_And_Operational_Dimensions.md` for the pasted supplement
(CI/CD "Constitution Pipeline", monetization/economic model, vendor interaction
models).*
