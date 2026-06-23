# ZUKA Canonical Schema Pack

**Version:** 1.0  
**Status:** Active — MVP Phase 1  
**Authority:** Derived from Book II (Canonical Domain Model), Book III (Identity), Book IV (Memory), Book VII (Trust)  
**Stack:** Supabase / PostgreSQL  

---

## Overview

This document defines the canonical field-level schemas for all entities in scope for ZUKA Phase 1 (Kampala MVP). These schemas are the authoritative reference for database migrations, API response contracts, Flutter data models, and any downstream data product.

**Schema design principles:**
- Every table has `id uuid DEFAULT gen_random_uuid() PRIMARY KEY`
- Every table has `created_at timestamptz DEFAULT now() NOT NULL`
- Every table has `updated_at timestamptz DEFAULT now() NOT NULL` (managed by trigger)
- Soft deletes via `deleted_at timestamptz` where noted — never hard delete participant or financial records
- All monetary values stored as integers (smallest currency unit — UGX has no subunits, store as whole shillings)
- All phone numbers stored in E.164 format (`+256...`)
- All percentages stored as integers (500 = 5.00%)
- Enum types defined centrally and referenced by name

---

## Domain Map

```
identity/
  participants           — the universal participant entity
  participant_profiles   — extended profile data
  phone_verifications    — OTP lifecycle
  identity_documents     — KYC documents (verified participants)

venue/
  venues                 — physical and virtual locations
  venue_affiliations     — participant↔venue relationships
  venue_verifications    — verification tier lifecycle

event/
  gatherings             — the canonical event entity
  gathering_tiers        — ticket tiers per gathering
  gathering_media        — photos, artwork

ticketing/
  buckets                — participant ticket wallets
  tickets                — individual ticket records
  ticket_provenance      — origin audit trail
  ticket_transfers       — gift and transfer lifecycle
  check_ins              — entry records

trust/
  trust_scores           — computed trust per participant
  trust_signals          — raw signal events feeding trust
  verification_tiers     — current tier per entity

community/
  follows                — follow relationships (participant→venue, participant→participant)
  
memory/
  attendance_memories    — preserved participation records
  pulse_points_ledger    — points earn/burn ledger

payment/
  payment_intents        — payment lifecycle records
  payouts                — organiser payout records
  wallet_ledger          — event wallet balance ledger
```

---

## Enum Definitions

```sql
-- Participant types
CREATE TYPE participant_type AS ENUM (
  'individual',    -- a person
  'venue',         -- a physical venue (may also be a participant)
  'organiser'      -- an event organiser (may be individual or org)
);

-- Verification tiers (applies to participants and venues)
CREATE TYPE verification_tier AS ENUM (
  'unverified',    -- self-registered, no documents reviewed
  'standard',      -- ID reviewed and approved
  'pro'            -- business registration + operating licence reviewed
);

-- Gathering (event) status lifecycle
CREATE TYPE gathering_status AS ENUM (
  'draft',         -- created, not published
  'published',     -- visible in feed, not yet active
  'active',        -- within 24h of start time
  'live',          -- Go Live activated OR check-in window open
  'ended',         -- past end time, not yet closed
  'closed',        -- post-event report generated
  'cancelled'      -- organiser cancelled
);

-- Gathering format
CREATE TYPE gathering_format AS ENUM (
  'standard',      -- single date, single location, single tier
  'multi_tier',    -- multiple ticket categories
  'recurring',     -- weekly/monthly repeating
  'festival',      -- multi-day, multi-stage
  'private',       -- invite-only
  'hybrid',        -- in-person + remote streaming
  'go_live'        -- no advance scheduling, instant activation
);

-- Event category (primary, one per gathering)
CREATE TYPE gathering_category AS ENUM (
  'nightclub', 'bar', 'live_music', 'concert', 'comedy',
  'sports', 'screening', 'movie_night', 'cultural', 'wedding',
  'corporate', 'party', 'festival', 'religious', 'market',
  'food_and_drink', 'exhibition', 'theatre', 'wellness',
  'conference', 'art_show'
);

-- Ticket status lifecycle
CREATE TYPE ticket_status AS ENUM (
  'owned',               -- normal state, holder can use it
  'gift_pending',        -- in escrow, awaiting recipient action
  'gift_pending_reg',    -- recipient not yet on platform
  'transferred',         -- gift accepted, now owned by recipient
  'checked_in',          -- used, terminal state
  'refunded',            -- refunded, terminal state
  'void'                 -- invalidated, terminal state
);

-- Ticket class
CREATE TYPE ticket_class AS ENUM (
  'standard',    -- single person, single entry
  'group',       -- multi-person, fixed group
  'table',       -- venue table reservation
  'vip',         -- premium access
  'streaming',   -- remote viewing pass
  'comp',        -- complimentary
  'staff'        -- internal staff access
);

-- Ticket provenance (origin)
CREATE TYPE ticket_origin AS ENUM (
  'purchase',          -- bought on platform
  'gift_transfer',     -- received as gift from another participant
  'promotion',         -- organiser promotional issue
  'points_redemption', -- redeemed via Pulse Points
  'comp_issue',        -- complimentary by organiser
  'group_split',       -- split from group/table ticket
  'refund_reissue',    -- reissued after partial refund
  'migration'          -- legacy import (admin-authorised only)
);

-- Payment methods
CREATE TYPE payment_method AS ENUM (
  'mtn_mobile_money',
  'airtel_money',
  'card_visa',
  'card_mastercard',
  'event_wallet',
  'pulse_points'
);

-- Payment status
CREATE TYPE payment_status AS ENUM (
  'initiated',
  'pending_confirmation',  -- USSD push sent, awaiting PIN
  'succeeded',
  'failed',
  'refunded',
  'partially_refunded'
);

-- Payout status
CREATE TYPE payout_status AS ENUM (
  'queued',
  'processing',
  'succeeded',
  'failed',
  'held'          -- payout failed 3x, funds held in organiser balance
);

-- Trust signal type
CREATE TYPE trust_signal_type AS ENUM (
  'identity_verified',
  'phone_verified',
  'event_completed',           -- organiser completed an event without cancellation
  'event_cancelled',           -- organiser cancelled an event
  'ticket_sold',
  'check_in_recorded',
  'review_received_positive',
  'review_received_negative',
  'dispute_raised_against',
  'dispute_resolved_for',
  'dispute_resolved_against',
  'payout_received',
  'fraud_flag',
  'chargeback_filed'
);

-- Follow target types
CREATE TYPE follow_target_type AS ENUM (
  'venue',
  'participant',
  'gathering_category'
);

-- Points transaction type
CREATE TYPE points_transaction_type AS ENUM (
  'earn_signup',
  'earn_profile_complete',
  'earn_first_checkin',
  'earn_checkin',
  'earn_streak_bonus',
  'earn_vibe_drop',
  'earn_vibe_drop_reaction',
  'earn_rsvp_attended',
  'earn_ticket_purchase',
  'earn_gift_given',
  'earn_gift_received',
  'earn_referral_signup',
  'earn_referral_purchase',
  'earn_follow',
  'earn_review',
  'earn_taste_profile',
  'burn_free_entry',
  'burn_ticket_discount',
  'burn_free_drink',
  'burn_priority_entry',
  'burn_waitlist_access',
  'burn_streaming_pass',
  'burn_points_gift',
  'expire',
  'admin_adjustment'
);
```

