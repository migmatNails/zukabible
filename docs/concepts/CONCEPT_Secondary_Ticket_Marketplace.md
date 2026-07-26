# ZUKA — Concept Document
# Secondary Ticket Marketplace: Verified Ownership Exchange

> **Type:** Feature Concept (commercial / ticketing + payment-rails) · **Tier:** 2
> **Status:** **Proposed — reality-checked + architecture-reviewed + security-audited
> + legal-reviewed 2026-07-09 (verdicts: TRIM · CONCERNS→resolved · risk LOW ·
> money-posture NOT self-clearing).** Concept-capture, no build authorized.
> `zuka-reviewer` confirmed the
> specified-vs-built table byte-accurate and every phase correctly axis-gated (fixed
> the two it raised — an ADR-010 "user"→"Person" slip and the "Flutterwave split"
> merchant-of-record ambiguity, §5). `zuka-security` rated live risk **LOW** (unbuilt,
> `transfer_policy = none` default) and its 2C/3H/2M/1L findings are captured in **§11**
> as binding requirements for the eventual build. `zuka-legal` (§12) answered the §9(b)
> money question: **seller-direct is necessary but NOT sufficient** — market
> *facilitation* + a per-transaction fee may trigger NPS-Act obligations regardless of
> custody, and merchant-of-record may be unachievable for peer sellers; 5 hard
> preconditions now gate ADR-029 (input for counsel, not legal advice).
> **Correction from the first draft:** the free-transfer engine
> is **specified in Vol 14 but unbuilt** (no transfer RPC, no transfer events,
> `TRANSFERRING` is a bare CHECK value) — so the free-transfer slice is a full
> subsystem, **post-launch + demand-gated, not MVP** (§2a, §4). The **paid**
> marketplace stays **dormant** behind ADR-029 + `zuka-legal` (§5).
> **Authority:** Subordinate to ZukaBibleV4 and SOUL.md. A concept does not amend
> the Bible; it proposes, the ADR process disposes.
> **Provenance:** Founder-directed brainstorm, 2026-07-09 (Ying). Consolidated from
> a pasted "platform-managed secondary marketplace" design, reconciled against the
> constitution in this session (`superpowers:brainstorming`). Design record:
> `docs/superpowers/specs/2026-07-09-secondary-ticket-marketplace-design.md` (this
> concept is the canonical home; the spec is the brainstorm trail).
> **Reviews required before ANY build:** the money slice is `payment_rails` —
> `zuka-legal` + `zuka-security` lead (careful + freeze `payments/`), then
> `zuka-reality-checker`. The free-transfer MVP slice is `ticketing` —
> `zuka-backend` + `zuka-tester`, `zuka-reviewer` on the schema change.
> **Shared entities (referenced, never redefined here):** the ticket lifecycle +
> QR token + provenance doctrine (owner: **Bible Vol 14**, `14_Ticketing_Commerce.md`);
> Scanner Pro (`CONCEPT_Scanner_Pro_Capability_Model.md`); Smart Bucket
> (`CONCEPT_Smart_Bucket_Reconciliation.md`); the money-posture ruling + payment-blind
> fulfilment (`CONCEPT_Settlement_Capability.md` → Vendor Marketplace §9B); the
> append-only ownership chain (ADR-015); PII-by-reference in events
> (`CONCEPT_EventStore_PII_Erasure.md`).

---

## 1. The idea in one paragraph

A **platform-managed secondary marketplace** that lets ticket ownership move safely
after the initial sale, under organizer control. The thesis:

> **ZUKA never resells screenshots or QR codes; it transfers verified digital
> ownership.**

A seller never shares a screenshot, sends a QR image, or exchanges money directly
with a buyer. Instead ownership is reassigned inside ZUKA, a fresh access
credential is issued, the previous credential is permanently revoked, and (when the
paid path is eventually activated) money moves **seller-direct**, never through a
ZUKA-held account. Every transfer is permanently recorded as an immutable chain.
This removes the fraud vectors of social-media/WhatsApp resale without ZUKA
becoming a payment aggregator.

---

## 2. Constitutional status & the one hard collision (read before referencing anything below)

