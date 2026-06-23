# ZUKA Architecture Bible
# Volume IV — Gatherings Engine

> **Series:** ZUKA Architecture Bible
> **Volume:** IV of N
> **Status:** Canonical — operational heart of the platform
> **Depends on:** Volume I (Vision), Volume II (Domain Model), Volume III (Identity)
> **Required by:** Volume V (Memory), Volume VI (Ticketing), Volume VII (Streaming),
>                  Volume VIII (Trust), Volume IX (Opportunity), Volume X (AI),
>                  Volume XI (Data & Intelligence), all product specifications

---

# Preface

Volume I is the vision.
Volume II is the DNA.
Volume III is the soul.
Volume IV is the beating heart.

Everything in ZUKA — every feature, every data model, every revenue stream, every intelligence product — exists in service of one thing:

**Humans gathering.**

Not events. Not tickets. Not content. Not engagement metrics.

Gatherings.

The moment people come together — in a church hall or a football stadium, at a family dinner table or a rooftop nightclub, at a village meeting or a music festival — something is produced that no other moment can produce:

Experience. Memory. Relationship. Community. Tradition. Identity. Legacy.

Most platforms model the transaction around a gathering:

```
User → Event → Ticket
```

ZUKA models the gathering itself — and everything it produces:

```
People
  ↓
Gathering
  ↓
Experience
  ↓
Memory
  ↓
Relationship
  ↓
Community
  ↓
Tradition
  ↓
Identity
  ↓
Legacy
```

This volume specifies the Gatherings Engine — the operational system that powers every gathering on ZUKA, from a birthday party to a continental music festival.

---

# Part I — The Gathering Thesis

---

## Chapter 1 — The Fundamental Unit

A gathering is not an event.

An **event** is a scheduled occurrence with a time, a place, and an agenda. It is a logistical construct.

A **gathering** is the human experience of coming together with shared purpose. It is a social and emotional construct. It may be scheduled or spontaneous. It may be planned for months or activated in minutes. It may be attended by two people or two hundred thousand. It may be joyful or solemn, structured or free-form, public or deeply private.

The distinction matters architecturally because:

- An event can be cancelled. The data disappears.
- A gathering, once it has occurred, is permanent. It becomes memory.

- An event has attendees. They are passive.
- A gathering has participants. They are active.

- An event has a start time and an end time.
- A gathering has a lifecycle — from the idea that sparked it to the tradition it may eventually become.

ZUKA is built on the gathering model, not the event model.
The difference is not semantic. It is the difference between a ticketing app and a civilisational platform.

---

## Chapter 2 — What Gatherings Produce

Every gathering — regardless of type, size, or context — produces a set of outputs. These outputs are not features of the platform. They are natural products of humans coming together. ZUKA's job is to capture, preserve, and amplify them.

```
GATHERING OUTPUTS

Immediate outputs (during the gathering):
  · Presence (who was there)
  · Experience (what happened)
  · Connection (who met whom)
  · Content (photos, videos, audio)
  · Economic activity (spending, revenue, exchange)

Short-term outputs (days to weeks after):
  · Memories (curated recollections)
  · Relationships (connections strengthened or formed)
  · Stories (narrative accounts)
  · Reviews and ratings (trust signals)
  · Opportunities (connections that became possibilities)

Long-term outputs (months to years after):
  · Traditions (recurring gathering patterns)
  · Community bonds (relationships that sustained)
  · Trust capital (reputation built through reliability)
  · Legacy (the gathering's place in someone's life story)
  · Identity (the gathering's role in shaping who someone is)
```

Most platforms capture only the immediate outputs — and only some of them.
ZUKA is architected to capture all of them, across all time horizons.

---

## Chapter 3 — The Gathering as Civilisational Atom

Volume I stated it plainly: *"Gatherings are civilization's atoms."*

Volume IV operationalises this claim.

The taxonomy of gatherings in Chapter 6 covers the full spectrum of human coordination — from the most intimate (a date, a family dinner) to the most communal (a village meeting, a national festival), from the most joyful (a wedding, a birthday) to the most solemn (a funeral, a memorial).

By supporting the full spectrum, ZUKA does not position itself as an entertainment app. It positions itself as infrastructure for human life. This is the 100-year position from Volume I.

The product implication: ZUKA must never make a funeral feel out of place. It must never make a church service feel like a nightclub listing. It must never make a family reunion feel like a ticketing workflow. The gathering type determines the tone, the features available, the privacy defaults, and the economic model. Nothing should feel generic.

---

# Part II — Universal Gathering Model

---

## Chapter 4 — The Gathering Record

The Gathering Record is the canonical data structure for every gathering on ZUKA. It is the most important entity in the system — the one that connects people to experiences, experiences to memories, and memories to legacy.

