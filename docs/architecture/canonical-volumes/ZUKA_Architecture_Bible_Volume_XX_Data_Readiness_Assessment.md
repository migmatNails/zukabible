# ZUKA Architecture Bible
# Volume XX — Data Readiness Assessment Operational Procedures

> **Series:** ZUKA Architecture Bible
> **Volume:** XX of N
> **Status:** Canonical — operationalises the DRL framework established across
>              Volumes VII, XI, XII, XIII, XIV into a reusable assessment procedure
> **Depends on:** Volume VII (DRL framework, DataReadinessAssessment structure),
>                  Volume XI (infrastructure capability gate), Volume XII (data use
>                  permission gate + Data Quality Framework), Volume XIII (trust-
>                  specific gate), Volume XIV (CRL governing inequality),
>                  Dormant Feature Register
> **Required by:** Every future DRL graduation event; every Founder Rule proposal
>                  (Volume XI Ch.13 / Volume XII Ch.12); every DRL Activation
>                  Contract (Volume XII Ch.5); the Dormant Feature Register's
>                  graduation discipline

---

# Preface

The ZUKA Architecture Bible has, across Volumes VII through XIV, produced the most rigorously specified maturity-gating framework in any platform architecture this author has encountered — four independently-worded DRL ladders reconciled into one, a CRL axis alongside it, a governing inequality binding both, and a Dormant Feature Register tracking every capability awaiting graduation.

There is one thing the Bible did not produce: **the operational procedure for actually performing an assessment.**

The `DataReadinessAssessment` structure (Volume VII, Part XII) exists. Its four signals — volume, density, temporal span, confidence — are named. The six DRL levels and their criteria are defined four different ways, all confirmed equivalent. The Dormant Feature Register's graduation discipline states that "a domain's DataReadinessAssessment has genuinely, measurably crossed its stated threshold" is required for graduation. But no volume ever specified *how* to measure whether it has crossed.

This volume closes that gap. It turns the framework's conceptual specification into an operational procedure a real team can execute against real data, on a real cadence, to produce real graduation decisions — without relying on gut feel, competitive pressure, or roadmap dates as substitutes for evidence.

It also introduces two items explicitly named in the continuation brief as not yet written anywhere in the Bible: the **five-dimension scoring model** (Volume, Quality, Freshness, Coverage, Stability) and the complete **DRL Activation Contract pre-launch checklist** as a reusable form, not merely a conceptual description.

---

# Part I — Grounding in Prior Volumes

---

## Chapter 1 — What Volume VII Established

Volume VII, Part XII's `DataReadinessAssessment` structure, reproduced here for this volume's operational grounding:

```typescript
DataReadinessAssessment {
  domain:                TemporalDomain
  // assessed independently per domain, per city/market

  current_drl:               Integer(0–5)
  assessed_at:                  Timestamptz

  volume_signal:                  Integer    // raw count of relevant events
  density_signal:                    Float   // relationship/connection density
  temporal_span_signal:                Integer  // months of history available
  confidence_signal:                     Float  // avg TemporalEntity.confidence

  next_drl_threshold:                       DRLThreshold
  // The specific, named criteria required to advance —
  // always explicit and inspectable, never a black box
}
```

Volume VII specified the STRUCTURE. This volume specifies how to POPULATE it — what queries to run, what thresholds to check, what the output means, and what happens next.

---

## Chapter 2 — The Four-Ladder Reconciliation, Preserved

Per the discipline established in Volumes XI through XIV, this volume does not introduce a fifth independently-worded DRL ladder. The single six-level gate, confirmed across all four prior wordings, governs this volume's assessment procedure identically:

```
DRL 0   Schemas only
DRL 1   Collection — FORBIDDEN: predictions, trust scoring
DRL 2   Pattern detection
DRL 3   Confidence/recommendations, explainable scoring
DRL 4   Automation
DRL 5   Intelligence (Twins, Historians, Copilots)
```

What this volume adds is the **evidence standard** required to honestly claim each level — the specific, measurable signals that must be present before a `current_drl` field is set to any value above 0.

---

## Chapter 3 — The Governing Inequality Applies to Assessments Themselves

Per Volume XIV, Chapter 2:

```
manifestation_depth ≤ min(DRL_permits, BRL_built, CRL_earned)
```

