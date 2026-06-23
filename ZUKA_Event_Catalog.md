# ZUKA Event Catalog

**Version:** 1.0  
**Status:** Active — MVP Phase 1  
**Authority:** Derived from Book II (Canonical Domain Model), `ZUKA_Canonical_Schema_Pack.md`  
**Stack:** Supabase Realtime / PostgreSQL LISTEN/NOTIFY / Edge Functions  

---

## Overview

This document is the canonical catalog of all **domain events** in the ZUKA platform. A domain event is an immutable record of something significant that happened. Domain events are the primary mechanism for:

- Triggering downstream side effects (notifications, trust signal ingestion, analytics, denormalised counter updates)
- Feeding the Realtime feed (home feed, heat map, scanner dashboard)
- Building the audit trail
- Eventually powering the Memory OS and Intelligence layer

**Every domain event has:**
- A unique `event_type` string in `DOMAIN.EntityName.action_past_tense` format
- A stable payload schema (fields are additive — never remove fields from a published event)
- An `event_id` (UUID)
- An `occurred_at` timestamp
- An `aggregate_id` (the primary entity this event concerns)
- A `causation_id` (the upstream event or request that caused this event, if any)

---

## Event Transport

At MVP, domain events are transported via two mechanisms:

**1. Supabase Database Webhooks / pg_notify**  
For server-side consumers (Edge Functions): events are inserted into the `domain_events` table; a pg_notify trigger fires on insert; Edge Functions subscribe via LISTEN.

**2. Supabase Realtime Channels**  
For client-side consumers (Flutter app): selected events are broadcast on named channels (e.g. `gathering:{id}:events`, `city:{city}:live_feed`).

---

## Domain Events Table

All events are written to a single append-only table before being routed.

```sql
CREATE TABLE domain_events (
  event_id        uuid DEFAULT gen_random_uuid() PRIMARY KEY,
  event_type      text NOT NULL,                  -- e.g. 'GATHERING.Gathering.published'
  aggregate_type  text NOT NULL,                  -- e.g. 'Gathering'
  aggregate_id    uuid NOT NULL,                  -- the primary entity's ID
  
  payload         jsonb NOT NULL,                 -- event-specific data (see catalog below)
  metadata        jsonb NOT NULL DEFAULT '{}',    -- causation_id, correlation_id, actor_id, etc.
  
  occurred_at     timestamptz NOT NULL DEFAULT now(),
  
  -- Routing state
  processed       boolean NOT NULL DEFAULT false,
  processed_at    timestamptz,
  processing_error text
);

CREATE INDEX idx_domain_events_type ON domain_events(event_type, occurred_at DESC);
CREATE INDEX idx_domain_events_aggregate ON domain_events(aggregate_type, aggregate_id, occurred_at DESC);
CREATE INDEX idx_domain_events_unprocessed ON domain_events(occurred_at) WHERE processed = false;
```

---

## Event Catalog

### Domain: IDENTITY

---

#### `IDENTITY.Participant.registered`

Fired when a participant completes OTP verification for the first time. This is the true creation moment — the participant entity now exists.

```json
{
  "event_type": "IDENTITY.Participant.registered",
  "aggregate_type": "Participant",
  "aggregate_id": "<participant_id>",
  "payload": {
    "participant_id": "uuid",
    "phone": "+256701234567",
    "auth_method": "otp | google | apple",
    "city": "Kampala",
    "referred_by_participant_id": "uuid | null",
    "referral_code_used": "string | null"
  }
}
```

**Side effects:**
- Create `buckets` row (generate qr_token)
- Create `participant_profiles` row
- Create `trust_signals` row: `identity_verified` (if phone verified)
- Award Pulse Points: `earn_signup` (100 pts)
- If `referred_by_participant_id` is set: trigger referral reward flow

---

#### `IDENTITY.Participant.profile_completed`

Fired when a participant completes the optional post-signup profile fields (name + photo + neighbourhood).

