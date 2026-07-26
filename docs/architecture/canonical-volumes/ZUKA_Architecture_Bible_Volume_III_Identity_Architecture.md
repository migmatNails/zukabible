# ZUKA Architecture Bible
# Volume III — Identity Architecture

> **Series:** ZUKA Architecture Bible
> **Volume:** III of N
> **Status:** Canonical — foundational to all future volumes
> **Depends on:** Volume I (Vision & Philosophy), Volume II (Domain Model)
> **Required by:** Volume IV (Memory), Volume V (Community), Volume VI (Ticketing),
>                  Volume VII (Streaming), Volume VIII (Trust), Volume IX (Opportunity),
>                  Volume X (AI & Intelligence), Volume XI (Data), all engineering specifications

---

# Preface

Volume I established why ZUKA exists.
Volume II established what ZUKA is made of.
Volume III establishes who ZUKA is for — and what it means to be a person on this platform.

If Volume II is the DNA, Volume III is the soul.

Every system in ZUKA eventually attaches to Identity:

```
Identity
  ↓
Relationships
  ↓
Communities
  ↓
Gatherings
  ↓
Memories
  ↓
Trust
  ↓
Opportunities
  ↓
Legacy
```

Get Identity wrong and every downstream system is built on a fragile foundation.
Get it right and the platform becomes a living, continuous record of a human life — irreplaceable, compounding, and deeply personal.

This volume is the most consequential in the Architecture Bible.
It will be referenced more than any other.

---

# Part I — Identity Philosophy

---

## Chapter 1 — The Problem with "User"

Most software systems view people as:

```
User {
  id
  email
  password
  created_at
}
```

This model treats a human being as a database record. It captures nothing of who they are, nothing of what they have experienced, nothing of who they are connected to, nothing of who they are becoming.

The "User" model was designed for transactional systems. It was never designed for platforms that aspire to serve human life.

ZUKA must reject this model entirely.

---

## Chapter 2 — What Identity Really Is

Identity is not an account.
Identity is not a username.
Identity is not a profile photo and a bio.

**Identity is continuity through time.**

A person at 22 attending their first music festival is the same person at 45 organising their child's graduation ceremony. The platform must hold both of these moments, connect them, and recognise the continuous thread of identity that runs through them.

Identity in ZUKA is:

```
Human Being
  ↓
  Who they are (Person entity — the core)
  ↓
  Who they are to others (Relationships)
  ↓
  Where they belong (Communities)
  ↓
  What they have experienced (Gatherings + Memories)
  ↓
  What others know them to be (Trust + Reputation)
  ↓
  Who they are becoming (Life Chapters)
  ↓
  What they leave behind (Legacy)
```

This is not a data model. This is a philosophy encoded as a data model.

---

## Chapter 3 — Identity as Continuity

The human experience of identity is continuous. People do not feel that they become a new person when they change jobs, move cities, or graduate from university. They carry their history with them.

ZUKA's identity model must reflect this continuity:

- A Memory created in 2025 must still be accessible and meaningful in 2045
- A Relationship built through shared gatherings in Kampala must persist when the person moves to Nairobi
- A Tradition established by a community must survive changes in community membership
- A Trust score earned over years must not be erased by a platform migration

This continuity requirement shapes every architectural decision in this volume.

---

## Chapter 4 — Identity in the African Context

Identity in Africa is rarely purely individual.

Ubuntu philosophy — *"I am because we are"* — encodes a relational model of personhood that predates any software system. A person is understood through their family, their clan, their community, their church, their neighbourhood.

This is not a limitation. It is a feature.

ZUKA's multi-dimensional identity model is not a concession to complexity. It is a recognition that the African understanding of personhood is more accurate than the Western "user" model — and that building from this foundation creates a platform that feels native to its users in a way that imported Western platforms never will.

The African context also brings specific identity considerations:

**Clan and family identity** are as important as individual identity for certain life events (weddings, introduction ceremonies, funerals, naming ceremonies).

**Community identity** (church member, school alumni, neighbourhood resident) carries real social weight and must be represented authentically.

**Generational identity** — knowing that you are the third generation of a family that has attended the same annual gathering — is a form of identity that no current platform supports. ZUKA must.

**Oral tradition** means that identity is often held in community memory, not personal records. ZUKA can digitise and preserve this — but must do so in a way that respects community ownership of that memory.

---

## Chapter 5 — The Identity Stack

ZUKA's identity model has five layers. Each layer builds on the one below it.

```
┌─────────────────────────────────────────────────────────────┐
│  LAYER 5 — LEGACY IDENTITY                                  │
│  What endures after the individual's active life            │
│  Family archives · Tradition stewardship · Community memory │
├─────────────────────────────────────────────────────────────┤
│  LAYER 4 — SOCIAL IDENTITY                                  │
│  Who the person is to others                                │
│  Relationships · Communities · Reputation · Trust           │
├─────────────────────────────────────────────────────────────┤
│  LAYER 3 — BIOGRAPHICAL IDENTITY                            │
│  The story of who they are becoming                         │
│  Life Chapters · Traditions · Memory archive                │
├─────────────────────────────────────────────────────────────┤
│  LAYER 2 — PERSONA IDENTITY                                 │
│  The roles they play in the world                           │
│  Consumer · Organiser · Creator · Parent · Professional     │
├─────────────────────────────────────────────────────────────┤
│  LAYER 1 — CORE IDENTITY                                    │
│  The irreducible person                                      │
│  Phone number · Name · Date of birth · Verified existence   │
└─────────────────────────────────────────────────────────────┘
```

Layer 1 is the foundation. Every other layer builds on it.
Layer 5 is the horizon. Few platforms ever reach it.
ZUKA must be designed to reach Layer 5 from day one — even if only Layer 1 is implemented at MVP.

---

# Part II — Universal Identity Model

---

## Chapter 6 — The Identity Record

The Identity Record is the central data structure that connects all layers of a person's presence on ZUKA. It is not a replacement for the Person entity defined in Volume II — it is the aggregating envelope that holds the Person entity and all the systems attached to it.