The proposal's **core mechanic is already ZUKA canon** — it invents almost nothing.
Its one genuinely new-and-dangerous element is **paid** resale, which collides with
the money posture. Both facts are established here so nothing below drifts.

### 2a · Specified vs. built (the trap to avoid — verified against `supabase/` 2026-07-09)

The proposal's transfer mechanic is **specified in the Bible but almost entirely
unbuilt.** Distinguishing the two is the whole game — treating a Vol 14 chapter as
if it were a shipped migration is how a "small addition" balloons into a subsystem.

| Proposal claim | Specified in | Built? (code truth) |
|---|---|---|
| Tickets are digital assets with persistent identity | Vol 14 Ch 1 + Ch 3 | ✅ ticket entity exists (`20260630130003_ticket.sql`) |
| Ownership transfers, not copies (`ISSUED → TRANSFERRING → ISSUED`) | Vol 14 Ch 3 | ❌ **spec-only** — `TRANSFERRING` is a bare CHECK value + comment (`ticket.sql:51,58`); **no transfer/gift RPC exists** in `supabase/` |
| New QR each transfer; previous QR permanently invalid | Vol 14 Ch 4 | ⚠️ QR-rotation *mechanism* built but wired **only** to payment-confirm (`ISSUED`) + check-in (`CHECKED_IN`) (`20260703100000_qr_token_rotation.sql:144,217`); **nothing rotates on transfer** |
| No screenshots / external tickets enter the system | Vol 14 Ch 1 | ✅ origin-constraint enforced |
| Immutable ownership chain | ADR-015 + Vol 14 catalogue | ❌ `TicketTransferInitiated/Completed` are **never emitted** by any migration — catalogue-only; no transfer ⇒ no chain to be immutable *of* |
| Scanner validates only the latest active credential | Scanner Pro + Vol 14 Ch 6 | ✅ latest-token validation built |
| Smart Bucket holds the current credential; change → regen + revoke | Vol 14 Ch 5 | ⚠️ mechanism exists; transfer-triggered regen does not (no transfer path) |

**Correction to an earlier draft of this doc:** it claimed QR-regen-on-transfer and
the ownership chain were "already built." They are **not.** What exists is a
reusable QR-rotation mechanism (purchase + check-in only) and a `ticket` table with
`TRANSFERRING` as an unreachable status value. This concept therefore adds **policy,
market, allocation — and the entire free-transfer engine itself**. It is a new
transfer subsystem (peer of the also-unbuilt gift flow), not a policy toggle.

### 2b · The hard collision: paid resale vs. money posture

The original design said *"Payment is processed by ZUKA"* and *"Settlement OS
automates payouts."* That is **escrow / hold-and-payout** behaviour — ZUKA holding
a seller's proceeds and disbursing them. It collides head-on with:

- **SOUL red line 4 / CLAUDE.md money posture:** ZUKA is **not** a payment
  aggregator; **vendor order money is vendor-direct**; the **wallet is frozen**
  pending NPS Act 2020 clearance.
- The **already-adjudicated** `CONCEPT_Settlement_Capability` ruling, where
  wallet / platform-split / payment-holding were **dropped** for exactly this
  reason.

**Resolution (this session):** the dormant paid path is reframed to the one money
pattern the Bible already blesses — **seller-direct settlement** (§5, Phase 2).
The escrow framing is **rejected**; see §7. Even so, the paid path stays **dormant**
until a formal ADR (candidate **ADR-029**) + a `zuka-legal` money-routing review.

---

## 3. Two walls, not one — *money is the outer wall; the missing engine is the inner one*

An earlier draft claimed a single wall ("money") with a fat money-free slice
buildable now. That was wrong: the **free-transfer engine itself does not exist**
(§2a), so the money-free slice is not a toggle — it is a whole subsystem to build.
There are two walls:

```
  INNER WALL (engineering reality)          OUTER WALL (constitution)
  the free-transfer engine is UNBUILT       money · pricing · market
  ─────────────────────────────────────────┼──────────────────────────────────────
  POST-LAUNCH BACKLOG (money-free, but a     │  DORMANT (Proposed — ADR-029 + legal)
  full subsystem — demand-gated):            │  paid resale listings + pricing policy
   · ISSUED→TRANSFERRING→ISSUED transition   │  platform fee + organizer commission
   · owner reassignment + acceptance         │  seller-direct settlement (money)
   · QR rotation-on-transfer (extend mech.)  │  waitlist auto-allocation-to-purchase
   · TicketTransferInitiated/Completed emit  │  markup caps / cooldowns / listing caps
   · DPPA by-reference ownership chain        │  suspicious-reseller detection (DRL 2+)
   · gathering.transfer_policy gate           │
  ─────────────────────────────────────────┴──────────────────────────────────────
  BUILDABLE / NEEDED NOW (pre-launch):  nothing. See §4.
```

The only near-term "value" the concept markets — anti-scalping — is **already
delivered by building nothing**: with no transfer path, tickets are non-transferable
today, which is exactly the `transfer_policy = none` default.

---

## 4. The first buildable slice — a POST-LAUNCH, demand-gated transfer-engine session (not MVP)

*Reality-checked 2026-07-09 (`zuka-reality-checker`): the free-transfer slice is
neither on the launch critical path nor a small addition. It is reclassified from
"buildable-now MVP" to a named post-launch backlog session, demand-gated.*

**Why it is not MVP.** It serves none of the three proofs (60-second Go Live ·
zero-login discovery · 3-tap ticket) — the concept itself concedes transfer is "not
the 3-tap buy or zero-login path." Launch revenue is the **primary-sale** commission
(Vol 14 Ch 13); resale fees are a Phase-2 nice-to-have. And its only pre-launch
value (anti-scalping) is already free via the no-transfer default (§3).

**Its honest size** (all money-free, but built from zero — a peer of the unbuilt
gift flow, NOT a policy toggle):

1. `gathering.transfer_policy ∈ {none, transfer_only}` field + the policy gate.
2. The `ISSUED → TRANSFERRING → ISSUED (new owner)` transition + owner reassignment.
3. Recipient-acceptance semantics (who can receive; verified-Person check).
4. **QR rotation on transfer** — extend the existing purchase/check-in rotation
   mechanism to the transfer transition + revoke the old credential.
5. `TicketTransferInitiated` / `TicketTransferCompleted` event emission.
6. The DPPA by-reference ownership chain (`person_id` refs, no PII in events).
7. Tests: transition integrity, RLS, QR-revocation-on-transfer, policy enforcement.

**Gating & readiness.** No ADR (money-free), but **do not schedule pre-launch.**
Build only if organiser/attendee demand appears — the settling signal is one
pre-launch venue saying *"I won't list without transfer control,"* or attendees
asking to hand off tickets. Until then: **build nothing.** Depends on a
transfer-engine session that does not yet exist.

> This concept authorizes **no build**. Concept capture ≠ build authorization; and
> the reality check says even the first slice waits for post-launch demand.

---

## 5. Dormant beyond the wall (Proposed · phased · ADR + legal gated)

### Phase 2 — Paid marketplace
- **Entities:** `gathering.resale_policy`, `resale_listing`, `resale_transaction`.
- **Organizer pricing sovereignty:** `resale_policy ∈ {none, face_value,
  capped_markup(pct), open_market}`. The organizer — never a global rule — sets it.
- **Fees:** a platform fee and (if configured) an organizer commission. ⚠️ **These
  are a per-transaction fee collected at point of sale — a *material escalation*
  over the cleared vendor posture** (`zuka-legal`, 2026-07-09). The vendor clearance
  rests on a **flat B2B listing/software fee not skimmed from any single sale**; a
  fee tied to the specific matched resale is closer to a *payment-facilitation* fee.
  Do **not** inherit the vendor clearance for this — it must be independently ruled.
