# ZUKA Architecture Bible
# Volume XII — Data Architecture & Data Governance

> **Series:** ZUKA Architecture Bible
> **Volume:** XII of N
> **Status:** Canonical — reconciles three independently-worded prior DRL ladders
>              and one prior graph count into single, lockstep specifications
> **Depends on:** The full Bible to date (Volumes I-XI)
> **Required by:** Volume XIII (Trust Infrastructure & Governance) and every
>                  future volume touching data quality, lineage, or portability

---

# Preface

Data is the primary strategic asset of ZUKA. Code is replaceable. Infrastructure is replaceable. Applications are replaceable. Data, history, relationships, trust, and knowledge are not.

This claim is not new to this volume — it is, in substance, the Crown Jewel Declaration Volume VIII, Chapter 28 already adopted as canonical. This volume's first job, addressed directly in Part I, is confirming that relationship rather than letting two differently-worded versions of the same claim stand unreconciled in the Bible. Its second and larger job is specifying, in full operational detail, three things this Bible has named but never actually built out: the Data Quality Framework, Data Lineage, and Data Portability — none of which existed anywhere in this Bible before this volume, despite being referenced in passing.

---

# Part I — Reconciling the Data Moat Claim

---

## Chapter 1 — One Declaration, Not Two

```
THIS VOLUME'S SOURCE MATERIAL CLAIMS:

  The moat is not: Source code, UI, Infrastructure
  The moat is: Temporal Knowledge Graph + Trust Graph +
  Community Graph + Opportunity Graph + Decades of historical
  continuity

VOLUME VIII, CHAPTER 28 ALREADY DECLARED:

  Temporal Knowledge Graph + Trust Graph + Opportunity Graph
  + Community Graph, as the primary strategic asset of ZUKA —
  not the mobile app, not the website, not the backend.
```

These are the same declaration, stated twice, by two different authors of this Bible, using nearly identical language and an identical four-graph emphasis. This is confirmed here as **one declaration**, not two competing ones. Volume VIII, Chapter 28 remains the canonical statement of record, for one precise reason: it already performed the graph-count reconciliation (Volume VIII, Chapter 7) that this volume's source material has not yet done — see Chapter 2 below — and a declaration naming specific graphs should be anchored to the volume that actually defined what those graphs are.

**This volume's sole addition to the declaration:** "decades of historical continuity," named in this volume's source material but not in Volume VIII's original wording. This phrase is adopted as a genuine, valuable addition — it makes explicit the TEMPORAL dimension of the moat (why a ten-year-old graph is categorically more defensible than a one-year-old graph of identical structure), which Volume VIII's declaration implied through "Temporal Knowledge Graph" but did not state as plainly. The combined, final, single declaration going forward:

```
ZUKA's primary strategic asset is the combination of:
  the Knowledge Graph (Volume VIII's seven official graphs,
  viewed through Volume VII's temporal lens), with particular
  commercial and provenance emphasis on the Trust, Opportunity,
  and Community graphs specifically (per Volume VIII, Ch.28's
  original reasoning), made categorically more defensible with
  every year of accumulated, undecayed historical continuity
  (Volume V's entire Memory Value Curve thesis, now stated
  explicitly as a MOAT property, not merely a product feature).

This is not the mobile app. Not the website. Not the backend.
The graph, and the years behind it.
```

---

## Chapter 2 — Correcting the Graph Count

This volume's source material names the Knowledge Graph Foundation as six graphs — Identity, Community, Memory, Trust, Opportunity, Tradition — forming, in its words, the "Human Coordination Graph."

```
THIS IS CORRECTED HERE AGAINST VOLUME VIII, CHAPTER 7's
ALREADY-SETTLED RECONCILIATION:

  The canonical count is SEVEN graphs, not six: Identity,
  Community, Memory, Trust, Opportunity, Tradition, AND
  Digital Presence (Volume VIII, Ch.14) — with Temporal
  confirmed as a CROSS-CUTTING DIMENSION over all seven,
  not an eighth peer graph and not absent from the count
  entirely.

  "Human Coordination Graph" is NOT itself a graph in this
  taxonomy. Per Volume VIII, Chapter 15's explicit ruling,
  it is the adopted INTERNAL ENGINEERING NAME for the
  combined seven-graph system when discussed as a whole —
  and Volume VIII, Chapter 15 was equally explicit that
  adopting this internal engineering name does NOT constitute
  adoption of "Human Coordination" as ZUKA's PUBLIC platform
  identity, which remains an open item reserved for founder
  review (first raised in Volume VII, carried forward through
  Volume VIII and Volume X's closing sections, and still
  unresolved at this writing).

THIS VOLUME ADOPTS VOLUME VIII'S SEVEN-GRAPH COUNT WITHOUT
MODIFICATION. Any future reference in this Bible, including
in volumes already written, that states "six graphs" or "five
graphs" should be read as superseded by Volume VIII, Chapter 7,
now reconfirmed here.

The omission of Digital Presence from this volume's source
material is not treated as a deliberate exclusion requiring
separate justification — Digital Presence remains, per Volume
VIII, Chapter 14, filed at the lowest DRL with its full
specification deliberately deferred pending dedicated privacy
review, and its absence from a high-level summary list is
unsurprising precisely because it is the least-developed of
the seven. It remains one of the seven for completeness and
consistency, not because it has gained any new specification
in this volume.
```

