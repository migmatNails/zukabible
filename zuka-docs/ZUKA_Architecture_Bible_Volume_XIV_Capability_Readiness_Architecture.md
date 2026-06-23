# ZUKA Architecture Bible
# Volume XIV — Capability Readiness Architecture (CRL)

> **Series:** ZUKA Architecture Bible
> **Volume:** XIV of N
> **Status:** Canonical — introduces the second governing axis alongside DRL,
>              reconciled against all four existing DRL ladder wordings
> **Depends on:** The full Bible to date (Volumes I-XIII)
> **Required by:** Volume XV (Opportunity Infrastructure), Volume XVI (Community OS),
>                  and every future volume specifying a Twin, Agent, Copilot, or
>                  OS-layer capability

---

# Preface

Every prior volume that introduced a maturity gate — Volume VII, Volume XI, Volume XII, Volume XIII — was answering one question, asked four different ways: **is there enough data for this capability to exist at all?** That question has now been asked and answered consistently four times, and Volume XIII, Chapter 4 confirmed all four landings on the same six-level gate.

This volume's source material asks a genuinely different question, one this Bible has never explicitly separated out before: once a capability is permitted to exist, **how deeply should it actually manifest?** A Community Twin (Volume IX) that has earned DRL 3 — sufficient data maturity for genuine insight — could still, in principle, be expressed to its community as anything from a single quiet number on a dashboard to a fully interactive Copilot offering proactive suggestions. DRL alone does not answer which of those is appropriate. That is a different axis entirely, and conflating it with DRL would be a mistake this volume exists specifically to prevent.

The mission, stated as given: **fair, adaptive capability activation.** The core principle, also stated as given and adopted without modification because it is exactly correct and already implicit across this Bible's entire AI-restraint posture since Volume VII: **the more sophisticated ZUKA becomes internally, the simpler it becomes externally.**

---

# Part I — Two Axes, Not One

---

## Chapter 1 — DRL and CRL Are Different Questions

```
DRL (Data Readiness Level) — already canonical since Volume VII:
  Determines WHETHER a capability can exist.
  Answers: "Is there enough evidence for this conclusion to
  be honest?"
  Governs: data, evidence, confidence.

CRL (Capability Readiness Level) — new in this volume:
  Determines HOW DEEPLY a capability manifests.
  Answers: "Given that this conclusion CAN honestly exist,
  how much should the system actually DO with it, and how
  visibly?"
  Governs: exposure, interactivity, autonomy.
```

The clearest way to hold the distinction: DRL is about the **evidence**. CRL is about the **relationship** between the system and the person, family, community, or city it is serving — specifically, how much that relationship has matured to the point where deeper, more proactive, more autonomous capability is welcome rather than presumptuous.

```
A CONCRETE CASE THAT MAKES THE DISTINCTION UNAVOIDABLE:

  A newly-formed Community (Volume VI) with a small but
  unusually active founding group could, in principle, reach
  DRL 3 (Volume VII: "useful insights possible") on its
  Community Trust Profile within months — the EVIDENCE is
  genuinely there.

  But a community three months old, however data-rich, has
  not yet built the RELATIONSHIP with the platform — or
  demonstrated the kind of sustained governance stability
  (Volume VI, Part XIV) — that would make it appropriate for
  ZUKA to proactively COORDINATE on its behalf (e.g., a
  Community Copilot, Volume VI Part XVIII, autonomously
  suggesting governance changes). DRL 3 says the data CAN
  support an insight. CRL is the separate, additional
  judgement of how much the system should actually DO with
  that insight, unprompted, for THIS particular community,
  at THIS particular stage of its relationship with the
  platform.

  Without CRL, a team would have only one lever (DRL) to
  govern an outcome that genuinely depends on two different
  things. This volume supplies the second lever.
```

---

## Chapter 2 — Reconciling Against the Existing Four DRL Ladders

