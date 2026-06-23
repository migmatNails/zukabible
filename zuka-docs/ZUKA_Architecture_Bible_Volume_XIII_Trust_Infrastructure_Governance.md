# ZUKA Architecture Bible
# Volume XIII — Trust Infrastructure & Governance Architecture

> **Series:** ZUKA Architecture Bible
> **Volume:** XIII of N
> **Status:** Canonical — assembles six prior volumes' scattered Trust specifications
>              into one complete picture; reconciles the fourth and final independently-
>              worded DRL ladder
> **Depends on:** The full Bible to date (Volumes I-XII)
> **Required by:** Every future volume touching reputation, fraud, dispute resolution,
>                  or the commercial Opportunity layer

---

# Preface

Trust is the invisible infrastructure of human coordination.

Six prior volumes have each specified a piece of Trust without ever assembling the complete picture: Volume III gave Trust its individual dimensions and portable certificate. Volume VI gave Trust its institutional, community-scale form. Volume VII gave Trust a timeline and an honest decay model. Volume VIII gave Trust a graph and an unconditional provenance requirement. Volume X gave Trust an active, continuously-running Agent. Volume XII gave Trust's data a quality framework and a lineage-tracing query.

This volume does not re-derive any of that work. It assembles it, reconciles the one piece of new vocabulary the source brief introduces (the Trust Pyramid), performs the fourth and final DRL ladder reconciliation this Bible owes, and then builds the three things that have never been specified anywhere: a real Anti-Fraud Architecture, a real Governance & Appeals process, and a confirmed Trust → Opportunity Pipeline.

---

# Part I — The Trust Constitution

---

## Chapter 1 — Four Principles, Adopted

```
TRUST MUST BE EARNED
TRUST MUST BE EXPLAINABLE
TRUST IS CONTEXTUAL
TRUST IS TEMPORAL
```

All four are adopted as canonical, and all four are confirmed, explicitly, as restatements of discipline already built rather than new commitments:

```
Trust Must Be Earned        → Volume III, Ch.15's entire trust
                              score computation model — never
                              self-declared, never purchased
                              (Volume VI, Ch.14's "character_tags
                              must never be gamed or purchased,
                              only earned" already states this
                              at community scale)

Trust Must Be Explainable     → Volume VIII, Ch.20's Knowledge
                              Provenance principle, applied to
                              Trust specifically — already
                              unconditional there, confirmed
                              here as Trust's own foundational
                              law, not merely inherited

Trust Is Contextual             → Volume III, Ch.15's multi-
                              dimensional model (reliability,
                              authenticity, professionalism,
                              safety, consistency, community
                              standing — six SEPARATE
                              dimensions, never collapsed into
                              one number) and Volume VI, Ch.34's
                              open question on cross-community
                              trust portability, both already
                              establishing that trust is never
                              a single global truth independent
                              of context

Trust Is Temporal                 → Volume VII, Part X in full —
                              the Trust Timeline, the decay
                              model, the "reality, not
                              punishment" framing
```

---

## Chapter 2 — Trust OS, Confirmed as a Cross-Cutting Composite View

```
COMPONENTS:
  Trust Ledger
  Trust Graph
  Trust Intelligence
  Trust Analytics
  Trust Governance
```

This is confirmed, without modification, against Volume XI, Chapter 11's already-completed Trust OS specification — "Trust Ledger" maps to the append-only Event Store filtered to Trust-type DomainEvents, "Trust Graph" maps to Volume VIII, Chapter 11's already-specified graph, "Trust Intelligence" maps to Volume X, Chapter 3's Trust Agent at DRL 3+, and "Trust Analytics" maps to Stage 2's Read Models applied to trust data. This volume adds the fifth component named here but not previously specified — **Trust Governance** — which is the primary new subject of Part V (Governance & Appeals) below. Trust OS remains, per Volume XI, Chapter 11's ruling, a composite view over the real domains (Identity, Community, Trust, Opportunity), not a standalone service with its own database — this volume does not revisit or weaken that ruling.

---

# Part II — The Trust Pyramid

---

## Chapter 3 — Resolving Whether This Is a New Layer or a Relabelling

```
1. IDENTITY TRUST
2. BEHAVIORAL TRUST
3. COMMUNITY TRUST
4. ECONOMIC TRUST
5. STEWARDSHIP TRUST
```

This is the first genuinely new structural question this volume must answer, named explicitly in Volume XII's closing section: is the Trust Pyramid a new layer, or a relabelling of dimensions Volume III, Chapter 15 already specified?

**The answer: it is neither, precisely. It is a different, valid, and useful AXIS of organisation over the same underlying evidence — ordered by ACCUMULATION SEQUENCE rather than by DIMENSION TYPE.**

