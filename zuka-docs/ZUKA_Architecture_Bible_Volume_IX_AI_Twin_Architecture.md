# ZUKA Architecture Bible
# Volume IX — AI Twin Architecture

> **Series:** ZUKA Architecture Bible
> **Volume:** IX of N
> **Status:** Canonical — fully DRL-gated, zero Twins active at this writing
> **Depends on:** Volume I (Vision), Volume II (Domain Model), Volume III (Identity),
>                  Volume IV (Gatherings), Volume V (Memory), Volume VI (Communities),
>                  Volume VII (Temporal Architecture), Volume VIII (Knowledge Graph)
> **Required by:** Volume X (Distributed Intelligence) and every future
>                  intelligence-layer volume

---

# Preface

Volume VIII closed by naming six questions Volume IX would have to answer before an AI Twin could be considered canonical architecture rather than a slogan. This volume answers all six, in order, and adds nothing beyond what those six questions require.

The source brief for this volume is deliberately minimal — six Twin types and one DRL gating ladder. That minimalism is respected rather than padded. Every Twin type named is given a real structural definition, bound explicitly to the seven graphs established in Volume VIII, and gated by the same DRL discipline established in Volume VII. Nothing in this volume is built. Everything in this volume has a Feature Seed.

**The single sentence that governs this entire volume, stated up front because it must never be lost in the detail that follows:** a Twin is not a chatbot, not an assistant, and not a surveillance system. It is a structured, provenance-bound, consent-gated synthesis of a person's, family's, community's, gathering's, opportunity's, or city's own data — owned by the entity it represents, explainable in every output, and silent until both its data and its permission to exist are genuinely earned.

---

# Part I — What a Twin Actually Is

---

## Chapter 1 — Distinguishing a Twin From Everything It Is Not

This is the first of Volume VIII's six required resolutions: what a Twin actually is, structurally, distinguished from a Historian, a Copilot, and a chatbot.

```
A CHATBOT
  Stateless or thinly-stateful conversational interface.
  No persistent structural representation of the entity
  it discusses. Answers questions; does not constitute
  a queryable model of anything.
  ZUKA does not build these as a category in this Bible.

A HISTORIAN (Volume VII, Part XIV; Volume VIII, Ch.21)
  A QUERY INTERFACE over one or more of the seven graphs
  (Volume VIII, Ch.7), scoped to a single domain
  (Memory, Family, Community, Relationship, Legacy,
  Personal, City). Answers a specific question when asked.
  Holds no standing representation between queries —
  each query is freshly computed from the graph.

A COPILOT (Volume IV, Part XIV; Volume VI, Part XVIII)
  A TASK-ORIENTED assistant scoped to a specific workflow
  (planning a retreat, forecasting attendance). Suggests
  actions. Does not claim to represent the entity it
  assists — it assists THAT entity's own decision-making.

AN AI TWIN (this volume)
  A PERSISTENT, STRUCTURED, NAMED REPRESENTATION of a
  specific entity (a Person, Family, Community, Gathering,
  Opportunity domain, or City), synthesised continuously
  from that entity's own data across all seven graphs
  (Volume VIII, Ch.7), maintained as a standing model
  between queries (not recomputed from scratch each time,
  though always re-verifiable against its source per
  Part VI), and explicitly representing the entity to
  itself and, where consented, to others.

  The defining structural difference from a Historian:
  a Historian ANSWERS QUESTIONS ABOUT an entity. A Twin
  IS a standing representation OF an entity. This is not
  a marketing distinction — it has direct architectural
  consequences for consent (Part II), DRL gating (Part IV),
  and legacy (Part VII) that a mere query interface does
  not carry.
```

---

## Chapter 2 — The Canonical Twin Record

Every Twin, regardless of which of the six types it represents, shares a common structural envelope. The type-specific content (Part III) extends this base.

```typescript
Twin {
  // ── IDENTITY ──────────────────────────────────────────────
  twin_id:               UUID
  twin_type:               TwinType
  // 'personal' | 'family' | 'community' | 'gathering' |
  // 'opportunity' | 'city'

  represented_entity_id:     UUID
  // The Person, Family, Community, Gathering, Opportunity
  // domain, or City entity (Volume II's taxonomy) this Twin
  // is a standing representation OF — never of anything else

  // ── EXISTENCE STATE ───────────────────────────────────────
  status:                   TwinStatus
  // 'seed' | 'active' | 'dormant_again' | 'memorialised' |
  // 'decommissioned'
  // See Part IV for the full lifecycle this status moves through

  current_drl:                Integer(0-5)
  // Per the DRL Gating ladder (Part IV) — the Twin's current
  // capability ceiling, assessed independently of any other
  // Twin, even another Twin of the same type representing a
  // different entity (a long-tenured Community's Twin may sit
  // at DRL 4 while a brand-new Community's Twin sits at DRL 0)

  // ── CONSENT ───────────────────────────────────────────────
  consent_record:               TwinConsentRecord
  // See Part II — a Twin literally cannot be instantiated
  // (cannot move past 'seed' status) without this being
  // populated first

  // ── SOURCE GRAPH BINDING ──────────────────────────────────
  source_graphs:                   GraphBinding[]
  // Which of the seven graphs (Volume VIII, Ch.7) this Twin
  // draws from, and which specific entity_ids within each
  // graph it is scoped to — a Twin NEVER draws from data
  // outside its represented_entity_id's own graph neighbourhood,
  // see Part III for the precise scoping rule per Twin type

  // ── SYNTHESIS STATE ───────────────────────────────────────
  synthesis: {
    last_synthesised_at:        Timestamptz
    synthesis_method_version:      TEXT
    // Per Volume VIII, Ch.20's computation_method versioning
    // discipline, applied here
    embedding:                       Vector(1536)
    // The Twin's own composite embedding, distinct from but
    // derived from its represented entity's embedding
    // (Volume II, Ch.16) — see Chapter 6
  }

  // ── PROVENANCE ────────────────────────────────────────────
  provenance_record_id:             UUID
  // Per Volume VIII, Ch.20 — see Part VI, this volume's
  // strictest requirement

  // ── LEGACY ────────────────────────────────────────────────
  legacy_binding:                      TwinLegacyBinding
  // See Part VII — links to Volume III, Ch.23's ai_twin_policy

  // ── AUDIT ─────────────────────────────────────────────────
  created_at:                            Timestamptz
  updated_at:                               Timestamptz
  version:                                    Integer
}
```