Per the discipline established across Volumes XI through XIII, this is performed immediately and explicitly, before any further specification, so that CRL is never mistaken for a fifth wording of the same DRL gate.

```
THIS IS NOT A FIFTH DRL LADDER.

The four existing DRL ladders (Volume VII: data maturity;
Volume XI: infrastructure capability; Volume XII: data use
permission; Volume XIII: trust-specific) all describe the
SAME six levels (0-5), confirmed in lockstep, answering the
SAME underlying question from four vantage points.

CRL is not a fifth vantage point on that question. It is
answering a DIFFERENT question entirely, and it is therefore
NOT required to share DRL's six-level structure, NOT required
to align numerically with DRL at any given level, and is
specified with its OWN distinct number of states (six, as it
happens — Chapter 3 — but this is confirmed here as a
coincidence of count, not evidence that CRL is secretly the
same ladder restated).

THE RELATIONSHIP BETWEEN THE TWO AXES IS GOVERNANCE, NOT
EQUIVALENCE:

  DRL is a NECESSARY but NOT SUFFICIENT condition for CRL
  advancement. A capability cannot reach a deep CRL state
  (Chapter 3) without first having earned the DRL level its
  underlying conclusion requires (per Volume VII/XI/XII/XIII's
  existing, unmodified ladders) — but reaching the required
  DRL does not AUTOMATICALLY grant the corresponding CRL
  state. CRL must be independently earned, against its own
  criteria (Chapter 4), even once DRL has cleared the way.

  This is formalised as a simple governing inequality,
  applying to every gated capability in this Bible from this
  volume forward:

    capability_manifestation_depth ≤ min(DRL_permits, CRL_earned)

  Neither axis alone determines what the user actually sees.
  Both must independently clear their own bar.
```

---

# Part II — Readiness Domains

---

## Chapter 3 — Six Domains, Each Assessed Independently

```
READINESS DOMAINS:
  Identity
  Community
  Gathering
  City
  Country
  Platform
```

This is confirmed as a direct extension of the per-domain, per-market independence already established for DRL in Volume VII, Part XII ("assessed independently per domain, and independently per market") — this volume's contribution is making that independence explicit across a SPECIFIC, named domain hierarchy rather than leaving "domain" undefined.

```
THE HIERARCHY, AND WHY EACH LEVEL IS ASSESSED SEPARATELY:

  Identity      — a single Person's own readiness (relates
                  directly to Volume IX's per-Twin DRL,
                  Chapter 2: "current_drl... assessed
                  independently of any other Twin")

  Community      — a single Community's readiness (Volume VI's
                  CommunityTrustProfile, Volume IX's Community
                  Twin)

  Gathering        — a single Gathering's or Tradition series'
                  readiness (Volume IX, Chapter 9's Gathering
                  Twin scoping note already anticipated this:
                  "a single-occurrence Gathering Twin... will,
                  in practice, rarely justify advancing past
                  DRL 1-2")

  City              — aggregate readiness across a City entity
                  (Volume IX, Chapter 11's City Twin scope)

  Country             — a NEW level of aggregation, not
                  previously named anywhere in this Bible —
                  see Chapter 3a below for why this is a
                  genuine and necessary addition

  Platform               — the broadest level: ZUKA's overall,
                  cross-market readiness, relevant primarily
                  to genuinely platform-wide capability
                  decisions (e.g., should ANY market be
                  permitted to attempt DRL 5 / CRL 6
                  capabilities yet, regardless of that
                  market's own individual readiness)

A capability's readiness is assessed AT THE SPECIFIC DOMAIN
LEVEL the capability actually operates at — per Volume X,
Chapter 13's already-established principle for Agents
("effective DRL... is the MINIMUM across every entity in
scope, never an average"), now confirmed as the general rule
across ALL SIX of these domain levels, not only the Agent
case it was first specified for.
```

---

## Chapter 3a — Why "Country" Is a Genuine, Necessary Addition

