# ZUKA BIBLE V4 — VOLUME 04
# Canonical Data Model & Graph Constitution

> **Part II — The Data Foundation** · **Authority Level 1**
> Synthesises: BLACK Edition XXXI, Volume II (Domain Model), Volume XII (Data Architecture)

---

## Chapter 1 — The Constitutional Premise

The Canonical Data Model is the permanent language of the ZUKA ecosystem. Applications
change. Technology changes. The canonical model must endure.

```
Model reality. Never model screens.
  Correct:   Person · Gathering · Venue · Community · Memory · Opportunity
  Incorrect: Profile Screen · Event Page · Dashboard Widget
```

No intelligence system may redefine canonical entities. (ADR-003, Volume XII premise.)

---

## Chapter 2 — The Three-Tier Entity Hierarchy

```
TIER 0 — FOUNDATIONAL (constitutionally protected; cannot be removed)
  Identity · Relationship · Memory · Trust · Community · Opportunity

TIER 1 — OPERATIONAL (the working entities of the platform)
  Person · Child · Persona · Verification · Badge · Gathering ·
  GatheringTier · Tradition · Venue · District · City · Country ·
  Media · Story · Ticket · PaymentIntent · Campaign · Organisation

TIER 2 — DERIVED (produced by intelligence; never a source of truth)
  Recommendation · Insight · Prediction · Intelligence · DataReadinessScore ·
  CapabilityReadiness · ProvenanceRecord
```

The Tier-2 doctrine is constitutional: **AI systems never become sources of truth.**
Truth flows Events → Relationships → Graphs → Intelligence, never backward.

---

## Chapter 3 — The Global Identifier Doctrine

```
Every entity receives a GUID:
  Format:        ZKA_<ENTITY>_<UUID>
  Requirements:  Immutable · Globally Unique · Non-Reusable · Traceable
  Examples:      ZKA_PERSON_<uuid>
                 ZKA_GATHERING_<uuid>
                 ZKA_COMMUNITY_<uuid>
                 ZKA_MEMORY_<uuid>
                 ZKA_TICKET_<uuid>
```

---

## Chapter 4 — Universal Entity Metadata

Every entity in every tier inherits this metadata envelope:

```typescript
interface CanonicalEntity {
  // Identity
  id:              string        // ZKA_<ENTITY>_<UUID>
  entity_type:     EntityType
  status:          EntityStatus

  // Temporal envelope (Volume 05 — mandatory on ALL entities)
  valid_from:      Timestamptz   // when the fact became true in the world
  valid_to:        Timestamptz   // when it stopped (null = still true)
  observed_at:     Timestamptz   // when ZUKA became aware
  recorded_at:     Timestamptz   // when written to the database
  confidence:      number        // 0.0 - 1.0
  version:         integer       // increments on every update

  // Ownership (Ownership Doctrine — mandatory)
  owner_id:        string        // the entity that owns this record
  steward_id:      string | null // who cares for it (may differ from owner)
  visibility:      VisibilityLevel
  privacy_class:   0 | 1 | 2 | 3 // Public | Community | Private | Restricted

  // Knowledge Graph readiness (Volume 06 — mandatory)
  embedding:       Vector(1536)  // semantic embedding (nullable until DRL 1)
  tags:            Tag[]
  attributes:      JSONB
  signals:         Signal[]

  // Audit
  created_at:      Timestamptz
  updated_at:      Timestamptz
}
```

---

## Chapter 5 — Canonical Entity Schemas

### Person (Tier 0 — Identity)
```typescript
interface Person extends CanonicalEntity {
  display_name:        string
  phone_number:        string        // primary identifier; one account per number
  email:               string | null // optional
  identity_level:      IdentityLevel // see Volume 08 lifecycle (0-7)
  verification_level:  VerificationLevel // L0-L4
  persona_ids:         string[]
  trust_profile_id:    string
  legacy_settings_id:  string
}
```

### Gathering (Tier 1)
```typescript
interface Gathering extends CanonicalEntity {
  title:               string
  gathering_type:      GatheringType    // 40+ types (Volume 13)
  host_persona_id:     string
  primary_community_id: string | null
  venue_id:            string | null    // null for virtual
  starts_at:           Timestamptz
  ends_at:             Timestamptz
  capacity:            integer | null
  tradition_id:        string | null
  gathering_medium:    'physical' | 'digital' | 'hybrid'  // Volume 13
  lifecycle_state:     GatheringState
}
```

### Community (Tier 0)
```typescript
interface Community extends CanonicalEntity {
  name:                string
  community_family:    CommunityFamily  // 8 families (Volume 12)
  community_type:      string
  founding_members:    string[]         // irreplaceable if missed
  founding_date:       Date
  lifecycle_stage:     LifecycleStage   // 5 stages (Volume 12)
  governance_model_id: string
  trust_profile_id:    string
  archive_id:          string
}
```

### Memory (Tier 0)
```typescript
interface Memory extends CanonicalEntity {
  creator_id:          string
  gathering_id:        string | null
  community_id:        string | null
  title:               string | null
  significance:        string | null    // the MEANING, not the media
  importance_level:    1 | 2 | 3 | 4 | 5
  emotion_tags:        EmotionTag[]     // irreplaceable if missed
  life_chapter_id:     string | null
  tradition_id:        string | null
  tagged_person_ids:   string[]
  media_refs:          string[]         // references to separate Media entities
  memory_type:         MemoryType       // 8 types (Volume 10)
}
```