---

# Part II — Consent and Ownership

---

## Chapter 3 — Who Controls a Digital Representation of an Entity

This is Volume VIII's second required resolution. The governing rule is stated once, plainly, and applies without exception across all six Twin types: **a Twin is owned by the entity it represents, never by ZUKA, and never by any party other than that entity or its properly authorised steward.**

```typescript
TwinConsentRecord {
  twin_id:                  UUID

  consenting_entity_id:        UUID
  // For a Personal Twin: the Person themselves, via their
  // own Identity (Volume III) — never a guardian, except
  // per the Child Identity carve-out in Chapter 4
  // For a Family Twin: requires consent per Volume VI,
  // Ch.16's strategic_layer decision model — a Family Twin
  // is too consequential a representation to be instantiated
  // by a single family member acting alone
  // For a Community Twin: requires consent per the
  // Community's own governance_change_policy (Volume VI,
  // Ch.16) — treated as a governance-layer decision, not an
  // operational one
  // For a Gathering Twin: the Gathering's organiser/host
  // (Volume IV, Ch.4's host field)
  // For an Opportunity Twin: BOTH parties to the relevant
  // Opportunity Graph relationship (Volume VIII, Ch.12) where
  // applicable — see Chapter 4's specific note
  // For a City Twin: no individual consent model applies —
  // see Chapter 4's specific note on aggregate-only Twins

  consent_given_at:               Timestamptz
  consent_scope:                     TwinConsentScope
  // Exactly what the Twin is permitted to do — see the
  // granular scope structure below; consent is never
  // blanket or implicit

  consent_scope_detail: {
    permitted_source_graphs:          GraphType[]
    // Which of the seven graphs the Twin may draw from —
    // a Person may consent to a Personal Twin drawing from
    // their Identity and Memory graphs while explicitly
    // excluding their Trust graph, for instance
    permitted_visibility:                TwinVisibilityLevel
    // 'self_only' | 'family' | 'community' | 'public'
    // Mirrors Volume III, Part VI's visibility levels exactly —
    // no new privacy taxonomy is introduced here
    permitted_outputs:                      TwinOutputPermission[]
    // 'insight_display' | 'recommendation_generation' |
    // 'third_party_query_response' | 'b2b_aggregate_inclusion'
    // — each independently grantable and revocable

  }

  revocable:                           true
  // ALWAYS true, without exception, for every Twin type —
  // see Chapter 5

  revoked_at:                              Timestamptz
  revocation_consequence:                     RevocationConsequence
  // See Chapter 5
}
```

---

## Chapter 4 — Type-Specific Consent Carve-Outs

Two Twin types do not fit the standard single-entity consent model cleanly, and both are addressed explicitly here rather than left ambiguous.

```
CHILD IDENTITY CARVE-OUT (Personal Twin)
  A Child entity (Volume II §3.1; Volume III, Ch.11's Parent
  Persona) cannot independently consent to a Personal Twin.
  A Guardian may consent ON BEHALF of a Child's Personal Twin,
  but ONLY at the most restrictive consent_scope available
  (self_only visibility, insight_display output only, no
  recommendation_generation, no third_party_query_response,
  no b2b_aggregate_inclusion) — and that Twin is STRUCTURALLY
  REQUIRED to re-request full consent directly from the
  person themselves upon their transition from Child to
  Person entity (Volume III, Ch.11), at which point the
  Guardian's prior consent is automatically suspended pending
  the now-adult's own decision. A Guardian-consented Child
  Twin that is never re-confirmed by the adult it becomes
  is automatically decommissioned (Part IV) after a defined
  grace period.

OPPORTUNITY TWIN — DUAL CONSENT
  Per Chapter 3's note above: an Opportunity Twin, because it
  is by definition a representation of a RELATIONSHIP or
  emerging pattern between two or more entities (Volume VII,
  Ch.20; Volume VIII, Ch.12), requires consent from every
  party the Twin's scope touches. If any one party revokes
  consent (Chapter 5), the Twin does not merely lose that
  party's data — it is decommissioned entirely, because an
  Opportunity Twin representing only one side of a two-sided
  emerging relationship is not a faithful representation of
  anything and must not be allowed to persist in a
  half-consented state.

CITY TWIN — AGGREGATE-ONLY, NO INDIVIDUAL CONSENT MODEL
  A City Twin (Part III, Chapter 9) draws exclusively from
  aggregated, anonymised data already governed by the
  k-anonymity and differential privacy rules established for
  B2B data products (referenced throughout this Bible's
  data-governance sections). No individual person, Family,
  or Community consents to inclusion in a City Twin any more
  than they individually consent to inclusion in a B2B Pulse
  Report — the existing aggregate-data consent and opt-out
  mechanisms (Volume III, Part VI's data_for_b2b_products
  flag) govern City Twin inclusion identically, and no
  separate Twin-specific consent flow is introduced for this
  type alone.
```

---

## Chapter 5 — Revocation Is Always Available, Always Honoured Immediately

```
Per Chapter 3: consent is ALWAYS revocable, without exception,
for every Twin type that has an individual or governance-body
consenting_entity_id (i.e., every type except City Twins,
governed per Chapter 4's aggregate model instead).

ON REVOCATION:

  Immediate effect:
    The Twin's status (Chapter 2) transitions to
    'decommissioned' within the same processing cycle the
    revocation is received — there is no grace period for
    continued operation after revocation, unlike the
    Memorialisation grace periods elsewhere in this Bible,
    because continuing to operate a representation of someone
    against their express withdrawal of consent is categorically
    different from preserving a Memory record.

  Data handling:
    The Twin's synthesis state (Chapter 2) and embedding are
    deleted, not merely deactivated — they are DERIVED data,
    and per the Memory ≠ Media principle's broader logic
    (Volume V, Ch.4), derived synthesis can be safely
    regenerated later if consent is re-granted, because the
    SOURCE data in the seven graphs (which the Twin draws
    from but does not own) remains intact per each graph's
    own retention rules.

  Provenance trail:
    The ProvenanceRecord (Part VI) of every output the Twin
    ever generated is RETAINED even after the Twin itself is
    decommissioned, specifically so that any past recommendation
    or insight the Twin produced remains independently
    auditable — revocation withdraws the Twin's right to exist
    going forward, it does not erase the historical fact that
    it existed and produced specific, accountable outputs.
```

