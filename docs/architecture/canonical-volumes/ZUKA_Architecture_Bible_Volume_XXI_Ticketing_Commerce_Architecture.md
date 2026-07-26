# ZUKA Architecture Bible
# Volume XXI — Ticketing & Commerce Architecture

> **Series:** ZUKA Architecture Bible
> **Volume:** XXI of N
> **Status:** Canonical — provides the canonical Ticket entity and all
>              associated commerce systems referenced in ZUKA.md
> **Depends on:** Volume II (Entity taxonomy), Volume IV (Gatherings Engine),
>                  Volume VII (Event Sourcing), Volume VIII (Knowledge Graph),
>                  Volume XIX (Flutterwave connector), Volume XX (DRL Activation)
> **Required by:** All product surfaces involving ticket purchase, entry
>                  management, or commerce

---

# Preface

ZUKA.md specifies the ticketing system across Sections 5-16 with considerable depth — the Smart Bucket QR, Scanner Pro, gift ticket escrow model, ticket provenance chain, and the three-proof-in-three-taps MVP goal. The parallel engineering session's `ZUKA_Canonical_Schema_Pack.md` began translating this into PostgreSQL, but without the TemporalEntity envelope (Volume VII), the embedding field (Volume II), provenance tracking (Volume VIII), or the Memory Layer separation (Volume V). The Domain Event catalog from the same session is broadly well-aligned.

This volume provides the canonical specification, grounded fully in this Bible's entity model. It is not a replacement for ZUKA.md's product vision — it is the architectural translation of that vision into structures consistent with everything this Bible has built since Volume II.

The three MVP proofs this volume enables:
1. A ticket can be bought in under 3 taps
2. A venue can go live and be discoverable within 60 seconds
3. A person can find something happening near them right now with zero friction

---

# Part I — The Canonical Ticket Entity

---

## Chapter 1 — Extending Volume II's Taxonomy

Volume II, §3.10 registered Ticket as an entity type within the broader Opportunity/Commerce taxonomy. This chapter provides its complete canonical structure.

```typescript
Ticket {
  // ── IDENTITY ──────────────────────────────────────────────
  ticket_id:             UUID
  ticket_ref:                TEXT
  // Human-readable reference: 'TKT-XXXXXXXX'
  // Used in UI, receipts, and Scanner Pro display

  // ── TEMPORAL ENVELOPE (Volume VII Ch.3 — mandatory) ──────
  valid_from:                Timestamptz  // when ticket becomes usable
  valid_to:                      Timestamptz  // when ticket expires
  observed_at:                       Timestamptz  // when system learned
  recorded_at:                           Timestamptz  // when written
  confidence:                                Float(0.0-1.0)
  version:                                       Integer

  // ── GATHERING LINKAGE ────────────────────────────────────
  gathering_id:              UUID
  tier_id:                       UUID
  // Links to the GatheringTier (Chapter 2) — price, capacity,
  // perks for this specific ticket category

  // ── OWNERSHIP ─────────────────────────────────────────────
  owner_person_id:               UUID
  // The CURRENT holder — changes on transfer (Chapter 4)
  // Per Volume II §3.1 — Person, not "user"

  original_purchaser_person_id:      UUID
  // Never changes — the person who first purchased this ticket
  // Separate from owner to support the gift ticket model (Ch.5)

  // ── PROVENANCE CHAIN ─────────────────────────────────────
  provenance: {
    purchase_event_id:           UUID
    // The DomainEvent ID of the TicketPurchased event (Ch.8)
    transfer_history:                TicketTransferRecord[]
    // Append-only chain of every Person who has held this
    // ticket — each transfer is a new record, never an edit
    // Per Volume VII's Temporal Truth Principle
  }

  // ── STATUS STATE MACHINE ─────────────────────────────────
  status:                          TicketStatus
  // See Chapter 3 — the lifecycle state machine

  // ── SMART BUCKET QR ──────────────────────────────────────
  smart_bucket_token:              TEXT
  // Cryptographically signed, expiring token embedding:
  //   ticket_id, gathering_id, valid_from, valid_to
  // Used by Smart Bucket QR (Chapter 6) — NOT a static QR;
  // regenerated on each check-in attempt within the
  // validity window
  smart_bucket_token_expires_at:       Timestamptz

  // ── COMMERCE ──────────────────────────────────────────────
  paid_amount:                         Decimal
  currency_code:                           CurrencyCode  // 'UGX' etc.
  payment_intent_id:                           UUID
  // References the Flutterwave payment intent (Vol XIX Ch.6)

  // ── GIFT STATE ────────────────────────────────────────────
  is_gift:                             Boolean
  gift_state:                              GiftState | null
  // See Chapter 5 — only populated when is_gift = true

  // ── MEMORY LINKAGE ────────────────────────────────────────
  memory_id:                           UUID | null
  // Set AFTER check-in when the attendance is elevated to
  // a Memory (Vol V Ch.7 Layer 1→2→4 curation path)
  // NOT set at purchase — purchase is not yet a memory

  // ── SEMANTIC EXTENSION (Volume VIII Ch.18 — mandatory) ───
  tags:                                Tag[]
  attributes:                              JSONB
  relationships:                               EdgeReference[]
  signals:                                         Signal[]

  // ── EMBEDDING (Volume II Ch.16 — mandatory) ───────────────
  embedding:                               Vector(1536)

  // ── AUDIT ─────────────────────────────────────────────────
  created_at:                              Timestamptz
  updated_at:                                  Timestamptz
}
```