```typescript
Gathering {

  // ── IDENTITY ──────────────────────────────────────────────
  gathering_id:         UUID
  // Globally unique. Immutable. Never recycled.
  // A gathering that occurred is a permanent historical fact.

  gathering_type:       GatheringType
  // From the taxonomy in Chapter 6. Immutable after first participant
  // joins — the type of gathering cannot be changed once people
  // have committed to attending.

  gathering_family:     GatheringFamily
  // The top-level category: personal | family | faith | educational |
  // social | community | entertainment | sports | professional
  // Derived from gathering_type. Used for privacy defaults,
  // feature unlocks, and B2B categorisation.

  // ── PURPOSE ───────────────────────────────────────────────
  title:                TEXT
  // The human-readable name of the gathering.
  // Required. 3–120 characters.

  purpose:              TEXT
  // What this gathering is for. The "why."
  // Optional but strongly encouraged for non-public gatherings.
  // 10–500 characters.

  description:          TEXT
  // Longer form details. Optional.
  // Displayed publicly or to invitees depending on visibility.

  tags:                 Tag[]
  // Semantic tags from Volume II's Universal Tag System.
  // Category, vibe, culture, emotion, crowd tags.

  // ── HOST ──────────────────────────────────────────────────
  host: {
    host_entity_id:     UUID
    // The primary host. Always a Person or Organisation entity.
    host_persona_id:    UUID
    // Which persona is hosting (organiser, community_leader, etc.)

    co_host_ids:        UUID[]
    // Additional hosts with management capabilities but not primary.

    host_display:       HostDisplayConfig
    // How the host is presented to participants:
    // { show_name: true, show_photo: true, show_contact: false }
    // Varies by gathering type and host preference.
  }

  // ── PARTICIPANTS ───────────────────────────────────────────
  participants: {
    total_invited:        Integer
    total_confirmed:      Integer
    total_attended:       Integer        // computed from check-ins
    total_capacity:       Integer        // null = no limit
    waitlist_count:       Integer

    participation_config: {
      rsvp_required:      Boolean
      approval_required:  Boolean        // host must approve each RSVP
      plus_ones_allowed:  Boolean
      max_plus_ones:      Integer
      child_policy:       ChildPolicy
      // 'welcome' | 'adults_only' | 'family_friendly' | 'with_guardian'
      age_restriction:    AgeRestriction // null | { min: 18 } | { min: 21 }
    }
  }

  // ── COMMUNITIES ───────────────────────────────────────────
  communities: {
    primary_community_id: UUID
    // The community this gathering belongs to (if any).
    // null for purely personal gatherings.

    invited_community_ids: UUID[]
    // Communities whose members are invited.
    // A church can invite its congregation. A school can
    // invite its alumni. A neighbourhood can invite residents.

    open_to_public:       Boolean
    // If true: discoverable in the public feed.
    // If false: invite-only — not discoverable.
  }

  // ── VENUE ─────────────────────────────────────────────────
  venue: {
    venue_entity_id:      UUID           // null for virtual or TBD
    venue_name:           TEXT           // display name (may differ from entity name)
    address:              TEXT
    location_pin:         PostGIS_Point
    city_id:              UUID
    district_id:          UUID
    neighbourhood_id:     UUID

    is_virtual:           Boolean        // online-only gathering
    virtual_link:         TEXT           // encrypted — only shown to confirmed participants
    is_hybrid:            Boolean        // both in-person and virtual

    // For private gatherings:
    location_reveal_policy: LocationRevealPolicy
    // 'always_visible' | 'on_confirmation' | 'on_host_approval' | 'manual'
    // Protects the privacy of home addresses for personal gatherings.
  }

  // ── TIMELINE ──────────────────────────────────────────────
  timeline: {
    gathering_mode:       GatheringMode
    // 'scheduled' | 'go_live' | 'recurring' | 'tentative'

    // For scheduled gatherings:
    start_time:           Timestamptz
    end_time:             Timestamptz
    timezone:             TEXT
    doors_open_at:        Timestamptz    // when check-in opens
    check_in_closes_at:   Timestamptz    // when check-in window ends

    // For Go Live gatherings:
    went_live_at:         Timestamptz    // when host activated Go Live
    went_offline_at:      Timestamptz    // when host deactivated

    // For recurring gatherings:
    recurrence_rule:      RRULE          // iCal RRULE format
    recurrence_end:       Timestamptz    // null = indefinite
    parent_gathering_id:  UUID           // links to first occurrence
    tradition_id:         UUID           // set when tradition is detected

    // Actual timing (filled in retrospectively):
    actual_start_time:    Timestamptz
    actual_end_time:      Timestamptz
  }

  // ── MEMORIES ──────────────────────────────────────────────
  memories: {
    photo_count:          Integer        // computed
    video_count:          Integer        // computed
    vibe_drop_count:      Integer        // computed
    story_count:          Integer        // computed
    memory_count:         Integer        // computed (curated memories)
    album_ids:            UUID[]
    highlight_reel_id:    UUID           // auto-generated (future)
    memory_privacy:       VisibilityLevel
    // Default memory visibility for this gathering.
    // Can be overridden per-memory.
  }

  // ── RITUALS ───────────────────────────────────────────────
  rituals: {
    is_recurring:         Boolean
    tradition_id:         UUID           // set when tradition formalised
    ritual_elements:      RitualElement[]
    // Named moments within the gathering that are expected
    // to repeat across occurrences.
    // Examples: "Opening prayer", "Birthday cake", "Group photo",
    //           "Closing hymn", "Annual trophy presentation"
  }

  // ── ECONOMICS ─────────────────────────────────────────────
  economics: {
    economic_model:       EconomicModel
    // 'free' | 'ticketed' | 'contribution' | 'donation' |
    // 'subscription' | 'vendor_marketplace' | 'mixed'

    ticket_config_id:     UUID           // null if not ticketed
    total_revenue:        Decimal        // computed from orders
    currency_code:        CurrencyCode
    contribution_target:  Decimal        // for fundraisers
    contribution_total:   Decimal        // computed
    vendor_count:         Integer        // for markets and festivals
    sponsorship_ids:      UUID[]

    payout_config: {
      organiser_payout_account_id: UUID
      payout_timing:      PayoutTiming
      // 'immediate' | 'post_event_48h' | 'post_event_7d'
    }
  }

  // ── VISIBILITY ────────────────────────────────────────────
  visibility: {
    discovery_level:      DiscoveryLevel
    // 'public' — in the Zuka feed, searchable, on the heat map
    // 'community' — visible to community members only
    // 'invited' — only visible to explicitly invited participants
    // 'private' — host-only during planning; no external visibility

    rsvp_visibility:      VisibilityLevel
    // Who can see who else is attending?
    // 'participants_only' | 'public' | 'host_only'

    participant_list_visibility: VisibilityLevel
    attendee_count_visibility:   VisibilityLevel
  }

  // ── STATUS ────────────────────────────────────────────────
  status:               GatheringStatus
  // See Chapter 5 — Gathering Lifecycle

  // ── INTELLIGENCE ──────────────────────────────────────────
  embedding:            Vector(1536)
  // Semantic embedding of the gathering — for recommendations,
  // tradition detection, and similarity queries.

  intelligence: {
    predicted_attendance: Integer        // ML model (future)
    tradition_probability: Float         // likelihood this becomes a tradition
    community_impact_score: Float        // estimated community capital contribution
    opportunity_density:  Float          // likelihood of meaningful connections
  }

  // ── AUDIT ─────────────────────────────────────────────────
  created_at:           Timestamptz
  updated_at:           Timestamptz
  created_by:           UUID            // identity_id of creator
  version:              Integer
}
```

---

## Chapter 5 — Gathering Status Lifecycle

A gathering's status reflects its position in the lifecycle — from conception to legacy.

```
IDEA
  The gathering has been imagined but not yet planned.
  Exists only as a draft in the host's planning tools.
  Not visible to anyone outside the host.
  │
  ↓
PLANNING
  Active preparation underway.
  Invitations being drafted. Venue being arranged.
  Still private — host controls who can see progress.
  │
  ↓
INVITATION_SENT
  Invitations dispatched to participants or communities.
  Visible to invitees. RSVP window open.
  Not yet in public feed (unless discovery_level = 'public').
  │
  ├──► PUBLISHED (if public gathering)
  │     Visible in the Zuka feed and on the heat map.
  │     Ticket sales open. Discovery enabled.
  │
  ↓
CONFIRMED
  Minimum confirmation threshold met.
  Gathering is definitely happening.
  Host notified. Final preparations triggered.
  │
  ↓
ACTIVE
  Within 24 hours of start time.
  Check-in window approaching.
  "Starting Soon" in the feed.
  │
  ↓
LIVE
  Gathering is happening right now.
  Check-in window open. Go Live activated (if applicable).
  Real-time signals active: crowd meter, audio stream, Vibe Drops.
  Heat map pin pulsing.
  │
  ↓
ENDED
  Gathering has concluded. Check-in window closed.
  Memory creation window opens.
  Post-gathering intelligence computed.
  │
  ↓
MEMORY_PHASE
  The gathering is over but it is still being documented.
  Photos uploading. Stories being written.
  Memory curation active. Album being built.
  Reviews open for 7 days.
  │
  ↓
ARCHIVED
  Gathering is fully documented and archived.
  Permanent historical record. Memories preserved.
  Tradition detection algorithm runs.
  Community and relationship graphs updated.
  │
  ↓ (if recurring pattern detected)
TRADITION_SEED
  This gathering has been identified as a recurring tradition.
  Host prompted to formalise the tradition.
  Links to Tradition entity.

── PARALLEL STATES (can occur at any stage before LIVE) ──────

CANCELLED
  Gathering will not occur. Participants notified.
  If ticketed: automatic refund triggered.
  Gathering record preserved but marked cancelled.
  Cancellation reason recorded (required field).
  Does not enter Memory Phase — but is preserved for
  trust/reputation signals (organiser reliability).

POSTPONED
  Gathering rescheduled. New date communicated.
  Tickets remain valid. Participants notified.
  Original date preserved in timeline history.
```

---

# Part III — Gathering Taxonomy

---

## Chapter 6 — Complete Gathering Type Registry

Every gathering type has:
- A canonical type identifier
- Default privacy settings
- Default economic model
- Available features (which platform features make sense for this type)
- Tone configuration (how the platform communicates about this gathering type)
- Special considerations

---

### Family 1 — Personal Gatherings

Intimate gatherings centred on an individual or small group. Typically private. High emotional significance.

```
BIRTHDAY
  Privacy default:    INVITED (private)
  Economic model:     FREE or CONTRIBUTION
  Available features: RSVP, invitations, photos, albums,
                      gift wishlist (future), memories
  Tone:               Celebratory, warm, personal
  Special:            Age milestone detection
                      (18, 21, 30, 40, 50 → milestone memory treatment)
  Tradition signal:   Annual recurrence → auto-tradition candidate

DATE
  Privacy default:    PRIVATE (host-only)
  Economic model:     FREE (platform never charges for dates)
  Available features: Planning tools only (no public features)
  Tone:               Intimate, discreet
  Special:            No public feed visibility. Ever.
                      No B2B data use for this gathering type.
                      The platform must feel like a trusted private space.

BABY_SHOWER
  Privacy default:    INVITED
  Economic model:     FREE or CONTRIBUTION
  Available features: RSVP, invitations, registry link (future),
                      photos, memories
  Tone:               Joyful, anticipatory, warm

FAMILY_DINNER
  Privacy default:    FAMILY (only linked family entities)
  Economic model:     FREE
  Available features: Invitations to family members, photos, memories
  Tone:               Warm, domestic, intimate

HOUSE_PARTY
  Privacy default:    INVITED
  Economic model:     FREE or CONTRIBUTION
  Available features: RSVP, invitations, music sync (Mode 2 — Shazam),
                      photos, Vibe Drops, memories
  Tone:               Social, casual, energetic
  Special:            Optional Go Live capability for public house parties

ANNIVERSARY
  Privacy default:    INVITED
  Economic model:     FREE or TICKETED (for formal anniversary events)
  Available features: RSVP, photos, memories, milestone treatment
  Tone:               Celebratory, romantic, significant
  Tradition signal:   Annual recurrence → strong tradition candidate
```

