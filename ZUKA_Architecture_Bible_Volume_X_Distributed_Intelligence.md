# ZUKA Architecture Bible
# Volume X — Distributed Intelligence Architecture

> **Series:** ZUKA Architecture Bible
> **Volume:** X of N
> **Status:** Canonical — fully DRL-gated, zero Agents active at this writing
> **Depends on:** Volumes I-VIII (full Bible to date), Volume IX (AI Twin Architecture)
> **Required by:** Every future intelligence-layer volume

---

# Preface

Volume IX closed by naming five questions Volume X would have to answer before an Agent could be considered canonical architecture. This volume answers all five, in order.

The source brief for this volume, like Volume IX's, is deliberately minimal — five named Agents and one governing sentence, the same sentence Volume IX adopted as its own: **no agent may operate above the maturity of the supporting data.** That shared sentence is the first and most important finding of this volume, addressed directly in Part I, because the relationship between Volume IX's Twins and this volume's Agents has to be settled correctly or every other chapter here is built on a confused foundation.

---

# Part I — Agent vs. Twin

---

## Chapter 1 — Resolving Volume IX's First Question

A Twin (Volume IX, Chapter 1) is a standing representation **of a single entity** — one Person, one Family, one Community, one Gathering, one Opportunity, one City. An Agent is something categorically different, and the difference is not scale, it is **directionality**.

```
A TWIN:
  Represents.
  Is queried about the entity it stands in for.
  Passive between synthesis cycles.
  Bound to exactly one represented_entity_id (Volume IX, Ch.2).

AN AGENT:
  Acts.
  Performs a specific FUNCTION across or between entities,
  drawing on one or more Twins, one or more graphs
  (Volume VIII, Ch.7), or both, in service of a named purpose.
  Not bound to a single entity — an Agent's scope is its
  FUNCTION, not a single represented thing.
```

There is no "Person Agent" in this volume's registry, because a Person is represented by a Twin (Volume IX, Chapter 6), not acted upon by an Agent. What exists instead are five Agents, each performing one kind of work that draws on potentially many Twins and graphs at once:

```
Trust Agent       → PERFORMS trust assessment across the Trust
                     Graph (Volume VIII, Ch.11), across MANY
                     entities, not standing FOR any single one
Opportunity Agent  → PERFORMS matching and emergence detection
                     across the Opportunity and Trust graphs
Community Agent     → PERFORMS pattern detection across the
                     Community Graph, potentially across
                     MULTIPLE communities at once
Historian Agent      → PERFORMS narration, orchestrating across
                     the seven already-named Historians
                     (Volume VII, Ch.25) — see Chapter 4
Tradition Agent        → PERFORMS lifecycle tracking across the
                     Tradition Graph, across MANY traditions
```

**The structural rule that follows directly:** a Twin's scope is a noun (a Person, a Family). An Agent's scope is a verb (assessing, matching, detecting, narrating, tracking). This is why the five Agents do not map one-to-one onto Volume IX's six Twin types.

---

## Chapter 2 — Why This Distinction Matters Architecturally

```
A Twin's access is BOUNDED by consent (Volume IX, Part II) —
scoped tightly to the single entity it represents.

An Agent's access is BOUNDED differently — by the FUNCTION
it performs, which inherently requires drawing across many
entities' data simultaneously.

This means an Agent's consent and authorisation model CANNOT
simply inherit Volume IX's single-entity TwinConsentRecord
structure unmodified — it needs its own model, specified in
Part III.
```

This is why Volume X could not have been written as a mere appendix to Volume IX, despite sharing a governing principle — the two systems differ enough in kind to require independent specification, while remaining tightly coupled in practice (Part IV).

---

# Part II — The Five Agents

---

## Chapter 3 — Trust Agent

