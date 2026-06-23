# ZUKA Architecture Bible
# Volume VI — Communities Architecture

> **Series:** ZUKA Architecture Bible
> **Volume:** VI of N
> **Status:** Canonical — the organism that civilisation is built from
> **Depends on:** Volume I (Vision), Volume II (Domain Model), Volume III (Identity),
>                  Volume IV (Gatherings), Volume V (Memory)
> **Required by:** Volume VII (Temporal Architecture), Volume VIII (Trust),
>                  Volume IX (Opportunity), Volume X (AI & Intelligence),
>                  Volume XI (Data), Volume XII (Legal & Governance)

---

# Preface

Volume III (Identity) is the soul.
Volume IV (Gatherings) is the heart.
Volume V (Memory) is the memory system.

Volume VI is the organism.

Communities are where identity, gatherings, memories, trust, and traditions converge into something larger than any single person — a living social structure that persists, grows, governs itself, and outlives its individual members.

Communities are not a feature of ZUKA. They are not a "groups" tab. They are living systems, and this volume treats them with the architectural seriousness that claim demands.

Most platforms think:

```
Users → Content → Engagement
```

ZUKA thinks:

```
Humans → Communities → Gatherings → Memories →
Traditions → Identity → Culture
```

Civilisations are built from communities. This volume is where ZUKA stops being an event platform and starts being social and cultural infrastructure.

---

# Part I — The Community Thesis

---

## Chapter 1 — Why Communities Are Civilisation Engines

Every gathering described in Volume IV happens because a community willed it into existence — a church organised the retreat, a family organised the reunion, a neighbourhood organised the cleanup. The Gatherings Engine cannot function without communities to power it. Volume IV's flywheel (Community → Gathering → Memory → Trust → Community) was stated as a thesis there; this volume builds the entity that makes it real.

The distinction that matters architecturally: a gathering is a single occurrence. A community is the continuous social structure that produces gatherings repeatedly, remembers them collectively, and develops an identity through the accumulation of that history. Remove the gathering and the community persists. Remove the community and the gathering was a one-off event with no future.

This is why ZUKA's long-term defensibility (Volume V's thesis that memories compound) is, at the institutional level, actually a *community* defensibility thesis. A family that has used ZUKA for ten years has a Family Vault. But it is the Family entity — the community — that holds that vault, governs its succession, and persists across the individual members who come and go.

---

## Chapter 2 — Defining a Community

```
A community is a group of people connected by shared identity,
purpose, memories, relationships, rituals, or geography.
```

This definition is deliberately broad because the things humans organise around in Africa — and everywhere — are genuinely various: blood (family, clan), faith (church, mosque, fellowship), place (village, neighbourhood, estate), institution (school, alumni, workplace), interest (cyclists, gamers), culture (heritage, diaspora), and even a single recurring event (the Blankets & Wine community, the Nyege Nyege community).

Every one of these is structurally a community in ZUKA's model. The taxonomy in Part III gives each type its specific defaults, but the underlying entity — and the rights, governance, and lifecycle mechanics in this volume — are shared across all of them.

---

## Chapter 3 — The Ten Components Every Community Possesses

```
IDENTITY      — what makes this community recognisably itself
MEMBERS       — who belongs, and at what tier of belonging
LEADERSHIP    — who is accountable for the community's direction
MEMORY        — the accumulated record of what this community has lived
TRADITIONS    — the recurring patterns that define community rhythm
TRUST         — the earned reliability the community has built, internally and externally
CULTURE       — the implicit norms, tone, and values that govern behaviour
PURPOSE       — why this community exists, stated or understood
HISTORY       — the timeline of what has happened
FUTURE        — what the community is moving toward, including its succession
```

Every part of this volume specifies one or more of these ten components in operational detail. A community missing any of the ten is, definitionally, an incomplete community record in ZUKA — the data model in Part IV is built to ensure none can be omitted.

---

# Part II — What Is a Community? (Architectural Treatment)

---

## Chapter 4 — Community vs. Other Entity Types

It is worth being precise about how Community differs from adjacent entities already defined in this Bible, because the boundary is easy to blur in implementation.

```
COMMUNITY vs. GATHERING (Volume IV)
  A Gathering is a single occurrence with a start and end time.
  A Community is the continuous structure that organises
  Gatherings, persists between them, and is not defined by
  any single occurrence.

COMMUNITY vs. FAMILY (Volume II §3.1)
  Family is a SPECIFIC TYPE of community — the kinship-based
  one. Every Family entity is a Community entity by structural
  membership in this volume's taxonomy (Part III), but not
  every Community is a Family. This volume's governance,
  membership, and lifecycle mechanics apply to Family entities
  exactly as they apply to a Choir or a Neighbourhood — the
  Family-specific nuances (succession via kinship, the Family
  Vault) layer on top of the shared Community model, they
  do not replace it.

COMMUNITY vs. RELATIONSHIP (Volume II, Part II)
  A Relationship connects two entities directly.
  A Community is itself an entity that many people relate TO
  via the COMMUNITY_MEMBER relationship type. The community
  is not the sum of its member relationships — it has its
  own identity, history, and persistence independent of any
  single member's relationship to it.

COMMUNITY vs. TRADITION (Volume IV, Chapter 11 / Volume V, Chapter 22)
  A Tradition belongs TO a community (or to a Family/Person).
  It is the recurring-gathering pattern that the community
  produces. A community can — and the strongest ones do —
  hold multiple Traditions simultaneously.
```

---

# Part III — Community Taxonomy

---

## Chapter 5 — Complete Community Type Registry

Each community type carries default governance expectations, typical membership scale, and typical relationship to the Gathering and Memory layers. These are defaults, not constraints — any community can configure itself differently from its type's default.

```
FAMILY COMMUNITIES
  Types: Nuclear Family · Extended Family · Clan · Lineage
  Typical scale: 4 – 500+ (clan-scale)
  Governance default: Elder/Steward-led, often informal
  Gathering relationship: Highest emotional density gatherings
    (weddings, funerals, reunions — Volume IV's FAMILY family)
  Memory relationship: Always produces a Family Vault
    (Volume V, Chapter 16)
  Succession model: Kinship-based (Volume III, Chapter 25)

──────────────────────────────────────────────────────────────

FAITH COMMUNITIES
  Types: Church · Mosque · Fellowship · Ministry · Choir
  Typical scale: 20 – 10,000+
  Governance default: Clergy/Leadership-led, often with a
    council or eldership structure
  Gathering relationship: Highest frequency gatherings on
    the platform (weekly services) — foundational tradition
    generators (Volume IV, Chapter 6, Family 3)
  Memory relationship: Strong Community Archive candidate
    (Volume V, Chapter 17) — "30 years of retreats"
  Special: Sub-community nesting is common and expected
    (a Choir nested within a Church — see Part XIX)

──────────────────────────────────────────────────────────────

EDUCATIONAL COMMUNITIES
  Types: School · Class · Alumni · Department · Society
  Typical scale: 30 – 50,000+ (large university alumni)
  Governance default: Institutional leadership for School/
    Department; elected or self-organised for Alumni/Society
  Gathering relationship: Milestone-heavy (graduations,
    reunions — Volume IV, Family 4)
  Memory relationship: Alumni communities are a primary
    Historical Memory type generator (Volume V, Chapter 8)
  Special: An Alumni community OUTLIVES its origin School
    relationship as defined in Volume II §3.2 — the alumni
    persist as members long after the formal School
    relationship period ends

──────────────────────────────────────────────────────────────

GEOGRAPHIC COMMUNITIES
  Types: Village · Neighbourhood · Estate · Town · District
  Typical scale: 50 – 100,000+
  Governance default: Often dual — informal community leaders
    alongside formal local government structures
  Gathering relationship: Civic gatherings (Volume IV, Family 6)
    — cleanups, village meetings, fundraisers
  Memory relationship: Strong HISTORICAL memory generator
    at City Timeline scale (Volume V, Chapter 18)
  Special: Dual nature with Place entities (Volume II §3.4) —
    the same entity_id can serve as both Community and Place
    where appropriate (carried over from Volume II's
    NEIGHBORHOOD treatment)

──────────────────────────────────────────────────────────────

PROFESSIONAL COMMUNITIES
  Types: Engineers · Doctors · Lawyers · Entrepreneurs
  Typical scale: 20 – 50,000+
  Governance default: Elected leadership, formal association
    structures common
  Gathering relationship: Volume IV, Family 9 (Professional
    Gatherings) — conferences, networking events
  Opportunity relationship: Highest Opportunity Graph
    density of any community type (Volume IV, Chapter 17)
  Special: Often has formal verification requirements for
    membership (professional licensing bodies)

──────────────────────────────────────────────────────────────

INTEREST COMMUNITIES
  Types: Cyclists · Gamers · Runners · Car Clubs
  Typical scale: 10 – 5,000+
  Governance default: Founder/organiser-led, often informal
  Gathering relationship: Volume IV, Family 5 & 8 — Social
    and Sports Gatherings, frequently recurring
  Special: Highest rate of organic Tradition formation
    (Friday Football, weekly group rides) — Part IX

──────────────────────────────────────────────────────────────

CULTURAL COMMUNITIES
  Types: Traditional Groups · Heritage Communities ·
         Diaspora Groups
  Typical scale: 20 – 100,000+ (diaspora-scale)
  Governance default: Often elder/council-based, reflecting
    traditional governance structures
  Gathering relationship: Volume IV, Family 7's CULTURAL
    memory linkage (Volume V, Chapter 8) — festivals,
    heritage celebrations
  Special: Carries the highest sensitivity requirement for
    cultural accuracy (extends Volume IV, Chapter 26's
    open question on cultural ceremony modelling) —
    these communities are often the appropriate authority
    on their own cultural ceremony templates

──────────────────────────────────────────────────────────────

EVENT COMMUNITIES
  Types: Festival-specific communities (e.g., "Blankets &
         Wine Community," "Nyege Nyege Community,"
         "World Cup Community")
  Typical scale: 500 – 1,000,000+
  Governance default: Organiser-led, often a Brand or
    Company entity (Volume II §3.6) at the helm
  Gathering relationship: Forms AROUND a recurring Festival
    or Tour gathering (Volume II §3.3) — the community
    is the fanbase that persists between occurrences
  Special: This is the community type most directly tied
    to ZUKA's commercial entertainment layer — a strong
    Event Community is a direct asset for B2B sponsorship
    products (extends ZUKA.md §16, Product 2)
```