---

## Chapter 2 — The GatheringTier Entity

```typescript
GatheringTier {
  tier_id:              UUID
  gathering_id:             UUID
  tier_name:                    TEXT    // 'Early Bird' | 'Regular' | 'VIP'
  price:                            Decimal
  currency_code:                        CurrencyCode
  capacity:                                 Integer
  tickets_sold:                                Integer  // computed, Vol VII events
  tickets_checked_in:                              Integer  // computed
  perks:                                               TEXT[]
  // Human-readable list of tier-specific perks
  // e.g., ['VIP entrance', 'Complimentary drink', 'Backstage access']
  sale_opens_at:                                   Timestamptz
  sale_closes_at:                                      Timestamptz
  visible_to_public:                                       Boolean
}
```

---

# Part II — The Ticket Lifecycle State Machine

---

## Chapter 3 — States and Transitions

```
                    ┌──────────────────┐
                    │    AVAILABLE      │
                    │  (tier has        │
                    │   remaining cap)  │
                    └────────┬─────────┘
                             │ purchase initiated
                             ▼
                    ┌──────────────────┐
                    │  PAYMENT_PENDING  │
                    │  (Flutterwave     │
                    │   intent created) │
                    └────────┬─────────┘
                      │              │
               payment         payment
               confirmed         failed
                      │              │
                      ▼              ▼
            ┌──────────────┐  ┌──────────────┐
            │    ISSUED     │  │    FAILED     │
            │ (held by owner│  │ (no ticket;  │
            │  person_id)   │  │  refund auto)│
            └────────┬──────┘  └──────────────┘
                     │
          ┌──────────┴──────────────────┐
          │                             │
    transfer         gift pending      check-in
    initiated        acceptance        attempted
          │                │            │
          ▼                ▼            ▼
    ┌──────────┐  ┌──────────────┐  ┌──────────────┐
    │TRANSFERRING│  │GIFT_PENDING  │  │ CHECKED_IN   │
    │(escrow;    │  │(recipient    │  │(entry granted;│
    │awaiting    │  │must accept   │  │attendance     │
    │recipient)  │  │within 48h)   │  │Memory created)│
    └─────┬──────┘  └──────┬───────┘  └──────────────┘
          │           │         │
    recipient     accepted   declined/expired
    accepts           │           │
          │           ▼           ▼
          │     ┌──────────┐  ┌──────────────┐
          │     │  ISSUED   │  │   RETURNED   │
          │     │(new owner)│  │(back to buyer│
          ▼     └──────────┘  │ per Vol VII  │
    ┌──────────┐               │ Temporal     │
    │  ISSUED   │              │ Truth)       │
    │(new owner)│              └──────────────┘
    └──────────┘

    ── PARALLEL TERMINAL STATES ──────────────────────────────

    EXPIRED      — gathering has ended; ticket not used
    REFUNDED     — organiser cancelled event; auto-refunded
    REVOKED      — fraud investigation confirmed (Vol XIII Part V);
                   only a human can set this state, never automated
```

