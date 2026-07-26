# ZUKA Unified Architecture Bible
# BOOK II — CANONICAL DOMAIN MODEL

> **Constitutional Authority:** Level 1 — Domain Constitution
> **Source:** Canonical Volume II (Domain Model & Entity Graph)
>              synthesised with Zuka-Bible 2, Book I Part A (Core Entities)
>              and Book II (Entity Theory)
> **Status:** Substantive — this is not a stub; it contains the complete
>              canonical entity taxonomy

---

## Constitutional Premise

The Canonical Domain Model defines the reality that ZUKA operates upon.

All services, APIs, graphs, intelligence systems, dashboards, and governance
mechanisms derive from this model.

No intelligence system may redefine canonical entities. (Zuka-Bible 2, Book II, Ch.10)

---

## The Six Constitutionally Protected Entities

*(Zuka-Bible 2, Book I Part A, Ch.4.4-4.5; Volume II, Ch.17)*

```
IDENTITY      Who participates
RELATIONSHIP  How participants connect — first-class entities (ADR-001)
MEMORY        What participation leaves behind
TRUST         Confidence earned through participation (ADR-002)
COMMUNITY     Networks formed through participation
OPPORTUNITY   Potential value creation pathways
```

---

## Entity Taxonomy (Volume II §3.1–§3.10)

### §3.1 — Human Entities

```typescript
Person {
  // The canonical human entity. NOT "User". NOT "Participant".
  // "Participant" is a role, not an entity type.
  person_id:        UUID
  display_name:     TEXT
  phone_number:     TEXT         // primary identifier; one account per number
  email:            TEXT | null  // optional
  persona_ids:      UUID[]       // links to Persona entities (§3.8)
  status:           PersonStatus // 'active' | 'suspended' | 'memorialised' | 'deleted'

  // Trust profile
  trust_profile:    TrustProfile    // Volume III, Ch.15
  reputation_profile: ReputationProfile

  // Legacy
  legacy_settings:  LegacySettings  // Volume III, Part X

  // Temporal envelope (Volume VII, Ch.3 — mandatory on all entities)
  valid_from:       Timestamptz
  valid_to:         Timestamptz
  observed_at:      Timestamptz
  recorded_at:      Timestamptz
  confidence:       Float(0.0-1.0)
  version:          Integer

  // Knowledge Graph (Volume VIII — mandatory on all entities)
  embedding:        Vector(1536)
  tags:             Tag[]
  attributes:       JSONB
  signals:          Signal[]
}

Child {
  // Distinct from Person — a Child entity has different consent rules
  // (Volume IX, Ch.4) and cannot independently create a Twin
  child_id:         UUID
  guardian_ids:     UUID[]  // Person entity IDs of guardians
  // Transitions to Person entity at legal majority
}

Family {
  // Confirmed as a Community-type entity (Volume VI, Ch.4)
  // Has its own Family Vault (Volume V, Ch.16)
  family_id:        UUID
  family_name:      TEXT
  founding_members: UUID[]  // irreplaceable if missed — Volume VI, Ch.26
  family_vault_id:  UUID
}
```

### §3.2 — Place Entities

```typescript
Venue {
  // Physical or hybrid location where Gatherings occur
  venue_id:         UUID
  name:             TEXT
  venue_type:       VenueType
  // 'indoor' | 'outdoor' | 'hybrid' | 'virtual' | 'pop_up'
  district_id:      UUID    // Volume II §3.4
  city_id:          UUID
  lat:              Float
  lng:              Float
  capacity:         Integer | null
  embedding:        Vector(1536)
}

District {
  district_id:      UUID
  name:             TEXT
  city_id:          UUID
  polygon:          Geography  // GeoJSON polygon
}

City {
  city_id:          UUID
  name:             TEXT        // e.g., 'Kampala'
  country_id:       UUID
  // City Twin (Volume IX, Ch.11) — aggregate-only, no individual consent
}

Country {
  country_id:       UUID
  iso_3166_code:    TEXT        // e.g., 'UG'
  name:             TEXT
  // Country readiness domain (Volume XIV, Ch.3a)
  // Legal Addendum reference (Volume XVIII, Part V)
}
```