```typescript
Identity {

  // ── CORE ──────────────────────────────────────────────────
  identity_id:          UUID
  // Globally unique. Immutable. Never recycled.
  // Distinct from person_entity_id — the identity_id
  // is the root reference that survives any entity migration.

  person_entity_id:     UUID
  // References the Person entity in Volume II's entity graph.

  status:               IdentityStatus
  // active | suspended | memorialised | deleted_pending | deleted

  created_at:           Timestamptz
  // When this identity was first established on the platform.
  // Never changes. The beginning of the identity timeline.

  // ── PERSON PROFILE ────────────────────────────────────────
  person_profile: {
    display_name:         TEXT          // how they appear to others
    full_name:            TEXT          // private — legal name
    date_of_birth:        Date          // private — exact
    age_bracket:          AgeBracket    // public — derived
    gender:               TEXT          // free text — optional
    primary_phone_hash:   TEXT          // hashed — never raw in this record
    primary_city_id:      UUID
    neighbourhood_id:     UUID          // optional — Tier B consent
    profile_photo_url:    TEXT
    bio:                  TEXT          // 280 chars max
    language_preference:  LanguageCode[]
    timezone:             TEXT
  }

  // ── PERSONAS ──────────────────────────────────────────────
  personas:             Persona[]
  // See Chapter 8 — Multi-Persona Architecture
  active_persona_id:    UUID
  // The persona currently active in the user's session.

  // ── RELATIONSHIPS ─────────────────────────────────────────
  relationship_graph: {
    total_connections:    Integer       // computed
    friend_count:         Integer       // computed
    family_connection_ids: UUID[]       // Person entity IDs
    community_ids:        UUID[]        // Community entity IDs
    following_ids:        UUID[]        // Venue/Creator/Community IDs
    follower_count:       Integer       // computed
  }
  // Full graph stored in Relationship entities (Volume II).
  // This summary is cached for performance.

  // ── COMMUNITIES ───────────────────────────────────────────
  community_memberships: CommunityMembership[]
  // { community_id, role, joined_at, membership_tier }

  // ── TRUST & REPUTATION ────────────────────────────────────
  trust_profile: {
    overall_trust_score:    Float(0.0–1.0)
    trust_dimensions:       TrustDimensions
    verification_tier:      VerificationTier
    badges:                 Badge[]
    review_count:           Integer
    review_average:         Float
    is_verified:            Boolean
    verified_at:            Timestamptz
  }

  reputation_profile: {
    reputation_score:       Float(0.0–1.0)
    reputation_trend:       Trend       // rising | stable | falling
    public_reviews:         Review[]    // capped at last 20 for display
    organiser_stats: {
      events_hosted:        Integer
      total_tickets_sold:   Integer
      delivery_rate:        Float       // events delivered / events listed
      avg_rating:           Float
    }
    creator_stats: {
      bookings_completed:   Integer
      avg_booking_rating:   Float
    }
  }

  // ── MEMORIES ──────────────────────────────────────────────
  memory_profile: {
    total_memories:         Integer
    total_gatherings_attended: Integer
    first_gathering_at:     Timestamptz
    most_recent_gathering_at: Timestamptz
    memory_capital_score:   Float
    traditions:             Tradition[]
    life_chapter_summary:   LifeChapterSummary[]
  }

  // ── LIFE CHAPTERS ─────────────────────────────────────────
  life_chapters:          LifeChapter[]
  current_chapter:        LifeChapterType
  // See Chapter 14 — Identity Timeline & Life Chapters

  // ── PREFERENCES ───────────────────────────────────────────
  preferences: {
    event_categories:       EventCategory[]   // ordered preference list
    vibe_tags:              Tag[]
    price_bracket:          PriceBracket
    max_travel_distance_km: Integer
    notification_settings:  NotificationPreferences
    discovery_mode:         DiscoveryMode
    // 'personalised' | 'explore' | 'social' | 'manual'
  }

  // ── PRIVACY ───────────────────────────────────────────────
  privacy_settings:       PrivacySettings
  // See Chapter 16 — Privacy Architecture

  // ── CAPITAL ───────────────────────────────────────────────
  capital_profile: {
    memory_capital:         Float
    relationship_capital:   Float
    trust_capital:          Float
    community_capital:      Float
    experience_capital:     Float
    opportunity_capital:    Float
    legacy_capital:         Float
    total_pulse_points:     Integer
  }

  // ── LEGACY ────────────────────────────────────────────────
  legacy_settings:        LegacySettings
  // See Chapter 18 — Legacy & Succession

  // ── ECONOMIC ──────────────────────────────────────────────
  economic_profile: {
    wallet_ids:             UUID[]
    active_subscriptions:   Subscription[]
    payout_account_id:      UUID          // for organisers
    payment_methods:        PaymentMethodRef[]
  }

  // ── INTELLIGENCE ──────────────────────────────────────────
  embedding:              Vector(1536)
  embedding_updated_at:   Timestamptz
  ai_twin_id:             UUID          // optional — see Volume X
}
```

---

## Chapter 7 — Identity Status Lifecycle

```
PENDING_VERIFICATION
  Phone number captured, OTP not yet verified.
  No platform access. Limited to 15-minute window.
  ↓
ACTIVE
  Standard operational status.
  Full platform access per privacy and age settings.
  ↓
SUSPENDED
  Temporary restriction — investigation, reported abuse,
  payment dispute. Limited read-only access.
  Cannot purchase tickets, create events, or initiate transfers.
  ↓
MEMORIALISED
  The person has died. The account is converted to a
  memorial state. See Chapter 18 — Legacy & Succession.
  Memories and community contributions preserved indefinitely.
  No new activity. Legacy steward assigned.
  ↓
DELETION_PENDING
  User requested deletion. 30-day grace period.
  Account suspended during grace period.
  Recoverable by the user within 30 days.
  ↓
DELETED
  Personal identifiers purged.
  Anonymised behavioural records retained (legitimate interest basis).
  Relationship edges preserved with anonymised node.
  Memories the person is tagged in: their tag is anonymised,
  not removed (their presence at the gathering is historical fact).
```

---

# Part III — Multi-Persona Architecture

---

## Chapter 8 — A Person Is Not One Thing

A single human being operating on ZUKA may simultaneously hold all of the following roles:

```
The same person can be:

  CONSUMER       — attending events, buying tickets, building memories
  ORGANISER      — creating and running events
  CREATOR        — DJing, performing, photographing
  PARENT         — managing a Child entity, attending family events
  COMMUNITY LEADER — running a church, alumni association, or neighbourhood group
  STUDENT        — a member of a school community
  ALUMNI         — a graduate of that same school, years later
  VOLUNTEER      — supporting NGO events
  TRAVELLER      — attending events in cities they don't live in
  BRAND AMBASSADOR — representing a commercial partner at events
  MENTOR         — guiding younger creators or organisers
```

No current entertainment platform supports this reality. They all collapse it into a single "user" type and force people to create separate accounts for different roles.