This is worth pausing on, because it is the one place in this volume's source material that names something not already implied elsewhere in the Bible.

```
Volume VII, Part XII's original DRL framework specified
assessment "independently per domain, and independently per
market" — and "market," throughout this Bible's revenue and
expansion sections (referenced across ZUKA.md's four-stage
revenue model), has always implicitly meant something closer
to CITY (Kampala, then other Ugandan cities, then East African
cities) than COUNTRY as a distinct aggregation tier.

This volume's addition of COUNTRY as its own readiness domain,
sitting between City and Platform, is adopted as genuinely
useful and is retained: a B2B Pulse Report (ZUKA.md's Product
1) for "Uganda" as a whole is a different aggregation than
a single City Twin's output, and a future Knowledge Federation
decision (Volume VIII, Ch.24, still held for founder review)
would almost certainly need to be evaluated at COUNTRY
granularity first, given that data protection law (Uganda's
Data Protection and Privacy Act 2019, referenced throughout
this Bible) is itself a country-level legal boundary, not a
city-level one. Country-level readiness assessment did not
exist as a named concept before this volume; it does now.
```

---

# Part III — Capability States

---

## Chapter 4 — The Six States, Fully Specified

```
CAPABILITY STATES:
  Observing → Learning → Advising → Assisting →
  Coordinating → Intelligence
```

Each state is specified here with its own concrete CRL criteria — what must be independently earned, beyond DRL's evidence bar, before a capability is permitted to manifest at that depth, for a given readiness domain (Part II) instance.