### §3.3 — Time Entities

```typescript
// Time is a foundational dimension (Zuka-Bible 2, Book II, Ch.6)
// All entities carry the TemporalEntity envelope (Volume VII, Ch.3):
//   valid_from, valid_to, observed_at, recorded_at, confidence, version
// Four Clocks (Volume VII, Part III):
//   Event Clock, Observation Clock, Recording Clock, Processing Clock

LifeChapter {
  // A named period in a Person's life (Volume III, Ch.12)
  chapter_id:       UUID
  person_id:        UUID
  name:             TEXT      // e.g., 'University Years', 'First Job'
  started_at:       Date
  ended_at:         Date | null
}
```

### §3.4 — Gathering Entities

```typescript
Gathering {
  // The canonical community-scale event entity. NOT "Event". (ADR-011)
  gathering_id:     UUID
  title:            TEXT
  gathering_type:   GatheringType     // 40+ types (Volume IV, Ch.6)
  status:           GatheringStatus
  // 'draft' | 'published' | 'live' | 'ended' | 'cancelled'
  host_persona_id:  UUID              // the organiser Persona
  primary_community_id: UUID
  venue_id:         UUID | null       // null for virtual
  starts_at:        Timestamptz
  ends_at:          Timestamptz
  capacity:         Integer | null
  tradition_id:     UUID | null       // if part of a Tradition (Volume IV, Ch.11)
  embedding:        Vector(1536)
}

GatheringTier {
  // Ticket pricing and capacity categories (Volume XXI, Ch.2)
  tier_id:          UUID
  gathering_id:     UUID
  tier_name:        TEXT
  price:            Decimal
  currency_code:    CurrencyCode
  capacity:         Integer
  tickets_sold:     Integer  // computed via events
}

Tradition {
  // A recurring Gathering recognised as a cultural pattern (Volume IV, Ch.11)
  tradition_id:     UUID
  name:             TEXT | null   // null until confirmed (discovered, not created)
  community_id:     UUID
  status:           TraditionStatus
  // 'seeded' | 'active' | 'at_risk' | 'lapsed' | 'revived'
  occurrence_count: Integer
  first_occurrence: Timestamptz
}
```

### §3.5 — Creator Entities

```typescript
// Creators are Personas (§3.8) with creative professional roles
// Types: DJ, Photographer, MC, Artist, Videographer, Designer

CreatorStats {
  // Attached to a Persona of type 'creator'
  persona_id:             UUID
  specialisations:        TEXT[]
  bookings_completed:     Integer
  avg_booking_rating:     Float | null
  delivery_rate:          Float     // completed / accepted
  booking_rate_range_min: Decimal
  booking_rate_range_max: Decimal
  equipment_list:         TEXT[]
}
```

### §3.6 — Organisation Entities

```typescript
Organisation {
  // Companies, NGOs, Schools, Churches, Alumni bodies
  org_id:           UUID
  org_type:         OrgType
  // 'company' | 'ngo' | 'school' | 'church' | 'alumni' | 'government'
  name:             TEXT
  registration_number: TEXT | null
  embedding:        Vector(1536)
}

Brand {
  // Commercial entity eligible for Sponsorship Engine (Volume XV, Ch.4)
  brand_id:         UUID
  org_id:           UUID    // parent organisation
  target_demographic: JSONB
}
```

### §3.7 — Community Entities

```typescript
Community {
  // Eight community families (Volume VI, Ch.5):
  // Family, Faith, Educational, Geographic, Professional,
  // Interest, Cultural, Event
  community_id:     UUID
  name:             TEXT
  community_family: CommunityFamily
  community_type:   TEXT            // specific type within family
  founding_members: UUID[]          // irreplaceable if missed
  founding_date:    Date
  lifecycle_stage:  LifecycleStage
  // 'formation' | 'growth' | 'maturity' | 'transformation' | 'legacy'
  // Volume VI, Ch.22's five-stage model — canonical
  governance_model: CommunityGovernanceModel
  trust_profile:    CommunityTrustProfile   // Volume VI, Part XI
  community_archive_id: UUID               // Volume V, Ch.17
  embedding:        Vector(1536)
}

CommunityMembership {
  membership_id:    UUID
  person_id:        UUID
  community_id:     UUID
  role:             MemberRole
  // 'member' | 'elder' | 'founder' | 'archivist' | 'treasurer'
  joined_at:        Timestamptz
  status:           MembershipStatus
}
```