---

# Part II — The Data Constitution

---

## Chapter 3 — Three Principles, Adopted

```
PRINCIPLE 1 — DATA IS SACRED
  Every piece of data collected must have a purpose.
  Questions: Why are we collecting it? Which domain owns it?
  Which future systems depend on it? What DRL level can use it?

PRINCIPLE 2 — COLLECT ONCE, USE MANY TIMES
  A signal should be collected once and reused across Trust,
  Communities, Opportunities, AI Twins, Historians,
  Recommendations.

PRINCIPLE 3 — REALITY FIRST
  The data model must represent reality, not screens.
  Wrong: EventScreen, FeedPage, ProfilePage
  Correct: Person, Community, Gathering, Memory, Trust,
  Opportunity
```

All three are adopted as canonical, without modification. Each is already, in substance, a restatement of discipline this Bible has practiced consistently since Volume II — Principle 1 is the question every "irreplaceable if missed" field flagged across Volumes V through XI was already implicitly answering; Principle 2 is the literal mechanism behind Volume VIII's SemanticExtension structure (signals accumulating once, read by many future consumers); Principle 3 is Volume II, Chapter 1's entire entity-not-screen taxonomy, restated here as an explicit constitutional rule rather than left implicit in the modelling choices themselves. Their value in this volume is making explicit, as named principles, disciplines that were previously only demonstrated through example.

---

# Part III — DRL Reconciliation Across Three Ladders

---

## Chapter 4 — The Third Independently-Worded Ladder

This volume's source material presents a third version of the DRL ladder, worded differently again from both Volume VII's data-maturity framing and Volume XI's infrastructure-capability framing:

```
DRL 0  — Schemas only. Allowed: Design, Contracts, Validation.
DRL 1  — Data collection. Allowed: Metrics, Dashboards.
          Forbidden: Predictions, Trust scoring.
DRL 2  — Pattern detection. Allowed: Trends, Emerging
          traditions, Community growth analysis.
DRL 3  — Confidence achieved. Allowed: Matching,
          Recommendations, Explainable scoring.
DRL 4  — Automation. Allowed: Automatic categorisation,
          Automatic grouping, Automated recommendations.
DRL 5  — Intelligence. Allowed: AI Twins, Historians,
          Community Copilots, Trust Intelligence.
```

Per this volume's own opening commitment (Preface), this is reconciled here rather than left as a third independent voice describing the same six levels.

```
THE RECONCILIATION, NOW COMPLETE ACROSS ALL THREE LADDERS:

  DRL  Volume VII (data maturity)   Volume XI (infra capability)   Volume XII (data USE permission)
  0    No data                      Schemas only                   Schemas only
  1    Collection started           Transactional systems          Collection; FORBIDS predictions/trust scoring
  2    Patterns visible             Analytics systems               Pattern detection
  3    Useful insights possible     Recommendations                   Confidence achieved; matching/recommendations
  4    Automation possible          Automation                          Automation
  5    AI-grade intelligence        AI Twins, Copilots, Intelligence      Intelligence (Twins, Historians, Copilots)

ALL THREE describe the SAME six-level gate from three
vantage points — data evidence (Volume VII), infrastructure
permission (Volume XI), and DATA USE permission (this volume,
the newest and narrowest lens: not "is there enough data" or
"can we build the system," but specifically "what is this
PARTICULAR dataset, at its current DRL, allowed to be USED
FOR"). All three move in lockstep by construction, exactly as
Volume XI, Chapter 7 already established for its own
reconciliation against Volume VII.

THIS VOLUME'S ONE GENUINE ADDITION, not present in either
prior ladder, and adopted here as a valuable strengthening of
the framework: DRL 1's EXPLICIT FORBIDDEN LIST ("Forbidden:
Predictions, Trust scoring"). Neither Volume VII nor Volume XI
stated a forbidden list at any level — both specified only
what IS permitted at each level, leaving "not yet permitted"
to be inferred by omission. This volume's explicit prohibition
is a meaningfully stronger guardrail than mere omission, and
it is adopted as a GENERAL PATTERN going forward: every DRL
level in this Bible's three ladders should, on next revision,
carry both an ALLOWED list and a FORBIDDEN list, not an
allowed list alone. This volume's DRL 1 forbidden list is the
first instance of that stronger pattern and is retained
exactly as specified.
```