---

### Family 2 — Family Gatherings

Gatherings that involve the Family entity. Higher formality and broader participation than personal gatherings.

```
REUNION
  Privacy default:    COMMUNITY (family entity members)
  Economic model:     CONTRIBUTION or TICKETED
  Available features: RSVP, invitations, photos, albums, stories,
                      group planning, Family Archive integration,
                      memories, Scanner Pro
  Tone:               Celebratory, nostalgic, communal
  Tradition signal:   Annual/biennial recurrence → strong tradition
  Special:            Family entity is the primary community.
                      Reunion memories are automatically added to
                      the Family Archive.

WEDDING
  Privacy default:    INVITED
  Economic model:     FREE (for guests) — host may use contribution
                      model for certain elements
  Available features: RSVP with plus_one config, invitation management,
                      seating coordination (future), photos, albums,
                      stories, memories, ceremony-specific features
  Tone:               Formal, celebratory, solemn and joyful
  Special:            Creates or links to COUPLE entity.
                      Wedding Memory is a Tier 1 capital event —
                      highest Memory Capital weight of any gathering type.
                      Photo tagging: enhanced — guests may tag themselves.

INTRODUCTION_CEREMONY (Kwanjula / Traditional Introduction)
  Privacy default:    INVITED
  Economic model:     CONTRIBUTION
  Available features: Ceremony-specific RSVP, cultural programme
                      management, photos, albums, memories
  Tone:               Cultural, formal, celebratory
  Special:            Cultural ceremony type — cultural context tag
                      (ugandan | kenyan | etc.) is required.
                      Ceremony programme displayed to confirmed invitees.
                      This is one of the most significant gathering types
                      in the Ugandan/East African context.

FUNERAL
  Privacy default:    COMMUNITY (family and close connections)
  Economic model:     FREE
  Available features: Dignified RSVP, family notifications,
                      condolence space, photos (host-controlled),
                      memorial memories, Legacy integration
  Tone:               Solemn, dignified, supportive
  Special:            No promotional features. No advertising.
                      No "similar events" recommendations.
                      No Vibe Drops.
                      No Go Live.
                      Grief-sensitive notification language.
                      Memories created at funerals are automatically
                      linked to the deceased's memorial profile
                      (if they are a ZUKA identity).
                      This is the gathering type that most tests
                      the platform's humanity. It must be handled
                      with absolute dignity.

NAMING_CEREMONY
  Privacy default:    INVITED
  Economic model:     FREE or CONTRIBUTION
  Available features: RSVP, cultural ceremony programme,
                      photos, memories, Child entity creation link
  Tone:               Joyful, cultural, welcoming
  Special:            Links to newly created Child entity.
                      This gathering creates the child's first
                      presence in the platform's memory.

GRADUATION_CELEBRATION
  Privacy default:    INVITED
  Economic model:     FREE or CONTRIBUTION
  Available features: RSVP, invitations, photos, memories,
                      milestone treatment (life chapter transition)
  Tone:               Proud, celebratory, significant
  Special:            Triggers life chapter transition suggestion
                      (UNIVERSITY → EARLY_CAREER or
                       SECONDARY_SCHOOL → UNIVERSITY).
```

---

### Family 3 — Faith Gatherings

Gatherings organised by or for Faith community entities. Recurring, high-frequency, community-defining.

```
CHURCH_SERVICE
  Privacy default:    PUBLIC (usually) or COMMUNITY
  Economic model:     FREE (offering is separate — not platform-managed)
  Available features: Recurring event setup, community invitations,
                      sermon/programme notes (future), photos, memories,
                      attendance tracking for the congregation
  Tone:               Respectful, spiritual, communal
  Tradition signal:   Weekly recurrence → foundational tradition

PRAYER_MEETING
  Privacy default:    COMMUNITY
  Economic model:     FREE
  Available features: Community invitations, attendance,
                      notes, memories
  Tone:               Intimate, spiritual

FELLOWSHIP
  Privacy default:    COMMUNITY
  Economic model:     FREE or CONTRIBUTION (for catering etc.)
  Available features: RSVP, community invitations, photos, memories
  Tone:               Warm, communal, faith-based

CHOIR_PRACTICE
  Privacy default:    COMMUNITY (choir members only)
  Economic model:     FREE
  Available features: Attendance tracking, schedule management,
                      community invitations
  Tone:               Functional, musical, communal

CRUSADE
  Privacy default:    PUBLIC
  Economic model:     FREE
  Available features: Full public event features, large-scale RSVP,
                      Scanner Pro for crowd management, photos, memories
  Tone:               Evangelical, energetic, communal
  Special:            May involve large crowds — Festival Mode available.

RETREAT
  Privacy default:    COMMUNITY
  Economic model:     TICKETED or CONTRIBUTION
  Available features: Multi-day support, accommodation coordination
                      (future), programme management, photos, memories
  Tone:               Spiritual, reflective, restorative
```

---

### Family 4 — Educational Gatherings

Gatherings centred on learning, academic milestones, and alumni communities.

```
SCHOOL_TOUR
  Privacy default:    COMMUNITY (school community)
  Economic model:     FREE or TICKETED
  Available features: Parental permission management (Child entities),
                      attendance tracking, photos, memories
  Tone:               Educational, supervised, structured
  Special:            Child entity protections apply.
                      Guardian notification required for attendance.

GRADUATION
  Privacy default:    PUBLIC or COMMUNITY
  Economic model:     FREE or TICKETED
  Available features: Large-scale RSVP, Scanner Pro, ceremony
                      programme, photos, albums, memories,
                      milestone treatment
  Tone:               Proud, formal, celebratory
  Special:            Creates School→Alumni relationship for graduates.
                      Links to Alumni Community entity.
                      Life chapter transition triggered.

ALUMNI_REUNION
  Privacy default:    COMMUNITY (alumni entity members)
  Economic model:     TICKETED or CONTRIBUTION
  Available features: Alumni community invitations, year-group filtering,
                      Scanner Pro, photos, albums, stories, memories
  Tone:               Nostalgic, celebratory, reconnective
  Tradition signal:   Annual recurrence → strong tradition

SEMINAR
  Privacy default:    PUBLIC or COMMUNITY
  Economic model:     FREE or TICKETED
  Available features: Speaker profiles, programme schedule,
                      RSVP, attendance, photos, memories
  Tone:               Professional, educational

WORKSHOP
  Privacy default:    PUBLIC or INVITED
  Economic model:     TICKETED
  Available features: Capacity management, RSVP, materials (future),
                      attendance, photos, memories
  Tone:               Practical, skills-focused
```

---

### Family 5 — Social Gatherings

Casual, enjoyment-centred gatherings between friends and acquaintances.

```
PICNIC
  Privacy default:    INVITED or PUBLIC
  Economic model:     FREE or CONTRIBUTION
  Available features: RSVP, location (park pin), photos,
                      Vibe Drops, memories
  Tone:               Casual, outdoor, relaxed

KARAOKE_NIGHT
  Privacy default:    INVITED or PUBLIC
  Economic model:     FREE or TICKETED
  Available features: RSVP, venue, photos, Vibe Drops, memories,
                      music sync
  Tone:               Fun, social, performative

BBQ
  Privacy default:    INVITED
  Economic model:     FREE or CONTRIBUTION
  Available features: RSVP, location, photos, Vibe Drops, memories
  Tone:               Casual, social, outdoor

ROAD_TRIP
  Privacy default:    INVITED (private — shared among participants)
  Economic model:     CONTRIBUTION (fuel, accommodation splits)
  Available features: Participant group, itinerary, location sharing,
                      photos, memories
  Tone:               Adventurous, bonding, explorative
  Special:            Multi-location gathering — route-based.
                      Memory creation at multiple stops.

WATCH_PARTY
  Privacy default:    INVITED or PUBLIC
  Economic model:     FREE or TICKETED
  Available features: Sync Session (Volume VII), RSVP, venue,
                      photos, Vibe Drops, memories, atmosphere audio
  Tone:               Social, entertainment, communal
```

---

### Family 6 — Community Gatherings

Gatherings organised by community entities for community benefit. Civic, participatory, place-based.

