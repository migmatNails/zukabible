# ZUKA Architecture Bible
# Volume II — Canonical Domain Model & Entity Graph

> **Series:** ZUKA Architecture Bible
> **Volume:** II of N
> **Status:** Canonical — foundational to all future volumes
> **Depends on:** Volume I — Vision, Philosophy & Human Gatherings
> **Required by:** Volume III (Identity), Volume IV (Memory), Volume V (Community), all engineering specifications

---

# Preface

Volume I established why ZUKA exists.

Volume II establishes what ZUKA is made of.

Every feature we will ever build — ticketing, scanning, audio streaming, memory vaults, community graphs, trust systems, AI recommendations, economic infrastructure — emerges from the primitives defined here.

Get the domain model wrong and every future system is built on sand.
Get it right and every future system has a natural home.

This document defines the canonical primitives. It will not change without a formal architecture review. Extensions are permitted. Contradictions are not.

---

# Part I — First Principles

---

## Chapter 1 — Four Universal Primitives

All of ZUKA — every feature, every system, every data structure — is built from exactly four primitives. Nothing exists outside these four.

```
┌─────────────────────────────────────────────────────────┐
│                  THE FOUR PRIMITIVES                     │
│                                                         │
│   ENTITY          RELATIONSHIP       EVENT       TIME   │
│                                                         │
│   A thing         A connection       A change    When   │
│   that exists     between things     occurred    it     │
│                                                 happened│
└─────────────────────────────────────────────────────────┘
```

### Primitive 1 — Entity
A thing that exists in the ZUKA universe. Has identity. Has state. Has history. Can participate in relationships. Can generate events.

### Primitive 2 — Relationship
A typed, weighted, directional or bidirectional connection between two entities. Has its own state and history. Is itself an entity (relationships can be related to other relationships).

### Primitive 3 — Event
An immutable record that something happened. Events are never deleted. Events are never updated. The history of an entity is the ordered log of events involving it. Events are the only mechanism by which state changes.

### Primitive 4 — Time
Every entity, relationship, and event exists in time. Time is not a timestamp attached to a record — it is a first-class dimension of the model. Entities have lifecycles. Relationships evolve. Events accumulate into histories that have meaning.

---

## Chapter 2 — Universal Entity Model

Every entity in ZUKA — regardless of type — shares a canonical base structure.

```typescript
Entity {
  // Identity
  id:           UUID             // globally unique, immutable, never recycled
  entity_type:  EntityType       // from the taxonomy in Chapter 3
  
  // State
  status:       EntityStatus     // active | suspended | archived | deleted
  
  // Flexible data
  metadata:     JSONB            // type-specific structured data
  tags:         Tag[]            // semantic tags from the universal tag system
  
  // Intelligence
  embedding:    Vector(1536)     // semantic embedding for similarity and search
  
  // Time
  created_at:   Timestamptz
  updated_at:   Timestamptz
  
  // Audit
  created_by:   UUID             // entity_id of creator (system or person)
  version:      Integer          // increments on every state change
}
```

### Design Decisions Encoded Here

**`id` is a UUID, never recycled.** An entity that is deleted does not have its ID reused. The ID is a permanent reference in the event log — reuse would corrupt history.

**`entity_type` is immutable after creation.** A Person cannot become a Brand. A Venue cannot become a Community. Type changes require creating a new entity and migrating relationships.

**`metadata` is JSONB with schema validation per type.** The base entity is type-agnostic. Type-specific structure lives in metadata, validated by the application layer against a registered schema per entity_type. This allows the entity model to evolve without schema migrations on the base table.

**`embedding` is a first-class field, not an afterthought.** Every entity is semantically embedded at creation. This enables similarity search, recommendation, clustering, and future AI capabilities across the entire entity graph without retrofitting.

**`version` enables optimistic concurrency.** Any update to an entity must present the current version. If the version has changed since the client read the entity, the update is rejected. This prevents lost updates across concurrent processes.

---

## Chapter 3 — Entity Taxonomy

The complete catalogue of entity types in ZUKA. This is exhaustive for version 1.0. New types require a formal taxonomy review before addition.

---

### 3.1 — Human Entities

The people and human units at the centre of every gathering.

```
PERSON
  The fundamental human entity. A real human being with identity,
  relationships, and a gathering history.
  
  Core fields (metadata):
    display_name, date_of_birth, gender, phone_number_hash,
    primary_city_id, profile_photo_url, language_preference,
    consent_profile (Tier A/B/C flags)
    
  Lifecycle:
    registered → verified → active → suspended → deleted
    
  Notes:
    A Person can have multiple Personas (see Volume III — Identity).
    The Person entity is the root of all relationship graphs.
    A Person's event log is their life history on the platform.

──────────────────────────────────────────────────────────────

COUPLE
  Two Person entities in a declared romantic partnership.
  Has its own identity on the platform — can attend events
  as a unit, receive couple-targeted recommendations, have
  a shared memory album.
  
  Core fields (metadata):
    person_a_id, person_b_id, partnership_type
    (married | engaged | dating | traditional_union),
    start_date, anniversary_date
    
  Notes:
    Created from a Relationship of type SPOUSE between two Persons.
    The Couple entity allows couple-level interactions without
    requiring both Persons to be active simultaneously.

──────────────────────────────────────────────────────────────

FAMILY
  A named family unit — parents, children, extended family —
  that gathers together as a group.
  
  Core fields (metadata):
    family_name, founding_date, primary_location_id, size_estimate
    
  Notes:
    Families can organise reunions, annual gatherings, and
    traditions through the platform. A Family entity persists
    across generations — it outlives individual members.
    This is the first entity type that embodies the
    "Legacy" layer from Volume I.

──────────────────────────────────────────────────────────────

CHILD
  A Person under the age of 18.
  Requires guardian linkage before any platform activity.
  
  Core fields (metadata):
    date_of_birth, guardian_ids[], school_id, age_verified_at
    
  Notes:
    Children cannot hold tickets independently until 16.
    Child entities are created by their guardian (Person entity).
    Child becomes Person on verified 18th birthday.
    All child-related data has enhanced privacy protections —
    excluded from all B2B data products absolutely.
```

---

### 3.2 — Community Entities

The groups and institutions that organise people around shared purpose, identity, or tradition.

```
CHURCH
  A religious congregation that gathers regularly.
  One of the most frequent gathering organisers in the
  African context — Sunday services, midweek meetings,
  crusades, choir performances, youth programmes.
  
  Core fields (metadata):
    denomination, founding_year, member_count_estimate,
    primary_venue_id, pastor_entity_id, weekly_schedule
    
  Notes:
    Churches can list recurring events (weekly services),
    special events (Easter, Christmas, crusades), and
    manage their congregation as a community on the platform.

──────────────────────────────────────────────────────────────

SCHOOL
  An educational institution — primary, secondary, or tertiary.
  Organises graduations, sports days, open days, alumni
  events, and student social events.
  
  Core fields (metadata):
    school_type (primary|secondary|university|vocational),
    founding_year, location_id, student_count_estimate,
    alumni_community_id

──────────────────────────────────────────────────────────────

ALUMNI
  A graduate community of a School entity.
  Persists independently of the School —
  alumni communities outlive the individual school
  relationships of their members.
  
  Core fields (metadata):
    school_id, graduation_year_range, member_count,
    active_since, annual_gathering_month

──────────────────────────────────────────────────────────────

CHOIR
  A musical performance ensemble — church choir,
  school choir, community choir, competition choir.
  Performs at events as a Creator entity and
  gathers as a Community entity.
  
  Core fields (metadata):
    voice_count, musical_style[], affiliation_entity_id,
    director_person_id, competition_history[]

──────────────────────────────────────────────────────────────

CLAN
  An extended kinship group or traditional family network.
  In the African context, clan identity is a fundamental
  component of personal identity — particularly relevant
  for cultural ceremonies and traditional gatherings.
  
  Core fields (metadata):
    clan_name, ethnic_group, geographic_origin,
    totem (where applicable), elder_entity_id

──────────────────────────────────────────────────────────────

NEIGHBORHOOD
  A named geographic community — a residential area
  whose residents share local identity and organise
  local gatherings (neighbourhood parties, community
  clean-ups, local celebrations).
  
  Core fields (metadata):
    city_id, district_id, boundary_polygon,
    population_estimate, community_leader_id
```

