# ZUKA Architecture Bible
# Volume XI — Infrastructure Architecture

> **Series:** ZUKA Architecture Bible
> **Volume:** XI of N
> **Status:** Canonical — the build-order and technology specification for everything
>              named in Volumes I through X
> **Depends on:** The full Bible to date (Volumes I-X)
> **Required by:** Every engineering decision from this point forward; the eventual
>                  Infrastructure & Ecosystem / Docking volume this volume's Part V
>                  begins but does not complete

---

# Preface

Every prior volume in this Bible has specified *what* ZUKA is — its entities, its identity model, its gatherings, its memory, its communities, its time, its knowledge, its Twins, its Agents. None of them specified *how* any of it gets built, in what order, on what technology, or at what physical layer the abstractions in Volumes II through X actually run.

This volume is that specification. It is, deliberately, the most engineering-literal volume in the Bible so far — fewer philosophical openings, more direct technology and sequencing decisions — because its job is to give a working engineering team an honest, buildable path from a blank repository to everything this Bible has promised, without ever requiring that team to throw away their work and start over.

The mission, stated exactly as the source material states it, because it is the correct mission and needs no improvement: **build infrastructure that can evolve from MVP to a civilisation-scale coordination platform without repeated rewrites.**

---

# Part I — The Infrastructure Constitution

---

## Chapter 1 — Four Principles

```
PRINCIPLE 1 — MODULAR MONOLITH FIRST
  Start with a modular monolith.
  Optimise for speed of learning rather than distributed
  complexity.

PRINCIPLE 2 — DOMAIN ISOLATION
  No domain leakage.

PRINCIPLE 3 — EVENT-FIRST ARCHITECTURE
  All meaningful actions emit events.

PRINCIPLE 4 — DRL GOVERNANCE
  No infrastructure capability may activate beyond the
  maturity of supporting data.
```

These four principles are adopted into this Bible's constitution without modification. Principle 3 and Principle 4 require no reconciliation — they are the infrastructure-layer restatement of Volume VII's event sourcing mandate (Volume VII, Part IV) and DRL framework (Volume VII, Part XII), already fully specified there. Principle 1 and Principle 2 are new to this volume and are expanded fully in Part II.

---

## Chapter 2 — Why Modular Monolith First, Stated Plainly