---

## Chapter 5 — The DRL Activation Contract

```
EVERY FEATURE MUST DEFINE:
  Required entities
  Required events
  Required signals
  Required relationships
  DRL threshold
  Activation criteria

NO FEATURE LAUNCHES WITHOUT MEETING ITS DRL REQUIREMENTS.
```

This is reconciled directly against Volume XI, Chapter 13's Founder Rule, which this volume's own later "Founder Rules" section (Chapter 12, below) also restates with partial overlap. Rather than allow three checklists (this chapter's six fields, Volume XI's six fields, and this volume's own later six fields) to drift, the reconciliation is performed once, here, and Chapter 12 explicitly inherits it rather than restating it independently.

```
RECONCILIATION:

  This chapter's "DRL Activation Contract" fields (Required
  entities, events, signals, relationships, DRL threshold,
  activation criteria) and Volume XI, Chapter 13's "Founder
  Rule" fields (Domain, Events, Data Contracts, DRL Level,
  Graph Edges, Future Dependencies) are NOT two separate
  checklists a feature proposal must pass. They are the SAME
  underlying review, described at two granularities:

    Volume XI's Founder Rule is the COARSE, first-pass gate —
    does this feature belong to a real domain, does it use
    real event types, what DRL does it target. This is the
    gate applied when a feature is FIRST PROPOSED.

    This chapter's DRL Activation Contract is the FINE-GRAINED,
    pre-launch gate — once a feature has passed Volume XI's
    coarse gate and has been built to the point of being
    DRL-eligible, THIS contract is what is checked immediately
    before the feature is actually switched on for users.

  "Required entities/events/signals/relationships" in this
  chapter map directly onto Volume XI's "Data Contracts" and
  "Graph Edges" fields, made more specific and enumerable at
  the point of launch rather than at the point of proposal.
  "DRL threshold" and "activation criteria" are this chapter's
  sharper restatement of Volume XI's "DRL Level" field,
  now requiring not just a STATED target but a CHECKED,
  PASSED threshold before launch is permitted.

  The combined, single process going forward:
    PROPOSAL → Volume XI, Ch.13's Founder Rule (coarse gate)
    → BUILD → THIS chapter's DRL Activation Contract
    (fine-grained, pre-launch gate) → LAUNCH
```

---

# Part IV — Feature Seed Architecture, Confirmed

---

## Chapter 6 — No New Specification Required

```
EVERY FUTURE FEATURE BEGINS AS A SEED.

Examples: Trust Engine, Opportunity Engine, Tradition Engine,
Community Historian.

Even when dormant, required data collection begins immediately.
This eliminates cold-start problems.
```

This is confirmed as identical, without modification, to Volume VII, Chapter 22-23's Feature Seed Architecture and Dormant Features specification, already fully built out there with the Trust Engine example used nearly verbatim. No new specification is required or added here — this volume's role is solely to confirm that the Feature Seed concept, as it appears in a third independent source document, refers to the same mechanism Volume VII already canonised, rather than a competing or subtly different one. The Dormant Feature Registers this principle produces remain exactly where Volume VII's Appendix A, Volume VIII's Part X, and Volume IX/X's DRL accounting chapters left them — still owed a unified, cross-volume dashboard, per Volume X, Chapter 16's Item 1, which remains outstanding after this volume.

---

# Part V — Temporal Data Architecture, Confirmed

---

## Chapter 7 — No New Specification Required

```
EVERY MEANINGFUL FACT HAS:
  Valid From, Valid To, Observed At, Recorded At, Confidence

Time is a first-class dimension.
```

This is confirmed as identical to Volume VII, Chapter 3's TemporalEntity envelope, field for field — `valid_from`, `valid_to`, `observed_at`, `recorded_at`, `confidence` map exactly onto Volume VII's already-specified structure (Volume VII additionally specifies a `version` field, which this volume's source material omits; that field is confirmed as still required, per Volume VII's original specification, since omission in a summary document does not constitute removal of a canonical field). No new specification is added.

---

# Part VI — Data Quality Framework

---

## Chapter 8 — The First Full Specification

This is the first of three genuinely new contributions this volume makes to the Bible (alongside Data Lineage, Part VII, and Data Portability, Part VIII) — named in the source material as a list of mandatory controls, but never before specified in operational detail anywhere in this Bible.

```
MANDATORY CONTROLS (as named):
  Duplicate detection
  Missing field detection
  Broken relationship detection
  Invalid timestamps
  Orphaned entities

BAD DATA COMPOUNDS. GOOD DATA COMPOUNDS.
```

The closing principle — bad data compounds, good data compounds — is the data-quality-specific instance of this entire Bible's central compounding thesis (Volume I's flywheel, Volume IV's network effects, Volume V's appreciating memory value), and it is the correct justification for treating data quality as architecture rather than as routine operational housekeeping: an uncorrected duplicate Person entity does not merely cost one bad row, it corrupts every downstream Twin, Agent, and graph traversal that ever touches that Person, compounding in exactly the same multiplicative way Volume IV's gathering flywheel compounds in the positive direction.