---

## Core Schemas

### 1. Participants

The universal entity. Every human actor on the platform — individual attendees, organisers, venue operators — is a participant. There is one record per person.

```sql
CREATE TABLE participants (
  -- Identity
  id              uuid DEFAULT gen_random_uuid() PRIMARY KEY,
  phone           text NOT NULL UNIQUE,           -- E.164, e.g. +256701234567
  phone_verified  boolean NOT NULL DEFAULT false,
  
  -- Display
  display_name    text,                            -- first name (required after onboarding)
  full_name       text,                            -- optional
  avatar_url      text,                            -- Supabase Storage path
  
  -- Classification
  participant_type  participant_type NOT NULL DEFAULT 'individual',
  verification_tier verification_tier NOT NULL DEFAULT 'unverified',
  
  -- Onboarding state
  onboarding_complete  boolean NOT NULL DEFAULT false,
  onboarding_step      smallint NOT NULL DEFAULT 0,  -- 0=phone, 1=name, 2=done
  
  -- Location context (city-level, not GPS)
  city            text,                            -- 'Kampala'
  city_zone       text,                            -- neighbourhood, e.g. 'Kololo'
  
  -- Auth (Supabase Auth links here)
  auth_user_id    uuid UNIQUE,                     -- references auth.users(id)
  
  -- Privacy
  is_invisible    boolean NOT NULL DEFAULT false,  -- invisible mode
  
  -- Account state
  is_suspended    boolean NOT NULL DEFAULT false,
  suspended_at    timestamptz,
  suspended_reason text,
  
  -- Deletion
  deleted_at      timestamptz,                     -- soft delete; 30-day grace
  deletion_scheduled_at timestamptz,
  
  -- Audit
  created_at      timestamptz NOT NULL DEFAULT now(),
  updated_at      timestamptz NOT NULL DEFAULT now(),
  last_seen_at    timestamptz
);

-- Index on phone for lookup; auth_user_id for session resolution
CREATE INDEX idx_participants_phone ON participants(phone);
CREATE INDEX idx_participants_auth_user ON participants(auth_user_id);
CREATE INDEX idx_participants_city_zone ON participants(city, city_zone) WHERE deleted_at IS NULL;
```

### 2. Participant Profiles

Extended profile data collected progressively. Separated from core identity to avoid bloating the primary participant record and to cleanly model consent tiers.

```sql
CREATE TABLE participant_profiles (
  id                    uuid DEFAULT gen_random_uuid() PRIMARY KEY,
  participant_id        uuid NOT NULL UNIQUE REFERENCES participants(id) ON DELETE CASCADE,
  
  -- Tier A — collected at sign-up or immediately after (low friction)
  date_of_birth         date,                     -- used for age-gated events
  gender                text,                     -- free text, optional
  
  -- Tier B — progressive profiling (offered contextually)
  occupation_category   text,                     -- 'student', 'professional', etc.
  relationship_status   text,
  budget_preference     text,                     -- 'free_only', 'under_10k', 'any'
  
  -- Tier C — explicit opt-in, reward offered
  home_pin_lat          numeric(10,7),            -- precise home location
  home_pin_lng          numeric(10,7),
  workplace_zone        text,
  spotify_connected     boolean NOT NULL DEFAULT false,
  apple_music_connected boolean NOT NULL DEFAULT false,
  
  -- Taste preferences (set from vibe tag interactions)
  preferred_categories  gathering_category[],     -- top categories by attendance
  preferred_vibe_tags   text[],                   -- top vibe tags
  
  -- Consent flags (granular, revocable)
  consent_behavioural_data    boolean NOT NULL DEFAULT false,
  consent_location_tracking   boolean NOT NULL DEFAULT false,
  consent_third_party_research boolean NOT NULL DEFAULT false,
  consent_marketing_comms     boolean NOT NULL DEFAULT false,
  
  -- Consent timestamps (for audit)
  consent_behavioural_at      timestamptz,
  consent_location_at         timestamptz,
  consent_research_at         timestamptz,
  consent_marketing_at        timestamptz,
  
  -- Referral tracking
  referred_by_participant_id  uuid REFERENCES participants(id),
  referral_code               text UNIQUE,        -- participant's own code to share
  
  created_at  timestamptz NOT NULL DEFAULT now(),
  updated_at  timestamptz NOT NULL DEFAULT now()
);
```