---

### 3.3 — Gathering Entities

The events and experiences that bring entities together.

```
EVENT
  A gathering with a defined time, place, and purpose.
  The most frequently created entity type on the platform.
  
  Core fields (metadata):
    title, category (from EventCategory enum),
    vibe_tags[], start_time, end_time,
    venue_id, organiser_id,
    status (draft|published|active|live|ended|cancelled),
    capacity, age_restriction,
    ticket_config_id, is_free,
    streaming_config, go_live_activated_at,
    audio_stream_active, crowd_level

──────────────────────────────────────────────────────────────

CEREMONY
  A structured, ritualised gathering marking a life
  transition or cultural observance. Higher emotional
  weight than a general Event. Creates memories that
  are qualitatively different in their long-term value.
  
  Examples:
    Wedding, introduction ceremony (kwanjula/introduction),
    graduation, funeral, naming ceremony, circumcision
    ceremony, baptism, traditional coronation.
    
  Core fields (metadata):
    ceremony_type, life_stage_transition,
    primary_persons_ids[], officiant_id,
    cultural_tradition, dress_code_requirements,
    photography_policy, invited_guests_count

──────────────────────────────────────────────────────────────

FESTIVAL
  A multi-day, multi-stage, multi-act gathering.
  Contains nested Event entities (each stage/day is an Event).
  Has its own identity, branding, and year-over-year tradition.
  
  Core fields (metadata):
    edition_number, festival_family_id,
    (links to previous years' festival entities),
    start_date, end_date, venue_ids[],
    stages[], total_capacity, headline_acts[]

──────────────────────────────────────────────────────────────

WATCH_PARTY
  A synchronised viewing gathering — sports screening,
  movie night, awards show viewing.
  Linked to an external content source or internal
  Sync Session (see Volume VII — Streaming Architecture).
  
  Core fields (metadata):
    content_type (sports|film|series|awards|other),
    content_reference, sync_session_id,
    screen_count, is_public

──────────────────────────────────────────────────────────────

TOUR
  A series of Events by the same Creator across
  multiple cities or venues.
  
  Core fields (metadata):
    artist_entity_id, tour_name,
    event_ids[], city_ids[],
    start_date, end_date, tour_status
```

---

### 3.4 — Place Entities

The physical and geographic contexts in which gatherings occur.

```
VENUE
  A physical location where Events are hosted.
  Has its own identity, reputation, and history of gatherings.
  
  Core fields (metadata):
    name, venue_type, address, location_pin (PostGIS Point),
    city_id, district_id, neighborhood_id,
    capacity, indoor_outdoor, facilities[],
    operating_hours, verification_status,
    audio_stream_capable, scanner_pro_enabled

──────────────────────────────────────────────────────────────

CITY
  A named urban area. The primary geographic context
  for discovery, heat maps, and B2B reporting.
  
  Core fields (metadata):
    name, country_code, timezone, currency_code,
    language_primary, language_secondary[],
    population_estimate, is_zuka_active,
    launch_date, city_editor_person_id

──────────────────────────────────────────────────────────────

DISTRICT
  A named administrative or colloquial sub-area within a City.
  (e.g., Kololo, Kabalagala, Ntinda within Kampala)
  
  Core fields (metadata):
    city_id, boundary_polygon (PostGIS),
    colloquial_name, entertainment_density_score,
    transport_coverage_score

──────────────────────────────────────────────────────────────

NEIGHBORHOOD
  [See Community Entities — NEIGHBORHOOD above]
  A Neighborhood is simultaneously a Place entity
  (geographic area) and a Community entity (social group).
  The same entity_id serves both contexts.
  This dual nature is intentional — place and community
  are inseparable in the African urban context.
```

---

### 3.5 — Creator Entities

The individuals and groups who produce the experiences at gatherings.

```
DJ
  A music performer who plays sets at Events.
  
  Core fields (metadata):
    stage_name, real_name_ref (Person entity),
    genres[], residency_venue_ids[],
    booking_rate_range, equipment_requirements,
    streaming_capable, social_profiles{}

──────────────────────────────────────────────────────────────

PHOTOGRAPHER
  A visual creator who documents gatherings.
  Produces Memory entities (Photo, Video) from Events.
  
  Core fields (metadata):
    person_id, specialisations[], portfolio_url,
    booking_rate_range, equipment_list,
    delivery_turnaround_days

──────────────────────────────────────────────────────────────

MC
  A live presenter/host who facilitates gatherings.
  
  Core fields (metadata):
    person_id, stage_name, languages[],
    event_types_specialised[], booking_rate_range,
    demo_reel_url

──────────────────────────────────────────────────────────────

ARTIST
  A musician, comedian, spoken word artist, dancer,
  or any performing artist who appears at Events.
  
  Core fields (metadata):
    stage_name, person_id, art_form[],
    genres[], label_affiliation,
    social_profiles{}, streaming_profiles{},
    booking_contact_id, touring_status,
    rider_requirements

──────────────────────────────────────────────────────────────

Notes on Creator Entities:
  All Creator entities are also Person entities.
  The Creator entity type represents their professional
  identity on the platform — separate from their
  personal identity (see Volume III — Identity).
  A Person can have a Creator persona without
  their personal identity being exposed.
```

---

### 3.6 — Organizational Entities

The institutional actors that sponsor, support, and produce gatherings at scale.

```
BRAND
  A commercial entity that sponsors or activates
  at gatherings. A buyer of B2B products and
  a payer of promoted placements.
  
  Core fields (metadata):
    brand_name, industry, parent_company,
    primary_contact_person_id,
    marketing_budget_tier (small|medium|large|enterprise),
    active_campaign_ids[]

──────────────────────────────────────────────────────────────

COMPANY
  A business entity that organises corporate events,
  team gatherings, product launches, and conferences.
  
  Core fields (metadata):
    company_name, registration_number,
    industry, employee_count_range,
    primary_contact_person_id,
    billing_entity_details{}

──────────────────────────────────────────────────────────────

NGO
  A non-governmental organisation that organises
  community gatherings, fundraisers, awareness events,
  and development programmes.
  
  Core fields (metadata):
    organisation_name, registration_number,
    cause_categories[], primary_contact_person_id,
    donor_visibility_preference
```

---

### 3.7 — Economic Entities

The financial and transactional primitives that power the platform economy.