```
COMMUNITY_CLEANUP
  Privacy default:    PUBLIC
  Economic model:     FREE
  Available features: Community invitations, attendance tracking,
                      volunteer coordination, photos, memories
  Tone:               Civic, energetic, purposeful

VILLAGE_MEETING
  Privacy default:    COMMUNITY
  Economic model:     FREE
  Available features: Community invitations, attendance,
                      agenda (future), notes, memories
  Tone:               Deliberative, communal, local

FUNDRAISER
  Privacy default:    PUBLIC or COMMUNITY
  Economic model:     DONATION or TICKETED
  Available features: Donation collection, progress tracker,
                      RSVP, photos, memories, sponsorship
  Tone:               Purposeful, emotional, communal

SACCO_MEETING
  (Savings and Credit Cooperative Organisation)
  Privacy default:    COMMUNITY (SACCO members only)
  Economic model:     FREE
  Available features: Member attendance, contribution tracking
                      (separate from ZUKA economy), notes, memories
  Tone:               Financial, serious, trustworthy
  Special:            Financial sensitivity — no B2B data use.
                      Member list is strictly private.
```

---

### Family 7 — Entertainment Gatherings

Public entertainment events. The core commercial gathering type for ZUKA's revenue model.

```
FESTIVAL
  Privacy default:    PUBLIC
  Economic model:     TICKETED (multi-tier)
  Available features: Festival Mode (Volume IV §31), multi-stage,
                      multi-day, Scanner Pro, Go Live per stage,
                      audio streaming, Vibe Drops, Live Wall,
                      artist profiles, sponsorship, photos,
                      albums, memories
  Tone:               Energetic, celebratory, electric
  Tradition signal:   Annual recurrence → flagship tradition

CONCERT
  Privacy default:    PUBLIC
  Economic model:     TICKETED
  Available features: Full ticketing, Scanner Pro, Go Live,
                      audio streaming, Vibe Drops, Live Wall,
                      artist profiles, photos, memories
  Tone:               Energetic, artistic, communal

CLUB_NIGHT
  Privacy default:    PUBLIC
  Economic model:     TICKETED or FREE (door)
  Available features: Go Live, audio streaming (board-out),
                      crowd meter, Vibe Drops, Live Wall,
                      Scanner Pro, photos, memories
  Tone:               Electric, nightlife, social
  Special:            Age restriction: 18+ default.
                      Alcohol venue protections apply.

COMEDY_SHOW
  Privacy default:    PUBLIC
  Economic model:     TICKETED
  Available features: Full ticketing, Scanner Pro, photos,
                      memories, comedian profiles
  Tone:               Entertaining, social, humorous

FASHION_SHOW
  Privacy default:    PUBLIC or INVITED (industry)
  Economic model:     TICKETED or INVITED
  Available features: Designer/brand profiles, RSVP,
                      photos, videos, memories, B2B opportunity features
  Tone:               Sophisticated, visual, creative

OPEN_MIC
  Privacy default:    PUBLIC
  Economic model:     FREE or CONTRIBUTION
  Available features: Go Live, performer sign-up (future),
                      audio streaming, photos, memories
  Tone:               Creative, supportive, communal

EXHIBITION
  Privacy default:    PUBLIC
  Economic model:     FREE or TICKETED
  Available features: Artist/creator profiles, multi-day support,
                      photos, memories, B2B opportunity features
  Tone:               Cultural, contemplative, visual
```

---

### Family 8 — Sports Gatherings

Sports events and viewing gatherings. High energy, tribal identity, tradition-rich.

```
FOOTBALL_MATCH
  Privacy default:    PUBLIC
  Economic model:     TICKETED or FREE
  Available features: Team profiles (future), score integration (future),
                      Scanner Pro, photos, memories
  Tone:               Passionate, tribal, energetic

MARATHON
  Privacy default:    PUBLIC
  Economic model:     TICKETED (registration)
  Available features: Participant registration, route (future),
                      check-in at finish line, photos, memories
  Tone:               Energetic, achievement-oriented, communal

TOURNAMENT
  Privacy default:    PUBLIC
  Economic model:     TICKETED or FREE
  Available features: Bracket management (future), team profiles,
                      Scanner Pro, photos, memories
  Tone:               Competitive, communal, seasonal
  Tradition signal:   Annual → strong tradition

WORLD_CUP_SCREENING
  Privacy default:    PUBLIC or INVITED
  Economic model:     FREE or TICKETED
  Available features: Go Live, atmosphere audio, Vibe Drops,
                      Watch Party Sync Session, photos, memories
  Tone:               Communal, tribal, electric
  Special:            Major cultural gathering moment.
                      Platform should surface all World Cup
                      screenings in a dedicated discovery mode.
```

---

### Family 9 — Professional Gatherings

Work-related gatherings. More formal. Different economic model. B2B significance.

```
CONFERENCE
  Privacy default:    PUBLIC or ORGANISATION
  Economic model:     TICKETED
  Available features: Multi-day, multi-track, speaker profiles,
                      programme schedule, Scanner Pro,
                      networking features (future), photos, memories
  Tone:               Professional, structured, informative

NETWORKING_EVENT
  Privacy default:    PUBLIC or ORGANISATION
  Economic model:     FREE or TICKETED
  Available features: RSVP, organisation profiles, opportunity
                      features, photos, memories
  Tone:               Professional, social, purposeful
  Special:            Opportunity Graph most active gathering type.
                      Connections made here have highest
                      opportunity_density score.

PRODUCT_LAUNCH
  Privacy default:    PUBLIC or INVITED (press/trade)
  Economic model:     FREE (invited)
  Available features: Brand profiles, RSVP, media features,
                      live streaming, photos, memories, B2B features
  Tone:               Professional, exciting, brand-forward

CORPORATE_RETREAT
  Privacy default:    ORGANISATION (company members only)
  Economic model:     CONTRIBUTION or ORGANISATION-PAID
  Available features: Multi-day, accommodation (future),
                      private photos, memories
  Tone:               Professional, team-building, private
  Special:            Strictly private. No public feed.
                      No B2B data use without explicit consent.
```

---

# Part IV — Gathering Lifecycle

---

## Chapter 7 — Full Lifecycle Architecture

The gathering lifecycle extends far beyond the platform norm of "event creation → attendance → done."

```
PHASE 1 — CONCEPTION
  Duration:   Hours to months before the gathering.
  
  Activities:
    · Host conceives the gathering idea
    · Initial planning using ZUKA's planning tools
    · Date, venue, and format exploration
    · Guest list drafting
    · Community consultation (for community gatherings)
    · Budget planning
    
  ZUKA role:   Discovery tools (find venues, find creators),
               planning interface, community consultation tools.
  
  Data captured:
    · Gathering draft created
    · Venue search queries (intelligence)
    · Creator browsing (opportunity signals)

──────────────────────────────────────────────────────────────

PHASE 2 — INVITATION
  Duration:   Days to weeks before the gathering.
  
  Activities:
    · Invitations sent to individuals, communities, or the public
    · RSVP management
    · Waitlist management
    · Guest communication
    · Logistics confirmation (venue, catering, performers)
    
  ZUKA role:   Invitation system, RSVP management,
               community broadcast, waitlist management,
               guest communication tools.
               
  Data captured:
    · Invitation open rates (intelligence)
    · RSVP conversion rates
    · Invited community demographics
    · Participant graph formation

──────────────────────────────────────────────────────────────

PHASE 3 — PREPARATION
  Duration:   Hours to days before the gathering.
  
  Activities:
    · Final confirmations
    · Vendor and performer briefings
    · Venue setup coordination
    · Last-minute ticket sales
    · Scanner Pro session setup
    · Staff role assignments
    
  ZUKA role:   Final participant list, Scanner Pro setup,
               staff role assignment, capacity monitoring,
               "starting soon" notification dispatch.
               
  Data captured:
    · Final confirmation numbers
    · Last-minute purchase behaviour
    · Staff readiness signals

──────────────────────────────────────────────────────────────

PHASE 4 — GATHERING (The Event Itself)
  Duration:   Minutes to days.
  
  Activities:
    · Check-in at gate (Smart Bucket / Scanner Pro)
    · Go Live activation
    · Audio/video streaming
    · Crowd meter updates
    · Vibe Drop creation
    · Live Wall aggregation
    · Real-time capacity monitoring
    · Vendor and economic activity
    · Photography and documentation
    
  ZUKA role:   Full real-time operational platform.
               This is the moment everything else builds toward.
               
  Data captured:
    · Check-in timestamps and gate data
    · Arrival curve
    · Crowd level progression
    · Audio stream listener data
    · Vibe Drop engagement
    · Economic transactions
    · Relationship formation signals (co-attendance)

──────────────────────────────────────────────────────────────

PHASE 5 — MEMORY CREATION
  Duration:   Hours to days after the gathering.
  
  Activities:
    · Photo and video upload
    · Album creation
    · Story writing
    · Memory curation
    · Tagging participants
    · Reviews and ratings
    
  ZUKA role:   Memory creation tools, album builder,
               story editor, tagging system, review prompts.
               
  Data captured:
    · Media upload velocity (engagement signal)
    · Tagging patterns (relationship signal)
    · Review content and ratings
    · Memory creation depth (capital signal)

──────────────────────────────────────────────────────────────

PHASE 6 — REFLECTION
  Duration:   Days to weeks after the gathering.
  
  Activities:
    · Organiser reviews post-event analytics
    · Participants revisit memories
    · Community discusses the gathering
    · Relationships formed are followed up
    · Opportunities identified are pursued
    
  ZUKA role:   Organiser analytics, community feed integration,
               memory sharing, connection follow-up prompts,
               opportunity matching.
               
  Data captured:
    · Analytics engagement depth
    · Memory revisit patterns
    · Post-event relationship activity
    · Opportunity conversion rates

──────────────────────────────────────────────────────────────

PHASE 7 — TRADITION FORMATION (for recurring gatherings)
  Duration:   Triggered after 2nd or 3rd occurrence.
  
  Activities:
    · ZUKA detects recurring pattern
    · Host is prompted to name and formalise the tradition
    · Tradition entity is created
    · Past occurrences are linked
    · Future occurrences are anticipated and announced
    · Community commemorates the tradition
    
  ZUKA role:   Tradition detection, Tradition entity management,
               commemoration features, anniversary recognition.
               
  Data captured:
    · Tradition strength score
    · Community reinforcement of tradition
    · Member continuity across occurrences
```