```
FUNCTION:        Continuous, cross-entity assessment and
                  recomputation of trust signals across the
                  Trust Graph (Volume VIII, Ch.11)

DISTINCT FROM:    An individual entity's own TrustTimelinePoint
                  history (Volume VII, Ch.17), which is a
                  PASSIVE record — the Trust Agent is the
                  ACTIVE process that PRODUCES those points by
                  continuously comparing trust signals across
                  the graph, detecting asymmetries, decay
                  patterns (Volume VII, Ch.18), and propagation
                  effects (e.g., does a Community's rising
                  CommunityTrustProfile shift the baseline
                  trust new members inherit, per Volume VI,
                  Ch.21's trust_inheritance rule)

INPUT GRAPHS:     Trust Graph (primary), Identity Graph,
                  Community Graph (for federation-level
                  trust_inheritance computation)

OUTPUT:           TrustEdge updates (Volume VIII, Ch.11) and
                  TrustTimelinePoint entries (Volume VII,
                  Ch.17) — the Trust Agent does NOT directly
                  message a user; its output feeds Twins and
                  other Agents (Chapter 7's handoff)

GOVERNING
CONSTRAINT:       Volume VII, Ch.18's "trust decay is reality,
                  not punishment" framing applies with even
                  more force to an ACTIVE process continuously
                  recomputing it
```

---

## Chapter 4 — Historian Agent

This is Volume IX's second required question, and it deserves the most careful resolution.

```
THE QUESTION:  Is the Historian Agent a unifying orchestration
                layer over the seven existing Historians
                (Memory, Family, Community, Relationship,
                Legacy, Personal, City), or a structurally
                distinct eighth capability?

THE ANSWER:    Orchestration layer. Not an eighth Historian.

WHY:           Each of the seven existing Historians is
                already closer to a QUERY INTERFACE than a
                standing representation. Creating an eighth
                alongside them would not add a capability; it
                would duplicate what exists.

                What does NOT yet exist: the FUNCTION of
                routing a single, possibly ambiguous question
                to the RIGHT Historian (or combination),
                synthesising their combined answer where a
                question spans more than one Historian's
                scope, and applying ONE consistent provenance
                and tone standard across all seven.

EXAMPLE:        "Tell me about my grandmother's role in our
                family's Christmas tradition" spans the Family
                Historian (her place in the Family Vault) and
                the Legacy Historian (if she is Memorialised).
                No "Tradition Historian" was ever named — this
                gap is resolved in Chapter 5.

CONSENT MODEL:  Inherits, additively, the consent already
                governing EACH Historian it queries. No
                authority of its own beyond what the
                underlying Historians already require.
```

---

## Chapter 5 — Resolving the Tradition Historian Gap

Volume VII, Chapter 25 named seven Historians; this volume's source brief separately names a **Tradition Agent** — but no "Tradition Historian" was ever specified, despite the Tradition Graph (Volume VIII, Ch.13) being one of the seven official graphs.

```
RESOLUTION:    The Tradition Agent (Chapter 8) IS the
               capability that fills this gap — but correctly
               specified as an AGENT (a function: tracking
               lifecycle state across MANY traditions) rather
               than an eighth HISTORIAN (a query interface
               about ONE tradition).

               "Tell me about my family's Christmas tradition's
               history" is a Family Historian question.
               "Which traditions across the platform are
               fading, and why" is a Tradition AGENT question.

               No Tradition Historian needs to be added to
               Volume VII's registry.
```

---

## Chapter 6 — Community Agent

```
FUNCTION:        Cross-community pattern detection — operating
                  across the Community Graph at a scope no
                  single Community Twin (bound to one
                  community) can reach

EXAMPLE:          Detecting that a specific Community Chapter
                  transition pattern (Volume VII, Ch.11's
                  institutionalization stage) correlates with
                  a measurable rise or fall in
                  CommunityTrustProfile ACROSS MANY communities
                  of the same community_family — a pattern no
                  single Community Twin or Historian could
                  ever surface on its own, by construction

INPUT GRAPHS:     Community Graph (primary), Trust Graph,
                  Tradition Graph

RELATIONSHIP TO
COMMUNITY GENOME:  The Community Agent is the eventual primary
                  CONSUMER of the Community Genome (Volume
                  VIII, Ch.26), the cross-community equivalent
                  of the Community Twin's single-community
                  consumption. Both remain Dormant until the
                  Genome earns its DRL.

B2B RELATIONSHIP:  The SECOND explicit, narrow B2B/aggregate
                  carve-out in this Bible, alongside the City
                  Twin/Historian: cross-community statistical
                  patterns, properly k-anonymised, do not
                  identify any single individual, family, or
                  named community. Stated explicitly as a
                  deliberate, narrow exception — not a general
                  loosening of the otherwise-absolute "no B2B
                  use" boundary.
```