```
TICKET
  A right of entry to a specific Event,
  held by a specific Person.
  
  Core fields (metadata):
    event_id, ticket_type_id, current_owner_id,
    original_buyer_id, status, ticket_class,
    seat_capacity, qr_token, valid_from,
    valid_until, checked_in_at,
    provenance_origin_type, provenance_ref_id
    
  Notes:
    Tickets are the most security-critical entity type.
    See Volume VI — Ticketing Architecture for full specification.
    Ticket records are never deleted — only archived.

──────────────────────────────────────────────────────────────

WALLET
  An in-platform balance held by a Person or
  Organisation entity.
  
  Core fields (metadata):
    owner_entity_id, currency_code,
    balance (computed from ledger — never stored directly),
    wallet_type (event_wallet | pulse_points | organiser_escrow),
    maximum_balance, is_active
    
  Notes:
    Balance is always derived from the Wallet Ledger —
    never stored as a field. Storing balance as a field
    creates race conditions and audit failures.
    The ledger is the balance.

──────────────────────────────────────────────────────────────

ORDER
  A purchase transaction — one or more Tickets
  bought in a single checkout session.
  
  Core fields (metadata):
    buyer_person_id, event_id,
    ticket_ids[], line_items[],
    gross_amount, commission_amount,
    convenience_fee_amount, net_amount,
    payment_method, payment_reference,
    payment_status, payout_status,
    currency_code

──────────────────────────────────────────────────────────────

SUBSCRIPTION
  A recurring commercial relationship between a
  Person/Organisation and the ZUKA platform.
  
  Core fields (metadata):
    subscriber_entity_id, plan_id,
    billing_cycle (monthly|annual),
    current_period_start, current_period_end,
    status (active|past_due|cancelled|paused),
    payment_method_id, auto_renew
```

---

### 3.8 — Memory Entities

The artifacts that preserve gathering experiences.
These are the entities that appreciate in value over time (Volume I, Chapter 5).

```
PHOTO
  A still image captured at or associated with a Gathering entity.
  
  Core fields (metadata):
    event_id, captured_by_person_id,
    captured_at_timestamp, storage_url,
    thumbnail_url, width, height,
    tagged_person_ids[], location_pin,
    is_vibe_drop (bool — distinguishes quick check-in drops
    from deliberate photographs),
    privacy_level (private|friends|public),
    embedding (visual embedding for similarity)

──────────────────────────────────────────────────────────────

VIDEO
  A video clip captured at or associated with a Gathering entity.
  Includes Vibe Drops (15-second check-in clips) and
  full-length event documentation.
  
  Core fields (metadata):
    event_id, captured_by_person_id,
    captured_at_timestamp, storage_url,
    thumbnail_url, duration_seconds,
    is_vibe_drop (bool),
    tagged_person_ids[], transcript_text,
    privacy_level, embedding

──────────────────────────────────────────────────────────────

ALBUM
  A curated collection of Photos and Videos
  associated with a Gathering, a Community,
  a Family, or a time period.
  
  Core fields (metadata):
    title, owner_entity_id,
    cover_photo_id, media_ids[],
    associated_event_ids[],
    associated_person_ids[],
    date_range{from, to},
    privacy_level

──────────────────────────────────────────────────────────────

STORY
  A structured narrative — a gathering recounted
  in the user's own words, optionally with media.
  The platform's long-form memory format.
  
  Core fields (metadata):
    author_person_id, associated_event_id,
    title, body_text,
    media_ids[], emotion_tags[],
    life_chapter (from Life Chapter taxonomy),
    privacy_level, word_count

──────────────────────────────────────────────────────────────

MEMORY
  The highest-order memory entity.
  A Memory is a curated, emotionally significant record
  of a gathering experience — richer than a check-in,
  more permanent than a Vibe Drop, more personal than
  an Album.
  
  A Memory can contain:
    - Photos, Videos, Albums
    - A Story
    - The Ticket (as a keepsake artifact)
    - Tagged Persons (who were there)
    - The Event entity reference
    - Emotion tags
    - A life chapter assignment
    
  Core fields (metadata):
    person_id, event_id,
    title, description,
    media_ids[], story_id, ticket_id,
    tagged_person_ids[],
    emotion_tags[], life_chapter,
    is_tradition_marker (bool),
    -- True if this is one of a recurring series
    tradition_id,
    privacy_level, is_archived
    
  Notes:
    Memories are the primary long-term retention mechanism.
    A user with 3 years of Memories on ZUKA has built
    something irreplaceable — a personal gathering archive
    that they cannot replicate anywhere else.
    Memories are never deleted. Only archived.
    Archived memories are retained indefinitely.
```

---

### 3.9 — Trust Entities

The reputation and verification layer that makes the platform safe.

```
REPUTATION
  An entity's computed trustworthiness score
  across multiple dimensions.
  Not edited directly — derived from Trust Events.
  
  Core fields (metadata):
    entity_id, overall_score (0.0–1.0),
    dimensions {
      delivery_score,      -- did events happen as listed?
      payment_score,       -- payment reliability
      attendance_score,    -- RSVP-to-attendance conversion
      community_score,     -- how others rate interactions
      longevity_score      -- time on platform, activity history
    },
    review_count,
    last_computed_at,
    trend (rising|stable|falling)

──────────────────────────────────────────────────────────────

VERIFICATION
  A formal verification record for an entity.
  Immutable — verifications are never edited, only superseded.
  
  Core fields (metadata):
    entity_id, verification_type,
    (identity|venue|business|age|professional),
    status (pending|approved|rejected|revoked),
    submitted_documents[], reviewer_id,
    submitted_at, reviewed_at,
    expiry_date (annual re-verification),
    revocation_reason

──────────────────────────────────────────────────────────────

BADGE
  A visible trust signal displayed on an entity's profile.
  Awarded by the platform or by other entities.
  
  Core fields (metadata):
    entity_id, badge_type,
    (verified_venue | top_organiser | trusted_creator |
     community_champion | founding_member | tradition_keeper),
    awarded_by_entity_id, awarded_at,
    visible (bool), expiry_date
```

---

### 3.10 — Opportunity Entities

The economic connections that emerge from the gathering ecosystem.

```
JOB
  A work opportunity connected to the gathering economy.
  
  Examples:
    DJ gig, photography booking, MC hire,
    security staff, catering contract,
    event coordinator role.
  
  Core fields (metadata):
    poster_entity_id, job_type,
    required_creator_type, event_id,
    compensation_type (fixed|hourly|revenue_share),
    compensation_amount, currency_code,
    status (open|filled|cancelled),
    application_deadline

──────────────────────────────────────────────────────────────

SPONSORSHIP
  A brand's commercial relationship with an Event,
  Festival, Community, or Creator.
  
  Core fields (metadata):
    sponsor_entity_id, sponsored_entity_id,
    sponsorship_type (title|presenting|supporting|media),
    value_amount, currency_code,
    deliverables[], start_date, end_date,
    status (proposed|active|completed|cancelled)

──────────────────────────────────────────────────────────────

BOOKING
  A confirmed engagement of a Creator entity
  for an Event.
  
  Core fields (metadata):
    creator_entity_id, event_id,
    booker_entity_id, role,
    performance_time, duration_minutes,
    fee_amount, currency_code,
    payment_status, contract_reference,
    rider_fulfilled (bool)

──────────────────────────────────────────────────────────────

COLLABORATION
  A creative or commercial partnership between
  two or more entities.
  
  Core fields (metadata):
    initiator_entity_id, partner_entity_ids[],
    collaboration_type, description,
    start_date, end_date, output_entity_ids[],
    status (proposed|active|completed)
```

