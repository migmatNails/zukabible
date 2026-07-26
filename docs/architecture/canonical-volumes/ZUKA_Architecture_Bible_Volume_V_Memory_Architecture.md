# ZUKA Architecture Bible
# Volume V — Memory Architecture

> **Series:** ZUKA Architecture Bible
> **Volume:** V of N
> **Status:** Canonical — the most strategically defensible domain in the platform
> **Depends on:** Volume I (Vision), Volume II (Domain Model), Volume III (Identity), Volume IV (Gatherings)
> **Required by:** Volume VI (Communities), Volume VIII (Trust), Volume X (AI & Intelligence),
>                  Volume XI (Data), Volume XII (Legal & Governance), all product specifications

---

# Preface

If Volume IV is the heart of ZUKA, Volume V is the soul.

This is the volume that separates ZUKA from Eventbrite, Meetup, Facebook Events, Ticketmaster, and every other platform that has ever modelled human gatherings as transactions.

Because events end.

**Memories don't.**

Every platform that has come before ZUKA has treated the photo, the post, the story as *content* — something created for consumption, optimised for engagement, and discarded when its attention value decays. ZUKA rejects this model entirely.

ZUKA optimises for **memory** — something created through lived experience, which does not decay, which appreciates, and which becomes more valuable to a person the longer they hold it.

This distinction is not aesthetic. It is the architectural foundation of everything in this volume, and it is the reason Volume V will likely become one of ZUKA's most defensible long-term assets. A competitor can copy a feed. A competitor can copy a ticketing flow. A competitor cannot copy fifteen years of someone's family memory archive.

---

# Part I — The Memory Thesis

---

## Chapter 1 — Content vs. Memory

```
CONTENT                          MEMORY

Created for consumption          Created through lived experience
Optimised for engagement         Preserved for meaning
Has an attention half-life       Has no expiry
Measured in views                Measured in significance
Belongs to a feed                Belongs to a life
Forgotten by the algorithm       Remembered by the person

Examples:                        Examples:
  Posts                            First date
  Reels                            Wedding
  Stories (24h)                    Graduation
  Trending videos                  Family reunion
                                    Festival
                                    Road trip
                                    Funeral
```

Most platforms collapse this distinction because their business model depends on it being collapsed. Attention-economy platforms need content to decay so that new content can replace it and command renewed attention. The feed must always feel fresh, which means yesterday's content must feel stale.

ZUKA's business model does not depend on this. ZUKA's value compounds *because* memories do not decay. A platform built on the Capital Model (Volume II, Part IX) needs exactly the opposite incentive structure — it needs memories to remain valuable indefinitely, because Memory Capital, Relationship Capital, and Legacy Capital are all functions of accumulated, undecayed history.

This is why Volume V exists as a separate architectural domain from any "media" or "content" system. It is not a feed. It is an archive that happens to be searchable, shareable, and beautiful.

---

## Chapter 2 — Memory Is Not Media

The single most important distinction in this volume:

```
A PHOTO IS EVIDENCE.
A MEMORY IS MEANING.
```

```
IMG_1234.jpg
  is data.
  A timestamp, a set of pixels, a GPS coordinate, a file size.

"My grandmother's last Christmas dinner."
  is human significance.
  A relationship, an emotion, a moment in a family's history,
  a piece of someone's identity.
```

Most platforms — including most photo storage products — never make this distinction. They store `IMG_1234.jpg` with a timestamp and a location and call that "memories." It is not. It is a media library.

ZUKA's architecture must separate these two things at the data model level, not just at the presentation layer. This separation is formalised in Chapter 9 (Memory Model) and is the single most consequential design decision in this volume: **Memory is the meaning-layer entity. Media is the evidence it points to. They are never the same record.**

---

## Chapter 3 — The Memory Value Curve

The clearest illustration of why ZUKA's architecture must diverge from every content platform that came before it:

```
CONTENT VALUE OVER TIME

Value
  │
  █
  █ Day 1
  █▄
  █ █  Day 30
  █ █▄
  █ █ █▄▄▄▄▄▄▄▄▄▄▄▄▄  Day 365
  █ █ █
  └─────────────────────────────► Time

  Peaks immediately. Decays fast. Approaches zero.
  This is why content platforms must produce
  an endless stream of new content to sustain engagement.


MEMORY VALUE OVER TIME

Value
  │                              █
  │                          █
  │                      █
  │                  █
  │              █
  │          █
  │      █
  │  █
  └─────────────────────────────► Time
    Day 1   Year 5   Year 20   Year 40

  Begins modest. Appreciates steadily. Compounds with time.
  This is why ZUKA does not need to produce more content —
  it needs to preserve what already happened, increasingly well.
```

This curve is not a metaphor. It is the basis for the Memory Capital scoring model (Chapter 20) and the reason ZUKA's retention strategy is fundamentally different from any attention-economy competitor. A platform whose core asset appreciates does not need to manufacture urgency. It needs to protect what it already holds.

---

# Part II — Memory ≠ Media (Architectural Consequence)

---

## Chapter 4 — The Two-Layer Separation

Because Memory and Media are categorically different things, ZUKA's data architecture maintains them as **two distinct entity layers that reference each other but never merge.**

```
┌──────────────────────────────────────────────────────┐
│                   MEDIA LAYER                          │
│                                                        │
│   Photo · Video · Audio Recording · Voice Note        │
│                                                        │
│   Properties: file, format, resolution, duration,      │
│   storage location, capture device, capture timestamp │
│                                                        │
│   Can be: replaced, re-compressed, re-hosted,          │
│   migrated to new storage providers, deleted and       │
│   regenerated from backup — WITHOUT loss of meaning    │
└──────────────────────────────────────────────────────┘
                          ▲
                          │ referenced by
                          │ (never embedded)
┌──────────────────────────────────────────────────────┐
│                  MEMORY LAYER                          │
│                                                        │
│   Memory entity (Volume II, §3.8)                      │
│                                                        │
│   Properties: participants, communities, gathering,    │
│   emotions, story, significance, relationships,        │
│   life chapter, tradition linkage                      │
│                                                        │
│   Cannot be regenerated. Cannot be reconstructed        │
│   from media alone. Represents irreplaceable human      │
│   meaning that must be actively preserved, never        │
│   passively stored                                      │
└──────────────────────────────────────────────────────┘
```

This separation has a direct operational consequence, stated plainly: **media can be lost and recovered from backup. Meaning, once lost, is gone forever.** This single principle governs every storage, backup, and disaster recovery decision in Chapter 27.

If ZUKA's media storage provider fails catastrophically and photos are lost, the Memory entities — the participants, the story, the emotional tags, the relationship graph, the tradition linkage — survive. A grieving family that loses the photos from their mother's last Christmas dinner has lost something painful, but they have not lost the fact that the gathering happened, who was there, what it meant, and its place in their Family Archive. The platform can re-solicit photos from other attendees. It cannot regenerate meaning that was never captured.

This is why meaning-capture (Chapter 5 onward) must happen at or near the point of the gathering — not retroactively inferred from media years later.

---

# Part III — Memory Model

---

## Chapter 5 — The Canonical Memory Record

This extends the Memory entity defined in Volume II, Chapter 3.8, with the full structure required to support the Memory Layer described in this volume.