```
Volume III, Chapter 15's six TrustDimensions (reliability,
authenticity, professionalism, safety, consistency, community
standing) answer: "WHAT KIND of trust signal is this?"

The Trust Pyramid answers a DIFFERENT question: "WHEN, in a
person's or entity's journey on the platform, does this LEVEL
of trust typically become available to assess at all?" It is
a SEQUENCING model, not a competing dimensional model — and
this distinction is the actual resolution, not a forced
reconciliation:

  LEVEL 1 — IDENTITY TRUST
    The earliest-available trust signal. Maps directly to
    Volume III, Chapter 15's `authenticity` dimension
    specifically — "are they who they say they are." This is
    assessable the moment phone verification and basic
    identity checks complete, before any gathering has even
    been attended. DRL 0-1 territory, in this volume's own
    ladder (Part III).

  LEVEL 2 — BEHAVIORAL TRUST
    Becomes assessable once a person has actually DONE
    something — attended a gathering, kept an RSVP. Maps to
    Volume III, Chapter 15's `reliability` and `consistency`
    dimensions. This is the Volume VII, Chapter 17 Trust
    Timeline's earliest real data points.

  LEVEL 3 — COMMUNITY TRUST
    Becomes assessable once a person has accumulated
    standing WITHIN specific communities. Maps directly,
    unmodified, to Volume III, Chapter 15's `community_
    standing` dimension AND Volume VI, Part XI's entire
    CommunityTrustProfile structure — this level of the
    Pyramid is, precisely, where Volume VI's institutional
    trust work lives.

  LEVEL 4 — ECONOMIC TRUST
    Becomes assessable once real money has moved — ticket
    purchases completed without dispute, payouts received
    reliably, an organiser's `delivery_rate` (Volume III,
    Chapter 15's `organiser_stats`). This maps to `reliability`
    and `professionalism` again, but specifically the SUBSET
    of those dimensions' evidence that is economic in nature —
    a genuinely useful filter, not previously named as its own
    level anywhere in this Bible.

  LEVEL 5 — STEWARDSHIP TRUST
    The highest, latest-available level — assessable only
    once a person has taken on RESPONSIBILITY for others'
    trust, not merely their own: a Community Archivist
    (Volume VI, Ch.8), a Family Vault steward (Volume V,
    Ch.16), a Legacy Steward (Volume III, Ch.23), a Tradition
    steward (Volume VI, Ch.25). This is, properly understood,
    a NEW naming for a real but previously-scattered concept —
    no prior volume named "stewardship" as its own trust
    category, even though every one of the examples above
    already existed as a real role carrying real responsibility.
    This is the Pyramid's single genuine, useful, NEW
    contribution.
```

**The final ruling:** the Trust Pyramid is adopted as a valid secondary organising axis — useful specifically for onboarding sequencing and for explaining to a person "what level of trust you're building toward next" — layered over, not replacing, Volume III, Chapter 15's six-dimension model, which remains the actual computational basis for any trust score. "Stewardship Trust" as a named concept is the one piece of this Pyramid that is genuinely new and is adopted as a formal addition to Volume III's trust vocabulary, retroactively naming a pattern (steward-type roles carrying elevated trust) that was already present but unnamed across Volumes III, V, and VI.

---

# Part III — DRL Reconciliation, The Fourth and Final Ladder

---

## Chapter 4 — Completing What Volume XII Started

```
DRL Trust Activation Matrix:
  DRL0: Schemas
  DRL1: Signal Collection
  DRL2: Pattern Detection
  DRL3: Explainable Trust Scoring
  DRL4: Trust-Assisted Automation
  DRL5: Trust Intelligence
```

This is the fourth independently-worded version of the same six-level ladder this Bible has now produced, after Volume VII (data maturity), Volume XI (infrastructure capability), and Volume XII (data use permission). Per the pattern established in Volume XII, Chapter 4, this is reconciled here, completing the full table:

```
DRL  Volume VII        Volume XI            Volume XII           Volume XIII (this volume)
0    No data            Schemas only          Schemas only           Schemas
1    Collection started  Transactional         Collection; FORBIDS    Signal Collection
                          systems               predictions/trust score
2    Patterns visible     Analytics systems      Pattern detection       Pattern Detection
3    Useful insights       Recommendations         Confidence achieved;     Explainable Trust
     possible                                       matching/recs           Scoring
4    Automation possible    Automation               Automation               Trust-Assisted
                                                                                Automation
5    AI-grade intelligence   AI Twins, Copilots,       Intelligence (Twins,     Trust Intelligence
                              Intelligence Systems       Historians, Copilots)

This is the FOURTH and, on present evidence, FINAL vantage
point on the same single gate — and it is adopted as
CONFIRMATION that the reconciliation pattern established in
Volume XI, Ch.7 and Volume XII, Ch.4 is robust: a fourth
independently-worded ladder, written by yet another pass over
this Bible's evolving thinking, STILL lands on the exact same
six levels with the exact same practical meaning at each level,
merely renamed for Trust's specific vocabulary ("Signal
Collection" instead of "Collection started," "Explainable
Trust Scoring" instead of "Recommendations"). This consistency
across four independent wordings is itself evidence that the
underlying six-level gate is correctly specified, not an
artifact requiring further correction.

NO NEW FORBIDDEN LIST IS ADDED HERE — Volume XII, Chapter 4's
genuine addition (explicit forbidden lists per level) is
NOTED as not yet present in this volume's source wording, and
is retrofitted here for DRL 1 specifically, following Volume
XII's adopted pattern:

  DRL 1 (Trust): Signal Collection.
  Allowed: collecting reliability/authenticity/consistency
  signals into the Trust Graph (Volume VIII, Ch.11).
  FORBIDDEN: surfacing any trust score, badge, or "Trusted X"
  label to any user — collection without scoring, exactly
  mirroring Volume XII's own DRL 1 forbidden list for the
  general data case, now stated explicitly for Trust.
```

---

# Part IV — Trust Ledger and Trust Graph, Confirmed

---

## Chapter 5 — Trust Ledger

```
IMMUTABLE TRUST-CHANGING EVENTS:
  TrustGranted
  TrustLost
  BookingCompleted
  DisputeResolved
  CommunityEndorsed
```

Confirmed against Volume II, Chapter 9's existing Trust Events registry (`VerificationSubmitted`, `VerificationApproved`, `BadgeAwarded`, `ReputationScoreUpdated`, `ReviewSubmitted`, `DisputeFiled`, `DisputeResolved`) and Volume VII, Chapter 8's `TrustScoreRecomputed` addition. Two names in this volume's source list are new and are formally added to the registry here: `TrustGranted` and `TrustLost` (more general than the existing `BadgeAwarded`/`BadgeRevoked` pair — these cover any trust-affecting event, not only formal badge changes) and `CommunityEndorsed` (a new, specifically community-scale endorsement event, distinct from an individual `ReviewSubmitted`, mapping to Volume VI, Chapter 13's `CommunityTrustProfile` and filling a genuine gap — no prior volume specified the event that actually feeds a community's trust profile upward). `BookingCompleted` is confirmed as already covered by Volume II's existing `BookingConfirmed`/booking-completion event family.

---

## Chapter 6 — Trust Graph

```
NODES:    People, Communities, Organizations, Opportunities
EDGES:    TRUSTS, ENDORSED, VERIFIED, WORKED_WITH
```

Confirmed without modification against Volume VIII, Chapter 11's already-complete `TrustEdge` specification. `TRUSTS` maps to the existing edge structure's core relationship; `ENDORSED` is the edge-form expression of Chapter 5's newly-registered `CommunityEndorsed` event; `VERIFIED` maps to Volume III, Chapter 6's Verification entity relationship; `WORKED_WITH` maps to Volume II, Chapter 5's existing `BUSINESS_PARTNER` relationship type, now confirmed as also Trust-Graph-relevant specifically when it carries trust-dimension weight. The addition of `Organizations` and `Opportunities` as Trust Graph node types alongside the already-specified `People` and `Communities` is adopted as a useful, valid extension — Volume VIII, Chapter 11 was written with People and Communities as its primary examples but never excluded Organisation or Opportunity entities from carrying trust edges, and this volume confirms they do.

---

# Part V — Anti-Fraud Architecture

---

## Chapter 7 — The First Full Specification

Fraud prevention has been referenced in passing since ZUKA.md's original §26 and again in Volume III, Chapter 30 (Reputation Abuse) and Volume VI, Chapter 31 (Trust Abuse, community-scale) — but no volume has ever specified the actual detection mechanics. This volume does so, organised around the four categories named in its source brief.