---

# Part II — Relationship Architecture

---

## Chapter 4 — Relationship Model

A Relationship is not metadata on an entity. It is its own first-class entity — with its own ID, its own state, its own history, and its own embedding.

```typescript
Relationship {
  // Identity (Relationship IS an entity)
  id:                UUID
  entity_type:       'relationship'  // always
  
  // The connection
  source_entity_id:  UUID
  source_type:       EntityType
  target_entity_id:  UUID
  target_type:       EntityType
  relationship_type: RelationshipType
  
  // Direction
  is_directional:    Boolean
  // false = bidirectional (Friend, Sibling)
  // true = directional (Follower→Followed, Fan→Artist)
  
  // Weight & trust
  weight:            Float(0.0–1.0)
  // Strength of the connection.
  // Decays over time without reinforcing events.
  // Increases with shared gatherings and interactions.
  
  trust_score:       Float(0.0–1.0)
  // Separate from weight.
  // Trust is earned through consistent behaviour.
  // Trust decays slowly. Trust loss is fast.
  
  // Temporal validity
  valid_from:        Timestamptz
  valid_to:          Timestamptz   // null = ongoing
  
  // State
  status:            RelationshipStatus
  // active | paused | ended | disputed
  
  // Intelligence
  embedding:         Vector(1536)
  metadata:          JSONB
  
  // Audit
  created_at:        Timestamptz
  updated_at:        Timestamptz
}
```

---

## Chapter 5 — Relationship Type Registry

### Personal Relationships
```
FRIEND
  Bidirectional. Between two Person entities.
  Weight increases with: shared events attended,
  direct interactions, mutual connections.
  
SIBLING
  Bidirectional. Between two Person entities.
  Declared by both parties. High base trust score.
  
PARENT_CHILD
  Directional (Parent → Child).
  Guardian authority relationship.
  Required before any Child entity activity.
  
SPOUSE
  Bidirectional. Between two Person entities.
  Creates or links to a Couple entity.
  Highest base trust score of any relationship type.
  
EXTENDED_FAMILY
  Bidirectional. Between Person entities.
  Clan/kinship relationship. Often community-mediated.
```

### Community Relationships
```
COMMUNITY_MEMBER
  Directional (Person → Community).
  Foundational community relationship.
  
COMMUNITY_LEADER
  Directional (Person → Community).
  Leadership authority over a Community entity.
  
COMMUNITY_FOUNDER
  Directional (Person → Community).
  Historical — persists even if person is no longer active.
  Founders are always commemorated.
```

### Professional / Creative Relationships
```
ORGANISER
  Directional (Person/Org → Event/Festival).
  Operational authority over a Gathering entity.
  
ATTENDEE
  Directional (Person → Event).
  Created by check-in. Verified by GPS proximity.
  
VENDOR
  Directional (Person/Brand → Event).
  Commercial service provider at a Gathering.
  
CREATOR
  Directional (DJ/Artist/Photographer/MC → Event).
  Performance or creative service relationship.
  
SPONSOR
  Directional (Brand/Company → Event/Community/Creator).
  Commercial sponsorship relationship.
  
MENTOR
  Directional (Person → Person).
  Knowledge and guidance relationship.
  Weight increases with interaction frequency.
  
STUDENT
  Directional (Person → Person/School).
  Learning relationship. Time-bounded by academic period.
  
BOOKING
  Directional (Event/Organiser → Creator).
  Operational engagement of a Creator.
  Links to Booking economic entity.
```

### Discovery Relationships
```
FAN
  Directional (Person → Artist/Creator).
  Emotional affinity relationship.
  Created by: concert attendance, music identification,
  explicit follow, high engagement with content.
  
FOLLOWER
  Directional (Person → Venue/Community/Person).
  Interest relationship. Lower commitment than Fan.
  Created by: explicit follow action.
  
BUSINESS_PARTNER
  Bidirectional. Between Organisational entities.
  Commercial partnership — B2B relationship type.
```

---

## Chapter 6 — Relationship Dynamics

### Weight Decay
Relationships have weight that decays over time without reinforcing events. This models reality — friendships that are never maintained fade.

```
weight_today = initial_weight × decay_factor^(days_since_last_interaction)

decay_factor:
  FRIEND:     0.9995 (very slow decay — friendships persist)
  FOLLOWER:   0.999  (moderate decay)
  ATTENDEE:   0.998  (faster decay — event attendance is episodic)
  FAN:        0.9998 (very slow decay — fan identity persists)
```

Weight is reinforced by events:
```
shared_event_attended:    +0.05 to weight
direct_interaction:       +0.03 to weight  
gift_ticket_sent:         +0.08 to weight
mutual_check_in:          +0.04 to weight
relationship_confirmed:   +0.10 to weight (both parties confirm)
```

### Trust Score
Trust is separate from weight. Trust is earned by consistent, reliable behaviour over time.

```
Trust increases with:
  Promises kept (RSVP → attendance)
  Payments made on time
  Events delivered as listed
  Positive peer assessments
  Long relationship duration

Trust decreases with:
  Broken commitments (RSVP → no-show, repeatedly)
  Payment failures
  Event cancellations (organiser)
  Disputes filed and upheld
  Reported misconduct

Trust recovery: slow (weeks to months)
Trust loss: fast (single significant incident)
```

### Relationship Lifecycle
```
PROPOSED → ACTIVE → [PAUSED] → ENDED
                           ↓
                       DISPUTED → RESOLVED
```

Disputes on relationships (e.g., a contested sponsorship, a reported relationship) enter a DISPUTED state. The relationship is frozen — no weight changes, no trust score changes — until resolution.

---

# Part III — Event Model

---

## Chapter 7 — Everything Is an Event

In ZUKA's architecture, an "Event" has two meanings that must be carefully distinguished:

**Gathering Event:** An entity of type `EVENT` — a party, concert, ceremony, festival. What users attend.

**Domain Event:** An immutable record that something happened in the system. How state changes are tracked.

This chapter is about Domain Events — the second meaning. They are the ledger of everything that has ever happened in ZUKA.

---

## Chapter 8 — Domain Event Schema

```typescript
DomainEvent {
  // Identity
  id:              UUID           // immutable
  event_type:      DomainEventType
  
  // Who and what
  actor_entity_id: UUID           // who caused this (null = system)
  subject_entity_id: UUID         // what entity this is about
  object_entity_id: UUID          // optional — what entity was acted upon
  
  // The data
  payload:         JSONB          // event-specific structured data
  previous_state:  JSONB          // snapshot of entity before change
  new_state:       JSONB          // snapshot of entity after change
  
  // Context
  city_id:         UUID
  session_id:      UUID           // platform session that caused this
  ip_address_hash: TEXT           // for audit — never raw
  device_type:     TEXT
  
  // Time (immutable)
  occurred_at:     Timestamptz    // device timestamp
  recorded_at:     Timestamptz    // server timestamp
  
  // Integrity
  sequence_number: BigInt         // monotonically increasing per entity
  checksum:        TEXT           // HMAC of payload — tamper detection
}

-- THIS TABLE IS APPEND-ONLY.
-- NO UPDATES. NO DELETES. EVER.
-- Rows may be archived to cold storage after 7 years.
-- They are never modified in transit.
```