- **Money model — SELLER-DIRECT (vendor-direct analog — necessary, NOT sufficient):**
  the seller is treated like a micro-vendor and must be the **merchant of record**
  for the principal — the buyer's payment settles to the **seller's own MoMo**, and
  **ZUKA never holds, escrows, disburses, or is the collector-of-record for the
  principal.** ZUKA's fee is a **separate** charge on a **separate transaction leg**
  (seller→ZUKA), never carved from a ZUKA-collected gross. Seller-direct removes the
  *custody* hallmark (which is what sank the Settlement Capability concept) — but per
  `zuka-legal` it does **not** self-clear the NPS-Act aggregator question (see §12).
  - **Merchant-of-record feasibility risk (HIGH — precondition of ADR-029):** MoR is
    coherent for a *vendor* with its own collection identity, but a **peer reseller
    holds only a personal MoMo line, not a merchant account.** Flutterwave's
    marketplace primitives typically make the **platform** MoR and split to
    subaccounts — exactly the collector-of-record posture to avoid — and carrying
    peer sellers as ZUKA sub-merchants pulls sub-merchant KYC/AML onto ZUKA. If
    peer sellers can't each be their own KYC'd MoR, seller-direct **collapses** and
    the paid path stays dormant. This is an engineering-feasibility precondition,
    not a contract detail.
  - **Gated by ADR-029 + `zuka-legal` money-routing review + `zuka-security`.**

### Phase 3 — Waitlist allocation
- **Entity:** `waitlist_entry`.
- **Flow:** sold-out gathering → Person joins waitlist → a newly listed ticket is
  **first-offered** to the head of the queue → on decline or reservation-expiry the
  offer **advances** to the next eligible person.
- A **money-free waitlist seam** (join / leave / position) could ship earlier as a
  discovery primitive, but **auto-allocation-to-purchase** rides Phase 2 (it needs a
  listing to allocate).

### Anti-scalping — split by readiness
| Control | Nature | Readiness |
|---|---|---|
| Purchase limits, resale cooldowns, markup caps, max simultaneous listings | deterministic config | rides Phase 2 (pure rules; no scoring) |
| "Detection of suspicious reseller activity" | behavioural inference / scoring | **DRL 2+ dormant** — DRL 1 forbids scoring; **trust is a state, never a number** (Vol 11) |

The MVP `transfer_policy = none` is itself the strongest anti-scalp control
available now: no transfer, no scalp.

---

## 6. New entities (the complete new-surface list)

| Entity / field | Phase | Notes |
|---|---|---|
| `gathering.transfer_policy` | **Backlog** (post-launch, money-free; §4) | `{none, transfer_only}`; default `none` |
| `gathering.resale_policy` | Phase 2 | `{none, face_value, capped_markup, open_market}` |
| `resale_listing` | Phase 2 | seller-owned offer of an owned ticket |
| `resale_transaction` | Phase 2 | the settled resale (fees, seller-direct split) |
| `waitlist_entry` | Phase 3 | queue position for a sold-out gathering |

Everything else — ticket, QR token, Person, Gathering, ownership events, Smart
Bucket, Scanner Pro — is a **shared entity referenced from its owning document**,
never redefined here.

---

## 7. Rejected / superseded ideas (kept for provenance)

- **Escrow / "Settlement OS automates payouts."** ZUKA holding buyer funds and
  disbursing to the seller = payment-aggregator/escrow behaviour → collides with
  SOUL red line 4, the frozen wallet, and the Settlement Capability ruling.
  **Superseded by the seller-direct model (§5).** Recorded, not adopted.
- **A single global resale rule.** Superseded by **organizer sovereignty** — each
  gathering sets its own policy.
- **Trust-scored reseller detection at MVP.** DRL-gated to 2+; cannot ship at DRL 1.

---

## 8. Integration map (each mapped to its real V4 home — no new "OS" entities)

The original design named "Identity OS / Trust OS / Payment OS / Settlement OS."
ZUKA has no such standalone OSes; each maps to an existing home:

| Named "OS" | Real ZUKA home | Role in this concept |
|---|---|---|
| Identity | Vol 03 (Identity) + `auth.uid()` | ownership belongs to a **verified Person** |
| Trust | Vol 11 (Trust OS / Anti-Fraud) | ticket **authenticity**; DRL-gated abuse detection |
| Payment | Flutterwave + Vol 14 | **seller-direct** split, ZUKA holds nothing |
| Settlement | Settlement ruling / Vendor §9B | payment-blind; **no ZUKA-held funds** |
| Scanner Pro | `CONCEPT_Scanner_Pro_Capability_Model` | validates **only the latest** active credential (built) |
| Smart Bucket | Vol 14 Ch 5 | holds current credential; QR-rotation mechanism exists — but transfer-triggered regen is **unbuilt** (no transfer path; §2a) |