### 3. Phone Verifications

OTP lifecycle management. Records every OTP issuance and its resolution.

```sql
CREATE TABLE phone_verifications (
  id              uuid DEFAULT gen_random_uuid() PRIMARY KEY,
  phone           text NOT NULL,                  -- E.164
  otp_hash        text NOT NULL,                  -- bcrypt hash of the 6-digit OTP
  purpose         text NOT NULL,                  -- 'signup', 'login', 'phone_change'
  
  -- Lifecycle
  expires_at      timestamptz NOT NULL,           -- now() + 60 seconds
  attempts        smallint NOT NULL DEFAULT 0,    -- max 3 before invalidation
  verified_at     timestamptz,                    -- null = not yet verified
  invalidated_at  timestamptz,                    -- null = still active
  
  -- Rate limiting context
  ip_address      inet,
  device_fingerprint text,
  
  created_at      timestamptz NOT NULL DEFAULT now()
);

CREATE INDEX idx_phone_verifications_phone ON phone_verifications(phone, created_at DESC)
  WHERE verified_at IS NULL AND invalidated_at IS NULL;
```

---

### 4. Venues

A venue is a physical (or virtual) location that hosts gatherings. A venue can also be a participant — the operator registers as a participant first, then creates a venue.

```sql
CREATE TABLE venues (
  id              uuid DEFAULT gen_random_uuid() PRIMARY KEY,
  
  -- Ownership
  owner_participant_id uuid NOT NULL REFERENCES participants(id),
  
  -- Identity
  name            text NOT NULL,
  slug            text NOT NULL UNIQUE,           -- URL-safe name, e.g. 'club-guvnor'
  description     text,
  category        gathering_category,             -- primary venue category
  
  -- Location
  address_line    text,
  neighbourhood   text,                           -- e.g. 'Kololo'
  city            text NOT NULL DEFAULT 'Kampala',
  country         text NOT NULL DEFAULT 'UG',
  lat             numeric(10,7),
  lng             numeric(10,7),
  plus_code       text,                           -- Open Location Code (no postcode in UG)
  
  -- Verification
  verification_tier  verification_tier NOT NULL DEFAULT 'unverified',
  verified_at        timestamptz,
  
  -- Go Live
  is_live            boolean NOT NULL DEFAULT false,
  went_live_at       timestamptz,
  go_live_count_month smallint NOT NULL DEFAULT 0,  -- resets monthly; cap: 1 for unverified
  
  -- Crowd state
  crowd_level     text CHECK (crowd_level IN ('quiet', 'picking_up', 'packed', 'at_capacity')),
  crowd_updated_at timestamptz,
  
  -- Media
  cover_image_url text,
  logo_url        text,
  
  -- Contact
  phone           text,
  whatsapp        text,
  instagram_handle text,
  website_url     text,
  
  -- Audio streaming capability
  audio_streaming_enabled boolean NOT NULL DEFAULT false,
  
  -- Status
  is_active       boolean NOT NULL DEFAULT true,
  deleted_at      timestamptz,
  
  created_at      timestamptz NOT NULL DEFAULT now(),
  updated_at      timestamptz NOT NULL DEFAULT now()
);

CREATE INDEX idx_venues_city_neighbourhood ON venues(city, neighbourhood) WHERE deleted_at IS NULL AND is_active = true;
CREATE INDEX idx_venues_location ON venues USING GIST (point(lng, lat)) WHERE deleted_at IS NULL;
CREATE INDEX idx_venues_live ON venues(is_live, went_live_at) WHERE is_live = true;
```

### 5. Venue Affiliations

Tracks which participants are affiliated with a venue (operators, staff, DJs, etc.) and what their role is.

```sql
CREATE TABLE venue_affiliations (
  id              uuid DEFAULT gen_random_uuid() PRIMARY KEY,
  venue_id        uuid NOT NULL REFERENCES venues(id),
  participant_id  uuid NOT NULL REFERENCES participants(id),
  
  role            text NOT NULL CHECK (role IN ('owner', 'manager', 'staff', 'artist', 'dj', 'host')),
  is_active       boolean NOT NULL DEFAULT true,
  
  -- Neighbourhood stored HERE (per §4.3 of Book II), not on the entity record
  -- Reasoning: a DJ resident at Club A in Kololo is different from the same DJ at Club B in Bugolobi
  neighbourhood   text,
  
  started_at      timestamptz NOT NULL DEFAULT now(),
  ended_at        timestamptz,
  
  created_at      timestamptz NOT NULL DEFAULT now(),
  
  UNIQUE (venue_id, participant_id, role)
);
```