---

# Part III — Domain Events for Ticketing

---

## Chapter 4 — The Ticketing Event Registry

Extending Volume II, Chapter 9's existing event registry with ticketing-specific events:

```
TICKETING EVENTS (extending Volume II Ch.9 registry):

  TicketPurchased
    Payload: ticket_id, gathering_id, tier_id, owner_person_id,
             paid_amount, currency_code, payment_intent_id,
             smart_bucket_token, smart_bucket_token_expires_at
    Side effects: tier.tickets_sold incremented,
                  PaymentIntentCompleted verified first

  TicketCheckedIn
    Payload: ticket_id, gathering_id, gate_id, check_in_method,
             checked_in_person_id, checked_in_at
             (check_in_method: 'smart_bucket' | 'manual_qr' |
              'name_list' | 'supervisor_override')
    Side effects: status → CHECKED_IN,
                  tier.tickets_checked_in incremented,
                  AttendanceMemoryCreated triggered (Ch.9)

  TicketTransferInitiated
    Payload: ticket_id, from_person_id, to_person_id,
             transfer_reason ('gift' | 'resale' | 'personal')
    Side effects: status → TRANSFERRING, escrow activated

  TicketTransferCompleted
    Payload: ticket_id, new_owner_person_id, transfer_event_id
    Side effects: status → ISSUED with new owner_person_id,
                  provenance.transfer_history appended

  TicketGiftPending
    Payload: ticket_id, gifter_person_id, recipient_phone_e164,
             recipient_person_id (null if not yet on ZUKA),
             gift_message (optional, max 200 chars),
             expiry_at (gifter_purchase_time + 48h)
    Side effects: status → GIFT_PENDING, is_gift = true,
                  gift_state → PENDING_ACCEPTANCE,
                  SMS notification to recipient (Vol XIX Ch.7)

  TicketGiftAccepted
    Payload: ticket_id, accepting_person_id
    Side effects: gift_state → ACCEPTED,
                  status → ISSUED with new owner,
                  TicketTransferCompleted also fired

  TicketGiftDeclined | TicketGiftExpired
    Payload: ticket_id, reason
    Side effects: gift_state → DECLINED | EXPIRED,
                  status → RETURNED (back to gifter),
                  payment NOT refunded (gifter retains ticket)

  TicketRefunded
    Payload: ticket_id, refund_amount, refund_reason,
             flw_refund_reference
    Side effects: status → REFUNDED,
                  Flutterwave refund initiated (Vol XIX Ch.6)

  TicketRevoked
    Payload: ticket_id, fraud_signal_id (Vol XIII Ch.8),
             revoked_by_identity_id, revocation_reason
    Side effects: status → REVOKED
    RULE: human identity_id required in revoked_by — no
          automated system may set REVOKED status directly
```

---

# Part IV — The Smart Bucket QR System

---

## Chapter 5 — Smart Bucket Architecture

ZUKA.md Section 7 describes Smart Bucket QR as "context-aware wallet scanning — zero user input at door." This chapter provides the canonical data model.