---

## Chapter 9 — Domain Event Type Registry

```
── PERSON EVENTS ──
PersonCreated
PersonVerified
PersonProfileUpdated
PersonSuspended
PersonReactivated
PersonDeletedRequested
PersonDeleted
PersonConsentUpdated
PersonLanguageChanged
PersonCityChanged

── RELATIONSHIP EVENTS ──
RelationshipProposed
RelationshipAccepted
RelationshipDeclined
RelationshipWeightUpdated
RelationshipTrustScoreUpdated
RelationshipPaused
RelationshipEnded
RelationshipDisputed
RelationshipDisputeResolved

── GATHERING EVENTS ──
GatheringCreated
GatheringPublished
GatheringGoLiveActivated
GatheringGoLiveDeactivated
GatheringUpdated
GatheringCrowdLevelUpdated
GatheringCapacityReached
GatheringCheckInOpened
GatheringCheckInClosed
GatheringEnded
GatheringCancelled

── TICKETING EVENTS ──
TicketPurchased
TicketGiftInitiated
TicketGiftDelivered
TicketGiftAccepted
TicketGiftRejected
TicketGiftExpired
TicketTransferred
TicketCheckedIn
TicketVoided
TicketRefunded
TicketExpired

── MEMORY EVENTS ──
PhotoUploaded
VideoUploaded
VibeDropPosted
AlbumCreated
StoryWritten
MemoryCreated
MemoryTagged
TraditionMarked
LifeChapterAssigned

── COMMUNITY EVENTS ──
CommunityCreated
CommunityMemberAdded
CommunityMemberRemoved
CommunityLeaderAssigned
CommunityGatheringCreated
CommunityTraditionEstablished

── ECONOMIC EVENTS ──
OrderCreated
PaymentInitiated
PaymentSucceeded
PaymentFailed
PaymentRefunded
PayoutInitiated
PayoutSucceeded
PayoutFailed
WalletCredited
WalletDebited
PulsePointsEarned
PulsePointsRedeemed
SubscriptionCreated
SubscriptionRenewed
SubscriptionCancelled

── TRUST EVENTS ──
VerificationSubmitted
VerificationApproved
VerificationRejected
VerificationRevoked
BadgeAwarded
BadgeRevoked
ReputationScoreUpdated
ReviewSubmitted
ReviewResponded
DisputeFiled
DisputeResolved

── SCANNER EVENTS ──
ScannerSessionCreated
ScannerSessionEnded
ScanAttempted
ScanAdmitted
ScanDenied
ScanConflictDetected
ScanOfflineSynced
StaffRoleAssigned
StaffRoleRevoked

── OPPORTUNITY EVENTS ──
JobPosted
JobApplicationSubmitted
JobFilled
JobCancelled
SponsorshipProposed
SponsorshipAccepted
SponsorshipCompleted
BookingCreated
BookingConfirmed
BookingCancelled
CollaborationInitiated
CollaborationCompleted

── PLATFORM EVENTS ──
CityLaunched
VenueVerified
ContentFlagged
ContentModerated
FraudDetected
AnomalyDetected
B2BReportGenerated
DataExportRequested
DataExportDelivered
```

---

# Part IV — Temporal Layer

---

## Chapter 10 — Time as a First-Class Dimension

Time in ZUKA is not a field. It is a dimension.

Every entity exists through time. Every relationship evolves through time. Every event occurs at a point in time. The accumulation of events over time creates history. History creates meaning.

### The Four Temporal Concepts

**Existence Time:** When an entity exists. Represented by `created_at` and, if applicable, `archived_at`. An entity that is archived continues to exist in the historical record — it is simply no longer active.

**Validity Time:** When a relationship or state is valid. A Couple relationship has `valid_from` (wedding date) and `valid_to` (if applicable). An organiser assignment has `valid_from` (when they were assigned) and `valid_to` (when they stepped down).

**Transaction Time:** When the system recorded something. `recorded_at` on DomainEvents. This is always the server timestamp — never editable. Distinguished from `occurred_at` (device timestamp) to handle clock skew and offline sync.

**Life Chapter Time:** A higher-order temporal concept unique to ZUKA. Events and Memories are assignable to Life Chapters — named periods in a Person's life. This creates a biographical temporal layer that standard timestamp systems cannot express.

### Life Chapter Taxonomy
```
PRE_SCHOOL         (0–5 years)
CHILDHOOD          (6–12 years)
SECONDARY_SCHOOL   (13–18 years)
UNIVERSITY         (18–24 years, approximate)
EARLY_CAREER       (first employment period)
CAREER             (established professional life)
PARTNERSHIP        (when a Person forms a Couple entity)
PARENTHOOD         (when a Child entity is linked)
SENIOR             (retirement and beyond)
LEGACY             (posthumous — for Family/Community entities
                    that outlive individual members)

Plus: CUSTOM        (user-defined chapter name and date range)
```

Life Chapter assignment is:
- Automatic where inferable from other events (e.g., a GraduationCeremony event triggers UNIVERSITY → EARLY_CAREER transition suggestion)
- Manual where personal context is needed
- Always user-controlled — never imposed by the system

---

## Chapter 11 — Tradition Model

A Tradition is a recurring gathering pattern that has been repeated enough times to be recognised as intentional.

```typescript
Tradition {
  id:                UUID
  entity_type:       'tradition'
  
  name:              TEXT         // "Annual Nakato Family Reunion"
  description:       TEXT
  
  owner_entity_id:   UUID         // Person, Family, or Community
  
  // Pattern
  recurrence_type:   RecurrenceType
  // annual | monthly | weekly | irregular
  
  recurrence_config: JSONB
  // For annual: { month: 12, approximate_date: "last Saturday" }
  
  // Evidence
  event_ids:         UUID[]       // the Gathering Events that constitute this tradition
  first_occurrence:  Timestamptz
  occurrence_count:  Integer      // how many times this tradition has occurred
  
  // Status
  is_active:         Boolean
  last_occurrence:   Timestamptz
  next_expected:     Timestamptz  // computed
  
  // Recognition
  tradition_strength: Float(0.0–1.0)
  // 0.0 = just started (2 occurrences)
  // 0.5 = established (5+ occurrences)
  // 1.0 = deep tradition (10+ occurrences, consistent pattern)
  
  tags:              Tag[]
  embedding:         Vector(1536)
  created_at:        Timestamptz
}
```

### Tradition Detection Algorithm
```
On every GatheringCreated or GatheringEnded event:

1. Check: has the same organiser/community created a gathering
   with similar semantic embedding (cosine similarity > 0.85)
   in the same approximate time window in a previous year?

2. If yes (2 occurrences): suggest Tradition creation to the organiser.
   "It looks like this might be an annual tradition. Mark it as one?"

3. If yes (3+ occurrences): auto-create Tradition entity,
   link all matching Gathering entities, notify the organiser.
   "We've recognised your 3rd annual gathering as a Zuka Tradition."

4. On each subsequent occurrence: increment occurrence_count,
   update tradition_strength, generate TraditionMarked domain event.
```

---

# Part V — Participation Model

---

## Chapter 12 — Participation as Relationship

Every interaction between an entity and a Gathering is a specific Relationship type with specific semantics.