---

# Part III — The Six Twin Types

---

## Chapter 6 — Personal Twin

```
REPRESENTS:           A single Person entity (Volume II §3.1)

SOURCE GRAPH SCOPE:      Identity Graph (Volume VIII, Ch.8) —
                         full, subject to consent scope
                       Memory Graph (Ch.10) — full, subject to
                         consent scope
                       Trust Graph (Ch.11) — the person's OWN
                         trust profile only (as truster AND as
                         trusted), never another person's
                       Opportunity Graph (Ch.12) — opportunities
                         where this person is a named party
                       Community Graph (Ch.9) — this person's
                         memberships and roles only, never
                         another member's data
                       Tradition Graph (Ch.13) — traditions
                         this person participates in
                       Digital Presence Graph (Ch.14) — EXCLUDED
                         by default; requires explicit, separate
                         opt-in beyond standard consent_scope,
                         given Volume VIII, Ch.14's privacy-review
                         gate on this graph generally

GOVERNING HISTORIAN
DEPENDENCY:               Draws conceptually on the same
                         underlying data as the Personal
                         Historian (Volume VII, Ch.25) but is
                         architecturally distinct per Chapter 1 —
                         the Historian answers questions on
                         demand; the Personal Twin is a standing
                         synthesis between questions

WHAT IT IS FOR (at DRL 5,
the ceiling — see Part IV):  Helping a person understand their
                         OWN accumulated gathering history,
                         relationships, and trust standing —
                         "tell me my story," reflectively,
                         never predictively about OTHER people

WHAT IT IS EXPLICITLY
NOT FOR, AT ANY DRL:        Generating insight ABOUT a person
                         for consumption by anyone other than
                         that person or their explicitly
                         consented recipients (Chapter 3's
                         permitted_visibility) — this is the
                         direct extension of Volume V, Ch.23's
                         "purpose is understanding, never
                         surveillance" to the Twin architecture,
                         and it is the single most important
                         constraint on this Twin type
```

---

## Chapter 7 — Family Twin

```
REPRESENTS:           A single Family entity (Volume II §3.1)

SOURCE GRAPH SCOPE:      Memory Graph — the Family Vault
                         (Volume V, Ch.16) specifically, not
                         individual members' personal Memory
                         Vaults beyond what they have
                         contributed TO the Family Vault
                       Identity Graph — family relationship
                         structure (Volume II, Ch.5's family
                         relationship types) and Life Chapter
                         data only where individual members
                         have separately consented (a Family
                         Twin cannot pull a member's full
                         Personal Identity Graph merely because
                         that member belongs to the family)
                       Community Graph — the Family's own
                         standing as a Community entity
                         (Volume VI, Ch.4's confirmed
                         Family-as-Community structural status)
                       Tradition Graph — Family Traditions
                         (Volume IV, Ch.11; Volume VI, Ch.10's
                         Christmas Gathering example) specifically

CONSENT MODEL:            Per Chapter 3 — strategic_layer
                         decision (Volume VI, Ch.16), not a
                         single family member's unilateral
                         choice — this is the most consequential
                         consent-gating decision in this volume,
                         given how emotionally significant Family
                         Vault content is (Volume V, Ch.16)

EXPANDED FROM SOURCE
BRIEF, JUSTIFICATION:      The skeleton brief notes "Family"
                         twin without further detail; this
                         volume expands it specifically as the
                         Family-entity equivalent of the Personal
                         Twin, drawing the explicit boundary that
                         a Family Twin is NOT simply the union of
                         its members' Personal Twins — it is its
                         own distinct synthesis scoped to
                         family-level data, consistent with
                         Volume VI, Ch.4's distinction between
                         Community-level and individual-level
                         data ownership

WHAT IT IS FOR (at the
DRL ceiling):              Helping a family understand its own
                         accumulated traditions, generational
                         patterns, and Family Vault richness —
                         the direct Twin-form expression of the
                         Family Historian (Volume V, Ch.23)
```

---

## Chapter 8 — Community Twin

```
REPRESENTS:           A single Community entity (Volume VI's
                         full taxonomy — Family, Faith,
                         Educational, Geographic, Professional,
                         Interest, Cultural, Event communities
                         all eligible, identically governed)

SOURCE GRAPH SCOPE:      Community Graph (Volume VIII, Ch.9) —
                         the Community's own structure,
                         federation relationships (Volume VI,
                         Part XIX), and membership AGGREGATE
                         data (never individual members'
                         personal data beyond what they have
                         contributed to community-owned
                         artifacts)
                       Memory Graph — the Community Archive
                         (Volume V, Ch.17) specifically
                       Tradition Graph — Community Traditions
                         (Volume VI, Part X)
                       Trust Graph — the Community's OWN
                         CommunityTrustProfile (Volume VI,
                         Part XI), not individual members'
                         personal trust profiles

CONSENT MODEL:            Per Chapter 3 — the Community's own
                         governance_change_policy (Volume VI,
                         Ch.16), treated as a governance-layer
                         decision

RELATIONSHIP TO THE
COMMUNITY GENOME
(Volume VIII, Ch.26):       The Community Twin is the natural,
                         eventual consumer of the Community
                         Genome composite view once that
                         Dormant Feature reaches sufficient DRL —
                         this volume does not build the Genome,
                         it confirms the Community Twin as the
                         Genome's primary future application,
                         consistent with Volume VIII's own
                         framing of the Genome as "used to
                         understand communities"

WHAT IT IS FOR (at the
DRL ceiling):              Helping community leadership
                         understand the community's own growth,
                         trust trajectory, and tradition health —
                         the direct Twin-form expression of the
                         Community Historian (Volume VII, Ch.25;
                         Volume VI, Part XVIII's Church/Alumni/
                         Club Copilots' eventual underlying
                         representation)
```