```
SMART BUCKET DESIGN PRINCIPLES:

  NOT a static QR code — a static QR can be screenshot,
  forwarded, and used fraudulently. The Smart Bucket token
  is time-limited, cryptographically signed, and valid only
  within the gathering's check-in window.

  NOT a manual scan — the attendee presents their phone;
  the ZUKA app generates the current token; Scanner Pro
  reads it. No user action beyond opening the app.

  OFFLINE CAPABLE — the token can be verified by Scanner Pro
  offline using the shared signing key. The online sync
  happens in the background. No internet required at the
  gate.

SMART BUCKET TOKEN STRUCTURE:
  A signed JWT (or equivalent) containing:
    ticket_id: UUID
    gathering_id: UUID
    valid_from: epoch seconds
    valid_to: epoch seconds
    token_version: Integer (for key rotation)
  Signed with ZUKA's server-side signing key.
  Expires: 30 seconds from generation (regenerated on next
    app open within the check-in window).
  The 30-second expiry window prevents screenshot fraud.

SCANNER PRO VERIFICATION:
  1. Decode the JWT header + payload (no network required)
  2. Verify signature against the cached public key
     (key pre-loaded from server before doors open)
  3. Check valid_from <= now <= valid_to
  4. Check gathering_id matches the gate's configured
     gathering_id
  5. Check ticket_id has not already been used in this
     session (local deduplication table in Scanner Pro)
  6. ADMIT or DENY
  7. Queue TicketCheckedIn event for background sync
```

---

## Chapter 6 — CheckInRecord Entity

```typescript
CheckInRecord {
  check_in_id:          UUID
  ticket_id:                UUID
  gathering_id:                 UUID
  gate_id:                          UUID | null
  // Null for single-gate events (most MVP gatherings)

  checked_in_person_id:         UUID
  // The Person who checked in — may differ from
  // ticket.owner_person_id if supervisor_override

  check_in_method:              CheckInMethod
  // 'smart_bucket'       — token scanned automatically
  // 'manual_qr'          — QR displayed and scanned
  // 'name_list'          — found on door list
  // 'supervisor_override' — staff manually admitted

  checked_in_at:                Timestamptz
  synced_at:                        Timestamptz | null
  // Null if check-in occurred offline; set when synced
  offline_session_id:                   UUID | null
  // Groups check-ins from the same offline Scanner Pro
  // session for audit reconciliation

  valid_from:                       Timestamptz  // TemporalEntity
  valid_to:                             Timestamptz  // (Vol VII Ch.3)
  observed_at:                              Timestamptz
  recorded_at:                                  Timestamptz
  confidence:                                       Float(0.0-1.0)
  version:                                              Integer
}
```

---

# Part V — Gift Ticket State Machine

---

## Chapter 7 — The Full Gift Flow

```typescript
GiftState enum:
  PENDING_ACCEPTANCE   // awaiting recipient decision
  ACCEPTED             // recipient confirmed; transferred
  DECLINED             // recipient declined; ticket returned
  EXPIRED              // 48-hour window passed; ticket returned

GiftTicketRecord {
  gift_id:                 UUID
  ticket_id:                   UUID
  gifter_person_id:                UUID
  recipient_phone_e164:                TEXT
  recipient_person_id:                     UUID | null
  // null if recipient is not yet a ZUKA Person entity;
  // they receive an SMS (Vol XIX Ch.7) with an invite
  // link + gift claim flow; set when they join and claim

  gift_message:                            TEXT | null
  // Optional personalised message from gifter (max 200 chars)

  gift_state:                              GiftState
  expires_at:                                  Timestamptz
  // gifter purchase time + 48 hours

  // TemporalEntity envelope
  valid_from:                              Timestamptz
  valid_to:                                    Timestamptz
  observed_at:                                     Timestamptz
  recorded_at:                                         Timestamptz
  confidence:                                              Float
  version:                                                     Integer
}
```

```
GIFT FLOW FOR NON-ZUKA RECIPIENTS:

  1. Gifter purchases ticket and selects "Gift this ticket"
  2. Gifter enters recipient's phone number
  3. ZUKA sends Africa's Talking SMS (Vol XIX Ch.7):
     "Kiyingi James sent you a ticket to [Gathering Name]
      on [Date]. Accept here: [deeplink] (expires 48h)"
  4. Recipient clicks deeplink → ZUKA app / web claim flow
  5. Recipient creates ZUKA account (if needed) or logs in
  6. Recipient accepts → TicketGiftAccepted fired
  7. recipient_person_id populated; ticket transferred
  8. If expired: ticket returned to gifter automatically
```