```
STATE 1 — OBSERVING
  The system watches. Nothing is surfaced to any human.
  Equivalent in spirit to DRL 0-1's "no output" floor
  (Volume IX, Ch.12), but assessed on the CRL axis
  independently — a capability can be DRL-eligible to surface
  basic insight_display (DRL 2+, per Volume IX's ladder) and
  STILL remain in Observing state if the relevant entity has
  not yet met Observing→Learning's own earned criteria below.

  Earned criteria to ADVANCE beyond Observing:
    Sustained presence — the entity (Person, Community, etc.)
    has been active on the platform for a minimum duration
    appropriate to its readiness domain (Part II), not merely
    accumulated enough EVENTS in a short burst — this is the
    CRL-specific anti-velocity-farming check, distinct from
    but related to Volume XIII, Chapter 7's DRL-side Trust
    Farming Detection

STATE 2 — LEARNING
  The system has begun forming an internal model (a Twin's
  early synthesis, Volume IX, Ch.14) but still surfaces
  NOTHING outward — this state exists specifically to give
  CRL its own "collecting, not yet speaking" floor, distinct
  from DRL's equivalent floor, because an entity could
  legitimately have ENOUGH data (DRL-side) while the
  RELATIONSHIP (CRL-side) is still too new for the system to
  have earned the right to speak about what it has learned.

  Earned criteria to ADVANCE beyond Learning:
    Consent confirmation — per Volume IX, Part II's
    TwinConsentRecord (for Identity/Community/Gathering/
    Opportunity domains) being not merely GRANTED but having
    survived at least one full re-confirmation cycle or
    equivalent sustained-consent signal, distinct from a
    single initial opt-in click

STATE 3 — ADVISING
  The system may now surface insight_display (Volume IX,
  Ch.12's DRL-side permission) — but ONLY in a passive,
  pull-based form: the person or community must ASK, or
  actively open the relevant view, to see it. This is the
  first state where DRL's output gates (Volume IX/X) and
  CRL's states actually begin to interact directly, per the
  governing inequality in Chapter 2.

  Earned criteria to ADVANCE beyond Advising:
    Demonstrated engagement with passive insight — the
    entity has actually VIEWED Advising-level output multiple
    times, per a defined minimum, without negative signal
    (no privacy complaint, no consent revocation per Volume
    IX, Ch.5) — this is the CRL-specific evidence that
    PROACTIVE surfacing (the next state) would be welcomed
    rather than intrusive

STATE 4 — ASSISTING
  The system may now PROACTIVELY surface insight (a
  notification, not merely a view the person had to open) and
  may surface recommendation_generation-level output (Volume
  IX, Ch.12's DRL 4 permission), but ONLY ever as a SUGGESTION
  requiring explicit human confirmation before any action is
  taken — this is the Copilot tier (Volume IV Part XIV; Volume
  VI Part XVIII) as previously specified, now confirmed as
  CRL State 4 specifically, not a separate concept

  Earned criteria to ADVANCE beyond Assisting:
    A sustained track record of the entity ACTING ON or
    explicitly DISMISSING (not merely ignoring) prior
    Assisting-tier suggestions — evidence that the
    relationship has matured to the point where the entity
    is actively partnering with, not merely tolerating, the
    system's suggestions

STATE 5 — COORDINATING
  The system may take LIMITED AUTONOMOUS ACTION within an
  explicitly pre-authorised, narrow scope (e.g., automatically
  advancing a Tradition's lifecycle state per Volume VII,
  Ch.14's existing state machine, without requiring
  confirmation for EVERY occurrence, once a Tradition has
  reached ACTIVE state) — this maps directly onto DRL 4's
  existing "Automation possible" / "Trust-Assisted Automation"
  permission (Volume IX/XIII), now confirmed as requiring
  BOTH that DRL permission AND this CRL state, per Chapter 2's
  governing inequality

  Earned criteria to ADVANCE beyond Coordinating:
    The narrowly-scoped autonomous actions already permitted
    have been running for a sustained period with zero
    disputed outcomes (Volume XIII, Part VI's Appeals
    mechanism having never been triggered against this
    specific capability for this specific entity) — the
    cleanest possible evidence that deeper autonomy is
    warranted

STATE 6 — INTELLIGENCE
  The system's full Twin/Agent/Historian capability (Volume
  IX/X, DRL 5: "AI Twins, Copilots, Intelligence Systems") is
  available, including third_party_query_response (Volume IX,
  Ch.12) where consented. This is the ceiling state on both
  axes simultaneously — DRL 5 AND CRL 6 both required, per
  Chapter 2's inequality, and per Volume X, Chapter 14's
  honest current-state finding, NO entity anywhere on the
  platform has reached DRL 5 on any domain at this writing,
  meaning NO entity has reached CRL 6 either, regardless of
  how mature any individual relationship might otherwise seem —
  the inequality is genuinely binding, not merely theoretical.
```

---

## Chapter 5 — The Governing Constitutional Statement

```
CAPABILITIES ACTIVATE THROUGH READINESS, NOT PRIVILEGE.
```

