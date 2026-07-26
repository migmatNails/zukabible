# ZUKA Architecture Bible
# Volume VII — Temporal Architecture

> **Series:** ZUKA Architecture Bible
> **Volume:** VII of N
> **Status:** Canonical — the deepest engineering volume in the Bible to date
> **Depends on:** Volume I (Vision), Volume II (Domain Model), Volume III (Identity),
>                  Volume IV (Gatherings), Volume V (Memory), Volume VI (Communities)
> **Required by:** Volume VIII (Knowledge Graph & Semantic Intelligence) and every
>                  subsequent volume — time is the substrate everything else runs on
> **Governs:** The Dormant Feature Register (Appendix A) — the disciplined holding
>              pattern for the wider V1.1 scope expansion pending data maturity

---

# Preface

Every prior volume in this Bible has quietly depended on time without formally specifying it. Volume II's DomainEvent ledger has `occurred_at` and `recorded_at` fields. Volume III's Identity Timeline assembles a chronological record. Volume IV's Gathering lifecycle moves through phases over time. Volume V's entire thesis — memories appreciate while content decays — is a claim about time. Volume VI's Community Timeline and succession model both depend on history being trustworthy.

None of those volumes formally defined what time *is* in ZUKA's architecture. They borrowed it as if it were obvious. It is not obvious, and the borrowing has reached its limit.

Most software understands **current state**. It can answer "what is true right now?" ZUKA must answer harder questions: what was true, what changed, why it changed, what is likely to happen next, what traditions are emerging, what opportunities are forming. A platform whose central thesis is that history compounds in value cannot treat history as an afterthought field on a database row.

This volume makes time a first-class architectural domain, formally unifies the timeline systems built independently across Volumes III, V, and VI into one substrate, and introduces the discipline machinery — Data Readiness Levels, Feature Seeds, Dormant Features — that lets ZUKA grow toward an ambitious long-term scope without building ahead of the data that should justify each step.

---

# Part I — The Temporal Thesis

---

## Chapter 1 — Time Is Not Metadata

```
MOST SYSTEMS STORE:
  user.created_at
  and stop there.

ZUKA MODELS:
  Identity Through Time
  Community Through Time
  Trust Through Time
  Memory Through Time
  Opportunity Through Time
```

A single `created_at` timestamp answers one question: when did this record first exist in the database. It cannot answer when something actually happened in the world, when ZUKA learned about it, when ZUKA became confident it was true, or what the entity looked like at any point before now. Volume V's `is_backdated` and `backdating_confidence` fields (Volume V, Chapter 5) were an early, narrow acknowledgment of this gap — a Memory can be dated to when it actually happened, distinct from when the record was created. This volume generalises that acknowledgment into a complete architectural principle covering every entity, not just Memory.

---

## Chapter 2 — Every Entity, Relationship, Signal, and Memory Exists in Time

This is a direct extension of Volume II's foundational claim that Entity, Relationship, Event, and Time are the four universal primitives (Volume II, Chapter 1). Volume II named Time as a primitive but did not yet give it its own canonical structure. This volume supplies that structure.

```
Every entity exists in time.
Every relationship exists in time.
Every trust signal exists in time.
Every memory exists in time.
```

---

# Part II — The Canonical Temporal Model

---

## Chapter 3 — The TemporalEntity Envelope

Every entity defined anywhere in this Bible — Person, Community, Memory, Tradition, Trust Profile, Opportunity — carries, in addition to its own fields, a temporal envelope. This is not a new entity type; it is a structural extension applied uniformly across the entity taxonomy already established in Volume II, Chapter 3.

```typescript
TemporalEntity {
  entity_id:        UUID            // the entity this envelope belongs to

  valid_from:        Timestamptz     // when this state became true
  valid_to:           Timestamptz     // when this state stopped being true
                                      // (null = currently valid)

  observed_at:          Timestamptz     // when ZUKA first observed this
  recorded_at:             Timestamptz     // when ZUKA wrote this to the ledger

  confidence:               Float(0.0–1.0) // how certain ZUKA is this is accurate
                                            // (relates to Volume V's
                                            // backdating_confidence pattern,
                                            // generalised here)

  version:                     Integer        // monotonic version counter,
                                                // consistent with Volume II,
                                                // Chapter 2's entity versioning
}
```

This envelope is what makes the Four Clocks (Part III) and bitemporal querying (Part IV) possible across every entity in the system, not just the ones that happened to think of it.

---

# Part III — The Four Clocks

---

## Chapter 4 — One Timestamp Is Not Enough

Most systems have one timestamp. ZUKA has four, because four genuinely different questions need four genuinely different answers.

```
┌─────────────────────────────────────────────────────────────┐
│  REALITY CLOCK                                                │
│  When something actually happened in the world.                │
│  Example: The wedding happened on June 5.                       │
│  Maps to: occurred_at (already established in Volume V,          │
│  Chapter 5's Memory.timestamp.occurred_at — this volume           │
│  generalises that field to every TemporalEntity)                   │
├─────────────────────────────────────────────────────────────┤
│  OBSERVATION CLOCK                                              │
│  When the system learned about it.                               │
│  Example: The wedding photo was uploaded on June 7.                │
│  Maps to: TemporalEntity.observed_at (Chapter 3)                    │
├─────────────────────────────────────────────────────────────┤
│  PROCESSING CLOCK                                                 │
│  When ZUKA processed and recorded it.                              │
│  Example: ZUKA wrote the Memory record on June 7.                   │
│  Maps to: TemporalEntity.recorded_at (Chapter 3), and is             │
│  the same concept as Volume II, Chapter 8's DomainEvent              │
│  recorded_at field — this volume confirms that linkage                │
│  rather than introducing a competing field                             │
├─────────────────────────────────────────────────────────────┤
│  KNOWLEDGE CLOCK                                                  │
│  When ZUKA became CONFIDENT it was true.                           │
│  Example: A tentatively-tagged Memory's date is confirmed            │
│  by a second attendee on June 15 — confidence rises from              │
│  'estimated' to 'exact' (Volume V, Chapter 5's                         │
│  backdating_confidence enum)                                            │
│  Maps to: the moment TemporalEntity.confidence crosses a                 │
│  meaningful threshold, logged as its own event                           │
└─────────────────────────────────────────────────────────────┘
```

These four clocks are not always identical, and the gap between them is itself meaningful data. A large gap between Reality Clock and Observation Clock (a Memory backdated by years from an inherited family photo — Volume V, Chapter 16's `oldest_memory_date`) is expected and unremarkable. A large gap between Observation Clock and Knowledge Clock (ZUKA recorded something but took a long time to become confident it was accurate) is itself a signal — potentially of a disputed Memory (Volume V, Chapter 5's `disputed` status) or contested Community succession (Volume VI, Chapter 25).