---

## 9. Compliance notes

- **NPS Act 2020:** seller-direct split narrows the aggregator risk but does **not**
  self-evidently clear it. The open legal question is broader than fund custody:
  **operating a resale venue and matching buyer→seller for a fee is marketplace
  *facilitation*, which may carry obligations even when ZUKA never touches the
  principal.** Not-holding-funds is necessary, not sufficient. So the Phase-2
  `zuka-legal` gate must answer two questions, not one: (a) do we hold principal?
  (seller-direct: no), and (b) does intermediating a paid resale market trigger
  regulatory obligations at all? Treat (b) as unresolved until counsel rules.
- **DPPA 2019:** the ownership chain names Persons across multiple transfers →
  personal data. The chain MUST store `person_id` **by reference**, per
  `CONCEPT_EventStore_PII_Erasure` — **no PII embedded** in `domain_events` payloads.
  Right-to-erasure must resolve without breaking chain integrity (by-reference
  default; crypto-shred/pseudonym deferred there).

---

## 10. Readiness gates & implied ADRs

- **First slice — free-transfer engine (§4):** no ADR (money-free), but **post-launch
  and demand-gated**, not MVP. It is a full transfer subsystem built from zero
  (not a policy toggle), a peer of the unbuilt gift flow → forward-only migration +
  `zuka-reviewer` + `zuka-tester`. Do not schedule pre-launch; build nothing until
  organiser/attendee demand appears (§4). Not authorized by this doc.
- **Phase 2 paid marketplace:** **candidate ADR-029** (secondary-marketplace money
  routing) + `zuka-legal` + `zuka-security`. **Blocked** until the five §12
  preconditions are resolved — chiefly the counsel ruling on facilitation itself and
  the Flutterwave merchant-of-record feasibility for peer sellers.
- **Phase 3 waitlist auto-allocation:** rides Phase 2.
- **Reseller detection:** **DRL 2+** — dormant until data readiness earns it.

---

## 11. Security requirements for the eventual build (binding — `zuka-security` audit, 2026-07-09)

*Risk posture as it stands: **LOW** — unbuilt, correctly dormant, `transfer_policy =
none` default = non-transferable today. All risk is latent in the build. These are
**requirements the transfer/resale subsystem MUST meet** when it is authorized; the
build re-audits actual RPCs against the S021 concurrency harness. Ranked by Crown
Jewel priority.*

**The one invariant that defeats most of the threat surface:** *at every instant
exactly one valid credential exists and it belongs to the current owner.* Credential
revocation and owner reassignment must be a **single atomic act under a `FOR UPDATE`
ticket-row lock.* This one property simultaneously kills the transfer-race
double-admission, the transfer-twice race, and the online replay of a pre-transfer
screenshot.

- **[CRITICAL] Atomic revoke + reassign under row lock.** The transfer RPC must
  `SELECT … FOR UPDATE` the ticket, assert `status='ISSUED'` **and** current
  ownership inside the lock, rotate `qr_token = gen_random_uuid()`, and transition
  status — all in one tx, serializing on the **same** lock `check_in_ticket` already
  takes (`20260703100000_qr_token_rotation.sql:238-242`). Threat: seller+buyer both
  check in → double `AttendanceSeedRecord` → **unrebuildable Trust-Graph pollution**.
  Avoid the event-then-`RAISE` trap ([[project_domain_event_rollback_trap]]): emit
  `TicketTransfer*` only once state settles; signal rejection via return value. Prove
  under the S021 harness ([[project_concurrency_harness]]).