*(BRL — Build Readiness Ladder — introduced by the parallel engineering session; incorporated per the continuation brief's instruction that it extends Volume XIV's original two-axis inequality without contradiction.)*

A DataReadinessAssessment is not exempt from its own discipline. An assessment that claims DRL 3 when the evidence only supports DRL 1 is itself a Knowledge Provenance violation (Volume VIII, Ch.20). This volume's procedure makes false DRL claims structurally difficult by requiring explicit evidence citations for every DRL advancement.

---

# Part II — The Five Assessment Dimensions

---

## Chapter 4 — Why Four Signals Are Not Enough

Volume VII's four signals (volume, density, temporal_span, confidence) are necessary but not sufficient for honest assessment. A domain can have high volume, moderate density, long temporal span, and reasonable confidence while still being practically unusable — because all of its data comes from one type of entity, or the data hasn't been updated in months, or it covers only one narrow slice of the intended user base.

The five dimensions adopted here — **Volume, Quality, Freshness, Coverage, Stability** — operationalise Volume VII's four signals plus Coverage, which has no counterpart in Volume VII's original structure and is the genuine addition this volume makes.

---

## Chapter 5 — The Five Dimensions, Fully Specified

```typescript
DataReadinessScore {
  // ── DIMENSION 1: VOLUME ──────────────────────────────────
  // Answers: "Is there enough raw data to draw from?"
  // Maps to: Volume VII's volume_signal

  volume: {
    domain_event_count:         Integer
    entity_population_size:         Integer
    minimum_for_drl_2:                  ThresholdSpec
    // Per-domain thresholds in Chapter 7
  }

  // ── DIMENSION 2: QUALITY ─────────────────────────────────
  // Answers: "Is the data trustworthy enough to reason from?"
  // Operationalises Volume XII Ch.8's Data Quality Framework

  quality: {
    duplicate_rate:              Float(0.0–1.0)
    // > 5%: cannot advance past DRL 2 — patterns built on
    // duplicates are patterns of the duplication, not reality

    broken_relationship_rate:        Float(0.0–1.0)

    missing_required_fields_rate:        Float(0.0–1.0)
    // Domain-specific required fields — e.g., Memory domain
    // cannot advance to DRL 3 if > 20% of Memory entities
    // are missing emotion_tags (Vol V Ch.28's
    // "irreplaceable if missed" discipline)

    clock_order_violation_rate:              Float(0.0–1.0)
    // Per Vol XII Ch.8's invalid_timestamp_detection
  }

  // ── DIMENSION 3: FRESHNESS ───────────────────────────────
  // Answers: "Is the data current enough?"
  // Partially maps to Volume VII's confidence_signal

  freshness: {
    median_data_age_days:            Integer
    last_event_recency_days:             Integer
    staleness_risk_level:                    StaleRiskLevel
    // 'current' | 'aging' | 'stale' | 'frozen'
    // Derived thresholds:
    //   'current'  — last event < 30d AND median age < 45d
    //   'aging'    — last event < 60d AND median age < 90d
    //   'stale'    — last event < 90d
    //   'frozen'   — last event ≥ 90d
    // A 'frozen' domain CANNOT advance DRL regardless of
    // volume — per Vol VII Ch.18's trust decay principle,
    // zero fresh signal means confidence is decaying
  }

  // ── DIMENSION 4: COVERAGE ────────────────────────────────
  // Answers: "Does the data represent the full intended population?"
  // NEW — no counterpart in Volume VII's four signals

  coverage: {
    geographic_coverage:             Float(0.0–1.0)
    // Fraction of the in-scope city/market's active entity
    // population with ≥ 1 relevant DomainEvent in the period

    entity_type_coverage:                Float(0.0–1.0)
    // Fraction of required entity types with minimum viable
    // population — a capability depending on Persons,
    // Communities, AND Gatherings cannot advance if only
    // Persons have data

    cold_start_risk_level:                   ColdStartRisk
    // 'none' | 'partial' | 'high'
    // HIGH: > 30% of entity population never in any relevant
    // DomainEvent — making DRL 3+ ("recommendations")
    // dishonest for that population

    minimum_coverage_for_drl_3:                  ThresholdSpec
    // The Coverage gate most frequently blocks advancement
    // in practice: Volume and Quality can be met while
    // Coverage remains thin for the actual target user base
  }

  // ── DIMENSION 5: STABILITY ───────────────────────────────
  // Answers: "Are the patterns today likely to be true next month?"
  // Partially maps to Volume VII's confidence_signal

  stability: {
    pattern_variance_30d:            Float
    // StdDev of domain's key aggregate metrics over 30 days
    // High variance = patterns still settling, not reliable
    // enough for DRL 2's "patterns visible" claim

    entity_churn_rate:                   Float(0.0–1.0)
    // Fraction of entity population that joined/left in 30d
    // High churn = patterns computed today may not apply
    // to a different population tomorrow

    trend_direction:                         TrendDirection
    // 'growing' | 'stable' | 'declining'
    // A DECLINING domain cannot advance DRL even if current
    // totals meet thresholds — the data must be EARNING
    // its DRL, not merely having once deserved it
  }

  // ── COMPOSITE SCORE ──────────────────────────────────────
  composite_readiness_score:           Float(0.0–1.0)
  // Weighted composite for human-readable summary ONLY.
  // NOT a substitute for per-dimension assessment — all
  // five dimensions must individually clear their thresholds.
  // No dimension may "subsidise" another's shortfall.
  //
  // Approximate weights (configurable per domain):
  //   Volume   × 0.20
  //   Quality  × 0.25  (highest — bad data compounds, Vol XII Ch.8)
  //   Freshness × 0.20
  //   Coverage  × 0.20
  //   Stability × 0.15

  assessed_at:           Timestamptz
  assessed_by:               UUID  // identity_id of assessor,
  // or 'system_automated' for scheduled sweeps
  assessment_source:             AssessmentSource
  // 'manual_founder_review' | 'automated_monthly_sweep' |
  // 'triggered_by_drl_activation_contract'
}
```