A modular monolith is a single deployable application, internally organised into strictly-separated modules (Chapter 3's eight domains), rather than a constellation of independently-deployed microservices from day one.

This is not a compromise or a temporary embarrassment to be apologised for. It is the correct engineering decision for ZUKA's actual situation, and the reasoning is worth stating in full because it will be questioned, repeatedly, by engineers joining later who have only ever worked at companies already past this stage:

```
Microservices solve a problem ZUKA does not yet have:
  independent team scaling, independent deployment cadences,
  and independent failure isolation across dozens of engineers
  working on the same system simultaneously.

Microservices CREATE a problem ZUKA cannot yet afford:
  distributed system complexity — network calls where function
  calls used to be, eventual consistency where transactions
  used to be, and a substantially higher cost of CHANGING
  the domain model while it is still being learned.

ZUKA, at MVP and for some time after, is in the LEARNING phase
of its domain model, not the SCALING phase. Volumes II through
X collectively represent an enormous amount of carefully
reasoned architecture — but architecture reasoned about on
paper is not the same as architecture validated against real
user behaviour, and the entire history of software engineering
shows that the first real contact with users reveals things
the design phase did not anticipate.

A modular monolith lets the TEAM change the domain model
quickly, in a single codebase, with the compiler/type system
catching cross-domain mistakes immediately — exactly the kind
of fast-iteration environment a system still learning its own
shape needs. A microservices architecture would make every one
of those learning-phase corrections an order of magnitude more
expensive, for a scaling benefit ZUKA does not yet need.
```

---

## Chapter 3 — Domain Isolation, The Eight Domains

```
DOMAINS:
  Identity
  Community
  Gathering
  Memory
  Trust
  Opportunity
  Payments
  Notifications

NO DOMAIN LEAKAGE.
```

Each domain corresponds directly to a Volume already specified in this Bible, and the mapping is made explicit here because it is the basis for the module boundaries this principle requires:

```
Identity        → Volume III (Identity Architecture)
Community        → Volume VI (Communities Architecture)
Gathering          → Volume IV (Gatherings Engine)
Memory               → Volume V (Memory Architecture)
Trust                  → Volume III, Part V + Volume VI, Part XI
                       + Volume VIII, Ch.11 + Volume X, Ch.3
                       (Trust Agent)
Opportunity               → Volume II §3.10 + Volume IV, Ch.17
                       + Volume VIII, Ch.12 + Volume X, Ch.7
                       (Opportunity Agent)
Payments                     → ZUKA.md's economic model
                       (ticketing, payouts, Event Wallet)
Notifications                   → ZUKA.md §21
```

"No domain leakage" means, concretely: the Gathering module's code may never directly query or write to a table the Memory module owns. If the Gathering module needs Memory data, it does so through the Memory module's own defined interface — even though, at MVP, both modules live in the same deployable application and the same physical database. This discipline is what makes the eventual extraction of any one domain into its own service (Part II, Stage 3-4, if and when warranted) a refactor of interface boundaries rather than a rewrite of business logic — the entire justification for Principle 1 depends on Principle 2 being genuinely enforced from the first commit, not retrofitted once leakage has already happened.

Notably absent from this list, and deliberately so: Knowledge Graph, Twin, and Agent are NOT separate domains in this list. This is addressed directly in Chapter 11 — they are cross-cutting capabilities that read from every domain above, not domains that own their own primary data.

---

# Part II — Infrastructure Evolution

---

## Chapter 4 — The Four Stages, As Specified

```
STAGE 1 — MVP
  Flutter
  Modular Monolith
  PostgreSQL
  Redis
  Object Storage

STAGE 2 — GROWTH
  Event Bus
  Search Layer
  Read Models

STAGE 3 — SCALE
  Event Streaming
  Data Lake
  Knowledge Graph

STAGE 4 — PLATFORM
  Community OS
  Trust OS
  Opportunity OS
  AI Infrastructure
```

This four-stage progression is adopted as canonical. Its single most important property, worth stating explicitly because it is easy to misread a numbered list as a fixed calendar: **these are NOT time-bound phases.** Per Principle 4 (Chapter 1) and the entire DRL discipline established across Volumes VII through X, a stage is entered when the PRECONDITIONS for it are genuinely met — primarily, sufficient transaction volume and data maturity to justify the additional operational complexity each stage introduces — never on a roadmap date. This volume's job is to specify what each stage CONTAINS and what triggers the transition INTO it (Chapter 5), not to predict WHEN any given platform or market will reach it.

---

## Chapter 5 — Stage Transition Triggers

This is the chapter that turns Chapter 4's list from a wishlist into an actionable specification — each stage transition is gated on a concrete, observable trigger, not a feeling that "it's probably time."

```
STAGE 1 → STAGE 2 TRIGGER:
  Observable signal: the modular monolith's domain modules
  (Chapter 3) begin showing genuine, sustained CONTENTION —
  specifically, read-heavy domains (Gathering discovery,
  feed queries) and write-heavy domains (Memory uploads,
  Gathering check-ins) competing for the same PostgreSQL
  resources in ways that degrade either path's performance
  below acceptable thresholds, OR the first cross-domain
  query pattern that PostgreSQL's standard indexing cannot
  serve fast enough (Volume IV's heat map, ZUKA.md §20's
  search and discovery, both being the most likely first
  candidates).

  This is the trigger for introducing an Event Bus and Read
  Models (CQRS-adjacent — see Chapter 6) and a dedicated
  Search Layer, NOT a trigger for abandoning the modular
  monolith itself, which remains the correct architecture
  for the write/transactional path even at Stage 2.

STAGE 2 → STAGE 3 TRIGGER:
  Observable signal: the DRL framework (Volume VII, Part XII)
  begins reporting that multiple domains have genuinely,
  independently crossed DRL 2 (pattern detection becomes
  possible, per this volume's own DRL ladder, Chapter 7) —
  meaning there is now enough accumulated, multi-domain
  historical data that batch/streaming analytics over a
  Data Lake produces genuinely different value than the
  Stage 2 Read Models can provide, AND the Knowledge Graph
  (Volume VIII) has enough underlying entity and edge volume
  that graph-native query patterns (multi-hop traversals,
  Volume VIII Ch.4's "how did James meet Sarah") start
  timing out or becoming impractical against PostgreSQL's
  recursive CTEs (which Volume VIII, Ch.7 explicitly
  specified as sufficient at MVP — this is the trigger for
  when that sufficiency runs out).

STAGE 3 → STAGE 4 TRIGGER:
  Observable signal: per Volume IX and Volume X's own DRL
  registers, MULTIPLE Twin types and MULTIPLE Agent types
  have independently, genuinely reached DRL 3+ across a
  meaningful population of entities (not just one pilot
  community or one enthusiastic family) — meaning the
  Community OS, Trust OS, and Opportunity OS (Chapter 11)
  are no longer single-instance experimental capabilities
  but recurring, platform-wide functions that justify their
  own dedicated infrastructure investment.
```

The discipline this chapter enforces: a team is never permitted to justify a Stage transition by pointing at Chapter 4's list and saying "we should have an Event Bus because Stage 2 says so." They must point at Chapter 5's specific, measured trigger having actually fired.

---

## Chapter 6 — Stage 2 Detail — What "Event Bus, Search Layer, Read Models" Actually Means

```
EVENT BUS
  A message broker (the specific product choice is left
  open at this writing — Redis Streams, given Redis is
  already in the Stage 1 stack per Chapter 4, is the lowest-
  friction first choice, with a dedicated broker such as
  NATS or Kafka considered only once Redis Streams'
  throughput or durability genuinely becomes limiting)
  consuming the SAME DomainEvents already mandated by
  Volume VII, Part IV's event sourcing requirement — this
  is NOT a new event type system, it is a new DISTRIBUTION
  mechanism for events that already exist in the append-only
  ledger (Volume II, Ch.8; Volume VII, Ch.6).

SEARCH LAYER
  A dedicated search index (e.g., a managed Postgres full-
  text/vector extension at the low end, or a dedicated search
  engine such as Meilisearch or Elasticsearch as volume grows)
  serving the discovery and search functions specified in
  ZUKA.md §20 — populated via the Event Bus above, kept
  eventually-consistent with the operational PostgreSQL
  database, never treated as the source of truth (the
  operational database, governed by Volume II's Entity model,
  remains the source of truth at every stage).

READ MODELS
  Denormalised, purpose-built views of data, populated
  asynchronously via the Event Bus, optimised for specific
  READ patterns that the normalised operational schema serves
  poorly under load (the City Pulse Strip's live event count,
  ZUKA.md §5; a Community's aggregate trust profile, Volume VI,
  Part XI) — this is the infrastructure-layer mechanism that
  makes Volume VI, Chapter 6's "computed" fields (member counts,
  trust scores) genuinely fast at scale, where at Stage 1 they
  may simply be computed on-demand via a database query.
```

---

## Chapter 7 — DRL Infrastructure Gates

```
DRL 0 — Schemas only
DRL 1 — Transactional systems
DRL 2 — Analytics systems
DRL 3 — Recommendations
DRL 4 — Automation
DRL 5 — AI Twins, Copilots and Intelligence Systems
```

This ladder is adopted as canonical, and it requires one explicit reconciliation against Volume VII, Part XII's original DRL ladder, because the two describe DRL through different lenses and a reader moving between volumes needs the mapping stated plainly rather than left to infer.

```
RECONCILIATION:

  Volume VII's DRL ladder (Part XII) describes DRL in terms
  of DATA MATURITY — what the underlying evidence can
  honestly support (no data → collection started → patterns
  visible → useful insights → automation possible → AI-grade).

  This volume's DRL ladder describes DRL in terms of
  INFRASTRUCTURE CAPABILITY — what kind of SYSTEM is permitted
  to be built and run at each level (schemas only →
  transactional → analytics → recommendations → automation →
  intelligence systems).

  These are NOT two competing ladders. They are the SAME six
  levels (0-5) viewed from two sides of the same gate: Volume
  VII's ladder asks "is the DATA ready for this," and this
  volume's ladder asks "what INFRASTRUCTURE is therefore
  permitted to exist." The two move in lockstep by
  construction — DRL 3 data maturity (Volume VII: "useful
  insights possible") is precisely what licenses DRL 3
  infrastructure (this volume: "recommendations") to be built
  at all. A team cannot build DRL 3 infrastructure against
  DRL 1 data, and conversely there is no reason to withhold
  DRL 3 infrastructure once DRL 3 data maturity has genuinely
  been reached — the two ladders are a single gate, described
  from the data side and the infrastructure side respectively.
```

---

# Part III — Storage Architecture

---

## Chapter 8 — The Five Storage Layers

```
OPERATIONAL DATABASE       PostgreSQL
EVENT STORE                 Immutable historical record
OBJECT STORAGE                Photos, Videos, Documents
SEARCH LAYER                    Indexes and retrieval
KNOWLEDGE GRAPH                   Relationships and intelligence
```

Each of these five layers maps directly onto storage decisions already made, in detail, across this Bible — this chapter's contribution is confirming the mapping and stating which layers exist from Stage 1 versus which are Stage 2+ additions.

```
OPERATIONAL DATABASE (PostgreSQL) — STAGE 1, FROM DAY ONE
  Holds every Entity (Volume II, Ch.2-3), every Relationship
  (Volume II, Part II), and the materialised CURRENT STATE
  that the append-only event log (below) projects into.
  This is the database every prior volume's TypeScript schema
  blocks describe.

EVENT STORE — STAGE 1, FROM DAY ONE
  NOT a separate physical database at Stage 1 — per Volume II,
  Ch.8's specification, the DomainEvents table lives WITHIN
  the same PostgreSQL instance as the Operational Database,
  as an append-only table with the integrity guarantees
  (no UPDATE, no DELETE, monotonic sequence_number) already
  specified there. It becomes a candidate for physical
  separation (a dedicated event-sourcing-optimised store)
  only at Stage 3, where Event Streaming infrastructure
  (Chapter 4) makes that separation valuable.

OBJECT STORAGE — STAGE 1, FROM DAY ONE
  Holds the Media Layer (Photo, Video, Audio entities) per
  Volume V, Ch.4 and Ch.27's tiered storage strategy — the
  hot/warm/cold tiering specified there maps directly onto
  this layer's lifecycle policies, with the explicit note
  (already established in Volume V) that cold-tier policies
  are a Stage 2+ cost optimisation, not a Stage 1 requirement
  (Volume V, Ch.28's MVP scope already deferred this exact
  thing).

SEARCH LAYER — STAGE 2 ADDITION
  Per Chapter 6's specification above.

KNOWLEDGE GRAPH — STAGE 3 ADDITION (with one caveat)
  The CAVEAT, stated explicitly because it could otherwise
  read as a contradiction: Volume VIII, Chapter 7 already
  established that the seven official graphs are, at MVP,
  VIEWS over the existing Entity Graph and Relationship
  tables in the SAME PostgreSQL Operational Database — "no
  new graph database is required at MVP" (Volume VIII,
  Ch.29's exact wording). This volume's "Knowledge Graph"
  storage layer, appearing at Stage 3, refers specifically
  to the point at which those views are no longer
  sufficiently performant and a DEDICATED graph-native store
  (e.g., Neo4j or Amazon Neptune, exactly as Volume II,
  Ch.13 originally flagged as the "at scale" technology
  choice) becomes warranted — per Chapter 5's Stage 2→3
  trigger. The Knowledge Graph, as a CONCEPT and as a set of
  VIEWS, exists from Stage 1. The Knowledge Graph as a
  PHYSICALLY SEPARATE storage layer is Stage 3.
```

---

# Part IV — Integration Architecture

---

## Chapter 9 — The ISS Docking Principle, Adopted

```
ALL EXTERNAL SYSTEMS CONNECT THROUGH:
  Connector Contracts
  Capability Frameworks
  Versioned Interfaces

NEVER THROUGH DIRECT COUPLING.
```

This is the same Docking Principle named, but not yet specified, across Volume VII's Appendix A and Volume VIII, Chapter 24's treatment of Knowledge Federation — both of which deferred its technical specification to "whatever future Infrastructure & Ecosystem volume eventually formalises" it. This volume is that volume, in part — it adopts the principle and gives it its first structural shape, while explicitly NOT completing the full specification, for the reason stated in Chapter 10.

```typescript
// The minimal structural shape this volume commits to —
// full specification (specific connector implementations,
// the Connector Framework's complete API surface) remains
// future work per Chapter 10

ConnectorContract {
  connector_id:           UUID
  external_system:           TEXT
  // e.g., 'safeboda' (ZUKA.md §12's Zuka Ride integration,
  // the FIRST real external connector this Bible has
  // already committed to, well before any Knowledge
  // Federation question arose)

  capability_framework:        CapabilityDeclaration[]
  // What this connector can DO — never an open-ended API
  // surface, always an enumerable, reviewable list of
  // specific capabilities (e.g., 'request_ride_quote',
  // 'deep_link_to_booking') — this is the architectural
  // mechanism that makes Volume VIII, Ch.24's caution about
  // Knowledge Federation operational: a future Google
  // Photos connector, IF ever approved per that chapter's
  // founder-review requirement, would be constrained to
  // an explicit capability list (e.g., 'import_photo_with_
  // user_consent') rather than open data access

  interface_version:              SemVer
  // Connectors are versioned independently of ZUKA's own
  // release cycle — a breaking change to SafeBoda's API
  // produces a new connector version, not a ZUKA platform
  // release

  coupling_boundary:                  'loose'
  // Always 'loose' — per the principle's "never through
  // direct coupling" rule, no domain module (Chapter 3)
  // may import or directly call an external system's SDK;
  // all such calls route through the Connector layer
}
```

---

## Chapter 10 — Why This Volume Does Not Complete the Docking Specification

This is worth stating explicitly rather than leaving the gap unexplained. Volume VII's Appendix A and Volume VIII, Chapter 24 both explicitly held back full adoption of Knowledge Federation and the broader Docking architecture pending founder and legal review — and that review has not yet occurred, per this Bible's record. This volume confirms the STRUCTURAL shape of connectors (Chapter 9) because that shape is needed regardless of how the federation question is resolved — ZUKA already has a committed, non-controversial external integration (Zuka Ride / SafeBoda, ZUKA.md §12) that needs a Connector Contract regardless of any Google/Meta/government question. But this volume deliberately does NOT specify the full Connector Framework, the complete Capability Framework taxonomy, or anything touching the actually-deferred external systems named in Volume VIII, Chapter 24 — that remains correctly held for the dedicated Infrastructure & Ecosystem volume Volume X, Chapter 16 (Item 3) already named as still-owed.

---

# Part V — The Three OS Layers

---

## Chapter 11 — Why These Are Cross-Cutting, Not Domains

Chapter 3 explicitly excluded Community OS, Trust OS, and Opportunity OS from the eight core domains. This chapter explains why, and specifies what each actually is at the infrastructure level.

```
A DOMAIN (Chapter 3) owns its own PRIMARY DATA — the
Community domain owns Community entities, the Gathering
domain owns Gathering entities, full stop.

An OS LAYER does not own primary data of its own. It is a
CROSS-CUTTING COMPOSITE VIEW, read from and synthesised across
MULTIPLE domains' data — exactly the same architectural
pattern Volume VIII, Chapter 7 already established for the
seven graphs ("implemented as views over the single underlying
Entity Graph substrate, not separately stored data") and
Volume VIII, Chapter 26 already established for the Community
Genome ("not a new data structure — a proposed composite view").

This volume's three OS layers are the INFRASTRUCTURE-LAYER
expression of exactly that same pattern, now named at the
platform-capability level rather than the data-model level:
```

```
COMMUNITY OS
  Composite view drawing on: Community domain (primary),
  Identity domain (membership), Trust domain (Community
  Trust Profile, Volume VI Part XI), Memory domain (Community
  Archive, Volume V Ch.17).
  Components named in the source material — Identity,
  Governance, Treasury, Knowledge, Memory, Trust — map
  directly onto: Volume VI's Community entity fields
  (identity, governance, economics), Volume V's Community
  Archive, Volume VI Part XI's Trust Profile, and Volume
  VIII Ch.17's still-Dormant CommunityKnowledgeBase.
  "Each community is treated as a mini operating system" is
  adopted as the GUIDING METAPHOR for this composite view,
  not as a literal claim that each Community entity runs its
  own isolated software instance — it remains, architecturally,
  one composite read pattern over the shared multi-tenant
  Operational Database (Chapter 8), exactly as every other
  entity is served.

TRUST OS
  Composite view drawing on: Trust domain (primary, Volume X
  Ch.3's Trust Agent output), Identity domain, Community domain.
  Components — Trust Ledger, Trust Graph, Trust Analytics,
  Trust Intelligence — map directly onto: the append-only
  Event Store filtered to Trust-type DomainEvents (the
  "Ledger"), the Trust Graph already specified in Volume
  VIII Ch.11, Stage 2's Read Models applied to trust data
  (the "Analytics"), and Volume X's Trust Agent at DRL 3+
  (the "Intelligence"). Full specification of Trust OS as
  a named governance and infrastructure concept is the
  primary subject of Volume XIII, which extends this
  volume's infrastructure framing into complete operational
  detail.

OPPORTUNITY OS
  Composite view drawing on: Opportunity domain (primary,
  Volume X Ch.7's Opportunity Agent output), Trust domain
  (per Volume VII Ch.20's trust-precedes-opportunity pattern),
  Community domain.
  Components — Matching, Sponsorships, Mentorships, Jobs,
  Partnerships — map directly onto Volume II §3.10's existing
  Opportunity entity taxonomy and Volume X Ch.7's Opportunity
  Agent Mode 2 (Matching).
```

**The single rule this chapter establishes for engineering practice:** no team should ever be asked to "build Community OS" or "build Trust OS" as if it were a standalone service with its own database. These three names refer to composite read/synthesis capabilities over the eight real domains (Chapter 3) — they are Stage 4 capabilities (Chapter 4) precisely because composite views over immature underlying domains produce nothing useful, per Volume VII's entire DRL discipline, regardless of how good the infrastructure serving them is.

---

# Part VI — Observability

---

## Chapter 12 — The Four Required Outputs and Four Required Answers

```
EVERY DOMAIN MUST EMIT:
  Logs
  Metrics
  Events
  Traces

QUESTIONS THAT MUST ALWAYS BE ANSWERABLE:
  What happened?
  Why?
  When?
  Why?
  Who?
```

This requirement is adopted as canonical, with one important reconciliation: "Events," in this observability context, are NOT the same artifact as the DomainEvents already mandated by Volume VII's event sourcing architecture, and conflating the two would be a real engineering mistake worth heading off explicitly.

```
DOMAIN EVENTS (Volume II, Ch.8; Volume VII, Ch.6)
  Business-meaningful, permanent, append-only facts about
  the WORLD this platform represents — "James checked into
  Friday Madness," "A Community Tradition was confirmed."
  These are themselves part of the PRODUCT — they are read
  by Twins, Agents, Historians, and feed the Knowledge Graph.
  Retained indefinitely, per Volume V's entire memory-
  preservation thesis.

OBSERVABILITY EVENTS (this chapter)
  Operational, infrastructure-meaningful facts about the
  SYSTEM itself — "the Gathering domain's check-in endpoint
  returned a 500 error," "the Search Layer's query latency
  exceeded 200ms." These are NOT part of the product, are
  NOT read by any Twin or Agent, and are retained per
  standard operational log-retention policy (weeks to
  months), NOT indefinitely.

  The four "answerable questions" (what/why/when/who) are
  asked of OBSERVABILITY data, in service of operating the
  platform — they are the infrastructure-layer mirror of
  Volume VIII, Chapter 20's Knowledge Provenance principle
  ("every conclusion must be traceable to its source
  signals"), applied here to SYSTEM BEHAVIOUR rather than to
  PRODUCT CONCLUSIONS. The two principles share a family
  resemblance (both demand traceability) but operate on
  entirely separate data with entirely separate retention
  and access rules — a support engineer debugging a 500
  error should never need, and should never be granted,
  access to the same DomainEvent data a Trust Agent reads
  to compute a person's trust trajectory, even though both
  are, in the loosest sense, "events."
```

---

# Part VII — The Founder Rule

---

## Chapter 13 — Every Feature Proposal's Required Fields

```
EVERY FEATURE PROPOSAL MUST DEFINE:
  Domain
  Events
  Data Contracts
  DRL Level
  Graph Edges
  Future Dependencies
```

This is adopted as canonical and binding — not aspirational guidance, an actual gate a feature proposal must pass before implementation begins. Each field is mapped to where it is actually adjudicated:

```
Domain                  → Must be one of Chapter 3's eight,
                          or explicitly justified as a
                          cross-cutting OS-layer concern
                          (Part V) with founder sign-off,
                          given Chapter 11's explicit warning
                          against treating OS layers as
                          ad-hoc dumping grounds for anything
                          that doesn't fit cleanly elsewhere

Events                     → Must reference EXISTING DomainEvent
                          types from Volume II, Ch.9's registry
                          (as extended by Volume VII, Ch.8;
                          Volume VIII, Ch.20-ish extensions;
                          Volume IX, Ch.15; Volume X, Ch.11) OR
                          propose new ones using that exact
                          registry's naming convention and
                          append-only guarantees

Data Contracts                → Must specify the TypeScript-style
                          entity/structure definitions this
                          Bible has used consistently since
                          Volume II, Chapter 2

DRL Level                       → Must state, explicitly, which
                          rung of Chapter 7's ladder (this
                          volume) / Volume VII Part XII's
                          ladder (data side) the feature
                          targets, and — per Volume VII,
                          Chapter 24's Data-Led Evolution
                          Principle, now constitutional —
                          must NOT state a target date instead

Graph Edges                       → Must specify which of
                          Volume VIII, Chapter 7's seven
                          official graphs (or new edge types
                          within them, following Volume VIII,
                          Chapter 4's RECOMMENDED-edge precedent
                          for how new edge types are properly
                          introduced) the feature reads from
                          or writes to

Future Dependencies                  → Must reference the
                          relevant Dormant Feature Register
                          entry (Volume VII Appendix A;
                          Volume VIII Part X; Volume IX/X's
                          DRL accounting chapters) if the
                          feature builds toward, or depends
                          on, anything currently filed there
```

A feature proposal missing any of these six fields is, per this Founder Rule, not yet ready for implementation — this is the single most actionable governance mechanism in the entire Bible to date, because it is the literal checklist a person reviewing a pull request, a sprint plan, or a hiring brief for "build the X feature" can apply directly.

---

# Part VIII — MVP Scope

---

## Chapter 14 — What to Build First

Unlike Volumes V through X, where the MVP discipline was about restraining a feature-rich domain volume down to a narrow build list, this volume IS substantially the MVP specification already — Stage 1 (Chapter 4) is, by definition, the build list. This chapter's job is narrower: confirming the specific sequencing within Stage 1.

```
BUILD, IN THIS ORDER (MVP / Stage 1):

  1. ✅ PostgreSQL Operational Database, schema for the eight
        domains (Chapter 3), with EVERY structural field
        already flagged as "irreplaceable if missed" across
        Volumes V-X included from the FIRST migration:
        emotion tags + tag-consent (Volume V), founding_
        members + leadership_history (Volume VI), the
        TemporalEntity envelope (Volume VII), SemanticExtension
        + ProvenanceRecord (Volume VIII), Twin/Agent schema +
        consent infrastructure (Volume IX/X) — this single
        migration is the most consequential piece of work in
        this entire Bible's MVP path, because every one of
        these fields was specifically flagged, in its own
        volume, as cheap now and brutally expensive to retrofit

  2. ✅ The append-only DomainEvent table (Volume II, Ch.8),
        within the same PostgreSQL instance (Chapter 8) — every
        domain's writes go through this table, not direct
        mutation, from the first line of application code

  3. ✅ Flutter mobile client + the modular monolith backend
        (Principle 1), with the eight domains (Chapter 3) as
        strictly-separated internal modules from the start

  4. ✅ Redis — for session state, rate limiting (per ZUKA.md
        §26's fraud prevention rate limits), and as the future
        Stage 2 Event Bus's likely first implementation
        (Chapter 6)

  5. ✅ Object Storage — hot-tier only (Volume V, Ch.27),
        for Photo/Video/Audio entities

  6. ✅ The Founder Rule (Chapter 13) as an actual, enforced
        process — a feature proposal template, reviewed before
        any sprint planning, not merely a document sitting in
        this Bible unused
```

```
DO NOT BUILD YET:

  ❌ Event Bus, Search Layer, Read Models, or anything else
      named under Stage 2 (Chapter 4) until Chapter 5's
      specific trigger fires — building these speculatively
      "because Stage 2 says so" is precisely the failure mode
      Chapter 5 exists to prevent
  ❌ Any dedicated graph database, data lake, or event
      streaming infrastructure (Stage 3) — per Chapter 8,
      the Knowledge Graph exists as VIEWS over PostgreSQL at
      MVP and remains sufficient until Stage 3's trigger
      genuinely fires
  ❌ Community OS, Trust OS, Opportunity OS, or AI
      Infrastructure (Stage 4) as named, dedicated
      infrastructure investments — per Chapter 11, these are
      composite views that produce nothing useful before the
      underlying domains have matured, regardless of how much
      infrastructure is thrown at them prematurely
  ❌ Any Connector beyond the minimum needed for ZUKA's own
      already-committed integrations (Zuka Ride/SafeBoda,
      payment processors) — the full Connector Framework and
      Capability taxonomy (Chapter 10) remains correctly
      deferred to the still-owed Infrastructure & Ecosystem
      volume
```

**The single most important MVP discipline in this volume, and arguably the single most important sentence in this entire Bible's engineering guidance to date:** Item 1 above — the first migration — is where every "irreplaceable if missed" field flagged across six prior volumes either gets captured correctly, once, or gets permanently and expensively lost. This volume's primary practical contribution is making that fact unmissable by collecting every one of those scattered warnings into a single, ordered build checklist.

---

# Volume XI Summary

```
The Infrastructure Constitution:
  4 principles adopted: Modular Monolith First, Domain
  Isolation, Event-First Architecture, DRL Governance —
  the latter two confirmed as infrastructure-layer
  restatements of Volume VII's already-canonical event
  sourcing and DRL mandates

Domain Isolation:
  8 domains, each mapped explicitly to its governing Volume —
  Identity, Community, Gathering, Memory, Trust, Opportunity,
  Payments, Notifications
  Knowledge Graph, Twin, and Agent explicitly NOT domains —
  resolved in Part V as cross-cutting composite capabilities

Infrastructure Evolution:
  4 stages (MVP, Growth, Scale, Platform), explicitly NOT
  time-bound — gated on Chapter 5's specific, observable
  triggers, never a calendar
  Full technology specification per stage, with explicit
  reconciliation of "Knowledge Graph as views" (Stage 1,
  already true per Volume VIII) vs. "Knowledge Graph as
  dedicated storage" (Stage 3, a genuinely new commitment)

DRL Infrastructure Gates:
  6-level ladder (Schemas → Transactional → Analytics →
  Recommendations → Automation → Intelligence), reconciled
  explicitly as the INFRASTRUCTURE-side view of the same gate
  Volume VII's DRL ladder describes from the DATA side — one
  gate, two vantage points, moving in lockstep by construction

Storage Architecture:
  5 layers, each mapped to existing Bible specification —
  Operational DB and Event Store and Object Storage from Stage
  1; Search Layer at Stage 2; Knowledge Graph as dedicated
  storage at Stage 3 (as views, already Stage 1)

Integration Architecture:
  ISS Docking Principle adopted, given minimal structural
  shape (ConnectorContract) sufficient for already-committed
  integrations (Zuka Ride/SafeBoda) — full Connector Framework
  and Capability taxonomy explicitly NOT completed here,
  correctly held for the still-owed dedicated Infrastructure
  & Ecosystem volume, consistent with Volume VII/VIII's prior
  deferrals on this exact topic

The Three OS Layers:
  Community OS, Trust OS, Opportunity OS resolved as
  cross-cutting COMPOSITE VIEWS over the 8 real domains —
  explicitly not standalone services with their own data,
  explicitly Stage 4, explicitly dependent on underlying
  domain maturity regardless of infrastructure investment

Observability:
  4 outputs (logs/metrics/events/traces), 4 answerable
  questions — with an explicit, important reconciliation
  separating OBSERVABILITY events (operational, short-
  retention, never read by Twins/Agents) from DOMAIN events
  (product-meaningful, permanent, the actual subject of
  Volumes II-X) — a distinction this volume is the first to
  draw explicitly and one that protects against a real
  category-confusion risk in implementation

The Founder Rule:
  6 mandatory fields for every feature proposal (Domain,
  Events, Data Contracts, DRL Level, Graph Edges, Future
  Dependencies), each mapped to exactly where in this Bible
  it is adjudicated — the single most actionable governance
  checklist produced so far

MVP Scope:
  6 ordered build items, with Item 1 (the first migration,
  including every "irreplaceable if missed" field flagged
  across Volumes V-X) identified as the single most
  consequential piece of work in the Bible's entire MVP path
  4 categories of explicit deferral, each tied to Chapter 5's
  trigger discipline
```

This volume does not introduce new philosophy. It takes everything Volumes I through X have promised and gives it a build order, a technology stack, and a checklist — the difference between an architecture that is merely well-reasoned and one that is actually buildable by a real team without losing its own discipline along the way.

---

# What Volume XII Must Address

Data Architecture & Governance — already uploaded alongside this volume's source brief — is, per its own opening claim, the volume that establishes data (not code, not infrastructure, not applications) as ZUKA's primary strategic asset. Given this volume's own findings, Volume XII must, at minimum:

1. **Reconcile its own graph count** against Volume VIII, Chapter 7's already-settled seven-graph reconciliation (Identity, Community, Memory, Trust, Opportunity, Tradition, Digital Presence) — the uploaded source material names only six and re-asserts "Human Coordination Graph" as if it were a graph rather than, per Volume VIII Chapter 15's explicit ruling, an internal engineering NAME for the combined system
2. **Reconcile its DRL ladder** against both Volume VII's data-maturity ladder and this volume's Chapter 7 infrastructure-capability ladder — a third independently-worded ladder describing the same six levels needs the same lockstep reconciliation this volume just performed
3. **Confirm or extend the Founder Rule** (this volume, Chapter 13) — the uploaded source material's own "Founder Rules" section asks an overlapping but not identical set of questions, requiring explicit reconciliation rather than two competing checklists
4. **Resolve the Data Moat Strategy's claim** against Volume VIII, Chapter 28's already-adopted Crown Jewel Declaration — confirming whether this is the same declaration restated or a genuinely distinct claim
5. **Specify the Data Quality Framework, Data Lineage, and Data Portability sections** in full operational detail, none of which have been specified anywhere in this Bible to date despite being named in the uploaded source material

---

> **ZUKA Architecture Bible**
> Volume XI — Infrastructure Architecture
> Built in Kampala. Built for Africa. Built for the world.
