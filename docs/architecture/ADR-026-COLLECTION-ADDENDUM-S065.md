# ADR-026 Addendum — Vendor Credential-Fee **COLLECTION** Posture vs NPS Act 2020

> **Type:** zuka-legal reviewer-posture addendum (session **S065**, 2026-07-19).
> **Extends:** [`ADR-026.md`](ADR-026.md) — which governs ticket-payment
> **settlement/payout**. This addendum extends that analysis to the **collection**
> step and does **not** restate or re-litigate the settlement decision.
> **Resolves:** S024 **F-③** (standing-owed zuka-legal money-routing / NPS-Act
> aggregator review of the credential-fee *collection* path) and the backlog row
> `legal-credential-fee-collection`.
> **Lead gate:** `zuka-legal` (per CLAUDE.md routing table, `legal_compliance`).
>
> ⚠️ **STATUS OF EVERY CONCLUSION BELOW:** *reviewer posture, pending qualified
> Ugandan counsel sign-off per CLAUDE.md §8.* This is legal-awareness surfacing,
> **not a legal ruling.** Engaging Ugandan counsel on the NPS-Act characterisation
> remains the founder's standing action item (bible-v4 Vol 23; ADR-026 Disposition).

---

## 0 · The question this addendum answers

ADR-026 covers **payout**: organiser ticket revenue must settle organiser-direct via
Flutterwave subaccount splits, and §5 (`ADR-026.md:147-182`) makes it a **binding
invariant** that every settlement/payout path **excludes** `ticket_type='vendor'`
rows, because a vendor credential fee is *ZUKA's own B2B revenue, not organiser
ticket revenue* (`ADR-026.md:154-167`).

ADR-026 never analysed the **collection** step. S024 flagged that gap explicitly and
deferred it on the record:

> **F-③** — *"a zuka-legal money-routing / NPS-Act aggregator review of the
> credential-fee **collection path** (`register_vendor_credential` → Flutterwave)
> is deferred to the S025-adjacent collection/settlement session."*
> — `docs/sessions/archive/S024-2026-07-09-crosswalk-write-path.md:380-383`

The precise question: **does collecting `ticket_type='vendor'` credential-fee money
into the same undifferentiated ZUKA Flutterwave merchant account as organiser ticket
money — with no subaccount split and no routing branch at the collection step —
create NPS Act 2020 money-transmission / aggregator exposure AT THE COLLECTION
step**, independent of ADR-026's settlement-side exclusion (which governs payout
only)?

---

## 1 · The live collection path (verified against the tree, 2026-07-19)

The credential-fee money rides the **identical, ticket-type-agnostic** rail as an
organiser ticket sale:

1. **Mint.** `register_vendor_credential()` creates a `ticket_type='vendor'`
   `PAYMENT_PENDING` ticket through the shared `_issue_pending_ticket` engine, with
   a server-derived `price_ugx`, a `tx_ref`, and `owner` = the vendor Person who
   pays. — `supabase/migrations/20260709120000_fn_vendor_tier_read_surface_isolation.sql:447-458`
2. **Collect.** `initiate-flutterwave-payment` POSTs to Flutterwave `/v3/payments`
   with **no `subaccounts` key and no `ticket_type` branch** — it accepts *any*
   `PAYMENT_PENDING` ticket the caller owns and settles it into ZUKA's single
   Flutterwave merchant account. — `supabase/functions/initiate-flutterwave-payment/index.ts:120-135`
3. **Confirm.** `flutterwave-webhook` (signature + server-to-server verify) calls
   `confirm_ticket_payment`, which transitions `PAYMENT_PENDING → ISSUED`, also
   ticket-type-agnostic. —
   `supabase/functions/flutterwave-webhook/index.ts:116-148` ·
   `supabase/migrations/20260714090000_fn_confirm_ticket_payment_inflight_gathering_terminal_guard.sql:86-524`

**Premise confirmed live:** vendor credential-fee money and organiser ticket money
collect into the **same undifferentiated ZUKA Flutterwave account**, with **no
split and no routing branch** distinguishing them at collection. The S024 F-③ gap
is real and still open.