```
PERSON ↔ EVENT
  As ATTENDEE:     Person checked in at Event.
                   Verified by GPS proximity + Smart Bucket scan.
                   Creates an ATTENDEE relationship.
                   Creates an AttendeeCheckedIn domain event.
                   Awards Pulse Points.
                   
  As ORGANISER:    Person created and manages the Event.
                   Full operational authority.
                   
  As CREATOR:      Person (as DJ/Artist/MC) performed at Event.
                   Creates a CREATOR relationship + Booking entity.

──────────────────────────────────────────────────────────────

CREATOR ↔ EVENT
  As CREATOR:      Performance engagement.
                   Linked via Booking entity.
                   
  As ORGANISER:    Creator is also the organiser
                   (self-produced event). Dual relationship.

──────────────────────────────────────────────────────────────

PERSON ↔ COMMUNITY
  As MEMBER:       Basic community membership.
  As LEADER:       Operational authority.
  As FOUNDER:      Historical — permanent record.

──────────────────────────────────────────────────────────────

BRAND ↔ FESTIVAL
  As SPONSOR:      Commercial relationship.
                   Linked via Sponsorship economic entity.
  As PARTNER:      Non-financial partnership (media, logistics).

──────────────────────────────────────────────────────────────

CHURCH ↔ FAMILY
  As HOST:         Church hosts Family events (weddings, funerals).
  As COMMUNITY:    Family is a member community of the Church.
                   
  Notes:
    The Church-Family relationship is one of the most
    important in the African context. A church is not
    just a venue — it is a community that holds Family
    entities over generations.

──────────────────────────────────────────────────────────────

SCHOOL ↔ ALUMNI
  As ORIGIN:       School is the origin institution of the Alumni community.
  As HOST:         School hosts Alumni gatherings.
  As PARTNER:      Alumni community partners with School for events.
```

---

# Part VI — Graph Architecture

---

## Chapter 13 — The Six Graphs

ZUKA maintains six distinct graph layers over the same entity set. Each graph answers different questions and powers different features.

---

### Graph 1 — Entity Graph
**What it stores:** Connections between entities.
**What it answers:** "Who is connected to whom? What is connected to what?"
**Powers:** Social features, community discovery, event recommendations through social graph.

```
Technology: PostgreSQL with recursive CTEs for traversal.
At scale: Neo4j or Amazon Neptune for deep graph queries.

Primary queries:
  - Friends of friends (2-hop traversal)
  - Community membership chains
  - Event-venue-city hierarchies
  - Creator-booking-event connections
```

---

### Graph 2 — Knowledge Graph
**What it stores:** Semantic meaning and categorical relationships between entities.
**What it answers:** "What does this entity mean? What is it related to conceptually?"
**Powers:** Search, discovery, tagging, category recommendations, trend detection.

```
Structure:
  Entity → [IS_A] → EntityType
  Event → [HAS_TAG] → Tag
  Tag → [RELATED_TO] → Tag (semantic tag relationships)
  Venue → [LOCATED_IN] → Neighbourhood → [PART_OF] → District → [PART_OF] → City

Primary queries:
  - "Find all Jazz events in Kololo" (tag + location traversal)
  - "What is semantically similar to Afrobeats?" (tag graph traversal)
  - "Events that share vibe tags with events I've attended" (cross-entity tag similarity)
```

---

### Graph 3 — Temporal Graph
**What it stores:** How entities and relationships evolve over time.
**What it answers:** "How has this changed? What was true when? What patterns exist over time?"
**Powers:** Memory features, tradition detection, life chapter assignment, B2B trend reports.

```
Structure:
  Entity → [STATE_AT(t)] → EntityState
  Relationship → [WEIGHT_AT(t)] → Float
  Tradition → [OCCURRED_AT(t)] → Event

Primary queries:
  - "What was this user's event history in 2023?" (temporal slice)
  - "Show the weight of this friendship over time" (weight evolution)
  - "Has this event happened annually for 3+ years?" (tradition detection)
  - "What events is this neighbourhood trending toward?" (temporal trend)
```

---

### Graph 4 — Trust Graph
**What it stores:** Reputation, verification status, and trust relationships between entities.
**What it answers:** "How trustworthy is this entity? Who trusts whom? What is the verification chain?"
**Powers:** Verified badge system, ranking algorithm quality_score, fraud detection, safe community features.

```
Structure:
  Entity → [HAS_REPUTATION] → Reputation
  Entity → [VERIFIED_BY] → Verification
  Person → [TRUSTS(score)] → Person
  Review → [ABOUT] → Entity
  Review → [BY] → Person → [ATTENDED] → Event (verification chain)

Primary queries:
  - "What is this venue's trust score?" (direct lookup)
  - "Is this review verified as genuine attendance?" (chain traversal)
  - "What is the community trust score in this neighbourhood?" (aggregate)
  - "Flag entities with anomalously fast trust accumulation" (fraud signal)
```

---

### Graph 5 — Opportunity Graph
**What it stores:** Economic interactions and potential connections in the gathering economy.
**What it answers:** "Who needs what? Who can provide what? What economic connections exist?"
**Powers:** Job marketplace, creator discovery, sponsorship matching, B2B connections.

```
Structure:
  Job → [REQUIRES] → CreatorType
  Creator → [CAPABLE_OF] → CreatorType
  Brand → [INTERESTED_IN] → EventCategory
  Festival → [NEEDS_SPONSOR] → SponsorshipType

Primary queries:
  - "Find DJs available for this event date in Kampala" (capability match)
  - "Match this brand's interests to upcoming festivals" (sponsorship match)
  - "What opportunities exist for this photographer?" (creator opportunity discovery)
```

---

### Graph 6 — Memory Graph
**What it stores:** Emotional and experiential connections between entities, persons, and memories.
**What it answers:** "What was experienced together? What memories connect these people? What is the gathering history of this community?"
**Powers:** Memory Vault features, Life Chapter construction, Tradition commemoration, Legacy features.

```
Structure:
  Memory → [INVOLVES] → Person
  Memory → [AT] → Event
  Memory → [CONTAINS] → Photo/Video/Story
  Memory → [PART_OF] → Tradition
  Memory → [IN_CHAPTER] → LifeChapter
  Person → [SHARES_MEMORY_WITH] → Person (derived — persons in same Memory)

Primary queries:
  - "Show all memories involving Amara and David" (intersection query)
  - "What memories does this family have from Christmas gatherings?" (tradition + time + family)
  - "Build this person's gathering timeline for 2022" (temporal memory traversal)
  - "Who else was present at this event?" (memory-to-person expansion)
```

---

# Part VII — Universal Tagging System

---

## Chapter 14 — Semantic Tag Architecture

Tags are the knowledge graph's primary labelling mechanism. They are not free-text strings — they are structured semantic objects with defined hierarchies and relationships.

```typescript
Tag {
  id:           UUID
  namespace:    TagNamespace      // the domain this tag belongs to
  key:          TEXT              // the tag key (e.g., "afrobeats")
  display_name: TEXT              // human-readable (e.g., "Afrobeats")
  parent_tag_id: UUID             // for hierarchical tags
  related_tag_ids: UUID[]         // semantic relationships to other tags
  embedding:    Vector(1536)      // for semantic tag similarity
  usage_count:  Integer           // how many entities carry this tag
  created_at:   Timestamptz
}
```

---

## Chapter 15 — Tag Namespace Registry