---

# Part V — Participation Architecture

---

## Chapter 8 — The Participation Model

People do not attend gatherings. They **participate** in them. The nature of participation varies enormously — and each participation type produces different data, different trust signals, and different memories.

```typescript
Participation {
  participation_id:     UUID
  gathering_id:         UUID
  participant_id:       UUID          // identity_id of participant
  persona_id:           UUID          // which persona they participated as

  participation_role:   ParticipationRole
  // See registry below

  // Invitation state
  invitation_status:    InvitationStatus
  // 'not_invited' | 'invited' | 'waitlisted' | 'confirmed' | 'declined'
  invited_at:           Timestamptz
  responded_at:         Timestamptz
  invited_by:           UUID          // who sent the invitation

  // Presence state
  presence_status:      PresenceStatus
  // See Chapter 9 — Presence Architecture

  // Check-in data
  checked_in_at:        Timestamptz
  checked_in_via:       CheckInMethod
  // 'smart_bucket' | 'manual_qr' | 'name_list' | 'supervisor_override'
  checked_in_gate:      UUID          // gate_id
  checked_out_at:       Timestamptz   // inferred from Zuka Ride request or manual

  // Participation quality
  engagement_score:     Float         // vibe drops, reactions, story mentions
  // Used for memory curation and recommendation intelligence

  // Post-gathering
  review_submitted:     Boolean
  memory_created:       Boolean
  photos_uploaded:      Integer

  // Economic
  amount_spent:         Decimal       // ticket + any other spending
  currency_code:        CurrencyCode

  created_at:           Timestamptz
  updated_at:           Timestamptz
}

ParticipationRole enum:
  host              // primary organiser — full control
  co_host           // joint organiser — management rights
  organiser         // operational organiser (may not be the host)
  attendee          // general participant
  volunteer         // non-paid supporter
  speaker           // presenter or lecturer
  performer         // artist, DJ, MC, choir, etc.
  vendor            // commercial seller at the gathering
  photographer      // official or hired documentation
  sponsor           // commercial backer
  community_leader  // representing a community at the gathering
  media             // press, content creator
  guardian          // accompanying a Child entity
  security          // physical safety role
  staff             // general event staff
  vip               // elevated access tier
```

---

# Part VI — Presence Architecture

---

## Chapter 9 — The Presence Ladder

Presence is not binary. It is a progression — from being invited to being remembered. Each step on the Presence Ladder is distinct, produces different signals, and has different implications for the gathering and for the participant's identity.

```
PRESENCE LADDER

STEP 0 — UNAWARE
  The person has no knowledge of this gathering.
  Not invited. May discover it through the feed.

STEP 1 — DISCOVERED
  The person has seen the gathering in their feed
  or been told about it. Aware but not committed.
  Signal: feed view, event detail page view.

STEP 2 — INTERESTED
  The person has saved the event or expressed interest.
  Signal: save action, share action.
  Not a commitment — but a meaningful intent signal.

STEP 3 — INVITED
  The person has received a direct invitation.
  Now within the participation record.
  Pending response.

STEP 4 — CONFIRMED
  The person has accepted the invitation / purchased a ticket.
  Committed. Expected to attend.
  Signal: RSVP confirmed, ticket purchased.

STEP 5 — ARRIVED
  The person has checked in at the gathering.
  Physical presence verified.
  Signal: Smart Bucket scan, check-in.
  This is the trust-creating moment.

STEP 6 — PARTICIPATED
  The person engaged during the gathering.
  Signal: Vibe Drop posted, reactions, interactions,
          duration of stay (inferred from Zuka Ride request timing).
  Not all arrivals participate deeply. Engagement matters.

STEP 7 — LEFT
  The person has departed.
  Signal: Zuka Ride request, check-out (if implemented),
          end of audio stream session.
  Inferred in most cases — not a required explicit action.

STEP 8 — REMEMBERED
  The person has created or been tagged in a Memory.
  The gathering is now part of their permanent record.
  This is the most significant step —
  it transforms attendance into memory.
  Signal: Memory created, photo tagged, story written,
          album contribution.

── SPECIAL PRESENCE STATES ──────────────────────────────────

REMOTE
  The person attended virtually — via stream or Sync Session.
  A valid and real form of presence.
  Creates attendance record, earns Pulse Points (reduced rate),
  can create memories, but does not count toward physical
  capacity or generate check-in trust signals.

DECLINED
  The person was invited but explicitly declined.
  Important for the host's planning. Preserved in
  participation record but does not affect the
  participant's reputation (declining is not a failure).

NO_SHOW
  The person confirmed but did not attend.
  Negative trust signal for repeated no-shows.
  One no-show: no consequence.
  3+ no-shows in 90 days: reliability dimension reduced.
  Systematic no-shows: notification to the user
  ("We noticed you've confirmed several events but not attended —
   is everything okay?")
```

---

# Part VII — Gathering Intelligence

---

## Chapter 10 — What Every Gathering Knows

Every gathering, as it occurs and after it ends, produces a body of intelligence. This intelligence serves three purposes:

1. **Operational intelligence** — real-time signals that help the host manage the gathering as it happens
2. **Participant intelligence** — signals that improve recommendations and personalisation for attendees
3. **Platform intelligence** — signals that feed the B2B data products and community understanding

```
REAL-TIME OPERATIONAL INTELLIGENCE (during the gathering)

  crowd_level:            Current density (quiet→packed→capacity)
  arrival_rate:           Participants arriving per minute
  gate_throughput:        Scans per gate per minute
  audio_stream_listeners: Current remote listener count
  vibe_drop_rate:         Drops being created per minute
  capacity_forecast:      Estimated time to full capacity
  no_show_rate:           Confirmed participants not yet arrived
  revenue_real_time:      Total economic activity to this point

──────────────────────────────────────────────────────────────

POST-GATHERING INTELLIGENCE (hours to days after)

  Attendance:
    actual_attendance       vs confirmed RSVPs (reliability signal)
    arrival_curve           (when did people actually arrive?)
    departure_distribution  (when did people leave?)
    peak_capacity_time      (when was it fullest?)
    no_show_count

  Engagement:
    vibe_drop_count         per hour of event
    audio_stream_sessions   (count, average duration)
    live_wall_views
    memory_creation_rate    (photos uploaded per attendee)
    review_rate             (reviews per 100 attendees)
    story_count

  Relationships:
    new_connections_formed  (co-attendees who connected after)
    community_crossover     (participants from multiple communities)
    creator_fan_conversions (attendees who followed performers)

  Economic:
    total_revenue
    ticket_utilisation_rate (tickets sold vs. tickets used)
    average_spend_per_attendee
    vendor_revenue_share

  Community:
    community_participants  (% from primary community)
    new_community_members   (joined community after attending)
    tradition_reinforcement (repeat attendees from past occurrences)
```