---

## 2 · Reviewer-posture analysis

### A · Is the credential fee ZUKA's own revenue, or third-party money?

**Reviewer posture (pending counsel): it is ZUKA's own B2B sale proceeds, collected
as merchant-of-record — NOT third-party intermediation.**

The NPS Act 2020 aggregator / money-transmission trigger is the **holding or
transmission of a *third party's* funds** — money that economically belongs to
someone else and passes *through* ZUKA to a downstream payee. The test is
**beneficial ownership** of the collected funds, not the rail they ride.

Applied to the credential fee:
- The payer is the **vendor Person themselves** (`register_vendor_credential` sets
  `owner` = the vendor and mints under `auth.uid()` —
  `...20260709120000...:447-458`).
- The economic counterparty is **ZUKA**: the vendor is buying a listing credential
  *from ZUKA*. There is **no organiser leg, no downstream beneficiary.** ADR-026
  states it directly — *"a vendor fee is **ZUKA's own B2B revenue** … Zuka charging
  a vendor for a listing credential, settled to Zuka directly"* (`ADR-026.md:159-161`).
- So the money landing in ZUKA's Flutterwave account is ZUKA collecting **its own
  sale proceeds** — ordinary merchant commerce, the same posture as any Ugandan
  business collecting card/MoMo for its own product.

Contrast the organiser ticket: the attendee's payment economically belongs to the
**organiser** (a third party — `gathering.organiser_id`), which is *why* ADR-026 §1
forces the organiser subaccount split. The credential fee has **no such third
party.** On this reasoning, **undifferentiated collection into ZUKA's single
account is, for the vendor-fee leg, the correct and non-aggregator posture —
because ZUKA *is* the payee.**

### B · Does ADR-026's subaccount-SPLIT mitigation extend to collection?

**Reviewer posture (pending counsel): No — and applying a split to the vendor leg
would be affirmatively WRONG. Collection and settlement are mirror images of one
principle: vendor money must never be given a third-party leg.**

The subaccount split exists to *create* a third-party settlement leg where a third
party genuinely exists (the organiser). For vendor fees there is no third party, so:
- **Settlement side (ADR-026 §5, `ADR-026.md:147-167`):** *exclude*
  `ticket_type='vendor'` from every payout/split; sweeping it into an organiser
  split would "route its own vendor-fee income to a third-party organiser as if it
  were that organiser's ticket sale … exactly the payment-aggregator posture."
- **Collection side (this addendum):** the mirror statement is *do not attach a
  subaccount to the vendor-fee charge* — doing so would **fabricate a third-party
  payee** for money that has no third party, manufacturing at capture time the very
  aggregator flow §5 forbids at payout.

Therefore the current state — `initiate-flutterwave-payment/index.ts:120-135`
posting with **no `subaccounts` key** — is, for the vendor leg specifically, the
**structurally correct** outcome. The absence of a split is **not a gap for vendor
fees; it is the right answer.** The two invariants are **one principle stated at two
points in the money's lifecycle**: vendor-fee money is ZUKA's, start to finish, and
must be given no third-party leg at either collection or payout.

### C · The forward risk — a collection-side companion to ADR-026 §5

The exposure is **latent today, activated later.** Today the collection endpoint is
ticket-type-agnostic and that is *safe* precisely because there is **no split at
all**. The danger arises the moment ADR-026 §1's organiser split is coded into
`initiate-flutterwave-payment`: if that split logic naively attaches a subaccount to
**every** paid ticket, it will silently sweep `ticket_type='vendor'` rows into
whatever organiser subaccount the gathering carries — and because a credential
ticket *does* hang off a gathering (`p_gathering_id` in `register_vendor_credential`),
the naive join **would find one**. That single omission converts a correct
non-aggregator flow into aggregator routing **at the collection step**.

**Reviewer-posture recommendation — file this as a collection-side companion to
ADR-026 §5 (founder/counsel to ratify; this review flags, it does not amend the
founder-locked ADR):**