---

# Part IV — Community Entity Model

---

## Chapter 6 — The Canonical Community Record

This extends the Community entity types defined throughout Volume II §3.2 into the full operational structure required by this volume.

```typescript
Community {

  // ── IDENTITY ──────────────────────────────────────────────
  community_id:          UUID
  community_type:         CommunityType        // from Part III taxonomy
  community_family:       CommunityFamily       // family|faith|educational|
                                                  // geographic|professional|
                                                  // interest|cultural|event

  name:                   TEXT
  description:             TEXT
  founding_story:           TEXT
  // The narrative of why this community began — distinct
  // from the structured founding_date below; this is the
  // human story, often the community's most-told Memory.

  // ── IDENTITY (Part V) ────────────────────────────────────
  identity: {
    display_name:            TEXT
    tagline:                  TEXT
    visual_identity:          CommunityVisualIdentity
    // logo_url, cover_image_url, colour_theme
    public_profile_url:        TEXT
  }

  // ── CULTURE ───────────────────────────────────────────────
  culture: {
    values:                   TEXT[]
    norms:                     TEXT[]
    // Stated or emergent — communities can declare these,
    // or ZUKA can surface them as patterns observed from
    // behaviour (gathering tone, ritual elements) for the
    // community's own reflection — never imposed
    tone:                      CommunityTone
    // 'formal' | 'casual' | 'spiritual' | 'mixed'
    languages:                  LanguageCode[]
  }

  // ── MEMBERS ───────────────────────────────────────────────
  membership: {
    total_member_count:        Integer          // computed
    active_member_count:        Integer          // computed,
                                                  // see Chapter 8
    member_tiers:                MemberTier[]
    // See Part VI — Membership Architecture
    join_policy:                  JoinPolicy
    // 'open' | 'approval_required' | 'invite_only' | 'closed'
  }

  // ── LEADERSHIP ────────────────────────────────────────────
  leadership:               LeadershipStructure
  // See Part XIV — Community Governance

  // ── MEMORY ────────────────────────────────────────────────
  memory: {
    community_archive_id:        UUID
    // Links to the CommunityArchive entity (Volume V, Chapter 17)
    memory_count:                  Integer      // computed
    oldest_memory_date:             Date
  }

  // ── TRADITIONS ────────────────────────────────────────────
  traditions:                 UUID[]
  // Array of Tradition entity references
  // (Volume IV, Chapter 11 / Volume V, Chapter 22)

  // ── TRUST ─────────────────────────────────────────────────
  trust_profile:               CommunityTrustProfile
  // See Part XI — Community Trust

  // ── REPUTATION ────────────────────────────────────────────
  reputation:                  CommunityReputation
  // See Part XII — Community Reputation

  // ── TIMELINE ──────────────────────────────────────────────
  timeline: {
    founding_date:               Date
    founding_members:            UUID[]
    // Permanently recorded — see Volume III, Chapter 21
    // (Community Founder relationship — historical, persists
    //  even if the founder is no longer active)
    lifecycle_stage:              CommunityLifecycleStage
    // See Part XX — Community Lifecycle
    milestone_ids:                 UUID[]
    // Links to HISTORICAL Memory entities (Volume V, Chapter 8)
  }

  // ── GOVERNANCE ────────────────────────────────────────────
  governance:                  GovernanceModel
  // See Part XIV

  // ── ECONOMICS ─────────────────────────────────────────────
  economics:                   CommunityEconomics
  // See Part XV

  // ── VISIBILITY & PRIVACY ──────────────────────────────────
  visibility: {
    discovery_level:              DiscoveryLevel
    // 'public' | 'invite_only' | 'private'
    member_list_visibility:        VisibilityLevel
    archive_visibility:             VisibilityLevel
  }

  // ── FEDERATION ────────────────────────────────────────────
  federation: {
    parent_community_id:           UUID    // null if top-level
    child_community_ids:             UUID[]
    // See Part XIX — Community Federation
    federation_role:                 FederationRole
    // 'standalone' | 'parent' | 'child' | 'peer_network_member'
  }

  // ── STATUS ────────────────────────────────────────────────
  status:                       CommunityStatus
  // 'active' | 'dormant' | 'transitioning' | 'archived'

  // ── INTELLIGENCE ──────────────────────────────────────────
  embedding:                    Vector(1536)

  // ── AUDIT ─────────────────────────────────────────────────
  created_at:                   Timestamptz
  updated_at:                   Timestamptz
  created_by:                    UUID
  version:                       Integer
}
```

---

# Part V — Community Identity

---

## Chapter 7 — Identity Creates Belonging

A community's identity is not decoration. It is the mechanism by which a person looks at a group and feels "this is mine" or "I belong here." Examples named in the source brief illustrate the range:

```
Makerere Alumni            — institutional pride, shared formative years
Jazz Lovers Kampala         — shared taste, chosen affinity
Bugolobi Residents          — shared place, proximity-based belonging
St. Mary's Choir             — shared practice, faith and craft combined
Toyota Owners Club Uganda    — shared object of interest, practical community
```

Each of these names does real work. "Makerere Alumni" instantly signals formative life experience and a specific multi-decade institutional bond. "Jazz Lovers Kampala" signals chosen identity around taste rather than circumstance. ZUKA's community identity fields (`display_name`, `tagline`, `visual_identity`) exist specifically to let every community claim and project this kind of instantly legible belonging — the same instinct behind Volume I's brand thesis applied at the community level rather than the platform level.

The `founding_story` field (Chapter 6) deserves particular product attention: it is frequently a community's single most emotionally important piece of content, more read and more shared than any operational detail. A well-told founding story is itself a Memory-Layer-eligible artifact (it can be linked as a HISTORICAL memory per Volume V, Chapter 8) and should be treated with the same narrative care as any Story entity.

---

# Part VI — Membership Architecture

---

## Chapter 8 — Not All Members Are Equal

Membership in a community is not a binary in/out state. ZUKA models it as a tiered structure with role-based permissions, consistent with how real communities — a church, a club, a choir — actually organise themselves.