---

# Part VI — Scanner Pro Architecture

---

## Chapter 8 — Zuka Scanner Pro Design

```
SCANNER PRO ROLES (extending Vol IV Ch.8's Participation roles):

  gate_supervisor
    - Full scan access
    - Can perform supervisor_override admissions
    - Can see real-time gate dashboard
    - Can sync offline sessions
    - Can assign scanner roles for this event

  gate_scanner
    - Can scan tickets (smart_bucket + manual_qr)
    - Cannot perform supervisor_override
    - Can see their own gate's count only
    - Offline-capable

  door_counter
    - Records arrivals by count only (no ticket scanning)
    - For free events with no ticketing — provides the
      crowd_level signal (ZUKA.md §20 heat map) without
      a full ticketing integration

OFFLINE CAPABILITY:
  Scanner Pro maintains a local database (SQLite on device)
  containing:
    - Ticket token signing public key (pre-fetched)
    - Full ticket list for this gathering_id (pre-fetched
      when gate session is opened while online)
    - Offline check-in queue (synced on reconnection)
    - Deduplication table (checked_ticket_ids set)

  Reconnection sync:
    - POST all queued TicketCheckedIn events to server
    - Server deduplicates by ticket_id + gathering_id
    - Conflicts resolved: FIRST check-in wins; subsequent
      duplicate scans produce a 'duplicate_scan' warning,
      not an admission error

GATE CONFIGURATION:
  Before a gate session opens, the gate_supervisor:
    1. Selects the gathering_id from their organiser list
    2. Downloads the ticket manifest (all valid ticket_ids
       for this gathering, their tier_id and status)
    3. Downloads the signing key for this gathering's tokens
    4. Assigns scanner roles to staff
  All of this requires an internet connection. Once complete,
  the session operates fully offline.
```

---

# Part VII — Attendance Memory Creation

---

## Chapter 9 — The Automatic Memory Seed

When a TicketCheckedIn event fires, the system automatically creates a minimal Memory record (Layer 1→2 per Volume V, Chapter 7) — the lowest possible Memory artifact, not yet a curated Memory:

```typescript
AttendanceSeedRecord {
  // This is NOT a full Memory entity (Vol V Ch.5) yet.
  // It is the seed that the Memory creation flow surfaces
  // to the person after the gathering ends.

  seed_id:                UUID
  ticket_id:                  UUID
  gathering_id:                   UUID
  person_id:                          UUID
  checked_in_at:                          Timestamptz
  status:                                     'seed'
  // 'seed' | 'elevated_to_memory' | 'dismissed'
  // 'dismissed': person chose not to create a Memory
  //   from this attendance — the seed is preserved
  //   (per Vol VII Temporal Truth) but no Memory entity
  //   is created, and no further prompts are sent
}
```

After the gathering ends, ZUKA surfaces a prompt:
*"You were at [Gathering Name]. Want to save this memory?"*