### Relationship (Tier 0 — first-class, ADR-001)
```typescript
interface Relationship extends CanonicalEntity {
  from_entity_id:      string
  to_entity_id:        string
  relationship_type:   RelationshipType
  weight:              number           // 0.0-1.0, decays over time
  trust_score:         number           // 0.0-1.0
  interaction_count:   integer
  last_interaction_at: Timestamptz
  lifecycle_stage:     RelationshipLifecycle  // 7 stages (Volume 09)
  origin_gathering_id: string | null
}
```

### Trust Profile (Tier 0)
```typescript
interface TrustProfile extends CanonicalEntity {
  subject_id:          string
  dimensions: {                          // six dimensions (Volume 11)
    reliability:        number
    authenticity:       number
    professionalism:    number
    safety:             number
    consistency:        number
    community_standing: number
  }
  pyramid_level:       1 | 2 | 3 | 4 | 5
  // composite is computed, never stored as a single exposed number
}
```

### Opportunity (Tier 0)
```typescript
interface Opportunity extends CanonicalEntity {
  opportunity_type:    OpportunityType  // job|sponsorship|booking|
                                        // collaboration|mentorship|scholarship
  parties:             string[]
  community_id:        string | null
  lifecycle_state:     OpportunityState
  provenance_record_id: string          // ADR-007 — mandatory
}
```

### Ticket (Tier 1)
```typescript
interface Ticket extends CanonicalEntity {
  ticket_ref:          string           // human-readable TKT-XXXXXXXX
  gathering_id:        string
  tier_id:             string
  owner_person_id:     string
  original_purchaser_id: string
  ticket_class:        TicketClass       // standard|group|table|vip|
                                         // streaming|comp|staff
  provenance_origin:   ProvenanceOrigin  // closed-system origin (Volume 14)
  smart_bucket_token:  string            // rotating QR token
  paid_amount:         decimal
  currency_code:       string
  is_gift:             boolean
  lifecycle_state:     TicketState
  memory_id:           string | null     // set after check-in
}
```

Full schemas for all 56 entities are maintained in the Master Entity Registry appendix.

---

## Chapter 6 — Canonical Relationship Types

```
PERSONAL:       friend · family · couple · neighbour
PROFESSIONAL:   colleague · business_partner · mentor · student ·
                employer · employee
COMMUNITY:      community_member · follows · co_organiser
CREATIVE:       collaborator · client · agent
GRAPH EDGES:    KNOWS · ATTENDED_WITH · COLLABORATED_WITH · TRUSTS ·
                MENTORED · ORGANIZED_WITH · RECOMMENDED · SPONSORED ·
                MEMBER_OF · HOSTED · CREATED · PARTICIPATED_IN · ENDORSED
```

Relationships are first-class entities (ADR-001), never join tables.

---

## Chapter 7 — Event Sourcing Doctrine

```
Nothing important is overwritten. Everything becomes an event. (ADR-015)

Canonical Event Envelope:
  event_id      ZKA_EVENT_<UUID>
  event_type    e.g. GatheringCreated, TicketPurchased
  actor_id      who/what caused it
  timestamp     observed_at + recorded_at
  source        which system emitted it
  payload       the event-specific data
  version       envelope schema version

The Event Store table has no UPDATE or DELETE operations against existing
records. Corrections append a superseding event. (Temporal Truth Principle.)
```

The full 77-event registry is maintained in the Master Event Registry appendix and
referenced by each domain volume.

---

## Chapter 8 — The Seven Capital Types (ADR-005)

```
Memory · Relationship · Trust · Community · Experience · Opportunity · Legacy
```

Capital Ontology is constitutional. No competing taxonomy may be introduced without a
constitutional amendment. Every capability that generates or consumes capital must
declare which type(s) it affects.

---

## Chapter 9 — Data Quality Constitution (ADR-018)

```
Five mandatory controls — all detect aggressively, correct conservatively:
  Duplicate detection          flag for review; NEVER auto-merge Person entities
  Missing field detection      block on required; warn on recommended
  Broken relationship detection on-write constraint + scheduled sweep
  Invalid timestamp detection  validate the four-clock ordering
  Orphaned entity detection    flag after grace period; NEVER auto-delete

Bad data compounds exactly as relentlessly as good data. A silent "correction"
is a silent rewrite of reality — forbidden by ADR-003.
```

---

## Chapter 10 — Data Portability & Anti-Lock-In

Users must be able to export their data, memories, communities, and histories.
Anti-lock-in is a constitutional principle. Every export carries provenance annotations
and is reachable within three taps from settings (no guilt-trip interstitials).

---

## Chapter 11 — The Founder Rules for Data

Every feature proposal must answer: What new entities? Relationships? Events? Graph
edges? Which DRL level? Which CRL level? Which future systems depend on it? If
unanswered, the feature is rejected. **Data debt is architectural debt.**

---

> **Volume 04 — Canonical Data Model & Graph Constitution**
> The permanent language of the ZUKA ecosystem.