---

## Chapter 5 — Why Four Clocks, Not One

The practical consequence of collapsing these four clocks into one — as nearly every system does by default — is that the system cannot distinguish "this happened recently" from "we just found out about this" from "we're now sure about this." For a platform whose differentiator is trustworthy multi-decade history (Volume V's central thesis), this distinction is not a nicety. It is the mechanism by which ZUKA can honestly answer "how sure are we that this is right" for any historical claim in the system — a question no competitor entertainment platform has ever needed to answer, because no competitor has committed to preserving history for decades.

---

# Part IV — Event Sourcing Becomes Mandatory

---

## Chapter 6 — Confirming and Formalising the Existing Pattern

Volume II, Chapter 8 already established the append-only DomainEvent ledger as the mechanism by which all state changes are tracked. This volume does not introduce a new pattern — it makes that pattern **mandatory and universal**, closing any gap where a future engineer might be tempted to store current state directly without an event trail behind it.

```
THE ARCHITECTURE DOCTRINE:

Reality → Events → Knowledge → Intelligence

Reality:        Something happens in the world (Reality Clock)
Events:         ZUKA records it as an immutable, append-only
                 DomainEvent (Observation Clock, Processing Clock)
Knowledge:       The event is integrated into the entity's
                 current state and the Temporal Graph (Part VI),
                 with appropriate confidence (Knowledge Clock)
Intelligence:      Patterns across many events become available
                 for the Historian Layer (Part XI) and future
                 AI Agents — strictly gated by the Data Readiness
                 Level framework (Part XII), never built ahead
                 of the data that justifies them
```

---

## Chapter 7 — Current State Is Temporary, History Is Permanent

```
BAD (current-state-only modelling):
  relationship_status = 'married'

GOOD (event-sourced modelling):
  RelationshipStarted
  EngagementOccurred
  MarriageOccurred
  DivorceOccurred   (if applicable — never deleted, never hidden)
```

This is the direct technical expression of Volume V, Chapter 5's `relationship_context: RelationshipSnapshot[]` field — which already captured relationships "as they were at the time of the memory, not as they are now." This volume confirms that pattern as universal doctrine, not a Memory-specific accommodation: **`relationship_status` as a single mutable field is structurally prohibited anywhere in ZUKA's schema.** Status is always a derived projection over an event history, never a directly written field. A person querying "is James married" gets an answer computed by replaying or indexing James's relationship events up to now — not by reading a field that some prior process overwrote.

The consequence stated plainly: **history is preserved forever.** A divorce does not erase a marriage. A community leadership change does not erase the prior leader's tenure (Volume VI, Chapter 25's `leadership_history` field already assumed this). A Tradition that lapses does not have its prior occurrences deleted (Volume V, Chapter 26's "show traditions that disappeared," framed without presuming loss, already assumed this too).

---

## Chapter 8 — The Universal Event Store, Consolidated

Volume II, Chapter 9 already registered roughly 85 domain event types across nine categories. This chapter does not replace that registry — it confirms its completeness against the categories named in this volume's source material and flags the small number of genuinely new event types this volume's concepts require.

```
ALREADY REGISTERED (Volume II, Chapter 9) — confirmed, no changes:

  Person Events:           PersonCreated, PersonVerified, ...
  Relationship Events:      RelationshipProposed, RelationshipAccepted, ...
  Gathering Events:          GatheringCreated, GatheringCheckInOpened, ...
  Ticketing Events:           TicketPurchased, TicketCheckedIn, ...
  Memory Events:                PhotoUploaded, MemoryCreated, ...
  Community Events:               CommunityCreated, CommunityMemberAdded, ...
  Economic Events:                  OrderCreated, PulsePointsEarned, ...
  Trust Events:                       VerificationApproved, ReputationScoreUpdated, ...
  Scanner Events:                       ScanAdmitted, ScanDenied, ...
  Opportunity Events:                     JobPosted, BookingConfirmed, ...
  Platform Events:                          CityLaunched, FraudDetected, ...

NEW EVENT TYPES INTRODUCED BY THIS VOLUME:

  TraditionDetected            (Tradition Detection Engine, Part VIII)
  TraditionStrengthened          (occurrence count crosses a threshold)
  TraditionLapsed                  (expected occurrence missed —
                                    see Part VIII's lifecycle state machine)
  TraditionRevived                    (a lapsed Tradition resumes)
  TrustScoreRecomputed                  (Temporal Trust, Part X —
                                          distinct from the existing
                                          ReputationScoreUpdated,
                                          this event specifically
                                          captures a Trust Timeline
                                          data point, not just the
                                          current score)
  OpportunityEmerged                      (Opportunity Emergence,
                                          Part XI — distinct from
                                          JobPosted/SponsorshipProposed,
                                          this captures the SYSTEM
                                          recognising emergent
                                          opportunity conditions
                                          before any human posts
                                          a formal Opportunity entity)
  DataReadinessLevelChanged                  (DRL Framework, Part XII)
  FeatureSeedActivated                         (Feature Seed
                                          Architecture, Part XIII)
```

This is a deliberately short list. The vast majority of what this volume describes is new *interpretation* of existing events (replaying the existing TraditionDetected-adjacent signals already scattered across Volumes IV–VI into a formal Tradition lifecycle, for instance) rather than new event types requiring new instrumentation. This is intentional — Volume VII's job is to give meaning and structure to data already being captured, not to demand new capture obligations beyond what's already specified.

---

# Part V — The Temporal Graph

---

## Chapter 9 — Adding Time as a Graph Dimension

Volume II, Chapter 13 established six graphs: Entity, Knowledge, Temporal, Trust, Opportunity, Memory. The Temporal Graph was named there but specified only briefly. This chapter gives it full operational definition.

```
MOST GRAPHS ANSWER:
  Who knows who?

THE TEMPORAL GRAPH ANSWERS:
  Who knew who?
  When?
  Through which communities?
  For how long?
  Through how many gatherings?
```

```sql
-- "When did James and Brian's friendship form, and through
--  which community?"

SELECT
  r.valid_from AS friendship_began,
  r.valid_to AS friendship_ended,   -- null if ongoing
  c.name AS originating_community,
  COUNT(DISTINCT ge.gathering_id) AS shared_gatherings_count
FROM relationships r
JOIN communities c ON c.id = (r.metadata->>'origin_community_id')::uuid
LEFT JOIN gathering_participants ge
  ON (ge.person_id = r.source_entity_id OR ge.person_id = r.target_entity_id)
WHERE r.source_entity_id = :james_id
  AND r.target_entity_id = :brian_id
  AND r.relationship_type = 'FRIEND'
GROUP BY r.valid_from, r.valid_to, c.name;

-- ────────────────────────────────────────────────────────

-- "Show the full temporal shape of a relationship's weight
--  over its lifetime" — i.e., not just current weight
--  (Volume II, Chapter 6) but its trajectory

SELECT
  de.occurred_at,
  de.payload->>'new_weight' AS weight_at_this_point
FROM domain_events de
WHERE de.event_type = 'RelationshipWeightUpdated'
  AND de.subject_entity_id = :relationship_id
ORDER BY de.occurred_at ASC;
```

Time becoming a graph dimension, rather than a property on graph edges, is what makes Chapter 11's relationship evolution model and Part X's Trust Timeline queryable as first-class capabilities rather than expensive manual reconstructions.

---

# Part VI — Life Chapters and Community Chapters

---

## Chapter 10 — Confirming the Existing Life Chapter Model

Volume III, Chapter 19 already established the Life Chapter taxonomy (PRE_SCHOOL through LEGACY, plus CUSTOM), and Volume V, Chapter 19 already specified the AI-assisted clustering signals that detect chapter boundaries. This volume's contribution is narrow and specific: confirming that Life Chapters are a **temporal** concept, not merely an organisational one, and that they sit precisely at the intersection of this volume's Reality Clock and Volume III/V's existing biographical framework.

```
Humans don't live in records.
Humans live in chapters.

Memories attach to chapters.       (Volume V, Chapter 5's
                                    life_chapter field — confirmed)
Communities attach to chapters.    (new — see below)
Relationships attach to chapters.   (new — see below)
```

The two genuinely new bindings this volume adds: a Relationship (Volume II, Chapter 4) can now carry a `life_chapter_context` annotation alongside its existing `valid_from`/`valid_to` fields, capturing which chapter of a person's life a given relationship belonged to (a university friendship that began in the UNIVERSITY chapter and continued into CAREER is one continuous relationship, but its *chapter context* shifts) — and a Community Membership (Volume VI, Chapter 8) can similarly carry chapter context, distinguishing "the church I attended growing up" from "the church I attend now" even where the Community entity itself is identical.

---

## Chapter 11 — Community Chapters

This is a genuinely new concept this volume introduces: communities, like people, move through chapters — but a community's chapters are not the same taxonomy as a person's.

```typescript
CommunityChapterType enum:
  formation              // Volume VI, Chapter 22's "Creation" stage,
                          // viewed through a chaptering lens
  growth                  // maps directly to Volume VI's "Growth" stage
  institutionalization      // a new distinction WITHIN what Volume VI
                            // called "Maturity" — specifically the
                            // period where informal practices become
                            // formal governance (Volume VI, Chapter 16's
                            // four-layer governance model crystallising)
  maturity                   // sustained, stable operation post-
                            // institutionalisation
  legacy                       // maps directly to Volume VI's "Legacy"
                              // stage
```

This refines, rather than replaces, Volume VI's five-stage Community Lifecycle (Chapter 22). The relationship between the two: `CommunityLifecycleStage` (Volume VI) is the coarse operational status used for product surfaces and succession-urgency logic. `CommunityChapterType` (this volume) is the finer-grained temporal lens used by the Community Historian (Part XI) to narrate a community's evolution — splitting Volume VI's single "Maturity" stage into the institutionalisation moment and the long stable period that follows it, because those two periods of a community's life look and feel very different even though both count as "mature" operationally.

This distinction exists specifically to help future AI systems (the Community Historian, Part XI) understand and narrate community evolution with more nuance than the five-stage lifecycle alone permits — without requiring Volume VI's existing, simpler, product-facing lifecycle model to be complicated for that purpose.

---

# Part VII — Bitemporal Querying

---

## Chapter 12 — Answering "What Was True At Time T"

This chapter formalises the query capability the Four Clocks (Part III) and the TemporalEntity envelope (Chapter 3) exist to enable. Most database queries answer "what is true now." Bitemporal querying answers two distinct historical questions, and the distinction matters:

```
QUESTION TYPE 1 — "What did the world look like at time T?"
  (Reality Clock framing)

  Example: "Show me James's relationship graph as it existed
  on his wedding day" — even if some of those relationships
  have since ended, and even if ZUKA didn't learn about some
  of them until later.

  SELECT * FROM relationships
  WHERE source_entity_id = :james_id
    AND valid_from <= :wedding_date
    AND (valid_to IS NULL OR valid_to > :wedding_date);


QUESTION TYPE 2 — "What did ZUKA BELIEVE was true at time T?"
  (Knowledge Clock framing — a genuinely different question)

  Example: "What did ZUKA's system believe about James's
  relationship graph as of the wedding day, USING ONLY
  information ZUKA had actually recorded by that date?"
  This excludes anything backdated or discovered later,
  even if it turns out to be historically accurate.

  SELECT * FROM relationships
  WHERE source_entity_id = :james_id
    AND recorded_at <= :wedding_date
    AND valid_from <= :wedding_date
    AND (valid_to IS NULL OR valid_to > :wedding_date);
```

Question Type 1 is what a person wants when reminiscing ("what was my life like then"). Question Type 2 is what an auditor, a dispute-resolution process (Volume V, Chapter 29's Memory Ownership Conflicts; Volume VI, Chapter 28's Leadership Disputes), or a legal proceeding wants ("what did the system actually know, and when"). Both are real, distinct, necessary capabilities, and the TemporalEntity envelope's four fields are what make both possible from the same underlying data without maintaining two separate historical records.

---

# Part VIII — The Tradition Engine

---

## Chapter 13 — Tradition as a First-Class Entity, Confirmed

Volume IV, Chapter 11 already defined the Tradition entity structurally. Volume V, Chapter 22 already added memory-layer detection signals. Volume VI, Chapter 12 already confirmed Traditions are community-owned. This volume's contribution is the **formal definition of what a Tradition fundamentally is**, and the **complete lifecycle state machine** governing how one forms, strengthens, weakens, lapses, and is revived — closing the gap explicitly flagged in this volume's required-resolution list at the close of Volume VI.

```
A TRADITION IS:

  Repeated Gathering
  +
  Time
  +
  Shared Memory
  +
  Shared Participation
```

This is a precise definition worth holding onto exactly as stated, because each of the four components maps to a specific, already-built ZUKA system: Repeated Gathering (Volume IV's Gathering entity, recurring), Time (this volume's temporal substrate), Shared Memory (Volume V's Memory entities, linked via `tradition_id`), Shared Participation (Volume IV's Participation records, aggregated). A Tradition is not a new kind of data — it is a *recognised pattern* across data that already exists in full elsewhere in the Bible.

---

## Chapter 14 — The Tradition Lifecycle State Machine

This is the formal resolution to the gap named at the close of Volume VI: a complete state machine for how a Tradition forms, strengthens, weakens, lapses, and is revived.

```
                    ┌─────────────────┐
                    │   UNRECOGNISED   │
                    │  (2 occurrences  │
                    │   detected, not  │
                    │   yet suggested) │
                    └────────┬────────┘
                             │ 3rd matching occurrence
                             │ (Volume V, Chapter 22's
                             │  detection signals)
                             ▼
                    ┌─────────────────┐
                    │     SEEDED       │◄──────────────┐
                    │ (suggested to     │                │
                    │  host/community,   │                │
                    │  awaiting           │                │
                    │  confirmation)       │                │
                    └────────┬────────┘                │
                             │ host/community confirms    │
                             ▼                              │
                    ┌─────────────────┐                    │
                    │     ACTIVE        │                    │
                    │ (formally named,    │                    │
                    │  tradition_strength   │                    │
                    │  accumulating per       │                    │
                    │  Volume II, Chapter 11)   │                    │
                    └────────┬────────┘                        │
                             │                                    │
                  ┌──────────┴──────────┐                          │
                  │                     │                          │
        occurrence happens     expected occurrence                  │
        on schedule            window passes with                   │
                  │             no occurrence                        │
                  │                     │                          │
                  ▼                     ▼                          │
        ┌─────────────────┐   ┌─────────────────┐                  │
        │   ACTIVE          │   │     AT_RISK       │                  │
        │  (strength          │   │ (one missed         │                  │
        │   increases,          │   │  occurrence,           │                  │
        │   stays in this        │   │  TraditionEvent          │                  │
        │   state)                 │   │  fired, gentle             │                  │
        └─────────────────┘   │  surfacing per             │                  │
                               │  Volume V, Ch.26's            │                  │
                               │  "framed without              │                  │
                               │  presuming loss")               │                  │
                               └────────┬────────┘                  │
                                        │                              │
                          ┌─────────────┴─────────────┐                  │
                          │                           │                  │
                resumes within             second consecutive               │
                next expected window        occurrence missed                 │
                          │                           │                  │
                          │                           ▼                  │
                          │                  ┌─────────────────┐          │
                          │                  │     LAPSED        │          │
                          │                  │ (TraditionLapsed    │          │
                          │                  │  event fired,          │          │
                          │                  │  tradition preserved     │          │
                          │                  │  in full per Volume       │          │
                          │                  │  VI, Ch.25's "Never        │          │
                          │                  │  cancelled" principle,      │          │
                          │                  │  but no longer actively      │          │
                          │                  │  expected)                    │          │
                          │                  └────────┬────────┘          │
                          │                           │                  │
                          │                resumes after a gap          │
                          │                (TraditionRevived event)      │
                          └───────────────────────────┴──────────────────┘
                                        revival returns to ACTIVE,
                                        with the full lapsed period
                                        preserved in the tradition's
                                        history — NOT erased, per the
                                        Temporal Truth Principle (Part XV)
```

The governing rule that resolves Volume V, Chapter 26's framing concern explicitly: **a Tradition's transition into AT_RISK or LAPSED is never communicated as a failure, and a Tradition's revival is never communicated as if the lapsed period didn't happen.** Both the gap and the resumption are permanent, equally weighted parts of the Tradition's history — consistent with Chapter 7's principle that history, once recorded, is never erased to make a narrative cleaner.

---

## Chapter 15 — Tradition Detection Is Discovery, Not Creation

```
A Tradition is not manually created. It is discovered.

EXAMPLE:

  System notices:
    12 Christmas gatherings
    8 years
    Same families
    Same venue

  Suggests:
    "Family Christmas Tradition"
```

This confirms Volume V, Chapter 22's detection signals and Volume VI, Chapter 12's community-ownership binding, while adding the explicit philosophical framing: ZUKA does not invent traditions, it recognises them. The SEEDED state in Chapter 14's lifecycle is where this distinction lives architecturally — the system can detect a pattern with high confidence, but the act of naming and formally adopting a Tradition remains a human act of recognition, never an automated declaration. This mirrors Volume III, Chapter 19's principle that Life Chapter transitions are always suggested, never automatic, and Volume IV, Chapter 11's existing Tradition detection note that host confirmation is always required.

---

# Part IX — Memory Aging Model

---

## Chapter 16 — Confirming the Memory Value Curve as Foundational

Volume V, Chapter 3 already specified the Memory Value Curve in detail — content peaks and decays, memory begins modest and appreciates. This volume's role is narrow: confirming that this curve is not merely a Memory-layer concern but a **temporal architecture principle that governs ranking and surfacing systems across the entire platform**, not just within the Memory Vault.

```
CONTENT CURVE                    MEMORY CURVE

Day 1   ██████████                Day 1   ██
Day 30  ████                      Year 1  ████
Year 1  █                         Year 10 ████████
                                   Year 30 ██████████████
```

The practical, cross-volume consequence: any ranking or surfacing algorithm anywhere in ZUKA — the home feed (entertainment-layer, time-decaying by design, correctly so), the Memory Vault (appreciating, per Volume V), the Community Archive's featured milestones (Volume VI, Chapter 17's `featured_milestones`), the Tradition Engine's significance weighting (Part VIII above) — must explicitly declare which curve it follows. **A system that silently applies content-curve decay logic to memory-layer data would directly contradict Volume V's central thesis**, and this volume exists in part to make that contradiction structurally visible and preventable: every ranking system's specification must state, explicitly, "this follows the Content Curve" or "this follows the Memory Curve." Silence on this point is treated as a specification defect.