---

## Chapter 7 — Opportunity Agent

```
FUNCTION:         Cross-entity matching and emergence detection
                   across the Opportunity Graph, drawing
                   directly on Trust Agent output (Chapter 9
                   specifies this handoff)

TWO MODES:

  Mode 1 — EMERGENCE DETECTION
    Scans for patterns matching Volume VII, Ch.20's
    OpportunityEmerged signal — this is the SOURCE of new
    OpportunityEmerged events, not merely a consumer.

  Mode 2 — MATCHING
    Answers Volume VIII, Ch.22's four Opportunity Intelligence
    questions, each match carrying a complete ProvenanceRecord
    before being surfaced to either party.

CONSTRAINT:        Volume VIII, Ch.22's stricter provenance bar
                   applies with NO exception — the same
                   commercial-and-relational risk Volume IX,
                   Ch.10 already applied to the Opportunity
                   Twin.
```

---

## Chapter 8 — Tradition Agent

```
FUNCTION:          Cross-tradition lifecycle tracking across the
                    Tradition Graph — the function that RUNS
                    the Tradition Detection Engine (Volume VII,
                    Ch.15) and the lifecycle state machine
                    (Volume VII, Ch.14) at scale, across every
                    community and family simultaneously

IMPLEMENTS:          Volume VII, Ch.15's "discovered, not
                    created" principle. The Tradition Agent
                    reads SEEDED-state candidates and surfaces
                    them for host/community confirmation —
                    never auto-confirming on its own.

ALSO PERFORMS:        The two Tradition Graph queries Volume
                    VIII, Ch.13 already specified ("which
                    traditions are fading," "which connect
                    generations") — run recurringly, not just
                    on ad-hoc demand.
```

---

# Part III — Cross-Entity Consent and Authorisation

---

## Chapter 9 — The Agent Authorisation Model

```typescript
AgentAuthorisation {
  agent_type: AgentType
  // 'trust' | 'opportunity' | 'community' | 'historian' | 'tradition'

  inherited_consent_basis: {
    source_records: ConsentReference[]
    // Points to TwinConsentRecord entries (Volume IX, Ch.3)
    // where the Agent draws on Twin-synthesised data, AND/OR
    // Volume III, Part VI privacy_settings where it draws
    // directly on graph data with no Twin yet
  }

  aggregate_output_basis: AggregateConsentBasis
  // For Community Agent cross-community patterns and any
  // future genuinely-aggregate output, the SAME aggregate-
  // consent model already governing City Twins applies —
  // ONLY for outputs that cross the k-anonymity threshold

  operation_scope: AgentOperationScope
  // The specific bounded set of entities THIS operation
  // touches — never unbounded standing access

  authorised_at: Timestamptz
  // Per-operation, not a standing grant
}
```

```typescript
function authoriseAgentOperation(
  agent: AgentType,
  proposedScope: AgentOperationScope
): AuthorisationResult {

  for (const entity of proposedScope.entities) {
    const consent = resolveConsentFor(entity, agent);
    if (!consent.permits(agent.requiredDataAccess)) {
      // PARTIAL EXCLUSION, not wholesale rejection — one
      // entity's non-consent narrows the scope, it does not
      // halt the function for every OTHER consented entity
      proposedScope.entities.remove(entity);
    }
  }

  if (proposedScope.entities.length === 0) {
    return { authorised: false, reason: 'NO_CONSENTED_ENTITIES_REMAIN' };
  }

  if (proposedScope.outputType === 'aggregate'
      && proposedScope.entities.length < K_ANONYMITY_MINIMUM) {
    return { authorised: false, reason: 'BELOW_KANONYMITY_THRESHOLD' };
  }

  return { authorised: true, scope: proposedScope };
}
```

---

# Part IV — Agent-to-Agent and Agent-to-Twin Interaction

---

## Chapter 10 — Resolving Volume IX's Third Question: Twins, Graphs, or Both

**The answer is both, governed by a precise rule.**