This single sentence is adopted as binding constitutional law, and its precise meaning is worth drawing out explicitly: "not privilege" rules out EVERY alternative basis for granting deeper capability that this Bible has, across thirteen prior volumes, consistently rejected in other forms — payment (no subscription tier, however expensive, can purchase a CRL state, exactly as Volume VI, Ch.14 already ruled that reputation "must never be gamed or purchased, only earned"), seniority alone (a founding member, per Volume VI Ch.8's Founder role, does NOT automatically grant their Community a higher CRL state — the Community's readiness is assessed on its own merits, per Chapter 3's domain-level independence), or platform favouritism (no city or country is fast-tracked ahead of its own genuinely measured readiness, regardless of strategic commercial interest in that market). Readiness, on both axes, independently earned, is the only path — full stop.

---

# Part IV — Activation Logic

---

## Chapter 6 — The Combined Activation Function

This chapter operationalises Chapter 2's inequality into the same enforcement-function style this Bible has used consistently since Volume IX's `authoriseTwinOutput()`.

```typescript
CapabilityReadiness {
  entity_id:                UUID
  readiness_domain:            ReadinessDomain
  // 'identity' | 'community' | 'gathering' | 'city' |
  // 'country' | 'platform' — per Part II

  current_drl:                    Integer(0-5)
  // Per the existing, unmodified Volume VII/XI/XII/XIII
  // ladder, assessed exactly as already specified there —
  // this volume does NOT re-assess or duplicate that work

  current_crl:                       CapabilityState
  // 'observing' | 'learning' | 'advising' | 'assisting' |
  // 'coordinating' | 'intelligence' — per Chapter 4,
  // assessed independently against its OWN earned criteria

  crl_advancement_history:              CRLTransition[]
  // Per-transition record of WHICH earned criteria were met
  // and WHEN — itself a ProvenanceRecord-backed conclusion
  // (Volume VIII, Ch.20), since "why is this Community
  // allowed to be at Assisting state" is exactly the kind
  // of question Volume VIII's Knowledge Provenance principle
  // already requires every conclusion in this Bible to answer
}

function authoriseCapabilityManifestation(
  entity: CapabilityReadiness,
  requestedDepth: CapabilityState
): AuthorisationResult {

  // STEP 1 — DRL gate (per existing, unmodified ladders)
  const drlRequiredForDepth = CRL_TO_MINIMUM_DRL_MAP[requestedDepth];
  // e.g., 'assisting' requires DRL 4 minimum (per Chapter 4's
  // mapping to Volume IX/XIII's existing DRL-side permissions)

  if (entity.current_drl < drlRequiredForDepth) {
    return { authorised: false, reason: 'INSUFFICIENT_DRL' };
  }

  // STEP 2 — CRL gate (genuinely independent, per Chapter 2)
  const crlRank = CRL_STATE_ORDER[entity.current_crl];
  const requestedRank = CRL_STATE_ORDER[requestedDepth];

  if (crlRank < requestedRank) {
    return { authorised: false, reason: 'INSUFFICIENT_CRL' };
    // Note: this can fire even when DRL fully permits the
    // requested depth — exactly the case Chapter 1's
    // worked example (the data-rich but relationship-young
    // Community) was constructed to illustrate
  }

  // BOTH gates cleared — Chapter 2's inequality satisfied
  return { authorised: true };
}
```

This function is adopted as the SECOND mandatory check every Twin output (Volume IX, Ch.12's `authoriseTwinOutput`), Agent output (Volume X, Ch.13's `authoriseAgentOutput`), and any future OS-layer capability (Volume XI, Part V) must pass — run AFTER the existing DRL-only checks those functions already perform, not as a replacement for them. Every existing authorisation function in this Bible is, from this volume forward, understood to call `authoriseCapabilityManifestation` as an additional, required step.

---

# Part V — MVP Scope

---

## Chapter 7 — What to Build First

```
BUILD (MVP):

  ✅ The CapabilityReadiness schema (Ch.6), added for every
     entity type already carrying a current_drl value
     (Volume IX's Twins, Volume X's Agent operation scopes) —
     structural fields, cheap now, consistent with this
     Bible's now-familiar discipline since Volume V
  ✅ authoriseCapabilityManifestation() (Ch.6), built and unit-
     tested against the full CRL_TO_MINIMUM_DRL_MAP and
     CRL_STATE_ORDER combinations — proven correct before it
     is ever load-bearing, exactly mirroring Volume IX/X's
     "prove the gate before it matters" discipline
  ✅ Every entity's current_crl initialised to 'observing' by
     default, with NO entity anywhere starting at any deeper
     state — per Chapter 5's constitutional rule, there is no
     legitimate basis for any entity to be granted a CRL head
     start
```

```
DO NOT BUILD YET:

  ❌ Any actual CRL advancement logic beyond Observing — per
      Volume X, Chapter 14's honest finding that no entity has
      reached DRL 5 on any domain, and per Chapter 4's earned
      criteria for even the EARLIEST advancement (Observing→
      Learning) requiring sustained presence over a meaningful
      duration, no entity at MVP has had TIME to earn
      advancement regardless of DRL — this is not a
      restriction this volume imposes artificially, it is simply
      what is honestly true on day one of any new platform
  ❌ Any product-facing indication of "CRL state" to a user —
      identical in spirit to every prior volume's prohibition
      on premature AI-feature UI; a state with nothing to show
      has nothing to indicate
```

**The single most important MVP discipline in this volume:** initialise the schema and the gate now, at `observing` for everyone, with zero exceptions — so that the discipline of "readiness, not privilege" is structurally true from the very first entity ever created, rather than being a rule retrofitted after the temptation to grant an early adopter or a strategic city a head start has already been acted on once.

---

# Volume XIV Summary

```
Two Axes, Not One:
  DRL answers "can this exist" (evidence). CRL answers "how
  deeply should this manifest" (relationship maturity).
  Confirmed as genuinely independent questions via a concrete
  worked example (the data-rich, relationship-young Community)

Reconciliation:
  CRL explicitly confirmed as NOT a fifth DRL ladder — the four
  existing DRL ladders (Volumes VII, XI, XII, XIII) remain
  exactly as reconciled in Volume XIII, Ch.4, untouched
  Governing inequality established:
  manifestation_depth ≤ min(DRL_permits, CRL_earned)

Six Readiness Domains:
  Identity, Community, Gathering, City, Country, Platform —
  each assessed independently, extending Volume X Ch.13's
  minimum-not-average principle from the Agent case to all
  six domain levels generally
  Country confirmed as a genuine new addition — not previously
  named, justified against existing B2B and data-protection-
  law granularity needs

Six Capability States:
  Observing → Learning → Advising → Assisting → Coordinating
  → Intelligence, each given concrete, independently-earned
  advancement criteria distinct from any DRL threshold
  State 4 (Assisting) confirmed as the existing Copilot tier,
  not a new concept; State 6 (Intelligence) confirmed as the
  simultaneous DRL-5-and-CRL-6 ceiling, genuinely unreached
  by any entity at this writing

Constitutional Principle:
  "Capabilities activate through readiness, not privilege" —
  explicitly ruling out payment, seniority, and platform
  favouritism as alternative paths to deeper capability,
  consistent with this Bible's prior rejections of each in
  other specific forms (Volume VI's reputation, Volume IX's
  Twin consent)

Activation Logic:
  authoriseCapabilityManifestation() specified as a mandatory
  SECOND check, called by every existing DRL-gated
  authorisation function in this Bible (Twin, Agent, future
  OS-layer) — not a replacement for existing DRL checks, an
  addition to them

MVP Scope:
  3 build items, all structural/schema, zero entities granted
  any head start
  2 explicit deferrals, both honestly following from the same
  "no entity has had time to earn advancement yet" finding
  already established for DRL in Volume X
```

---

# What Volume XV Must Address

Opportunity Infrastructure & Economic Coordination — already uploaded alongside this volume's source brief — must, at minimum:

1. **Confirm, rather than re-specify, the Opportunity Pipeline** already fully reconciled in Volume XIII, Chapter 10 — the uploaded source material's "Participation → Trust → Opportunity → Value" is a four-stage compression of Volume XIII's already-confirmed six-stage chain and needs to be reconciled as the same pipeline, not presented as new
2. **Confirm Opportunity OS** against Volume XI, Chapter 11's already-completed composite-view ruling
3. **Specify, as genuinely new content, only the Sponsorship Engine, Mentorship Engine, Scholarship Engine, and Creator Economy Engine** — none of which have operational specification anywhere in this Bible despite being referenced in ZUKA.md's B2B product suite and Volume VI's Opportunity Integration
4. **Apply this volume's CRL framework** to every one of those four engines explicitly, given they are exactly the kind of proactive, potentially-autonomous capability (Sponsorship matching, automated Scholarship distribution) this volume was built to govern

---

> **ZUKA Architecture Bible**
> Volume XIV — Capability Readiness Architecture (CRL)
> Built in Kampala. Built for Africa. Built for the world.