```typescript
DataQualityCheck {
  check_id:              UUID
  check_type:                DataQualityCheckType
  // 'duplicate_detection' | 'missing_field_detection' |
  // 'broken_relationship_detection' | 'invalid_timestamp' |
  // 'orphaned_entity'

  applies_to_domain:             DomainType
  // One of Volume XI, Chapter 3's eight domains

  // ── DUPLICATE DETECTION ──────────────────────────────────
  // Specific to Person entities (most consequential case,
  // given Volume III's Identity is the root of every graph):
  duplicate_detection_strategy: {
    primary_signal:           'phone_number_hash'
    // Per Volume III, Ch.7's existing rule that phone number
    // is the primary identifier — one account per phone
    // number, OTP-enforced
    secondary_signals:           ['device_fingerprint',
                                   'embedding_similarity']
    // Per Volume III, Ch.28's existing identity theft
    // mitigations (device fingerprinting) and Volume II,
    // Ch.16's embedding layer (a near-duplicate Person
    // embedding is itself a quality signal, not just a
    // recommendation input)
    confidence_threshold:           Float
    action_below_threshold:           'flag_for_review'
    action_above_threshold:             'auto_merge_candidate'
    // NEVER auto-merge without human confirmation for Person
    // entities specifically, given Volume III's entire
    // Identity Architecture treats identity continuity as
    // sacred — auto-merging two real people's histories
    // incorrectly would be a Volume III, Ch.28-adjacent
    // identity-integrity failure, not a minor data hygiene
    // slip
  }

  // ── MISSING FIELD DETECTION ──────────────────────────────
  missing_field_detection_strategy: {
    required_fields_by_entity_type:    Map<EntityType, Field[]>
    // Derived directly from each entity's canonical schema
    // across Volumes II-XI — e.g., per Volume IX, Ch.16, a
    // Personal Twin's TwinLegacyBinding.steward_id is
    // REQUIRED once default_policy departs from
    // 'immediate_on_death', and its absence in that state
    // is precisely the kind of missing-field defect this
    // control exists to catch before it becomes a real
    // legacy-handling failure
    severity:                              'blocking' | 'warning'
  }

  // ── BROKEN RELATIONSHIP DETECTION ────────────────────────
  broken_relationship_detection_strategy: {
    // Checks every Relationship entity (Volume II, Ch.4) for
    // referential integrity: does source_entity_id and
    // target_entity_id both resolve to a live, non-deleted
    // entity? Does relationship_type match a type valid for
    // BOTH entities' types (per Volume II, Ch.5's registry —
    // a SPOUSE relationship between a Person and a Venue is
    // a broken-relationship defect, not a valid edge)
    check_frequency:          'on_write' | 'scheduled_sweep'
    // on_write: a database-level constraint check, the
    // first line of defence
    // scheduled_sweep: catches relationships that became
    // broken AFTER creation — e.g., the source entity was
    // later soft-deleted (Volume III, Ch.7's DELETED status)
    // without its relationships being properly wound down
  }

  // ── INVALID TIMESTAMP DETECTION ──────────────────────────
  invalid_timestamp_detection_strategy: {
    // Specifically validates the Four Clocks (Volume VII,
    // Part III) against each other for logical consistency:
    rules: [
      'observed_at >= valid_from OR is_backdated = true',
      // (per Volume V, Ch.5's backdating exception — a
      // genuinely backdated Memory is NOT a quality defect,
      // it is an explicitly flagged, confidence-scored
      // historical claim; this rule exists specifically to
      // distinguish a LEGITIMATE backdated entry from an
      // accidental clock-order violation)
      'recorded_at >= observed_at',
      // Processing Clock can never precede Observation Clock
      'valid_to IS NULL OR valid_to > valid_from',
      // A relationship or state cannot end before it begins
    ]
  }

  // ── ORPHANED ENTITY DETECTION ────────────────────────────
  orphaned_entity_detection_strategy: {
    // An entity with ZERO relationships of any kind, after
    // a defined grace period since creation — this is a
    // SOFT signal, not an automatic deletion trigger, since
    // Volume V's entire Memory-without-media,
    // Media-without-Memory pattern (Volume V, Ch.6) means
    // some genuinely valid entities ARE meant to exist with
    // sparse connections; this control exists to SURFACE
    // candidates for review, never to auto-prune
    grace_period_days:        Integer
    action:                       'flag_for_review'
    // NEVER 'auto_delete' — consistent with this Bible's
    // consistent preference, established across Volumes
    // V-X, for preservation over deletion as the default
    // posture
  }

  detected_at:                    Timestamptz
  resolved_at:                       Timestamptz
  resolution_action:                    TEXT
}
```

