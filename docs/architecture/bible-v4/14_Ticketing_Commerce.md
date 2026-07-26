# ZUKA BIBLE V4 — VOLUME 14
# Ticketing, Entry & Commerce

> **Part IV — Experience & Commerce** · **Authority Level 3**
> Synthesises: ZUKA.md §8-12,22,23,30, Canonical Volume XXI (Ticketing)

---

## Chapter 1 — The Ticket Provenance Doctrine (the closed system)

Every ticket in a Zuka bucket must have arrived through a Zuka-native process. Enforced
at the database level via a deferred constraint trigger — no ticket exists without a
valid origin record.

```
Valid origins:
  purchase · gift_transfer · promotion · points_redemption ·
  comp_issue · group_split · refund_reissue · migration (admin only)

Any attempt to inject an external ticket without an authorised origin fails at
the transaction level. The bucket cannot be poisoned.
```

---

## Chapter 2 — Ticket Classes & Tiers

```
CLASSES: standard · group · table · vip · streaming · comp · staff
Up to 8 tiers per gathering: price points, access levels, quantities,
time-limited availability, per-tier capacity.
```

---

## Chapter 3 — The Ticket Lifecycle State Machine

```
AVAILABLE → PAYMENT_PENDING → ISSUED → CHECKED_IN
                           → FAILED
ISSUED → GIFT_INITIATED → GIFT_PENDING → ACCEPTED (→ ISSUED, new owner)
                                       → REJECTED (→ returns to donor)
                                       → EXPIRED (72h → returns to donor)
ISSUED → TRANSFERRING → ISSUED (new owner)
Terminal: CHECKED_IN · EXPIRED · REFUNDED · REVOKED
```

---

## Chapter 4 — The QR Token Architecture

```
Every ticket has a qr_token (32-byte random hex). It is:
  Rotated on every significant state change
  Invalidated immediately when a ticket enters gift escrow
  Re-issued fresh when a gift is rejected/expired
  Permanently nulled after check-in
```

---

## Chapter 5 — The Smart Bucket QR (inverts ticket scanning)

```
TRADITIONAL: user finds ticket → presents ticket QR → scanner admits.
SMART BUCKET: user presents ONE persistent Bucket QR → scanner reads bucket →
  server matches the bucket to a valid ticket for THIS event → admits.

The user never searches their wallet. They raise their phone. Zero-tap entry
via a lock-screen widget.

Format: ZUKA:BKT:v1:[40-char-token]:[HMAC-SHA256-hash]
  Token has no relationship to any user identifier.
  HMAC prevents forgery. Meaningless without the server.
```

---

## Chapter 6 — Scanner Session Binding (contextual-blind security)

```
Every scanner session is bound at creation to a specific event_id, gate_id, and
time window via a signed JWT (RS256, short-lived). The scanner is BLIND outside
its session scope — it cannot probe other events, gates, or time windows
regardless of what QR it scans.

The matching engine runs server-side. The scanner receives only a boolean
result + minimum display data (name, ticket type, gate). It never sees bucket
contents.
```

---

## Chapter 7 — The Matching Engine

```
Receive { bucket_token, scanner_JWT, timestamp }
1. Verify scanner JWT (signature, expiry, nonce)
2. Verify scan timestamp within session window
3. Verify bucket_token HMAC integrity
4. Lookup user from bucket_token
5. Query tickets WHERE owner=user AND event=session.event
   AND status='owned' AND valid window active
6. If found: atomically check-in, rotate QR, log
7. Return: admitted + minimum display data, OR denied (reason withheld)

Display states:
  GREEN  ADMITTED       name, ticket type, entry zone, timestamp
  RED    NOT VALID      "No ticket for this event." No detail.
  AMBER  ALREADY IN     gate + time of previous scan → escalate to supervisor
```

---

## Chapter 8 — Offline Capability (Scanner Pro)

```
On connectivity loss, the scanner operates against a local cached manifest:
  Zuka buckets:    Bloom filter (1,000 attendees ≈ 2KB, 0.1% false positive)
  External tickets: SHA-256 hash set (exact match)
  Capacity:        last known count + local increment

Offline scans queue in an encrypted on-device DB, sync on reconnect.
Capacity overruns are tolerated over false denials — deliberate trade-off.

Staff hierarchy: organiser → supervisor (override) → gate_operator (no override).
Gate operators authenticate via 6-digit SMS PIN — no Zuka account needed.
Heartbeat every 30s; suspended roles lock on next heartbeat.
```