```
namespace: music
  afrobeats, amapiano, afro_soul, afropop,
  hiphop, trap, rnb, gospel, traditional,
  jazz, blues, reggae, dancehall, edm,
  house, techno, live_band, acoustic,
  classical, drill, gqom, bongo_flava,
  benga, kwaito, highlife

namespace: emotion
  joy, celebration, nostalgia, excitement,
  peace, energy, romance, spiritual,
  grief, community, pride, gratitude

namespace: crowd
  family, friends, couples, singles,
  professionals, students, youth,
  seniors, all_ages, lgbtq_friendly,
  international, local

namespace: season
  christmas, easter, eid, diwali,
  new_year, independence_day,
  valentines, halloween, ramadan,
  end_of_year, school_holidays

namespace: culture
  ugandan, kenyan, tanzanian, rwandan,
  nigerian, ghanaian, south_african,
  east_african, west_african, pan_african,
  diaspora, international

namespace: energy
  high, medium, low, electric,
  intimate, massive, underground, mainstream

namespace: format
  live_music, dj_set, comedy_show,
  sports_screening, movie_night,
  art_exhibition, conference, ceremony,
  festival, market, rooftop, outdoor,
  indoor, pool_party, brunch

namespace: price
  free, budget, mid_range, premium, vip

namespace: time
  daytime, afternoon, evening, late_night,
  all_day, weekend, weekday, recurring

namespace: dress_code
  casual, smart_casual, formal,
  black_tie, themed, cultural_attire,
  no_restriction

namespace: accessibility
  wheelchair_accessible, deaf_friendly,
  sign_language, family_changing_room,
  quiet_room, gender_neutral_facilities
```

---

# Part VIII — Embedding Layer

---

## Chapter 16 — Semantic Embeddings

Every entity in ZUKA has a semantic embedding — a 1,536-dimensional vector representation of its meaning. This is not a future feature. It is a foundational architectural requirement from day one.

### Why Embeddings Are Foundational

Without embeddings, ZUKA's discovery capabilities are limited to:
- Exact keyword match
- Category filter
- Geographic proximity
- Social graph connections

With embeddings, ZUKA can:
- Find semantically similar events ("events like this one")
- Recommend venues based on experiential similarity (not just category match)
- Cluster users by gathering behaviour (not just demographics)
- Detect tradition patterns without explicit recurrence rules
- Match creators to events based on style fit, not just category
- Power future AI features (personal gathering assistant, memory synthesis) without retrofitting

### Embedding Generation

```
Person embedding:
  Synthesised from:
    - Attended event embeddings (weighted by recency and engagement)
    - Followed venue/community embeddings
    - Memory entity embeddings
    - Self-described tags and preferences
  Represents: "What kind of gathering person are they?"

Event embedding:
  Synthesised from:
    - Title + description (text embedding)
    - Category and vibe tags
    - Historical attendee embeddings
    - Venue embedding
    - Creator embeddings
  Represents: "What kind of experience is this event?"

Venue embedding:
  Synthesised from:
    - Venue description and tags
    - Historical event embeddings
    - Attendee demographic embeddings
    - Neighbourhood embedding
  Represents: "What kind of place is this venue?"

Memory embedding:
  Synthesised from:
    - Event embedding at time of memory
    - Emotion tags
    - Photo/video visual embeddings
    - Story text embedding
  Represents: "What did this experience mean?"

Community embedding:
  Synthesised from:
    - Member person embeddings
    - Community description and tags
    - Historical gathering embeddings
  Represents: "What kind of community is this?"
```

### Embedding Update Policy
- Recomputed on every significant entity update (not on every field change)
- Full recomputation: scheduled weekly for all active entities
- Incremental update: triggered by GatheringAttended, MemoryCreated, ProfileUpdated events
- Model versioning: embedding model version tracked per entity — enables migration when model is upgraded

---

# Part IX — Capital Model

---

## Chapter 17 — Forms of Capital

Volume I established that relationships are assets and memories appreciate. Volume II formalises this into a **Capital Model** — a framework for understanding what accumulates in ZUKA's economy beyond money.

Capital in ZUKA is not metaphorical. It is computed, tracked, and used as the basis for features, recommendations, and eventually economic opportunities.

---

### Memory Capital
**Definition:** The depth and richness of a person's or community's gathering history on the platform.

**Composed of:**
- Number of Memories created
- Quality of those Memories (completeness, media richness, people tagged)
- Temporal span (a 5-year gathering history > a 5-month one)
- Tradition depth (traditions count more than single events)
- Rarity (attending significant/rare events contributes more)

**Appreciates because:** A Memory from 5 years ago is more valuable today than when it was created. The longer the archive exists, the more irreplaceable it becomes.

**Powers:** Memory Vault features, Life Chapter construction, Legacy features, user retention.

---

### Relationship Capital
**Definition:** The strength and breadth of a person's connection graph on the platform.

**Composed of:**
- Number of active Relationships (by type, weighted)
- Average relationship weight across the graph
- Graph depth (connections to connections)
- Relationship quality (trust scores, shared gathering history)
- Cross-community bridges (relationships that span communities)

**Appreciates because:** Relationships built through shared gatherings compound — a friendship of 3 years of shared events is stronger than a declared friendship with no shared history.

**Powers:** Social features, Group Planning, co-attendance notifications, friend-based recommendations.

---

### Trust Capital
**Definition:** The earned reputation of an entity across all interactions on the platform.

**Composed of:**
- Reputation scores across all dimensions
- Verification tier (unverified / standard / pro)
- Badges held
- Review scores
- History of commitment vs. delivery (RSVP kept, events delivered, payments made)

**Appreciates slowly, depreciates fast.** A single significant breach of trust depletes years of accumulated trust capital. This asymmetry is intentional — it mirrors how trust works in human relationships.

**Powers:** Search ranking, organiser payout speed, scanner access levels, B2B buyer trust.

---

### Community Capital
**Definition:** The strength and cohesion of a community entity and its network of gatherings.

**Composed of:**
- Active member count and growth rate
- Gathering frequency (how often the community gathers)
- Tradition depth (how many established traditions exist)
- Member relationship density (are community members also friends?)
- Inter-community bridges (connections to other communities)

**Appreciates because:** Communities that gather consistently grow stronger. The traditions they establish become cultural infrastructure.

**Powers:** Community features, tradition recognition, community-targeted events, B2B community intelligence.

---

### Experience Capital
**Definition:** The breadth and depth of a person's gathering experience portfolio.

**Composed of:**
- Total gatherings attended
- Category diversity (variety of event types attended)
- Geographic range (attended events across multiple cities/countries)
- Life chapter coverage (gathered across multiple life stages)
- Rare/significant events attended (flagship festivals, once-in-a-generation gatherings)

**Powers:** Pulse Points, recommendation diversity, opportunity matching (high experience capital → surfaces more opportunities).

---

### Opportunity Capital
**Definition:** The economic potential available to an entity based on their position in the gathering ecosystem.

**Composed of:**
- Creator booking history and ratings
- Venue's event production track record
- Organiser's delivery reputation
- Community's sponsorship attractiveness
- Person's influence within their social graph

**Powers:** Opportunity Graph features, creator marketplace, sponsorship matching, B2B commercial relationships.

---

### Legacy Capital
**Definition:** The enduring impact of an entity's gathering history — the part that outlives the entity itself.