---

## Chapter 9 — Why Auto-Correction Is Deliberately Restrained

The pattern visible across every sub-strategy in Chapter 8 — flag for review rather than auto-correct, with the single narrow exception of database-level referential integrity constraints — is stated here as an explicit, named design principle, because it is easy to mistake restraint for incompleteness rather than recognise it as a deliberate choice consistent with this Bible's broader values.

```
THE PRINCIPLE: data quality controls in ZUKA DETECT
aggressively and CORRECT conservatively.

This is the direct consequence of two already-canonical
commitments converging: Volume VII's Temporal Truth
Principle ("never overwrite history when history can be
preserved") and Volume III's treatment of Identity continuity
as sacred. An automated system empowered to silently merge,
delete, or "correct" entities at scale is an automated system
empowered to silently REWRITE HISTORY at scale — precisely
the failure mode this Bible has guarded against since Volume
VII first established the append-only Event Store. Data
quality tooling is therefore architected to surface problems
loudly and resolve them through human judgment, even at the
cost of accumulating a visible backlog of flagged issues —
that backlog is a feature, not a bug, because it is the
auditable record of every judgment call this discipline
requires, consistent with Volume VIII's Knowledge Provenance
principle applied to the data-quality process itself.
```

---

# Part VII — Data Lineage

---

## Chapter 10 — The Second New Specification

```
EVERY INSIGHT MUST ANSWER:
  Where did it come from?
  Which events contributed?
  Which entities participated?
  What confidence exists?
```

This is the data-architecture-layer restatement of a principle this Bible has already built real infrastructure for — Volume VIII, Chapter 20's `ProvenanceRecord` already answers exactly these four questions (`evidence_events` for "which events contributed," the broader record structure for "where did it come from," `evidence_events` traced back to entities for "which entities participated," and the `confidence` field inherited from Volume VII, Chapter 19 for "what confidence exists"). This volume's contribution is not a new structure but the explicit naming of "Data Lineage" as the formal data-architecture discipline this `ProvenanceRecord` infrastructure already implements, plus one genuinely new operational addition: lineage as a queryable, traversable property in its own right, not merely an attached record.

```sql
-- A genuinely new capability this chapter introduces: a
-- LINEAGE QUERY that traces backward through Volume X,
-- Chapter 11's two-hop AgentProvenanceRecord chain to its
-- full depth, regardless of how many Agent/Twin layers deep
-- the original insight passed through

WITH RECURSIVE lineage_trace AS (
  -- Base case: the insight in question
  SELECT
    conclusion_id,
    conclusion_type,
    evidence_events,
    inherited_twin_provenance,
    0 AS depth
  FROM provenance_records
  WHERE conclusion_id = :insight_id

  UNION ALL

  -- Recursive case: follow inherited_twin_provenance chains
  -- (Volume X, Ch.11) back through each layer
  SELECT
    pr.conclusion_id,
    pr.conclusion_type,
    pr.evidence_events,
    pr.inherited_twin_provenance,
    lt.depth + 1
  FROM provenance_records pr
  JOIN lineage_trace lt
    ON pr.conclusion_id = ANY(lt.inherited_twin_provenance)
  WHERE lt.depth < 10  -- safety bound against any
                       -- unexpected cyclical reference,
                       -- though Volume X's architecture
                       -- should make cycles structurally
                       -- impossible given the asynchronous
                       -- write-then-read handoff rule
                       -- (Volume X, Ch.12)
)
SELECT * FROM lineage_trace ORDER BY depth;

-- This single query answers, for ANY insight anywhere in
-- the platform — a single Twin's output, or a five-layer-deep
-- Agent synthesis built on Agent output built on Twin output
-- built on raw DomainEvents — the complete, full-depth
-- provenance chain in one traversal, fulfilling Volume VIII
-- Ch.20's "Knowledge Provenance" principle at full operational
-- scale, not merely as a two-hop guarantee per single layer.
```