---

### 6. Gatherings

The canonical event entity. Called "Gathering" in the constitutional layer to generalise beyond ticketed events (any form of human coming-together). The consumer-facing name is "Event" or "Party" etc. depending on context.

```sql
CREATE TABLE gatherings (
  id              uuid DEFAULT gen_random_uuid() PRIMARY KEY,
  
  -- Ownership
  organiser_id    uuid NOT NULL REFERENCES participants(id),
  venue_id        uuid REFERENCES venues(id),     -- null for pop-up / address-only events
  
  -- Identity
  title           text NOT NULL,
  slug            text NOT NULL UNIQUE,
  description     text,
  category        gathering_category NOT NULL,
  vibe_tags       text[] DEFAULT '{}',            -- up to 3 from allowed list
  
  -- Format
  format          gathering_format NOT NULL DEFAULT 'standard',
  
  -- Timing
  starts_at       timestamptz,                    -- null for Go Live events
  ends_at         timestamptz,
  timezone        text NOT NULL DEFAULT 'Africa/Kampala',
  
  -- Location (denormalised for events without a venue record)
  location_name   text,                           -- e.g. 'Rooftop at Speke Hotel'
  location_address text,
  location_neighbourhood text,
  location_city   text NOT NULL DEFAULT 'Kampala',
  location_lat    numeric(10,7),
  location_lng    numeric(10,7),
  
  -- Admission
  is_free         boolean NOT NULL DEFAULT false,
  min_price       integer,                        -- UGX, in whole shillings; null if free
  max_price       integer,                        -- UGX, highest tier price
  age_restriction text CHECK (age_restriction IN ('none', '18+', '21+')),
  dress_code      text,
  
  -- Capacity
  total_capacity  integer,                        -- null = unlimited
  tickets_sold    integer NOT NULL DEFAULT 0,     -- denormalised counter
  check_ins_count integer NOT NULL DEFAULT 0,     -- denormalised counter
  
  -- Status
  status          gathering_status NOT NULL DEFAULT 'draft',
  cancelled_at    timestamptz,
  cancelled_reason text,
  
  -- Go Live state
  is_live         boolean NOT NULL DEFAULT false,
  went_live_at    timestamptz,
  
  -- Gift transfer settings
  gift_transfer_allowed   boolean NOT NULL DEFAULT true,
  gift_transfer_deadline  interval,               -- e.g. '2 hours' before start
  forward_gift_allowed    boolean NOT NULL DEFAULT false,
  
  -- Check-in window
  checkin_opens_before    interval DEFAULT '30 minutes',
  checkin_closes_after    interval DEFAULT '2 hours',
  
  -- Media
  cover_image_url text,
  
  -- External links
  external_url    text,
  
  -- Boost state (paid placement)
  is_boosted      boolean NOT NULL DEFAULT false,
  boost_expires_at timestamptz,
  
  -- Audit
  view_count      integer NOT NULL DEFAULT 0,
  rsvp_count      integer NOT NULL DEFAULT 0,
  
  created_at      timestamptz NOT NULL DEFAULT now(),
  updated_at      timestamptz NOT NULL DEFAULT now(),
  deleted_at      timestamptz
);

CREATE INDEX idx_gatherings_status_city ON gatherings(status, location_city, starts_at)
  WHERE deleted_at IS NULL;
CREATE INDEX idx_gatherings_live ON gatherings(is_live, went_live_at)
  WHERE is_live = true AND deleted_at IS NULL;
CREATE INDEX idx_gatherings_organiser ON gatherings(organiser_id);
CREATE INDEX idx_gatherings_venue ON gatherings(venue_id) WHERE venue_id IS NOT NULL;
CREATE INDEX idx_gatherings_location ON gatherings USING GIST (point(location_lng, location_lat))
  WHERE deleted_at IS NULL;
```

### 7. Gathering Tiers

Each gathering can have up to 8 ticket tiers. A free gathering still has one tier (quantity = unlimited, price = 0).

```sql
CREATE TABLE gathering_tiers (
  id              uuid DEFAULT gen_random_uuid() PRIMARY KEY,
  gathering_id    uuid NOT NULL REFERENCES gatherings(id) ON DELETE CASCADE,
  
  name            text NOT NULL,                  -- e.g. 'Early Bird', 'VIP', 'General'
  description     text,
  ticket_class    ticket_class NOT NULL DEFAULT 'standard',
  
  -- Pricing
  price           integer NOT NULL DEFAULT 0,     -- UGX; 0 = free
  
  -- Availability
  quantity        integer,                        -- null = unlimited
  quantity_sold   integer NOT NULL DEFAULT 0,     -- denormalised counter
  
  -- Sale window
  sale_opens_at   timestamptz,
  sale_closes_at  timestamptz,
  
  -- Group/table settings
  group_size      smallint,                       -- for group/table tickets: number of seats
  
  -- Ordering
  display_order   smallint NOT NULL DEFAULT 0,
  is_visible      boolean NOT NULL DEFAULT true,
  
  created_at      timestamptz NOT NULL DEFAULT now(),
  updated_at      timestamptz NOT NULL DEFAULT now(),
  
  CONSTRAINT max_tiers_per_gathering CHECK (
    (SELECT COUNT(*) FROM gathering_tiers gt WHERE gt.gathering_id = gathering_id) <= 8
  )
);

CREATE INDEX idx_gathering_tiers_gathering ON gathering_tiers(gathering_id, display_order);
```