---

# Part III — Assessment Domains and Their Thresholds

---

## Chapter 6 — The Domain Registry

```
DOMAIN                  PRIMARY ENTITY TYPES      RELEVANT EVENT TYPES

identity                Person                    PersonCreated, PersonVerified,
                                                  PersonUpdated, OTP events

relationship_evolution  Person ↔ Person,          RelationshipProposed,
                        Relationship              RelationshipAccepted,
                                                  RelationshipWeightUpdated

gathering               Gathering                 GatheringCreated,
                                                  CheckInRecorded,
                                                  RSVPSubmitted,
                                                  GatheringEnded

memory                  Memory, Media             PhotoUploaded, MemoryCreated,
                                                  AlbumCreated, MemoryShared

tradition               Tradition, Gathering      TraditionDetected,
                                                  TraditionStrengthened,
                                                  TraditionLapsed,
                                                  TraditionRevived

community_evolution     Community                 CommunityCreated,
                                                  CommunityMemberAdded,
                                                  CommunityMemberLeft,
                                                  CommunityGatheringCreated,
                                                  CommunityTraditionEstablished

trust                   Person, Community,        TrustGranted, TrustLost,
                        TrustEdge                 TrustScoreRecomputed,
                                                  CommunityEndorsed,
                                                  VerificationApproved,
                                                  ReviewSubmitted,
                                                  DisputeResolved

opportunity             Opportunity,              JobPosted, BookingConfirmed,
                        OpportunityEdge           SponsorshipProposed,
                                                  OpportunityEmerged,
                                                  OpportunityAccepted,
                                                  OpportunityCompleted

city_aggregate          City + all domains        All event types, k-anonymised
                                                  (Vol IX Ch.11; Vol X Ch.6)
```

---

## Chapter 7 — Per-Domain DRL Thresholds

This chapter specifies the measurable preconditions required to advance each domain from one DRL level to the next. These are the `next_drl_threshold` values the assessment's five dimensions must collectively clear.

---

### Trust Domain

