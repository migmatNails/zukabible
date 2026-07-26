# ZUKA — Vendor Marketplace Design Document
# Version 1.0 · 2026-07-01 · Pre-build Design Session

## Status: CONFIRMED — founder decisions locked 2026-07-01; ready for
## writing-plans (implementation plan) on Route A

> **Method note:** This document does not re-derive the vendor marketplace from
> scratch. `docs/concepts/CONCEPT_In_Event_Vendor_Marketplace.md` (Ch 3, 3B–3H,
> 4, 7, 8, 9, 11) already settles nearly all of the entity, activation, and
> monetization design with field-level detail. This session's job was to (1)
> verify those decisions against Bible citations, (2) check them against the
> **actual** S001–S007 schema rather than the concept doc's assumptions about
> what exists, and (3) surface contradictions, gaps, and cross-concept
> dependencies. Deltas from the concept doc are called out explicitly below;
> everything else is the concept doc's design, cited.

---

## 0. Schema Reconciliation (found by reading the real migrations, not assumed)

Before any vendor-specific design, four gaps were found between the concept
doc's model of "what exists" (per its Ch 1 framing and this session's own
brief) and the actual S001–S007 schema:

```
1. gathering.ends_at is NULLABLE today, not required.
   Concept doc Ch 3C says "EVENT DURATION IS REQUIRED AT EVENT CREATION" —
   the schema doesn't enforce that. This blocks the grace-window / dynamic-
   disabling logic every vendor lifecycle transition depends on. NOT a
   vendor-specific fix — belongs to general gathering-lifecycle hardening,
   but is a hard prerequisite for vendor Phase 1.

2. ticket.ticket_type CHECK constraint only allows
   standard|group|table|vip|streaming|comp|staff — no 'vendor' value.
   Concept doc Ch 4 anticipates this ("add 'vendor' to the existing class
   enum") but it is a literal migration, not yet done.

3. discovery_feed() is anon-callable, SECURITY DEFINER, with NO auth context
   at all — a deliberate design for MVP Proof 2 (zero-login discovery,
   SOUL.md red line 5). A vendor feed needs the OPPOSITE posture (gated on
   proven attendance). This is not "discovery_feed() + a filter" — it must
   be a distinct RPC with a distinct security model.

4. No Organisation entity exists yet, though Vol 04 Ch2 lists it in Tier 1.
   VendorCompany has nothing to specialize from — it is genuinely new.
```

These are treated as constraints on the design below, not separate asks.

---

## 1. Entity Design

### VendorCompany / VendorTeam
*Cites: concept doc Ch 3G (settled) · Vol 04 Ch2 (Organisation, Tier 1 precedent) ·
Vol 04 Ch5 (Ticket schema pattern) · Vol 08 (identity mutability — a company
must outlive a change to the Person who registered it)*

Not a specialization of Person — a solo vendor is a company-of-one, not
literally their Person record, because the debt ledger and T&C acceptance
(Ch 3G, 3F) must survive independent of any one person's account. Minimum
viable for a solo vendor and a two-team vendor, without over-building for a
50-team vendor that may never exist:

```
VendorCompany   new Tier-1 entity (Vol 04 Ch2 Organisation precedent)
  id, name, logo_url, momo_number, owner_person_id, reputation,
  fee_cleared BOOLEAN (derived cache — see §2, Q6)

VendorTeam      new entity — a labelled unit of a company, NOT a Person
  id, vendor_company_id, label ("Rooftop crew")

VendorTeamMember  join table
  vendor_team_id, person_id
```

A solo vendor is one row in each of the three tables — no special-cased
"solo mode." A three-team vendor is the same shape with more rows. This
matches Ch 3G's framing exactly: "independent activation, shared
accountability" — teams activate per-event: billing and the lock evaluate
at the company level.