This is the genuinely new contribution: Volume VIII and Volume X both guaranteed that provenance NEVER terminates at an unverifiable summary, one hop at a time. This chapter is the first place in the Bible specifying the QUERY that actually walks that guarantee to its full depth in a single operation — turning a structural guarantee into an operationally usable capability.

---

# Part VIII — Data Portability

---

## Chapter 11 — The Third New Specification

```
USERS MUST BE ABLE TO:
  Export data
  Export memories
  Export communities
  Export histories

ANTI-LOCK-IN IS A CONSTITUTIONAL PRINCIPLE.
```

This is reconciled against, and extends, ZUKA.md's existing Privacy Architecture data export commitment (the "Download My Data" capability already specified in the broader Bible's privacy sections) and Volume III, Part VI's Data Access Request. This volume's contribution is elevating that existing user-level commitment to a named, constitutional, PLATFORM-level principle — "anti-lock-in" — and extending its scope explicitly beyond a single person's own data to the three additional, genuinely harder export categories this volume's source material names: Memories, Communities, and Histories.

```typescript
DataPortabilityExport {
  export_id:               UUID
  requesting_identity_id:        UUID
  export_scope:                    PortabilityScope

  PortabilityScope enum:
    'personal_data'
    // Already specified — Volume III, Part VI's existing
    // Data Access Request, unmodified

    'memories'
    // NEW, full specification below — exports the
    // requesting person's Memory Vault (Volume V), including
    // every Memory they created or are tagged in at a
    // visibility level they're entitled to export, WITH
    // its full Media Layer references resolved into actual
    // downloadable files (not merely pointers), and WITH
    // its ProvenanceRecord chain (Chapter 10's lineage
    // query) included so the export is independently
    // verifiable even outside ZUKA's own systems

    'communities'
    // NEW, full specification below — exports a Community's
    // data, but ONLY where the requesting identity holds
    // sufficient governance authority to authorise it
    // (per Volume VI, Ch.16's governance_change_policy,
    // treated identically to any other consequential
    // governance-layer decision) — never exportable by a
    // single ordinary member acting alone, given the
    // Community's data belongs to the institution, not to
    // any individual member, per Volume VI, Ch.9's
    // entire "community memory survives individual memory"
    // thesis

    'histories'
    // NEW, full specification below — exports the FULL
    // event-sourced history (Volume VII's DomainEvent
    // ledger, Volume XI Ch.8's Event Store) for the scope
    // already authorised under one of the three categories
    // above — i.e., not the CURRENT STATE of a person's
    // data, but the complete, time-ordered SEQUENCE of
    // events that produced that state, consistent with
    // Volume VII's Temporal Truth Principle that history,
    // not just current state, is the thing worth preserving
    // and, per this chapter, worth being EXPORTABLE

  format:                       'json_structured' | 'human_readable_pdf'
  // Mirrors the existing dual-format pattern already
  // established for personal data exports (ZUKA.md's
  // existing privacy architecture)

  media_handling: {
    resolve_references_to_files:    true
    // Per Volume V, Ch.4's Memory ≠ Media separation: an
    // export of "memories" must resolve every media_refs
    // pointer into an actual file in the export package,
    // not merely export the Memory metadata with dangling
    // references to media the person can no longer access
    // outside ZUKA — this is the practical, operational
    // test of whether "anti-lock-in" is genuinely honoured
    // or merely claimed
  }

  processing_time_target:           '<72 hours'
  // Consistent with ZUKA.md's existing data export SLA

  delivery:                            'encrypted_download_link'
  expiry:                                 '48 hours'
}
```

```
THE COMMUNITIES EXPORT — WHY IT IS DELIBERATELY THE
HARDEST CASE, AND WHY THAT IS CORRECT:

  A Community export is, by construction, the most
  consequential portability case in this chapter, because it
  intersects directly with Volume VI's entire governance and
  succession architecture (Volume VI, Part XIV, Part XXII).
  Allowing any single member to unilaterally export — and, by
  implication, threaten to take elsewhere — a community's
  entire Archive, membership history, and governance record
  would directly undermine Volume VI, Chapter 9's foundational
  claim that "a community's memory is not the sum of its
  members' individual memories" and Chapter 24's claim that
  "communities outlive individuals." Anti-lock-in, correctly
  understood, protects an individual's right to leave a
  platform with what is THEIRS — it does not grant any single
  member the unilateral right to extract what belongs to an
  institution larger than themselves. This chapter's gating of
  Community exports behind genuine governance authority is
  therefore not a weakening of the anti-lock-in principle but
  its correct, careful application — anti-lock-in for
  individuals, properly distinguished from anti-lock-in for
  institutions, which is a governance question, not merely a
  data-access question.
```