```typescript
Memory {

  // ── IDENTITY ──────────────────────────────────────────────
  memory_id:             UUID
  // Permanent. Never recycled. A Memory, once created,
  // exists indefinitely (archived, never deleted, except
  // by explicit and confirmed user request).

  // ── MEANING (the irreducible core) ───────────────────────
  title:                 TEXT
  // Human-authored. The name a person gives to this memory.
  // "My grandmother's last Christmas dinner"
  // NOT auto-generated from a filename or timestamp.

  significance:          TEXT
  // Why this matters. Optional but the single highest-value
  // field in the entire Memory Layer. A short note explaining
  // the meaning, written by the person who experienced it.

  importance_level:      ImportanceLevel
  // 'everyday' | 'meaningful' | 'significant' | 'defining'
  // Self-assessed by the creator. Influences Memory Capital
  // weighting and surfacing priority in the Anniversary Engine.

  // ── PARTICIPANTS ───────────────────────────────────────────
  participants: {
    creator_identity_id:   UUID
    // Who created this Memory record (not necessarily who
    // attended — a family steward may create a Memory on
    // behalf of the family).

    tagged_person_ids:     UUID[]
    // Everyone this Memory is about. Each tag carries its
    // own consent state — see Chapter 15.

    relationship_context:  RelationshipSnapshot[]
    // Captures the relationships AS THEY WERE at the time
    // of the memory — not as they are now. A divorced couple's
    // wedding Memory still records them as SPOUSE at that time.
  }

  // ── CONTEXT ───────────────────────────────────────────────
  gathering_id:           UUID
  // Links to the Gathering entity (Volume IV) this Memory
  // originated from. Null for memories not tied to a
  // specific gathering (e.g., a memory of a relationship
  // milestone with no associated event).

  community_ids:          UUID[]
  // Which communities this memory belongs to.
  // A wedding memory may belong to: the Couple, both Families,
  // and the Church community that hosted the ceremony.

  location: {
    location_pin:          PostGIS_Point
    location_name:          TEXT
    city_id:                UUID
    venue_id:                UUID
  }

  timestamp: {
    occurred_at:            Timestamptz
    // When the memory actually happened — may differ from
    // created_at (memories can be backdated, e.g., uploading
    // old family photos with their true historical date).
    created_at:              Timestamptz
    // When the Memory record was created in ZUKA.
    is_backdated:            Boolean
    backdating_confidence:   Confidence
    // 'exact' | 'approximate' | 'estimated'
  }

  // ── MEDIA REFERENCES (not embedded — see Chapter 4) ──────
  media_refs: {
    photo_ids:               UUID[]
    video_ids:                UUID[]
    audio_ids:                 UUID[]
    cover_media_id:             UUID
    // The single image/video that represents this Memory
    // in summary views and timelines.
  }

  // ── NARRATIVE ─────────────────────────────────────────────
  story_id:                UUID
  // Links to a Story entity (Volume II §3.8) if a longer
  // narrative account exists.

  // ── EMOTIONAL ARCHITECTURE ────────────────────────────────
  emotions:                 EmotionTag[]
  // See Chapter 10 — Emotional Architecture
  // Multi-select. Memories carry complex, sometimes
  // contradictory emotional signatures (a funeral can carry
  // both 'grief' and 'gratitude').

  // ── TRADITION & CHAPTER LINKAGE ───────────────────────────
  tradition_id:             UUID
  // Set if this Memory is part of a recurring Tradition
  // (Volume II, Chapter 11 / Volume IV, Chapter 7).
  tradition_occurrence_number: Integer
  // "This is the 4th Christmas Dinner Memory."

  life_chapter:              LifeChapterType[]
  // Can belong to multiple chapters for different participants —
  // a wedding is PARTNERSHIP for the couple, possibly CHILDHOOD
  // for a young flower girl tagged in the photos.

  // ── VISIBILITY ─────────────────────────────────────────────
  visibility:                MemoryVisibility
  // See Chapter 14 — Shared Memory Ownership

  // ── TAGS ───────────────────────────────────────────────────
  tags:                      Tag[]
  // From Volume II's Universal Tagging System.

  // ── INTELLIGENCE ───────────────────────────────────────────
  embedding:                  Vector(1536)
  // Synthesised from: story text, emotion tags, tagged
  // participants' embeddings, gathering embedding, visual
  // embeddings of attached media.

  // ── AUDIT ────────────────────────────────────────────────
  status:                     MemoryStatus
  // 'active' | 'archived' | 'disputed' | 'memorialised'
  created_at:                 Timestamptz
  updated_at:                 Timestamptz
  version:                    Integer
}
```

---

## Chapter 6 — Media Entity (Reference Layer)

The Media Layer entities (Photo, Video, Audio) are defined structurally in Volume II §3.8. This chapter specifies how they relate to Memory in practice.

```typescript
// Extends Volume II's Photo/Video entity definitions
// with Memory-Layer-specific reference behaviour.

MediaReferenceRules {

  // A single piece of media can be referenced by
  // MULTIPLE Memory entities.
  // Example: A group photo from a wedding may be referenced
  // by the Couple's Wedding Memory, by an individual guest's
  // "Reunited with old friends" Memory, AND by the Photographer's
  // portfolio — three different meanings, one photo.

  many_to_many:               true

  // Media can exist WITHOUT a Memory.
  // A photo uploaded during a gathering's live phase
  // (a Vibe Drop, or a raw upload) exists in the Media Layer
  // immediately. It is NOT a Memory until someone — the
  // uploader, a tagged participant, or a family steward —
  // performs the act of curation that elevates it to Memory.

  media_without_memory:        true

  // A Memory can exist with NO media at all.
  // A Story-only memory, or a memory of an oral family
  // history with no photographic record, is fully valid.

  memory_without_media:        true

  // Deleting a piece of media does NOT delete the Memory
  // record it was referenced by. The Memory persists with
  // a "media unavailable" placeholder and a note of what
  // was lost, if known.

  media_deletion_preserves_memory: true
}
```

---

# Part IV — Memory Layers

---

## Chapter 7 — The Six-Layer Stack

Memory in ZUKA is not a single concept. It is a stack of increasing abstraction, where each layer is built from the one below it.

```
┌─────────────────────────────────────────────────────────┐
│ LAYER 6 — LEGACY                                          │
│ Memories surviving generations                            │
│ Family Archive entries that outlive their creator         │
├─────────────────────────────────────────────────────────┤
│ LAYER 5 — TRADITIONS                                      │
│ Repeated memories, recognised as a pattern                 │
│ "The Annual Christmas Dinner" — 11 occurrences             │
├─────────────────────────────────────────────────────────┤
│ LAYER 4 — MEMORIES                                         │
│ Meaningful experiences, curated and named                  │
│ "My grandmother's last Christmas dinner"                   │
├─────────────────────────────────────────────────────────┤
│ LAYER 3 — EXPERIENCES                                       │
│ A collection of moments forming a coherent whole            │
│ "Wedding Day" — ceremony + reception + after-party          │
├─────────────────────────────────────────────────────────┤
│ LAYER 2 — MOMENTS                                            │
│ Single meaningful occurrences within an experience            │
│ "The first dance" · "Cutting the cake" · "The toast"         │
├─────────────────────────────────────────────────────────┤
│ LAYER 1 — RAW MEDIA                                           │
│ Photos. Videos. Audio. The unprocessed evidence.              │
└─────────────────────────────────────────────────────────┘
```

Each layer is a real, separately addressable construct in the data model — not just a conceptual framing.

```
Layer 1 → Layer 2:
  Raw media is grouped into Moments through:
    · Timestamp clustering (photos within a 5-minute window)
    · Explicit RitualElement tagging (Volume IV, Chapter 11)
      — "this photo is from the cake-cutting moment"
    · Manual curation by the uploader

Layer 2 → Layer 3:
  Moments are grouped into Experiences through:
    · Shared gathering_id (all moments from the same Gathering)
    · Temporal proximity (same day, same multi-day event)
    · Manual grouping by a curator

Layer 3 → Layer 4:
  An Experience becomes a Memory when:
    · A human assigns it meaning — a title, a significance
      note, emotion tags, importance level
    · This is the critical curation step. Not all Experiences
      become Memories. A forgettable Tuesday house party may
      remain an Experience with photos but no curated Memory.
      A defining moment always does.

Layer 4 → Layer 5:
  A Memory becomes part of a Tradition when:
    · The Tradition detection algorithm (Volume IV, Chapter 11)
      identifies 2-3+ occurrences of a similar Memory pattern
    · The Tradition entity links all constituent Memories

Layer 5 → Layer 6:
  A Tradition (or an individual Memory) becomes Legacy when:
    · The creator's Identity transitions to MEMORIALISED
      (Volume III, Chapter 24), or
    · The Memory is explicitly designated for Family Archive
      inheritance (Chapter 19), or
    · A Tradition persists across a generational transition
      in its owning Community or Family entity
```