```
DRL 0 → DRL 1:
  Volume:    trust-relevant event types operational in schema
  Quality:   Vol XII Ch.8 on_write constraints active
  Freshness: ≥ 1 TrustScoreRecomputed event in last 30 days
  Coverage:  n/a (schema-only threshold)
  Stability: n/a

DRL 1 → DRL 2:
  Volume:    ≥ 500 distinct Person entities with ≥ 1
             trust-relevant event each
  Quality:   duplicate_rate < 3%; broken_relationship_rate < 2%
  Freshness: median_data_age_days < 90; staleness_risk ≠ 'frozen'
  Coverage:  ≥ 40% of active Person population has ≥ 1
             trust signal
  Stability: trend_direction ≠ 'declining';
             pattern_variance_30d < 0.30

DRL 2 → DRL 3:
  Volume:    ≥ 2,000 distinct Persons with ≥ 3 trust events each
  Quality:   duplicate_rate < 1%; confidence_signal ≥ 0.70;
             all 6 TrustDimensions (Vol III Ch.15) represented
             in evidence — a Trust domain cannot advance to DRL 3
             if only identity-verification events exist and no
             participation/reliability events have fired
  Freshness: median_data_age_days < 60; staleness_risk =
             'current' or 'aging'
  Coverage:  ≥ 65% of active Person population with ≥ 1 signal
  Stability: pattern_variance_30d < 0.15; entity_churn_rate
             < 0.10; trend_direction = 'growing' or 'stable'
  FORBIDDEN CHECK: no Trust Twin may have been surfaced with
             recommendation_generation output at the prior DRL —
             if it was, document as governance violation before
             advancing

DRL 3 → DRL 4:
  Volume:    ≥ 10,000 Persons with trust history; ≥ 6 months
             continuous collection
  Quality:   duplicate_rate < 0.5%; missing_required_fields < 1%
  Freshness: median_data_age_days < 30; staleness_risk = 'current'
  Coverage:  ≥ 80% of active Persons with ≥ 3 trust events;
             Trust Graph density > 0.4
  Stability: pattern_variance_30d < 0.08; trend_direction =
             'stable' for ≥ 3 consecutive monthly assessments
  ADDITIONAL: Zero active fraud investigations implicating
             the trust scoring computation (Vol XIII Part V)

DRL 4 → DRL 5:
  Volume:    ≥ 50,000 Persons; ≥ 24 months continuous collection
  Quality:   ProvenanceRecord coverage ≥ 99% of all trust
             conclusions surfaced
  Freshness: staleness_risk = 'current' consistently
  Coverage:  ≥ 95% of active Person population
  Stability: ≥ 12 consecutive monthly assessments at DRL 4
             without regression; zero successful fraud attacks
             on the trust model in that period
  ADDITIONAL: CRL 'assisting' (Vol XIV Ch.4) earned by target
             entity population
```

---

### Gathering Domain

```
DRL 0 → DRL 1:
  GatheringCreated and CheckInRecorded operational and
  writing to Event Store

DRL 1 → DRL 2:
  Volume:    ≥ 100 distinct Gatherings with check-in records;
             ≥ 500 CheckInRecorded events
  Quality:   Person duplicate_rate at check-in < 3%
  Freshness: ≥ 3 Gatherings per week on average, trailing 30d
  Coverage:  ≥ 3 distinct Gathering types (Vol IV Ch.6);
             ≥ 2 distinct city districts
  Stability: ≥ 4 weeks of continuous operation

DRL 2 → DRL 3:
  Volume:    ≥ 500 distinct Gatherings; ≥ 50 distinct
             Communities hosting Gatherings
  Quality:   All gathering types in schema have ≥ 10 instances;
             check-in integrity ≥ 99%
  Freshness: ≥ 5 Gatherings per week average, consistently
  Coverage:  ≥ 6 Gathering types; ≥ 60% of in-scope Venues
             have hosted ≥ 1 Gathering
  Stability: Attendance trend stable or growing ≥ 8 weeks
```

---

### Tradition Domain

```
DRL 0 → DRL 1:
  TraditionDetected + TraditionStrengthened events
  operational; Tradition entity schema (Vol IV Ch.11) active

DRL 1 → DRL 2:
  Volume:    ≥ 3 distinct Tradition entities in SEEDED or
             ACTIVE state (Vol VII Ch.14)
  Quality:   Each Tradition has ≥ 2 constituent Memory entities
             (Vol V Ch.22) with matching tradition_id
  Freshness: ≥ 1 TraditionStrengthened event in last 90 days
  Coverage:  ≥ 2 distinct Community types holding Traditions
  Stability: No Tradition misfired from ACTIVE to LAPSED without
             TraditionRevived in the assessment window

DRL 2 → DRL 3:
  Volume:    ≥ 10 distinct Tradition entities; ≥ 5 ACTIVE;
             ≥ 3 with occurrence_count ≥ 3
  Coverage:  ≥ 4 Community families (Vol VI Ch.5) represented
  Quality:   TraditionDetected confidence_signal ≥ 0.75
```