---

### 8. Buckets

A bucket is a participant's ticket wallet. Every participant has exactly one bucket. The bucket is their persistent identity at the door — they never need to find individual tickets.

```sql
CREATE TABLE buckets (
  id              uuid DEFAULT gen_random_uuid() PRIMARY KEY,
  participant_id  uuid NOT NULL UNIQUE REFERENCES participants(id),
  
  -- The QR token that physically represents this bucket
  -- Format: ZUKA:BKT:v1:[40-char-token]:[HMAC-SHA256]
  qr_token        text NOT NULL UNIQUE,
  qr_token_version smallint NOT NULL DEFAULT 1,
  qr_rotated_at   timestamptz NOT NULL DEFAULT now(),
  
  created_at      timestamptz NOT NULL DEFAULT now(),
  updated_at      timestamptz NOT NULL DEFAULT now()
);

CREATE INDEX idx_buckets_qr_token ON buckets(qr_token);
```

### 9. Tickets

One row per ticket. The provenance system means no ticket can exist without a valid origin record.

```sql
CREATE TABLE tickets (
  id              uuid DEFAULT gen_random_uuid() PRIMARY KEY,
  
  -- Ownership chain
  bucket_id       uuid NOT NULL REFERENCES buckets(id),
  gathering_id    uuid NOT NULL REFERENCES gatherings(id),
  tier_id         uuid NOT NULL REFERENCES gathering_tiers(id),
  
  -- Current owner
  owner_participant_id uuid NOT NULL REFERENCES participants(id),
  
  -- Classification
  ticket_class    ticket_class NOT NULL,
  
  -- For group/table tickets
  group_size      smallint,                       -- total seats on this ticket
  group_seats_used smallint NOT NULL DEFAULT 0,   -- seats gifted out as individual tickets
  
  -- Status
  status          ticket_status NOT NULL DEFAULT 'owned',
  
  -- QR (for individual ticket display — secondary to Bucket QR)
  qr_token        text UNIQUE,                    -- rotated on state changes; null after check-in
  
  -- Check-in record
  checked_in_at   timestamptz,
  checked_in_gate text,
  
  -- Financial
  amount_paid     integer NOT NULL DEFAULT 0,     -- UGX; 0 for comp/points/free
  currency        text NOT NULL DEFAULT 'UGX',
  payment_intent_id uuid,                         -- references payment_intents(id)
  
  -- Soft delete (refunded/voided tickets kept for audit)
  deleted_at      timestamptz,
  
  created_at      timestamptz NOT NULL DEFAULT now(),
  updated_at      timestamptz NOT NULL DEFAULT now()
);

CREATE INDEX idx_tickets_bucket ON tickets(bucket_id);
CREATE INDEX idx_tickets_gathering ON tickets(gathering_id, status);
CREATE INDEX idx_tickets_owner ON tickets(owner_participant_id, status);
CREATE INDEX idx_tickets_qr ON tickets(qr_token) WHERE qr_token IS NOT NULL;
```

### 10. Ticket Provenance

The provenance log. A deferred constraint trigger ensures no ticket exists without a valid row here. Append-only — no updates, no deletes.

```sql
CREATE TABLE ticket_provenance (
  id              uuid DEFAULT gen_random_uuid() PRIMARY KEY,
  ticket_id       uuid NOT NULL REFERENCES tickets(id),
  
  origin          ticket_origin NOT NULL,
  
  -- Source details (only relevant fields populated per origin type)
  source_participant_id  uuid REFERENCES participants(id),  -- for gift_transfer: the donor
  source_payment_intent  uuid,                              -- for purchase
  source_ticket_id       uuid REFERENCES tickets(id),       -- for group_split
  admin_authorised_by    uuid REFERENCES participants(id),  -- for migration
  admin_notes            text,
  
  recorded_at     timestamptz NOT NULL DEFAULT now()
  -- NO updated_at — this is an append-only audit log
);

CREATE INDEX idx_ticket_provenance_ticket ON ticket_provenance(ticket_id, recorded_at);
```

### 11. Ticket Transfers (Gift System)

Tracks the full lifecycle of every gift transfer.

```sql
CREATE TABLE ticket_transfers (
  id              uuid DEFAULT gen_random_uuid() PRIMARY KEY,
  ticket_id       uuid NOT NULL REFERENCES tickets(id),
  
  -- Parties
  donor_participant_id     uuid NOT NULL REFERENCES participants(id),
  recipient_phone          text NOT NULL,          -- E.164; recipient may not be on platform yet
  recipient_participant_id uuid REFERENCES participants(id),  -- null until they sign up
  
  -- Transfer token (sent in SMS/WhatsApp link)
  transfer_token  text NOT NULL UNIQUE,            -- 32-byte random hex, single-use
  
  -- Personal message
  personal_message text,
  
  -- Lifecycle
  status          text NOT NULL DEFAULT 'pending' CHECK (
                    status IN ('pending', 'pending_registration', 'accepted', 'rejected', 'expired', 'cancelled')
                  ),
  
  -- For group/table tickets: which seat(s) this transfer covers
  seats_transferred smallint NOT NULL DEFAULT 1,
  
  initiated_at    timestamptz NOT NULL DEFAULT now(),
  expires_at      timestamptz NOT NULL,            -- initiated_at + 72 hours
  resolved_at     timestamptz,                     -- when accepted/rejected/expired
  
  -- Monetary uplift (gift wrapping)
  gift_wrap_paid  boolean NOT NULL DEFAULT false,
  gift_wrap_amount integer,                        -- UGX paid for premium wrapping
  
  created_at      timestamptz NOT NULL DEFAULT now(),
  updated_at      timestamptz NOT NULL DEFAULT now()
);

CREATE INDEX idx_ticket_transfers_ticket ON ticket_transfers(ticket_id);
CREATE INDEX idx_ticket_transfers_token ON ticket_transfers(transfer_token) WHERE status = 'pending';
CREATE INDEX idx_ticket_transfers_recipient ON ticket_transfers(recipient_phone, status);
```