```
AN AGENT CONSUMES A TWIN'S SYNTHESIS WHEN:
  The Agent's function concerns a SINGLE entity's already-
  synthesised state, and a Twin for that entity already
  exists at sufficient DRL — reading the Twin is more
  efficient than re-traversing the same graphs, and respects
  the Twin's existing consent_scope (Volume IX, Ch.3).

  EXAMPLE: The Trust Agent, assessing one Person's trust
  trajectory, reads that Person's Personal Twin's Trust Graph
  scope where it exists at DRL 2+, rather than re-querying
  from scratch.

AN AGENT CONSUMES A GRAPH DIRECTLY WHEN:
  The Agent's function is inherently CROSS-ENTITY and no
  single Twin's scope could ever satisfy it, OR when the
  relevant entity has no Twin yet — the Agent traverses the
  seven graphs directly, exactly as a Twin's own synthesis
  cycle would (Volume IX, Ch.14), but across MANY entities.

  EXAMPLE: The Community Agent, detecting a cross-community
  trust correlation, has no choice but to query the Community
  and Trust graphs directly across many communities, since no
  single Community Twin's scope could ever supply a
  cross-community answer.
```

---

## Chapter 11 — Provenance for Synthesis-of-Syntheses

Volume IX's third required question flagged that an Agent drawing on Twins would be performing a "synthesis-of-syntheses," requiring provenance handling beyond Volume IX, Chapter 15's single-Twin standard.

```typescript
// Extending Volume VIII, Ch.20's ProvenanceRecord and
// Volume IX, Ch.15's Twin-specific ConclusionTypes

ConclusionType enum (further extended, this volume):
  // ...all prior entries, plus:
  'agent_trust_assessment'
  'agent_opportunity_match'
  'agent_community_pattern'
  'agent_historian_synthesis'
  'agent_tradition_lifecycle_update'

AgentProvenanceRecord extends ProvenanceRecord {
  // All base ProvenanceRecord fields apply unmodified, PLUS:

  layered_provenance: {
    direct_evidence_events: DomainEventReference[]
    // Any DomainEvents the Agent read directly from a graph

    inherited_twin_provenance: ProvenanceRecordReference[]
    // Where the Agent consumed a Twin's synthesis, this field
    // points to THAT Twin's OWN ProvenanceRecord for the
    // specific synthesis cycle read — the Agent's trail does
    // NOT duplicate or re-summarise the Twin's evidence; it
    // CHAINS to it, preserving full re-traversability without
    // flattening into an unverifiable summary
  }

  // The rule this enforces: a TWO-HOP trace is always
  // available. From any Agent output, trace to the Twin
  // synthesis (or direct DomainEvents) that produced it, and
  // from that, trace further to the exact DomainEvents that
  // produced THAT. Provenance never terminates at an
  // intermediate, unverifiable summary.
}
```

---

## Chapter 12 — Resolving Volume IX's Fourth Question: Agent-to-Agent Handoff

The clearest overlap case: the Opportunity Agent's matching (Mode 2) fundamentally requires Trust Agent output, since opportunities emerge FROM accumulated trust (Volume VII, Ch.20).

```
THE HANDOFF RULE:

  An Agent may consume another Agent's MOST RECENT output as
  an INPUT to its own function, exactly as it would consume
  any other graph data — there is no special "Agent-to-Agent
  protocol" beyond this: one Agent's output is data, written
  into the same graphs any other Agent (or Twin) can read.

  WHAT IS EXPLICITLY NOT PERMITTED: direct, synchronous
  Agent-to-Agent invocation, where one Agent calls another
  live, mid-operation. Disallowed for two reasons:

    1. It would break Chapter 11's two-hop provenance
       guarantee — a live call chain risks an unverifiable,
       flattened intermediate summary.

    2. It would couple Agents' DRL gating together in ways
       that violate this volume's governing principle — an
       Opportunity Agent at DRL 4 must NOT force a live
       computation from a Trust Agent only honestly at DRL 2
       for the entities in question. The asynchronous,
       write-then-read model means the Opportunity Agent
       simply sees whatever DRL-appropriate Trust Graph state
       genuinely exists at read time.

  EXAMPLE, FULLY SPECIFIED:
    The Trust Agent runs on its own schedule, writing TrustEdge
    updates into the Trust Graph. The Opportunity Agent, when
    matching, READS the current Trust Graph state directly
    (Chapter 10's second path) — it does not invoke the Trust
    Agent. If the Trust Agent has not yet processed a pair,
    the Opportunity Agent sees thinner trust data and its own
    match confidence is correspondingly, honestly lower.
```