---

### Community Evolution Domain

```
DRL 0 → DRL 1:
  CommunityCreated + CommunityMemberAdded events operational

DRL 1 → DRL 2:
  Volume:    ≥ 50 distinct Community entities;
             ≥ 500 CommunityMemberAdded events
  Quality:   founding_members recorded on ≥ 95% of Communities
             (Vol VI Ch.26 irreplaceable-if-missed discipline)
  Freshness: ≥ 5 Communities with activity in last 30 days
  Coverage:  ≥ 3 community_family types (Vol VI Ch.5)
  Stability: total_member_count trend stable or growing ≥ 6 weeks

DRL 2 → DRL 3:
  Volume:    ≥ 200 distinct Communities; ≥ 10 at lifecycle_stage
             'maturity' or 'legacy' (Vol VI Ch.22)
  Coverage:  ≥ 5 community_family types; ≥ 60% of Communities
             have community_archive_id (Vol V Ch.17) populated
  Quality:   Missing-required-fields < 2% across Community
             entities' ten-component set (Vol VI Ch.3)
```

---

### Memory Domain

```
DRL 0 → DRL 1:
  Memory entity schema (Vol V Ch.5) active WITH emotion_tags
  field operational — per Vol V Ch.28's "irreplaceable if missed"
  discipline, this field MUST be collecting from day one

DRL 1 → DRL 2:
  Volume:    ≥ 500 Memory entities with significance or title
             populated (curated, not merely raw media)
  Quality:   emotion_tags populated on ≥ 30% of Memory entities;
             if below 30%, assessment flags to improve onboarding
             prompts before advancing
  Freshness: ≥ 20 Memory entities created in last 30 days
  Coverage:  Memory entities linked to ≥ 3 Gathering types;
             ≥ 2 Memory types (Vol V Ch.8)
  Stability: Memory creation rate stable or growing

DRL 2 → DRL 3:
  Volume:    ≥ 2,000 Memory entities; ≥ 200 with tradition_id
  Quality:   emotion_tags ≥ 60% of entities;
             importance_level ≥ 40%
  Coverage:  ≥ 5 Memory types; ≥ 100 tagged pairs
             (tagged_person_ids with ≥ 2 entries)
  NOTE: Anniversary Engine (Vol V Ch.21) remains Dormant until
        this threshold AND the sensitivity infrastructure
        (Vol V Ch.21's mandatory rules) are both confirmed
        fully built and tested
```

---

### Opportunity Domain

```
DRL 0 → DRL 1:
  Opportunity entity types schema (Vol II §3.10) active;
  OpportunityEmerged event type operational (Vol VII Ch.8)

DRL 1 → DRL 2:
  Volume:    ≥ 50 formal Opportunity entities; ≥ 10
             OpportunityCompleted events
  Quality:   ProvenanceRecord (Vol VIII Ch.20) populated on
             ≥ 90% of Opportunity matching outputs
  Coverage:  ≥ 2 Opportunity entity types; ≥ 3 distinct
             Community types generating Opportunities
  Stability: Opportunity completion rate ≥ 0.6 (60% of
             confirmed Opportunities reached Completed) —
             low rate indicates matching quality issues to
             resolve before advancing
  DEPENDENCY: Trust domain MUST be at DRL 2+ before
              Opportunity can advance to DRL 2, per
              Vol VII Ch.20's trust-precedes-opportunity

DRL 2 → DRL 3:
  Volume:    ≥ 500 formal Opportunity entities;
             ≥ 100 OpportunityCompleted
  Quality:   ProvenanceRecord coverage = 100%
             (per Vol VIII Ch.22's unconditional bar,
             no exceptions at DRL 3)
  Coverage:  All 4 Opportunity Engine types (Vol XV Ch.4-7)
             with ≥ 5 completed instances each
  DEPENDENCY: Trust domain at DRL 3+; Opportunity Agent
              (Vol X Ch.7) effective DRL matches
```