---

# Part VIII — Ritual Architecture

---

## Chapter 11 — Rituals Transform Gatherings into Traditions

A Ritual is a named, expected, recurring element within a gathering — a moment that participants know will happen, that marks the gathering as belonging to a specific tradition.

Rituals are the threads that weave individual gatherings into a tradition over time.

```typescript
RitualElement {
  ritual_element_id:    UUID
  gathering_id:         UUID
  tradition_id:         UUID          // null until tradition is formalised

  name:                 TEXT
  // The name of this ritual moment.
  // Examples: "The Birthday Song", "Opening Prayer", "Annual Trophy",
  //           "Group Photo on the Steps", "Closing Hymn", "The Toast"

  description:          TEXT
  sequence_position:    Integer       // where in the gathering this occurs
  is_required:          Boolean       // if true, gathering feels incomplete without it
  duration_minutes:     Integer       // approximate
  
  // Documentation
  typically_photographed: Boolean     // this moment is usually captured
  typically_recorded:   Boolean       // this moment is usually filmed
  
  // History
  first_occurrence_at:  Timestamptz   // when this ritual was first performed
  occurrence_count:     Integer       // how many times it has occurred
  
  tags:                 Tag[]
  // emotion:celebration, emotion:spiritual, emotion:nostalgia, etc.
}
```

### Ritual Detection
```
Ritual patterns are detected by comparing:
  · Named or tagged moments across gathering occurrences
  · Photo timestamps clustering around the same point in the event
  · Audio markers (applause, singing patterns) — future capability
  · Host-defined programme elements that repeat

When a ritual element has occurred in 3+ gatherings of the same
tradition, it is formalised as a RitualElement entity and linked
to the Tradition.

Host is notified: "We noticed the Birthday Song always happens at
your family dinners. We've added it to your Family Dinner tradition."
```

---

# Part IX — Gathering Economics

---

## Chapter 12 — Economic Models

Every gathering has economics — from the free village meeting where someone bought the chai, to the multi-day festival grossing tens of millions of shillings. ZUKA's economic infrastructure must support the full spectrum.

```
ECONOMIC MODEL REGISTRY

FREE
  No financial transactions through the platform.
  Platform captures economic intelligence only
  (estimated attendance, estimated local spend).
  Available to all gathering types.
  No commission.

TICKETED
  Paid tickets sold through the platform.
  ZUKA takes commission (5% MVP → 6.5% at scale).
  Buyer may pay convenience fee (UGX 1,000).
  Organiser payout within 48-72 hours of event.
  Full ticketing infrastructure (see Volume VI).

CONTRIBUTION
  Participants contribute a suggested amount —
  no hard ticket price.
  "Suggested contribution: UGX 10,000"
  Platform processes contributions like tickets.
  ZUKA takes commission on contributions.
  Used for: reunions, fundraisers, baby showers,
  potluck dinners, community events.

DONATION
  No price. No suggestion. Participants give what they choose.
  For: charity events, church offerings (careful — see below),
  memorial fundraisers.
  ZUKA takes no commission on donations to verified NGO entities.
  ZUKA takes standard commission on donations to unverified entities.
  
  Church offering note: Church offerings are not processed
  through the platform by default. They remain a separate
  offline tradition. ZUKA can support church fundraisers
  (building funds, specific projects) through the Donation model
  with host approval.

VENDOR_MARKETPLACE
  For markets, festivals, and events with multiple vendors.
  Individual vendors sell through the platform.
  Per-vendor commission applies.
  Host may also charge a vendor listing fee (separate from ZUKA commission).

MIXED
  Any combination of the above models.
  Example: A festival with ticketed general admission +
  donation-based backstage access + vendor marketplace.

SUBSCRIPTION
  For recurring gatherings (weekly classes, monthly club nights).
  Participants pay a recurring subscription for access.
  ZUKA takes commission on subscription revenue.
```

---

## Chapter 13 — Gathering Economic Data

```
ECONOMIC INTELLIGENCE CAPTURED PER GATHERING

Pre-event:
  ticket_revenue_by_tier    (early bird vs regular vs VIP)
  sales_velocity_curve      (tickets sold per day before event)
  price_sensitivity_signals (abandoned purchases at each price point)

During event:
  gate_revenue              (walk-in tickets if applicable)
  vendor_transactions       (if vendor marketplace active)
  total_real_time_revenue

Post-event:
  gross_revenue
  net_revenue_to_organiser  (after commissions and fees)
  no_show_economic_impact   (tickets purchased but not used)
  refund_total
  average_spend_per_attendee (estimated from ticket price + walk-in)
  vendor_revenue_by_category (future — vendor marketplace)
  sponsorship_value
```

---

# Part X — Memory Layer

---

## Chapter 14 — Memories as Gathering Outputs

Memories are not a feature of gatherings. They are the natural output of gatherings. ZUKA does not create memories — it captures and preserves them.

The Memory Layer at the gathering level works as follows:

```
GATHERING → MEMORY OUTPUTS

During the gathering:
  · Vibe Drops — 15-second micro-moments captured by attendees
  · Live Wall — aggregated real-time Vibe Drop display
  · Photos uploaded — real-time event documentation
  · Audio stream archive — if streaming was active, the audio
    session is preserved as an artifact

After the gathering:
  · Photo albums — curated collections from the event
  · Stories — narrative accounts written by participants
  · Memories — curated, emotionally significant records
  · Highlight Reel — auto-generated (future) short video summary
  · Tradition archive — if this gathering is part of a tradition,
    it is added to the tradition's historical record

Memory Privacy at the Gathering Level:
  · Host sets the default memory visibility at gathering creation
  · Personal gatherings (BIRTHDAY, FAMILY_DINNER): default PRIVATE
  · Community gatherings: default COMMUNITY
  · Public entertainment gatherings: default PUBLIC
  · Individual participants can override for their own contributions
  · Funeral gatherings: host controls all media — no individual
    overrides permitted during the sensitivity period (30 days)
```

---

# Part XI — Community Integration

---

## Chapter 15 — The Gathering-Community Flywheel

Communities and Gatherings exist in a compounding loop. This loop is the most important dynamic in ZUKA's long-term value creation.

```
COMMUNITY → GATHERING → MEMORY → TRUST → COMMUNITY

Step 1: Community Creates Gathering
  A Church entity organises a Sunday Service.
  An Alumni Community organises a Reunion.
  A Neighbourhood entity organises a Cleanup.
  
  The community is the reason the gathering exists.

Step 2: Gathering Strengthens Community
  Members who attend reinforce their community membership.
  New attendees discover the community and join.
  The gathering produces shared memories that belong to the community.
  Traditions are established that define community identity.

Step 3: Memory Deepens Belonging
  Community members have a shared historical record.
  "We were there together" is a powerful bonding statement.
  The community's Memory Archive grows richer with every gathering.

Step 4: Trust Builds Through Consistency
  Communities that gather consistently build trust.
  Members who show up reliably earn community trust capital.
  Organisers who deliver quality gatherings earn reputation.

Step 5: Trust Enables More Gathering
  Trusted communities attract more members.
  Trusted organisers attract more participants.
  The community becomes more capable of organising larger,
  more ambitious gatherings.
  The flywheel accelerates.
```

---

# Part XII — Trust Integration

---

## Chapter 16 — How Gatherings Build Trust

Trust on ZUKA is not declared. It is earned through behaviour at gatherings. Every gathering is a trust-building opportunity.

```
TRUST SIGNALS FROM GATHERINGS

Organiser Trust Signals:
  Event delivered as listed         → reliability +
  Accurate crowd level reporting    → authenticity +
  Timely participant communication  → professionalism +
  No safety incidents               → safety maintained
  Refunds processed on cancellation → reliability +
  Cancellation without notice       → reliability −−
  Fake event listing                → authenticity −−−

Attendee Trust Signals:
  RSVP confirmed → check-in         → reliability +
  RSVP confirmed → no-show          → reliability −
  Positive Vibe Drop (no flags)     → community +
  Reported Vibe Drop (offensive)    → safety −−
  Review submitted (verified)       → authenticity +

Creator Trust Signals:
  Booking completed as agreed       → professionalism +
  Performance rated highly          → reputation +
  No-show on booking                → professionalism −−−
  Rider requirements met            → reliability + (for organiser)

Venue Trust Signals:
  Events consistently held there    → reliability +
  No safety incidents               → safety maintained
  Accurate capacity reporting       → authenticity +
```