---

# Part IX — Founder Rules, Reconciled

---

## Chapter 12 — Confirming Inheritance From Chapter 5

```
THIS VOLUME'S SOURCE MATERIAL'S OWN "FOUNDER RULES" SECTION
ASKS:
  1. What new entities?
  2. What new relationships?
  3. What new events?
  4. What new graph edges?
  5. Which DRL level?
  6. Which future systems depend on it?

  If unanswered: Feature rejected.
```

Per Chapter 5's reconciliation, this is confirmed as the SAME underlying review already reconciled there between Volume XI's Founder Rule and this volume's own DRL Activation Contract — not a fourth independent checklist. This specific six-question phrasing is adopted as the PREFERRED PHRASING for the coarse, first-pass proposal gate specifically (Volume XI, Chapter 13's role in the combined process), because its question-form ("What new entities?") is more naturally usable as an actual conversation prompt in a proposal review than Volume XI's field-label form ("Domain") — while the underlying six fields being checked remain identical across all three documents' wording. No new gate is created. The wording is adopted; the mechanism is confirmed as already-existing.

---

# Part X — MVP Scope

---

## Chapter 13 — What to Build First

```
BUILD (MVP):

  ✅ The Data Quality Framework's five check types (Ch.8),
     implemented as a combination of database-level
     constraints (broken relationships, invalid timestamps —
     enforceable on_write, cheap, and consistent with Volume
     XI, Ch.14's Item 1 first-migration discipline) and a
     scheduled batch sweep (duplicate detection, missing
     field detection, orphaned entities — these require
     cross-row analysis that a single-row constraint cannot
     perform) — built from MVP specifically because, per
     Chapter 8's closing principle, BAD DATA COMPOUNDS, and
     the cost of detecting it early is dramatically lower
     than the cost of discovering it after years of
     downstream Twin/Agent/graph computation has been built
     on top of it

  ✅ The lineage trace query (Ch.10) — built and tested
     against the existing ProvenanceRecord and
     AgentProvenanceRecord schemas (Volume VIII, IX, X),
     even though, per those volumes' own MVP findings, there
     is little real provenance data yet to trace — the query
     itself is cheap to build now and proves correct before
     it is ever genuinely load-bearing, consistent with the
     "prove it before it is load-bearing" discipline this
     Bible has applied consistently since Volume IX

  ✅ The 'personal_data' DataPortabilityExport scope (Ch.11) —
     this is NOT new work; it is the existing ZUKA.md privacy
     architecture export capability, simply confirmed here as
     falling under this volume's now-formal Data Portability
     principle
```

```
DO NOT BUILD YET:

  ❌ The 'memories', 'communities', and 'histories'
      DataPortabilityExport scopes (Ch.11) — genuinely new
      capability, correctly deferred until there is
      meaningful Memory Vault and Community Archive content
      for any user to actually want exported; building this
      against near-empty MVP-stage data provides no real
      product value yet, though the SCHEMA fields (the
      PortabilityScope enum itself) should exist now per this
      Bible's consistent "structural fields are cheap now"
      pattern
  ❌ Auto-merge or any other automated correction action for
      ANY data quality check type — per Chapter 9's explicit
      restraint principle, every check type's MVP
      implementation resolves to 'flag_for_review' only, with
      human-in-the-loop resolution, full stop, regardless of
      how confident any future automated merge candidate
      scoring becomes
```

**The single most important MVP discipline in this volume:** build the Data Quality Framework's detection (not correction) capability from day one, specifically because Chapter 8's closing principle — bad data compounds exactly as relentlessly as good data does — means this is the one category of "irreplaceable if missed" risk in this Bible that compounds in the WRONG direction if neglected, rather than simply representing a missed opportunity.

---

# Volume XII Summary