---

# Part IV — The Assessment Procedure

---

## Chapter 8 — How to Perform a DataReadinessAssessment

```
DATAREADINESS ASSESSMENT PROCEDURE v1.0

INPUTS REQUIRED:
  - Domain (from Chapter 6)
  - Scope: city/market
  - Assessment date
  - Assessor identity_id (named person, not "the team")

STEP 1 — FREEZE THE ASSESSMENT WINDOW
  assessment_period = [today − 30 days, today]
  temporal_span_signal is the exception — measures total
  history since first relevant event (not the 30d window).
  Record before running any queries.

STEP 2 — VOLUME QUERIES
  Q1: SELECT COUNT(DISTINCT entity_id)
      FROM domain_events
      WHERE event_type IN (:domain_events)
        AND occurred_at >= :assessment_start
        AND city_id = :scope_city_id
  Q2: SELECT COUNT(*) [same WHERE clause]
  → volume.entity_population_size, volume.domain_event_count

STEP 3 — QUALITY QUERIES
  Read from existing DataQualityCheck records (Vol XII Ch.8).
  Do NOT re-run detection logic — read existing results.
  Q3-Q6: duplicate_rate, broken_relationship_rate,
          missing_required_fields_rate,
          clock_order_violation_rate — all via:
    SELECT COUNT(*) FILTER (WHERE status IN
      ('detected','under_review')) / NULLIF(total,0)::float
    FROM data_quality_checks
    WHERE check_type = :check_type
      AND applies_to_domain = :domain
  BLOCKING CONDITION: If any query returns NULL (quality
  checks not yet configured), STOP and file a blocking note.

STEP 4 — FRESHNESS QUERIES
  Q7: PERCENTILE_CONT(0.5) of (CURRENT_DATE - occurred_at)
      → median_data_age_days
  Q8: CURRENT_DATE - MAX(occurred_at) → last_event_recency_days
  Derive staleness_risk_level:
    'current'  — last < 30d AND median < 45d
    'aging'    — last < 60d AND median < 90d
    'stale'    — last < 90d
    'frozen'   — last ≥ 90d (CANNOT advance DRL)

STEP 5 — COVERAGE QUERIES
  Active entity population is domain-specific:
    gathering: persons with ≥ 1 check-in ever
    trust:     persons with ≥ 1 completed gathering
    memory:    persons with account > 7d AND ≥ 1 gathering
  Q9: Distinct entities with events / active entity count
  Q10: Distinct entity types with events / required count

STEP 6 — STABILITY QUERIES
  Q11: STDDEV of (daily_event_count / entity_count) over 30d
       → pattern_variance_30d
  Q12: (new_entities + departed_entities) / baseline_count
       → entity_churn_rate
  trend_direction: > 5% growth = 'growing';
                   ± 5% = 'stable'; > 5% decline = 'declining'

STEP 7 — DETERMINE CURRENT DRL
  Check Chapter 7 thresholds for the TARGET level (current + 1).
  ALL FIVE DIMENSIONS must independently clear the threshold.
  No weighting. No partial credit. No dimension may be waived.
  If ALL FIVE clear → current_drl advances by one.
  If ANY ONE fails → current_drl unchanged; record which
  dimension blocked and its actual vs. required value.

STEP 8 — SIGN AND FILE
  Complete DataReadinessAssessment record with all fields.
  If current_drl advanced: fire DataReadinessLevelChanged.
  If unchanged: file record only, no event.
  Assessor signs with identity_id.
  Record is APPEND-ONLY — per Vol VII Temporal Truth Principle.
  If wrong: file a new assessment with a correction note.
```

---

## Chapter 9 — Assessment Cadence

```
SCHEDULED SWEEP (automated, monthly):
  Runs Steps 1–8 for all registered domains in all active
  city/market scopes. Produces assessment records for human
  review. Does NOT autonomously advance DRL — human sign-off
  at Step 8 is always required.

TRIGGERED ASSESSMENT (on-demand):
  Initiated by a feature team believing their domain has
  crossed a threshold. Must complete all 8 steps with
  assessor sign-off. May not be triggered more than once
  per 14 days for the same domain-scope combination —
  prevents assessment-shopping.

REGRESSION CHECK (automated, weekly):
  Runs only Freshness and Stability (Steps 4 and 6) to
  detect staleness_risk = 'stale' or trend_direction =
  'declining' in a domain that previously advanced.
  Raises an alert for human review — NOT an automatic
  DRL demotion. Human decision required before any downgrade.
```