```
SYBIL DETECTION
  The threat: one real person operating multiple Identity
  entities (Volume III, Ch.6) to manufacture artificial
  social proof, inflate a Community's apparent membership
  (Volume VI, Ch.6's total_member_count), or farm Pulse
  Points (ZUKA.md §13's referral rewards, already noting
  "self-referral detection: same device, same network").

  Detection signals:
    Device fingerprint clustering — multiple Identity
    records sharing a device_id (Volume IX, Ch.2's Twin
    schema already establishes device_id as a tracked field
    at the Scanner level; this volume extends the same
    signal to general Identity creation)
    Phone number pattern analysis — sequential or batch-
    registered numbers from the same telecom block in a
    short window
    Behavioural graph anomaly — per Volume XII, Ch.8's
    duplicate_detection_strategy, now applied specifically
    to the FRAUD case rather than the innocent-duplicate
    case: an embedding_similarity cluster combined with
    near-identical gathering attendance PATTERNS (not just
    similar profiles) is a stronger sybil signal than
    profile similarity alone
    Relationship graph topology — a cluster of "FRIEND"
    relationships (Volume II, Ch.5) that only ever interact
    with each other and no other established identity in
    the graph is structurally suspicious, distinct from a
    legitimate small real-world friend group, which
    typically has SOME edges reaching into the wider graph
    (shared communities, shared gatherings with strangers)

  Response: per Volume XII, Ch.9's restraint principle,
  flagged for review, never auto-merged or auto-suspended —
  EXCEPT where Sybil signals combine with active financial
  harm (see Chapter 9's escalation tiers below)

TRUST FARMING DETECTION
  The threat: gaming the Trust Pyramid (Part II) or
  individual TrustDimensions (Volume III, Ch.15) through
  volume rather than genuine reliability — e.g., attending
  many low-stakes, low-cost gatherings specifically to
  inflate `reliability` without ever facing a real test of
  trustworthiness.

  Detection signals:
    Velocity anomaly — trust-relevant events (check-ins,
    RSVPs kept) occurring at a rate exceeding what a single
    real person's calendar could plausibly sustain (per
    ZUKA.md §26's existing "velocity checks: >5 ticket
    purchases in 10 minutes" pattern, generalised here to
    trust-building actions specifically, not just purchases)
    Low-stakes concentration — a trust profile built
    overwhelmingly from free, zero-commitment, easily-
    repeatable actions (RSVP-and-attend a free event) with
    near-zero representation from Volume III, Ch.15's
    `professionalism` or Part II's Level 4 (Economic Trust)
    evidence — not disqualifying on its own, but a signal
    that downweights the CONFIDENCE of the resulting score
    even where the raw count looks high
    Reciprocal pattern detection — two or more identities
    that disproportionately attend, endorse, or review ONLY
    each other's gatherings/communities, in a pattern that
    looks like mutual inflation rather than organic overlap
    (the collusion case, addressed more fully below)

  Response: confidence DOWNWEIGHTING (per Volume VIII,
  Ch.19's confidence model — a farmed-looking trust score is
  not deleted, it is computed with a LOWER confidence value,
  which itself flows through every downstream Twin/Agent
  consumer per Volume X, Ch.13's effective-DRL minimum rule)
  rather than binary flagging, in most cases — this is a
  more honest response than a simple flag/no-flag binary,
  consistent with this Bible's general preference for
  graduated, explainable responses over blunt ones

COLLUSION DETECTION
  The threat: coordinated manipulation, most acutely
  relevant to the Opportunity Agent's matching function
  (Volume X, Ch.7) — two parties artificially manufacturing
  an OpportunityEmerged signal (Volume VII, Ch.20) through
  staged interactions, in order to extract a favourable
  match, sponsorship, or booking recommendation.

  Detection signals:
    Builds directly on Trust Farming's reciprocal pattern
    detection above, specifically scoped to PAIRS or small
    GROUPS whose interaction pattern concentrates suspiciously
    around Opportunity-Graph-relevant events (Volume VIII,
    Ch.12) rather than general social activity
    Timing correlation — coordinated account creation,
    coordinated first-interaction timing, or coordinated
    endorsement timing across multiple identities, beyond
    what organic real-world coordination (e.g., a genuine
    friend group joining together) would typically produce

  Response: per Chapter 7's Opportunity Agent inheritance
  of Volume VIII, Ch.22's unconditional stricter provenance
  bar — any ProvenanceRecord (Volume VIII, Ch.20) for an
  Opportunity match flagged as a collusion candidate is
  AUTOMATICALLY held below the recommendation_generation
  output threshold (Volume IX, Ch.12's DRL ladder) regardless
  of its otherwise-computed DRL, until human review clears it —
  this is the one case in this volume where a fraud signal
  directly overrides an otherwise-met DRL threshold, justified
  by the same commercial-and-relational risk Volume VIII,
  Ch.22 already named as warranting a stricter bar

FAKE REVIEW DETECTION
  The threat: coordinated or fabricated reviews, already
  partially addressed in ZUKA.md §28's review system
  (verified-attendance-only eligibility) and Volume VI,
  Ch.14's CommunityReviewSummary, but never given dedicated
  detection mechanics.

  Detection signals:
    Per ZUKA.md §28's existing rules, attendance verification
    (Smart Bucket check-in, Volume on Ticketing) is already
    the FIRST line of defence — a review cannot be submitted
    without a verified check-in record. This volume's addition
    is the SECOND line: pattern analysis ACROSS reviews from
    accounts that passed attendance verification but show the
    same coordinated-network signals as Sybil/Collusion
    detection above — i.e., real check-ins, by real but
    Sybil-linked or colluding accounts, is a more sophisticated
    attack than fake check-ins, and requires the SAME network-
    topology analysis, not a separate detection system
    Sentiment/timing clustering — multiple reviews submitted
    within a tight time window, from accounts with otherwise
    minimal interaction history, all carrying unusually similar
    phrasing or sentiment — a content-pattern signal layered
    on top of the network-pattern signal above

  Response: per ZUKA.md §28's existing rules, held for review
  before publication when flagged — no change to that existing
  process, only the addition of these specific detection
  signals feeding into it
```