---

# Part V — Memory Types

---

## Chapter 8 — Memory Type Registry

Memory types are not the same as Gathering types (Volume IV, Chapter 6), though they are often correlated. A Memory type describes the *category of significance*, while a Gathering type describes the *category of event*. A single Wedding gathering can produce multiple Memories of different types — a Personal Memory for the bride, a Family Memory for both families, a Community Memory for the church congregation.

```
PERSONAL
  Memories centred on an individual's personal milestones.
  Examples: Birthday, Graduation, Promotion, First Apartment,
  Personal Achievement, First Date
  Default visibility: PRIVATE or FRIENDS
  Default life chapter linkage: strong

FAMILY
  Memories that belong to a Family entity collectively.
  Examples: Wedding, Reunion, Christmas, Family Vacation,
  New Baby, Anniversary
  Default visibility: FAMILY
  Always eligible for Family Archive inclusion (Chapter 16)

COMMUNITY
  Memories that belong to a Community entity.
  Examples: Retreat, Choir Trip, Village Celebration,
  Alumni Gathering, Club Event
  Default visibility: COMMUNITY
  Always eligible for Community Archive inclusion (Chapter 17)

CULTURAL
  Memories tied to broader cultural identity and observance.
  Examples: Festival, National Holiday, Traditional Ceremony,
  Cultural Heritage Day
  Default visibility: PUBLIC or COMMUNITY
  Carries mandatory culture: namespace tag (Volume II)

FAITH
  Memories tied to religious and spiritual practice.
  Examples: Church Conference, Pilgrimage, Baptism,
  Confirmation, Crusade
  Default visibility: COMMUNITY
  Highest emotional weight tends toward: spiritual, gratitude, peace

HISTORICAL
  Memories that mark milestones for an institution, city,
  or community rather than an individual or family.
  Examples: "30 years of this church," "City's centenary
  celebration," "School's 50th anniversary"
  Default visibility: PUBLIC
  Owner entity: Community or City, not a Person
  Always flows directly into Layer 6 (Legacy) — these memories
  are institutional by nature and built to outlive any
  individual member
```

---

# Part VI — Emotional Architecture

---

## Chapter 9 — Modelling Emotion Without Manipulating It

Every memory carries an emotional signature. This is not optional metadata — it is core to what makes a Memory different from an Experience (Chapter 7, Layer 3 → Layer 4 transition is, in large part, the act of attaching emotional meaning).

The governing principle, stated explicitly because it is easy to violate: **Emotion should be modelled. Not manipulated. Understood.**

This principle has direct product consequences:

```
WHAT THIS MEANS IN PRACTICE

✓ Emotion tags are self-selected by the person who
  experienced the memory. ZUKA never assigns an emotion
  tag on someone's behalf without their input.

✓ Emotion data is never used to target advertising
  to the person who expressed it. A person who tags a
  memory with 'grief' is never shown grief-adjacent
  commercial content as a result.

✓ Emotion data is never used to manufacture urgency
  or manipulate engagement ("You haven't felt this much
  joy in 3 months — come back!"). This crosses directly
  into the dark-pattern territory ZUKA's constitution
  must reject.

✗ ZUKA does not use emotion tags to optimise for
  emotional intensity in the feed (i.e., never surfacing
  "more emotionally engaging" content to keep someone
  scrolling). Memory is not a feed. There is no scroll
  to optimise.

✓ Emotion tags ARE used to: power the Anniversary Engine
  (Chapter 21) sensitively, build emotionally coherent
  Family/Community Archives, and inform Memory Capital
  weighting (a 'defining' memory with deep emotional
  signature appreciates more meaningfully than a routine one).
```

---

## Chapter 10 — Emotion Tag Registry

```typescript
EmotionTag {
  tag_id:            UUID
  emotion:           EmotionType
  intensity:         Float(0.0–1.0)  // self-assessed, optional
  is_primary:        Boolean         // the dominant emotion, if multiple selected
}

EmotionType enum:
  joy
  love
  pride
  belonging
  wonder
  gratitude
  nostalgia
  sadness
  hope
  grief
  peace
  excitement
  relief
  bittersweet      // explicitly supported — many of the most
                    // significant memories are not single-emotion
                    // (a graduation can be pride AND sadness
                    // at a chapter ending; a funeral can be
                    // grief AND gratitude for a life lived)
```

Multiple emotion tags per Memory are not just allowed but expected for significant memories. The `is_primary` flag identifies the dominant note without forcing a false single-emotion simplification.

---

# Part VII — Memory Graph

---

## Chapter 11 — Memories as Relationship-Dense Objects

A single Memory connects far more entities than its participant list suggests. This is the Memory Graph — the sixth graph defined in Volume II, Chapter 13, now specified in operational detail.

```
A SINGLE WEDDING MEMORY CONNECTS:

James (Person)
  ↓ [tagged_as: groom]
Wedding Memory
  ↓ [tagged_as: bride]
Sarah (Person)
  ↓ [creates/links]
Kiyingi-Nakato (Couple entity)
  ↓ [member_of]
Nakato Family (Family entity)
  ↓ [hosted_at]
Watoto Church Kololo (Community entity)
  ↓ [attended_by]
47 other tagged guests (Person entities)
  ↓ [photographed_by]
[Photographer Creator entity]
  ↓ [catered_by]
[Vendor entity]
  ↓ [part_of]
Kiyingi-Nakato Anniversary Tradition (future Tradition entity,
  formed after the 2nd anniversary memory is created)
```

One Memory. Eleven or more distinct entity connections. This density is precisely why the Memory Graph compounds in value over time — every new Memory added does not just add one node, it adds dozens of edges that strengthen the entire relational fabric of the platform.

---

## Chapter 12 — Memory Graph Queries

```sql
-- "Show me all memories that connect James and his grandmother"
-- (a core Memory Time Machine query — see Chapter 26)

SELECT m.*
FROM memories m
WHERE :james_person_id = ANY(m.tagged_person_ids)
  AND :grandmother_person_id = ANY(m.tagged_person_ids)
ORDER BY m.occurred_at DESC;

-- ────────────────────────────────────────────────────────

-- "What is the richest connected memory in James's archive?"
-- (most distinct entity connections — likely his most
--  significant life memory)

SELECT m.id, m.title,
  array_length(m.tagged_person_ids, 1) +
  array_length(m.community_ids, 1) +
  (CASE WHEN m.tradition_id IS NOT NULL THEN 5 ELSE 0 END)
  AS connection_density
FROM memories m
WHERE m.participants->>'creator_identity_id' = :james_identity_id
ORDER BY connection_density DESC
LIMIT 1;

-- ────────────────────────────────────────────────────────

-- "Build the Nakato Family's complete memory graph"
-- (for Family Archive construction — Chapter 16)

SELECT m.*
FROM memories m
WHERE :nakato_family_entity_id = ANY(m.community_ids)
   OR m.participants->>'creator_identity_id' IN (
        SELECT person_id FROM family_members
        WHERE family_id = :nakato_family_entity_id
      )
ORDER BY m.occurred_at ASC;
```

---

# Part VIII — Shared Memories

---

## Chapter 13 — The Ownership Problem

Many memories belong to multiple people simultaneously. A Family Reunion attended by 50 people produces a shared Memory — but the platform's economic and architectural model has historically required a single owner per record. This is the central tension this chapter resolves.

**The questions, stated plainly:**
- Who owns it?
- Who can edit it?
- Who can archive it?

---