```
Data Moat Claim, Reconciled:
  Confirmed as the SAME declaration as Volume VIII, Ch.28's
  Crown Jewel Declaration, not a competing one — "decades of
  historical continuity" adopted as a genuine, valuable
  addition to the original four-graph wording

Graph Count, Corrected:
  This volume's source material's six-graph framing corrected
  against Volume VIII, Ch.7's already-settled seven-graph count
  (the omission was Digital Presence) — "Human Coordination
  Graph" reconfirmed as an internal engineering name only, not
  a brand decision, not an eighth graph

The Data Constitution:
  3 principles adopted unmodified — Data is Sacred, Collect
  Once Use Many Times, Reality First — each confirmed as
  already-practiced discipline now given explicit named status

DRL, Reconciled Across Three Ladders:
  Volume VII (data maturity) / Volume XI (infrastructure
  capability) / Volume XII (data use permission) — one gate,
  three vantage points, full reconciliation table
  This volume's genuine addition: explicit FORBIDDEN lists per
  DRL level, not just allowed lists — adopted as a strengthening
  pattern for all three ladders going forward

DRL Activation Contract:
  Reconciled as the fine-grained, pre-launch companion to
  Volume XI's coarse, first-pass Founder Rule — one combined
  process: Proposal → Founder Rule → Build → Activation
  Contract → Launch

Feature Seed Architecture & Temporal Data Architecture:
  Both confirmed as identical to Volume VII's already-canonical
  specifications — no new content, explicit confirmation only

Data Quality Framework (NEW):
  First full specification — 5 check types, each fully
  structured, governed by an explicit detect-aggressively/
  correct-conservatively principle directly derived from
  Volume VII's Temporal Truth and Volume III's Identity
  sanctity commitments

Data Lineage (NEW):
  Confirmed as the data-architecture restatement of Volume
  VIII's already-built ProvenanceRecord infrastructure, with
  one genuine addition: a full-depth recursive lineage trace
  query, turning the existing two-hop-per-layer guarantee into
  an operationally walkable, arbitrary-depth capability

Data Portability (NEW):
  Extends ZUKA.md's existing personal data export into 4
  scopes — personal_data (existing), memories, communities,
  histories (all new) — with Communities export deliberately
  gated behind genuine governance authority, correctly
  distinguishing individual anti-lock-in from institutional
  anti-lock-in rather than conflating the two

Founder Rules, Reconciled:
  Confirmed as the same 6-field review already reconciled in
  Chapter 5, with this volume's question-form phrasing adopted
  as the preferred conversational wording for the coarse
  proposal-stage gate specifically

MVP Scope:
  3 build items (data quality detection, lineage query,
  existing personal export confirmed) — explicit emphasis that
  detection-only data quality tooling is uniquely urgent given
  bad data's compounding cost
  2 categories of explicit deferral (new portability scopes,
  any automated correction action)
```

This volume added no new philosophy and very little new architecture. Its primary value is the work most Bible-writing efforts skip: taking three independently-worded versions of the same DRL ladder, two independently-stated versions of the same moat declaration, and one undercounted graph registry, and making them say the same thing, in one place, so that no future engineer ever has to wonder which version is the real one.

---

# What Volume XIII Must Address

Trust Infrastructure & Governance Architecture — already uploaded alongside this volume's source brief — is, per its own opening claim, set to become one of the largest and most consequential volumes in the Bible, given how many prior volumes (III, VI, VII, VIII, X, and now XII) have each specified a PIECE of Trust without ever assembling the complete picture. Volume XIII must, at minimum:

1. **Perform the same DRL ladder reconciliation this volume performed** — Volume XIII's own source material presents a fourth independently-worded DRL ladder ("DRL Trust Activation Matrix"), requiring the same lockstep treatment as Chapter 4 above
2. **Assemble the Trust Pyramid** (Identity Trust → Behavioral Trust → Community Trust → Economic Trust → Stewardship Trust) against every trust-dimension specification already scattered across Volume III (Trust Dimensions), Volume VI (Community Trust), Volume VIII (Trust Graph), and Volume X (Trust Agent) — confirming whether this Pyramid is a genuinely new structural layer or a relabelling of existing dimensions
3. **Fully specify the Anti-Fraud Architecture** (Sybil Detection, Trust Farming Detection, Collusion Detection, Fake Review Detection) — none of which have been specified in operational detail anywhere in this Bible, despite fraud prevention being referenced in passing since ZUKA.md's original §26
4. **Specify Governance & Appeals** in full — the first place in this Bible a formal appeals mechanism for any automated or semi-automated conclusion (a trust score, a flagged data quality issue per this volume's Chapter 8, a Twin or Agent output per Volumes IX-X) would be specified
5. **Confirm the Trust → Opportunity Pipeline** against Volume VII, Chapter 20 and Volume X, Chapter 7's already-specified opportunity emergence mechanics

---

> **ZUKA Architecture Bible**
> Volume XII — Data Architecture & Data Governance
> Built in Kampala. Built for Africa. Built for the world.