---

## Chapter 8 — The Shared Detection Substrate

This is worth naming explicitly because it is the single most efficient finding in this Part: **all four fraud categories above share the same three underlying detection primitives** — device/identity fingerprint clustering, relationship graph topology analysis, and temporal/velocity anomaly detection. This is not a coincidence requiring four separately-built systems; it is one shared anti-fraud detection substrate, queried with four different framings depending on which threat is being assessed.

```typescript
FraudSignal {
  signal_id:              UUID
  signal_type:                FraudSignalType
  // 'device_clustering' | 'graph_topology_anomaly' |
  // 'velocity_anomaly' | 'content_pattern_match'
  // — note: only FOUR signal types, not four separate
  // systems per fraud category, confirming Chapter 8's
  // shared-substrate finding structurally

  implicated_entity_ids:        UUID[]
  fraud_category_relevance:        FraudCategory[]
  // A single signal frequently implicates MULTIPLE
  // categories simultaneously — e.g., a device-clustering
  // signal is relevant to BOTH Sybil Detection AND, where
  // the clustered accounts also show reciprocal endorsement
  // patterns, Collusion Detection

  confidence:                       Float(0.0-1.0)
  evidence_events:                     DomainEventReference[]
  // Per Volume VIII, Ch.20's ProvenanceRecord pattern,
  // applied here — a fraud signal is itself a conclusion
  // requiring provenance, not exempt from this Bible's
  // explainability principle merely because it concerns
  // misconduct rather than a positive trust score

  status:                              'detected' | 'under_review' |
                                       'confirmed' | 'dismissed'
  // 'dismissed' is a fully valid, expected outcome — this
  // architecture is built to surface SUSPICION for human
  // judgement, not to assert GUILT algorithmically, fully
  // consistent with Chapter 9's response patterns above
}
```

---

# Part VI — Governance & Appeals

---

## Chapter 9 — The First Formal Appeals Mechanism in This Bible

```
USERS CAN:
  View explanations
  Appeal outcomes
  Correct inaccuracies
```

This is the first place in the Bible a formal appeals mechanism is specified for ANY automated or semi-automated conclusion — not only trust scores, but, by direct extension of this chapter's structure, every conclusion type this Bible has built a `ProvenanceRecord` for since Volume VIII: Twin outputs (Volume IX), Agent outputs (Volume X), and flagged data quality issues (Volume XII, Chapter 8).