---

## Chapter 9 — Gathering Twin

```
REPRESENTS:           A single Gathering entity (Volume IV),
                         OR — distinctly — a recurring
                         Gathering series already recognised
                         as a Tradition (Volume VII, Part VIII) —
                         see the scoping note below

SOURCE GRAPH SCOPE:      Memory Graph — Memories specifically
                         tagged to this Gathering (Volume V,
                         Ch.5's gathering_id field)
                       Tradition Graph — if this Gathering is
                         part of a recognised Tradition, the
                         Twin may draw on the Tradition's full
                         occurrence history (Volume VIII, Ch.13)
                       Community Graph — the Gathering's
                         primary_community_id and
                         invited_community_ids (Volume IV, Ch.4)
                       Opportunity Graph — opportunities that
                         emerged from this specific Gathering
                         (Volume VII, Ch.20's OpportunityEmerged
                         signal, scoped to this gathering_id)

SCOPING NOTE — SINGLE
OCCURRENCE VS. SERIES:      A Gathering Twin for a single,
                         one-off gathering (a birthday party,
                         a one-time conference) is necessarily
                         a much thinner representation than a
                         Gathering Twin for a Tradition's full
                         multi-year occurrence series — both
                         are structurally the SAME Twin type,
                         but the single-occurrence case will,
                         in practice, rarely justify advancing
                         past DRL 1-2 (Part IV), since a single
                         gathering simply does not generate
                         enough data for a richer synthesis to
                         be honest. This is not a special rule;
                         it is the DRL framework working exactly
                         as intended — thin data produces a thin
                         Twin, by design, not by exception.

CONSENT MODEL:            Per Chapter 3 — the Gathering's host/
                         organiser (Volume IV, Ch.4's host field)

WHAT IT IS FOR (at the
DRL ceiling, Tradition-
series case specifically):  Helping an organiser or community
                         understand a recurring gathering's own
                         evolution — attendance trends, ritual
                         element consistency (Volume IV, Ch.11),
                         and tradition strength trajectory — the
                         direct Twin-form expression of the
                         Tradition Historian named in Volume X's
                         Agent registry, operating at the level
                         of a single named Tradition rather than
                         the Tradition Graph as a whole
```

---

## Chapter 10 — Opportunity Twin

```
REPRESENTS:           A single emerging or formalised
                         Opportunity (Volume II §3.10's Job,
                         Sponsorship, Booking, Collaboration
                         entities, OR a pre-formal
                         OpportunityEmerged pattern per
                         Volume VII, Ch.20)

SOURCE GRAPH SCOPE:      Opportunity Graph (Volume VIII, Ch.12) —
                         this specific opportunity's evidence
                         path
                       Trust Graph — the trust relationship(s)
                         between the parties this opportunity
                         connects (Volume VIII, Ch.11's
                         TrustEdge), since Volume VII, Ch.20
                         already established that opportunities
                         emerge FROM accumulated trust
                       Community Graph — the shared community
                         context the opportunity emerged through,
                         where applicable (Volume VI, Part XVI)

CONSENT MODEL:            Per Chapter 4's dual-consent carve-out —
                         the strictest consent model of any Twin
                         type in this volume, and the one most
                         likely to be decommissioned by a single
                         party's revocation

ADDITIONAL CONSTRAINT,
INHERITED FROM VOLUME
VIII, CH.22:                Per Volume VIII's stricter provenance
                         bar for Opportunity Intelligence
                         specifically: an Opportunity Twin's
                         outputs are held to the SAME stricter
                         bar — no recommendation_generation
                         output (Chapter 3) from an Opportunity
                         Twin may be surfaced without a complete
                         ProvenanceRecord, with NO exception even
                         at DRL 5, given the direct relationship
                         and reputational cost a wrong Opportunity
                         recommendation carries to real people

WHAT IT IS FOR (at the
DRL ceiling):              Helping two consenting parties
                         understand and consider formalising an
                         emerging collaborative relationship that
                         the graph has detected forming between
                         them — never initiating contact on its
                         own, never surfaced to either party
                         without their own prior consent already
                         on file
```

---

## Chapter 11 — City Twin

```
REPRESENTS:           A single City entity (Volume II §3.4)

SOURCE GRAPH SCOPE:      ALL SEVEN GRAPHS, but EXCLUSIVELY at
                         the aggregate, anonymised level already
                         governed by this Bible's existing
                         k-anonity/differential-privacy B2B data
                         rules — the City Twin draws no
                         individually-identifiable data from any
                         graph under any circumstance, per
                         Chapter 4's aggregate-only consent model

CONSENT MODEL:            None at the individual level (Chapter 4) —
                         governed entirely by the existing
                         aggregate B2B data consent and opt-out
                         mechanisms already specified elsewhere
                         in this Bible

RELATIONSHIP TO THE
CITY HISTORIAN
(Volume VII, Ch.25):        The City Twin is the standing,
                         continuously-synthesised counterpart to
                         the City Historian's on-demand query
                         interface — per Chapter 1's general
                         Twin/Historian distinction, applied here
                         specifically. The City Historian answers
                         "show me this city's cultural moments
                         over the past decade" when asked; the
                         City Twin IS the continuously-maintained
                         representation that makes such a query
                         fast and current rather than requiring
                         a full graph traversal each time.

RELATIONSHIP TO B2B
PRODUCTS:                   Per Volume VII, Ch.25's explicit
                         carve-out (the City Historian is the
                         ONE Historian permitted for B2B/aggregate
                         use, unlike the other six), the City
                         Twin inherits this SAME single carve-out
                         and no other Twin type may be used this
                         way — a City Twin's aggregate insights
                         MAY feed B2B Pulse Reports and Audience
                         Intelligence products (per ZUKA.md's
                         existing B2B Product Suite); NO other
                         Twin type's outputs may ever be used
                         this way, full stop, regardless of
                         consent_scope settings, because the
                         other five Twin types represent
                         identifiable individuals, families,
                         communities, gatherings, or two-party
                         relationships, and Volume V, Ch.23's
                         "no Historian output is ever an input to
                         advertising" absolute boundary applies
                         identically and without exception to
                         every Twin type built on top of those
                         Historians' underlying data
```