---

# Part V — The DRL Activation Contract (Complete Form)

---

## Chapter 10 — The Activation Contract as a Usable Document

Volume XII, Chapter 5 defined this conceptually. This chapter produces it as a literal fillable form.

```
╔══════════════════════════════════════════════════════════════╗
║     ZUKA DRL ACTIVATION CONTRACT v1.0                        ║
╚══════════════════════════════════════════════════════════════╝

CAPABILITY:       _____________________________________________
REGISTER ENTRY:   _____________________________________________
GOVERNING VOLUME: _____________________________________________
REQUEST DATE:     _____________________________________________
REQUESTING:       ________________________________ (identity_id)

FOUNDER SIGN-OFF REQUIRED? ☐ Yes  ☐ No
  Required for: DRL 4+ capability; CRL 'coordinating'+;
  financial transactions; external B2B data consumers.

SECTION A — REQUIRED ENTITIES
  Entity type     Schema?        Count     Quality gate passed?
  ___________     ☐ Yes ☐ No    _______   ☐ Yes ☐ No
  ___________     ☐ Yes ☐ No    _______   ☐ Yes ☐ No

SECTION B — REQUIRED EVENTS
  Event type           Registered?    Events in last 30d
  _________________    ☐ Yes ☐ No     __________________

SECTION C — REQUIRED SIGNALS
  Signal type          Collecting since:    Coverage:
  _________________    _______________      _________%

SECTION D — REQUIRED GRAPH RELATIONSHIPS
  Graph       Edge type       Min density required    Actual
  _______     _________       ___________________     ______

SECTION E — DRL GATE
  Domain: __________________   Scope: __________________
  Assessment ID: __________ Date: __________ Assessor: ___
  Confirmed current DRL: ______
  Required DRL for this capability: ______
  All 5 dimensions cleared? ☐ Yes  ☐ No
  Failed dimension(s): ___________________________________
  DRL gate passed?  ☐ YES → continue  ☐ NO → STOP
  If No: update the Dormant Feature Register with the
  assessment result and revisit at next monthly assessment.

SECTION F — CRL GATE
  Readiness domain: __________ (Vol XIV Part II)
  Current CRL state: __________
  Required CRL state: __________
  DRL gate passed?  ☐ Yes ☐ No
  CRL gate passed?  ☐ Yes ☐ No
  Both passed?      ☐ Yes ☐ No
  If either fails: STOP.

SECTION G — PROVENANCE CHECK
  Capability surfaces a conclusion to a user? ☐ Yes ☐ No
  If Yes:
    evidence_events populated by capability?     ☐ Yes ☐ No
    evidence_summary generated FROM events
      (never authored independently)?             ☐ Yes ☐ No
    computation_method version recorded?          ☐ Yes ☐ No
    expires_at set?                               ☐ Yes ☐ No
  If any No: STOP. Mandatory per Vol VIII Ch.20.

SECTION H — FUTURE DEPENDENCIES
  Dependent capability in Register    Precondition updated?
  ________________________________    ☐ Yes ☐ No

SECTION I — SIGN-OFF
  Requesting team member: _________________ (identity_id)
  Date: _________________

  Founder sign-off (if required):
    Kiyingi James  Date: ________________
    ☐ Approved  ☐ Approved with conditions: _______________

  On completed approval:
    ✅ Fire DomainEvent: FeatureSeedActivated (Vol VII Ch.8)
    ✅ Remove from Dormant Feature Register
    ✅ Update README and governing volume(s)

╔══════════════════════════════════════════════════════════════╗
║  A capability that cannot complete this form is not ready.  ║
║  A capability whose DRL gate fails is Dormant, not delayed. ║
╚══════════════════════════════════════════════════════════════╝
```

---

# Part VI — Dormant Feature Register Integration

---

## Chapter 11 — How This Volume Changes the Register's Graduation Discipline

The Unified Dormant Feature Register specifies graduation as: "a domain's DataReadinessAssessment has genuinely, measurably crossed its stated threshold." With this volume now written, that phrase has concrete operational meaning.