```typescript
AppealRecord {
  appeal_id:                 UUID
  appellant_identity_id:         UUID
  contested_conclusion_id:           UUID
  // References ANY ProvenanceRecord-backed conclusion
  // (Volume VIII, Ch.20's ConclusionType enum, as extended
  // by Volume IX, Ch.15 and Volume X, Ch.11) — this appeals
  // mechanism is deliberately built ONCE, generically, against
  // the ConclusionType abstraction, rather than separately
  // for trust scores, Twin outputs, Agent outputs, and data
  // quality flags as four different systems

  // ── STEP 1: VIEW EXPLANATION (always available) ──────────
  explanation_provided: {
    full_provenance_chain:       LineageTraceResult
    // Per Volume XII, Ch.10's recursive lineage query — the
    // appellant is shown the COMPLETE chain, not a summary,
    // satisfying both Volume VIII's Explainable Knowledge
    // principle (the user-facing obligation) and Knowledge
    // Provenance principle (the re-traceability obligation)
    // simultaneously, at the moment it matters most: when
    // the person affected is asking "why"
  }

  // ── STEP 2: APPEAL (initiated by the appellant) ──────────
  appeal_grounds:                   AppealGroundsType
  // 'factual_error' — "the underlying DomainEvent is wrong"
  // 'missing_context' — "the conclusion is technically
  //    correct but omits relevant context" (e.g., a
  //    reliability dimension penalised for a no-show that
  //    was actually a documented event cancellation by the
  //    ORGANISER, not the attendee's fault)
  // 'methodology_dispute' — "the computation_method
  //    (Volume VIII, Ch.20) itself produced an unfair result
  //    even though every input event is accurate"
  // 'fraud_signal_false_positive' — specifically for
  //    Part V's fraud detection outputs (Chapter 7-8)

  appeal_submitted_at:                 Timestamptz
  appeal_evidence:                        TEXT
  appeal_supporting_references:               DomainEventReference[]
  // The appellant may cite ADDITIONAL events not already
  // in the original evidence_events chain — e.g., proof of
  // the organiser-side cancellation referenced above

  // ── STEP 3: REVIEW ────────────────────────────────────────
  review_assigned_to:                      UUID
  // A human reviewer — per Chapter 9's restraint principle
  // pattern (Volume XII, Ch.9) and this Bible's consistent
  // human-in-the-loop preference, NO appeal is ever resolved
  // by the same automated system that produced the original
  // conclusion; review is always performed by a person with
  // explicit reviewer authority, distinct from the system
  review_outcome:                              AppealOutcome
  // 'upheld_no_change' | 'upheld_with_correction' |
  // 'overturned'
  review_rationale:                               TEXT
  // Itself a new, human-authored explanation, NOT a re-run
  // of the automated conclusion's evidence_summary — a
  // genuinely independent judgement, recorded as such

  // ── STEP 4: CORRECTION (where warranted) ─────────────────
  correction_applied: {
    method:                            CorrectionMethod
    // 'new_domain_event' — per Volume VII's Temporal Truth
    //    Principle, a correction is NEVER applied by editing
    //    the original (now-disputed) DomainEvent or
    //    ProvenanceRecord in place. It is applied by writing
    //    a NEW, append-only event (e.g.,
    //    'AppealUpheldCorrection') that supersedes the
    //    prior conclusion in all FUTURE computations while
    //    leaving the historical record of what was originally
    //    concluded, and why, fully intact and inspectable —
    //    this is the single most important architectural
    //    choice in this entire chapter, and it is a direct,
    //    non-negotiable consequence of Volume VII's already-
    //    canonical principle, not a new invention
    new_event_id:                          UUID
    affected_downstream_recomputation:          UUID[]
    // Per Volume X, Ch.12's asynchronous write-then-read
    // discipline, any Twin or Agent that previously consumed
    // the now-corrected conclusion will, on its NEXT
    // synthesis cycle (Volume IX, Ch.14), naturally pick up
    // the correction without requiring any special "cascade
    // correction" mechanism — this field exists purely for
    // audit visibility into which downstream consumers WILL
    // be affected, not as a trigger mechanism in itself
  }

  closed_at:                                  Timestamptz
}
```

```
THE GOVERNING PRINCIPLE THIS CHAPTER ESTABLISHES, STATED
EXPLICITLY: an appeal never rewrites history. It always adds
to it. This is Volume VII's Temporal Truth Principle, tested
for the first time against the hardest possible case — a
person who believes the system got something about THEM
wrong — and it holds without exception, exactly as it held
across every other test this Bible has put it through since
Volume VII first stated it.
```

---

# Part VII — The Trust → Opportunity Pipeline, Confirmed

---

## Chapter 10 — One Pipeline, Already Built

```
PARTICIPATION → TRUST → REPUTATION → VISIBILITY →
OPPORTUNITIES → VALUE CREATION
```

This is confirmed, directly and without modification, against the mechanics already fully specified in Volume VII, Chapter 20 (Opportunity Emergence) and Volume X, Chapter 7 (Opportunity Agent, Mode 1 specifically). No new architecture is required — this chapter's sole job is mapping this volume's six-stage naming onto the already-built mechanism, so a reader encountering this pipeline stated in this form recognises it as the same thing rather than a competing model.

```
Participation       → Volume IV's Participation records,
                       Volume VI Ch.8's gathering_
                       participation_count

  ↓

Trust                  → Volume III Ch.15's TrustDimensions,
                       now organised additionally per Part
                       II's Trust Pyramid sequencing

  ↓

Reputation                → Volume III Ch.15's separate
                       reputation_profile (distinct from
                       trust_profile, per Volume III Ch.14's
                       original "Trust measures earned
                       reliability; Reputation captures how
                       an entity is PERCEIVED" distinction —
                       still holding, unmodified, here)

  ↓

Visibility                   → Volume VIII Ch.21-22's
                       Community/Opportunity Intelligence
                       Layers' matching and surfacing logic —
                       a high-trust, high-reputation entity
                       is more LIKELY to be surfaced by the
                       Opportunity Agent's Mode 2 matching
                       (Volume X, Ch.7)

  ↓

Opportunities                    → Volume VII Ch.20's
                       OpportunityEmerged signal, generated
                       by Volume X Ch.7's Opportunity Agent
                       Mode 1 — the literal mechanism that
                       converts accumulated trust into a
                       detected, candidate opportunity

  ↓

Value Creation                       → Volume II §3.10's
                       formal Opportunity entities (Job,
                       Sponsorship, Booking, Collaboration) —
                       the point at which an emerged pattern
                       becomes a real, economically meaningful
                       outcome
```