---

# Part IV — DRL Gating

---

## Chapter 12 — The Ladder, As Specified, Fully Operationalised

The source brief states the gating ladder in one line: *"DRL0-2 history only, DRL3 insights, DRL4 recommendations, DRL5 full copilots."* This chapter operationalises that line precisely, mapping it onto Volume VII's existing six-level DRL scale (0-5) without alteration, and onto the specific output permissions already established in Chapter 3's `TwinOutputPermission` enum.

```
DRL 0 — NO DATA
  Twin status: 'seed' only. No synthesis has occurred.
  No output of any kind is possible. This is the
  default and starting state for every Twin of every
  type for every newly-created represented_entity_id.

DRL 1 — DATA COLLECTION STARTED
  Twin status: 'seed', continuing.
  Permitted output: NONE.
  The Twin's source_graphs (Chapter 2) are accumulating
  data per Volume VII, Ch.22-23's Feature Seed discipline,
  but no synthesis is performed and no output is generated.
  This and DRL 0 together constitute the "history only"
  floor named in the source brief — at these two levels,
  the Twin is, structurally, nothing more than an
  accumulating data scope, not yet a representation.

DRL 2 — PATTERNS VISIBLE
  Twin status: transitions from 'seed' to 'active' at this
  threshold, but with the narrowest possible output scope.
  Permitted output: insight_display ONLY, and ONLY of the
  most basic, directly-observable kind — e.g., a Personal
  Twin may display "you have attended 47 gatherings" but
  may NOT yet synthesise "you tend to attend music events
  with the same group of friends" (that synthesis requires
  DRL 3). This completes the "history only" floor — DRL 2
  permits surfacing raw accumulated history, not yet
  interpreted patterns.

DRL 3 — USEFUL INSIGHTS POSSIBLE
  Permitted output: insight_display, fully enabled —
  genuine synthesised insight (Volume VIII, Part III's
  Semantic Layer) becomes available, always carrying a
  full ProvenanceRecord (Part VI) per Volume VIII, Ch.20's
  unconditional requirement. This is the level named
  "insights" in the source brief's gating ladder.

DRL 4 — AUTOMATION POSSIBLE
  Permitted output: insight_display + recommendation_
  generation. The Twin may now actively suggest something
  ("would you like to revisit this tradition," "this
  emerging collaboration may be worth formalising" for an
  Opportunity Twin specifically) rather than merely
  displaying observed patterns. This is the level named
  "recommendations" in the source brief.

DRL 5 — AI-GRADE INTELLIGENCE POSSIBLE
  Permitted output: ALL FOUR TwinOutputPermission types,
  including third_party_query_response (the Twin may
  respond to queries from consented third parties, e.g.
  a Community Twin answering a prospective member's
  question about the community) and, where the Twin type
  permits per Part III's individual scoping rules,
  b2b_aggregate_inclusion (City Twin only, per Chapter 11's
  exclusive carve-out). This is the level named "full
  copilots" in the source brief — at DRL 5, a Twin's
  capability ceiling converges with, but remains
  architecturally distinct from (Chapter 1), a domain-
  specific Copilot.
```

```typescript
// The enforcement mechanism — every Twin output request
// is checked against this gate before generation, not
// merely before display, since even COMPUTING a
// recommendation-level synthesis at DRL 3 would itself
// be operating "above the maturity of the supporting data"
// per this volume's governing principle (Chapter 13)

function authoriseTwinOutput(
  twin: Twin,
  requestedOutput: TwinOutputPermission
): AuthorisationResult {

  const ladder: Record<TwinOutputPermission, number> = {
    'insight_display':           2,   // minimum DRL required
    'recommendation_generation': 4,
    'third_party_query_response': 5,
    'b2b_aggregate_inclusion':    5,  // AND twin_type === 'city'
  };

  if (twin.current_drl < ladder[requestedOutput]) {
    return { authorised: false, reason: 'INSUFFICIENT_DRL' };
  }

  if (!twin.consent_record.consent_scope_detail
        .permitted_outputs.includes(requestedOutput)) {
    return { authorised: false, reason: 'CONSENT_NOT_GRANTED' };
  }

  if (requestedOutput === 'b2b_aggregate_inclusion'
      && twin.twin_type !== 'city') {
    return { authorised: false, reason: 'TWIN_TYPE_INELIGIBLE' };
    // Hard-coded, non-overridable per Chapter 11's exclusive
    // carve-out — this check exists even though, in practice,
    // a non-city Twin's consent_scope_detail should never
    // grant this permission in the first place; the
    // double-check is intentional defence in depth
  }

  return { authorised: true };
}
```

---

## Chapter 13 — The Governing Principle, Held Without Exception

```
No agent may operate above the maturity of the supporting data.
```

This sentence is reproduced here verbatim from this volume's own source material because it is, in fact, the precise statement of Volume IX's discipline too, not only Volume X's — the source documents stated it under the Agent volume's heading, but it governs Twins identically, and this volume adopts it explicitly as its own. The two volumes share one principle because Twins and Agents (Volume X) are two expressions of the same underlying constraint: synthesis capability is never permitted to outrun evidence.

The practical, non-negotiable consequence for this volume specifically: **a Twin's `current_drl` value is never set optimistically.** It is computed, per Volume VII, Part XII's `DataReadinessAssessment` structure, from actual measured signals (volume, density, temporal span, confidence) — never from a roadmap date, a stakeholder's enthusiasm, or a competitive pressure to ship something resembling AI sooner. A Personal Twin for a user who joined yesterday is, and must remain, at DRL 0, regardless of how complete that user's profile fields are, because profile completeness is not the same as accumulated graph evidence.

---

# Part V — Synthesis Mechanics

---