---

### 12. Check-ins

Every confirmed entry at a gathering. Terminal event — no updates after creation.

```sql
CREATE TABLE check_ins (
  id              uuid DEFAULT gen_random_uuid() PRIMARY KEY,
  
  -- What and who
  gathering_id    uuid NOT NULL REFERENCES gatherings(id),
  ticket_id       uuid NOT NULL UNIQUE REFERENCES tickets(id),  -- one check-in per ticket
  participant_id  uuid NOT NULL REFERENCES participants(id),
  
  -- Where (gate)
  gate_id         text,                            -- e.g. 'main_gate', 'vip_entrance'
  scanner_session_id text,                         -- the JWT jti of the scanner session
  
  -- Location verification
  gps_lat         numeric(10,7),
  gps_lng         numeric(10,7),
  gps_accuracy_m  numeric(6,2),
  
  -- Method
  scan_method     text CHECK (scan_method IN ('bucket_qr', 'ticket_qr', 'manual', 'supervisor_override')),
  
  -- Offline flag (synced from device queue)
  was_offline     boolean NOT NULL DEFAULT false,
  
  checked_in_at   timestamptz NOT NULL DEFAULT now()
  -- NO updated_at — terminal record
);

CREATE INDEX idx_check_ins_gathering ON check_ins(gathering_id, checked_in_at);
CREATE INDEX idx_check_ins_participant ON check_ins(participant_id);
```

---

### 13. Trust Scores

Computed trust state per participant. Updated by the Trust Engine whenever a new trust signal arrives. The score is internal — never exposed raw to users.

```sql
CREATE TABLE trust_scores (
  id              uuid DEFAULT gen_random_uuid() PRIMARY KEY,
  participant_id  uuid NOT NULL UNIQUE REFERENCES participants(id),
  
  -- Composite score (0–1000)
  score           integer NOT NULL DEFAULT 0 CHECK (score BETWEEN 0 AND 1000),
  
  -- Component sub-scores
  identity_score  integer NOT NULL DEFAULT 0,     -- derived from verification tier
  participation_score integer NOT NULL DEFAULT 0, -- derived from attendance history
  organiser_score integer NOT NULL DEFAULT 0,     -- derived from event completion rate
  financial_score integer NOT NULL DEFAULT 0,     -- derived from payment/payout history
  
  -- Tier derived from score
  trust_tier      text NOT NULL DEFAULT 'new' CHECK (
                    trust_tier IN ('new', 'emerging', 'established', 'trusted', 'verified_trusted')
                  ),
  
  last_computed_at timestamptz NOT NULL DEFAULT now(),
  signal_count     integer NOT NULL DEFAULT 0,    -- total signals processed
  
  created_at      timestamptz NOT NULL DEFAULT now(),
  updated_at      timestamptz NOT NULL DEFAULT now()
);
```

### 14. Trust Signals

Raw signal events that feed the Trust Engine. Append-only.

```sql
CREATE TABLE trust_signals (
  id              uuid DEFAULT gen_random_uuid() PRIMARY KEY,
  participant_id  uuid NOT NULL REFERENCES participants(id),
  
  signal_type     trust_signal_type NOT NULL,
  
  -- Signal weight at time of recording (can change as model evolves)
  weight          integer NOT NULL,               -- positive or negative
  
  -- Context
  related_gathering_id  uuid REFERENCES gatherings(id),
  related_ticket_id     uuid REFERENCES tickets(id),
  related_payment_id    uuid,
  notes                 text,
  
  recorded_at     timestamptz NOT NULL DEFAULT now()
);

CREATE INDEX idx_trust_signals_participant ON trust_signals(participant_id, recorded_at DESC);
```

---

### 15. Follows

Participant follow relationships. A participant can follow a venue, another participant, or a category.

```sql
CREATE TABLE follows (
  id              uuid DEFAULT gen_random_uuid() PRIMARY KEY,
  follower_id     uuid NOT NULL REFERENCES participants(id),
  
  -- Target (only one of these is non-null)
  target_type     follow_target_type NOT NULL,
  target_venue_id        uuid REFERENCES venues(id),
  target_participant_id  uuid REFERENCES participants(id),
  target_category        gathering_category,
  
  followed_at     timestamptz NOT NULL DEFAULT now(),
  
  CONSTRAINT follow_target_exclusive CHECK (
    (target_venue_id IS NOT NULL)::int +
    (target_participant_id IS NOT NULL)::int +
    (target_category IS NOT NULL)::int = 1
  ),
  UNIQUE (follower_id, target_type, target_venue_id, target_participant_id, target_category)
);

CREATE INDEX idx_follows_follower ON follows(follower_id);
CREATE INDEX idx_follows_venue ON follows(target_venue_id) WHERE target_venue_id IS NOT NULL;
CREATE INDEX idx_follows_participant ON follows(target_participant_id) WHERE target_participant_id IS NOT NULL;
```