ZUKA must support the full complexity of human roles within a single identity.

---

## Chapter 9 — The Persona Model

A Persona is a named role-context in which a person operates on the platform. It is a lens over the same Identity — not a separate identity.

```typescript
Persona {
  persona_id:         UUID
  identity_id:        UUID          // parent identity
  persona_type:       PersonaType
  display_name:       TEXT
  // Can differ from identity display_name.
  // A DJ may have a stage name separate from their personal name.

  is_active:          Boolean
  is_primary:         Boolean       // the default persona at login
  is_public:          Boolean       // whether this persona is discoverable

  // Persona-specific profile data
  profile: {
    bio:              TEXT
    photo_url:        TEXT
    contact_info:     PersonaContactInfo   // public contact for this persona
    tags:             Tag[]
    location_display: TEXT                 // city/region — not precise
  }

  // Persona-specific settings
  privacy_settings:   PersonaPrivacySettings
  notification_settings: PersonaNotificationSettings

  // Capabilities unlocked by this persona
  capabilities:       PersonaCapability[]
  // 'create_events' | 'sell_tickets' | 'receive_payouts' |
  // 'book_creators' | 'manage_community' | 'scan_tickets' |
  // 'access_analytics' | 'post_opportunities'

  // Economic identity for this persona
  payout_account_id:  UUID          // null if persona doesn't receive payouts
  subscription_id:    UUID          // persona-level subscription (e.g., Pro tier)

  // Trust and reputation for this persona
  // (separate from global identity trust — see Chapter 15)
  persona_trust_score: Float
  persona_reputation:  PersonaReputation

  created_at:         Timestamptz
  last_active_at:     Timestamptz
}

PersonaType enum:
  consumer            // browsing, attending, memory-building
  organiser           // event creation and management
  dj                  // DJ booking and performance
  artist              // musical or performing artist
  photographer        // event photography and videography
  mc                  // master of ceremonies
  venue_manager       // managing a Venue entity
  community_leader    // managing a Community entity
  parent              // managing Child entities
  brand               // brand/company representative
  volunteer           // non-commercial event support
  media               // press, content creators, journalists
```

---

## Chapter 10 — Persona Switching

The user experience of personas is **seamless switching**, not separate logins.

```
Single sign-on with phone number + OTP.
After authentication: identity is resolved.
App detects active personas and presents switcher if >1 exists.

Persona switcher UI:
  [ James K. (Personal) ]   ← active
  [ DJ Blaze (Creator)  ]
  [ Club Nexus (Venue)  ]
  [ + Add Persona       ]

Switching persona:
  1. Tap desired persona
  2. Instant switch — no re-authentication required
  3. UI adapts to the capabilities and context of the new persona:
     - Navigation items change (organiser sees scanner, analytics)
     - Feed content adapts (venue manager sees competitor activity)
     - Notification context switches
     - Economic context switches (different wallet, different payouts)
```

### Cross-Persona Privacy
By default, personas are **not linked** in any public-facing way. A DJ persona and a personal persona sharing the same Identity are not publicly connected unless the person explicitly chooses to link them.

This protects:
- A DJ who wants to keep their personal life private
- An organiser whose personal gathering preferences they don't want associated with their professional brand
- A community leader who attends events in a personal capacity without that being visible to their community

Internally (for trust and fraud detection), the link is always known. Cross-persona fraud — creating multiple personas to game trust scores or referral rewards — is detectable and actionable.

---

## Chapter 11 — Persona Creation & Verification

### Consumer Persona
Created automatically at sign-up. No additional verification required. Every Identity has exactly one Consumer persona — it cannot be deleted (only deactivated).

### Organiser Persona
Created when a person first attempts to list an event.

Minimum requirements (Open tier):
- Active Consumer persona
- Phone number verified
- City confirmed

Additional requirements (Verified Organiser):
- Full name + government ID (for payout KYC)
- Payout account linked (Mobile Money or bank)
- Agrees to Organiser Terms of Service

### Creator Personas (DJ, Artist, Photographer, MC)
Created when a person lists themselves for booking or is tagged as a performer on an event.

Requirements:
- Active Consumer persona
- Creator type selected (DJ / Artist / Photographer / MC)
- Stage name or professional name
- Portfolio or social profile link (optional but encouraged)
- Professional verification available (for fee) — adds Verified Creator badge

### Venue Manager Persona
Created when a person claims or creates a Venue entity.

Requirements:
- Active Consumer persona
- Venue entity created or claimed
- Venue verification submitted (address proof, business registration)
- Agrees to Venue Terms of Service

### Community Leader Persona
Created when a person creates a Community entity or is assigned leadership of an existing one.

Requirements:
- Active Consumer persona
- Community entity created or leadership role assigned
- Community verification (for formal institutions — churches, schools, registered associations)

### Parent Persona
Created when a person adds a Child entity to their Identity.

Requirements:
- Active Consumer persona
- Child's date of birth (for age-appropriate access control)
- Guardian relationship declaration (no document required at this stage — legal guardianship is assumed)
- For children attending events: organiser may require guardian verification at check-in

---

# Part IV — Identity Graph

---

## Chapter 12 — Relationships as First-Class Identity Components

A person's identity is not legible from their profile alone. It is legible from their relationships.

The Identity Graph is the relational layer of Volume II's Entity Graph, viewed through the lens of a specific identity. Every relationship a person holds is part of their identity — as defining as their name or their profile photo.

```
James Kiyingi
│
├── [friend_of]────────────────► Brian Okello
│                                 (8 shared events, weight: 0.73)
│
├── [spouse_of]────────────────► Sarah Nakato
│                                 (Couple entity: Kiyingi-Nakato)
│                                 (wedding: January 2020)
│
├── [parent_of]────────────────► Emma Kiyingi (Child entity)
│                                 (born: March 2021)
│
├── [member_of]────────────────► Kampala Jazz Lovers Community
│                                 (joined: 2019, role: member)
│
├── [member_of]────────────────► Makerere University Alumni (2018)
│                                 (role: member, graduation year: 2018)
│
├── [member_of]────────────────► Watoto Church Kololo
│                                 (role: choir member)
│
├── [alumni_of]────────────────► Makerere University
│                                 (2014–2018, degree: Computer Science)
│
├── [fan_of]───────────────────► Fik Fameica (Artist entity)
│                                 (5 concerts attended)
│
├── [follows]──────────────────► Club Guvnor (Venue entity)
│                                 (47 Go Live notifications received)
│
└── [creator_at]───────────────► 12 events (as DJ Blaze, Creator persona)
```

