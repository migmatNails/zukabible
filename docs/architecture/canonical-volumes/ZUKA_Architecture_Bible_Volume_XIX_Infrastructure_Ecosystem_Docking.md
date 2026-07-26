# ZUKA Architecture Bible
# Volume XIX — Infrastructure & Ecosystem / Docking Architecture

> **Series:** ZUKA Architecture Bible
> **Volume:** XIX of N
> **Status:** Canonical — completes the Connector Framework deferred by Volume XI Ch.10
> **Depends on:** Volume XI (minimal ConnectorContract shape), Volume VIII
>                  (Knowledge Federation context), Volume XVIII (Legal Addenda),
>                  Volume XX (DRL Activation Contract as approval gate)
> **Required by:** Every external integration ZUKA builds

---

# Preface

Volume XI, Chapter 9 gave the ConnectorContract its first and minimal shape. Volume XI, Chapter 10 explicitly stated: "This volume deliberately does NOT specify the full Connector Framework." Volume X named this as still-owed. Volume XVII named it again. Volume XVIII referenced it in the Founder Review Queue process.

This volume closes that debt. It specifies the full Connector Framework — the CapabilityDeclaration taxonomy, the ConnectorContract versioning model, the approval process gating every new integration, and the three committed connectors (SafeBoda, Flutterwave, Africa's Talking) in complete form.

The governing principle: **every external system connects like a spacecraft docking with the ISS — loosely coupled, capability-based, reversible, observable, versioned.**

---

# Part I — The Docking Principle, Operationalised

---

## Chapter 1 — Five Properties Every Connector Must Have

```
LOOSELY COUPLED
  No domain module (Volume XI, Chapter 3's eight domains) may
  import or directly call an external system's SDK. All calls
  route through the Connector layer. A change to an external
  API never reaches domain code — only the Connector changes.

CAPABILITY-BASED
  A connector exposes a specific, enumerable list of named
  CAPABILITIES — nothing outside that list is accessible,
  regardless of what the external system's API permits.

REVERSIBLE
  A connector can be disabled, replaced, or removed without
  changing domain code. The connector is the only artifact
  that knows about the external system.

OBSERVABLE
  Every connector interaction is logged as an observability
  event (Volume XI Ch.12 — NOT DomainEvents; connector
  interactions are operational telemetry, not product history).

VERSIONED
  Connectors are versioned independently of ZUKA's release
  cycle. A breaking external API change produces a new
  connector version, never a platform release change.
```

---

# Part II — The Connector Framework

---

## Chapter 2 — The Full ConnectorContract

```typescript
ConnectorContract {
  connector_id:              UUID
  connector_name:                TEXT     // e.g., 'safeboda_ride_v1'
  connector_version:                 SemVer
  external_system:                      TEXT
  external_system_category:                ExternalSystemCategory
  // 'mobility_provider' | 'payment_processor' | 'sms_gateway' |
  // 'identity_verification' | 'mapping_service' | 'push_notification' |
  // 'cloud_storage' | 'knowledge_source' | 'analytics_service'

  capabilities:                  CapabilityDeclaration[]

  coupling_boundary:                'loose'    // always

  reversibility: {
    can_be_disabled_without_domain_changes:    true   // always
    fallback_behaviour_when_disabled:              FallbackBehaviour
    // 'graceful_degradation' | 'feature_hidden' |
    // 'queue_and_retry' | 'fail_open'
  }

  observability: {
    log_every_call:              true   // always
    alert_on_error_rate_above:       Float
    alert_on_latency_p99_above_ms:      Integer
  }

  version_history:              ConnectorVersion[]
  deprecation_policy: {
    notice_period_days:         Integer
    sunset_date:                    Date | null
  }

  approval: {
    approved_at:               Timestamptz
    approved_by:                   UUID    // founder identity_id
    approval_basis:                    ApprovalBasis
    // 'operational_prerequisite' — ZUKA cannot function without it
    // 'drl_activation_contract' — DRL-gated capability
    // 'founder_review_queue' — requires legal review
    legal_addendum_reference:          TEXT | null
  }

  privacy_classification: {
    transmits_personal_data:     Boolean
    personal_data_categories:        PersonalDataCategory[]
    // ['location', 'phone_number', 'identity_document',
    //  'financial', 'biometric']
    data_minimisation_notes:             TEXT
    pia_record_id:                           UUID | null
  }

  status:                                ConnectorStatus
  // 'active' | 'deprecated' | 'disabled' | 'pending_approval'
}
```

---

## Chapter 3 — The CapabilityDeclaration

```typescript
CapabilityDeclaration {
  capability_name:         TEXT
  description:                  TEXT
  input_schema:                     JSONSchema
  output_schema:                        JSONSchema
  personal_data_in_input:                   PersonalDataCategory[]
  error_handling:                               ErrorHandlingStrategy
  // 'fail_closed' | 'fail_open_with_fallback' | 'queue_and_retry'
  rate_limit_per_minute:                            Integer | null
  timeout_ms:                                           Integer
  idempotent:                                               Boolean
}
```

---

## Chapter 4 — Connector Approval Process

```
STEP 1 — CONNECTOR PROPOSAL (Founder Rule format, Vol XI Ch.13)
  Domain, Events, Data Contracts, DRL Level, Graph Edges,
  Future Dependencies — same six fields as any feature proposal

STEP 2 — PRIVACY REVIEW
  Any connector transmitting personal data requires a PIA
  (Vol XVIII Ch.10) before production approval.
  Output: PIA record ID attached to ConnectorContract.

STEP 3 — LEGAL ADDENDUM CHECK
  Cross-border personal data transmission requires
  confirmation from the relevant Legal Addendum
  (Vol XVIII Part V) that the transfer is compliant.

STEP 4 — TECHNICAL REVIEW
  Engineering confirms:
    - coupling_boundary = 'loose' (no direct SDK imports)
    - fallback_behaviour_when_disabled specified and tested
    - observability hooks in place
    - error_handling = 'fail_closed' for financial/PII capabilities

STEP 5 — FOUNDER APPROVAL
  ConnectorContract approved (approved_by = founder identity_id)
  before status → 'active'.

  Operational prerequisites: may skip DRL gate.
  Capability-extending connectors: additionally require
  Vol XX Ch.10 DRL Activation Contract.
```

---

# Part III — The Three Committed Connectors

---

## Chapter 5 — SafeBoda / Zuka Ride Connector

```
Connector: safeboda_ride_v1
Category: mobility_provider
Status: active (operational prerequisite)

CAPABILITIES:

  request_ride_quote
    Input:  origin_lat, origin_lng, destination_lat,
            destination_lng, ride_type ('boda'|'car')
    Output: estimated_fare_ugx, estimated_duration_minutes,
            deeplink_url, quote_valid_until
    Personal data: location (coordinates only)
    Error handling: fail_open_with_fallback
      ('Ride estimate temporarily unavailable')
    Timeout: 3000ms | Idempotent: true

  deep_link_to_booking
    Input:  quote_id, destination_label
            (NO personal data — user authenticates in
             SafeBoda's own app independently)
    Output: deeplink_url, expires_at
    Personal data: none
    Error handling: fail_closed
    Timeout: 1000ms | Idempotent: true

FALLBACK: feature_hidden when connector disabled
  ('Get a Ride' CTA hidden, not broken)

PRIVACY: Only lat/lng coordinates transmitted.
  No identity, gathering, or social graph data shared.
  Coordinates not stored after quote returned.

LEGAL: Addendum 001 Section 7 (domestic location data transfer)
```

---

## Chapter 6 — Flutterwave Payment Connector

```
Connector: flutterwave_payments_v1
Category: payment_processor
Status: active (operational prerequisite)

CAPABILITIES:

  initiate_payment_intent
    Input:  amount, currency, payment_reference,
            customer_email, customer_phone_e164,
            redirect_url, tx_ref (ZUKA order UUID)
    Output: flw_ref, payment_link, status
    Personal data: phone_number, financial
    Error handling: fail_closed
    Timeout: 5000ms | Idempotent: FALSE
      (always use a new tx_ref; never retry with same ref)

  verify_payment
    Input:  flw_transaction_id, expected_amount,
            expected_currency, expected_tx_ref
    Output: verified (bool), amount_charged, currency,
            payment_method, charged_at
    Personal data: none
    Error handling: fail_closed
    Timeout: 3000ms | Idempotent: true

  initiate_payout
    Input:  amount, currency, account_type, account_number,
            account_name, narration, reference (ZUKA payout UUID)
    Output: status, transfer_code, complete_message
    Personal data: financial, identity_document
    Error handling: fail_closed
    Timeout: 8000ms | Idempotent: FALSE
      (always deduplicate using reference field first)

FALLBACK: fail_closed when connector disabled
  (ticket purchases fail gracefully; no free tickets)

PRIVACY: Phone and account details minimum necessary.
  No social graph data transmitted.

LEGAL: Addendum 001 Sections 1-3 (financial data processing)
```

---

## Chapter 7 — Africa's Talking SMS Connector

```
Connector: africas_talking_sms_v1
Category: sms_gateway
Status: active (operational prerequisite)

CAPABILITIES:

  send_sms_otp
    Input:  phone_e164, otp_code (ZUKA generates),
            message_template
    Output: message_id, status, cost
    Personal data: phone_number
    Error handling: fail_closed
    Timeout: 5000ms | Idempotent: false

  send_sms_notification
    Input:  phone_e164, message_body (max 160 chars),
            sender_id ('ZUKA')
    Output: message_id, status, recipients
    Personal data: phone_number
    Error handling: graceful_degradation
      (queue_and_retry; SMS is secondary to FCM push)
    Timeout: 3000ms | Idempotent: false

FALLBACK: queue_and_retry
PRIVACY: Only E.164 phone numbers. No names, gathering
  data, or social graph shared with Africa's Talking.
LEGAL: Addendum 001 Section 2 (phone number processing)
```

---

# Part IV — Future Connectors

---

## Chapter 8 — Pending Approval Connectors

```
Smile Identity (identity_verification)
  Status: pending_approval
  Blocking condition: PIA completion (biometric data) +
    Legal Addendum 001 biometric section by qualified counsel
  Single capability: verify_government_id
    Input:  id_type, id_number, country, first_name,
            last_name, date_of_birth, zuka_reference_uuid
    Output: verified (bool), confidence_value, result_code
    ZUKA stores ONLY verified (bool) and confidence_value —
    never the ID number or any document content
  Personal data: identity_document, biometric
  Data minimisation: strictest — only the boolean result
    and confidence score retained in ZUKA schema

Mapbox (mapping_service)
  Status: pending_approval
  Blocking condition: founder approval (operational prerequisite)
  Capabilities: geocode_venue, get_district_for_coordinates,
    get_gathering_heatmap_tiles
  Personal data: none in primary queries (no user identifiers)

Firebase Cloud Messaging (push_notification)
  Status: pending_approval
  Blocking condition: founder approval (operational prerequisite)
  Capabilities: send_push_notification, register_device_token
  Personal data: device_tokens (pseudonymous)

Sentry (analytics_service / error tracking)
  Status: pending_approval
  Blocking condition: founder approval
  Special requirement: error payloads must be sanitised
    before transmission — no PII in stack traces
  Personal data: none (sanitised)

Cloudflare (cdn / ddos_protection)
  Status: pending_approval
  Blocking condition: founder approval
  Personal data: IP addresses (anonymised per Cloudflare terms)
```

---

## Chapter 9 — Knowledge Federation Connector Architecture

Per Volume VIII, Chapter 24 and Volume XVIII, Chapter 5, Knowledge Federation remains in the Founder Review Queue. This chapter specifies the TECHNICAL ARCHITECTURE that connectors in this category would follow IF the decision is approved — so that a positive decision produces a scoped connector implementation, not a data architecture rewrite.

```
KNOWLEDGE FEDERATION CONNECTOR — ADDITIONAL REQUIREMENTS

MANDATORY CONSENT GATE (beyond standard ConnectorContract):
  No federation capability may be invoked for any Person
  without EXPLICIT, separately-granted consent naming the
  specific external system and specific data being imported.
  Stored as an extension of Vol IX Ch.3's TwinConsentRecord.
  Revocable immediately per Vol IX Ch.5.
  Non-inheriting: each external system requires its OWN
  explicit consent — Google Photos consent does not imply
  Facebook consent.

DATA MINIMISATION (stricter than standard):
  ONLY data explicitly requested by the user in the consent
  flow is imported. No background import of additional data.

IMPORT SCOPE:
  Federation imports are scoped to the PERSON entity's own
  data only. A user imports photos THEY created or are the
  primary subject of — not photos of them uploaded by others.

PROVENANCE ANNOTATION (extends Vol VIII Ch.20):
  Every imported record carries:
    source_external_system: 'google_photos'
    imported_at: [timestamp]
    imported_with_consent_id: [TwinConsentRecord UUID]
  This annotation is never hidden; always included in exports.

IMPORTED MEDIA IS NOT AUTOMATICALLY A MEMORY:
  Per Vol V Ch.7's Layer 1→4 curation requirement:
  imported photos enter the Media Layer (Layer 1) only.
  The same human curation step required for ZUKA-native
  uploads is required before imported photos become Memories.
  Federation does not bypass the curation discipline.

EXAMPLE CAPABILITY — Google Photos Federation:
  capability_name: 'import_photos_with_consent'
  input_schema:
    person_id: UUID (ZUKA internal only)
    date_range_start: ISO8601 date
    date_range_end: ISO8601 date
    consent_id: UUID (TwinConsentRecord.consent_id)
  output_schema:
    imported_media_count: number
    media_layer_ids: UUID[]  // IDs in ZUKA's Media Layer only
  personal_data_in_input: ['location']
    // If photos contain location metadata — whether to
    // import or strip that metadata is a per-PIA decision
    // per applicable market law
```

---

# Part V — MVP Scope

---

## Chapter 10 — What to Build First

```
BUILD (MVP):

  ✅ SafeBoda connector — both capabilities operational;
     observability hooks in place; fallback tested

  ✅ Flutterwave connector — all three capabilities
     operational; payment verification webhook handler
     built and tested BEFORE any ticket is sold

  ✅ Africa's Talking connector — OTP capability
     operational before any user registration; SMS
     notification capability with queue_and_retry tested

  ✅ Connector approval process (Ch.4) — document template
     in repo governance folder; the three connectors above
     serve as the first three instances in practice

  ✅ ConnectorContract schema — in the database from
     first migration; pending-approval connectors have
     their records in 'pending_approval' status, making
     the full connector inventory always visible
```

```
DO NOT BUILD YET:

  ❌ Smile Identity connector — pending PIA + Addendum 001
      biometric section by qualified counsel

  ❌ Any Knowledge Federation connector — pending Founder
      Review Queue decision per Vol XVIII Ch.5

  ❌ Mapbox, FCM, Sentry, Cloudflare — build as standard
      infrastructure; ConnectorContract records created
      once the Framework is the standard approval gate
```

---

# Volume XIX Summary

```
The Docking Principle:
  5 properties: loosely coupled, capability-based,
  reversible, observable, versioned — structural requirements

Full ConnectorContract:
  Extends Vol XI Ch.9's minimum shape: capability
  declarations, coupling boundary, fallback behaviours,
  observability config, versioning, approval record,
  privacy classification

CapabilityDeclaration:
  Atomic unit — named, bounded, typed input/output,
  personal data classification, error handling, idempotency

Approval Process:
  5 steps: Proposal → Privacy Review → Legal Addendum →
  Technical Review → Founder Approval
  Operational prerequisites: expedited; capability-extending:
  additionally require DRL Activation Contract

Three Committed Connectors, Fully Specified:
  SafeBoda (2 capabilities, location only)
  Flutterwave (3 capabilities, financial/phone data)
  Africa's Talking (2 capabilities, phone data only)
  All: ConnectorContract + privacy classification + fallback

Future Connectors:
  Smile Identity: full spec in 'pending_approval';
    PIA + legal addendum required before activation
  Knowledge Federation: technical architecture defined
    without prejudging adoption; mandatory consent gate,
    data minimisation, provenance annotation requirements
    specified regardless of which systems are approved

MVP Scope:
  5 build items (3 connectors + approval process + schema)
  2 explicit deferrals with clear preconditions
```

---

# What Volume XXI Must Address

Ticketing & Commerce Architecture — the most operationally critical system at MVP (without it, no ticket can be bought in under 3 taps) that has never received its own canonical Bible volume. Volume XXI must specify:

1. The canonical Ticket entity extending Volume II's taxonomy — with TemporalEntity envelope, embedding, provenance chain
2. The ticket lifecycle state machine as a formal event-sourced state machine
3. The Smart Bucket QR system (ZUKA.md §7) — QR entity, scan record, idempotency guarantee
4. The gift ticket state machine (ZUKA.md §10) — PENDING_ACCEPTANCE through EXPIRED, with escrow model
5. The Zuka Scanner Pro architecture (ZUKA.md §8) — offline capability, staff roles, gate throughput
6. Confirmation of the Flutterwave connector (Volume XIX Ch.6) as the operational payment path

---

> **ZUKA Architecture Bible**
> Volume XIX — Infrastructure & Ecosystem / Docking Architecture
> Built in Kampala. Built for Africa. Built for the world.