---

### 16. Attendance Memories

The Memory OS seed. Every confirmed check-in creates an attendance memory. Unlike check_ins (operational), memories are the preserved participation record — portable, personal, permanent.

```sql
CREATE TABLE attendance_memories (
  id              uuid DEFAULT gen_random_uuid() PRIMARY KEY,
  participant_id  uuid NOT NULL REFERENCES participants(id),
  gathering_id    uuid NOT NULL REFERENCES gatherings(id),
  check_in_id     uuid NOT NULL UNIQUE REFERENCES check_ins(id),
  
  -- Preserved at time of check-in (denormalised — gathering may change later)
  gathering_title       text NOT NULL,
  gathering_category    gathering_category NOT NULL,
  venue_name            text,
  neighbourhood         text,
  city                  text NOT NULL,
  attended_at           timestamptz NOT NULL,
  
  -- Participant's own annotations (added later)
  personal_note         text,
  private_rating        smallint CHECK (private_rating BETWEEN 1 AND 5),
  
  -- Privacy
  is_private            boolean NOT NULL DEFAULT true,  -- private by default
  
  created_at            timestamptz NOT NULL DEFAULT now(),
  updated_at            timestamptz NOT NULL DEFAULT now()
);

CREATE INDEX idx_attendance_memories_participant ON attendance_memories(participant_id, attended_at DESC);
```

### 17. Pulse Points Ledger

Immutable ledger of all Pulse Points transactions. Never update — only append. Balance is the running sum.

```sql
CREATE TABLE pulse_points_ledger (
  id              uuid DEFAULT gen_random_uuid() PRIMARY KEY,
  participant_id  uuid NOT NULL REFERENCES participants(id),
  
  transaction_type pulse_transaction_type NOT NULL,
  
  amount          integer NOT NULL,               -- positive = earn, negative = burn
  balance_after   integer NOT NULL,               -- denormalised running total
  
  -- Context
  related_gathering_id  uuid REFERENCES gatherings(id),
  related_ticket_id     uuid REFERENCES tickets(id),
  related_participant_id uuid REFERENCES participants(id),  -- for referral/gift context
  notes                 text,
  
  -- Anti-farming: GPS proximity snapshot at time of check-in earn
  earn_gps_lat    numeric(10,7),
  earn_gps_lng    numeric(10,7),
  earn_verified   boolean NOT NULL DEFAULT true,  -- false if proximity check failed
  
  recorded_at     timestamptz NOT NULL DEFAULT now()
  -- NO updated_at — append-only ledger
);

CREATE INDEX idx_pulse_points_participant ON pulse_points_ledger(participant_id, recorded_at DESC);
```

---

### 18. Payment Intents

Tracks the full lifecycle of every payment. One record per purchase attempt.

```sql
CREATE TABLE payment_intents (
  id              uuid DEFAULT gen_random_uuid() PRIMARY KEY,
  participant_id  uuid NOT NULL REFERENCES participants(id),
  
  -- What is being paid for
  gathering_id    uuid REFERENCES gatherings(id),
  tier_id         uuid REFERENCES gathering_tiers(id),
  quantity        smallint NOT NULL DEFAULT 1,
  
  -- Amount
  subtotal        integer NOT NULL,               -- UGX: price × quantity
  convenience_fee integer NOT NULL DEFAULT 0,     -- UGX: disclosed upfront
  total           integer NOT NULL,               -- subtotal + convenience_fee
  currency        text NOT NULL DEFAULT 'UGX',
  
  -- Payment method
  payment_method  payment_method NOT NULL,
  
  -- External payment processor reference
  processor       text NOT NULL,                  -- 'flutterwave', 'iotec'
  processor_ref   text,                           -- processor transaction ID
  processor_payload jsonb,                        -- full processor response (encrypted at rest)
  
  -- Status lifecycle
  status          payment_status NOT NULL DEFAULT 'initiated',
  
  -- Failure handling
  failure_reason  text,
  retry_count     smallint NOT NULL DEFAULT 0,
  
  -- Mobile Money specific
  ussd_push_sent_at  timestamptz,                 -- when USSD push was dispatched
  ussd_confirmed_at  timestamptz,                 -- when participant entered PIN
  
  -- Timestamps
  initiated_at    timestamptz NOT NULL DEFAULT now(),
  succeeded_at    timestamptz,
  failed_at       timestamptz,
  refunded_at     timestamptz,
  
  created_at      timestamptz NOT NULL DEFAULT now(),
  updated_at      timestamptz NOT NULL DEFAULT now()
);

CREATE INDEX idx_payment_intents_participant ON payment_intents(participant_id, created_at DESC);
CREATE INDEX idx_payment_intents_gathering ON payment_intents(gathering_id);
CREATE INDEX idx_payment_intents_processor_ref ON payment_intents(processor, processor_ref) WHERE processor_ref IS NOT NULL;
```

### 19. Payouts

Organiser payouts. One payout per gathering (after event date passes), or per payout period for recurring events.