This graph, read together, tells a story that no profile field ever could.

---

## Chapter 13 — Identity Graph Queries

The Identity Graph enables questions that are impossible in a flat user model:

```sql
-- "Who in James's friend graph has attended
--  events in the same category as him?"
-- (for social recommendations)

WITH james_categories AS (
  SELECT DISTINCT e.category
  FROM gatherings e
  JOIN admission_events ae ON ae.event_id = e.id
  JOIN tickets t ON t.id = ae.ticket_id
  WHERE t.current_owner_id = :james_person_id
)
SELECT DISTINCT p.display_name, r.weight
FROM relationships r
JOIN persons p ON p.id = r.target_entity_id
JOIN admission_events ae ON ae.ticket_id IN (
  SELECT id FROM tickets WHERE current_owner_id = r.target_entity_id
)
JOIN gatherings e ON e.id = ae.event_id
WHERE r.source_entity_id = :james_person_id
  AND r.relationship_type = 'FRIEND'
  AND e.category IN (SELECT category FROM james_categories)
ORDER BY r.weight DESC;

-- ────────────────────────────────────────────────────────

-- "Which communities does James belong to that
--  have events happening this weekend?"
-- (for community-targeted discovery)

SELECT c.name, e.title, e.start_time
FROM community_memberships cm
JOIN communities c ON c.id = cm.community_id
JOIN gatherings e ON e.organiser_entity_id = c.id
WHERE cm.person_id = :james_person_id
  AND e.start_time BETWEEN now() AND now() + INTERVAL '7 days'
  AND e.status IN ('published', 'active')
ORDER BY e.start_time;

-- ────────────────────────────────────────────────────────

-- "How has James's relationship capital changed
--  over the past 12 months?"
-- (for capital model tracking)

SELECT
  DATE_TRUNC('month', de.occurred_at) AS month,
  COUNT(*) AS new_relationships,
  AVG(r.weight) AS avg_relationship_weight
FROM domain_events de
JOIN relationships r ON r.id = de.subject_entity_id
WHERE de.event_type = 'RelationshipAccepted'
  AND de.actor_entity_id = :james_identity_id
  AND de.occurred_at >= now() - INTERVAL '12 months'
GROUP BY 1
ORDER BY 1;
```

---

# Part V — Trust & Reputation

---

## Chapter 14 — Trust Architecture Overview

Trust in ZUKA operates at three levels:

**Platform Trust:** Does this entity behave reliably on the platform? (Tickets purchased, events delivered, commitments kept)

**Community Trust:** Does this entity have standing within specific communities? (Church elder, school alumni leader, neighbourhood organiser)

**Peer Trust:** Do the people who know this entity vouch for them? (Friend vouching, shared gathering history, mutual connections)

These three levels interact. High Platform Trust provides a baseline. Community Trust provides context. Peer Trust provides the warmest signal.

---

## Chapter 15 — Trust Dimensions

```typescript
TrustDimensions {

  reliability:          Float(0.0–1.0)
  // Did they show up? RSVPs kept, events delivered,
  // payments made on time.
  // Primary signal: (RSVPs attended / RSVPs made) over time.

  authenticity:         Float(0.0–1.0)
  // Are they who they say they are?
  // Primary signal: verification tier, identity documents reviewed,
  // phone number age, account age, consistency of behaviour.

  professionalism:      Float(0.0–1.0)
  // For Organiser and Creator personas only.
  // Did events match their listing? Were communications timely?
  // Primary signal: organiser reviews, creator booking ratings,
  // event delivery rate.

  safety:               Float(0.0–1.0)
  // Has this entity ever been associated with safety incidents?
  // Primary signal: reported incidents (weighted by severity),
  // dispute outcomes, moderation history.
  // Default: 1.0. Decreases on verified incidents.
  // Slowest to recover of all dimensions.

  consistency:          Float(0.0–1.0)
  // Does behaviour match declared identity over time?
  // Primary signal: event category consistency, community activity
  // regularity, relationship stability.

  community_standing:   Float(0.0–1.0)
  // What is this entity's standing within their communities?
  // Primary signal: community leadership roles,
  // tradition stewardship, peer assessments within community.
  // Community-specific — varies by community context.
}
```

### Trust Score Computation

```
overall_trust_score = weighted_average(
  reliability         × 0.25,
  authenticity        × 0.25,
  professionalism     × 0.20,   // 0.0 weight for Consumer-only personas
  safety              × 0.20,
  consistency         × 0.10,
  community_standing  × 0.10    // + weighted_by(community_membership_count)
)

Clamped to [0.0, 1.0].
Recomputed weekly for active identities.
Recomputed immediately after any TrustEvent.
```

---

## Chapter 16 — Portable Trust

Trust Capital accumulated on ZUKA should not be trapped in the platform. It should be portable — presentable as a verifiable credential outside the platform.

### Zuka Trust Certificate
A verifiable digital credential issued to any identity with:
- `overall_trust_score` ≥ 0.7
- Account age ≥ 6 months
- Minimum 10 gatherings attended or organised

**Format:**
```json
{
  "issuer": "ZUKA",
  "subject_display_name": "James K.",
  "certificate_type": "organiser_trust",
  "issued_at": "2025-06-01T00:00:00Z",
  "valid_until": "2026-06-01T00:00:00Z",
  "claims": {
    "events_organised": 24,
    "delivery_rate": 0.96,
    "avg_event_rating": 4.7,
    "trust_score": 0.84,
    "verification_tier": "standard"
  },
  "verification_url": "https://zukaapp.com/trust/verify/[token]",
  "qr_code": "[QR encoding of verification_url]"
}
```

The verification URL returns a public-facing trust summary page. The QR code on a business card, email signature, or venue proposal lets a recipient verify the credential without being on ZUKA.

This is the first step toward portable trust crossing platform boundaries.

---

# Part VI — Privacy Architecture

---

## Chapter 17 — Privacy as Identity Control

Privacy on ZUKA is not a compliance exercise. It is a fundamental feature of identity.

A person must be able to control:
- What others can see about them
- Who can find them on the platform
- What data ZUKA holds about them
- What data can be used for B2B products
- What happens to their data after they are gone

### Visibility Levels