```typescript
CommunityMembership {
  membership_id:          UUID
  community_id:            UUID
  person_id:                UUID

  role:                     MembershipRole
  // See registry below

  status:                    MembershipStatus
  // 'active' | 'inactive' | 'suspended' | 'alumni' | 'honorary'

  joined_at:                  Timestamptz
  invited_by:                  UUID

  // Activity tracking (feeds active_member_count, Chapter 6)
  last_active_at:               Timestamptz
  gathering_participation_count: Integer
  // Computed from Volume IV's Participation records where
  // gathering.community_id matches this community

  // Contribution tracking (feeds Community Capital, Part XIII)
  memory_contribution_count:      Integer
  leadership_history:              LeadershipTenure[]

  permissions:                     MembershipPermission[]
  // Derived from role by default, can be individually extended
}

MembershipRole enum:
  founder        // Permanent historical designation — see Chapter 7
  leader          // Current operational authority
  elder            // Advisory/spiritual/cultural authority —
                    // common in Faith and Cultural communities,
                    // distinct from operational Leader
  admin             // Day-to-day operational management
  moderator           // Content and behaviour stewardship
  member               // Standard membership
  volunteer              // Active non-leadership contributor
  archivist                // Community Archive steward
                            // (Volume V, Chapter 17)
  treasurer                  // Financial steward — see Part XV
  guest                       // Limited, often time-bounded access
                              // (e.g., a visitor at a church service)
```

### Default Permissions by Role

```
FOUNDER:
  All admin permissions + permanent visibility in community
  history regardless of current activity status + veto rights
  over succession disputes (configurable per community
  governance model, Part XIV)

LEADER:
  Manage membership, create/edit gatherings on behalf of the
  community, manage community profile, assign moderator/
  archivist/treasurer roles, initiate governance changes

ELDER:
  Advisory visibility into all governance decisions, voice
  in succession matters, often non-operational (cannot
  unilaterally change settings, but cannot be excluded from
  major decisions either) — the specific balance is
  configurable per community's governance model

ADMIN:
  Manage membership (within bounds set by Leader), manage
  day-to-day gathering operations, access community analytics

MODERATOR:
  Review flagged content within the community's gatherings
  and memories, manage member conduct issues

MEMBER:
  Participate in gatherings, contribute to shared memories
  (per Volume V, Chapter 14's default 'contributor' rights),
  view community archive per its visibility settings

VOLUNTEER:
  Member permissions + elevated trust signal in
  Community Trust Profile (Part XI) for consistent
  volunteer participation

ARCHIVIST:
  Steward-level rights over the Community Archive
  (Volume V, Chapter 17) — curate, organise, manage
  archive visibility, without requiring full Leader access
  to the community's operational and governance functions

TREASURER:
  Access to Community Economics (Part XV) — financial
  visibility and contribution management, without requiring
  full Leader access to membership/governance functions

GUEST:
  View-only, often scoped to a single gathering or a
  time-bounded window (e.g., a visiting alumnus given
  90-day guest access to an alumni community's recent
  archive)
```

---

# Part VII — Community Memory

---

## Chapter 9 — Community Memory Survives Individual Memory

This is the central thesis connecting Volume V to Volume VI: a community's memory is not the sum of its members' individual memories. It is its own accumulating record, governed by the community rather than by any single person, and — critically — it persists when individual members forget, move away, or pass on.

```
EXAMPLES OF WHAT A COMMUNITY REMEMBERS, COLLECTIVELY,
THAT NO SINGLE MEMBER NECESSARILY REMEMBERS ALONE:

  Annual Retreats        — the full unbroken sequence, even
                            if any single attendee has only
                            been to a handful
  Graduations              — every cohort, not just the
                              member's own
  Fundraisers                — the cumulative record of giving
                                and impact over years
  Trips                        — the community's full travel
                                  history
  Festivals                      — every occurrence, building
                                    the case for Tradition status
  Milestones                       — the founding, the growth
                                      markers, the anniversaries
```

This is operationally why the Community Archive (Volume V, Chapter 17) is governed at the community level via the Archivist role (Chapter 8) and the succession policy (Part XXII) — not by whichever individual happened to upload the most photos. The archive belongs to the institution, and the institution's continuity must be engineered independently of any individual member's continuity.

---

# Part VIII — Community Timeline

---

## Chapter 10 — Founded → Growth → Milestones → Traditions → Generations

Every community has a Timeline, structurally parallel to the Identity Timeline defined for individuals in Volume III, Chapter 18, and the Memory Timelines defined in Volume V, Chapter 18 — but operating at institutional rather than personal scale.

```
EXAMPLE — Jazz Lovers Kampala, Community Timeline

2018 ─── Community Founded
          [timeline.founding_date · founding_members: 6]
  │
2019 ─── First Retreat
          [First Tradition-eligible gathering recorded]
  │
2022 ─── 100 Members
          [Membership milestone — auto-detected from
           membership.total_member_count crossing threshold]
  │
2024 ─── Sunday Jazz formalised as Tradition
          [Tradition entity created — Volume IV, Chapter 11]
  │
2026 ─── 10th Anniversary
          [HISTORICAL Memory created — Volume V, Chapter 8 —
           community-wide celebratory gathering]
```

The Community Timeline construction follows the same domain-event-sourced approach established in Volume II, Chapter 8: `CommunityCreated`, `CommunityMemberAdded`, `CommunityGatheringCreated`, `CommunityTraditionEstablished` events (already registered in Volume II's Domain Event Type Registry) are the raw material from which this Timeline is automatically assembled, with milestone thresholds (member count round numbers, anniversary years) surfaced automatically per the same pattern as the Anniversary Engine (Volume V, Chapter 21) — and governed by the same dignity-first sensitivity principles where applicable (a community's history includes its hard years, not only its growth milestones).

---

# Part IX — Community Rituals

---

## Chapter 11 — Rituals as the Mechanism of Strength

This chapter operationalises, at the community level, the RitualElement entity already defined in Volume IV, Chapter 11, and the Tradition-detection signals defined in Volume V, Chapter 22.

```
EXAMPLES OF COMMUNITY-DEFINING RITUALS:

  Friday Football         — Interest community, weekly cadence
  Sunday Service            — Faith community, weekly cadence,
                              foundational tradition (see
                              Part III, Faith Communities)
  Annual Reunion              — Family or Alumni community,
                                yearly cadence
  Christmas Gathering            — Family community, yearly,
                                    high emotional density
  Choir Practice                   — Faith/Interest community,
                                      frequent, skill-building
  Monthly Meetup                     — Professional or Interest
                                        community, regular cadence
```

The thesis stated plainly: **repeated rituals create identity.** A community that gathers without ritual is a loose association of people with shared circumstance. A community that develops and sustains rituals becomes something people belong to, defend, and pass on. This is the mechanism, not a metaphor — and it is precisely why Volume IV's Ritual Detection (Chapter 11) and Volume V's Tradition Detection (Chapter 22) are positioned as foundational rather than decorative platform intelligence.

---

# Part X — Community Traditions

---

## Chapter 12 — Rituals → Traditions → Culture

The progression stated in the source material is precise and worth preserving exactly: **rituals become traditions. Traditions become culture.**