### §3.8 — Identity & Persona Entities

```typescript
Persona {
  // A Person may have multiple Personas (Volume III, Ch.9)
  // e.g., personal, professional, creator, organiser
  persona_id:       UUID
  person_id:        UUID        // owning Person
  persona_type:     PersonaType
  display_name:     TEXT
  visibility:       VisibilityLevel   // Volume III, Part VI
  persona_trust_score: Float | null
  tags:             Tag[]
}

Verification {
  // Earned verification tiers (Volume III, Ch.6)
  verification_id:  UUID
  person_id:        UUID
  tier:             VerificationTier
  // 'basic_phone' | 'phone_otp' | 'id_document' | 'biometric'
  verified_at:      Timestamptz
  method:           TEXT
  confidence:       Float(0.0-1.0)
}

Badge {
  badge_id:         UUID
  person_id:        UUID
  badge_type:       TEXT
  awarded_at:       Timestamptz
  awarded_for:      TEXT        // human-readable reason
  provenance_record_id: UUID    // ADR-007: every badge carries ProvenanceRecord
}
```

### §3.9 — Memory Entities

```typescript
Memory {
  // Preserved meaning derived from participation. NOT raw media. (ADR-009)
  memory_id:        UUID
  person_id:        UUID
  gathering_id:     UUID | null
  community_id:     UUID | null
  title:            TEXT | null
  significance:     TEXT | null   // the meaning, not the media
  importance_level: Integer(1-5)
  emotion_tags:     EmotionTag[]  // irreplaceable if missed (Volume V, Ch.28)
  life_chapter_id:  UUID | null
  tradition_id:     UUID | null
  tagged_person_ids: UUID[]
  media_refs:       UUID[]        // references to separate Media entities
  visibility:       VisibilityLevel
  embedding:        Vector(1536)
  // TemporalEntity envelope — mandatory
}

Media {
  // Evidence layer — bytes, not meaning. Separate from Memory. (ADR-009)
  media_id:         UUID
  media_type:       MediaType  // 'photo' | 'video' | 'audio' | 'document'
  storage_url:      TEXT
  storage_tier:     StorageTier  // 'hot' | 'warm' | 'cold'
  file_size_bytes:  Integer
  captured_at:      Timestamptz | null   // when the media was created
  uploader_id:      UUID
  checksum:         TEXT
}

Story {
  // A curated narrative linking multiple Memories (Volume V)
  story_id:         UUID
  creator_id:       UUID
  title:            TEXT
  memory_ids:       UUID[]
  community_id:     UUID | null
  visibility:       VisibilityLevel
}
```

### §3.10 — Opportunity & Commerce Entities