## Chapter 14 — Shared Ownership Model

```typescript
MemoryOwnership {

  memory_id:                  UUID

  // ── PRIMARY OWNERSHIP ────────────────────────────────────
  creator_identity_id:        UUID
  // The person (or family/community steward) who created
  // this Memory record. Has the deepest editing rights.

  owner_entity_id:             UUID
  // Can be a Person, a Family, or a Community entity.
  // A Memory created BY a person but ABOUT a Family gathering
  // can have owner_entity_id set to the Family entity —
  // meaning the family, not the individual, is the canonical
  // long-term owner.

  // ── CO-OWNERSHIP ──────────────────────────────────────────
  co_owners: CoOwner[]
  // Other identities with elevated rights over this Memory.

  CoOwner {
    identity_id:               UUID
    permission_level:          MemoryPermissionLevel
    granted_by:                 UUID
    granted_at:                  Timestamptz
  }

  MemoryPermissionLevel enum:
    viewer            // can see the memory per its visibility setting
                       // (this is the default for ALL tagged participants)
    contributor        // can add media, add a story, add tags
    editor              // can edit title, significance, emotion tags
    steward              // can change visibility, archive, manage
                          // co-ownership, and (for Family/Community
                          // owned memories) act as the long-term
                          // custodian

  // ── DEFAULT RIGHTS BY RELATIONSHIP TO THE MEMORY ─────────
  // (applied automatically — co_owners list is for EXPLICIT
  //  grants beyond these defaults)

  default_rights: {
    creator:                    'steward'
    tagged_participants:         'contributor'
    // Anyone tagged can add their own photos/story to a
    // shared memory — this is how a 50-person reunion memory
    // becomes richly documented without requiring one person
    // to do all the work.
    family_members_if_family_owned: 'viewer'  // unless co-owner
    community_members_if_community_owned: 'viewer'
    everyone_else:                'no_access'  // subject to
                                                // visibility settings
  }

  // ── ARCHIVE & DELETION RULES ─────────────────────────────
  archive_authority: ArchiveAuthority
  // 'creator_only' | 'any_steward' | 'majority_co_owner_vote'
  // Default: 'any_steward'

  deletion_authority: DeletionAuthority
  // Deletion of a SHARED memory (owner_entity_id is Family or
  // Community) requires MORE than a single steward's action:
  //   single_steward: insufficient for shared memories
  //   majority_steward_vote: required if 2+ stewards exist
  //   dispute_resolution: triggered if stewards disagree
  // Personal memories (owner_entity_id is the individual Person):
  //   single_steward: sufficient — the person owns their
  //   own personal memory outright
}
```

---

## Chapter 15 — Tag Consent and Removal

Every person tagged in a Memory they did not create retains certain irrevocable rights, regardless of the memory's ownership structure:

```
TAGGED PARTICIPANT RIGHTS (cannot be overridden by owner/steward)

1. Right to see the memory (if their visibility settings
   and the memory's visibility settings both permit)

2. Right to remove their own tag at any time
   — removing a tag does not delete the Memory,
   only their association with it

3. Right to flag the memory for privacy review
   if they believe their inclusion is harmful or
   non-consensual (see Volume III, Chapter 31)

4. Right to add their own contributing content
   (photos, story) per the default 'contributor' right

5. Right to request the memory's visibility be reviewed
   if it has been set more broadly than they consented to
   — does not guarantee a change, but guarantees a response
   within a defined SLA (see Volume IX — Content & Moderation)
```

---

# Part IX — Family Archives

---

## Chapter 16 — The Family Vault

Every Family entity (Volume II, §3.1) has an associated Family Vault — a dedicated, structured archive that aggregates Memories across the family's history.

```typescript
FamilyVault {
  vault_id:                UUID
  family_entity_id:         UUID

  contents: {
    photo_count:             Integer
    video_count:             Integer
    story_count:              Integer
    voice_recording_count:    Integer
    document_count:           Integer
    // Birth certificates, marriage certificates, land titles —
    // family documents that have generational significance.
    // Stored with the same archival rigor as memories.
    memorial_count:           Integer
    // Memorial profiles of deceased family members
    // (Volume III, Chapter 24)

    memory_ids:               UUID[]
    tradition_ids:            UUID[]
    // All Traditions belonging to this family —
    // Annual Christmas Dinner, Annual Reunion, etc.
  }

  organisation: {
    by_generation:            GenerationGroup[]
    // Memories grouped by which generation of the family
    // they primarily concern
    by_branch:                FamilyBranch[]
    // For large extended families — sub-groupings by
    // lineage branch (useful for clan-scale family entities)
    by_decade:                DecadeGroup[]
    by_tradition:              TraditionGroup[]
  }

  stewardship: {
    current_stewards:         UUID[]
    // Identity_ids of current Family Vault stewards
    succession_policy:         SuccessionPolicy
    // From Volume III, Chapter 25 — Family Archive Succession
  }

  intergenerational_features: {
    family_tree_linkage:       Boolean
    // Future: link to a structured family tree, connecting
    // Memories to specific ancestors and descendants
    oldest_memory_date:         Date
    // The earliest dated memory in the vault — often
    // backdated from inherited physical photographs
    generations_represented:    Integer
  }
}
```

The Family Vault is the clearest expression of Volume I's thesis that ZUKA is building toward a 100-year horizon. A family that begins using ZUKA today, and continues for three generations, will have a Family Vault that no other system — not a shared Google Drive folder, not a WhatsApp group, not a physical photo album — can match in structure, searchability, and resilience.

---

# Part X — Community Archives

---

## Chapter 17 — Institutional Memory

Churches, schools, choirs, villages, and clubs need the same archival depth as families — but at institutional scale, and with institutional succession dynamics rather than family ones.

```typescript
CommunityArchive {
  archive_id:                UUID
  community_entity_id:        UUID

  contents: {
    memory_ids:                UUID[]
    tradition_ids:              UUID[]
    // "30 years of retreats." "20 years of graduations."
    // "15 years of choir performances."
    historical_milestone_ids:    UUID[]
    // HISTORICAL-type memories (Chapter 8) specifically —
    // anniversaries, founding dates, milestone celebrations

    member_contribution_index:   Map<identity_id, contribution_count>
    // Who has contributed most to the community's documented
    // history — surfaces natural community historians
  }

  organisation: {
    by_era:                      EraGroup[]
    // For institutions with distinct leadership eras
    // (e.g., "Under Pastor X's leadership, 2005–2018")
    by_tradition:                 TraditionGroup[]
    by_decade:                     DecadeGroup[]
  }

  public_presentation: {
    is_publicly_browsable:         Boolean
    // Many institutional archives (church history, school
    // alumni history) benefit from public visibility —
    // this is distinct from individual Memory privacy and
    // is set at the Community level by community leadership
    featured_milestones:            UUID[]
    // Curated highlights for the community's public profile
  }

  stewardship: {
    archive_curators:              UUID[]
    // Identity_ids with editor/steward rights over the
    // community's institutional memory
    succession_policy:              SuccessionPolicy
    // From Volume III — community leadership succession
    // applies equally to archive stewardship
  }
}
```

A church with a Community Archive spanning 30 years of retreats does not just have a feature. It has an institutional memory that previously existed only in the recollection of its oldest members. When those members are no longer able to recall it, the church's history was, in the pre-ZUKA world, simply lost. The Community Archive is a direct technological answer to that loss — and it is, again, a feature no competitor entertainment platform has any reason to build, because no competitor is organised around the thesis that memory is the product.

---

# Part XI — Memory Timelines

---

## Chapter 18 — Where Every Memory Belongs

Every Memory exists simultaneously on multiple Timelines. This is not duplication — it is the natural consequence of a Memory's rich connection density (Chapter 11).