---

# Part V — DRL Gating, Per Agent

---

## Chapter 13 — Applying Volume IX's Ladder to Functions, Not Representations

This resolves Volume IX's fifth required question, with one necessary adaptation: an Agent's DRL is assessed against its FUNCTION's data requirements, not a single entity's data.

```
A Twin's current_drl is a single value for ONE entity.

An Agent has NO single current_drl, because its function
spans many entities, each potentially at a different DRL.
An Agent's operation is gated PER-OPERATION, against the
SPECIFIC entities that operation's scope actually includes.
```

```typescript
function authoriseAgentOutput(
  agent: AgentType,
  scope: AgentOperationScope,
  requestedOutput: AgentOutputLevel
): AuthorisationResult {

  // MINIMUM current_drl across every entity in this
  // operation's final, consent-filtered scope
  const effectiveDRL = Math.min(
    ...scope.entities.map(e => getDRLFor(e, agent.domain))
  );

  const ladder = {
    'history_only':           2,
    'insights':                3,
    'recommendations':           4,
    'full_copilot_function':       5,
  };

  if (effectiveDRL < ladder[requestedOutput]) {
    return { authorised: false, reason: 'INSUFFICIENT_EFFECTIVE_DRL' };
  }

  return { authorised: true };
}
```

```
THE CONSEQUENCE: an Agent's output for any GIVEN operation is
only ever as mature as its LEAST mature contributing entity —
never an average, never the maximum. A Community Agent
detecting a pattern across ten communities, nine at DRL 4 and
one brand-new at DRL 0, produces output gated at DRL 0's
ceiling for that operation. This is the strictest possible
reading of "no agent may operate above the maturity of the
supporting data" — more conservative than a weighted average,
deliberately, because an average would mean the output is,
for that one thin entity, dishonestly more confident than the
evidence supports.
```

---

## Chapter 14 — Current Honest State of Every Agent, At This Writing

```
Trust Agent          — effective DRL 0-1 across nearly all
                        entities. No platform has accumulated
                        sufficient temporal span yet.

Opportunity Agent      — effective DRL 0. Mode 1 requires Trust
                        Agent output that does not yet
                        meaningfully exist; Mode 2 requires
                        Mode 1 as a precondition.

Community Agent          — effective DRL 0. Cross-community
                        pattern detection requires MULTIPLE
                        communities each independently reaching
                        non-trivial DRL — not yet collectively
                        reached.

Historian Agent             — effective DRL 0-1, with a caveat:
                        its function is orchestration, so it can
                        only be as mature as the LEAST mature
                        Historian it might route to — and every
                        one of the seven remains at DRL 0.

Tradition Agent                — effective DRL 0-1. Some SEEDED-
                        state candidates are plausibly already
                        detectable, making this the closest of
                        the five to a genuine DRL-2 "history
                        only" output — but not there yet.
```

This chapter is included deliberately: stating plainly, in the document itself, that every Agent named in this volume is not yet doing anything.

---

# Part VI — MVP Scope

---

## Chapter 15 — What to Build First

```
BUILD (MVP):

  ✅ The Agent type registry (Ch.1, Ch.3-8) — five Agent types
     defined in schema/codebase as named functions, even
     though none execute meaningfully yet
  ✅ AgentAuthorisation structure and authoriseAgentOperation()
     (Ch.9) — built and tested against the same "prove it
     before it is load-bearing" discipline Volume IX
     established for Twin consent infrastructure
  ✅ AgentProvenanceRecord structure (Ch.11) — the layered,
     two-hop chain built into the schema now, so the FIRST
     Agent output ever produced is provenance-complete from
     the start
  ✅ The per-operation effective-DRL computation (Ch.13) —
     built and unit-tested against representative scope
     combinations to confirm the minimum-not-average rule
     behaves correctly before it is ever genuinely exercised
```

```
CAPTURE (data foundation, not yet surfaced as features):

  📊 No new capture burden whatsoever — every Agent operates
     exclusively on DomainEvents and graph data already
     mandated for capture across Volumes II through IX. This
     volume, like Volume IX before it, is purely an
     interpretation and orchestration layer.
```