## Chapter 14 — How a Twin's Standing Representation Is Actually Computed

This chapter exists to make Chapter 1's Twin/Historian distinction concrete rather than purely conceptual: what, technically, does it mean for a Twin to be a "standing representation between queries" rather than a fresh computation each time?

```
SYNTHESIS CYCLE:

  1. TRIGGER
     A Twin's synthesis is re-run when:
       - A new DomainEvent (Volume II, Ch.8-9; Volume VII,
         Ch.8) occurs within any of its source_graphs scope
       - A scheduled re-synthesis interval elapses (frequency
         scales inversely with current_drl — a DRL 1 Twin's
         synthesis is recomputed far less often than a DRL 4
         Twin's, since low-DRL Twins have little to gain from
         frequent recomputation and high-DRL Twins' outputs
         are more time-sensitive)
       - The Twin's current_drl crosses a threshold (Chapter 12),
         triggering immediate re-synthesis to reflect newly
         unlocked output permissions

  2. GRAPH TRAVERSAL
     The synthesis process queries each graph in source_graphs
     (Chapter 2) bound by consent_scope_detail.permitted_
     source_graphs (Chapter 3) — never beyond that explicit
     scope, regardless of what data technically exists and is
     technically reachable

  3. SEMANTIC ENRICHMENT
     Where the Semantic Enrichment Engine (Volume VIII, Ch.6)
     has itself reached sufficient DRL to be active, its
     output feeds the Twin's synthesis; where it has not, the
     Twin's synthesis is correspondingly thinner — a Twin's
     own DRL ceiling can never exceed what its UNDERLYING
     Dormant Features (Volume VIII, Part X's register) have
     themselves earned

  4. EMBEDDING UPDATE
     The Twin's own embedding (Chapter 2) is recomputed,
     versioned per Volume II, Ch.16's embedding versioning
     discipline, distinct from but derived from the
     represented_entity_id's own entity-level embedding

  5. PROVENANCE GENERATION
     A new ProvenanceRecord (Part VI) is generated for this
     synthesis cycle, superseding (not deleting — per Volume
     VII's Temporal Truth principle) the prior cycle's record

  6. PERSISTENCE
     The Twin's synthesis state is written, its
     last_synthesised_at timestamp updated, and its version
     counter incremented (Chapter 2) — consistent with every
     other versioned entity in this Bible
```

---

# Part VI — Provenance and Explainability

---

## Chapter 15 — Inheriting Volume VIII's Strictest Requirement, Without Modification

This is Volume VIII's fifth required resolution for this volume: whether and how a Twin's outputs inherit the `ProvenanceRecord` requirement.

**The answer is unconditional and requires no new mechanism.** Every Twin output — at every DRL level from 2 upward (DRL 0-1 produce no output at all, per Chapter 12) — is itself a `ConclusionType` per Volume VIII, Chapter 20's existing `ProvenanceRecord` structure, and is governed by it identically to every other conclusion type already named in that volume (trust scores, tradition recognitions, recommendations, reputation tags).

```typescript
// Extending Volume VIII, Ch.20's ConclusionType enum —
// the only change this volume makes to that structure

ConclusionType enum (extended):
  'trust_score'
  'tradition_recognition'
  'recommendation'
  'reputation_tag'
  'twin_insight'              // NEW — this volume
  'twin_recommendation'          // NEW — this volume
  'twin_third_party_response'        // NEW — this volume
```