```
TIMELINE TYPES

Personal Timeline
  The individual's own chronological memory record.
  This is the Identity Timeline from Volume III, Chapter 18,
  specifically filtered to Memory-type events.

Family Timeline
  The Family Vault's chronological view (Chapter 16).
  A single Memory (e.g., a wedding) appears on BOTH the
  individual's Personal Timeline AND the Family Timeline —
  same Memory entity, different timeline contexts.

Community Timeline
  The Community Archive's chronological view (Chapter 17).

Relationship Timeline
  A filtered timeline showing only Memories that involve
  two specific people together.
  "James and Brian's shared history" — every memory where
  both are tagged, in chronological order.
  This is one of the most emotionally resonant views in
  the entire product — friendship and family bonds made
  visible as an accumulated record.

City Timeline
  A civic-scale timeline — HISTORICAL memories tagged to
  a City entity. "Kampala's cultural moments" —
  aggregated from PUBLIC-visibility memories across the
  platform. This is also a direct input to the B2B
  Pulse Reports product (referenced in ZUKA.md §16).
```

### Example Timeline Construction

```
James Kiyingi — Personal Timeline (Memory-filtered view)

2019 ─── First University Day
          [Memory: PERSONAL · life_chapter: UNIVERSITY]
  │
2020 ─── First Campus Trip (Jinja)
          [Memory: PERSONAL · with: Brian, Sarah, Joseph]
  │
2023 ─── Graduation
          [Memory: PERSONAL · life_chapter transition:
           UNIVERSITY → EARLY_CAREER]
  │
2025 ─── 5 Years Since Jinja
          [Anniversary Engine surfaces the 2020 memory]
```

This view — simple, chronological, emotionally legible — is the product surface that makes the entire Memory Layer architecture worth building. Everything in Chapters 1–17 exists to make this timeline rich, accurate, and meaningful.

---

# Part XII — Memory Chapters

---

## Chapter 19 — AI-Assisted Chapter Clustering

This extends the Life Chapter model defined in Volume III, Chapter 19, with the Memory-Layer-specific clustering mechanics.

```
The Life Chapter framework already exists (Volume III).
Volume V's contribution is the AI clustering layer that
makes chapter assignment increasingly automatic as the
Memory Graph grows dense enough to support it.

CLUSTERING SIGNALS USED:

  Temporal density:        Memories cluster naturally around
                            life transitions (many memories in
                            a short window often indicate a
                            significant chapter — e.g., wedding
                            season, a graduation year)

  Relationship shifts:      A sudden change in who appears
                            in memories together (new romantic
                            partner appearing repeatedly,
                            university friends fading from
                            recent memories) signals a chapter
                            boundary

  Location shifts:          A consistent change in the
                            geographic location of memories
                            (moving cities, moving countries)

  Community membership
  changes:                  Joining or leaving community
                            entities aligns with chapter
                            boundaries (joining a workplace
                            community → EARLY_CAREER begins)

  Explicit triggers:        GraduationCeremony, marriage,
                            child birth — explicit domain
                            events (Volume II, Chapter 9)
                            that map directly to chapter
                            transitions

OUTPUT:

  Suggested chapter boundaries, ALWAYS presented to the user
  for confirmation — never auto-applied without consent
  (consistent with Volume III, Chapter 19's principle that
  chapter transitions are suggested, never automatic).

  "Life becomes navigable" — Memories are not just a flat
  list. They are organised into the chapters a person
  actually recognises as the shape of their own life.
```

---

# Part XIII — Memory Capital

---

## Chapter 20 — The Internal Scoring Model

Memory Capital was introduced conceptually in Volume II, Chapter 17, and referenced throughout Volume III and IV. This chapter specifies its computation.

**Memory Capital is never displayed as a raw number to the user or to any external party.** It is an internal scoring construct used to power recommendations, surfacing priority, and long-term platform intelligence. This restriction is deliberate and absolute — turning Memory Capital into a visible, gamified score would corrupt the very thing it measures, creating an incentive to perform memory-creation rather than genuinely document meaningful life experience.

```
MEMORY CAPITAL COMPONENTS

memory_capital_score = weighted_sum(

  experience_breadth × 0.20
    // Diversity of gathering types and memory types
    // represented in the archive

  experience_depth × 0.25
    // Number and richness of memories (media count,
    // story length, emotion tag richness, significance
    // note quality)

  relationship_density × 0.20
    // How many distinct, meaningful relationships are
    // represented across the memory archive

  community_density × 0.15
    // How many distinct communities the memory archive
    // connects to

  temporal_span × 0.10
    // How long the archive extends — a 10-year archive
    // scores higher than a 6-month one, independent of
    // memory count, because longevity itself is valuable

  tradition_depth × 0.10
    // Participation in recognised Traditions — recurring
    // memories carry outsized weight, consistent with
    // Volume IV's thesis that traditions compound community
    // and identity strength
)

RECOMPUTATION:
  Triggered on every MemoryCreated, MemoryTagged, and
  TraditionMarked domain event (Volume II, Chapter 9).
  Full recomputation: weekly batch job for all active identities.
```

---

# Part XIV — Anniversary Engine

---

## Chapter 21 — Resurfacing Memory With Dignity

The Anniversary Engine is one of the most powerful retention systems in the entire ZUKA platform — and one of the most ethically sensitive. It must be engineered with explicit care.

```typescript
AnniversaryEngine {

  trigger_rules: {
    standard_anniversaries:    [1, 2, 3, 5, 10, 15, 20, 25, 30, 40, 50]
    // Years since occurred_at. Standard milestone years
    // surface with higher priority.

    relationship_anniversaries: Boolean
    // "5 years since you and Brian first attended an
    // event together"

    tradition_anniversaries:    Boolean
    // "This is the 11th occurrence of your Christmas Dinner
    // tradition" — surfaced near the expected next occurrence

    chapter_anniversaries:       Boolean
    // "5 years since you graduated"
  }

  surfacing_logic: {
    notification_eligible:       Boolean
    // Whether this resurfaced memory should trigger a
    // push notification, or only appear passively when
    // the user opens their Memory Vault

    sensitivity_check:           SensitivityCheck
    // CRITICAL — see below
  }
}
```

### The Sensitivity Check — Non-Negotiable

The example given in the original brief — *"10 years ago today. Your first trip to Jinja. You were with: Brian, Sarah, Joseph"* — is exactly the right tone for a joyful memory. But the same mechanical system, applied without sensitivity logic, would resurface a memory of a funeral, a breakup, or a Memory involving a person who has since been removed from the user's life, with the same cheerful framing. This would be a serious failure of platform care, directly contradicting the wellbeing principles this platform must hold.

```
MANDATORY SENSITIVITY RULES:

1. Memories with emotion tags of 'grief' or 'sadness' as
   primary emotion are NEVER surfaced with celebratory
   framing. If surfaced at all, framing is gentle and
   optional: "A memory from [date] is in your archive
   if you'd like to revisit it" — never a push notification,
   never with festive visual treatment.

2. Memories tagged with a person who has since had their
   relationship to the user marked ENDED or DISPUTED
   (Volume II, Chapter 6 — Relationship Lifecycle) are
   suppressed from proactive resurfacing entirely. They
   remain accessible in the Memory Vault but are never
   pushed.

3. Memories involving a person who is now MEMORIALISED
   (Volume III, Chapter 24) are surfaced with elevated
   sensitivity — warm, never algorithmic-sounding framing,
   and never bundled with unrelated celebratory content
   in the same notification batch.

4. A user can globally disable the Anniversary Engine,
   or selectively disable it per Life Chapter, at any time
   in their notification preferences (ZUKA.md §21).

5. No anniversary notification is ever timed using
   engagement-optimisation logic (e.g., sending it at the
   statistically highest-open-rate hour regardless of
   content). Anniversary notifications are sent at a fixed,
   predictable time chosen for appropriateness to the
   memory's content — morning for joyful occasions, more
   measured timing for solemn ones.
```