```
DO NOT BUILD YET:

  ❌ Any Agent's actual scheduled execution — per Ch.14's
      honest accounting, there is not yet sufficient data for
      any execution to produce output above the "history only"
      floor
  ❌ Any Agent-to-Agent live data flow beyond the asynchronous
      write-then-read pattern — Ch.12 prohibits synchronous
      invocation architecturally, not merely defers it
  ❌ Any product-facing UI surface for any Agent's output,
      identical in spirit to Volume IX's prohibition on Twin UI
  ❌ The aggregate_output_basis exception path in active use —
      the k-anonymity check should exist and be tested, but no
      Community Agent output will genuinely clear that
      threshold for some time
```

**The single most important MVP discipline in this volume, identical in spirit to Volume IX's:** build the authorisation, provenance, and DRL-gating infrastructure completely now, against zero real operational load, so that the first time any Agent genuinely has something honest to say, the scaffolding around it is already proven correct rather than rushed into existence under delivery pressure.

---

# Volume X Summary

```
Agent vs. Twin:
  A Twin represents (noun: one entity). An Agent acts
  (verb: a function across or between entities). This
  distinction determines scope of access, which determines
  everything else.

Five Agents, Each Precisely Scoped:
  Trust Agent — continuous cross-entity trust assessment
  Opportunity Agent — two modes, source of OpportunityEmerged
    events, inherits the stricter provenance bar unconditionally
  Community Agent — cross-community patterns, second explicit
    B2B/aggregate carve-out, gated strictly on k-anonymity
  Historian Agent — orchestration layer over the seven existing
    Historians, NOT an eighth Historian
  Tradition Agent — literal implementation of "discovered, not
    created," fills the apparent Tradition Historian gap as a
    cross-tradition function

Cross-Entity Consent:
  AgentAuthorisation, distinct from TwinConsentRecord —
  inherited additively, re-derived per-operation, partial
  exclusion not wholesale rejection, narrow aggregate exception
  gated on k-anonymity

Agent-to-Agent and Agent-to-Twin Interaction:
  Both Twin-consumption and direct-graph-consumption valid,
  governed by a precise rule
  Two-hop provenance chain prevents synthesis-of-syntheses from
  terminating at an unverifiable summary
  NO synchronous Agent-to-Agent invocation — asynchronous
  write-then-read only

DRL Gating, Per Operation:
  Effective DRL for any operation is the MINIMUM across every
  entity in scope, never an average
  Current honest state of all five Agents disclosed explicitly:
  every one sits at DRL 0-1, producing no real output

MVP Scope:
  4 build items (all infrastructure, built against zero real
  load, deliberately)
  1 capture confirmation (no new burden)
  4 explicit deferrals, including no scheduled execution and no
  product-facing UI for any Agent, anywhere, yet
```

An Agent is not a feature ZUKA ships either. Like a Twin, it is a discipline ZUKA keeps: that a function spanning many people's, families', and communities' data will only ever speak as confidently as the least mature data point it honestly touches — never the most impressive one, never an average that flatters the system's apparent maturity.

---

# What Volume XI Must Address

With Volumes IX and X complete, ZUKA's intelligence architecture now has: seven graphs (Volume VIII), six Twins (Volume IX), and five Agents (this volume).

Volume XI must, at minimum:

1. **Confirm or revise the Dormant Feature Registers** established across Volume VII's Appendix A, Volume VIII's Part X, and this volume's Part V/Chapter 14, ideally unifying them into a single, platform-wide DRL dashboard
2. **Address the still-unresolved founder-review items**: the platform-identity reframe (Volume VII), Knowledge Federation (Volume VIII), and this volume's own Community Agent B2B carve-out
3. **Specify the Infrastructure & Ecosystem volume** the Docking Principle has been promised across three prior volumes now
4. **Begin treating the five Agents and six Twins as a unified "Intelligence Layer"** for resourcing and prioritisation, given how thoroughly interdependent they have proven to be

---

> **ZUKA Architecture Bible**
> Volume X — Distributed Intelligence Architecture
> Built in Kampala. Built for Africa. Built for the world.