The only genuinely new contribution this chapter makes is naming "Reputation → Visibility" as an explicit, intermediate step — prior volumes moved directly from Trust/Reputation to Opportunity matching without naming Visibility as its own distinct stage. This is adopted as a useful clarification: Visibility is the SURFACING mechanism (ranking, recommendation eligibility) that sits between having earned trust/reputation and actually being matched to an opportunity, and naming it separately makes clear that a high-trust entity that is never surfaced (e.g., due to narrow privacy settings, Volume III Part VI) will not automatically receive opportunities — trust alone is necessary but not sufficient; visibility is the connecting mechanism, and it remains fully subject to the entity's own privacy and consent settings throughout.

---

# Part VIII — The Founder Rule

---

## Chapter 11 — Confirming Final Inheritance

```
NO TRUST DECISION MAY RELY SOLELY ON RATINGS, LIKES, OR
FOLLOWERS.
```

This single-sentence Founder Rule is adopted as a binding, specific instance of the general principle already established across Volume III, Chapter 15 (six dimensions, never collapsed to a vanity metric) and Volume VI, Chapter 14 (reputation tags "never gamed or purchased, only earned"). It requires no new specification — it is confirmed here as the sharpest, most quotable possible statement of a rule this Bible has already enforced structurally since Volume III's original multi-dimensional trust model made a single popularity number structurally impossible to use as the sole basis for any trust conclusion in the first place. Its value is rhetorical and practical for engineering review, not architectural — exactly the same role Volume XI and Volume XII's Founder Rules play, now extended with Trust's own sharpest one-line test.

---

# Part IX — MVP Scope

---

## Chapter 12 — What to Build First

```
BUILD (MVP):

  ✅ The shared FraudSignal detection substrate (Ch.8) — the
     FOUR signal types (device_clustering, graph_topology_
     anomaly, velocity_anomaly, content_pattern_match),
     built ONCE as a shared system from day one, specifically
     because Chapter 8's finding (one substrate, four framings)
     means building it once now is strictly cheaper than
     building four separate fraud systems later under
     pressure once each threat category becomes individually
     urgent
  ✅ The AppealRecord schema and the 'new_domain_event'
     correction method (Ch.9) — built and tested against
     zero real appeals at MVP, mirroring the now-familiar
     "prove the infrastructure before it is load-bearing"
     discipline this Bible has applied consistently since
     Volume IX — specifically because Volume VII's Temporal
     Truth Principle makes the CORRECTION mechanism (never
     edit, always append a superseding event) one more
     structural choice that is cheap now and would be a
     serious, trust-undermining retrofit later if a team
     under pressure to resolve a real, live dispute reached
     for "just edit the record" instead
  ✅ Velocity anomaly detection specifically (the narrowest,
     cheapest-to-implement of the four FraudSignal types,
     and already partially specified via ZUKA.md §26's
     existing rate-limit rules) — promoted ahead of the other
     three signal types for immediate MVP implementation,
     since it requires no graph density or historical depth
     to be useful, unlike graph_topology_anomaly and
     content_pattern_match, which need real data volume
     (per this Bible's DRL discipline) to produce honest
     signal at all
```

```
DO NOT BUILD YET:

  ❌ graph_topology_anomaly and content_pattern_match
      detection logic — per Volume VII's DRL discipline,
      these require genuine graph density and review volume
      respectively to produce non-noisy signal; the
      FraudSignal SCHEMA should exist (per the BUILD section
      above) but the DETECTION ALGORITHMS for these two
      specific signal types are correctly deferred
  ❌ Any human reviewer WORKFLOW TOOLING beyond the bare
      AppealRecord schema — at MVP scale, appeals (if any
      occur at all) can be resolved through direct founder/
      support-team review without dedicated tooling; building
      a full reviewer dashboard ahead of any real appeal
      volume would be premature investment exactly analogous
      to every other "do not build yet" item across this
      Bible's prior volumes
  ❌ Collusion-specific Opportunity Agent overrides (Ch.7's
      automatic provenance-threshold suppression) — this
      logic depends on the Opportunity Agent itself reaching
      a meaningful DRL (per Volume X, Ch.14's honest current-
      state finding that the Opportunity Agent sits at
      effective DRL 0 at this writing) — there is nothing for
      this override to meaningfully protect yet
```

**The single most important MVP discipline in this volume:** build the correction mechanism (append, never edit) before the first real appeal ever arrives, for the same reason this Bible has insisted on building every other piece of irreplaceable infrastructure ahead of load since Volume IX — the moment a real, emotionally significant dispute is in front of a support team under time pressure is the worst possible moment to be improvising whether the fix should be an edit or an event.

---

# Volume XIII Summary