```json
{
  "event_type": "IDENTITY.Participant.profile_completed",
  "aggregate_type": "Participant",
  "aggregate_id": "<participant_id>",
  "payload": {
    "participant_id": "uuid",
    "fields_completed": ["display_name", "avatar_url", "city_zone"]
  }
}
```

**Side effects:**
- Award Pulse Points: `earn_profile_complete` (50 pts)

---

#### `IDENTITY.Participant.verification_tier_changed`

Fired when a participant's verification tier is upgraded or downgraded.

```json
{
  "event_type": "IDENTITY.Participant.verification_tier_changed",
  "aggregate_type": "Participant",
  "aggregate_id": "<participant_id>",
  "payload": {
    "participant_id": "uuid",
    "previous_tier": "unverified | standard | pro",
    "new_tier": "unverified | standard | pro",
    "changed_by": "uuid",
    "reason": "string"
  }
}
```

**Side effects:**
- Recompute `trust_scores` for participant
- If `new_tier == 'standard'`: emit `trust_signals` row `identity_verified`
- Update `venues` owned by this participant if tier affects venue capabilities

---

#### `IDENTITY.Participant.suspended`

```json
{
  "event_type": "IDENTITY.Participant.suspended",
  "aggregate_type": "Participant",
  "aggregate_id": "<participant_id>",
  "payload": {
    "participant_id": "uuid",
    "reason": "string",
    "suspended_by": "uuid",
    "expected_review_at": "timestamptz | null"
  }
}
```

**Side effects:**
- Cancel any active scanner sessions for this participant
- Invalidate bucket QR token
- Notify participant via SMS

---

#### `IDENTITY.Participant.deletion_scheduled`

Fired when a participant initiates account deletion. 30-day grace period starts now.

```json
{
  "event_type": "IDENTITY.Participant.deletion_scheduled",
  "aggregate_type": "Participant",
  "aggregate_id": "<participant_id>",
  "payload": {
    "participant_id": "uuid",
    "deletion_scheduled_at": "timestamptz",
    "final_deletion_at": "timestamptz",
    "wallet_balance_to_refund": 0,
    "tickets_in_escrow_count": 0
  }
}
```

**Side effects:**
- Return any tickets in gift escrow to donors (fire `TICKETING.TicketTransfer.expired` for each)
- Schedule wallet balance refund if `wallet_balance_to_refund > 0`
- Suspend account immediately (no new actions possible during grace period)

---

### Domain: VENUE

---

#### `VENUE.Venue.created`

```json
{
  "event_type": "VENUE.Venue.created",
  "aggregate_type": "Venue",
  "aggregate_id": "<venue_id>",
  "payload": {
    "venue_id": "uuid",
    "owner_participant_id": "uuid",
    "name": "string",
    "slug": "string",
    "neighbourhood": "string",
    "city": "Kampala",
    "lat": 0.0,
    "lng": 0.0,
    "category": "gathering_category"
  }
}
```

---

#### `VENUE.Venue.went_live`

Fired when an organiser taps Go Live. This is the most time-sensitive event in the platform — it must be processed and broadcast within 10 seconds of the tap.

```json
{
  "event_type": "VENUE.Venue.went_live",
  "aggregate_type": "Venue",
  "aggregate_id": "<venue_id>",
  "payload": {
    "venue_id": "uuid",
    "venue_name": "string",
    "neighbourhood": "string",
    "city": "Kampala",
    "lat": 0.0,
    "lng": 0.0,
    "went_live_at": "timestamptz",
    "category": "gathering_category",
    "crowd_level": "quiet | picking_up | packed | at_capacity",
    "follower_count": 0,
    "audio_streaming_enabled": false
  }
}
```

**Side effects (within 10 seconds):**
- Broadcast on `city:Kampala:live_feed` Realtime channel
- Push notification to all followers within 5km radius
- Update heat map pin (bump to top of Live Now feed)
- Insert `domain_events` row for analytics pipeline

---

#### `VENUE.Venue.went_offline`