The Tradition entity itself is already fully specified in Volume IV, Chapter 11 (data structure) and operationalised further in Volume V, Chapter 22 (memory-layer detection signals). This chapter's sole addition is the explicit ownership binding: every Tradition entity's `owner_entity_id` (per Volume II's pattern, extended) is, in the overwhelming majority of cases, a Community entity — making Traditions fundamentally a Community-Architecture concern even though their data structure lives in the Gatherings/Memory volumes.

```typescript
// Re-stated from source brief for completeness within
// this volume's context — full canonical definition
// remains in Volume IV, Chapter 11

Tradition {
  tradition_id:          UUID
  community_id:            UUID        // the owning Community
  start_date:                Date
  occurrences:                 Integer
  participants:                  UUID[] // aggregate across all
                                         // occurrences
  significance:                   TEXT
  // The community's own account of why this tradition matters —
  // distinct from individual Memory.significance fields
  // (Volume V, Chapter 5) — this is the COMMUNITY's collective
  // statement of meaning, typically authored or ratified by
  // Leadership/Elder roles
}
```

The practical implication for community leaders: formalising a recurring gathering as a named Tradition (Chapter 10's example: "Sunday Jazz formalised as Tradition") is one of the highest-leverage actions a community can take on the platform — it converts a series of individually-memorable gatherings into a single, compounding institutional asset that strengthens with every future occurrence.

---

# Part XI — Community Trust

---

## Chapter 13 — Trust at Institutional Scale

This extends Volume III's individual Trust Architecture (Chapter 14–16) with community-specific dimensions. A community is itself a trust-bearing entity, separately from the aggregate trust of its individual members.

```typescript
CommunityTrustProfile {
  community_id:               UUID

  trust_dimensions: {
    reliability:                Float(0.0–1.0)
    // Does this community deliver the gatherings it announces?
    // Computed from the community's aggregate gathering
    // delivery rate (Volume IV's organiser reliability signals,
    // aggregated where the organiser acted on behalf of
    // this community)

    safety:                       Float(0.0–1.0)
    // Has this community's gatherings been associated with
    // safety incidents? Default 1.0, decreases on verified
    // incidents — same asymmetric recovery model as Volume III,
    // Chapter 15 (slow to rebuild, fast to lose)

    participation:                  Float(0.0–1.0)
    // Healthy engagement signal — active_member_count relative
    // to total_member_count, gathering attendance consistency

    contribution:                     Float(0.0–1.0)
    // Memory Archive richness, member contribution_count
    // distribution (a healthy community has broad contribution,
    // not just from one or two members)

    leadership:                         Float(0.0–1.0)
    // Stability and accountability of the leadership structure —
    // informed by succession history (clean transitions score
    // higher than disputed ones, Part XXII), leadership tenure
    // patterns, and governance transparency

    integrity:                            Float(0.0–1.0)
    // Absence of fraud, fake membership inflation, or
    // manipulated trust signals — informed by the same
    // fraud-detection patterns established in Volume III,
    // Chapter 30 (Reputation Abuse), applied at community scale
  }

  overall_community_trust:            Float(0.0–1.0)
  // Weighted composite — weights configurable per
  // community_family (a Faith community's trust profile
  // weights safety and integrity more heavily than a
  // casual Interest community's would, for example)

  trust_compounds_over_years:          true
  // Explicit architectural acknowledgment: community trust
  // decay rates are SLOWER than individual relationship
  // decay rates (Volume II, Chapter 6) — an institution with
  // a 10-year unblemished record should not lose meaningful
  // trust from a single quiet year, in contrast to an
  // individual friendship's faster decay model
}
```

---

# Part XII — Community Reputation

---

## Chapter 14 — How Communities Are Perceived

Distinct from Trust (Chapter 13, which measures earned reliability), Reputation captures how a community is *perceived* — its character, as understood by members, prospective members, and the wider platform.

```typescript
CommunityReputation {
  community_id:              UUID

  character_tags:              ReputationTag[]
  // Self-reported by leadership AND/OR algorithmically
  // surfaced from patterns in gathering tone, member
  // reviews, and community culture fields (Chapter 6)
  // — always presented as a blend of both, never purely
  // algorithmic without community input

  ReputationTag enum:
    friendly
    professional
    faith_centered
    creative
    family_oriented
    reliable
    welcoming
    exclusive            // not inherently negative — some
                          // communities (professional bodies,
                          // certain cultural communities)
                          // legitimately have selective
                          // membership, and stating this
                          // plainly serves prospective members
                          // better than false openness signalling

  review_summary:               CommunityReviewSummary
  // Aggregated from member and gathering-attendee reviews,
  // following the same verified-attendance-only review
  // eligibility rule established for venues/organisers
  // (ZUKA.md §28) — applied here to community-organised
  // gatherings specifically

  reputation_influences_growth:    true
  // Explicit acknowledgment: reputation directly affects
  // Community Discovery ranking and prospective member
  // conversion — this is why character_tags must never be
  // gamed or purchased, only earned
}
```

---

# Part XIII — Community Capital

---

## Chapter 15 — Community Wealth

This extends the Capital Model first introduced in Volume II, Chapter 17, with the community-specific aggregation logic. Community Capital is not a new capital type — it is the institutional accumulation of the same capital types already defined, computed at the Community entity level rather than the individual Identity level.

```
COMMUNITY CAPITAL = AGGREGATE OF:

  Memory Capital
    The Community Archive's accumulated richness
    (Volume V, Chapter 17 contents, scored per Volume V,
    Chapter 20's methodology, applied at community scale)

  Trust Capital
    The Community Trust Profile (Part XI), specifically
    the overall_community_trust score and its multi-year
    trend

  Relationship Capital
    The density and health of the Member ↔ Member,
    Member ↔ Leader relationship graph within the
    community (Part XVII)

  Knowledge Capital
    [New capital type, introduced at community scale]
    The accumulated expertise, institutional knowledge,
    and documented wisdom a community has built —
    most relevant for Professional and Faith communities.
    Composed of: meeting notes, documented decisions,
    mentorship relationships formed, knowledge-sharing
    gathering frequency.

  Cultural Capital
    [New capital type, introduced at community scale]
    The strength and distinctiveness of the community's
    identity, traditions, and norms (Part V, Part IX, Part X)
    — how recognisable and how deeply held the community's
    culture is, both internally and to outside observers.

  Opportunity Capital
    The aggregate Opportunity Graph density generated
    by this community (Volume IV, Chapter 17) — jobs posted,
    mentorships formed, business referrals made within
    or sourced from the community

  Legacy Capital
    The community's permanence signal — founding members
    permanently credited (Chapter 6), traditions that have
    survived leadership transitions, Memorialised former
    members whose contributions remain credited
    (Volume V, Chapter 24)

THIS IS COMMUNITY WEALTH:
  Never displayed as a single raw score (consistent with
  Volume V, Chapter 20's restriction on individual Memory
  Capital), but used internally to power: Community Discovery
  ranking, B2B sponsorship attractiveness signals (extends
  ZUKA.md §16's B2B Audience Intelligence product), and
  succession-planning prioritisation (a high-Legacy-Capital
  community's succession governance deserves more platform
  support attention than a brand-new one's).
```

---

# Part XIV — Community Governance

---

## Chapter 16 — Four Governance Layers

Every community requires governance — even an informal Interest community has *some* decision-making structure, even if it is simply "the founder decides." ZUKA's governance model is structured into four layers, each addressable independently so that a community can have, for example, strong Operational governance with minimal Strategic governance (a small running club) or the reverse (a large church with a strong Elder council but day-to-day operations delegated broadly).

```typescript
GovernanceModel {
  community_id:            UUID

  operational_layer: {
    admins:                  UUID[]
    moderators:                UUID[]
    leaders:                     UUID[]
    decision_scope:                TEXT[]
    // What this layer can decide unilaterally:
    // typically day-to-day gathering scheduling, content
    // moderation, membership approval
  }

  financial_layer: {
    treasurers:               UUID[]
    finance_team:                UUID[]
    decision_scope:                 TEXT[]
    // Budget approval thresholds, fundraising authorisation,
    // expenditure limits — see Part XV
    transparency_policy:              FinancialTransparencyPolicy
    // 'fully_public' | 'member_visible' | 'leadership_only'
  }

  historical_layer: {
    archivists:                  UUID[]
    memory_custodians:              UUID[]
    decision_scope:                   TEXT[]
    // Archive curation, visibility settings for community
    // memory, succession of archival stewardship —
    // see Volume V, Chapter 17's stewardship field
  }

  strategic_layer: {
    council:                      UUID[]
    elders:                          UUID[]
    founders:                          UUID[]
    decision_scope:                     TEXT[]
    // Major direction changes, leadership succession
    // ratification, governance model changes themselves,
    // dissolution decisions (Part XX)
    decision_model:                       StrategicDecisionModel
    // 'founder_authority' | 'elder_consensus' |
    // 'council_majority_vote' | 'full_member_vote'
  }

  governance_change_policy: {
    who_can_propose_changes:           MembershipRole[]
    ratification_required_from:           GovernanceLayer
    minimum_notice_period_days:             Integer
  }
}
```

The explicit separation of these four layers means a community's financial steward does not automatically have authority over its archive, and its archivist does not automatically have authority over its membership policy. This separation of powers is a direct architectural defence against the governance failure modes named in Part XXIV's Risks (political capture, leadership disputes) — concentrating all authority in one role is what makes those failure modes catastrophic rather than merely inconvenient.

---

# Part XV — Community Economics

---

## Chapter 17 — How Communities Spend and Raise Money

This extends the Gathering Economics model (Volume IV, Part IX) with the persistent, cross-gathering economic layer that exists at the Community level — money that flows through the community over time, not just during a single gathering.

```typescript
CommunityEconomics {
  community_id:                  UUID

  income_streams: {
    membership_fees:                MembershipFeeConfig
    // recurring (monthly/annual) or one-time
    contributions:                     ContributionRecord[]
    // ad-hoc giving, distinct from a specific gathering's
    // CONTRIBUTION economic model (Volume IV, Chapter 12) —
    // this is general community support, e.g. an ongoing
    // church building fund
    fundraising_campaigns:                FundraisingCampaign[]
    donations:                               DonationRecord[]
    merchandise_revenue:                       Decimal
  }

  expense_categories: {
    travel:                          Decimal      // aggregated
    events:                             Decimal     // aggregated from
                                                     // community-organised
                                                     // Gathering economics
    merchandise_costs:                    Decimal
    operational:                             Decimal
  }

  treasury: {
    current_balance:                      Decimal
    currency_code:                            CurrencyCode
    // FUTURE: Community Wallet (see below)
  }

  financial_transparency:              FinancialTransparencyPolicy
  // Inherited from governance.financial_layer by default,
  // overridable

  audit_trail:                            DomainEvent[]
  // Per Volume II, Chapter 8 — every income and expense
  // event is append-only logged, never editable in place
}
```

### Future Capability — Community Wallets & Treasuries

```
COMMUNITY WALLET (future)
  Extends the Event Wallet concept (ZUKA.md §30) to
  persistent community-level balances. A church's
  building fund, a club's annual trip savings, an
  alumni association's scholarship fund — all held as
  a Community Wallet with the same closed-loop prepaid
  instrument regulatory positioning as the individual
  Event Wallet (ZUKA.md §30's regulatory note applies
  equally here).

COMMUNITY TREASURY
  A more formal extension, appropriate for larger
  Professional and Faith communities — multi-signatory
  approval for expenditure above configurable thresholds,
  full financial reporting to the membership per the
  transparency policy, and integration with the
  financial_layer governance structure (Chapter 16) for
  authorisation workflows.

Both are explicitly DO NOT BUILD YET per Part XXIII's MVP
scope — documented here because Community Economics data
capture at MVP (basic contribution and fundraising
tracking) is the foundation these future capabilities
will be built on.
```

---

# Part XVI — Community Opportunity Engine

---

## Chapter 18 — Communities as Opportunity Networks

This extends the Opportunity Graph and Opportunity Integration concepts from Volume IV, Chapter 17, with the specific mechanics of how a Community entity — as opposed to a single Gathering — generates and sustains opportunity over time.

```
OPPORTUNITY TYPES GENERATED BY COMMUNITIES (sustained,
not single-gathering-bound):

  Jobs
    A Professional community's member posts a job opening
    visible to fellow members — leveraging the trust and
    relevance the community provides over a cold job board

  Mentorship
    A Professional or Educational (Alumni) community
    facilitates ongoing Mentor/Student relationships
    (Volume II, Chapter 5) that persist across many
    gatherings, not formed at a single event

  Partnerships
    Community ↔ Community relationships (Part XVII) that
    produce joint gatherings, resource sharing, or
    cross-promotion

  Scholarships
    A Faith or Alumni community's fundraising (Part XV)
    converted into a formal Opportunity entity
    (Volume II, §3.10 — extends the existing JOB/
    SPONSORSHIP/BOOKING/COLLABORATION registry)

  Business Referrals
    The highest-frequency, often-informal opportunity type —
    community trust (Part XI) makes members more willing
    to refer business to each other than to strangers,
    and ZUKA's Relationship Graph (Volume III, Chapter 12)
    can make these referral patterns visible and trackable
    over time, without requiring formal transaction processing

  Volunteering
    Community-organised gatherings (cleanups, fundraisers —
    Volume IV, Family 6) generate Volunteer-role
    participation (Chapter 8) that itself builds the
    volunteer's Trust Capital (Volume III, Chapter 15) and
    Experience Capital (Volume II, Chapter 17)

COMMUNITIES BECOME OPPORTUNITY NETWORKS:
  The compounding mechanism — a community with strong
  internal trust and a dense relationship graph naturally
  becomes the channel through which its members find work,
  partners, mentors, and referrals, BEFORE they would think
  to look on a general marketplace. This is a direct
  consequence of Volume IV, Chapter 17's thesis applied at
  sustained, institutional scale rather than single-gathering
  scale.
```

---

# Part XVII — Community Graph

---

## Chapter 19 — How Communities Connect

This specifies the Community-specific subset of relationships within the Entity Graph (Volume II, Chapter 13, Graph 1), making explicit the relationship types that exist at and between the community level.

```
MEMBER ↔ MEMBER
  Standard Relationship types (FRIEND, MENTOR, etc. —
  Volume II, Chapter 5) frequently FORM via shared
  community membership — the Community is often the
  causal origin of these relationships, even though the
  relationship entity itself is between the two Person
  entities directly. This causal linkage (which community
  introduced this relationship) is a valuable signal,
  captured via the relationship's metadata field
  (Volume II, Chapter 4).

MEMBER ↔ LEADER
  COMMUNITY_LEADER relationship type (Volume II, Chapter 5),
  carries elevated trust_score by structural default given
  the accountability inherent in leadership roles.

COMMUNITY ↔ COMMUNITY
  See Part XIX — Community Federation for the structured
  parent/child case. Beyond federation, peer relationships
  exist too: two independent churches that jointly host an
  annual crusade, two alumni associations that co-organise
  a shared event — these are PARTNERSHIP-type Community ↔
  Community relationships, distinct from federation's
  nesting structure.

COMMUNITY ↔ EVENT
  Already established structurally — every Gathering entity
  (Volume IV) carries a communities.primary_community_id
  and communities.invited_community_ids fields. This Graph
  relationship is the aggregation point that powers the
  Gathering-Community flywheel (Volume IV, Part XI).

COMMUNITY ↔ VENUE
  A community's pattern of gathering at a particular Venue
  entity (Volume II, §3.4) over time — feeds Venue trust
  signals (ZUKA.md §24's Venue Verification, extended) and
  is a direct input to Venue Intelligence B2B products
  (ZUKA.md §16, Product 3) — "this venue is the trusted
  home of these 5 communities" is valuable signal for a
  multi-venue hospitality group evaluating expansion.

COMMUNITY ↔ BRAND
  SPONSOR relationship type (Volume II, Chapter 5) — formalises
  the connection that powers Event Communities' (Part III)
  commercial relevance and feeds the Audience Intelligence
  B2B product (ZUKA.md §16, Product 2).

THE GRAPH BECOMES VALUABLE:
  Each of these relationship types compounds the others.
  A community with dense Member ↔ Member relationships,
  strong Member ↔ Leader trust, healthy Community ↔ Community
  partnerships, a stable Community ↔ Venue home, and an
  active Community ↔ Brand sponsorship — this is, in graph
  terms, a thriving institution, and every one of these
  signals is independently visible and queryable in ZUKA's
  data model, not locked away in any single person's
  knowledge of "how things work around here."
```

---

# Part XVIII — Community AI (Future)

---

## Chapter 20 — The Community Copilots

These are explicitly future-state, governed by the same restraint principle established for the Gathering Copilots (Volume IV, Part XIV) and the Memory Historians (Volume V, Chapter 23): **purpose is understanding and assistance, never surveillance, and never an input to external commercial use without explicit community leadership authorisation.**

```
CHURCH COPILOT
  Retreat planning — venue suggestions informed by the
  community's own gathering history (Part VIII Timeline)
  Attendance — forecasting and pattern recognition across
  the congregation's participation history (Chapter 8)
  Archives — assisted curation suggestions for the
  Archivist role (Chapter 8), surfacing under-documented
  periods in the Community Archive (Volume V, Chapter 17)

ALUMNI COPILOT
  Reunions — optimal timing and format suggestions based
  on historical attendance patterns by cohort
  Fundraising — campaign suggestions informed by the
  community's Economics history (Part XV) and comparable
  Alumni community benchmarks (anonymised, aggregate only)
  Mentorship — matching suggestions within the Opportunity
  Engine (Part XVI), connecting senior alumni to recent
  graduates based on field, location, and stated interest

FAMILY COPILOT
  Birthdays — reminders and gathering-planning assistance,
  informed by the Family Vault (Volume V, Chapter 16)
  Trips — suggestions informed by past Family gathering
  locations and stated preferences
  Traditions — identifying ritual elements (Chapter 11)
  that are at risk of lapsing (gap analysis, gently
  surfaced, consistent with Volume V, Chapter 26's
  "show traditions that disappeared" — framed without
  presuming loss)

CLUB COPILOT
  Meetups — scheduling optimisation based on member
  availability patterns inferred from past attendance
  Membership growth — discovery and outreach suggestions,
  always operating within the community's own stated
  join_policy (Chapter 6) — never auto-recruiting without
  leadership configuration and consent
  Engagement — identifying members whose
  gathering_participation_count (Chapter 8) has dropped,
  surfaced to leadership as a gentle "this member may
  value re-engagement" signal, never as a punitive or
  exclusionary flag
```

---

# Part XIX — Community Federation

---

## Chapter 21 — Communities Contain Subcommunities

The example given is precise and common across the African faith and institutional context:

```
Church
  ↓
  Choir
  ↓
  Youth Group
  ↓
  Parents Fellowship
```

A single Church community is, in practice, a federation of multiple sub-communities, each with its own membership (often a strict subset of the parent's membership), its own leadership, its own rituals and gatherings, and frequently its own emerging Tradition entities — while still belonging to, drawing identity from, and reporting up to the parent Church community.

```typescript
// Extends the federation field already specified in
// Chapter 6's canonical Community Record

FederationStructure {
  parent_community_id:        UUID
  child_community_ids:         UUID[]

  federation_rules: {
    membership_inheritance:        MembershipInheritanceRule
    // 'automatic' — child members are automatically parent
    //   members (e.g., Choir members are automatically
    //   Church members)
    // 'independent' — child membership is separate and must
    //   be separately granted (e.g., a Youth Group might
    //   admit non-member visitors who are not yet full
    //   Church members)
    // 'parent_subset' — child membership MUST be a subset
    //   of parent membership (most common for Faith
    //   sub-communities)

    governance_inheritance:           GovernanceInheritanceRule
    // 'autonomous' — child community has fully independent
    //   governance (Part XIV)
    // 'parent_oversight' — child's Strategic layer decisions
    //   require parent Strategic layer ratification
    // 'fully_delegated' — child operates entirely under
    //   parent governance, with no independent Strategic
    //   layer of its own

    trust_inheritance:                  TrustInheritanceRule
    // Does the child community's Trust Profile (Part XI)
    // inherit baseline trust from the parent? Common for
    // newly formed sub-communities (a new Youth Group
    // within an established, highly trusted Church
    // benefits from inherited baseline trust, separate
    // from its own track record still being built)

    archive_inheritance:                  ArchiveInheritanceRule
    // Is the child's Community Archive (Volume V, Chapter 17)
    // a fully separate archive, or a filtered view within
    // the parent's archive? Most Faith sub-communities
    // (Choir, Youth Group) function best as filtered views
    // within a unified Church archive, preserving the
    // institution's complete history in one place while
    // still allowing sub-community-specific browsing
  }

  federation_role:                       FederationRole
  // 'standalone' — no federation relationships
  // 'parent' — has child communities
  // 'child' — has a parent community
  // 'peer_network_member' — part of a horizontal network
  //   without strict parent/child hierarchy (e.g., a
  //   denomination's regional churches, which may relate
  //   to each other and to a denominational body without
  //   a strict single-parent structure — this case is
  //   explicitly supported via multiple peer-level
  //   Community ↔ Community PARTNERSHIP relationships,
  //   Chapter 19, rather than forcing a federation
  //   hierarchy where none naturally exists)
}
```

The Federation model is deliberately flexible across these dimensions rather than prescriptive, because real institutional structures vary enormously — a school's relationship to its alumni community is structurally different from a church's relationship to its choir, which is different again from a denomination's relationship to its member churches. Forcing a single federation model onto all of these would misrepresent how each actually functions.

---

# Part XX — Community Lifecycle

---

## Chapter 22 — Creation Through Legacy

```
CREATION
  A community is founded. Founding members and founding_date
  recorded permanently (Chapter 6). The founding_story
  (Chapter 7) is typically authored at this stage, though it
  may be elaborated later as the community matures and its
  origin becomes more storied in the retelling.

  ↓

GROWTH
  Membership expands (Chapter 8). Early rituals begin forming
  (Part IX). The community's identity (Part V) solidifies as
  more gatherings accumulate and the founding_story is tested
  against lived experience.

  ↓

MATURITY
  Rituals have become recognised Traditions (Part X).
  Community Trust (Part XI) and Reputation (Part XII) have
  developed real track records, not just founding promise.
  The Community Archive (Volume V, Chapter 17) has genuine
  institutional depth. Governance (Part XIV) has typically
  formalised beyond founder-authority alone.

  ↓

TRANSFORMATION
  This stage is explicitly distinct from simple maturity —
  it covers communities undergoing significant change:
  leadership succession (Part XXII), federation restructuring
  (Part XIX), a shift in community_family or purpose (e.g.,
  an Event Community around a single festival broadening
  into a year-round Cultural community), or — without
  euphemism — periods of real difficulty (membership decline,
  governance disputes, Part XXIV's risks materialising).
  Transformation is not always growth, and the platform's
  Community Lifecycle model must not presume it always is.

  ↓

LEGACY
  The community's accumulated Legacy Capital (Part XIII)
  becomes its defining characteristic — founding members are
  permanently honoured (Chapter 6), traditions have survived
  multiple leadership generations (Part X, Part XXII), and
  the Community Archive (Volume V, Chapter 17) stands as
  genuine institutional memory, consultable by current
  members and, where public_presentation permits
  (Volume V, Chapter 17), by the wider platform as a
  HISTORICAL resource.

  A community at the Legacy stage is not necessarily
  inactive — many of ZUKA's strongest, longest-running
  communities (an established church, a decades-old alumni
  association) are simultaneously at peak activity AND at
  the Legacy stage. Legacy describes accumulated institutional
  depth, not decline.
```

```typescript
CommunityLifecycleStage enum:
  creation
  growth
  maturity
  transformation
  legacy

// Note: 'dormant' and 'archived' (Chapter 6's status field)
// are SEPARATE from lifecycle_stage — a community can be
// dormant at any lifecycle stage (a mature community going
// quiet for a season is different from a community still
// in its growth stage going quiet). Status and lifecycle
// stage are tracked independently and both inform, but
// neither alone determines, succession urgency (Part XXII).
```

---

# Part XXI — Community Archives

---

## Chapter 23 — Confirming the Volume V Linkage

The Community Archive entity is fully specified in Volume V, Chapter 17. This chapter exists to confirm, within this volume's context, that the Archive is governed structurally as a Community-Architecture concern even though its data model lives in the Memory volume — consistent with the same cross-volume pattern already established for Tradition entities (Part X, Chapter 12).

```
COMMUNITY ARCHIVE CONTENTS (re-stated for completeness):

  Photos
  Videos
  Documents
    (extends Volume V, Chapter 16's document_count field —
    meeting notes, founding charters, institutional records)
  Meeting Notes
    (a Professional/Faith community-specific content type,
    feeding Knowledge Capital — Chapter 15)
  Historical Records
    (HISTORICAL-type Memory entities — Volume V, Chapter 8)
  Oral Histories
    (voice recordings — Volume V, Chapter 16's
    voice_recording_count — particularly significant for
    Cultural and Family communities where oral tradition
    is a primary historical record, per Volume I's African
    context chapter)

COMMUNITY HISTORY BECOMES SEARCHABLE:
  The same Memory Time Machine capability (Volume V,
  Chapter 26) operates at Community Archive scale —
  "show me 30 years of this church's retreats," "show me
  every graduation this school has celebrated" — governed
  by the same future-capability status and the same
  Community Historian copilot (Part XVIII) as its
  primary interface.
```

---

# Part XXII — Community Succession

---

## Chapter 24 — Communities Outlive Individuals

This chapter directly extends Volume III, Chapter 21 (Community Inheritance — explicitly flagged there as an open question deferred to this volume) and Volume III, Chapter 25 (Family Archive Succession, as the Family-specific instance of this volume's general model).

**The questions, stated plainly, exactly as posed in the source brief:**
- What happens when leaders leave?
- Who inherits archives?
- Who preserves traditions?
- Who controls governance?

---

## Chapter 25 — The Succession Model

```typescript
CommunitySuccessionPolicy {
  community_id:                   UUID

  leadership_succession: {
    method:                          SuccessionMethod
    // 'designated_successor' — outgoing leader names their
    //   replacement, consistent with Volume III, Chapter 23's
    //   LegacySteward pattern applied to active (not just
    //   posthumous) succession
    // 'elder_council_decision' — the strategic_layer
    //   (Chapter 16) decides
    // 'membership_election' — full or partial membership vote
    // 'founder_authority' — reserved founder veto/decision
    //   right, where the governance model (Chapter 16)
    //   grants it

    notice_period_days:               Integer
    transition_support_period_days:    Integer
    // A defined handover window where outgoing and incoming
    // leadership have concurrent access — preventing the
    // operational gap that causes much real-world community
    // disruption during leadership change

    dispute_resolution:                 SuccessionDisputeProcess
    // Mirrors Volume III, Chapter 32's Family Ownership
    // Disputes pattern: contested succession enters a
    // READ_ONLY-equivalent state for the contested
    // authority (the community continues operating on
    // existing settings, but no new governance changes
    // are made) until resolved
  }

  archive_succession: {
    // Governed primarily by the archivist role (Chapter 8)
    // and the Community Archive's own stewardship field
    // (Volume V, Chapter 17) — this field exists to confirm
    // alignment between the two, since archive succession
    // and leadership succession are NOT always the same
    // event (a community can change its operational
    // leadership while its long-serving Archivist remains
    // in place, preserving archive continuity precisely
    // because it is decoupled from leadership transition)
    inherits_from_leadership_succession:    Boolean
    independent_archivist_succession:        Boolean
  }

  tradition_preservation: {
    // Per Volume IV, Chapter 11's Tradition entity and
    // this volume's Chapter 12 — Traditions are NEVER
    // cancelled by leadership succession. A new leader
    // inherits stewardship of existing Traditions by
    // default; discontinuing a Tradition is itself a
    // significant strategic_layer decision (Chapter 16),
    // not an automatic consequence of leadership change.
    default_continuity:                       'preserved'
  }

  governance_control: {
    // What governance authority transfers automatically
    // vs. requires explicit re-ratification at succession —
    // configurable, but defaults toward continuity
    // (minimising disruption) rather than requiring every
    // governance setting to be re-decided at every
    // leadership change
    auto_transfer_scope:                        TEXT[]
    requires_reratification_scope:                TEXT[]
  }
}
```

**The governing principle, stated as the chapter's thesis:** communities are explicitly designed to outlive any individual member, including their founders and their longest-serving leaders. Succession is not an edge case to be handled reactively when a crisis emerges — it is a first-class, plannable governance function that every community is encouraged (and, for communities reaching meaningful Legacy Capital per Part XIII, will be platform-prompted) to configure well before succession becomes urgent.

---

# Part XXIII — MVP Scope

---

## Chapter 26 — What to Build First

Consistent with the MVP discipline established in Volume IV, Chapter 22, and Volume V, Chapter 28, the Communities Architecture MVP is deliberately narrow relative to this volume's full scope.

```
BUILD (MVP):

  ✅ Community creation
     — Basic fields: name, type, description, join_policy
  ✅ Membership
     — Join/leave, basic member list
  ✅ Roles
     — Simplified set: Leader, Admin, Member only at MVP
       (full 10-role registry from Chapter 8 deferred)
  ✅ Events
     — Community-organised gatherings (linking to Volume IV's
       existing Gathering creation flow with community_id set)
  ✅ Albums
     — Community-level shared albums (extends Volume V's
       MVP album scope to community ownership)
  ✅ Shared memories
     — Basic community-owned Memory creation, using Volume V's
       simplified MVP ownership rule (all tagged participants
       can add media)
  ✅ Basic governance
     — Single-layer only: who can manage membership and
       create events on the community's behalf (full
       four-layer governance model from Chapter 16 deferred)
```

```
CAPTURE (data foundation, not yet surfaced as features):

  📊 Relationships          (Member ↔ Member formation via
                              shared community membership)
  📊 Participation           (gathering_participation_count
                              per member, per Chapter 8)
  📊 Trust signals             (raw reliability/safety inputs,
                                even if Community Trust Profile
                                scoring — Chapter 13 — is not
                                yet computed or displayed)
  📊 Ritual indicators           (recurring community-organised
                                  gathering patterns — feeds
                                  future Tradition detection,
                                  Volume IV/V's existing
                                  algorithms, applied at
                                  community scale)
```

```
DO NOT BUILD YET:

  ❌ Community AI (any of the four Copilots — Part XVIII)
  ❌ Community Capital (computed scoring — Part XIII —
                          premature without sufficient
                          community-scale data density)
  ❌ Federation Intelligence (full Federation Structure
                               mechanics — Part XIX — though
                               the parent_community_id /
                               child_community_ids fields
                               should exist in the schema
                               from MVP for forward
                               compatibility, even if the
                               UI to configure federation
                               rules is not yet built)
  ❌ Governance Councils (the full four-layer governance
                            model — Chapter 16 — beyond
                            MVP's simplified single-layer
                            approach)
  ❌ Community Wallets / Treasuries (Part XV's future
                                      capabilities)
  ❌ Succession tooling (Part XXII's formal
                          CommunitySuccessionPolicy
                          configuration UI — though
                          founding_members and
                          leadership_history data, per
                          Chapter 6 and Chapter 8, MUST
                          be captured from MVP, since this
                          data — like Volume V's emotion
                          tags — cannot be reconstructed
                          retroactively if not recorded
                          at the time)
```

**The single most important MVP discipline in this volume, parallel to Volume V's emotion-tag discipline:** capture `founding_members`, `founding_date`, and `leadership_history` from day one, in full, even though no succession tooling exists yet to act on this data. A community founded in the MVP era whose founding members are not permanently recorded loses that historical fact irretrievably — there is no way to reconstruct "who founded this community" five years later if it was not captured at the moment of creation.

---

# Part XXIV — Risks

---

## Chapter 27 — Community Fragmentation

**Risk:** A community splits — through disagreement, growth beyond manageable cohesion, or natural divergence of interest — into multiple competing or disconnected successor groups, with the platform's data model forced to represent something messier than a clean entity split.

**Current position:** The Federation model (Part XIX) provides a partial structural answer for *planned* fragmentation (a growing community formally splitting into sub-communities). Unplanned, contentious fragmentation — where two factions both claim to be the "real" continuation of the original community — is not yet fully resolved and likely requires the same dispute-resolution escalation pattern as Chapter 25's succession disputes, applied to the more severe case of the community entity itself being contested.

→ Deferred to a future Legal & Governance Architecture volume, extending the pattern from Volume III, Chapter 32 and this volume's Chapter 25.

---

## Chapter 28 — Leadership Disputes

**Risk:** Contested succession (Chapter 25), disputed authority within the governance layers (Chapter 16), or allegations of leadership misconduct that the platform must somehow adjudicate or, more appropriately, decline to adjudicate while still protecting the community's continuity.

**Current position:** The `SuccessionDisputeProcess` (Chapter 25) provides the structural mechanism (freeze contested authority, preserve operational continuity, await resolution). ZUKA explicitly does NOT position itself as an arbiter of who is "right" in a leadership dispute — its role is to preserve the community's data, membership, and archive intact while the dispute is resolved through the community's own governance processes or external means (courts, denominational authority, etc., depending on community type).

→ The boundary of ZUKA's appropriate involvement (preserve and freeze vs. actively adjudicate) requires explicit legal review, given the real-world stakes — a contested church leadership dispute, for example, can have significant legal and financial dimensions ZUKA's platform-level dispute resolution is not equipped to resolve. Deferred to a future Legal & Governance Architecture volume.

---

## Chapter 29 — Ownership Conflicts

**Risk:** Distinct from leadership disputes — disagreement specifically over WHO owns community assets (the Archive, the Wallet/Treasury once built, the community's name and identity itself) rather than who leads.

**Current position:** This extends Volume V, Chapter 29 (Memory Ownership Conflicts) and Volume III, Chapter 32 (Family Ownership Disputes) to the Community-asset case generally. The same `disputed` status pattern (Volume V, Chapter 5's MemoryStatus enum, extended conceptually to Community-level assets) applies: freeze, preserve, await resolution.

→ Deferred alongside Chapters 27–28 to a future Legal & Governance Architecture volume.

---

## Chapter 30 — Archive Disputes

**Risk:** Specifically, disagreement over Community Archive content — what should be public vs. private, what reflects the community accurately vs. what one faction wants suppressed, particularly acute during Chapter 27's fragmentation or Chapter 28's leadership dispute scenarios.

**Current position:** The Archivist role's steward-level rights (Chapter 8) provide day-to-day authority, but the archive_succession field's `independent_archivist_succession` option (Chapter 25) is specifically designed to let archive stewardship remain stable and trusted even when other governance layers are contested — protecting the historical record from being weaponised in an active dispute.

→ Extends Volume V, Chapter 30 (Privacy Concerns) and Chapter 29 (Ownership Conflicts) at community scale. Deferred to the same future Legal & Governance and Content & Moderation volumes.

---

## Chapter 31 — Trust Abuse

**Risk:** A community's earned Trust Capital (Part XI, Part XIII) is exploited — used to lend false credibility to a scam, a fraudulent fundraiser (Part XV), or harmful content, leveraging the community's accumulated reputation rather than building fraudulent reputation from scratch.

**Current position:** This is the community-scale instance of the same asymmetric trust decay principle established in Volume III, Chapter 15 (slow to build, fast to lose) — but the risk specifically is that the SPEED of trust loss must be fast enough to prevent meaningful harm during the window before the abuse is detected and the community's trust score reflects it. The Community Trust Profile's `integrity` dimension (Chapter 13) is the primary signal here, and fraud-detection patterns from Volume III, Chapter 30, applied at community scale.

→ Detection and response mechanics deferred to a future Fraud & Abuse Architecture volume, extending ZUKA.md §26.

---

## Chapter 32 — Political Capture

**Risk:** A community — particularly a large, influential one (a major denomination's regional body, a significant professional association, a large geographic community) — has its governance captured by a faction pursuing interests not representative of or beneficial to the broader membership, using ZUKA's own governance tooling (Chapter 16) to entrench that capture.

**Current position:** This is named explicitly as a risk precisely because robust governance tooling (Chapter 16) is, by its nature, dual-use — the same structures that enable healthy community self-governance can be used to formalise and protect an illegitimate power grab if the platform does not maintain some baseline safeguards. The `governance_change_policy` field (Chapter 16) — requiring defined notice periods and ratification from appropriate layers before governance changes take effect — is a partial structural defence (it prevents rapid, unilateral governance capture) but does not fully resolve the risk for communities whose entire strategic_layer has already been captured.

→ This is flagged as requiring the deepest further thought of any risk in this volume — likely intersecting with future Trust Architecture work's portable trust concepts and external verification, since a captured community's OWN governance cannot be relied upon to self-correct. Deferred, unresolved, to future Trust and Legal & Governance volumes, with an explicit acknowledgment here that this is not yet a solved problem.

---

## Chapter 33 — Cultural Sensitivity Issues

**Risk:** Particularly acute for Cultural and Faith communities (Part III) — ZUKA's platform-level features, defaults, and even this Architecture Bible's own framing could inadvertently impose an inappropriate structure on communities whose actual cultural or religious governance norms do not map cleanly onto ZUKA's generalised model (the four-layer governance structure of Chapter 16, for instance, is a useful generalisation but is not how every traditional or religious community actually organises itself).

**Current position:** This extends Volume IV, Chapter 26's open question on cultural ceremony modelling to the community-governance level. The Part III taxonomy's explicit note under Cultural Communities — that these communities are "often the appropriate authority on their own cultural ceremony templates" — should be read as the governing principle for this broader risk too: where ZUKA's generalised governance and structural models conflict with a specific community's authentic cultural or religious practice, the community's own authority and self-definition should take precedence over the platform's generalised defaults, with the platform's role being to flex and accommodate rather than to standardise.

→ Requires ongoing cultural consultation per market, consistent with Volume IV, Chapter 26's existing commitment. Not a one-time resolution but a continuous design discipline as ZUKA expands across diverse African (and eventually international) cultural and religious contexts.

---

# Volume VI Summary

```
The Community Thesis:
  Humans → Communities → Gatherings → Memories →
  Traditions → Identity → Culture
  Communities are civilisation engines, not features.

What Is a Community:
  Shared identity, purpose, memories, relationships,
  rituals, or geography
  10 components every community possesses
  Clearly distinguished from Gathering, Family,
  Relationship, and Tradition entities

Community Taxonomy:
  8 community families: Family, Faith, Educational,
  Geographic, Professional, Interest, Cultural, Event
  Each with distinct governance, scale, and gathering
  relationship defaults

Community Entity Model:
  Full canonical Community Record specification
  spanning identity, culture, membership, leadership,
  memory, traditions, trust, reputation, timeline,
  governance, economics, visibility, and federation

Community Identity:
  Identity creates belonging — visual identity, tagline,
  founding_story as a primary emotional asset

Membership Architecture:
  10 membership roles with default permission sets,
  from Founder through Guest

Community Memory:
  Community memory survives individual memory —
  the central thesis connecting Volumes V and VI

Community Timeline:
  Founded → Growth → Milestones → Traditions →
  Generations, event-sourced and milestone-aware

Community Rituals & Traditions:
  Repeated rituals create identity. Rituals become
  traditions. Traditions become culture.
  Direct extension of Volume IV/V's Tradition entity,
  community-owned

Community Trust & Reputation:
  6 trust dimensions at institutional scale
  Reputation tags influence discovery and growth
  Trust compounds slower-decaying than individual trust

Community Capital:
  7 capital types aggregated at community scale,
  including 2 new types introduced here: Knowledge
  Capital and Cultural Capital

Community Governance:
  4 governance layers — Operational, Financial,
  Historical, Strategic — independently configurable,
  explicit separation of powers as a defence against
  capture and concentration risk

Community Economics:
  Persistent cross-gathering financial layer
  Future: Community Wallets and Treasuries

Community Opportunity Engine:
  Communities as sustained opportunity networks —
  jobs, mentorship, partnerships, scholarships,
  referrals, volunteering

Community Graph:
  6 relationship types connecting communities to
  members, leaders, other communities, events,
  venues, and brands

Community AI (future):
  4 Copilots — Church, Alumni, Family, Club —
  same understanding-not-surveillance restraint
  as prior volumes' AI capabilities

Community Federation:
  Parent/child nesting with 4 configurable
  inheritance rules: membership, governance, trust,
  archive — deliberately flexible, not prescriptive

Community Lifecycle:
  5 stages: Creation, Growth, Maturity,
  Transformation, Legacy
  Explicit acknowledgment that Transformation is not
  always growth, and Legacy is not decline

Community Archives:
  Confirmed as a Community-Architecture-governed
  concern, full specification remains in Volume V

Community Succession:
  Communities outlive individuals — succession as a
  first-class, plannable governance function, not a
  reactive crisis response
  Leadership, archive, tradition, and governance
  succession explicitly decoupled from one another

MVP Scope:
  7 build items, 4 data-capture priorities,
  6 explicit deferrals
  Critical discipline: founding_members, founding_date,
  and leadership_history must be captured from day one —
  irreplaceable if missed, parallel to Volume V's
  emotion-tag discipline

Risks (7):
  Fragmentation · Leadership disputes · Ownership
  conflicts · Archive disputes · Trust abuse ·
  Political capture (flagged as least-resolved risk
  in the volume) · Cultural sensitivity issues
  (flagged as requiring continuous, not one-time,
  design discipline)
```

This is where ZUKA begins transitioning from an event platform into a social and cultural infrastructure platform.

---

# What Volume VII Must Address

Volume VII — Temporal Architecture (Time, Event Sourcing, Timelines, Traditions & Legacy) is, by the assessment carried into this volume's closing, arguably the deepest engineering volume in the entire Architecture Bible — because it is the volume that transforms ZUKA from a system that stores data into a system that understands history.

Volume VII must resolve:

1. **Formal Event Sourcing Architecture** — the complete technical specification for the append-only DomainEvent ledger introduced in Volume II, Chapter 8, including replay mechanics, snapshotting strategy, and event schema versioning
2. **Time as Queryable Dimension** — how ZUKA answers "what was true at time T" for any entity, not just "what is true now" (temporal/bitemporal database mechanics)
3. **The Unified Timeline Engine** — a single underlying system powering the Identity Timeline (Volume III), Memory Timelines (Volume V), and Community Timeline (Volume VI) as configured views over the same temporal substrate, rather than three separately implemented features
4. **Tradition Lifecycle Formalisation** — the complete state machine for how a Tradition forms, strengthens, weakens, lapses, and is revived, building on the detection signals scattered across Volumes IV, V, and VI
5. **Legacy as a Temporal Concern** — how the Memorialisation process (Volume III), Family Archive succession (Volume III/V), and Community succession (Volume VI) are unified under a single temporal/legacy data model rather than three parallel implementations
6. **Calendar & Recurrence Engineering** — the precise technical handling of recurring gatherings (RRULE mechanics already referenced in Volume IV, Chapter 4) across timezone changes, calendar system differences, and irregular real-world recurrence (a "first Sunday of the month" gathering, a lunar-calendar-based cultural observance)
7. **Anniversary & Milestone Computation at Scale** — the production-grade architecture for Volume V's Anniversary Engine and this volume's Community milestone detection, operating efficiently across millions of entities
8. **Historical Data Integrity** — versioning, audit, and tamper-evidence mechanics for the entire temporal substrate, given how much of ZUKA's value (per Volume V's central thesis) depends on historical records being trustworthy decades into the future

---

*Volume VI is canonical. Extensions require architecture review.*
*Contradictions with this volume must be resolved before implementation.*

---

> **ZUKA Architecture Bible**
> Volume VI — Communities Architecture
> Built in Kampala. Built for Africa. Built for the world.