```
PRIVATE
  Visible only to the identity holder themselves.
  Examples: exact date of birth, full legal name,
  phone number, precise home location, wallet balance,
  income bracket preference.

FAMILY
  Visible to entities linked via family relationship types
  (PARENT_CHILD, SIBLING, SPOUSE, EXTENDED_FAMILY).
  Examples: home address (for family event planning),
  family-level memories, child entity details.

FRIENDS
  Visible to entities linked via FRIEND relationship.
  Examples: attendance history (when set to friends),
  upcoming RSVPs, pulse points balance (optional).

COMMUNITY
  Visible to members of shared community entities.
  Examples: community role, community event history,
  contact info shared with a community (not globally).

ORGANISATION
  Visible to verified organisational entities
  (companies, NGOs, government bodies — for B2B features).
  Examples: aggregated demographic data (in B2B products),
  anonymised behavioural data.

PUBLIC
  Visible to anyone on or off the platform.
  Examples: display name, profile photo, public persona bio,
  public review responses, city (not neighbourhood).
```

### Per-Field Privacy Control

```typescript
PrivacySettings {

  profile_visibility:         VisibilityLevel   // default: PUBLIC
  attendance_history:         VisibilityLevel   // default: PRIVATE
  upcoming_events:            VisibilityLevel   // default: FRIENDS
  memory_archive:             VisibilityLevel   // default: PRIVATE
  community_memberships:      VisibilityLevel   // default: PUBLIC
  relationship_graph:         VisibilityLevel   // default: FRIENDS
  pulse_points_balance:       VisibilityLevel   // default: PRIVATE
  location_sharing:           LocationPrivacy   // see below
  search_discoverability:     Boolean           // can others find me by name/phone?
  co_attendance_notifications: Boolean          // "your friend is here" — opt-in
  vibe_drop_attribution:      Boolean           // default: anonymous
  data_for_b2b_products:      Boolean           // default: true (can opt out)
  data_for_recommendations:   Boolean           // default: true (can opt out)

  // Per-community overrides
  community_overrides: {
    [community_id: UUID]: {
      visibility_level:       VisibilityLevel
      share_attendance:       Boolean
      share_contact:          Boolean
    }
  }

  // Per-persona overrides
  persona_overrides: {
    [persona_id: UUID]: {
      is_linked_publicly:     Boolean   // link this persona to main identity?
      discovery_visibility:   VisibilityLevel
    }
  }
}

LocationPrivacy {
  mode:                       LocationMode
  // precise | neighbourhood | city | off
  share_with:                 VisibilityLevel
  // who can see location data
  event_checkin_gps:          Boolean
  // required for Pulse Points and crowd data
  // false = attend without location verification
  // (forfeits Pulse Points for that check-in)
  ride_location_share:        Boolean
  // for Zuka Ride safety features
}
```

### The Data Access Request

Any identity holder can, at any time, request a complete export of their data:

```
In-app: Profile → Privacy → Download My Data

Export contains:
  - Identity record (all fields they own)
  - All domain events involving them
  - All Memories they created or are tagged in
  - All tickets purchased, gifted, or received
  - All relationships (anonymised where the other party is private)
  - All community memberships
  - All capital scores and their computation history
  - Consent records (what was consented to, when, which version)
  - B2B data inclusion records (what was shared, when, to whom)

Format: JSON + human-readable PDF summary
Delivery: encrypted download link, expires in 48 hours
Processing time: up to 72 hours (target: under 2 hours for active accounts)
```

---

# Part VII — Identity Timeline

---

## Chapter 18 — The Biographical Record

Every Identity has a Timeline — a chronological record of significant events in their ZUKA life. The Timeline is not a feed. It is a biographical record.

```
James Kiyingi — Identity Timeline

2019-03-14  ● Joined ZUKA
              [First gathering attended: Blankets & Wine Kampala]

2019-06-01  ● First tradition begun
              [Annual attendance at Blankets & Wine — Occurrence 1]

2019-09-12  ● Community joined
              [Kampala Jazz Lovers]

2020-01-08  ● Life chapter transition
              [UNIVERSITY → EARLY_CAREER]

2020-01-15  ● Relationship milestone
              [Spouse relationship created: Sarah Nakato]
              [Couple entity created: Kiyingi-Nakato]

2020-02-14  ● Ceremony attended
              [Introduction Ceremony — Kiyingi-Nakato]
              [Memory created — 47 photos, 3 videos]

2021-03-22  ● Life chapter transition
              [EARLY_CAREER → PARENTHOOD]
              [Child entity created: Emma Kiyingi]

2021-06-01  ● Tradition milestone
              [Blankets & Wine — Occurrence 3 → Tradition confirmed]

2022-09-01  ● Creator persona created
              [DJ Blaze persona activated]
              [First booking: Rooftop 256, Naguru]

2025-06-19  ● Present day
```

The Timeline is:
- Automatically constructed from domain events
- Editable by the user (they can add context, notes, and chapter assignments)
- Exportable as a PDF life summary
- The basis for the Memory Vault's biographical organisation

---

## Chapter 19 — Life Chapter Model

Life Chapters are named periods that give biographical context to Memories and Gatherings.

```typescript
LifeChapter {
  chapter_id:         UUID
  identity_id:        UUID
  chapter_type:       LifeChapterType
  custom_name:        TEXT          // user-defined chapter name (optional)
  started_at:         Date
  ended_at:           Date          // null = current chapter
  description:        TEXT          // user-written chapter summary
  cover_memory_id:    UUID          // the memory that defines this chapter
  gathering_count:    Integer       // how many gatherings occurred in this chapter
  tradition_ids:      UUID[]        // traditions active during this chapter
  key_relationship_ids: UUID[]      // relationships most significant in this chapter
}

LifeChapterType enum:
  childhood
  secondary_school
  university
  early_career
  career
  partnership               // when Couple entity is created
  parenthood                // when first Child entity is linked
  senior
  legacy                    // posthumous — administered by legacy steward
  custom                    // user-defined
```

### Automatic Chapter Detection
```
Trigger events that suggest chapter transitions:

GatheringAttended where ceremony_type = 'graduation'
  → Suggest: SECONDARY_SCHOOL → UNIVERSITY
     or UNIVERSITY → EARLY_CAREER

RelationshipCreated where relationship_type = 'SPOUSE'
  → Suggest: current_chapter → PARTNERSHIP

PersonCreated (Child entity linked to this identity)
  → Suggest: current_chapter → PARENTHOOD

PersonProfileUpdated where age ≥ 65 (or retirement declared)
  → Suggest: CAREER → SENIOR

All chapter transitions are SUGGESTED, never automatic.
The user always confirms or rejects the suggestion.
```

---

# Part VIII — Memory Integration

---

## Chapter 20 — Identity as Memory Container