```json
{
  "event_type": "VENUE.Venue.went_offline",
  "aggregate_type": "Venue",
  "aggregate_id": "<venue_id>",
  "payload": {
    "venue_id": "uuid",
    "went_offline_at": "timestamptz",
    "live_duration_minutes": 0
  }
}
```

**Side effects:**
- Remove from Live Now feed
- Update heat map pin
- Log live session duration to analytics

---

#### `VENUE.Venue.crowd_level_updated`

```json
{
  "event_type": "VENUE.Venue.crowd_level_updated",
  "aggregate_type": "Venue",
  "aggregate_id": "<venue_id>",
  "payload": {
    "venue_id": "uuid",
    "previous_level": "quiet | picking_up | packed | at_capacity",
    "new_level": "quiet | picking_up | packed | at_capacity",
    "source": "manual | checkin_inference",
    "updated_at": "timestamptz"
  }
}
```

**Side effects:**
- If `new_level == 'packed'`: push notification to followers within 5km who follow this venue
- Broadcast on `city:Kampala:live_feed` channel (heat map update)

---

#### `VENUE.Venue.verification_tier_changed`

```json
{
  "event_type": "VENUE.Venue.verification_tier_changed",
  "aggregate_type": "Venue",
  "aggregate_id": "<venue_id>",
  "payload": {
    "venue_id": "uuid",
    "owner_participant_id": "uuid",
    "previous_tier": "unverified | standard | pro",
    "new_tier": "unverified | standard | pro",
    "reviewed_by": "uuid",
    "reason": "string"
  }
}
```

**Side effects:**
- If upgraded to `standard`: unlock audio streaming, unlimited Go Live, priority search rank
- If upgraded to `pro`: unlock promoted events, direct follower messaging, analytics dashboard
- Notify venue owner via push + SMS

---

### Domain: GATHERING

---

#### `GATHERING.Gathering.published`

Fired when an organiser publishes a gathering (moves from `draft` to `published`).

```json
{
  "event_type": "GATHERING.Gathering.published",
  "aggregate_type": "Gathering",
  "aggregate_id": "<gathering_id>",
  "payload": {
    "gathering_id": "uuid",
    "organiser_id": "uuid",
    "venue_id": "uuid | null",
    "title": "string",
    "category": "gathering_category",
    "vibe_tags": ["string"],
    "format": "gathering_format",
    "starts_at": "timestamptz | null",
    "is_free": true,
    "min_price": 0,
    "max_price": 0,
    "location_neighbourhood": "string",
    "location_city": "Kampala",
    "location_lat": 0.0,
    "location_lng": 0.0,
    "total_capacity": null
  }
}
```

**Side effects:**
- Index gathering in search engine
- Add to Tonight / Upcoming feed for relevant city zones
- Notify category followers (participants who follow this `gathering_category`)
- Notify venue followers if `venue_id` is set

---

#### `GATHERING.Gathering.went_live`

Fired when a gathering transitions to `live` status — either via Go Live tap or when check-in window opens.

```json
{
  "event_type": "GATHERING.Gathering.went_live",
  "aggregate_type": "Gathering",
  "aggregate_id": "<gathering_id>",
  "payload": {
    "gathering_id": "uuid",
    "title": "string",
    "category": "gathering_category",
    "venue_id": "uuid | null",
    "venue_name": "string | null",
    "location_neighbourhood": "string",
    "location_lat": 0.0,
    "location_lng": 0.0,
    "went_live_at": "timestamptz",
    "is_go_live": true,
    "tickets_remaining": null
  }
}
```

**Side effects:**
- Promote to top of Live Now feed
- Broadcast on `city:Kampala:live_feed`
- Push notification to RSVP holders
- Push notification to followers of the venue/organiser within 5km

---

#### `GATHERING.Gathering.capacity_threshold_crossed`

Fired at 70% and 90% capacity to trigger "Filling up fast" signals.