---

# Part X — Temporal Trust

---

## Chapter 17 — Trust as a Timeline, Not a Number

Volume III, Chapters 14–16 established Trust Dimensions and an `overall_trust_score`. Volume VI, Chapter 13 extended this to Community Trust. Both, as specified, describe trust as a current value. This volume adds the temporal dimension neither prior volume formalised: **trust is not a number, it is a timeline.**

```
NOT:
  trust = 87
  (forever, as a single static fact)

INSTEAD — Trust Timeline:
  2019:  55
  2020:  63
  2021:  75
  2022:  88
  2023:  90
```

```typescript
// Extends Volume III, Chapter 15's TrustDimensions with the
// temporal record this volume requires

TrustTimelinePoint {
  identity_id:           UUID     // or community_id, per Volume VI,
                                    // Chapter 13's CommunityTrustProfile
  recorded_at:              Timestamptz
  overall_trust_score:         Float(0.0–1.0)
  dimension_snapshot:             TrustDimensions   // the full
                                                      // dimensional
                                                      // breakdown at
                                                      // this point,
                                                      // not just the
                                                      // composite
  trigger_event_id:                  UUID     // the TrustEvent
                                                // (Volume II, Ch.9)
                                                // that caused this
                                                // recomputation
}

-- Generated by the new TrustScoreRecomputed event (Chapter 8),
-- fired on every meaningful trust-affecting event, distinct
-- from simply overwriting the current trust_profile field
```