**Composed of:**
- Traditions established that are still active
- Communities founded that are still gathering
- Memories that others have of gatherings they hosted or organised
- Family and community entities that they are foundational to

**Appreciates after life.** A Family entity contains the Legacy Capital of deceased members — their traditions, their founding role, their Memory contributions. This is the 100-year feature from Volume I, Chapter 13.

**Powers:** Legacy features (long-term), Family and Community memory archives, Tradition commemoration.

---

# Part X — Gaps and Open Questions

---

## Chapter 18 — Identity Boundaries

**Question:** When does a Person entity end and another begin?

In the African context, personhood is sometimes more communal than individual. A person operates simultaneously as:
- An individual (their personal gatherings)
- A family member (their family's gatherings)
- A community member (their church's, school's, neighbourhood's gatherings)
- A professional (their creator or organiser identity)

The Entity Taxonomy handles this through Relationship types and multi-entity participation. But the question of **which identity is primary at any given moment** is unresolved.

→ Deferred to Volume III — Identity Architecture

---

## Chapter 19 — Multi-Persona Support

**Question:** Should a Person entity support multiple personas — a personal identity and a professional identity — that are linked but distinct?

Use case: A DJ (Creator entity) who is also a regular event-goer (Person entity). Their DJ bookings, professional profile, and earnings are separate from their personal gathering history and friend graph.

Current position: Creator entities are separate entity types linked to a Person entity via relationship. Whether the app presents them as distinct accounts or as one account with persona switching is an open UX and architecture question.

→ Deferred to Volume III — Identity Architecture

---

## Chapter 20 — Relationship Decay Calibration

**Question:** What are the correct decay rates for each relationship type?

The decay model is defined structurally in Chapter 6. The specific decay factors (0.9995, 0.999, etc.) are initial estimates. They need:
- Empirical calibration against real user behaviour data (post-launch)
- Separate calibration for different cultural contexts (decay rates may differ between markets)
- A governance process for adjusting decay rates as the model matures

→ Open — requires post-launch data

---

## Chapter 21 — Community Inheritance

**Question:** What happens to a Community entity when its founding members are no longer active?

Use case: A church community where the founding pastor has died. A school alumni community where the original organisers have graduated and moved on. A family entity where the matriarch has passed.

The platform must support **community inheritance** — the transfer of community leadership, memory stewardship, and tradition maintenance to a new generation.

Proposed model:
- Community entities have a `succession_policy` field
- Succession can be: automatic (most senior active member), elected (community votes), or designated (founder assigns)
- In the absence of active leadership, a Community enters `dormant` status — preserved but not actively curated

→ Partially specified. Full specification deferred to Volume V — Community Architecture

---

## Chapter 22 — Cross-Community Reputation

**Question:** Does a person's Trust Capital in one community transfer to another?

Use case: A highly trusted event organiser in the Kampala church community who starts organising secular music events. Do they carry their trust score?

Current position: Reputation has a global score and community-specific dimensions. Transfer is partial — global trust score carries over, community-specific trust must be rebuilt.

→ Requires further specification in Volume VIII — Trust Architecture

---

## Chapter 23 — Portable Trust

**Question:** Can a person's Trust Capital on ZUKA be presented as a credential outside the platform?

Use case: A DJ with a high booking reputation on ZUKA wants to use that reputation when approaching a venue that isn't on the platform.

Proposed mechanism: A verifiable digital credential (QR-linked trust certificate) that presents a subset of the Trust Graph for a specific purpose without exposing the full entity data.

→ Long-term feature. Deferred to Volume VIII — Trust Architecture

---

## Chapter 24 — Semantic Conflicts in Tagging

**Question:** What happens when the same concept has different meanings in different cultural contexts?

Use case: The tag `gospel` means Christian contemporary music in one context and traditional religious music in another. The tag `traditional` means different things in Nigeria vs. Uganda vs. Kenya.

Proposed solution: Namespace tags with cultural context where ambiguity exists:
```
culture:ugandan → music:traditional (Ugandan traditional)
culture:nigerian → music:traditional (Nigerian traditional)
```

Full resolution requires: a tagging governance process, cultural consultants per market, and a semantic conflict resolution protocol.

→ Ongoing — requires market-by-market cultural input

---

## Chapter 25 — Embedding Versioning

**Question:** When the embedding model is upgraded (e.g., from text-embedding-3-small to a future superior model), how are existing embeddings migrated?

The challenge: 10 million entity embeddings generated with model v1 are not directly comparable to embeddings generated with model v2. All similarity queries across model versions will return incorrect results.

Proposed solution:
- Track `embedding_model_version` per entity (already in the schema)
- On model upgrade: run a background migration job that recomputes all embeddings with the new model
- During migration: only compare embeddings within the same model version
- Migration window: completion before the old model is deprecated

→ Operational requirement. Specification deferred to Volume XI — Data & Intelligence Architecture

---

# Volume II Summary

```
Four Primitives:
  Entity  ·  Relationship  ·  Event  ·  Time

Entity Taxonomy (10 families, 35 types):
  Human  ·  Community  ·  Gathering  ·  Place
  Creator  ·  Organizational  ·  Economic
  Memory  ·  Trust  ·  Opportunity

Relationship Architecture:
  20 relationship types  ·  Weight decay  ·  Trust scores
  Lifecycle management  ·  Dispute resolution

Domain Events:
  Append-only ledger  ·  85+ event types
  Tamper detection  ·  Full audit trail

Temporal Layer:
  Existence time  ·  Validity time  ·  Transaction time
  Life Chapter taxonomy  ·  Tradition model

Six Graphs:
  Entity  ·  Knowledge  ·  Temporal
  Trust  ·  Opportunity  ·  Memory

Universal Tagging System:
  11 namespaces  ·  100+ tags
  Hierarchical  ·  Semantic relationships

Embedding Layer:
  All entities embedded at creation
  Six embedding types  ·  Version tracking

Capital Model:
  Memory  ·  Relationship  ·  Trust
  Community  ·  Experience  ·  Opportunity  ·  Legacy

Open Questions (8):
  Identity boundaries  ·  Multi-persona  ·  Decay calibration
  Community inheritance  ·  Cross-community reputation
  Portable trust  ·  Semantic conflicts  ·  Embedding versioning
```

---

# What Volume III Must Address

Volume III — Identity Architecture must resolve:

1. **The Identity Stack** — how a single phone number maps to a Person entity, one or more Personas, and their public/private presentation layers
2. **Multi-Persona Architecture** — how a DJ and a regular event-goer can be the same person without mixing their professional and personal identities
3. **Guardian-Child Identity** — how a parent manages a Child entity and transitions it to an independent Person entity at 18
4. **Anonymous Participation** — how a user attends events, posts Vibe Drops, and accumulates Trust Capital without exposing their real identity
5. **Identity Portability** — what a user can take with them if they leave the platform
6. **Cross-Platform Identity** — how ZUKA identity relates to a user's identity on other platforms (Spotify, Instagram, SafeBoda)
7. **Posthumous Identity** — what happens to a Person entity when the person dies, and how their Memory and Legacy Capital are preserved for their Family and Community

---

*Volume II is canonical. Extensions require architecture review.*
*Contradictions with this volume must be resolved before implementation.*

---

> **ZUKA Architecture Bible**
> Volume II — Canonical Domain Model & Entity Graph
> Built in Kampala. Built for Africa. Built for the world.