```json
{
  "event_type": "GATHERING.Gathering.capacity_threshold_crossed",
  "aggregate_type": "Gathering",
  "aggregate_id": "<gathering_id>",
  "payload": {
    "gathering_id": "uuid",
    "title": "string",
    "threshold_percent": 70,
    "tickets_sold": 0,
    "total_capacity": 0,
    "tickets_remaining": 0
  }
}
```

**Side effects:**
- If `threshold_percent == 70`: show "Filling up fast" badge in feed
- If `threshold_percent == 90`: push notification to users who RSVPed but haven't purchased

---

#### `GATHERING.Gathering.cancelled`

```json
{
  "event_type": "GATHERING.Gathering.cancelled",
  "aggregate_type": "Gathering",
  "aggregate_id": "<gathering_id>",
  "payload": {
    "gathering_id": "uuid",
    "organiser_id": "uuid",
    "title": "string",
    "cancelled_at": "timestamptz",
    "reason": "string",
    "tickets_sold_count": 0,
    "refund_amount_total": 0
  }
}
```

**Side effects:**
- Push + SMS notification to all ticket holders
- Initiate refund intents for all sold tickets
- Remove from all active feeds
- Log `trust_signals` row: `event_cancelled` for organiser
- If organiser cancellation_count >= 3 in 90 days: flag for account review

---

#### `GATHERING.Gathering.ended`

Fired when end_time passes (or organiser manually ends the event).

```json
{
  "event_type": "GATHERING.Gathering.ended",
  "aggregate_type": "Gathering",
  "aggregate_id": "<gathering_id>",
  "payload": {
    "gathering_id": "uuid",
    "organiser_id": "uuid",
    "title": "string",
    "ended_at": "timestamptz",
    "total_check_ins": 0,
    "total_tickets_sold": 0,
    "gross_revenue": 0
  }
}
```

**Side effects:**
- Close check-in window (scanner sessions invalidated)
- Queue payout intent for organiser
- Log `trust_signals` row: `event_completed` for organiser
- Trigger post-event report generation
- Open review window for checked-in participants (30-min delay)

---

### Domain: TICKETING

---

#### `TICKETING.Ticket.purchased`

Fired when a ticket purchase payment succeeds and tickets are issued.

```json
{
  "event_type": "TICKETING.Ticket.purchased",
  "aggregate_type": "Ticket",
  "aggregate_id": "<ticket_id>",
  "payload": {
    "ticket_id": "uuid",
    "participant_id": "uuid",
    "bucket_id": "uuid",
    "gathering_id": "uuid",
    "tier_id": "uuid",
    "tier_name": "string",
    "ticket_class": "ticket_class",
    "amount_paid": 0,
    "currency": "UGX",
    "payment_intent_id": "uuid",
    "payment_method": "payment_method",
    "provenance": "purchase"
  }
}
```

**Side effects:**
- Insert `ticket_provenance` row
- Increment `gathering_tiers.quantity_sold`
- Increment `gatherings.tickets_sold`
- Check capacity thresholds (fire `capacity_threshold_crossed` if needed)
- Push notification + SMS confirmation to buyer
- Award Pulse Points: `earn_ticket_purchase` (100 pts)
- Rotate bucket QR token

---

#### `TICKETING.Ticket.gift_initiated`

Fired when a participant starts the gift transfer process.

```json
{
  "event_type": "TICKETING.Ticket.gift_initiated",
  "aggregate_type": "Ticket",
  "aggregate_id": "<ticket_id>",
  "payload": {
    "ticket_id": "uuid",
    "donor_participant_id": "uuid",
    "recipient_phone": "+256...",
    "recipient_participant_id": "uuid | null",
    "transfer_id": "uuid",
    "personal_message": "string | null",
    "seats_transferred": 1,
    "expires_at": "timestamptz",
    "recipient_is_on_platform": true,
    "gift_wrap_paid": false
  }
}
```