No other modification to Volume VIII's `ProvenanceRecord` structure is required. A Twin's `evidence_events` field points to the exact DomainEvents its synthesis drew from (Chapter 14, Step 2's bounded graph traversal makes this fully enumerable, never approximate). A Twin's `evidence_summary` is generated FROM those events, never authored independently, per Volume VIII, Ch.20's existing prohibition. A Twin's `computation_method` records the synthesis method version from Chapter 14, Step 4.

**Why this matters more for Twins than for any other conclusion type named in Volume VIII:** a Twin is, by design, the single richest synthesis point in the entire architecture — it draws on more graphs, more data, more inference than a single trust score or a single recommendation does in isolation. This is precisely why Volume VIII, Chapter 20's principle ("Knowledge must be explainable") was written as a Bible-wide constitutional commitment rather than a narrow rule — it was always going to be tested hardest by exactly this volume, and it holds, without modification, because it was built correctly the first time.

---

# Part VII — Legacy and Succession

---

## Chapter 16 — Full Specification of `ai_twin_policy`

This is Volume VIII's sixth and final required resolution: extending Volume III, Chapter 23's `ai_twin_policy` field — already anticipated there as part of `LegacySettings`, but left unspecified pending this volume — into a complete structure.

```typescript
// Full specification, superseding the placeholder
// referenced in Volume III, Chapter 23

TwinLegacyBinding {
  twin_id:                      UUID
  represented_entity_id:           UUID

  // ── FOR PERSONAL TWINS SPECIFICALLY ──────────────────────
  // (Family, Community, Gathering, and Opportunity Twins
  //  follow their OWNING entity's own succession model —
  //  Volume VI, Ch.25 for Communities; Volume III, Ch.25 for
  //  Families — rather than a Twin-specific legacy model;
  //  City Twins have no legacy/succession concept at all,
  //  consistent with Ch.11's aggregate-only nature)

  twin_active_at_death:               Boolean
  // Was this Personal Twin at DRL 2+ (i.e., actually
  // producing output) at the time of the person's
  // Memorialisation (Volume III, Ch.24)?

  posthumous_access:                     PosthumousAccessPolicy
  // 'immediate_on_death' — decommissioned the moment
  //   Memorialisation begins, no exceptions, full stop
  // 'steward_controlled' — the Legacy Steward (Volume III,
  //   Ch.23's LegacySteward) may, per their granted
  //   steward_permissions, view the Twin's LAST synthesised
  //   state (frozen, per the rule below) but the Twin itself
  //   does not continue synthesising or generating new output
  // 'preserve_frozen' — the Twin's final synthesis state is
  //   preserved indefinitely as a Memory-equivalent artifact
  //   (linked into the Family Vault per Volume V, Ch.16, where
  //   applicable) but is permanently moved out of 'active'
  //   status into a new terminal state, 'memorialised'
  //   (Chapter 2), and NEVER resumes synthesis, regardless
  //   of any future event

  default_policy:                           'immediate_on_death'
  // Stated explicitly as the DEFAULT, requiring affirmative
  // opt-in to either of the other two options while the
  // person is living — consistent with this Bible's general
  // pattern of defaulting to the most protective, most
  // conservative option and requiring explicit choice to
  // expand from there (mirroring Volume III, Ch.23's own
  // memory_policy defaults)

  steward_id:                                  UUID
  // Inherited directly from the represented Person's
  // Legacy Settings (Volume III, Ch.23) — NOT a separate
  // Twin-specific steward designation; a Twin cannot have
  // a different steward than the rest of the person's
  // Legacy Settings govern

  rationale_for_default: {
    // Documented explicitly here because this is a
    // consequential, easily-second-guessed default
    text: "A Personal Twin, even at a thin DRL 2-3 level,
    is a more complete and more actively-synthesising
    representation of a living person than any single
    Memory or Historian query. Continuing to operate it
    — even in a read-only, steward-controlled mode —
    immediately upon a person's death, without their own
    prior explicit choice to permit this, would mean ZUKA
    deciding on their behalf to continue representing them
    past the one boundary this Bible has treated as most
    inviolable throughout: that a Twin's existence is
    something only the represented entity (or, for Children,
    their Guardian until the Child's own adult re-confirmation,
    per Chapter 4) gets to authorise. Death is the one
    moment a person can no longer make or revise that choice
    themselves, which is exactly why the architecture must
    default to the most protective option rather than the
    most feature-complete one."
  }
}
```

---

## Chapter 17 — Why Family, Community, Gathering, and Opportunity Twins Do Not Get a Separate Legacy Model

This is worth stating explicitly rather than leaving as a parenthetical, because it might otherwise look like an oversight.

```
A Family Twin's continuity is governed entirely by the
Family entity's OWN succession model (Volume III, Ch.25;
Volume VI's Family-as-Community structural status) — when
the Family Vault's stewardship transfers, the Family Twin
(if active) transfers its consent authority identically,
with no separate Twin-specific succession event required.

A Community Twin follows the exact same logic against
Volume VI, Ch.24-25's Community Succession model — leadership,
archive, tradition, and governance succession are already
explicitly decoupled there (Volume VI, Ch.25), and a Community
Twin's consent authority is simply one more thing that moves
with governance succession specifically, requiring no new
mechanism.

A Gathering Twin representing a single occurrence has no
meaningful "succession" question at all — the Gathering ended,
and per Chapter 9's scoping note, single-occurrence Gathering
Twins rarely advance past DRL 1-2 in any case. A Gathering
Twin representing a Tradition's full series follows the
Tradition's OWNING Community's succession model, per Chapter 8.

An Opportunity Twin, given Chapter 4's dual-consent and
single-party-revocation-decommissions-the-whole-Twin rule,
has no succession question either — if one of the consenting
parties dies, this is functionally identical to a unilateral
consent revocation (Chapter 5), and the Twin is decommissioned,
full stop, with no steward-continuation option ever available
for this Twin type specifically, since an Opportunity Twin
inherently represents a relationship between two living,
choosing parties and cannot meaningfully persist with only
one side of that relationship still able to consent.

The only Twin type requiring its OWN dedicated legacy
specification, distinct from an underlying entity's existing
succession model, is therefore the Personal Twin — because a
Person, unlike a Family, Community, Gathering, or Opportunity,
has no "successor" in the structural sense those other entities
do. This is precisely why Chapter 16 exists as a standalone
specification and the other four types do not require one.
```

---

# Part VIII — MVP Scope

---

## Chapter 18 — What to Build First

Consistent with every prior volume's discipline, and made unusually simple by this volume's own DRL ladder (Chapter 12): **at MVP, every Twin of every type sits at DRL 0 or DRL 1, by definition, because no domain anywhere in this Bible has yet accumulated the temporal span and density Volume VII, Part XII's assessment requires for DRL 2.** This is not a pessimistic assumption — it is the direct, honest consequence of this being a new platform.

```
BUILD (MVP):

  ✅ The Twin entity schema (Ch.2), for all six types,
     added to the database from the first migration that
     introduces this volume's concepts — consistent with
     every prior volume's "structural fields are cheap now,
     expensive to retrofit later" discipline
  ✅ TwinConsentRecord structure (Ch.3-5), fully built and
     enforced, even though no Twin will reach DRL 2 (the
     first level requiring any output, per Ch.12) for some
     time — consent infrastructure must exist and be tested
     BEFORE the first Twin reaches a DRL level where consent
     actually gates a real output, not built reactively once
     that day arrives
  ✅ authoriseTwinOutput() enforcement function (Ch.12),
     built and unit-tested against all six TwinOutputPermission
     × DRL combinations, even though it will return
     'INSUFFICIENT_DRL' for every real Twin in the system
     at MVP — this is intentional; the gate must be proven
     correct before it is ever truly load-bearing
```

```
CAPTURE (data foundation, not yet surfaced as features):

  📊 Every twin_type's source_graphs accumulate exactly the
     same DomainEvents already specified as MVP-captured per
     Volumes II through VIII — this volume introduces NO new
     capture burden whatsoever; it is purely an interpretation
     and synthesis layer over data collection already mandated
     elsewhere
```

```
DO NOT BUILD YET:

  ❌ Any actual synthesis cycle (Ch.14) — there is nothing
      to synthesise yet, by DRL definition
  ❌ Any Twin output of any kind, for any type, at any DRL —
      MVP-stage Twins exist purely as 'seed' status records
      accumulating consent and graph bindings
  ❌ The TwinLegacyBinding mechanics (Ch.16) beyond the bare
      schema field existing — no Personal Twin will be
      anywhere near DRL 2 (the threshold at which Ch.16's
      twin_active_at_death question becomes meaningful) for
      long enough that this is safely deferred in full
  ❌ Any product-facing UI mentioning "AI Twin" in any form —
      per this volume's own discipline, a feature that exists
      only at 'seed' status with zero possible output has no
      legitimate user-facing surface yet, and presenting one
      anyway would itself violate the spirit, if not yet the
      letter, of Chapter 13's governing principle
```

**The single most important MVP discipline in this volume:** build the consent and authorisation infrastructure completely and correctly now, against zero real load, specifically so that the first time any Twin genuinely reaches DRL 2 — months or years from now, whenever the actual data earns it — the gate is already proven, tested, and trustworthy, rather than being rushed into existence under the pressure of "the data is ready, now we need consent infrastructure fast." This inverts the usual build-pressure pattern deliberately: the highest-stakes infrastructure in this volume is built with the LEAST time pressure, while the time pressure is still low.

---

# Volume IX Summary

```
What a Twin Actually Is:
  A persistent, structured, consent-gated, provenance-bound
  standing representation of a specific entity — distinguished
  precisely from a Historian (query interface), a Copilot
  (task assistant), and a chatbot (stateless interface)

The Canonical Twin Record:
  Full schema spanning identity, status, DRL, consent,
  source graph binding, synthesis state, provenance, and
  legacy — shared structurally across all six types

Consent and Ownership:
  A Twin is owned by the entity it represents, never by ZUKA
  Always revocable, immediate effect, derived data deleted on
  revocation, provenance trail retained regardless
  Two explicit carve-outs: Child Identity (guardian-consented,
  most restrictive scope, re-confirmation required at majority)
  and Opportunity Twin (dual consent, single-party revocation
  decommissions the whole Twin)
  City Twin: aggregate-only, no individual consent model,
  governed by existing B2B data consent mechanisms

Six Twin Types, Each Fully Scoped:
  Personal — individual's own data, Digital Presence Graph
    excluded by default
  Family — Family Vault and family-level data, NOT the union
    of members' Personal Twins, strategic_layer consent
  Community — community-owned data and aggregate membership,
    governance-layer consent
  Gathering — single occurrence (thin, rarely past DRL 1-2)
    or Tradition series (richer), host/organiser consent
  Opportunity — strictest consent model, dual-party,
    inherits Volume VIII's stricter Opportunity provenance bar
    unconditionally
  City — aggregate-only, sole Twin type eligible for B2B use,
    mirroring the City Historian's exclusive carve-out

DRL Gating, Fully Operationalised:
  DRL 0-1: no output, accumulation only ("history only" floor,
    part one)
  DRL 2: raw history display only, no interpreted patterns
    ("history only" floor, part two)
  DRL 3: full insight_display, provenance-backed
  DRL 4: + recommendation_generation
  DRL 5: + third_party_query_response, + b2b_aggregate_inclusion
    (City Twin only, hard-coded exclusive, defence-in-depth
    checked)
  Governing principle, shared verbatim with Volume X: no agent
  may operate above the maturity of the supporting data —
  current_drl always measured, never asserted optimistically

Synthesis Mechanics:
  6-step cycle: trigger, bounded graph traversal, semantic
  enrichment (where available), embedding update, provenance
  generation, persistence
  A Twin's DRL ceiling can never exceed its underlying Dormant
  Features' own earned DRL

Provenance and Explainability:
  Unconditional inheritance of Volume VIII's ProvenanceRecord —
  three new ConclusionTypes added, no structural modification
  required, the principle "every insight must explain why it
  exists" tested hardest here and holding without exception

Legacy and Succession:
  Full TwinLegacyBinding specification for Personal Twins
  specifically — default: immediate decommission on death,
  requiring explicit living opt-in to extend
  Family/Community/Gathering(series)/Opportunity Twins inherit
  their owning entity's existing succession model with no
  separate Twin-specific mechanism; Opportunity Twins have no
  succession option at all, by design

MVP Scope:
  3 build items (schema, consent infrastructure, enforcement
  function — all built against zero real load, deliberately,
  to be proven before they are ever load-bearing)
  1 capture confirmation (no new burden — purely an
  interpretation layer over existing MVP capture)
  4 explicit deferrals, including the explicit prohibition on
  any product-facing "AI Twin" UI surface until a real Twin
  has real output to show
```

A Twin is not a feature ZUKA ships. It is a promise ZUKA keeps to every Person, Family, Community, Gathering, Opportunity, and City it might one day represent: that the representation will be theirs, explainable, revocable, and never built faster than the truth that justifies it.

---

# What Volume X Must Address

Volume X — Distributed Intelligence Architecture, already uploaded alongside this volume's source brief, specifies five Agents (Trust, Opportunity, Community, Historian, Tradition) under the identical governing principle this volume has now fully adopted: no agent may operate above the maturity of the supporting data.

Given this volume's resolutions, Volume X must, at minimum:

1. **Distinguish an Agent from a Twin precisely** — a Twin is a standing representation of a single entity; an Agent, per its name, appears to operate across or between entities — this distinction needs the same rigour Chapter 1 gave Twin/Historian/Copilot/chatbot
2. **Clarify the Historian Agent's relationship to the seven Historians already named across Volumes VII-VIII** — is the Historian Agent a unifying orchestration layer over the existing seven, or a structurally distinct eighth capability
3. **Establish whether Agents consume Twins, consume the seven graphs directly, or both** — given this volume's Twins are themselves graph syntheses, an Agent built on top of Twins would be a synthesis-of-syntheses, which needs explicit provenance handling beyond what Chapter 15 specifies for single-Twin outputs
4. **Define Agent-to-Agent interaction rules**, if any — five named Agents (Trust, Opportunity, Community, Historian, Tradition) inevitably overlap in scope (an Opportunity Agent surely needs Trust Agent input, per Volume VII, Ch.20's trust-precedes-opportunity pattern) and the architecture must specify how that handoff is governed
5. **Apply this volume's MVP discipline identically** — every Agent, like every Twin, should be expected to sit at the equivalent of DRL 0-1 for some time, with the same "do not build yet" honesty

---

> **ZUKA Architecture Bible**
> Volume IX — AI Twin Architecture
> Built in Kampala. Built for Africa. Built for the world.