Every Register entry should carry this standard procedure:

```
GRADUATION PROCEDURE FOR THIS FEATURE:
  1. Run DataReadinessAssessment per Vol XX Ch.8
     for domain [X] in scope [city/market]
  2. Confirm ALL FIVE DIMENSIONS clear DRL [Y] thresholds
     per Vol XX Ch.7's per-domain table
  3. Complete DRL Activation Contract per Vol XX Ch.10
  4. Obtain founder sign-off if Section E/F requires it
  5. Fire FeatureSeedActivated event
  6. Remove this entry from the Register
```

---

# Part VII — MVP Scope

---

## Chapter 12 — What to Build First

```
BUILD (MVP):

  ✅ DataReadinessScore schema (Ch.5) — the five-dimension
     record, added to the database now; extends Vol VII's
     DataReadinessAssessment structure formally

  ✅ The assessment SQL templates (Ch.8, Steps 2-6) — as
     views or stored procedures, runnable against existing
     schema; at MVP results will be near-zero and DRL will
     correctly remain 0-1 for all domains

  ✅ The DRL Activation Contract form (Ch.10) — as a literal
     document template in the repo's governance folder; costs
     nothing; establishes discipline before the first real
     activation

  ✅ Automated monthly sweep scheduler (Ch.9) — configured
     now, even with near-trivially zero results; making
     assessment a routine habit before it matters prevents
     the scramble when graduation decisions actually arrive
```

```
DO NOT BUILD YET:

  ❌ Full DataReadinessAssessment service with APIs and
      dashboards — not justified by near-zero data density;
      SQL templates are sufficient

  ❌ Automated DRL advancement — human sign-off at Step 8
      is always required; this matters most at MVP when
      data is thinnest and optimistic advancement is
      most tempting
```

---

# Volume XX Summary

```
Grounding:
  Vol VII's DataReadinessAssessment confirmed as the
  foundation; this volume operationalises it — no
  competing structure introduced

Five Assessment Dimensions (new):
  Volume, Quality, Freshness, Coverage (genuinely new —
  no counterpart in Vol VII's four signals), Stability
  Coverage identified as the most frequently blocking
  dimension in practice

Domain Registry (new):
  9 domains with entity types and event type sets

Per-Domain DRL Thresholds (new):
  Trust, Gathering, Tradition, Community Evolution, Memory,
  Opportunity — all five dimensions specified per level
  Key dependency: Opportunity cannot reach DRL 2 until
  Trust is at DRL 2+ (Vol VII Ch.20)

Assessment Procedure (new — 8 steps):
  With literal SQL templates for each dimension
  Human sign-off mandatory; append-only records
  Three-cadence model: monthly sweep, triggered
  (14d floor), weekly regression check

DRL Activation Contract (new — complete form):
  Nine sections as a literal fillable form
  Covers Entities, Events, Signals, Graphs, DRL gate,
  CRL gate, Provenance, Dependencies, Sign-off

Dormant Register Integration:
  Standard 6-step graduation procedure defined
  "Crossed its stated threshold" now has operational meaning

MVP Scope:
  4 build items (schema, SQL templates, form, scheduler)
  2 explicit deferrals
```

This volume turns "data-led activation" from a principle stated seventeen times into a procedure a team can actually follow. The principle was always right. Now it is also operational.

---

# What Volume XVIII Must Address

Legal & Governance Architecture — the longest-standing gap (first flagged in Volume VI Ch.27-32, carried through thirteen volumes of deferrals). Volume XVIII must:

1. Constitutional framework for institutional-scale disputes (community fragmentation, leadership contests, political capture) that Vol XIII's Appeals mechanism never addressed
2. Cross-market data protection compliance architecture — Uganda's DPPA 2019 as baseline, with extension points per new market
3. ZUKA's arbitration role definition — preserve-and-freeze vs. affirmative governance obligations
4. Legal Review Queue governance process — how Founder Review Queue items move to decisions
5. Privacy Impact Assessment procedure — mirroring this volume's DataReadiness Assessment for data-protection-adjacent activations

---

> **ZUKA Architecture Bible**
> Volume XX — Data Readiness Assessment Operational Procedures
> Built in Kampala. Built for Africa. Built for the world.