When implemented with this discipline, the Anniversary Engine becomes one of ZUKA's strongest organic retention mechanisms — not because it manipulates engagement, but because it does something no content feed can do: it gives people back a piece of their own life, at exactly the moment it has become more meaningful with time.

---

# Part XV — Tradition Detection

---

## Chapter 22 — Memory-Layer Tradition Recognition

The Tradition entity and its detection algorithm are fully specified in Volume IV, Chapter 11. This chapter adds the Memory-Layer-specific recognition signals that strengthen detection accuracy.

```
ADDITIONAL TRADITION SIGNALS FROM THE MEMORY LAYER
(supplementing Volume IV's gathering-pattern detection):

  Recurring title patterns:
    Multiple Memories with semantically similar
    self-authored titles ("Christmas Dinner 2023,"
    "Christmas Dinner 2024") strongly reinforce
    tradition detection beyond gathering metadata alone.

  Recurring participant cores:
    A stable core group (e.g., the same 8 family members)
    appearing across multiple memories at similar intervals.

  Recurring emotional signatures:
    Memories that consistently carry the same emotion
    tag pattern (e.g., always 'joy' + 'belonging') across
    occurrences reinforce that this is a recognised,
    emotionally consistent tradition rather than a
    coincidental repeat gathering.

  Recurring ritual elements:
    Volume IV's RitualElement entities, when consistently
    present across Memory occurrences, are the strongest
    single signal of tradition formation.

EXAMPLE OUTPUT:

  Annual Christmas Dinner
    Started: 2014
    Occurrences: 11
    Participants: 38 (across all occurrences; core group of 12)
    Tradition strength: 0.94 (deep tradition)
    Constituent memories: [11 linked Memory entities]
```

---

# Part XVI — Memory AI (Future)

---

## Chapter 23 — The Historian Capabilities

These capabilities are explicitly future-state, dependent on the Memory Graph reaching sufficient density (data volume, relationship richness, and temporal span) to support them responsibly. They are documented now so that every MVP data-capture decision is made with this destination in mind.

```
MEMORY HISTORIAN
  Personal-level AI capability.
  "Show me my happiest year." "What did I do most often
  with Sarah?" "How has my circle of friends changed
  since university?"
  Operates entirely on the individual's own Memory Graph —
  never cross-references other users' private data.

FAMILY HISTORIAN
  Family Vault-level capability.
  "Tell me about Grandma's life." "What traditions has
  our family kept for the longest?" "Show the family's
  story across three generations."
  Requires Family Vault steward authorisation to activate
  for the full family archive.

COMMUNITY HISTORIAN
  Community Archive-level capability.
  "How has our church grown over 20 years?" "Who are the
  members who have contributed most to our shared memory?"
  Requires community leadership authorisation.

RELATIONSHIP HISTORIAN
  "Show the evolution of James and Brian's friendship."
  Operates only on memories both parties have consented
  to share at a level visible to this query.

LEGACY HISTORIAN
  Operates on MEMORIALISED identities and Family/Community
  Archives that include deceased members.
  "What was my grandfather's life like, based on what
  the family recorded?"
  The most ethically sensitive Historian — governed entirely
  by Legacy Settings (Volume III, Chapter 23) and never
  active without explicit steward authorisation.

GOVERNING PRINCIPLE FOR ALL HISTORIANS:
  Purpose: understanding.
  Never: surveillance.

  These systems exist to help a person, family, or community
  understand their own accumulated history — they do not
  exist to generate insight ABOUT a person for any party
  other than the person (or authorised steward) themselves.
  No Historian output is ever used as an input to advertising,
  B2B data products, or any external-facing intelligence
  system. This boundary is absolute.
```

---

# Part XVII — Legacy Architecture

---

## Chapter 24 — Memory's Place in the Legacy Domain

Volume III, Chapters 22–25 established the Legacy and Succession model at the Identity level — Legacy Settings, the Memorialisation Process, and Family Archive succession. This chapter confirms the Memory Layer's specific obligations within that model and treats Legacy as a first-class domain concern, not an afterthought bolted onto a content feature.

```
WHAT SURVIVES AFTER DEATH (memory-layer specifics):

  Every Memory the deceased created:           PRESERVED
    (subject to their memory_policy in Legacy Settings —
    Volume III, Chapter 23 — public/private/community
    content policies apply per-category)

  Every Memory the deceased is tagged in,
  created by someone else:                      PRESERVED
    Their tag is never silently removed. Their presence
    in others' memories is a historical fact, not subject
    to deletion by their passing.

  Their contributions to Family/Community
  Archives:                                       PRESERVED
    Permanently credited to their name within the
    archive structure (Chapters 16-17).

  Traditions they established or stewarded:        PRESERVED
    Stewardship transfers per Volume III, Chapter 24,
    Step 6. The Tradition entity is permanently annotated:
    "This tradition was established by [Name] in [year]."

  Their Memory Capital contribution to family/
  community archives:                               PRESERVED
    Becomes part of the institution's Legacy Capital
    (Volume II, Chapter 17) — it does not vanish, it
    converts into the permanent cultural memory of the
    family or community they belonged to.
```

---

## Chapter 25 — Digital Succession Choices

Extending Volume III's Legacy Settings with the specific, granular choices a user can make about their Memory Layer content, set while living:

```typescript
MemorySuccessionPreference {

  // Set per Memory category, or as a global default
  // overridable per-category

  default_policy:           SuccessionAction

  category_overrides: {
    personal_memories:       SuccessionAction
    family_memories:          SuccessionAction
    // Note: Family-owned memories (owner_entity_id = Family
    // entity) are governed primarily by Family Vault
    // succession policy (Chapter 16), not individual
    // preference — but an individual's PERSONAL contribution
    // notes/stories within those memories follow this setting
    community_memories:        SuccessionAction
    sensitive_memories:         SuccessionAction
    // User-flagged as especially private — defaults to
    // the most conservative succession action
  }
}

SuccessionAction enum:
  preserve            // Kept exactly as-is, accessible per
                       // existing visibility settings, with
                       // steward access per Legacy Settings
  archive              // Moved to a read-only, deprioritised
                       // state — preserved but not actively
                       // surfaced (e.g., not eligible for
                       // Anniversary Engine resurfacing)
  transfer              // Ownership/stewardship transfers to
                        // a named successor (typically used for
                        // Family/Community-relevant memories
                        // the person was the sole creator of)
  donate                 // Transferred to a Community Archive
                         // or Family Vault even if not
                         // originally created under that
                         // ownership — for memories the person
                         // wants to formally gift to an
                         // institution's permanent record
  delete                  // Permanently removed.
                          // ONLY available for memories where
                          // the requesting identity is the
                          // SOLE participant and SOLE creator.
                          // A memory involving other tagged
                          // people cannot be unilaterally
                          // deleted via succession preference —
                          // their right to the shared memory
                          // (Chapter 15) supersedes.
```

This is, deliberately, the most granular and most consequential settings panel in the entire platform. It is presented to users not as a grim administrative task but as an act of care — "decide how your story is remembered" — consistent with the dignity-first tone established in Volume III's Memorialisation Process.

---

# Part XVIII — The Memory Time Machine

---

## Chapter 26 — Future Capability Specification

The Memory Time Machine is the user-facing product surface that makes the entire architecture of this volume tangible. It is explicitly a future capability — dependent on the Memory Graph, the emotional architecture, and the AI Historian layer (Chapter 23) all reaching maturity — but it is specified now because it is the destination every MVP decision in this volume should be made in service of.