> **Cross-ref — per-gathering deputy delegation (owned elsewhere).** How a vendor
> admin manages Scanner Pro across 2+ concurrent gatherings — assigning a per-event
> *deputy* with limited, event-scoped abilities — is the capability-delegation model
> **owned by `CONCEPT_Scanner_Pro_Capability_Model.md` §4/§10**, not redefined here.
> That section flags two gaps this schema will need for the feature: `VendorTeam` has
> no `gathering_id` (event binding is a label, not enforced) and `VendorTeamMember`
> has no role (no deputy/lead vs. worker). Both are Phase-2 additions; neither is
> needed for Phase-1 presence + discovery.

### Vendor Credential (reuses the closed-system Ticket entity)
*Cites: concept doc Ch 3 (unified rule), Ch 4 (settled — reuses Vol 14 Ch1) ·
Vol 14 Ch1 (Ticket Provenance Doctrine) · Vol 14 Ch3 (lifecycle state machine)*

**One new `ticket_type` value: `vendor`** — not two. The brainstorming brief's
own question 2 suggested `VENDOR_ATTENDEE` / `VENDOR_PASS` as separate values;
the concept doc's Ch 4 table says "Vendor = a ticket_class — add 'vendor' to
the existing class enum" (singular), and Ch 3's unified rule deliberately
converges Route A and Route B on one activation path. Splitting the
`ticket_type` would fork logic the concept doc keeps single. Route A vs
Route B is distinguished by `provenance_origin` (`'purchase'` vs a new
`'vendor_listing_fee'` origin), which already exists as a closed-system
column (Vol 14 Ch1) — reused, not duplicated.

```sql
ALTER TABLE public.ticket
  DROP CONSTRAINT ticket_ticket_type_check,
  ADD CONSTRAINT ticket_ticket_type_check
    CHECK (ticket_type IN (
      'standard','group','table','vip','streaming','comp','staff','vendor'
    ));

ALTER TABLE public.ticket
  DROP CONSTRAINT IF EXISTS ticket_provenance_origin_check,
  ADD CONSTRAINT ticket_provenance_origin_check
    CHECK (provenance_origin IN (
      'purchase','gift_transfer','promotion','points_redemption',
      'comp_issue','group_split','refund_reissue','migration',
      'vendor_listing_fee'
    ));
```

**No new ticket states.** Verified directly against `check_in_ticket()`
(20260630170001_fn_check_in_ticket.sql): it status-guards on `ISSUED`, locks
the row `FOR UPDATE`, transitions to `CHECKED_IN`. A vendor ticket goes
through this identical guard — ISSUED → CHECKED_IN already models "credential
exists, then scanned" exactly as Route A/B both need.

**Cross-use is prevented structurally**, not by a new rule: `ticket_type =
'vendor'` is the single condition that triggers the presence-activation side
effect inside `check_in_ticket()` (§2, Q4). An attendee ticket never touches
`VendorTeam`; a vendor ticket always does. No cross-use is reachable because
the two flows are disjoint branches off a column that already exists.

### VendorPresence (the concept doc's name for "VendorListing")
*Cites: concept doc Ch 3 (lifecycle), Ch 7 (already fully specified)*

Note: this session's brief used the working name "VendorListing"; the concept
doc's own Ch 7 entity registry already names and specifies this entity as
`VendorPresence`. This document uses the concept doc's name to avoid two
labels for one entity.

```
VendorPresence
  vendor_team_id  → vendor_team (new)
  gathering_id    → gathering (existing table — matches real FK naming, not
                    the concept doc's "event_id")
  credential_id   → ticket.id (existing table, ticket_type='vendor')
  status          [DORMANT|ACTIVATING|LIVE|PAUSED|CLOSED]
  location_text
  activated_at, closed_at
```

Every FK target already exists except the two new vendor tables — no new FK
pattern is introduced.

---

## 2. Activation Flow