```
The Trust Constitution:
  4 principles adopted, each confirmed as already-built
  discipline (Volume III's earned-trust model, Volume VIII's
  unconditional provenance requirement, Volume III's
  multi-dimensional contextuality, Volume VII's temporal
  decay model) — no new commitments, formal naming only

Trust OS:
  Confirmed against Volume XI, Ch.11's existing composite-view
  ruling, unmodified — 5 components, 4 already mapped, the
  5th (Trust Governance) the new subject of this volume's
  Part VI

The Trust Pyramid:
  Resolved as a valid SEQUENCING axis (when trust becomes
  assessable) layered over, not replacing, Volume III's
  6-dimension TYPE axis (what kind of trust signal) — one
  genuinely new contribution: "Stewardship Trust" as a formal
  name for an already-present-but-unnamed pattern across
  Volumes III, V, and VI

DRL, Fourth and Final Reconciliation:
  Completes the 4-ladder table started in Volume XII —
  Volume VII (data) / XI (infrastructure) / XII (data use) /
  XIII (trust-specific) — all four landing on the identical
  6-level gate, confirming the reconciliation pattern's
  robustness across independent authorship

Trust Ledger & Trust Graph:
  Confirmed against Volume II/VIII's existing registries, with
  2 new events formally added (TrustGranted, TrustLost,
  CommunityEndorsed) and 2 new graph node types confirmed
  (Organizations, Opportunities)

Anti-Fraud Architecture (NEW):
  First full specification — 4 named threat categories (Sybil,
  Trust Farming, Collusion, Fake Review), all confirmed to
  share ONE underlying 4-signal detection substrate rather
  than requiring 4 separate systems
  Graduated responses (flag, confidence-downweight, provenance-
  threshold-override, hold-for-review) replacing any binary
  ban/no-ban model

Governance & Appeals (NEW):
  First formal appeals mechanism in the Bible, built generically
  against the ConclusionType abstraction (not separately for
  trust/Twin/Agent/data-quality conclusions)
  4-step process: View Explanation → Appeal → Review → Correction
  Correction mechanism is append-only by structural requirement,
  per Volume VII's Temporal Truth Principle tested for the
  first time against its hardest case

Trust → Opportunity Pipeline:
  Confirmed against Volume VII Ch.20 and Volume X Ch.7's
  already-built mechanics — one genuinely new contribution:
  naming "Visibility" as its own explicit intermediate stage
  between Reputation and Opportunities

Founder Rule:
  1 sharp, quotable, binding sentence, confirmed as an already-
  structurally-enforced rule given Volume III's multi-
  dimensional model

MVP Scope:
  3 build items (shared fraud substrate, appeals schema +
  append-only correction mechanism, velocity detection
  specifically promoted ahead of the other 3 signal types)
  3 explicit deferrals, each tied to genuine DRL/volume
  preconditions not yet met
```

This volume closes the loop on six prior volumes' scattered Trust work and, not incidentally, closes the loop on this Bible's own four-times-independently-rediscovered DRL ladder — the same six levels, found honestly four separate times by four different passes of thinking, is itself a form of provenance for the framework's own correctness.

---

# What Volume XIV Must Address

With Volume XIII complete, this Bible has now fully specified: the domain model (II), identity (III), gatherings (IV), memory (V), communities (VI), time (VII), knowledge (VIII), Twins (IX), Agents (X), infrastructure (XI), data governance (XII), and trust (XIII). Twelve volumes of architecture, with a small number of explicitly-named open items still genuinely outstanding:

1. **The platform-identity reframe** ("Operating System for Human Coordination" vs. Volume I's original "Human Gatherings") — first raised Volume VII, carried through Volumes VIII, X, and now reconfirmed still-open here — remains the single oldest unresolved item in this Bible and should likely be the next item addressed, simply because it has now been correctly deferred across five consecutive volumes without resolution
2. **Knowledge Federation** (Volume VIII, Chapter 24) — still held for founder and legal review, unchanged
3. **The full Infrastructure & Ecosystem / Docking volume** — Volume XI, Chapter 10 confirmed only the minimal structural shape of a Connector Contract; the complete Connector Framework and Capability taxonomy remains genuinely owed
4. **A unified, cross-volume Dormant Feature Register dashboard** — Volume X, Chapter 16's Item 1, still outstanding after Volumes XI through XIII each added their own scattered DRL accounting without consolidating the existing three (now effectively four, counting this volume's fraud-specific additions) registers into one
5. **Legal & Governance Architecture proper** — referenced as a deferral target across Volumes III, IV, V, and VI for disputes this volume's Part VI now partially addresses at the individual-conclusion level, but never specified as its own volume for the harder institutional cases (Volume VI Ch.27-32's community fragmentation, leadership disputes, and political capture, none of which this volume's appeals mechanism — built for contesting a single conclusion — is actually built to resolve)

---

> **ZUKA Architecture Bible**
> Volume XIII — Trust Infrastructure & Governance Architecture
> Built in Kampala. Built for Africa. Built for the world.