If the person responds:
- **Yes** → Memory creation flow opens (Vol V Ch.3's full entity); status → 'elevated_to_memory'; ticket.memory_id set
- **Later** → seed persists; prompt resurfaces once at 24h post-event
- **No** → status → 'dismissed'; no further prompts

The prompt carries the gathering's cover image, date, and any tagged co-attendees (other checked-in Person entities the person follows). This is the Volume IV, Part X Memory Layer integration — the bridge from Gathering to Memory.

---

# Part VIII — Commerce Integrity Rules

---

## Chapter 10 — Invariants That Cannot Be Violated

These are the commerce-layer equivalents of Volume II's architectural non-negotiables, applied specifically to the ticketing and payment system.

```
RULE 1 — ONE TICKET PER CHECK-IN
  A single ticket_id may only produce ONE TicketCheckedIn
  event per gathering. The deduplication table in Scanner Pro
  enforces this offline; the server enforces it for online
  admissions. Duplicate scan attempts produce a warning,
  not a second admission.

RULE 2 — PAYMENT VERIFIED BEFORE TICKET ISSUED
  A TicketPurchased event fires ONLY after
  TicketPaymentVerified confirms the Flutterwave transaction
  (Vol XIX Ch.6, verify_payment capability). No ticket is
  ever issued speculatively — a failed verification produces
  a FAILED status; a pending Flutterwave response holds the
  ticket in PAYMENT_PENDING. There is no "issue first, charge
  later" path.

RULE 3 — REFUNDS ONLY ON VERIFIED CANCELLATION
  TicketRefunded fires ONLY when a GatheringCancelled event
  exists for the relevant gathering_id, or when an organiser
  with organiser_persona trust level explicitly initiates
  a refund (Vol IV Ch.4's host/organiser roles). No
  automated refunds without one of these two preconditions.

RULE 4 — REVOCATION IS ALWAYS HUMAN
  The REVOKED status (Chapter 3) requires a human
  identity_id in the revoked_by field. No automated system
  — not the Trust Agent (Vol X Ch.3), not a fraud signal
  (Vol XIII Ch.8) — may directly set a ticket to REVOKED.
  Fraud signals flag for human review; humans act.

RULE 5 — GIFT TICKET EXPIRY IS NON-NEGOTIABLE
  A gift ticket that has not been accepted within 48 hours
  returns to ISSUED status with the original purchaser as
  owner, automatically. No extension is permitted without
  the gifter repurchasing. This is the only automated
  status transition in the lifecycle — it is mechanical,
  not a judgement call, and cannot be overridden by any
  party other than ZUKA engineering (with a documented
  reason and version increment).

RULE 6 — COMMISSION BOUNDS ARE ABSOLUTE
  Per ZUKA.md's original constitutional commitments:
  commission is bounded at a minimum floor and a maximum
  ceiling. No gathering-specific configuration, no operator
  override, and no promotional arrangement may set commission
  outside these bounds. The Flutterwave payout calculation
  enforces this as a computed field, not a configurable one.
```

---

# Part IX — MVP Scope

---

## Chapter 11 — What to Build First

```
BUILD (MVP):

  ✅ Ticket entity schema (Ch.1) — with TemporalEntity envelope,
     SemanticExtension, embedding field, provenance chain;
     this is the schema correction the parallel engineering
     session's schema pack requires

  ✅ GatheringTier entity (Ch.2) — the pricing and capacity
     structure for each ticket category

  ✅ Ticket lifecycle state machine (Ch.3) — specifically
     ISSUED → PAYMENT_PENDING → ISSUED/FAILED and
     ISSUED → CHECKED_IN as the MVP critical path

  ✅ Core ticketing events (Ch.4) — TicketPurchased,
     TicketCheckedIn, TicketRefunded as the minimum MVP set

  ✅ Smart Bucket QR (Ch.5) — the token structure and
     generation logic; 30-second expiry; offline-safe
     JWT verification

  ✅ CheckInRecord entity (Ch.6) — with offline session
     support (offline_session_id, synced_at)

  ✅ Scanner Pro offline mode (Ch.8) — local SQLite,
     ticket manifest download, deduplication table,
     reconnection sync; this is the most technically
     complex MVP item in this volume

  ✅ AttendanceSeedRecord (Ch.9) — the minimal memory
     seed created on check-in; post-gathering Memory
     creation prompt

  ✅ Commerce Integrity Rules (Ch.10) — enforced as
     database constraints and application-layer validations
     from day one, not added retroactively
```

```
DO NOT BUILD YET:

  ❌ Gift ticket flow (Ch.7) — NOT MVP; adds complexity
     to the critical path; deferred to V1.1 post-MVP.
     The gift ticket model is fully specified here so
     that when it IS built, it extends the existing state
     machine rather than requiring a redesign.

  ❌ GiftTicketRecord entity — schema can be added at gift
     ticket feature time without migration cost

  ❌ Multi-gate Scanner Pro (separate gate_id per gate) —
     MVP uses single-gate mode for all gatherings; multi-
     gate support is straightforward to add once the
     single-gate model is proven

  ❌ door_counter role — for free events at MVP, crowd_level
     is inferred from Go Live activation time and estimated
     capacity; a dedicated counter role is V1.1
```

---

# Volume XXI Summary

```
Canonical Ticket Entity:
  Full TypeScript schema extending Vol II §3.10, with:
  TemporalEntity envelope (Vol VII), SemanticExtension +
  embedding (Vol VIII), provenance chain (Vol VIII Ch.20),
  Memory linkage (Vol V), gift state, smart_bucket_token

GatheringTier Entity:
  Pricing, capacity, sale window, computed ticket counts
  via event sourcing

Ticket Lifecycle State Machine:
  9 states (AVAILABLE through REVOKED/EXPIRED/REFUNDED)
  Gift sub-machine embedded within (GIFT_PENDING →
  ACCEPTED|DECLINED|EXPIRED)

Ticketing Domain Events (8 new):
  TicketPurchased, TicketCheckedIn, TicketTransferInitiated,
  TicketTransferCompleted, TicketGiftPending,
  TicketGiftAccepted, TicketGiftDeclined/Expired,
  TicketRefunded, TicketRevoked

Smart Bucket QR:
  Time-limited JWT (30-second expiry), server-signed,
  offline-verifiable via cached public key
  Prevents screenshot fraud; zero user action at gate

CheckInRecord:
  Offline session support, method tracking, deferred sync

Gift Ticket State Machine:
  PENDING_ACCEPTANCE → ACCEPTED|DECLINED|EXPIRED
  48-hour non-extendable window
  Supports non-ZUKA recipients via SMS deeplink flow
  (Africa's Talking connector, Vol XIX Ch.7)

Scanner Pro Architecture:
  3 roles: gate_supervisor, gate_scanner, door_counter
  Full offline mode: SQLite, manifest pre-fetch, key pre-load,
  deduplication, background sync with FIRST-CHECK-IN-WINS
  conflict resolution

Attendance Memory Creation:
  AttendanceSeedRecord on every check-in
  Post-gathering Memory prompt; dismiss path preserved
  (Vol VII Temporal Truth — seed never deleted)

Commerce Integrity Rules (6):
  One ticket per check-in, payment before issuance,
  refunds on verified cancellation only, revocation is
  always human, gift expiry non-negotiable, commission
  bounds absolute

MVP Scope:
  8 build items (entity schemas, state machine, events,
  Smart Bucket, CheckInRecord, Scanner Pro offline,
  attendance seed, integrity rules)
  3 explicit deferrals (gift flow, multi-gate, counter role)
```

---

# What Volume XXII Must Address

Experience & Product Constitution — the volume specifying the constitutional rules for ZUKA's user-facing surface. Volume XXII must:

1. Establish the UX doctrine that governs every product decision (the "Invisible Sophistication" principle Volume XIV's source material named but was not adopted into the Bible: maximum 3-5 clicks, most actions < 30 seconds, AI invisible, progressive disclosure)
2. Define the three constitutionally protected product experiences (Go Live in 60 seconds, find something near you in zero friction, buy a ticket in under 3 taps) as verifiable, testable criteria — not aspirations
3. Specify the notification architecture — what ZUKA may notify, when, and what the user can configure; the sensitivity rules from Volume V's Anniversary Engine extended to every notification type
4. Specify the accessibility baseline — minimum requirements for the African market context (low-bandwidth, entry-level Android hardware, mixed-connectivity environments)
5. Establish the anti-dark-pattern constitution — specific prohibited UX patterns that would violate this Bible's consent architecture, data ethics commitments, or the "city's data belongs to the city" principle

---

> **ZUKA Architecture Bible**
> Volume XXI — Ticketing & Commerce Architecture
> Built in Kampala. Built for Africa. Built for the world.