**Side effects:**
- Set `ticket.status = 'gift_pending'`
- Invalidate donor's current QR token for this ticket
- Send SMS + WhatsApp to recipient phone with transfer link
- If `recipient_is_on_platform == false`: set `status = 'gift_pending_reg'`
- If `recipient_is_on_platform == true`: push notification to recipient

---

#### `TICKETING.Ticket.gift_accepted`

```json
{
  "event_type": "TICKETING.Ticket.gift_accepted",
  "aggregate_type": "Ticket",
  "aggregate_id": "<ticket_id>",
  "payload": {
    "ticket_id": "uuid",
    "transfer_id": "uuid",
    "donor_participant_id": "uuid",
    "recipient_participant_id": "uuid",
    "accepted_at": "timestamptz",
    "was_new_user": false
  }
}
```

**Side effects:**
- Set `ticket.status = 'transferred'`, update `owner_participant_id`
- Move ticket to recipient's bucket
- Rotate QR token (recipient gets fresh QR)
- Push notification to donor: "Your gift was accepted"
- Award Pulse Points: donor `earn_gift_given` (75 pts), recipient `earn_gift_received` (50 pts)
- If `was_new_user == true`: gift acquisition attributed to donor; track LTV for 30 days
- Insert `ticket_provenance` row: `gift_transfer`

---

#### `TICKETING.Ticket.gift_rejected`

```json
{
  "event_type": "TICKETING.Ticket.gift_rejected",
  "aggregate_type": "Ticket",
  "aggregate_id": "<ticket_id>",
  "payload": {
    "ticket_id": "uuid",
    "transfer_id": "uuid",
    "donor_participant_id": "uuid",
    "recipient_participant_id": "uuid | null",
    "rejected_at": "timestamptz"
  }
}
```

**Side effects:**
- Return ticket to donor: set `status = 'owned'`, update `owner_participant_id` back
- Issue fresh QR token to donor
- Push notification to donor: "Your gift was declined"

---

#### `TICKETING.TicketTransfer.expired`

Fired when a gift transfer reaches its 72-hour expiry without resolution.

```json
{
  "event_type": "TICKETING.TicketTransfer.expired",
  "aggregate_type": "TicketTransfer",
  "aggregate_id": "<transfer_id>",
  "payload": {
    "transfer_id": "uuid",
    "ticket_id": "uuid",
    "donor_participant_id": "uuid",
    "recipient_phone": "+256...",
    "expired_at": "timestamptz"
  }
}
```

**Side effects:**
- Return ticket to donor (same as gift_rejected side effects)
- Push notification to donor: "Your gift link expired — ticket returned to your bucket"

---

#### `TICKETING.Ticket.checked_in`

Fired when a ticket is successfully validated at a gate.

```json
{
  "event_type": "TICKETING.Ticket.checked_in",
  "aggregate_type": "Ticket",
  "aggregate_id": "<ticket_id>",
  "payload": {
    "ticket_id": "uuid",
    "participant_id": "uuid",
    "gathering_id": "uuid",
    "gate_id": "string",
    "ticket_class": "ticket_class",
    "checked_in_at": "timestamptz",
    "scan_method": "bucket_qr | ticket_qr | manual | supervisor_override",
    "was_offline": false
  }
}
```

**Side effects:**
- Set `ticket.status = 'checked_in'`
- Null out `ticket.qr_token` (permanently invalid)
- Insert `check_ins` row
- Insert `attendance_memories` row
- Increment `gatherings.check_ins_count`
- Broadcast on `gathering:{id}:live_dashboard` (organiser dashboard update)
- Award Pulse Points: `earn_checkin` (50 pts) — if first check-in ever: `earn_first_checkin` (150 pts)
- Update crowd inference model (check-in velocity signal)

---

#### `TICKETING.Ticket.refunded`

```json
{
  "event_type": "TICKETING.Ticket.refunded",
  "aggregate_type": "Ticket",
  "aggregate_id": "<ticket_id>",
  "payload": {
    "ticket_id": "uuid",
    "participant_id": "uuid",
    "gathering_id": "uuid",
    "amount_refunded": 0,
    "refund_method": "payment_method",
    "refund_reason": "event_cancelled | organiser_dispute_resolved | admin",
    "refunded_at": "timestamptz"
  }
}
```