A person's Identity is, over time, primarily a container for Memories. The gatherings attended, the people shared with, the traditions established — these accumulate into something that becomes more valuable than any feature the platform offers.

The Memory integration with Identity works through three structures:

### The Memory Vault
A curated, organised view of all Memories attached to an Identity. Organised by:
- Life Chapter (biographical)
- Year (chronological)
- Community (social)
- Tradition (recurring)
- People (relational — "all memories with Brian")

The Vault is private by default. Individual Memories or Albums can be shared at any visibility level.

### The Gathering Archive
A complete log of every gathering attended or organised, going back to the identity creation date. Includes:
- Event name, date, venue, city
- Who else from the friend graph attended
- Vibe Drops posted
- Ticket (as a keepsake artifact)
- Memory created (if any)

The Gathering Archive is the raw material from which Memories are curated.

### The Tradition Register
A dedicated section of the Identity showing all active and historical Traditions:
- Tradition name and description
- Occurrence count and history
- Tradition strength score
- Next expected occurrence
- All gatherings that constitute this tradition
- The community or people this tradition involves

---

# Part IX — Identity Capital

---

## Chapter 21 — Capital as Identity Signal

The Capital Model (introduced in Volume II, Chapter 17) is most meaningfully experienced through Identity. Capital is not an abstract score — it is a summary of who a person is on the platform, what they have built, and what they are worth to the ecosystem.

### Capital Display
Capital scores are:
- **Private by default** — the user sees their own scores in full detail
- **Partially visible to trusted connections** — friends may see a summary (e.g., "high memory capital" without the exact score)
- **Used internally for ranking and recommendations** — never exposed as a raw score to general users
- **Used by B2B products in aggregate** — individual capital scores are never sold

### Capital Milestones
When a capital score crosses a threshold, the identity receives a milestone recognition:

```
Memory Capital milestones:
  ● First Memory created
  ● 10 gatherings attended
  ● 5-year gathering archive
  ● First Tradition established
  ● 10-year gathering archive (Legacy milestone)

Relationship Capital milestones:
  ● First friend connection
  ● 10 active connections
  ● First community joined
  ● Cross-city connections (connections in 3+ cities)

Trust Capital milestones:
  ● Phone verified
  ● Identity verified
  ● First Verified badge
  ● 100 events organised / delivered
  ● Trusted Organiser status (top 5% delivery rate)

Community Capital milestones:
  ● First community joined
  ● First community event attended
  ● Community leadership role
  ● Tradition steward
  ● Community founder
```

---

# Part X — Legacy & Succession

---

## Chapter 22 — Identity After Life

This is the most philosophically significant section of the Architecture Bible.

Most platforms have no answer for what happens when a user dies. The account is orphaned. The memories are inaccessible. The relationships are severed. The communities lose their record of the person.

ZUKA, built on the philosophy that *"memories appreciate"* and that *"communities outlive events"* (Volume I), must have a principled answer to this question from day one — even if the full feature set is years away.

**The position:** A person's gathering history, their memories, their community contributions, and their traditions are not erased when they die. They are preserved. They become Legacy Capital — part of the community's memory of who this person was.

---

## Chapter 23 — The Legacy Settings

Every active Identity can configure Legacy Settings — what happens to their ZUKA identity when they die.

```typescript
LegacySettings {

  // Who is authorised to manage the account after death
  legacy_stewards:          LegacySteward[]
  // Ordered list — primary steward first

  // What the steward can do
  steward_permissions: {
    can_memorialise:         Boolean   // convert to memorial account
    can_view_private_memories: Boolean // access private memory vault
    can_download_archive:    Boolean   // export full memory archive
    can_manage_traditions:   Boolean   // continue tradition stewardship
    can_respond_to_messages: Boolean   // respond to community condolences
    can_share_memories:      Boolean   // make memories public posthumously
  }

  // What happens to each type of content
  memory_policy: {
    public_memories:         LegacyPolicy
    // 'preserve' | 'archive' | 'delete'
    private_memories:        LegacyPolicy
    community_contributions: LegacyPolicy
    traditions:              LegacyPolicy
    // Traditions: always preserved — they belong to the community
  }

  // Successor for community roles
  community_succession: {
    [community_id: UUID]: {
      successor_person_id:   UUID      // who takes over leadership
      succession_type:       SuccessionType
      // 'immediate' | 'election' | 'dormant'
    }
  }

  // Memorial preferences
  memorial_preferences: {
    memorial_message:        TEXT      // shown on memorial profile
    profile_photo_policy:    TEXT      // 'keep' | 'remove' | 'replace_with_candle'
    contact_message_policy:  TEXT      // who can send condolence messages
  }

  // AI Twin policy (see Volume X)
  ai_twin_policy: {
    twin_active:             Boolean   // was an AI twin created?
    posthumous_access:       Boolean   // can steward interact with twin?
    twin_deactivation:       TwinDeactivationPolicy
    // 'immediate_on_death' | 'steward_controlled' | 'preserve_indefinitely'
  }

  created_at:              Timestamptz
  last_updated_at:         Timestamptz
}

LegacySteward {
  person_id:               UUID
  relationship_type:       RelationshipType
  // (SPOUSE, SIBLING, PARENT_CHILD, FRIEND)
  steward_rank:            Integer    // 1 = primary
  consent_given_at:        Timestamptz
  // The steward must consent to this role while both are living.
}
```

---

## Chapter 24 — The Memorialisation Process

When a person dies and a steward (or Zuka support, with evidence) initiates memorialisation:

```
1. VERIFICATION
   The steward provides evidence of death (death certificate,
   news report, family confirmation via secondary contact).
   Zuka support reviews within 48 hours.

2. ACCOUNT CONVERSION
   Identity status → MEMORIALISED
   Login capability removed (no one can log into the account)
   Active session (if any) terminated

3. PERSONA MANAGEMENT
   All active commercial personas suspended:
     - Organiser persona: pending events transferred to
       named successor or cancelled with full refunds
     - Creator persona: pending bookings cancelled with notice
     - Venue Manager persona: venue enters steward management
   Consumer persona: preserved as memorial record

4. MEMORY PRESERVATION
   All Memories preserved indefinitely (never aged out)
   Memory Vault accessible to steward per permissions
   Tagged memories (where person appears in others' memories)
   preserved — their presence in those gatherings is historical

5. COMMUNITY NOTIFICATIONS
   Communities the person was a member of receive a
   dignity-first notification:
   "[Display Name] will be remembered by this community."
   Community leadership succession triggered per settings.

6. TRADITION STEWARDSHIP
   Active Traditions are not cancelled.
   Tradition stewardship transferred to named successor
   or to the community entity itself.
   The Tradition carries a memorial note for future occurrences:
   "This tradition was established by [Name] in [year]."

7. MEMORIAL PROFILE
   A public memorial profile is available at the person's
   ZUKA URL. Shows:
     - Display name and profile photo
     - Memorial message (from Legacy Settings)
     - Public memories (per settings)
     - Community memberships
     - Traditions established
   Does NOT show: private information, economic data,
   trust scores, relationship graph details.

8. CONDOLENCE MESSAGES
   Community members and friends can leave condolence messages
   on the memorial profile per the contact_message_policy.
   Steward can acknowledge messages if steward_permissions allow.
```