> *"The organiser subaccount split at the collection step
> (`initiate-flutterwave-payment`) MUST exclude `ticket_type='vendor'` rows. A
> vendor-credential charge MUST be POSTed to Flutterwave with **no `subaccounts`
> key** — it settles to ZUKA's main merchant account as ZUKA's own B2B revenue.
> Applying an organiser subaccount to a vendor-fee ticket would route ZUKA's own
> income to a third-party organiser at capture time — aggregator routing under the
> NPS Act 2020."*

**Enforcement (mirror of the §5 pgTAP guard):** when the split lands, ship it with a
hard-failing test asserting that a `ticket_type='vendor'` ticket produces a
Flutterwave payload with **no `subaccounts` key** — the collection-side twin of the
backlog guard `settlement-vendor-ticket-exclusion-test-pgtap` (`ADR-026.md:176-181`).
The split and its vendor-exclusion are **inseparable**; shipping the split without
the exclusion is the failure mode.

> **Guard now EXISTS (session S068, 2026-07-20):**
> `supabase/functions/initiate-flutterwave-payment/vendor_exclusion.test.ts` — a Deno
> source-scan trip-wire that hard-fails if a `subaccounts` key reaches the Flutterwave
> POST body without a `ticket_type`-vs-`'vendor'` exclusion, and asserts today's
> zero-`subaccounts` state so the §D sequencing rule cannot be silently broken. It is
> armed **before** the split exists — the collection-side twin of S067's payout-side
> pgTAP guard (`supabase/tests/s062_settlement_vendor_ticket_exclusion.sql`). No
> collection code changed; §D's disposition below is unaltered.
>
> **AUTHORED, NOT WIRED — this guard executes in NO runner today (RS068 F1).**
> No CI job runs Deno tests: `.github/workflows/supabase-checks.yml` runs
> `flutter analyze`/`test`, the ADR-027 checks and `supabase/tests/*.sql` only
> (`grep -rn "deno" .github/workflows/` → no match), and the `zuka` loop globs
> `*.sql`. The guard is therefore **dormant until backlog item
> `ci-runs-no-deno-tests-github` lands** — it will not trip on its own, and a green
> CI run says NOTHING about it.
>
> **Until that item lands, the ADR-026 §1 organiser-split build MUST run it by hand
> and paste the output into its session record:**
> ```
> deno test --allow-read \
>   supabase/functions/initiate-flutterwave-payment/vendor_exclusion.test.ts
> ```
> (deno is installed at `~/.deno/bin/deno`, off PATH.) Treat "the guard exists" as
> "the guard is written," not "the guard is enforcing." §C's obligation rests on a
> human running it.

### D · Pre-scale action items — fix-now vs leave-as-is

**Reviewer-posture disposition: NO code change is required today.** With *no* split
anywhere in `initiate-flutterwave-payment`, vendor fees already land in ZUKA's own
account as ZUKA's own revenue — the correct posture (§A/§B). There is no third-party
misrouting to remediate because there is no routing branch at all yet. **This
remains true when the `seed-vendor-credential-fees` item ships real vendor-fee
money** — seeding fee rows does not introduce a split.

**MUST fix BEFORE real money — but *gated on the split being built*, not immediate:**
1. **The collection-side exclusion (§C) MUST land in the SAME changeset as
   ADR-026 §1's organiser split — never after.** This is the one hard sequencing
   constraint. (Nothing to exclude today; the obligation activates with the split.)
2. **Counsel sign-off on the collection-step characterisation itself** — that a
   credential fee collected into ZUKA's own merchant account is "own-revenue
   commerce," not intermediation under the NPS Act 2020. Mirrors ADR-026's
   Disposition §2 gate (counsel + subaccount onboarding). Standing founder item
   (CLAUDE.md §8, Vol 23).