**Side effects:**
- Set `ticket.status = 'refunded'`
- Decrement `gatherings.tickets_sold`
- Decrement `gathering_tiers.quantity_sold`
- Initiate reverse payment to original payment method

---

### Domain: PAYMENT

---

#### `PAYMENT.PaymentIntent.initiated`

```json
{
  "event_type": "PAYMENT.PaymentIntent.initiated",
  "aggregate_type": "PaymentIntent",
  "aggregate_id": "<payment_intent_id>",
  "payload": {
    "payment_intent_id": "uuid",
    "participant_id": "uuid",
    "gathering_id": "uuid",
    "tier_id": "uuid",
    "quantity": 1,
    "total": 0,
    "currency": "UGX",
    "payment_method": "payment_method",
    "processor": "flutterwave | iotec"
  }
}
```

---

#### `PAYMENT.PaymentIntent.succeeded`

```json
{
  "event_type": "PAYMENT.PaymentIntent.succeeded",
  "aggregate_type": "PaymentIntent",
  "aggregate_id": "<payment_intent_id>",
  "payload": {
    "payment_intent_id": "uuid",
    "participant_id": "uuid",
    "gathering_id": "uuid",
    "amount": 0,
    "currency": "UGX",
    "payment_method": "payment_method",
    "processor_ref": "string",
    "succeeded_at": "timestamptz"
  }
}
```

**Side effects:**
- Issue tickets (trigger `TICKETING.Ticket.purchased`)
- SMS confirmation to buyer

---

#### `PAYMENT.PaymentIntent.failed`

```json
{
  "event_type": "PAYMENT.PaymentIntent.failed",
  "aggregate_type": "PaymentIntent",
  "aggregate_id": "<payment_intent_id>",
  "payload": {
    "payment_intent_id": "uuid",
    "participant_id": "uuid",
    "failure_reason": "string",
    "failure_code": "network_timeout | insufficient_funds | pin_incorrect | cancelled",
    "retry_count": 0,
    "will_auto_retry": false
  }
}
```

---

#### `PAYMENT.Payout.queued`

```json
{
  "event_type": "PAYMENT.Payout.queued",
  "aggregate_type": "Payout",
  "aggregate_id": "<payout_id>",
  "payload": {
    "payout_id": "uuid",
    "organiser_id": "uuid",
    "gathering_id": "uuid",
    "gross_revenue": 0,
    "commission": 0,
    "net_amount": 0,
    "currency": "UGX",
    "payout_method": "payment_method",
    "queued_at": "timestamptz"
  }
}
```

---

#### `PAYMENT.Payout.succeeded`

```json
{
  "event_type": "PAYMENT.Payout.succeeded",
  "aggregate_type": "Payout",
  "aggregate_id": "<payout_id>",
  "payload": {
    "payout_id": "uuid",
    "organiser_id": "uuid",
    "gathering_id": "uuid",
    "net_amount": 0,
    "currency": "UGX",
    "processor_ref": "string",
    "succeeded_at": "timestamptz"
  }
}
```

**Side effects:**
- Push + email notification to organiser: "Your payout of UGX X has been sent"
- Log `trust_signals` row: `payout_received`

---

#### `PAYMENT.Payout.held`

Fired when 3 payout attempts have failed and funds are held in organiser balance.

```json
{
  "event_type": "PAYMENT.Payout.held",
  "aggregate_type": "Payout",
  "aggregate_id": "<payout_id>",
  "payload": {
    "payout_id": "uuid",
    "organiser_id": "uuid",
    "net_amount": 0,
    "failure_reason": "string",
    "held_at": "timestamptz"
  }
}
```

**Side effects:**
- Push + SMS to organiser: "We couldn't process your payout. Please update your payout account in the app."
- Flag for manual review

---

### Domain: TRUST

---