---

## Chapter 25 — Family Archive Succession

The Family entity (Volume II) is designed to outlive individual members. When a family member dies:

- Their contributions to Family Memories are preserved
- Their role in Family Traditions is commemorated
- Family Album entries they created remain, credited to their name
- The Family entity's `founding_member_ids` field permanently records their founding role (if applicable)
- Future Family members can discover who this person was through the Family Archive

This is the **Legacy Capital** described in the Capital Model — the gathering history of an individual becoming part of the permanent cultural memory of their family and community.

---

# Part XI — Identity APIs

---

## Chapter 26 — Identity Service Architecture

The Identity system is implemented as a set of internal services that other ZUKA systems call. These services are not exposed publicly — they are the internal API surface of the Identity domain.

```
Identity Service
  ├── resolveIdentity(phone_number) → Identity
  ├── createIdentity(phone, name, dob) → Identity
  ├── getIdentity(identity_id) → Identity
  ├── updateProfile(identity_id, profile_delta) → Identity
  ├── switchPersona(identity_id, persona_id) → SessionContext
  ├── getCapabilities(identity_id, persona_id) → Capability[]
  ├── suspendIdentity(identity_id, reason) → void
  ├── initiateMemorialisation(identity_id, evidence) → void
  └── deleteIdentity(identity_id) → void

─────────────────────────────────────────────────────────────

Relationship Service
  ├── getRelationshipGraph(identity_id, depth) → Graph
  ├── addRelationship(source_id, target_id, type) → Relationship
  ├── updateRelationshipWeight(relationship_id, event) → void
  ├── getRelationships(identity_id, type?) → Relationship[]
  ├── getSharedConnections(identity_a, identity_b) → Identity[]
  ├── getCommunityMembers(community_id) → Identity[]
  └── getCoAttendees(event_id) → Identity[]

─────────────────────────────────────────────────────────────

Trust Service
  ├── getTrustScore(identity_id) → TrustProfile
  ├── getTrustDimensions(identity_id) → TrustDimensions
  ├── recordTrustEvent(event) → void
  ├── issueBadge(identity_id, badge_type) → Badge
  ├── revokeBadge(identity_id, badge_id, reason) → void
  ├── issueTrustCertificate(identity_id, type) → Certificate
  └── verifyTrustCertificate(token) → CertificateStatus

─────────────────────────────────────────────────────────────

Reputation Service
  ├── getReputation(identity_id) → ReputationProfile
  ├── submitReview(reviewer_id, subject_id, review) → Review
  ├── respondToReview(identity_id, review_id, response) → void
  ├── flagReview(review_id, reason) → void
  ├── recomputeReputation(identity_id) → ReputationProfile
  └── getOrganiserStats(identity_id) → OrganiserStats

─────────────────────────────────────────────────────────────

Privacy Service
  ├── getPrivacySettings(identity_id) → PrivacySettings
  ├── updatePrivacySettings(identity_id, delta) → PrivacySettings
  ├── checkVisibility(viewer_id, subject_id, field) → Boolean
  ├── exportData(identity_id) → DataExport
  ├── revokeConsent(identity_id, consent_type) → void
  └── getConsentHistory(identity_id) → ConsentRecord[]

─────────────────────────────────────────────────────────────

Legacy Service
  ├── getLegacySettings(identity_id) → LegacySettings
  ├── updateLegacySettings(identity_id, settings) → void
  ├── assignSteward(identity_id, steward_id) → void
  ├── initiateMemorialisation(identity_id, evidence) → void
  ├── transferCommunityLeadership(identity_id) → void
  └── archiveTraditionStewardship(identity_id) → void
```

---

## Chapter 27 — Service Interaction Patterns

The Identity services interact with other ZUKA domains through defined patterns:

```
Ticketing system → Identity Service:
  resolveIdentity(phone) → verify buyer exists
  getCapabilities(id, persona) → verify purchase allowed
  
Scanner system → Identity Service:
  resolveIdentity(bucket_token) → identify entry person
  Trust Service.getTrustScore(id) → flag low-trust entries

Memory system → Identity Service:
  getIdentity(id) → attach memories to identity
  Relationship Service.getRelationships(id) → tag friends in memories

Community system → Relationship Service:
  getCommunityMembers(community_id) → notify members
  getRelationshipGraph(id, depth=2) → community discovery

B2B system → Privacy Service:
  checkVisibility(null, subject_id, 'b2b_data') → consent check
  [Never calls Identity Service directly — always via Privacy Service]

AI system → Identity Service:
  getIdentity(id) → build context for AI twin
  [Requires explicit user opt-in — see Volume X]
```

---

# Part XII — Risks & Open Questions

---

## Chapter 28 — Identity Theft & Account Takeover

**Risk:** A bad actor gains access to a person's phone number (SIM swap, number recycling by telecom) and takes over their ZUKA identity.

**Mitigations:**
- Phone number is the authentication factor, not the identity root. The `identity_id` is the root — the phone number is a credential.
- Number changes tracked: when a phone number is changed on the account, a 24-hour cooling period prevents high-value actions (ticket transfers, payout account changes)
- Step-up verification for high-risk actions: changing payout account requires identity document re-verification
- SIM swap detection: integration with telecom APIs (where available) to detect recent SIM changes before allowing authentication
- Number recycling: a previously used phone number that appears as a new registration is flagged for manual review if the previous identity had significant capital

---

## Chapter 29 — Relationship Verification

**Risk:** Relationships are self-declared. Someone can claim to be a family member of a person without verification.

**Current position:** Most relationship types are trust-based (mutual consent required for FRIEND, SPOUSE). Family relationships (SIBLING, PARENT_CHILD, EXTENDED_FAMILY) are declared by one party and confirmed by the other.