```
QUERIES THE TIME MACHINE MUST EVENTUALLY ANSWER:

  "Show my happiest year."
    Requires: emotion tag aggregation by year,
    importance_level weighting, Memory Capital
    temporal analysis.

  "Show all memories with grandmother."
    Requires: Memory Graph traversal (Chapter 12),
    tagged_person_ids filtering, chronological assembly.

  "Show traditions that disappeared."
    Requires: Tradition entity occurrence gap analysis —
    identifying traditions with is_active = false and
    a meaningful final occurrence date, presented with
    appropriate gentleness (not all disappeared traditions
    are mourned — some simply evolved or ended naturally;
    the framing must not presume loss).

  "Show the evolution of our family."
    Requires: Family Vault full timeline construction
    (Chapter 18), generational grouping, relationship
    graph evolution over time.

History becomes searchable — not as a database query
interface, but as a natural-language conversation with
one's own accumulated life record. This is the product
expression of the entire Memory Architecture's thesis:
ZUKA is not collecting photos. It is preserving human history.
```

---

# Part XIX — Memory Storage Strategy

---

## Chapter 27 — Separating What Can Be Lost From What Cannot

This chapter operationalises the principle established in Chapter 4: Media and Memory are architecturally separate, and this separation must be reflected in storage, backup, and cost engineering.

```
MEDIA LAYER STORAGE

  Storage tier strategy:
    Hot tier (0-90 days):        Full-resolution, CDN-backed,
                                  immediate access
                                  (recent uploads, active Vibe
                                  Drops, Live Wall content)
    Warm tier (90 days - 2 yrs):  Full-resolution, standard
                                   object storage, slight
                                   latency acceptable
    Cold tier (2+ years):         Compressed archival storage
                                   (cost-optimised), retrieval
                                   latency acceptable in seconds,
                                   NEVER deleted

  Compression policy:
    Original resolution preserved for the first export/
    download a user performs. Platform-served versions
    may be compressed for bandwidth efficiency, but the
    SOURCE file is retained at original quality in cold
    storage indefinitely — because a compressed memory
    photo, viewed in 20 years, deserves to be as clear
    as the day it was taken.

  Redundancy:
    Minimum 3x geographic redundancy for all Media Layer
    content past the hot tier. This is non-negotiable given
    the irreplaceability principle (Chapter 4) — even though
    Memory survives media loss, media loss is still a real
    harm to be actively prevented, not casually accepted.

  Cost model:
    Storage costs decrease over time per byte (cold tier
    pricing) even as TOTAL stored volume increases with
    platform growth — this cost curve must be modelled
    explicitly in financial planning, as it is structurally
    different from a content platform's cost curve (where
    old content is often deleted/expired to control cost).
    ZUKA's commitment to indefinite preservation means
    storage cost is a permanent, growing line item that
    must be priced into the platform's unit economics from
    day one — not treated as a deferred problem.

──────────────────────────────────────────────────────────

MEMORY LAYER STORAGE

  Storage tier strategy:
    Always hot. Memory entities (the meaning-layer records —
    title, participants, emotions, story, relationships) are
    comparatively tiny in storage footprint and infinitely
    more valuable per byte than media. They remain in
    primary, fast-access database storage indefinitely.
    There is no cold tier for meaning.

  Backup policy:
    Memory Layer backups run at higher frequency and
    higher redundancy than Media Layer backups, precisely
    because Chapter 4 establishes that meaning, once lost,
    cannot be regenerated. If forced to choose where
    engineering resilience investment goes first, it goes
    to the Memory Layer before the Media Layer.

  Append-only event sourcing:
    Per Volume II, Chapter 8 — every change to a Memory
    record is a DomainEvent, never an in-place mutation.
    This means even accidental or malicious data corruption
    of a Memory's current state is recoverable by replaying
    its event history.
```

---

# Part XX — MVP Strategy

---

## Chapter 28 — What to Build First

Consistent with the MVP discipline established in Volume IV, Chapter 22, the Memory Architecture MVP is deliberately narrow — proving the core mechanics while capturing the data needed for everything described in this volume's later chapters.

```
BUILD (MVP):

  ✅ Albums
     — Manual creation, photo/video grouping
  ✅ Photos
     — Upload, storage (hot tier only at MVP), basic display
  ✅ Videos
     — Upload, storage, basic display, Vibe Drop support
  ✅ Memory timelines
     — Personal timeline only (Chapter 18) — Family/Community
       timelines deferred
  ✅ Shared albums
     — Basic multi-contributor upload to a single album
       (full Shared Ownership Model from Chapter 14 deferred —
       MVP uses simplified 'all tagged participants can add
       media' rule only)
  ✅ Memory tagging
     — Tag participants in photos/videos with consent flow
       (Chapter 15's removal rights — MUST be included even
       at MVP, this is a non-negotiable trust requirement)
```

```
CAPTURE (data foundation, not yet surfaced as features):

  📊 Participants               (every tag, every co-attendance)
  📊 Communities                (which community each memory
                                  belongs to, even if Community
                                  Archive UI doesn't exist yet)
  📊 Relationships                (who appears together repeatedly)
  📊 Locations                    (where memories occurred)
  📊 Gatherings                    (linkage to Volume IV's
                                    Gathering entities)
  📊 Emotion tags                  (collect from day one — even
                                    a simple single-select emotion
                                    picker at memory creation time
                                    — this data is foundational
                                    and cannot be reconstructed
                                    retroactively for early memories
                                    if not captured at the time)
```

```
DO NOT BUILD YET:

  ❌ AI Historians (any of the five — Chapter 23)
  ❌ Legacy Vaults (full Family/Community Archive UI —
                     Chapters 16-17 — though the underlying
                     data capture above feeds this later)
  ❌ Tradition detection (algorithmic — Chapter 22)
                          (manual tradition tagging by users
                          is acceptable at MVP if simple)
  ❌ Memory Capital (computed internally is fine post-MVP;
                      NOT at MVP — premature without sufficient
                      data density to be meaningful)
  ❌ Anniversary Engine (Chapter 21 — requires both temporal
                          data depth AND the sensitivity
                          infrastructure to be built correctly;
                          better to launch late than launch
                          insensitively)
  ❌ Memory Time Machine (Chapter 26 — natural language query
                           interface is a multi-volume-dependent
                           future capability)
  ❌ Cold storage tiering (Chapter 27 — MVP can run hot-tier-only
                            storage; tiering is a cost optimisation
                            appropriate once volume justifies the
                            engineering investment, NOT before)
```

**The single most important MVP discipline in this volume:** capture emotion tags and tag-consent infrastructure from day one, even in their simplest possible form, because these two data types cannot be reconstructed retroactively. Everything else in the "do not build yet" list can be added later without loss. These two cannot.

---

# Part XXI — Risks

---

## Chapter 29 — Memory Ownership Conflicts

**Risk:** Two co-owners or stewards of a shared Memory disagree about visibility, editing, or archival.

**Current position:** The default_rights model (Chapter 14) and the majority_steward_vote archive/deletion authority provide a first line of resolution. Where stewards remain in genuine disagreement, the Memory enters a `disputed` status (per the MemoryStatus enum in Chapter 5) — frozen from further edits, fully preserved, pending resolution.

→ Formal dispute resolution procedure deferred to a future Legal & Governance Architecture volume, extending the pattern set in Volume III, Chapter 32 (Family Ownership Disputes).

---

## Chapter 30 — Privacy Concerns

**Risk:** A Memory's visibility setting conflicts with one tagged participant's expectation of privacy, particularly for sensitive life moments captured in a Family or Community context where the individual did not control the original visibility decision.

**Current position:** Chapter 15's tagged participant rights (see in particular the right to flag for privacy review) are the primary safeguard. These rights are explicitly stated as non-overridable by any owner or steward — this is intentional and should not be weakened in implementation under any commercial or product pressure.

→ Full moderation and review SLA deferred to a future Content & Moderation Architecture volume.

---

## Chapter 31 — Family Disputes

**Risk:** Beyond simple ownership conflicts (Chapter 29), deeper family disputes — divorce, estrangement, contested inheritance — intersect with Family Vault content in emotionally and legally complex ways.