#### `TRUST.TrustScore.recomputed`

Fired after the Trust Engine processes new signals and updates a participant's score.

```json
{
  "event_type": "TRUST.TrustScore.recomputed",
  "aggregate_type": "TrustScore",
  "aggregate_id": "<trust_score_id>",
  "payload": {
    "participant_id": "uuid",
    "previous_score": 0,
    "new_score": 0,
    "previous_tier": "string",
    "new_tier": "string",
    "trigger_signal_type": "trust_signal_type",
    "computed_at": "timestamptz"
  }
}
```

**Side effects:**
- If `new_tier != previous_tier`: log tier change, update participant's feed ranking weight
- If score drops below 100: flag for review

---

### Domain: COMMUNITY

---

#### `COMMUNITY.Follow.created`

```json
{
  "event_type": "COMMUNITY.Follow.created",
  "aggregate_type": "Follow",
  "aggregate_id": "<follow_id>",
  "payload": {
    "follower_id": "uuid",
    "target_type": "venue | participant | gathering_category",
    "target_id": "uuid | null",
    "target_category": "gathering_category | null",
    "followed_at": "timestamptz"
  }
}
```

**Side effects:**
- Increment venue/participant follower count
- Award Pulse Points: `earn_follow` (10 pts)
- If following a venue: subscribe participant to that venue's Go Live notifications

---

#### `COMMUNITY.Follow.removed`

```json
{
  "event_type": "COMMUNITY.Follow.removed",
  "aggregate_type": "Follow",
  "aggregate_id": "<follow_id>",
  "payload": {
    "follower_id": "uuid",
    "target_type": "venue | participant | gathering_category",
    "target_id": "uuid | null",
    "removed_at": "timestamptz"
  }
}
```

**Side effects:**
- Decrement follower count
- Unsubscribe from Go Live notifications for this venue

---

#### `COMMUNITY.CoAttendance.detected`

Fired when two participants who follow each other both check in to the same gathering.

```json
{
  "event_type": "COMMUNITY.CoAttendance.detected",
  "aggregate_type": "Gathering",
  "aggregate_id": "<gathering_id>",
  "payload": {
    "gathering_id": "uuid",
    "participant_a_id": "uuid",
    "participant_b_id": "uuid",
    "detected_at": "timestamptz"
  }
}
```

**Side effects:**
- Push notification to both participants: "Your friend is also at [Event Name]"
- Only sent if neither participant has `is_invisible = true`

---

### Domain: MEMORY

---

#### `MEMORY.AttendanceMemory.created`

Fired when an attendance memory is created from a check-in.

```json
{
  "event_type": "MEMORY.AttendanceMemory.created",
  "aggregate_type": "AttendanceMemory",
  "aggregate_id": "<memory_id>",
  "payload": {
    "memory_id": "uuid",
    "participant_id": "uuid",
    "gathering_id": "uuid",
    "gathering_title": "string",
    "gathering_category": "gathering_category",
    "venue_name": "string | null",
    "neighbourhood": "string",
    "attended_at": "timestamptz"
  }
}
```

**Side effects:**
- Feed personalisation engine (update category/vibe preference weights)
- If this is participant's 3rd check-in: fire `MEMORY.TasteProfile.initialised`
- If this is participant's 5th check-in: award `earn_taste_profile` (100 pts)

---

#### `MEMORY.TasteProfile.initialised`

Fired when enough attendance data exists to begin personalising the feed.

```json
{
  "event_type": "MEMORY.TasteProfile.initialised",
  "aggregate_type": "Participant",
  "aggregate_id": "<participant_id>",
  "payload": {
    "participant_id": "uuid",
    "check_in_count": 3,
    "top_categories": ["gathering_category"],
    "top_neighbourhoods": ["string"],
    "initialised_at": "timestamptz"
  }
}
```

**Side effects:**
- Enable personalised feed mode for this participant
- Update `participant_profiles.preferred_categories`

---

### Domain: PULSE_POINTS

---