---

## Chapter 9 — Gift Tickets (the viral acquisition path)

```
A ticket is OWNED or IN ESCROW. A gift places it in escrow — belongs to nobody
until accepted or rejected. One ticket, one owner, always.

When gifted to a non-Zuka user:
  SMS + WhatsApp → gift landing page (event, donor name, message, visual) →
  "Claim your ticket — download Zuka" → app download with transfer_token deep
  link → on signup, gift surfaces as the first app experience.

Highest-trust acquisition channel (a gift from someone you trust), at near-zero
cost. 48-72h non-extendable expiry. QR rotates on every state change.
Group/table tickets gift as seat-level individual gifts.
```

---

## Chapter 10 — The Five Commerce Integrity Rules

```
RULE 1  One ticket per check-in (dedup in Scanner + server)
RULE 2  Payment verified before ticket issued (no speculative issuance)
RULE 3  Refunds only on verified cancellation or organiser authorisation
RULE 4  Revocation is always human (ADR-014)
RULE 5  Gift expiry is non-negotiable — no override
```

---

## Chapter 11 — Payments & Checkout (ZUKA.md §22)

```
Methods (Uganda launch): MTN MoMo · Airtel Money · Visa/Mastercard
  (via Flutterwave) · Zuka Event Wallet

The 3-Tap Checkout (MVP Proof 3):
  Tap 1 Get Tickets → Tap 2 Select type/quantity → Tap 3 Confirm payment

Mobile Money flow: select MoMo → enter phone → USSD push → PIN on own device
(never on Zuka) → tickets in bucket within 10s.

Failure handling: network timeout auto-retries once; insufficient funds suggests
wallet top-up; system verifies status before showing failure (prevents
double-charge anxiety); multi-ticket purchase is all-or-nothing.
```

---

## Chapter 12 — The Zuka Event Wallet

```
A closed-loop prepaid ledger (lighter regulatory class than e-money).
Top up with MoMo/card; spend on tickets in 1 tap.
Every credit/debit is an immutable ledger entry.
Max balance UGX 500,000 (Bank of Uganda compliance). Min top-up UGX 5,000.
Non-refundable except on account deletion (full refund).
```

---

## Chapter 13 — Organiser Payouts (ZUKA.md §23)

```
Sold → escrow → event passes → payout initiated within 48h → arrives within 24h.
Total cycle under 72h from event date — faster than Eventbrite (a stated
competitive differentiator).

Methods: MTN MoMo (instant) · Airtel Money (instant) · Bank (2-3 days).
Deductions (shown before payout): Zuka commission (5% MVP, 6.5% Uganda scale) +
payment processing (at cost, disclosed). No surprise deductions.
Funds never lost: failed payouts held in Zuka balance indefinitely until claimed.
```

---

## Chapter 14 — Pulse Points Economy (ZUKA.md §13, Phase 2)

```
EARN: sign-up 100 · first check-in 150 · check-in 50 · 7-day streak 200 ·
  Vibe Drop 30 · ticket purchase 100 · gift accepted 75 · referral 200/300.
BURN: free off-peak entry 500 · 10% discount 300 · free drink 400 ·
  priority entry 250 · streaming pass 200.

Protection: never purchasable with cash · GPS-proximity check-in verification ·
  max 500/day · referral chain depth limit 3 · never expire while active.

Venue Reward Pool: venues buy point packages (Zuka 20% margin) to distribute as
loyalty incentives — a three-way value loop (user earns → redeems at venue →
venue pays Zuka).
```

---

## Chapter 15 — Ticketing & Commerce Domain Events

```
TicketPurchased · TicketClaimed · TicketCheckedIn · TicketTransferInitiated ·
TicketTransferCompleted · TicketGiftPending · TicketGiftAccepted ·
TicketGiftDeclined · TicketGiftExpired · TicketRefunded · TicketRevoked ·
PaymentIntentCreated · PaymentIntentVerified · PayoutInitiated ·
GatheringTierCreated · PaymentIntentExpired · PaymentIntentFailed ·
ScannerSessionCreated · ScannerManifestGenerated · VendorCompanyCreated ·
VendorCompanyLocked ·
VendorCompanyUnlocked · VendorTeamCreated · VendorPresenceActivated ·
VendorPresenceBlocked · VendorStatusChanged ·
VendorTermsAccepted · MenuTemplateSaved · MenuTemplateImported ·
VendorOrderPlaced · VendorOrderPreparing · VendorOrderReady ·
VendorOrderCollected · VendorOrderRejected · VendorOrderCancelled ·
VendorOrderExpired · TicketResurrected · PaidTicketRefundQueued ·
TicketRefundExecuted

Tier amendment + scanner-session lifecycle (registered 2026-07-20, emitted by
20260718150000 amend_tier / 20260720120000 deactivate_scanner_session RPCs):
TierAmended · ScannerSessionDeactivated
```