**Open question:** For high-stakes relationships (guardianship of a Child entity, legacy steward designation), should document verification be required?

**Current proposal:** Guardian relationship for Child entities: declared, with a prompt to the other party if they are on the platform. Formal legal guardianship documents optional but not required at platform level. Legal liability clause in Terms of Service.

→ Requires legal counsel per market before implementation.

---

## Chapter 30 — Reputation Abuse

**Risk:** Coordinated positive reviews (review farming), coordinated negative reviews (reputation attacks), and paid review schemes.

**Detection signals:**
- Multiple reviews from the same network (IP, device fingerprint) in short succession
- Reviewers who have never attended an event at the reviewed venue
- Review patterns that differ significantly from organic baseline (sudden spike in 5-star or 1-star reviews)
- New accounts with no gathering history submitting reviews

**Response:**
- Flagged reviews held for human review before publication
- Confirmed coordinated campaigns: reviews suppressed, accounts investigated
- Organiser notified of suspected attack (not confirmed — investigation ongoing)
- Severe cases: Zuka Reputation Alert badge applied pending investigation

→ Detailed detection rules deferred to Volume VIII — Trust Architecture

---

## Chapter 31 — Privacy Conflicts

**Risk:** Two people with a shared memory disagree about its privacy level.

**Use case:** A person attends an event and is tagged in a Vibe Drop by another attendee. They don't want that Vibe Drop to be public.

**Resolution model:**
- Any person tagged in a Memory can remove their tag at any time
- Removing a tag does not remove the Memory — only the tag
- If the Memory is the person themselves (they created it), they control the privacy
- If they are tagged in someone else's Memory, they can: remove their tag, or flag for review if they believe the Memory violates their privacy

→ Content policy and takedown procedure deferred to Volume IX — Content & Moderation Architecture

---

## Chapter 32 — Family Ownership Disputes

**Risk:** Multiple family members claim ownership of Family entity memories or tradition stewardship after a death.

**Use case:** A matriarch dies. Two adult children both claim to be the rightful legacy steward of the Family entity.

**Current proposal:**
- Primary legacy steward is designated in Legacy Settings during the person's lifetime
- If no steward is designated, succession defaults to: SPOUSE → eldest PARENT_CHILD relationship → eldest SIBLING relationship
- Disputes are resolved by Zuka support through a formal evidence process
- In unresolvable disputes, the Family entity enters a READ_ONLY state — memories preserved, no new activity — until the dispute is resolved externally

→ Formal dispute resolution protocol deferred to Volume XII — Legal & Governance Architecture

---

## Chapter 33 — Succession Governance

**Open questions for Community entities:**

1. What happens when a Community Leader persona holder dies and no succession plan exists?
2. What authority does a legacy steward have over a Community entity that has other active members?
3. Can a community vote to override a designated succession?
4. What is the minimum viable community to sustain a Community entity? (How many members must remain active?)

**Current position:** These questions require community governance design — not just technical architecture. The Community entity model must include governance rules that are set at community creation and modifiable by active leadership.

→ Deferred to Volume V — Community Architecture

---

## Chapter 34 — Cross-Community Trust Portability

**Open question:** A person with high trust scores in one community (e.g., a trusted church events organiser) moves to a new city. How much of their trust transfers to their new community context?

**Current position:** Global trust score carries across communities. Community-specific trust dimensions must be rebuilt in the new context. This is by design — community standing is earned locally, not transferred automatically.

**Tension:** This creates a cold-start problem for established people in new markets. A solution may involve a "trust introduction" — a trusted member of the new community vouching for the incoming person — as a trust bootstrap mechanism.

→ Partially specified. Deferred to Volume VIII — Trust Architecture

---

# Volume III Summary

```
Identity Philosophy:
  Identity = continuity through time, not an account
  Five-layer identity stack
  African relational personhood model

Universal Identity Model:
  13-component Identity Record
  6 identity status states
  Full lifecycle from registration to legacy

Multi-Persona Architecture:
  10+ persona types
  Seamless persona switching
  Cross-persona privacy protection
  Persona-specific capabilities, trust, and economic identity

Identity Graph:
  Relationships as identity components
  Graph queries for social recommendation
  Biographical graph traversal

Trust & Reputation:
  6 trust dimensions
  Weighted trust score computation
  Portable Trust Certificate
  Persona-level and global trust layers

Privacy Architecture:
  6 visibility levels
  Per-field privacy control
  Community and persona overrides
  Full data export and deletion

Identity Timeline:
  Biographical record construction
  Life Chapter model with auto-detection
  9 standard life chapter types + custom

Memory Integration:
  Memory Vault structure
  Gathering Archive
  Tradition Register

Identity Capital:
  7 capital types tracked per identity
  Milestone recognition system
  Capital display and sharing rules

Legacy & Succession:
  Legacy Settings model
  Memorialisation process (8 steps)
  Family Archive succession
  AI Twin policy

Identity APIs:
  6 internal service domains
  40+ service methods
  Cross-domain interaction patterns

Risks & Open Questions (7):
  Identity theft · Relationship verification
  Reputation abuse · Privacy conflicts
  Family ownership disputes · Succession governance
  Cross-community trust portability
```

---

# What Volume IV Must Address

Volume IV — Memory Architecture must resolve:

1. **The Memory Creation Flow** — how a gathering becomes a Memory: the capture, curation, and contextualisation process
2. **The Memory Vault UX** — how the Memory Vault is navigated, searched, and shared
3. **Vibe Drop Architecture** — the full technical specification for 15-second in-event media capture and aggregation
4. **Photo and Video Storage** — content delivery, compression, long-term archival, and cost model for media at scale
5. **Memory Sharing Model** — how Memories are shared, who can see what, and how sharing affects privacy
6. **AI Memory Features** — automated memory summaries, highlight reels, "on this day" features, and memory-based recommendations
7. **The Photo Tagging System** — how people are tagged in memories, consent for tagging, tag removal, and facial recognition policy (a sensitive topic that requires careful ethical treatment)
8. **Tradition commemoration UX** — how Traditions are displayed, celebrated, and carried forward across occurrences
9. **The Legacy Archive** — how the Memory Vault transitions to memorial state and what stewards can access
10. **Media rights and attribution** — who owns a photo taken by a Photographer Creator at an event they were booked for

---

*Volume III is canonical. Extensions require architecture review.*
*Contradictions with this volume must be resolved before implementation.*

---

> **ZUKA Architecture Bible**
> Volume III — Identity Architecture
> Built in Kampala. Built for Africa. Built for the world.