This is what makes a question like "has this organiser's reliability been improving or declining over the past two years" answerable with a real query rather than an inference from anecdote — directly useful for the B2B Venue Intelligence product's demand forecasting (Volume on B2B Product Suite, Product 3) and for a community's own succession-planning prioritisation (Volume VI, Chapter 13's `trust_compounds_over_years` acknowledgment, now made concretely queryable).

---

## Chapter 18 — Trust Decay as Reality, Not Punishment

```
If participation disappears:
  Trust slowly decays.

  Not punishment.
  Reality.
```

This framing matters and should be held precisely. Volume III, Chapter 6 already established relationship weight decay with specific decay factors per relationship type. This volume confirms the same *logic* applies to Trust, but with an explicit reframe of intent: decay is not a penalty mechanism for inactivity, it is simply an honest model of the fact that trust built through consistent demonstrated reliability becomes less *current* — not less *real* — when the demonstrating behaviour stops. A trusted organiser who stops running events for two years has not done anything wrong; their trust score declining reflects only that ZUKA has less recent evidence to be confident about, exactly as a person would naturally feel less certain vouching for a friend they haven't seen in years, without believing anything bad necessarily happened.

This distinction should govern UX copy wherever trust decay is surfaced: it must never read as an accusation.

---

# Part XI — Relationship Evolution and Opportunity Emergence

---

## Chapter 19 — Relationships Tell a Story Over Time

```
Strangers
  ↓
Community Members
  ↓
Friends
  ↓
Business Partners
  ↓
Mentors

Time tells the story.
```

This is the narrative expression of Volume II, Chapter 6's relationship weight and trust mechanics, and Volume VI, Chapter 19's observation that community membership is frequently the causal origin of other relationship types. This volume's specific addition: the **relationship_evolution_path** becomes a queryable, narratable sequence rather than an implicit byproduct — the Temporal Graph (Part V) can reconstruct and present this exact progression for any pair of identities, which is the foundational capability the future Relationship Historian (Volume V, Chapter 23) depends on.

---

## Chapter 20 — Opportunities Emerge, They Are Not Created

This is one of the more important reframes in this volume, with direct implications for how the Opportunity Graph (Volume IV, Chapter 17; Volume VI, Chapter 18) should be understood architecturally.

```
NOT:
  A person posts a Job entity. An Opportunity exists.
  (This is real and valid, but it is the SHALLOW case.)

THE DEEPER PATTERN:

  Same Community
    ↓
  Multiple Gatherings
    ↓
  Trust (accumulated, per Part X)
    ↓
  Collaboration (informal, often unrecorded as a formal entity)
    ↓
  Business (the eventual, often much-later, formal Opportunity)
```

The new `OpportunityEmerged` event (Chapter 8) exists precisely to capture the system *recognising* this pattern forming — sustained co-attendance, rising mutual trust, repeated informal collaboration signals — **before** any person formally posts a Job, Sponsorship, or Booking entity. This is structurally identical to the Tradition Engine's SEEDED state (Chapter 14): the system detects an emerging pattern with rising confidence, and surfaces it as a *possibility* rather than asserting it as a fact. A future Opportunity Agent (governed under the same Dormant Feature discipline as Part XIII) could eventually use this signal to gently suggest "you and Brian have collaborated informally three times — would you like to formalise this?" — but that suggestion capability is explicitly **not** built at this stage; only the underlying `OpportunityEmerged` signal capture is.

---

# Part XII — Data Readiness Levels (DRL)

---

## Chapter 21 — The Discipline Framework

This is the chapter that resolves the scope-discipline concern raised before this volume was written. The Data Readiness Level framework is the formal, principled mechanism by which ZUKA can hold an ambitious long-term architecture (Trust OS, Opportunity Engine, the full Historian Layer, and everything named in the wider V1.1 revision document) as genuinely canonical — worth designing for now — **without building any of it until the data justifies it.**

```
DRL 0 — NO DATA
  The capability has been designed (it exists in this Bible)
  but zero relevant data has been collected yet.

DRL 1 — DATA COLLECTION STARTED
  The relevant events (Chapter 8) are being captured.
  No analysis has been performed. No patterns surfaced.

DRL 2 — PATTERNS VISIBLE
  Sufficient volume exists that basic patterns are
  statistically observable (e.g., enough Tradition-candidate
  gatherings exist that the detection signals from Volume V,
  Chapter 22 can run without producing noise-dominated results).

DRL 3 — USEFUL INSIGHTS POSSIBLE
  Patterns are reliable enough to surface to humans as
  suggestions (e.g., the Tradition Engine's SEEDED state,
  Chapter 14, becomes viable — the system can suggest a
  Tradition with reasonable confidence of being right).

DRL 4 — AUTOMATION POSSIBLE
  Confidence is high enough that some actions can be taken
  with reduced or no human confirmation (e.g., automatic
  TraditionStrengthened progression without requiring
  re-confirmation at every occurrence, once ACTIVE).

DRL 5 — AI-GRADE INTELLIGENCE POSSIBLE
  Sufficient volume, density, and historical depth exists
  to support the Historian Layer (Part XIV), AI Copilots
  (Volume IV, Part XIV; Volume VI, Part XVIII), and any
  future AI Twin capability named in the wider V1.1 scope
  register (Appendix A) — these remain firmly out of reach
  until DRL 5 is reached for the relevant data domain, and
  DRL 5 is reached independently per domain, not platform-wide
  (Trust data may reach DRL 5 in a given market years before
  Opportunity data does, for instance).
```

```typescript
DataReadinessAssessment {
  domain:                  TemporalDomain
  // 'tradition' | 'trust' | 'opportunity' | 'community_evolution' |
  // 'relationship_evolution' | etc. — assessed independently
  // per domain, and independently per city/market given
  // Volume on Multi-City Architecture's market-by-market
  // rollout model

  current_drl:               Integer(0–5)
  assessed_at:                  Timestamptz
  // Recomputed on a scheduled cadence (e.g., monthly) —
  // see new DataReadinessLevelChanged event (Chapter 8)

  volume_signal:                  Integer    // raw count of
                                              // relevant events
  density_signal:                    Float      // relationship/
                                              // connection density
                                              // in the relevant graph
  temporal_span_signal:                Integer    // months of
                                              // history available
  confidence_signal:                     Float      // average
                                              // TemporalEntity.confidence
                                              // across the relevant
                                              // entity population

  next_drl_threshold:                       DRLThreshold
  // The specific, named criteria required to advance —
  // always explicit and inspectable, never a black box
}
```

---

# Part XIII — Feature Seeds and Dormant Features

---

## Chapter 22 — Every Future Feature Begins as a Seed

```
EXAMPLE — Trust Engine

  Visible to users:    Nothing.

  Collecting:
    Attendance            (Volume IV's Participation records)
    Participation           (gathering_participation_count,
                            Volume VI, Chapter 8)
    Communities                (membership, Volume VI, Chapter 8)
    Relationships                 (Volume II, Chapter 4)
    Bookings                        (Volume II, §3.10)

  The future feature grows from a seed already planted.
```

This is the operational discipline this entire volume exists to protect: **every feature named anywhere in this Bible — including everything in the wider V1.1 scope register (Appendix A) — has a Feature Seed defined now, even though the feature itself is not built now.** A Feature Seed is nothing more than: the list of DomainEvents (Chapter 8, and Volume II's existing 85+) that need to be captured, and the DRL threshold (Part XII) that must be reached before the feature graduates from dormant to active.

---

## Chapter 23 — Dormant Features Are Not Vapourware

```
Feature not active.
Data already collecting.

Examples:
  Tradition Engine          (Part VIII — currently DRL 1-2
                              in most markets at this writing)
  Opportunity Engine          (Part XI — DRL 0-1)
  Community Historian           (Volume VI, Part XVIII — DRL 0)
  Family Historian                 (Volume V, Chapter 23 — DRL 0)
  Trust OS                            (the wider Trust
                                       Infrastructure named in
                                       Appendix A — DRL 0-1)

Dormant today.
Ready tomorrow.
```

The crucial distinction this terminology is built to protect: a "dormant feature" is fundamentally different from a "planned feature" or a "roadmap item." A planned feature is a commitment to build something at a future date regardless of evidence. A dormant feature is a commitment to **collect the right data now**, and to build the feature **only once the data earns it** — which may be sooner or later than any calendar-based roadmap would have predicted, and which may, honestly, never happen for some dormant features if the underlying data signal never materialises the way this Bible's authors hypothesised it would. That possibility is accepted as a legitimate outcome, not a failure — Chapter 24 makes this explicit.

---

## Chapter 24 — Data-Led Feature Activation

```
OLD MODEL:
  Year 1 → Year 2 → Year 3
  (features ship because the calendar says so)

NEW MODEL:
  Data → Signals → Confidence → Activation
  (features ship when the evidence justifies them)
```

This directly operationalises the Data-Led Evolution Principle. A feature's activation is gated on its Feature Seed (Chapter 22) reaching its named DRL threshold (Part XII), full stop — not on a sprint deadline, not on competitive pressure, not on a stakeholder's enthusiasm. This is the discipline mechanism that makes it safe and honest to hold an expansive long-term vision (the full V1.1 scope, Appendix A) as genuinely canonical architecture without that expansiveness ever becoming an unfunded promise or a half-built feature shipped before its data foundation is real.

---

# Part XIV — The Historian Layer

---

## Chapter 25 — Confirming and Extending the Historian Family

Volume V, Chapter 23 already specified five Historian capabilities (Memory, Family, Community, Relationship, Legacy Historian) under the explicit principle "purpose is understanding, never surveillance." This volume adds two further Historians implied by its own temporal scope, and confirms the governing principle applies identically to both.

```
ALREADY SPECIFIED (Volume V, Chapter 23) — confirmed, unchanged:
  Memory Historian · Family Historian · Community Historian ·
  Relationship Historian · Legacy Historian

NEW, ADDED BY THIS VOLUME:

  PERSONAL HISTORIAN
    Distinct from Memory Historian's focus on curated Memory
    entities — the Personal Historian operates on a person's
    FULL temporal substrate (every DomainEvent touching their
    identity, not just their curated Memories), answering
    "tell me my story" at a level of completeness the Memory
    Historian alone cannot, because it draws on Volume III's
    Identity Timeline as well as Volume V's Memory Vault.

  CITY HISTORIAN
    Operates at the broadest scale this Bible has yet defined —
    "community ecosystem evolution" across an entire city,
    drawing on Volume V, Chapter 18's City Timeline concept,
    aggregated across many Communities' individual Timelines
    (Volume VI, Chapter 10). This Historian is also the
    direct internal engine behind the B2B Pulse Reports
    product's trend and historical-comparison features
    (Volume on B2B Product Suite, Product 1) — the same
    underlying capability serving both an internal narrative
    function and an external commercial product, governed
    by the same data-boundary rules established in Volume V,
    Chapter 23 (no Historian output is ever an input to
    advertising; B2B use is explicitly permitted ONLY for
    the City Historian, given its City/aggregate scope and
    its pre-existing commercial purpose, and explicitly
    NEVER for the five personal/family/community/relationship/
    legacy Historians, whose boundary against B2B and
    advertising use remains absolute)
```

All seven Historians — the five confirmed and the two added here — remain governed by Part XII's DRL framework and Part XIII's Dormant Feature discipline without exception. None are built at this stage. All have their Feature Seeds defined now.

---

# Part XV — The Temporal Constitution

---

## Chapter 26 — Three Principles Added to the Architecture Bible's Foundational Constitution

These three principles, alongside the four broader principles already under consideration from the wider V1.1 revision (Human Coordination, Invisible Sophistication, Data-Led Evolution, Docking), are added as binding constitutional law governing every volume that follows this one.

```
PRINCIPLE OF TEMPORAL TRUTH
  Never overwrite history when history can be preserved.

  This is the principle underlying Chapter 7's prohibition
  on directly-mutable status fields, Chapter 14's Tradition
  lifecycle (lapses are preserved, not erased), and Volume VI,
  Chapter 25's "Traditions are NEVER cancelled by leadership
  succession" — now generalised as universal law rather than
  a collection of independently-arrived-at specific rules.

PRINCIPLE OF DATA-LED EVOLUTION
  Features activate when data maturity reaches required
  thresholds.

  This is Part XII and Part XIII's DRL and Feature Seed
  machinery, elevated to constitutional status. No future
  volume may specify a feature as "built by date X" — only
  as "built when DRL threshold Y is reached."

PRINCIPLE OF HISTORICAL CONTINUITY
  Every important entity, relationship, trust signal,
  community, memory, and opportunity should be explainable
  through time.

  This is the test every future volume's data model must
  pass: can a person, years from now, ask "why is this true"
  or "how did this come to be" and receive a real, evidence-
  backed answer reconstructed from the event ledger — not
  a shrug, and not a fabricated-sounding narrative with no
  underlying data trail.
```

---

# Part XVI — MVP Scope

---

## Chapter 27 — What to Build First

Consistent with the MVP discipline established in every prior volume, the Temporal Architecture MVP is narrow — and unusually, for this volume specifically, the build/capture/defer split is less about feature surfaces and more about **schema and event discipline**, since this volume's primary deliverable is structural rigour rather than user-facing functionality.

```
BUILD (MVP):

  ✅ TemporalEntity envelope fields
     — valid_from, valid_to, observed_at, recorded_at,
       confidence, version — added to the core schema
       for Person, Community, Relationship, Memory, and
       Tradition entities from day one (this is schema
       work, not feature work, and is cheap now and
       extremely expensive to retrofit later — consistent
       with the discipline already established in Volume V,
       Chapter 28 and Volume VI, Chapter 26 for similarly
       irreplaceable foundational data)
  ✅ Event-sourced status fields
     — relationship_status and equivalent "current state"
       fields are NEVER directly written; always derived
       from event history, from the first migration onward
  ✅ The 8 new domain events (Chapter 8)
     — registered in the event type enum, even though most
       will not fire meaningfully until later DRL stages
  ✅ Basic DRL tracking
     — a simple, even manually-updated-at-MVP DRL value per
       domain (Part XII), so that the discipline habit is
       established even before automated DRL computation
       is built
```

```
CAPTURE (data foundation, not yet surfaced as features):

  📊 All four clock timestamps           (Reality, Observation,
                                          Processing, Knowledge —
                                          even where Knowledge
                                          Clock simply equals
                                          Processing Clock at
                                          MVP, absent any
                                          confidence-revision
                                          workflow yet)
  📊 Tradition-candidate signals            (already captured per
                                          Volume IV/V/VI's existing
                                          MVP scopes — this volume
                                          adds no NEW capture
                                          burden here, only the
                                          lifecycle interpretation
                                          layer, Chapter 14, which
                                          IS deferred)
  📊 Trust Timeline points                     (fire
                                          TrustScoreRecomputed
                                          on every existing trust-
                                          affecting event, even
                                          though no UI surfaces
                                          the timeline yet)
```

```
DO NOT BUILD YET:

  ❌ Tradition lifecycle state machine UI (Chapter 14) —
      detection and SEEDED-state suggestion already exist
      per Volume IV/V's specs; the full AT_RISK/LAPSED/
      REVIVED state machine surfacing is deferred
  ❌ Bitemporal query interfaces (Chapter 12) — the underlying
      data IS captured (per the BUILD section above), but no
      product surface exposes "what was true at time T" queries
      to end users yet
  ❌ Any Historian (all seven, Part XIV) — explicitly DRL 0
      for all domains at MVP, per Part XII's framework
  ❌ DRL-gated automation (DRL 4+ capabilities, Part XII) —
      no feature anywhere in the platform should take
      automated action based on DRL thresholds until a
      domain has genuinely, measurably reached DRL 4
  ❌ The Opportunity Emergence detection (Chapter 20) —
      the OpportunityEmerged event type is registered
      (per the BUILD section) but the detection LOGIC
      that would fire it is not yet built
```

**The single most important MVP discipline in this volume:** add the TemporalEntity envelope fields to the schema now, even though almost nothing reads or uses most of them yet. This is the temporal-architecture equivalent of Volume V's emotion-tag discipline and Volume VI's founding-members discipline — a small number of fields that are nearly free to capture from day one and brutally expensive to backfill once millions of rows exist without them.

---

# Volume VII Summary

```
The Temporal Thesis:
  Time is not metadata. Time is a first-class architectural
  domain. ZUKA answers not just "what is true" but "what was
  true, what changed, why, and what is likely next."

The Canonical Temporal Model:
  TemporalEntity envelope — valid_from, valid_to, observed_at,
  recorded_at, confidence, version — applied universally
  across the entire entity taxonomy

The Four Clocks:
  Reality · Observation · Processing · Knowledge
  Confirmed against and bound to existing Volume II/V fields,
  not introduced as competing structures

Event Sourcing, Mandatory:
  Confirms and universalises Volume II, Chapter 8
  Status fields structurally prohibited as directly-mutable —
  always derived from event history
  8 new domain event types registered

The Temporal Graph:
  Time as a graph dimension, not just an edge property —
  full operational specification of Volume II's previously
  under-specified sixth-graph-adjacent concept

Life Chapters & Community Chapters:
  Confirms Volume III/V's existing Life Chapter model as
  temporal in nature
  New: Community Chapters (5 types), refining Volume VI's
  five-stage lifecycle with a finer-grained Historian-facing
  lens — without complicating Volume VI's product-facing model

Bitemporal Querying:
  Two distinct historical question types formalised —
  "what was true" vs. "what did ZUKA believe was true" —
  both enabled by the same TemporalEntity envelope

The Tradition Engine:
  Formal definition: Repeated Gathering + Time + Shared
  Memory + Shared Participation
  Complete lifecycle state machine: UNRECOGNISED → SEEDED →
  ACTIVE → AT_RISK → LAPSED → REVIVED, with lapses preserved
  never erased

Memory Aging Model:
  Confirms Volume V's Memory Value Curve as a cross-platform
  ranking principle, not a Memory-layer-only concern —
  every ranking system must explicitly declare which curve
  it follows

Temporal Trust:
  Trust as a timeline, not a static number
  Decay reframed explicitly as reality, not punishment —
  governs UX copy requirements

Relationship Evolution & Opportunity Emergence:
  Relationship progressions as queryable, narratable sequences
  Opportunities emerge from sustained community/trust patterns
  before they are formally posted — new OpportunityEmerged
  signal captures this pre-formal-entity stage

Data Readiness Levels:
  DRL 0–5 framework, assessed independently per domain and
  per market
  The formal discipline mechanism resolving the V1.1 scope-
  expansion tension: ambitious architecture can be canonical
  without being built ahead of its data

Feature Seeds & Dormant Features:
  Every future feature (including the full wider V1.1 scope
  register) has a defined seed now: required events + DRL
  threshold
  Dormant ≠ vapourware ≠ guaranteed — data-led, honestly so

The Historian Layer:
  5 Historians confirmed from Volume V, 2 new added:
  Personal Historian, City Historian
  City Historian uniquely permitted for B2B/aggregate use;
  all six others retain the absolute no-advertising,
  no-B2B boundary

The Temporal Constitution:
  3 new binding principles: Temporal Truth, Data-Led
  Evolution, Historical Continuity

MVP Scope:
  4 build items (schema/event discipline, not features),
  3 data-capture priorities, 5 explicit deferrals
  Critical discipline: TemporalEntity envelope fields from
  day one — irreplaceable if missed, parallel to Volumes
  V and VI's equivalent disciplines
```

Without Temporal Architecture, ZUKA is a platform. With Temporal Architecture, ZUKA becomes a living historical system capable of understanding how people, communities, trust, traditions, and opportunities evolve across years and, eventually, generations.

---

# Appendix A — The Dormant Feature Register (V1.1 Scope Reconciliation)

This appendix is where the wider V1.1 Architecture Revision document is formally filed, using this volume's own Data Readiness Level and Dormant Feature machinery, rather than retroactively rewriting Volumes II through VI's existing, deliberately narrow MVP sections.

**Why this approach, stated plainly:** the V1.1 revision document contains genuinely good long-range thinking — Trust OS, Community OS, the Opportunity Economy, AI Twins, Federation/Docking architecture, the reframe from "gatherings" to "human coordination" — alongside real scope-inflation risk if absorbed wholesale and immediately. This volume's own machinery, built independently of that concern, turns out to be exactly the right tool to hold both truths at once: this expanded vision can be **canonical** (worth designing for, worth this Bible naming and respecting) **without being imminent** (none of it overrides any prior volume's MVP scope).

```
REGISTERED DORMANT FEATURES (V1.1 Scope), each assessed per
Part XII's framework, each requiring its own Feature Seed
per Part XIII:

  Trust OS / Trust Ledger / Trust Capital (full)    DRL 0–1
  Opportunity Engine / Opportunity Graph (full)       DRL 0–1
  Community OS (9-layer model)                          DRL 0
  Community App Store                                      DRL 0
  Community Maturity Score                                   DRL 0
  AI Twins (all five named variants)                            DRL 0
  Federation & Docking Architecture                                DRL 0
  Memory Federation (external import: Google Photos,                 DRL 0
    Facebook, Instagram)
  Virtual/Digital Gathering as equal citizen to Physical                 DRL 0
  Community Treasury / Community Wallet                                    (confirmed
                                                                            already
                                                                            DEFERRED in
                                                                            Volume VI,
                                                                            Part XV —
                                                                            no change)
  Sponsorship Marketplace / Creator Economy                                       DRL 0
  Community GDP                                                                      DRL 0

NOT YET ADOPTED INTO THIS BIBLE AS CANONICAL — held for
explicit founder review before even Dormant-Feature status
is granted, given the scale of reframe involved:

  The platform-identity reframe itself — "Operating System
  for Human Coordination" vs. the existing "Operating System
  for Human Gatherings" (Volume I) — is NOT adopted in this
  volume. This is a brand- and identity-level decision, not
  a temporal-architecture one, and belongs in a formal Volume I
  amendment with the same deliberateness Volume I itself
  received, not as a side effect of this volume's appendix.

  "Civilization Architecture" as a named future volume —
  held pending the same review, for the same reason.
```

```
ADOPTED INTO THIS VOLUME'S CONSTITUTION (Part XV), low-risk,
pure discipline, no scope inflation:

  ✅ Principle of Temporal Truth
  ✅ Principle of Data-Led Evolution
  ✅ Principle of Historical Continuity

CARRIED FORWARD FOR FOUNDER DECISION, not yet adopted,
genuinely good candidates for Volume I amendment:

  · Invisible Sophistication Principle (3-5 clicks,
    <30 seconds, AI invisible, progressive disclosure) —
    strong product discipline, recommended for adoption,
    requires only a short Volume I amendment, not a
    restructuring of any existing volume
  · Docking Principle (loosely coupled, capability-based,
    reversible, observable, versioned external integrations) —
    strong infrastructure discipline, recommended for
    adoption, belongs in a future Infrastructure &
    Ecosystem volume when that volume is actually written,
    not retrofitted into this one
```

This appendix itself is a living document. As each Dormant Feature's relevant domain crosses its next DRL threshold, it graduates out of this register and into an active specification within whichever volume governs it — at that point, and not before.

---

# What Volume VIII Must Address

Volume VIII — Knowledge Graph & Semantic Intelligence Architecture is where collected data becomes meaning, and where the foundation is laid for Trust OS, Opportunity Infrastructure, Community Intelligence, AI Twins, and the long-term Human Coordination Infrastructure vision — all of it, per this volume's Appendix A, still governed by the DRL and Dormant Feature discipline established here.

Volume VIII must resolve:

1. **Unifying the Six Graphs** — Volume II named Entity, Knowledge, Temporal, Trust, Opportunity, and Memory graphs; this volume's source material additionally names Tradition Graph, Community Graph, and Digital Presence Graph — Volume VIII must reconcile whether these are genuinely new graphs or facets of the existing six
2. **Semantic Readiness** — extending this volume's DRL framework with the specific semantic/embedding-quality thresholds (building on Volume II, Chapter 16's embedding layer) required before knowledge-graph-dependent features can activate
3. **The Signal Layer** — the architecture for converting raw DomainEvents (this volume, Chapter 8) into the structured "signals" this volume's DRL assessments (Part XII) depend on
4. **Data Quality Engine** — how ZUKA detects and handles low-confidence, contradictory, or degraded data within the temporal substrate this volume establishes
5. **Explainable Trust** — extending Part X's Temporal Trust with the requirement that any computed trust score be traceable back to the specific events that produced it
6. **Knowledge Graph Versioning** — how the unified knowledge graph evolves as embedding models improve (extending Volume II, Chapter 16's `embedding_model_version` field) without invalidating historical analysis
7. **The relationship between Volume VII's Dormant Feature Register (Appendix A) and Volume VIII's Knowledge Graph** — specifically, which DRL thresholds are computed FROM the knowledge graph itself, creating the bootstrapping question of how a knowledge graph that doesn't exist yet helps determine when it's ready to exist

---

*Volume VII is canonical. Extensions require architecture review.*
*Contradictions with this volume must be resolved before implementation.*
*Appendix A is explicitly a living register, distinct from the canonical chapters above it — its contents may be revised without a full architecture review, provided no change contradicts Chapters 1–27.*

---

> **ZUKA Architecture Bible**
> Volume VII — Temporal Architecture
> Built in Kampala. Built for Africa. Built for the world.