### Q: trigger point
*Cites: concept doc Ch 4 ("the server simply recognises the vendor class and
fires the presence activation"), Ch 7 ("the rules the backend must enforce
hardest") · Vol 14 Ch6/Ch7 (Matching Engine, contextual-blind) · ADR-015
(append-only events, same transaction)*

Extends `check_in_ticket()` in place — **not** a second RPC the client calls
separately. Verified against the real function body: after the ticket
`UPDATE ... SET status = 'CHECKED_IN'` and alongside the existing
`TicketCheckedIn` / `AttendanceSeedRecord` event inserts, add:

```sql
IF v_ticket_type = 'vendor' THEN
    -- resolve vendor_team via credential_id = v_ticket_id
    -- read vendor_company.fee_cleared (see Q6) off the already-locked path
    IF v_fee_cleared THEN
        INSERT INTO public.vendor_presence (...) ... status = 'LIVE';
        -- fire VendorPresenceActivated
    ELSE
        v_vendor_lock_message := format(
          'Listing locked. %s has an unsettled platform fee of UGX %s from %s. '
          'Settle to go live.', v_company_name, v_debt_amount, v_debt_source_event);
        -- fire VendorPresenceBlocked
    END IF;
END IF;
```

`check_in_ticket()`'s `RETURNS TABLE` gains one nullable column
(`vendor_lock_message TEXT`) to carry the Ch 3G "told at the gate, not
discovered by silence" flag back to the scanner device — no scanner-app
change beyond displaying that field when present.

This makes "no scan, no listing" (Ch 7's hardest rule) true by construction:
activation happens in the same transaction as check-in, so there is no window
where a listing could go live without a real scan, and no second call that
could be skipped or replayed.

### Q: Route B / scanner_session changes
*Cites: concept doc Ch 3 Route B, verified against 20260630170000_scanner_session.sql*

None needed. `scanner_session` is bound to `gathering_id` only and has no
ticket-type awareness — it doesn't need any, because `check_in_ticket()`
already looks up by `qr_token` and branches on `ticket_type` inside one
function. A vendor pass scans through the identical PIN-authenticated
session as any ticket. Transparent, as the concept doc assumed.

### Q: fee-cleared check at scan time
*Cites: concept doc Ch 3G ("INSTANT UNLOCK... no appeal queue, no manual
review for a plain payment" — this requirement rules out a live aggregate
query, since the state must already be true the instant a settlement posts)*

```
vendor_company.fee_cleared BOOLEAN NOT NULL DEFAULT true
```
Flipped by the same transaction that writes a `CommissionDebtLedger` entry:
an accrual sets it `false`; a settlement recomputes and sets it `true` only
if no other unsettled entries remain for that company. `check_in_ticket()`
already does a `SELECT ... FOR UPDATE` on the ticket row — one additional
join to `vendor_company` for a single indexed boolean read adds negligible
latency to the 99% clear-case path. This is a precomputed-flag design, not a
live invoice scan, and it satisfies both "fails fast, no latency for the
common case" and "instant unlock" simultaneously.

---

## 3. Attendee Discovery

### vendor_feed() vs discovery_feed() extension
*Cites: verified against 20260630140004_fn_discovery_feed.sql · SOUL.md red
line 5 (zero-login discovery is sacred) · concept doc Ch 3 condition (e)*

**Must be a separate RPC.** `discovery_feed()` is `SECURITY DEFINER`,
anon-granted, with no auth context — by design, for MVP Proof 2. Vendor
discovery requires the opposite: gated on "viewer is a proven attendee
present at THIS event" (Ch 3 condition e). Bolting attendance-gating onto
`discovery_feed()` would either break zero-login discovery for every caller
or require branching the anon/authenticated posture inside one function —
strictly worse than two small functions with different security postures.

```
vendor_feed(p_gathering_id UUID)
  authenticated-only. Checks the caller's own ticket.status = 'CHECKED_IN'
  for p_gathering_id (Route A), OR an AttendancePresence row for
  p_gathering_id (Route B — see §5 dependency note) before returning any
  vendor_presence rows for that gathering.
```

### Trackability tier at MVP
*Cites: concept doc Ch 3H (settled, not open) · Ch 8 (Phase 1 scope)*

Not an open question — Ch 8 already scopes it precisely: Phase 1 builds the
full category checklist and its trackability-tiered **pricing** (Ch 3H), but
Phase 1 has no `VendorOrder` at all — ordering is Phase 2. "Fully-trackable"
in Phase 1 means "this category is priced as if orders will be trackable
once ordering ships," not that anything is tracked yet. Keep this reading
explicit — it would be easy to accidentally build order-tracking into
Phase 1 because the category checklist mentions it.

### Table delivery
*Cites: concept doc Ch 11 (ratified reality-check verdict)*

Confirmed still the position: **DEFER**, own future concept, blocked on (a)
Phase 2 ordering being proven and (b) a table/seat-location entity that does
not exist (the GatePlan gap). Phase 1's attendee interaction is discover →
walk over → buy in person. No in-app ordering of any kind at Phase 1.

---

## 4. Commission and Money Posture

*Cites: concept doc Ch 9 (settled) · SOUL.md red line 4 · CLAUDE.md §5 (NPS
Act 2020 posture) · Vol 23 Ch6 (Bank of Uganda / PSP-only money movement) ·
Vol 23 Ch9 (Anti-Drain Principles)*

### The hard constraint (unchanged, reaffirmed)
ZUKA does not sit in the buyer→vendor money flow. Attendee-to-vendor order
money is vendor-direct (MoMo, cash) and Zuka never touches it — confirmed
against SOUL.md red line 4 and CLAUDE.md's standing NPS Act posture. This
governs every design choice below.

### Listing fee collection — reuses the existing payment rail, no new integration
- **Route A** (paid events): the vendor commission is baked into the
  vendor-ticket *price*, exactly like attendee tickets. Verified against the
  real schema: attendee ticket pricing already works via
  `gathering_tier.price_ugx`; a vendor tier is simply another
  `gathering_tier` row with a higher `price_ugx` reflecting the higher
  commission (Ch 3F). It flows through the **already-built**
  `initiate_ticket_purchase()` → Flutterwave Edge Function →
  `confirm_ticket_payment()` path, unchanged.
- **Route B** (free events): the listing fee is collected the same way — a
  Flutterwave PaymentIntent, but against a `vendor_listing_fee` product
  instead of a `gathering_tier`. This fee is *Zuka's own* B2B charge (Zuka is
  the counterparty, not an intermediary for buyer→vendor money), so it sits
  cleanly inside the existing legally-clean rail. Neither route requires a
  new payment integration.

### CommissionDebtLedger — minimum viable design
*Cites: concept doc Ch 3G/Ch 7*

```
commission_debt_ledger   append-only (ADR-015)
  vendor_company_id, entry_type ['accrual'|'settlement'],
  amount_ugx, source_gathering_id, created_at
```
Never holds vendor money — it is a record of what a company owes Zuka for
sales Zuka demonstrably drove (Ch 3G's anti-drain reasoning: "not extending
more vendor credit until settled is fair, not extractive"). It is the source
of truth; `vendor_company.fee_cleared` (§2, Q6) is a cache recomputed on every
ledger write.

### NPS Act compliance confirmation
No part of this design has Zuka receiving, holding, or remitting
buyer→vendor money at any phase, including Phase 2 (order fulfilment is
collect-in-person; payment for the goods themselves is vendor-direct MoMo,
outside Zuka's rails). The only money Zuka ever touches in this feature is
the vendor's own fee to Zuka, via the existing Flutterwave PaymentIntent
flow already used for ticketing. This matches Vol 23 Ch6's requirement that
all payment processing run through licensed PSPs and that Zuka not hold or
move money directly.

---

## 5. MVP Cut Lines (Phase 1 only)

*Cites: concept doc Ch 8 (phasing), Ch 11 (ratified reality-check verdict)*

Phase 1 — presence + discovery — stands alone as a complete, valuable
feature: a vendor who's findable and a buyer who walks over is a full
transaction, just off-platform. It requires no Phase 2 capability to be
useful.

### Cross-concept dependency found this session (not in the original brief)

`docs/concepts/CONCEPT_Poster_Acquisition_Engine.md` already exists and
**owns** `EventPoster`, `AttendancePresence`, and `ScanEvent` outright (its
Ch 11, Ch 14: "THIS document owns the poster as acquisition + general
presence mechanism... the vendor doc CONSUMES the presence it produces").
That concept's own Ch 13 flags **DPPA consent for non-user scans as its
single most important pre-build item**, unresolved. This means:

```
Route A (ticketed events) has NO external dependency — it ships on
  check_in_ticket() + gathering_tier + Flutterwave, all already built.

Route B (free events, poster-activated) has a HARD dependency on the Poster
  Acquisition Engine's own Phase 1 (poster generation, scan, presence
  creation) AND its DPPA consent question being resolved by zuka-legal.
  This is not a vendor-marketplace decision to make — it is an external
  blocker owned by a different, also-unscheduled concept.
```

**Confirmed by founder (2026-07-01): Route A only for the first build.**
Route B's schema (the `provenance_origin = 'vendor_listing_fee'` path, the
free-event pricing model) is designed above but not activated. The Poster
Acquisition Engine runs on its own, independent timeline — its build is not
gated on the vendor marketplace, and Route B does not get scheduled until
that concept has shipped its own Phase 1 and resolved its DPPA consent
question. The two concepts proceed in parallel, not in sequence, with no
cross-blocking in either direction beyond Route B's activation itself.

### Minimum viable schema — Phase 1 (Route A unblocked; Route B schema present, gated)

```
NEW TABLES
  vendor_company, vendor_team, vendor_team_member
  commission_debt_ledger
  vendor_category            (platform checklist; Ch 3F/3H)
  vendor_credential_fee       (category × trackability-tier pricing; Ch 3F/3H —
                               shape proposed this session, not verbatim in the
                               concept doc)
  vendor_terms_template, vendor_terms_acceptance   (Ch 3F)
  menu_template, vendor_menu_item                  (Ch 3D)
  vendor_presence                                  (Ch 3 lifecycle)

DEFERRED TABLES (Route B — owned by Poster Acquisition Engine, not this build)
  event_poster, attendance_presence, scan_event

PREREQUISITE SCHEMA CHANGE (not vendor-scoped, but blocking)
  gathering.ends_at → NOT NULL (Ch 3C)

SCHEMA CHANGES TO EXISTING VENDOR-ADJACENT TABLES
  ticket.ticket_type CHECK: add 'vendor'
  ticket.provenance_origin CHECK: add 'vendor_listing_fee'

NEW / EXTENDED RPCs
  check_in_ticket()            extended in place (§2) — not a new function
  create_vendor_company(), create_vendor_team()
  register_vendor_credential()  wraps initiate_ticket_purchase() for Route A
  vendor_feed(p_gathering_id)   (§3)
  settle_commission_debt()      writes a ledger settlement, flips fee_cleared
  vendor_status_toggle()        OPEN / PAUSE / SOLD_OUT / DONE
  save_menu_template() / import_menu_template()

NEW DOMAIN EVENTS (concept doc Ch 7 list — unchanged, already minimal)
  VendorPresenceActivated · VendorPresenceBlocked · VendorPresenceClosed ·
  VendorStatusChanged · VendorCompanyLocked · VendorCompanyUnlocked ·
  VendorTermsAccepted · MenuTemplateSaved · MenuTemplateImported
```

### Explicitly deferred and why
```
Route B / poster activation    external dependency on Poster Acquisition
                                Engine + its unresolved DPPA question (above)
Ordering, payment, order status  Phase 2 — money enters, gated on legal per
                                  Ch 8
Reviews, richer live status,
  paid analytics                 Phase 3 — Ch 8
Table delivery                   own future concept — Ch 11 ratified verdict
```

---

## 6. Founder Decisions (confirmed 2026-07-01)

1. **Poster self-scan presence design (Ch 3B):** Design 1 vs Design 2 —
   **deferred.** Moot until Route B is actually scheduled; not a decision
   this design or its first build depends on. Revisit when the Poster
   Acquisition Engine's own build session is planned.
2. **Phase 1 alone vs. Phase 1 + minimal Phase 2:** **confirmed — Phase 1
   alone.** The concept doc's Ch 8/11 reality-check verdict stands as
   ratified, not merely read as ratified. The implementation plan targets
   presence + discovery only; no ordering, no payment-for-goods, no
   in-app fulfilment.
3. **Exact commission rate structure (Ch 9):** vendor-ticket commission
   uplift, Route B listing-fee price points, Tier 0/1/2 order-fee
   thresholds, per-category untrackable surcharges — **to be decided
   separately with zuka-growth before the build session, not a blocker for
   this schema.** The schema in §5 stores rates as data
   (`vendor_credential_fee.amount_ugx`, ledger `amount_ugx`), not as
   hardcoded constants, so the numbers can be supplied later without a
   schema change.
4. **Sequencing vs. the Poster Acquisition Engine:** **confirmed — Route A
   first, independently.** The Poster Acquisition Engine runs on its own
   timeline; the two concepts do not block each other except that Route B's
   *activation* specifically waits on that concept's Phase 1 + its DPPA
   resolution (§5 above).
5. **`vendor_credential_fee` schema shape:** **provisionally accepted** as
   proposed in §5 — `vendor_category` × trackability tier drives pricing.
   May be revisited once zuka-growth's commission-rate work (decision 3)
   surfaces a shape this doesn't accommodate.
6. **Route B scope for the first build:** **confirmed — Route A only.**
   Matches §5's recommendation exactly; Route B schema is designed but
   inert until the Poster Acquisition Engine ships independently.

---

## 7. Implementation Sequence

```
PREREQUISITE (not vendor-scoped; can run independently or as this session's
  first migration)
  0. gathering.ends_at → NOT NULL, add grace-window field(s) per Ch 3C.
     Blocks every downstream lifecycle-close behavior.

SESSION A — Company/Team foundation
  1. vendor_company, vendor_team, vendor_team_member tables + RLS.
     No dependency on anything vendor-specific in the ticket/scan path yet.

SESSION B — Credential + activation (Route A only)
  2. ticket.ticket_type + provenance_origin CHECK constraint updates.
  3. Extend check_in_ticket() with the vendor branch (§2, Q4) — reads
     vendor_company.fee_cleared, writes vendor_presence, fires
     VendorPresenceActivated / VendorPresenceBlocked.
     Depends on: Session A (vendor_team must exist to resolve from
     credential_id), Prerequisite 0 (lifecycle close needs ends_at).

SESSION C — Commission lock
  4. commission_debt_ledger table, settle_commission_debt() RPC,
     fee_cleared derivation trigger/function.
     Depends on: Session A.
     Should land before or alongside Session B, since Session B's
     check_in_ticket() extension reads fee_cleared — order B after C, or
     land C's schema first and wire the check in the same session as B.

SESSION D — Organiser vendor setup
  5. vendor_category, vendor_credential_fee, vendor_terms_template,
     vendor_terms_acceptance. register_vendor_credential() wrapping
     initiate_ticket_purchase().
     Depends on: Session A. Independent of B/C — can run in parallel.

SESSION E — Reusable vendor assets
  6. menu_template, vendor_menu_item, save/import RPCs.
     Depends on: Session A. Independent of B/C/D — can run in parallel.

SESSION F — Attendee discovery + vendor console
  7. vendor_feed() RPC, vendor_status_toggle().
     Depends on: Session B (vendor_presence must exist and activate),
     Session D (categories drive the feed's filter), Session E (menu must
     be importable to show anything).

EXTERNAL DEPENDENCY (not sequenced here — owned by a different concept,
  confirmed by founder to run on its OWN independent timeline, not gated by
  or gating this build)
  Route B activation (poster-based) — blocked on Poster Acquisition Engine
  Phase 1 shipping AND its DPPA consent question being resolved. Do not
  schedule vendor Route B work until that concept has its own confirmed
  build session. Sessions A–F above proceed regardless of that concept's
  status.
```

---

*Design document — not a build session. Two routes to prove presence, one
scanned key, one company that owns the debt. Reality before build: this
document reconciles the concept's design against the code that already
exists, so the next session builds on facts, not assumptions.*