- **[CRITICAL] Ownership proven from `auth.uid()`, never a client arg; REVOKE the
  auto-grant.** Inside SECURITY DEFINER, `current_user` = function owner
  ([[project_security_definer_current_user_trap]]) — resolve seller via
  `auth.uid() → person.id`, compare to `ticket.owner_id` read under the same
  `FOR UPDATE`. Every transfer/resale RPC needs `REVOKE EXECUTE … FROM PUBLIC, anon;
  GRANT … TO authenticated`, verified via `has_function_privilege`
  ([[project_supabase_auto_grant]]). `resale_listing` is created **only** through a
  DEFINER RPC binding seller = caller — never client-INSERTable with a spoofable
  `owner_id`. Threat: a non-owner lists/transfers another Person's ticket (theft).
- **[HIGH] New tables ship RLS-enabled default-deny; no PII / ownership-history /
  trust-state leak.** `resale_listing`, `resale_transaction`, `waitlist_entry`,
  and the `gathering.transfer_policy`/`resale_policy` columns: model on `rls_ticket`
  (`20260630160002`). Public listing view = minimal projection (gathering + price +
  opaque listing id) — **never** seller `person_id`, MoMo, PII, or any join to
  `trust_profile`. `resale_transaction` SELECT limited to the two counterparties (it
  links seller↔buyer↔ticket↔price — a private economic graph). `waitlist_entry`
  owner-scoped. Ownership chain stores `person_id` by reference (§9).
- **[HIGH] Offline lane must not admit a pre-transfer screenshot.** Online, a rotated
  token makes the old screenshot resolve NOT_FOUND — but a **stale offline manifest**
  cached before the transfer still holds the old token's hash (and lacks the new
  owner's). Bound manifest staleness (short re-download TTL near door-open),
  regenerate/mark manifests on transfer, and have `sync_offline_scans` flag an
  admission whose token was superseded by a transfer as a **conflict for human
  review** (ADR-014), not silent `ALREADY_IN`. Transfer must never degrade the
  offline door below single-use. Keep the offline lane admitting on cache so a
  no-signal gate still works.
- **[HIGH · dormant Phase 2] Seller-direct money path atomicity — ZUKA must not
  inherit liability.** Payment (buyer→seller MoMo) and transfer (ZUKA DB) are not
  atomic. Three holes ADR-029 must close: (1) **pay-but-no-ticket** — reserve the
  ticket at purchase-initiation (a `resale-pending` status mirroring
  `PAYMENT_PENDING`), re-verify ownership `FOR UPDATE` at settlement; (2)
  **list-then-transfer-elsewhere / list-what-you-no-longer-own** — invalidate the
  listing the instant the ticket transfers or checks in by any path; verify ownership
  at settlement, not at listing; (3) **fee evasion** vs the collector-of-record
  tension — coupling issuance to a ZUKA-confirmed multi-leg settlement is the
  "aggregator in disguise" posture; this is the §9(b) legal question. Define
  chargeback/refund-after-check-in as a seller↔buyer dispute ZUKA is **not** party to.
- **[MED] Anti-scalp caps are only as strong as Person-uniqueness, and must be
  race-safe.** Person-keyed caps are bypassed by multi-account creation; at DRL 1
  (no Smile Identity binding) they are soft. Implement with the S010 advisory-lock +
  `FOR UPDATE` TOCTOU pattern (`20260702110001:143-154`), prove no burst-past-cap on
  the S021 harness, and gate **open-market** resale readiness on verified-Person
  coverage. Tune thresholds to real Kampala group-buying so friend hand-offs aren't
  false-blocked. No new scoring at DRL 1.
- **[MED] No seller trust/reputation cue on listings.** A "trusted seller" badge or a
  raw-dimension-derived ranking rendered client-side breaches the trust-state
  non-exposure invariant. Listings show at most the sanctioned human-readable state —
  never a number, never a raw-dimension-derived sort. Risk levers stay server-side /
  human-in-the-loop (ADR-014).
- **[LOW] Seller/buyer MoMo + settlement identifiers stay by-reference.** Never in
  `domain_events` payloads or log lines (consistent with `qr_token` never entering
  events, `qr_token_rotation.sql:26`). Right-to-erasure resolves by reference without
  breaking chain integrity (§9).

---

## 12. Legal-awareness review — money posture (`zuka-legal`, 2026-07-09)