---

# Part XIII — Opportunity Integration

---

## Chapter 17 — Gatherings as Opportunity Markets

Gatherings are markets. Every time people come together with different skills, resources, and needs, opportunities are created.

ZUKA's Opportunity Engine (Volume IX) captures these opportunities naturally from gathering participation data.

```
OPPORTUNITY TYPES CREATED BY GATHERINGS

Creator → Organiser:
  A photographer attending a wedding is seen by the couple's
  friend who is planning their own event.
  Signal: Photographer (Creator entity) attended Wedding.
  Opportunity: Photography Booking enquiry.

Brand → Festival:
  A beer brand sees that a particular annual festival
  consistently attracts their target demographic.
  Signal: Festival audience demographics match brand target.
  Opportunity: Sponsorship proposal.

Student → Mentor:
  A young organiser attends a conference and meets an
  experienced event producer.
  Signal: Co-attendance + professional gathering type.
  Opportunity: Mentorship relationship.

Business → Client:
  A caterer at a corporate event is seen by another
  corporate event organiser.
  Signal: Vendor participation at Professional gathering.
  Opportunity: New vendor booking.

Community → Sponsor:
  A local NGO consistently organises well-attended community
  events. A development organisation sees the data.
  Signal: High community gathering frequency + attendance.
  Opportunity: Organisational funding / sponsorship.
```

---

# Part XIV — AI Gathering Layer

---

## Chapter 18 — Future AI Capabilities

The AI features described in this chapter are **not MVP features**. They are the destination toward which the data infrastructure we build today is pointing. Every data point captured now is training data for these future systems.

```
GATHERING COPILOT
  An AI assistant that helps a host plan any type of gathering.
  
  Capabilities:
    · Suggests venues based on gathering type, expected size,
      and host's previous gathering history
    · Recommends creators (DJs, photographers, MCs) based on
      gathering type and budget
    · Estimates attendance from RSVP conversion history
    · Suggests optimal timing based on community patterns
    · Generates invitation copy appropriate to the gathering type
    · Warns about conflicts (other gatherings same date/community)

──────────────────────────────────────────────────────────────

FAMILY COPILOT
  Specialised for Family entities.
  
  Capabilities:
    · Suggests reunion dates that maximise family member availability
    · Recommends venues that have hosted family gatherings of similar size
    · Plans the gathering programme based on family gathering history
    · Detects traditions and suggests new tradition-worthy moments
    · Helps the family steward build the Family Archive

──────────────────────────────────────────────────────────────

CHURCH COPILOT
  Specialised for Church/Faith Community entities.
  
  Capabilities:
    · Suggests retreat venues and programmes
    · Plans the calendar of church gatherings
    · Manages recurring service schedules
    · Tracks congregation attendance patterns
    · Suggests outreach events based on community demographics

──────────────────────────────────────────────────────────────

ORGANISER COPILOT
  Specialised for Organiser persona holders.
  
  Capabilities:
    · Forecasts attendance from RSVP curves and historical patterns
    · Recommends optimal ticket pricing for maximum revenue
    · Suggests the ideal event start time for the target audience
    · Identifies the marketing channels that drove most conversions
    · Flags risk signals (low RSVP conversion, unusual cancellation rate)
    · Generates post-event summary reports automatically

──────────────────────────────────────────────────────────────

COMMUNITY COPILOT
  Specialised for Community entities and community leaders.
  
  Capabilities:
    · Suggests new traditions based on gathering history patterns
    · Identifies community members at risk of disengagement
      (haven't attended in X months)
    · Recommends cross-community collaborations
    · Plans the community's annual gathering calendar
    · Generates community health reports for leadership
```

---

# Part XV — Gathering Capital

---

## Chapter 19 — Capital Generated by Gatherings

Every gathering contributes to the capital accounts of its participants, host, and community. Capital is not abstract — it is computed from gathering participation data.

```
CAPITAL CONTRIBUTIONS PER GATHERING

Memory Capital (participant):
  Attending the gathering:          + base amount (by gathering type)
  Creating a Memory:                + significant bonus
  Writing a Story:                  + bonus
  Being part of a Tradition:        + tradition_strength × bonus

Relationship Capital (participant):
  Meeting a new connection:         + per new relationship formed
  Shared gathering with existing:   + weight reinforcement
  Attending with family:            + family relationship reinforcement

Trust Capital (organiser):
  Event delivered as listed:        + reliability
  High attendee satisfaction:       + reputation
  Repeat gathering (tradition):     + consistency

Community Capital (community):
  Community gathering held:         + gathering frequency
  High attendance:                  + community strength
  New members joined post-event:    + growth signal
  Tradition reinforced:             + tradition_strength

Experience Capital (participant):
  New gathering type attended:      + diversity bonus
  New city/neighbourhood:           + geographic range bonus
  Rare or significant event:        + rarity bonus

Opportunity Capital (creator/organiser):
  Booking completed:                + professional experience
  High booking rating:              + reputation
  New client relationship:          + opportunity network growth
```

---

# Part XVI — Gathering Network Effects

---

## Chapter 20 — The Core Flywheel

The network effects of ZUKA operate through gatherings. More gatherings create more of everything the platform needs to grow.

```
THE ZUKA GATHERING FLYWHEEL

More Gatherings
  ↓
More Participants Onboarded
  (gift ticketing, friend invitations, community broadcasts)
  ↓
More Memories Created
  (the permanent record that keeps users returning)
  ↓
More Relationships Formed
  (the social graph that makes the platform irreplaceable)
  ↓
More Communities Active
  (the communities that organise more gatherings)
  ↓
More Traditions Established
  (the recurring gatherings that compound community strength)
  ↓
More Trust Accumulated
  (the reputation that makes the platform safe and credible)
  ↓
More Opportunities Created
  (the economic activity that sustains creators and organisers)
  ↓
MORE GATHERINGS

Each revolution of the flywheel is faster than the last.
Each gathering added compounds all the others.
```

---

## Chapter 21 — Cross-Gathering Network Effects

The gathering flywheel is amplified by cross-gathering effects — where one gathering directly creates the conditions for another:

```
CROSS-GATHERING EFFECTS

A wedding creates:
  → A family reunion (the two families now know each other)
  → A photographer's portfolio (booked for more weddings)
  → A DJ's reputation (booked for more events)
  → A venue's track record (chosen for future weddings)
  → A tradition (annual anniversary gathering)

A festival creates:
  → Fan-artist relationships (fans become future concert attendees)
  → Artist-organiser relationships (booked for future events)
  → Brand-community relationships (sponsors find their audience)
  → New community formation (people who met at the festival)
  → A tradition (returning attendees become the festival's identity)

A church retreat creates:
  → Strengthened community bonds (members who gathered return)
  → A new tradition (annual retreat becomes expected)
  → Leadership relationships (who organised it earns respect)
  → A memory archive (the church's historical record grows)
```

---

# Part XVII — MVP Scope

---

## Chapter 22 — What to Build First

The Gatherings Engine is the most feature-rich domain in ZUKA. The MVP must resist the temptation to build everything. The rule: **build only what is necessary to prove the three core theses.**

```
THESIS 1: A venue can go live and be discoverable in 60 seconds.
THESIS 2: A user can find something happening near them with zero friction.
THESIS 3: A ticket can be bought in under 3 taps.

MVP GATHERING FEATURES (build these):

  ✅ Gathering creation
     — Basic fields: title, type, date, venue, capacity
     — Ticket configuration (free or paid)
     — Go Live mode (instant activation)
     — Public vs. private visibility setting

  ✅ RSVP
     — Confirm/decline
     — Waitlist (basic)
     — Guest count for host

  ✅ Invitations
     — Share link (WhatsApp-optimised)
     — Community broadcast (to followed communities)
     — Phone number invitation

  ✅ Communities (basic)
     — Create a community
     — Join a community
     — Community event broadcast

  ✅ Photos (basic)
     — Upload during/after gathering
     — Album creation (manual)

  ✅ Check-ins
     — Smart Bucket QR
     — GPS proximity verification

  ✅ Event discovery
     — Public feed (Live Now / Starting Soon / Tonight)
     — Category filter
     — Map view

  ✅ Basic Memories
     — Photo album
     — Check-in record (permanent)
```