**Current position:** Volume V's Relationship Snapshot field (Chapter 5 — `relationship_context: RelationshipSnapshot[]`) is the key architectural safeguard here: a Memory always preserves the relationship state AS IT WAS at the time of the memory, never silently updated to reflect a relationship's current (possibly hostile) state. A wedding Memory remains a Memory of a wedding, regardless of what happens afterward.

→ This chapter is explicitly acknowledged as requiring legal and family-counselling expert input beyond what architecture alone can resolve. Deferred, with the same caveat applied to Funeral Architecture in Volume IV, Chapter 25.

---

## Chapter 32 — False Memories

**Risk:** Deliberately fabricated or substantively inaccurate Memory content — whether malicious (fraud, harassment via false tagging) or simply mistaken (misremembered dates, misattributed participants).

**Mitigations:**
- Tag consent (Chapter 15) prevents a person from being falsely associated with a memory without their ability to remove the false tag
- Backdating confidence levels (Chapter 5 — `backdating_confidence`) allow uncertain historical claims to be marked as such rather than presented with false precision
- Community/Family Archive content, given its public or semi-public nature, is subject to the same reporting and moderation pathway as other platform content
- A Memory is, fundamentally, one person's (or group's) account of a lived experience — ZUKA does not and cannot adjudicate "objective truth" of personal memory, but it can and must adjudicate harm (false tagging used to harass, fabricated content used to defraud)

→ Detection and response procedures deferred to a future Content & Moderation Architecture volume and Fraud & Abuse extensions (building on ZUKA.md §26).

---

## Chapter 33 — Storage Costs

**Risk:** The indefinite-preservation commitment (Chapter 27) creates a permanently growing cost base with no corresponding decay — structurally different from, and more expensive over a long horizon than, a typical content platform's storage economics.

**Mitigations:**
- Tiered storage strategy (Chapter 27) — cold tier pricing decreases per-byte cost significantly for content past 2 years
- Compression policy balances quality preservation against bandwidth/serving cost (source files preserved at full quality; served versions optimised)
- This cost must be explicitly modelled in long-term financial planning as a structural, permanent line item — not treated as an optimisation problem to defer indefinitely

→ Full cost modelling deferred to a financial planning supplement, informed by actual MVP-stage storage volume data.

---

## Chapter 34 — Media Bloat

**Risk:** Without curation incentives, the Media Layer could grow into an undifferentiated mass of low-significance uploads, burying genuinely meaningful content and degrading the product experience (this is, notably, the opposite failure mode from data loss — too much undifferentiated media is its own form of harm to the Memory thesis).

**Mitigations:**
- The Layer 1 → Layer 4 curation pathway (Chapter 7) is intentionally not automatic — a photo remains in the Media Layer, accessible but not elevated, until a human act of curation (titling, significance-tagging) makes it a Memory
- The Memory Vault's primary user-facing surface should prioritise curated Memories over raw Media Layer browsing, by default
- Cover media selection (Chapter 5 — `cover_media_id`) ensures even media-rich memories present a single, intentional representative image rather than an undifferentiated gallery

→ UX-level curation incentive design deferred to product specification work, informed by this volume's architectural foundation.

---

## Chapter 35 — Legacy Governance

**Risk:** The Legacy and succession model (Chapters 24–25, extending Volume III) is among the most legally, culturally, and emotionally complex domains in the entire platform — covering inheritance-adjacent questions across dozens of legal jurisdictions as ZUKA expands across Africa and internationally.

**Current position:** The architecture in this volume and in Volume III establishes the principled framework (preservation by default, granular succession choices, dignity-first process) but explicitly does NOT attempt to resolve jurisdiction-specific legal questions about digital inheritance, which vary significantly by country and are, in many African markets, not yet settled in law at all.

→ Requires dedicated legal counsel engagement per major market before Legacy features move beyond their current architectural specification into full production implementation. This is flagged as one of the highest-priority legal review items across the entire Architecture Bible.

---

# Volume V Summary

```
The Memory Thesis:
  Content is consumed. Memories are lived.
  Content decays. Memory appreciates.
  ZUKA optimises for the latter — structurally, not just
  philosophically.

Memory ≠ Media:
  Two permanently separate entity layers.
  Media can be lost and recovered. Meaning cannot.
  This single distinction governs storage, backup,
  and architectural priority throughout the volume.

Memory Model:
  Full canonical Memory entity specification
  Media as referenced, not embedded

Six-Layer Stack:
  Raw Media → Moments → Experiences →
  Memories → Traditions → Legacy

Memory Types:
  6 categories: Personal, Family, Community,
  Cultural, Faith, Historical

Emotional Architecture:
  14 emotion types, multi-select, self-authored
  Modelled, not manipulated — explicit anti-manipulation
  commitments stated as product law

Memory Graph:
  Memories as the richest-connection entity type
  in the entire platform — every memory touches
  people, communities, gatherings, traditions

Shared Memory Ownership:
  Creator / co-owner / steward permission model
  Tagged participant rights — irrevocable, even
  against the memory's owner

Family & Community Archives:
  Institutional memory infrastructure
  Family Vault and Community Archive fully specified

Memory Timelines:
  5 timeline types — Personal, Family, Community,
  Relationship, City — same Memory, multiple contexts

Memory Chapters:
  AI-assisted clustering extending Volume III's
  Life Chapter model — always suggested, never automatic

Memory Capital:
  Internal-only scoring model — never displayed raw,
  never gamified, 6 weighted components

Anniversary Engine:
  Powerful retention mechanism — governed by
  mandatory, non-negotiable sensitivity rules

Tradition Detection:
  Memory-layer signals supplementing Volume IV's
  gathering-pattern detection

Memory AI (future):
  5 Historian capabilities — purpose is understanding,
  never surveillance, absolute boundary from B2B/ad use

Legacy Architecture:
  Memory's specific obligations within Volume III's
  Identity-level Legacy model
  Granular per-category succession choices

Memory Time Machine:
  Future natural-language query capability —
  the product expression of the entire volume's thesis

Storage Strategy:
  Memory Layer: always hot, highest backup priority
  Media Layer: tiered, cost-optimised, never deleted

MVP Scope:
  6 build items, 6 data-capture priorities,
  7 explicit deferrals
  Critical discipline: emotion tags and tag-consent
  must be captured from day one — irreplaceable if missed

Risks (7):
  Ownership conflicts · Privacy concerns · Family disputes
  False memories · Storage costs · Media bloat ·
  Legacy governance (flagged as highest-priority legal item)
```

---

# What Volume VI Must Address

Volume VI — Communities Architecture moves from individuals and memories to the living organisms that create civilisations: families, churches, schools, alumni networks, villages, clubs, choirs, companies, and every other form of human community.

Volume VI must resolve:

1. **The Community Entity Model in full** — extending Volume II's taxonomy (§3.2) into complete operational specification
2. **Community Governance** — leadership structures, decision-making models, succession (extending Volume III, Chapter 21 and Volume IV, Chapter 23's deferred questions)
3. **Community Lifecycle** — formation, growth, maturity, dormancy, and dissolution
4. **Community-Gathering Relationship** — formalising the flywheel described in Volume IV, Part XI
5. **Cross-Community Relationships** — how communities relate to each other (partnership, nesting, federation — e.g., a local church within a denomination, a school within an alumni network)
6. **Community Trust & Reputation** — extending Volume III's Trust Architecture with community-specific dimensions
7. **Community Economics** — membership models, community-level fundraising, institutional revenue
8. **Community Discovery** — how people find and join communities that matter to them
9. **Community Privacy** — extending Volume III's Privacy Architecture with community-specific visibility models
10. **The Community-Memory-Tradition Loop** — formalising how Volume V's Community Archives and Volume IV's Tradition entities are governed by the communities that own them

---

*Volume V is canonical. Extensions require architecture review.*
*Contradictions with this volume must be resolved before implementation.*

---

> **ZUKA Architecture Bible**
> Volume V — Memory Architecture
> Built in Kampala. Built for Africa. Built for the world.