```typescript
Opportunity {
  // Formal opportunity entity
  opportunity_id:   UUID
  opportunity_type: OpportunityType
  // 'job' | 'sponsorship' | 'booking' | 'collaboration' |
  // 'mentorship' | 'scholarship'
  status:           OpportunityStatus
  // 'emerging' | 'proposed' | 'active' | 'completed' | 'declined'
  parties:          UUID[]      // Person/Organisation IDs involved
  community_id:     UUID | null
  provenance_record_id: UUID    // ADR-007 — mandatory
  embedding:        Vector(1536)
}

Ticket {
  // Full canonical schema (Volume XXI, Ch.1)
  ticket_id:        UUID
  ticket_ref:       TEXT       // 'TKT-XXXXXXXX'
  gathering_id:     UUID
  tier_id:          UUID
  owner_person_id:  UUID       // current holder
  original_purchaser_person_id: UUID
  status:           TicketStatus
  paid_amount:      Decimal
  currency_code:    CurrencyCode
  smart_bucket_token: TEXT     // time-limited JWT (Volume XXI, Ch.5)
  smart_bucket_token_expires_at: Timestamptz
  is_gift:          Boolean
  memory_id:        UUID | null
  embedding:        Vector(1536)
  // TemporalEntity envelope — mandatory
}

Relationship {
  // First-class entity. NOT a simple foreign key. (ADR-001)
  relationship_id:  UUID
  from_entity_id:   UUID
  to_entity_id:     UUID
  relationship_type: RelationshipType
  // 'friend' | 'family' | 'colleague' | 'mentor' | 'student' |
  // 'community_member' | 'business_partner' | 'follows' | etc.
  weight:           Float(0.0-1.0)     // decays over time (Volume VII)
  trust_score:      Float(0.0-1.0)
  lifecycle_stage:  RelationshipLifecycle
  started_at:       Timestamptz
  embedding:        Vector(1536)
}
```

---

## The Seven Capital Types (Volume II, Ch.17; ADR-005)

```
Memory Capital      — accumulated preserved meaning
Relationship Capital — depth and breadth of connections
Trust Capital        — earned confidence across dimensions
Community Capital    — standing within and across communities
Experience Capital   — breadth and depth of gathering experience
Opportunity Capital  — value-creation pathways earned
Legacy Capital       — what persists beyond active participation
```

Capital Ontology is constitutional. No competing taxonomy may be introduced without
constitutional amendment (ADR-005).

---

## The Seven Knowledge Graphs (Volume VIII, Ch.7; ADR-012)

```
Identity Graph        — Person → Persona → Verification → Badge
Community Graph       — Community → Membership → Federation
Memory Graph          — Memory → Media → Story → Tradition
Trust Graph           — TrustEdge → TrustTimeline → TrustProfile
Opportunity Graph     — Opportunity → OpportunityEdge → Emergence
Tradition Graph       — Tradition → Occurrence → TraditionEdge
Digital Presence Graph — (Dormant — privacy review required first)

Temporal: cross-cutting dimension over all seven, not an eighth graph
```

---

## Entity Lifecycle Model (Zuka-Bible 2, Book II, Ch.8)

```
Person:    Creation → Verification → Participation → Legacy
Memory:    Capture → Preservation → Interpretation → Discovery → Legacy
Community: Formation → Growth → Maturity → Transformation → Legacy
Trust:     Earned → Strengthened → Maintained → Decayed/Recovered
Opportunity: Discovery → Evaluation → Participation → Outcome
Tradition: Seeded → Confirmed → Strengthened → At-Risk/Lapsed/Revived
Ticket:    Payment_Pending → Issued → Checked_In → [Expired/Refunded/Revoked]
```

---

## Domain Boundary Rules (Volume XI, Ch.3; Zuka-Bible 2, Book II, Ch.10)

```
No domain leakage.
Every entity belongs to exactly one primary domain.
Cross-domain reads go through defined interfaces.
No intelligence system may redefine canonical entities.
No duplicate canonical entities.
No hidden entity classes.
```

```
Identity Domain     → Person, Child, Persona, Verification, Badge
Community Domain    → Community, CommunityMembership, Family
Gathering Domain    → Gathering, GatheringTier, Tradition, Venue
Memory Domain       → Memory, Media, Story
Trust Domain        → TrustProfile, TrustEdge, TrustTimeline, FraudSignal
Opportunity Domain  → Opportunity, Ticket, GatheringTier
Payments Domain     → PaymentIntent, Payout, WalletLedger
Notifications Domain → NotificationRecord, DeviceToken

Cross-cutting (not owned by a single domain):
  Relationship       → spans Identity + Community + all domains
  Knowledge Graphs   → views over all domain data (Volume VIII)
  Twins / Agents     → interpretation layer over all domains (Volumes IX-X)
```

---

*Source: Volume II (full entity taxonomy); Zuka-Bible 2, Books I-II*
*Authority: Level 1 — Domain Constitution*
*Next: Book III — Identity Civilization*