```sql
CREATE TABLE payouts (
  id              uuid DEFAULT gen_random_uuid() PRIMARY KEY,
  organiser_id    uuid NOT NULL REFERENCES participants(id),
  gathering_id    uuid REFERENCES gatherings(id),
  
  -- Amounts (all UGX)
  gross_revenue   integer NOT NULL,               -- sum of ticket sales
  commission      integer NOT NULL,               -- Zuka commission
  processing_fees integer NOT NULL DEFAULT 0,     -- pass-through payment processing fees
  net_amount      integer NOT NULL,               -- gross - commission - processing_fees
  
  -- Destination
  payout_method   payment_method NOT NULL,
  payout_account  text NOT NULL,                  -- phone number or bank account (encrypted)
  
  -- Status
  status          payout_status NOT NULL DEFAULT 'queued',
  
  -- Processor
  processor_ref   text,
  processor_payload jsonb,
  
  failure_reason  text,
  attempt_count   smallint NOT NULL DEFAULT 0,
  
  -- Timestamps
  queued_at       timestamptz NOT NULL DEFAULT now(),
  processing_at   timestamptz,
  succeeded_at    timestamptz,
  failed_at       timestamptz,
  held_at         timestamptz,
  
  created_at      timestamptz NOT NULL DEFAULT now(),
  updated_at      timestamptz NOT NULL DEFAULT now()
);

CREATE INDEX idx_payouts_organiser ON payouts(organiser_id, created_at DESC);
CREATE INDEX idx_payouts_status ON payouts(status) WHERE status IN ('queued', 'processing', 'failed');
```

### 20. Wallet Ledger

Event Wallet balance ledger. Append-only. Balance = running sum per participant.

```sql
CREATE TABLE wallet_ledger (
  id              uuid DEFAULT gen_random_uuid() PRIMARY KEY,
  participant_id  uuid NOT NULL REFERENCES participants(id),
  
  -- Transaction
  transaction_type text NOT NULL CHECK (transaction_type IN ('topup', 'purchase', 'refund', 'deletion_payout')),
  amount          integer NOT NULL,               -- positive = credit, negative = debit
  balance_after   integer NOT NULL,               -- must be >= 0; enforced in application layer
  
  -- Context
  related_payment_intent_id uuid REFERENCES payment_intents(id),
  related_gathering_id      uuid REFERENCES gatherings(id),
  
  recorded_at     timestamptz NOT NULL DEFAULT now()
  -- NO updated_at — append-only
);

CREATE INDEX idx_wallet_ledger_participant ON wallet_ledger(participant_id, recorded_at DESC);

-- Maximum balance constraint enforced at application layer (UGX 500,000)
-- Regulatory: closed-loop prepaid instrument classification (Bank of Uganda)
```

---

## Schema Conventions Reference

| Convention | Rule |
|-----------|------|
| Primary key | `uuid DEFAULT gen_random_uuid()` always |
| Monetary values | Integer, smallest currency unit. UGX has no subunits — store whole shillings |
| Phone numbers | E.164 format (`+256701234567`) always |
| Percentages | Integer basis points (500 = 5.00%) |
| Timestamps | `timestamptz` always; never `timestamp` without timezone |
| Soft deletes | `deleted_at timestamptz` on participant and financial records; never hard delete |
| Audit logs | No `updated_at` on append-only tables (provenance, signals, ledgers, check-ins) |
| Triggers | `updated_at` managed by a single shared trigger function across all tables |
| Encryption at rest | PII fields (payout_account, processor_payload) encrypted at the application layer before write |
| Indexes | Created for every foreign key and every filtering pattern in the Feed and Search engines |

---

## Constraint Rules (Non-Negotiable)

These rules are enforced at the database level, not just the application layer:

1. **Provenance completeness** — A deferred constraint trigger verifies that every ticket row has at least one corresponding `ticket_provenance` row before the transaction commits. No ticket may exist without a valid origin.

2. **Single check-in per ticket** — `UNIQUE (ticket_id)` on `check_ins` ensures a ticket cannot be scanned twice.

3. **Wallet non-negative** — `balance_after >= 0` is enforced in the application layer before every wallet debit. A wallet can never go negative.

4. **Pulse Points daily cap** — Application layer enforces a maximum of 500 points earned per participant per calendar day.

5. **Go Live monthly cap** — Unverified venues: 1 Go Live per calendar month. Standard verified: unlimited. Enforced via `go_live_count_month` on the venues table, reset by a monthly cron job.

6. **Transfer token single-use** — Transfer tokens are invalidated on first resolution (accepted/rejected/expired). The `UNIQUE` constraint on `transfer_token` prevents replay.

7. **Tier count limit** — Maximum 8 tiers per gathering enforced via CHECK constraint.

---

## Status Codes

| Entity | Status | Terminal? |
|--------|--------|-----------|
| Gathering | `draft → published → active → live → ended → closed` | `closed`, `cancelled` |
| Ticket | `owned → gift_pending → (transferred or back to owned) → checked_in` | `checked_in`, `refunded`, `void` |
| Payment | `initiated → pending_confirmation → succeeded` | `succeeded`, `failed`, `refunded` |
| Payout | `queued → processing → succeeded` | `succeeded`, `held` |
| Transfer | `pending → (accepted or rejected or expired)` | `accepted`, `rejected`, `expired` |

---

*Authority: Book II (Canonical Domain Model) · Book III (Identity) · Book IV (Memory) · Book VII (Trust)*  
*Next: ZUKA_Event_Catalog.md*