```
CAPTURE DATA FOR FUTURE (collect from day one, don't surface yet):

  📊 Participation history (every check-in, every RSVP)
  📊 Relationship formation (co-attendance pairs)
  📊 Community engagement patterns
  📊 Presence ladder progression
  📊 Memory creation depth
  📊 Ritual indicators (recurring gathering patterns)
  📊 Economic patterns (price points, purchase timing)
  📊 Geographic patterns (where people travel from)
```

```
DO NOT BUILD YET:

  ❌ AI Copilots (any of them)
  ❌ Ritual detection (collect data, don't process yet)
  ❌ Opportunity Engine (collect signals, don't surface yet)
  ❌ Capital Models (compute internally, don't display yet)
  ❌ City Intelligence (collect, don't sell yet)
  ❌ Highlight Reels (future AI feature)
  ❌ Vendor Marketplace (Phase 2)
  ❌ Multi-day Festival Mode (Phase 2)
  ❌ Virtual/Hybrid gathering support (Phase 2)
  ❌ Funeral-specific features (Phase 2 — requires careful design)
```

---

# Part XVIII — Open Questions

---

## Chapter 23 — Private Gathering Privacy Model

**Question:** How are fully private gatherings handled — gatherings where the host does not want ZUKA to know the details?

**Use cases:**
- A date (already flagged as maximum-privacy in the taxonomy)
- A domestic violence support group gathering
- A political organising meeting in a sensitive context
- A corporate gathering with NDA requirements

**Current position:**
Private gatherings (discovery_level = 'private') are:
- Not discoverable in the feed
- Not included in B2B data products
- Location data not stored for private gatherings where location_reveal_policy = 'manual'
- Participant list never shared, even in aggregate

The platform must make a clear, legally sound commitment about private gathering data.

→ Deferred to Volume XII — Legal & Governance Architecture

---

## Chapter 24 — Family Permission Management

**Question:** How are permissions managed for Family gatherings that involve Child entities?

**Use cases:**
- A school tour that involves children whose parents must consent to photography
- A family reunion where some family members don't want to be photographed
- A naming ceremony where the parents control all media

**Current position:**
- Child entities cannot be tagged in public photos without guardian consent
- Guardian consent is managed through the Parent persona
- Family gathering privacy defaults protect minor participants

→ Deferred to Volume III extension — Child Identity & Guardian Model

---

## Chapter 25 — Funeral Gathering Architecture

**Question:** How should funerals be represented? What features are appropriate? What tone must the platform take?

**Current position (partial):**
- Funeral is a distinct gathering type with strict feature restrictions
- No Go Live, no Vibe Drops, no advertising adjacency
- Host controls all media — no individual overrides for 30 days
- Grief-sensitive notification language required
- Deceased's memorial profile integration

**Open questions:**
- How does ZUKA handle the dual nature of a funeral — grief for the living AND celebration of a life well-lived?
- How are memorial contributions (harambee/fundraising at a funeral) handled economically?
- What happens to a gathering that was planned as a celebration and unexpectedly becomes a memorial?

→ Requires specialist input. Deferred to a dedicated Funeral & Memorial Architecture supplement.

---

## Chapter 26 — Cultural Ceremony Modelling

**Question:** How are cultural ceremonies — kwanjula, dowry ceremonies, circumcision ceremonies, traditional coronations — modelled accurately?

**The challenge:**
These ceremonies have deep cultural specificity. A generic "ceremony" type is not sufficient. But creating a distinct type for every cultural variation across Africa is also impractical.

**Current proposal:**
- Use the INTRODUCTION_CEREMONY and CEREMONY types as the primary types
- Require a `cultural_tradition` tag (using the culture namespace from Volume II)
- Allow cultural ceremony templates — pre-configured programme elements that match common ceremony structures
- Community entities can define their own ceremony templates for their cultural context

**Open:**
- Who validates cultural accuracy of templates?
- How are ceremonies that cross cultural boundaries handled (e.g., a Ugandan-Kenyan wedding)?

→ Requires cultural consultants per market. Ongoing.

---

## Chapter 27 — Recurring Ritual Detection Rules

**Question:** What are the exact rules for detecting a recurring gathering pattern as a Tradition?

**Current proposal:**
- 2 occurrences: suggest tradition creation to host
- 3 occurrences: auto-create Tradition entity (with host confirmation)
- Similarity threshold: cosine similarity > 0.85 on gathering embedding
- Time window: annual ± 4 weeks, or monthly ± 1 week, etc.

**Open questions:**
- What if the gathering changes significantly over time (different venue, different host, different participants)? Is it still the same tradition?
- What is the minimum attendance for a gathering to count as a tradition occurrence? (A birthday party attended by 2 people vs. 200)
- How are interrupted traditions handled? (Annual gathering missed for 2 years due to COVID, then resumed)

→ Requires post-launch empirical calibration. Partially deferred to Volume X — AI & Intelligence Architecture.

---

## Chapter 28 — Trust Across Gathering Types

**Question:** How does trust built in one gathering type transfer to another?

**Use case:**
An organiser with a strong reputation for church retreats starts organising secular music events. Does their church trust score transfer?

**Current proposal:**
- Global trust score carries across all gathering types (base reliability and authenticity)
- Gathering-type-specific reputation is tracked separately
- Professionalism dimension is type-specific (church retreat professionalism ≠ nightclub professionalism)
- Explicit "trust introduction" mechanism: a trusted organiser in one type can vouch for an organiser entering a new type

→ Deferred to Volume VIII — Trust Architecture

---

# Volume IV Summary

```
The Gathering Thesis:
  Gathering = fundamental unit of human coordination
  Platform models the full lifecycle, not just the transaction

Universal Gathering Model:
  16-component Gathering Record
  9 status states across full lifecycle

Gathering Taxonomy:
  9 gathering families
  40+ gathering types
  Each with: privacy defaults, economic model,
  available features, tone, special considerations

Gathering Lifecycle:
  7 phases from Conception to Tradition Formation
  Platform active in all 7 phases (not just the event itself)

Participation Architecture:
  16 participation roles
  Participation as data — each role produces distinct signals

Presence Architecture:
  9-step Presence Ladder from Unaware to Remembered
  Presence creates trust. Presence creates memories.

Gathering Intelligence:
  Real-time operational intelligence
  Post-gathering intelligence
  Community and platform intelligence

Ritual Architecture:
  RitualElement entity
  Ritual detection from recurring gathering patterns
  Rituals → Traditions → Identity

Gathering Economics:
  6 economic models from FREE to MIXED
  Full economic intelligence capture

Memory Layer:
  Memories as outputs, not features
  Privacy model per gathering type
  Lifecycle through to Legacy Archive

Community Integration:
  Gathering-Community compounding flywheel

Trust Integration:
  Trust signals from every participation role

Opportunity Integration:
  Gatherings as natural opportunity markets

AI Gathering Layer (future):
  5 AI Copilots — all data-dependent on MVP capture

Gathering Capital:
  6 capital types generated per gathering

Network Effects:
  Core flywheel architecture
  Cross-gathering compounding effects

MVP Scope:
  7 must-build features
  8 data-capture priorities
  10 explicit deferrals

Open Questions (6):
  Private gatherings · Family permissions · Funerals
  Cultural ceremonies · Ritual detection rules
  Trust across gathering types
```

---

# What Volume V Must Address

Volume V — Memory Architecture is where the long-term defensibility of ZUKA is built.

Volume V must resolve:

1. **The Memory Creation Flow** — from Vibe Drop captured at a gathering to a curated Memory in the Vault
2. **The Memory Vault Architecture** — how memories are organised, navigated, searched, and shared
3. **Photo and Video Storage** — content infrastructure, compression, CDN, long-term archival, cost model
4. **The Vibe Drop System** — full technical specification for the 15-second in-event micro-moment system
5. **Memory Sharing Model** — who can see what, sharing controls, the privacy model for shared memories
6. **AI Memory Features** — "On This Day", highlight reels, memory summaries, tradition retrospectives
7. **The Photo Tagging System** — tagging consent, facial recognition policy, tag removal
8. **Tradition Commemoration UX** — how the platform celebrates recurring gathering milestones
9. **The Legacy Memory Archive** — how the Memory Vault transitions to memorial state
10. **Media Rights & Attribution** — photographer IP, organiser rights, participant rights

---

*Volume IV is canonical. Extensions require architecture review.*
*Contradictions with this volume must be resolved before implementation.*

---

> **ZUKA Architecture Bible**
> Volume IV — Gatherings Engine
> Built in Kampala. Built for Africa. Built for the world.