*Input for qualified Ugandan counsel — **not legal advice**, and does not clear
ADR-029. Read-only awareness pass on the dormant paid path. Every item below carries
an implicit "verify with counsel before proceeding."*

**Answer to §9(b): "not holding funds" is necessary but NOT sufficient.** The
operative NPS Act 2020 test (per Vendor Marketplace §9, verified authority) catches
*"any party that **facilitates** electronic receipt and payment for goods and
services"* — that verb is broader than custody. Operating without a Bank of Uganda
licence sits in the UGX 50M–500M penalty class. Matching buyer→seller + setting/
enforcing settlement price + taking a per-transaction fee + coupling issuance to
settlement are facilitation hallmarks regardless of whose account the principal hits.

| # | Risk flag | Severity |
|---|---|---|
| 1 | **NPS-Act facilitation survives seller-direct.** The *fee-collection leg* is the weak point: a per-sale fee at point of sale is closer to a payment-facilitation fee than the vendor's flat listing fee — **does not inherit the vendor clearance** (§5 ⚠️). A naive "Flutterwave split" (ZUKA collector-of-record) = aggregator in disguise. | **HIGH** |
| 2 | **Merchant-of-record likely unachievable for *peer* sellers.** Personal MoMo ≠ merchant identity; Flutterwave marketplace primitives make the *platform* MoR; peer sellers as ZUKA sub-merchants ⇒ ZUKA carries sub-merchant KYC/AML. If each seller can't be their own KYC'd MoR, seller-direct collapses (§5). | **HIGH** |
| 3 | **Consumer-protection / broken refund chain.** Secondary buyer paid the *seller*; the organizer holds the *original* face value (Vol 14 Ch 13 escrow, RULE 3). On cancellation the secondary buyer has **no clean refund path**. The §11-H3 "ZUKA not party to disputes" disclaimer is a *position to be tested*, not settled — disclaimers are read down against the party in control. Fees must be disclosed **before commit** (Vol 23 Ch 9; Electronic Transactions Act distance-selling). | **HIGH** |
| 4 | **DPPA — consent + settlement-PII erasability + re-identification.** By-reference architecture (§9/§11) is the right foundation, but: no lawful-basis/consent defined for resale processing; `resale_transaction` settlement identifiers (MoMo) must be erasable without breaking ADR-015 (crypto-shred/pseudonym deferral must be **closed before collection**); a who-bought-from-whom graph is highly re-identifiable at Kampala scale. | **MED** |
| 5 | **Tax/VAT — marketplace-facilitator question (informational).** ZUKA's fee/commission are taxable supplies (VAT + income tax + EFRIS). Separately, URA may deem a *marketplace facilitator* to owe VAT duties on facilitated resales **even without holding principal** — distinct from the NPS question; don't assume seller-direct settles tax. | **LOW/info** |

**Must be resolved before ADR-029 can proceed:**
1. Prove the Flutterwave mechanic concretely — fee on a **separate leg**, principal
   buyer→seller with ZUKA never collector/MoR, **including for peer sellers** (Findings 1+2).
2. A counsel ruling on **facilitation itself** — does a paid resale venue for a
   per-transaction fee require a BoU licence under the NPS Act, independent of custody?
3. Map the **refund/cancellation chain** for resold tickets end-to-end + test the
   disclaimer's enforceability (Finding 3).
4. Define **DPPA consent capture + settlement-PII erasability + retention** for
   `resale_transaction`; close the deferred crypto-shred/pseudonym design (Finding 4).
5. Retain the §11 `zuka-security` requirements unchanged.

**The single biggest open legal question:** *Under Ugandan law, does ZUKA's act of
intermediating a paid resale market — matching buyer→seller, setting/enforcing resale
pricing, taking a per-transaction fee — by itself constitute regulated "facilitation
of electronic receipt and payment" under the NPS Act 2020 requiring a BoU licence,
even though the principal settles seller-direct?* Everything in Phase 2 is downstream
of that answer. Seller-direct is necessary; it has **not** been shown to be sufficient.

---

*Subordinate to SOUL.md and ZukaBibleV4. This concept flags and proposes; people
and ADRs decide. Tickets are transferred — never copied.*