#### `PULSE_POINTS.Transaction.recorded`

Fired for every Pulse Points earn or burn.

```json
{
  "event_type": "PULSE_POINTS.Transaction.recorded",
  "aggregate_type": "PulsePointsLedger",
  "aggregate_id": "<ledger_entry_id>",
  "payload": {
    "participant_id": "uuid",
    "transaction_type": "points_transaction_type",
    "amount": 0,
    "balance_after": 0,
    "related_gathering_id": "uuid | null",
    "related_ticket_id": "uuid | null",
    "recorded_at": "timestamptz"
  }
}
```

**Side effects:**
- Update participant's in-app points display (Realtime broadcast)
- If new balance crosses a milestone (500, 1000, 2500, 5000): fire `PULSE_POINTS.Milestone.reached`

---

#### `PULSE_POINTS.Milestone.reached`

```json
{
  "event_type": "PULSE_POINTS.Milestone.reached",
  "aggregate_type": "Participant",
  "aggregate_id": "<participant_id>",
  "payload": {
    "participant_id": "uuid",
    "milestone_points": 1000,
    "balance": 1050,
    "reached_at": "timestamptz"
  }
}
```

**Side effects:**
- Push notification: "You've reached [X] Pulse Points! Here's what you can do with them."

---

#### `PULSE_POINTS.DailyCapReached`

```json
{
  "event_type": "PULSE_POINTS.DailyCapReached",
  "aggregate_type": "Participant",
  "aggregate_id": "<participant_id>",
  "payload": {
    "participant_id": "uuid",
    "date": "2025-01-01",
    "total_earned_today": 500
  }
}
```

**Side effects:**
- Suppress further point awards for this participant until next calendar day (Africa/Kampala)

---

## Realtime Channels

| Channel | Events Broadcast | Consumers |
|---------|-----------------|-----------|
| `city:{city}:live_feed` | `VENUE.Venue.went_live`, `GATHERING.Gathering.went_live`, `VENUE.Venue.crowd_level_updated`, `VENUE.Venue.went_offline` | Flutter home feed, heat map |
| `gathering:{id}:live_dashboard` | `TICKETING.Ticket.checked_in`, `GATHERING.Gathering.capacity_threshold_crossed` | Organiser scanner dashboard |
| `participant:{id}:notifications` | `TICKETING.Ticket.gift_initiated`, `TICKETING.Ticket.gift_accepted`, `PULSE_POINTS.Milestone.reached`, `PAYMENT.Payout.succeeded` | Flutter notification inbox |
| `scanner:{session_id}:events` | `TICKETING.Ticket.checked_in` (this session) | Scanner display (gate operators) |

---

## Event Versioning Rules

1. **Fields are additive only.** Never remove or rename a field from a published event type. Add new optional fields instead.
2. **Breaking changes require a new event type.** If semantics change fundamentally, create `DOMAIN.Entity.action_past_tense_v2` and deprecate the original.
3. **All consumers must handle unknown fields gracefully** (use `jsonb`, ignore unknown keys).
4. **Event types are permanent.** A deprecated event type is never deleted — it is marked deprecated in this catalog and consumers are migrated.

---

## Event Processing SLAs

| Event | Processing Target | Channel |
|-------|------------------|---------|
| `VENUE.Venue.went_live` | < 10 seconds to push notification delivery | Push + Realtime |
| `TICKETING.Ticket.purchased` | < 10 seconds to ticket appearance in bucket | Realtime |
| `TICKETING.Ticket.checked_in` | < 400ms scanner response; < 2s dashboard update | Realtime |
| `GATHERING.Gathering.cancelled` | < 60 seconds to all ticket holder notifications | Push + SMS |
| `PAYMENT.Payout.succeeded` | < 5 minutes to notification after processor confirms | Push + email |
| All others | < 30 seconds | Async |

---

*Authority: Book II (Canonical Domain Model) · `ZUKA_Canonical_Schema_Pack.md`*  
*Next: Trust Engine Specification*