**Emitted-event provenance (registry completeness, S041):**
- `ScannerManifestGenerated` — aggregate `Gathering`. An offline-scan manifest
  (Bloom filter of valid tickets + capacity snapshot) was generated for a
  gathering's scanner session, enabling device-side offline check-in. Emitted at
  `supabase/migrations/20260703100002_fn_scanner_manifest.sql:157`.

**`aggregate_id` convention — `VendorPresenceActivated` vs `VendorPresenceBlocked`:**
these two events key `aggregate_id` differently. `VendorPresenceActivated.aggregate_id`
is the `vendor_presence` row id. `VendorPresenceBlocked.aggregate_id` is the
ticket/credential id instead, because on the blocked path no `vendor_presence` row
has been created yet — there is nothing to key it to. Any projection reading these
events must branch on event type to resolve the correct aggregate.

---

## Chapter 16 — The Vendor Template (category-typed, multi-template, plan-capped)

*Graduated to prose by ADR-035 (ACCEPTED 2026-07-21), built in S075 2026-07-22.
Before S075 the durable vendor menu library carried no category link; this chapter
is the authorization to record the category-typed Template as canon.*

A vendor's durable, always-editable, **multi-template** account is not a new entity
— it **is** S022's `menu_template` (owned by `vendor_company`, versioned, keyed
`(vendor_company_id, name)`, one active version per name). ADR-035 rejected a
parallel `vendor_template` entity (it would fork S022's import/snapshot machinery).
A vendor's "Snacks booth" and "Makeup booth" are distinct named `menu_template`
lineages on one company. S075 adds the single load-bearing link and its guards:

- **Category-typed** — `menu_template.vendor_category_id` (nullable until first
  categorized use) lets an event filter booths to the categories it accepts and
  lets the server bind fee to category. An *uncategorized* booth can never be
  credentialed against a paid tier.
- **Issue-time category-match guard** — when a booth is named at credential issue
  (`register_vendor_credential.p_menu_template_id`), `template.vendor_category_id`
  must equal the tier's `vendor_category_id`, rejected before any capacity is
  reserved. This is **booth-first binding**: the client picks the booth, the tier
  follows. *Residual (S075):* the booth argument is optional and
  `import_menu_template` does not yet category-check, so a raw client retains a
  bounded, self-selected fee-underpayment path — the door/organiser is the human
  backstop (ADR-014). The import-time category check is **owed follow-up**; the
  hole is narrowed, not closed.
- **Category-lock after first paid use** — once a lineage has issued a non-terminal
  vendor credential, its category is immutable (it bound a fee). Freely editable —
  including category — before first paid use. A different service is a new booth.
- **Plan-cap seam** — `vendor_company.plan` (free-tier default) caps the number of
  *active* templates (free = 3), enforced server-side on a new named lineage. Real
  paid tiers / entitlement matrix / billing are **sub-project E** (its own ADR +
  legal + security); this ratifies only the field + free cap.
- **Vendor status** — `vendor_company.vendor_status`, durable, auto-granted true
  (idempotent) on the first successful vendor-credential issue. Gates the client's
  `/vendor` template manager.
- **Push-to-live (O1)** — the durable library is always editable; the attendee
  listing is the frozen `vendor_event_menu` snapshot; the vendor triggers an
  explicit `push_menu_template_to_live(team, gathering)` to re-import the lineage's
  current active version. Attendees never see prices shift mid-event.

**Money posture (ADR-026 §5) is untouched:** the credential fee is platform-fixed
per category and settlement-excluded; none of the above accrues to
`commission_debt_ledger` or lets a client influence the fee.

*Canonical-data-model note (Vol 04):* the vendor entity family is not yet
canonicalized in Vol 04 — this chapter graduates the **category-typed Template**
in the commerce volume where the vendor entities already live; a fuller Vol 04
canonical-entity graduation of `vendor_company` / `vendor_team` / `menu_template`
remains outstanding.

---

> **Volume 14 — Ticketing, Entry & Commerce**
> The bucket cannot be poisoned. The user just raises their phone.