**Acceptable to LEAVE AS-IS pending counsel:**
- The ticket-type-agnostic collection endpoint
  (`initiate-flutterwave-payment/index.ts:120-135`) and the shared
  `_issue_pending_ticket` / `confirm_ticket_payment` engines. **Sharing the rail is
  not itself aggregation** — ADR-026 already accepted the shared `ticket` table
  (`ADR-026.md:154-158`); what matters is that money is never *split to a third
  party*, and today it never is.

**Net:** the exposure is a **forward code-sequencing risk, not a present defect.**
File the §C companion invariant now (as this addendum + a backlog row) so it cannot
be forgotten when the split is built; change **no collection code today.**

### E · DPPA 2019 / other flags at the vendor collection step

- **Payer PII to a third-party processor (existing, already minimised).**
  `initiate-flutterwave-payment/index.ts:113-118,131` forwards the payer's
  email/phone/name to Flutterwave. For a vendor fee the payer is the *vendor
  Person*, so **vendor PII** crosses to Flutterwave (cross-border / GDPR-adjacent if
  the vendor is diaspora). The S007 minimisation posture (minimal fields, no
  raw-body logging — `index.ts:113-115,137-144`) already covers this; **no new
  defect.** *Action:* log vendor-fee payer PII in the **DPPA processing register as
  a distinct purpose** ("B2B credential-fee collection"), since the lawful-basis
  analysis differs (contract-with-vendor vs contract-with-attendee).
- **Append-only ↔ erasure tension (carried, S020 item #6,
  `docs/sessions/archive/S020-2026-07-05-vendor-organiser-setup.md:407`).**
  `vendor_terms_acceptance` / `VendorTermsAccepted` retain `person_id` in
  append-only stores. Standing DPPA posture item; the credential-fee flow is the
  trigger that creates those rows. Same register entry, no new gate.
- **No new consent surface** is introduced at collection beyond what ticketing
  already carries; the flag here is *purpose-labelling and lawful-basis
  differentiation*, not a missing consent gate.

---

## 3 · Overall reviewer posture (pending qualified Ugandan counsel sign-off, CLAUDE.md §8)

Undifferentiated collection of vendor credential-fee money into ZUKA's single
Flutterwave account does **not**, on this reasoning, create NPS Act 2020 aggregator /
money-transmission exposure **at the collection step** — because ZUKA is the
beneficial payee of its **own B2B revenue**, with **no third-party leg to
intermediate**. The no-split state is **correct, not deficient**.

The real risk is **forward**: ADR-026's organiser subaccount split, once built,
could naively sweep `ticket_type='vendor'` into a third-party organiser's subaccount
— which **would** be aggregator routing.

**Recommendation:** adopt the §C collection-side companion to ADR-026 §5 and require
it to ship **in lockstep** with the split, backed by a hard-failing exclusion test.
**No collection-code change is needed today.**

> **Verify with qualified Ugandan legal counsel before proceeding.** This addendum
> is an input for that counsel, never final advice.

---

## 4 · Provenance

- **Session:** S065 (2026-07-19), `legal_compliance` domain, review-only (no code
  change).
- **Lead gate:** `zuka-legal` — verdict **PASS / no present defect; forward
  code-sequencing risk flagged** (§C).
- **Cited (live tree, verified 2026-07-19):**
  - `supabase/migrations/20260709120000_fn_vendor_tier_read_surface_isolation.sql:447-458,462-463`
  - `supabase/functions/initiate-flutterwave-payment/index.ts:113-135,137-144`
  - `supabase/functions/flutterwave-webhook/index.ts:116-148`
  - `supabase/migrations/20260714090000_fn_confirm_ticket_payment_inflight_gathering_terminal_guard.sql:86-524`
  - `docs/architecture/ADR-026.md:147-182`
  - `docs/sessions/archive/S020-2026-07-05-vendor-organiser-setup.md:406-407`
  - `docs/sessions/archive/S024-2026-07-09-crosswalk-write-path.md:374-383`

*Subordinate to SOUL.md and ZukaBibleV4. Flags and proposes — people and counsel*
*decide. ZUKA is not a payment aggregator; this addendum is how the vendor-fee leg*
*stays ZUKA's own revenue, at collection as at payout.*
