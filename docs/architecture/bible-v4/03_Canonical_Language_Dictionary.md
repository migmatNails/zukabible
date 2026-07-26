# ZUKA BIBLE V4 — VOLUME 03
# The Canonical Language Dictionary

> **Part I — The Constitution** · **Authority Level 0**
> Status: NEW — fills the gap named in the Constitutional Reconciliation Audit
> Purpose: One word, one meaning. Terminology drift is architectural drift.

---

## Why This Volume Exists

The Reconciliation Audit named a missing constitutional component: a Canonical Language
Dictionary. Across four source layers, the same concept was sometimes called different
things ("User" vs "Person", "Event" vs "Gathering"), and different concepts sometimes
shared a name. This volume fixes the vocabulary permanently. Every other volume, every
schema, every API, and every line of code must use these terms exactly.

---

## Part A — The Forbidden / Preferred Pairs

These are the substitutions that ADRs make binding:

```
FORBIDDEN          CANONICAL          AUTHORITY    WHY
─────────────────────────────────────────────────────────────────────────
User               Person             ADR-010      A Person is a human;
                                                    "User" reduces them to
                                                    their relationship to software
Participant (as    Person + a role    ADR-010      Participation is an act,
  entity type)                                      not an identity type
Event (community   Gathering          ADR-011      A Gathering generates capital;
  scale)                                            an Event is mere logistics
Profile            Identity           BLACK XXVIII  Identity is a journey;
                                                    a profile is a static record
Media = Memory     Media ≠ Memory     ADR-009      Media is bytes; Memory is meaning
Follower count     Trust / Relationship Vol 11/09  Vanity metric vs earned signal
Trust Score = N    Trust state (human Vol 11        Never expose raw scores;
  (exposed)          readable)                      expose "Trusted Member"
```

Note: "Event" remains the correct term at the **product/MVP level** (the app shows
"Events" to users, per ZUKA.md) AND as a Tier-1 operational entity. "Gathering" is the
**architectural/civilisational** term that carries capital-generation semantics. Both
exist: an Event is the logistical listing; a Gathering is what it becomes when it
generates memories, relationships, and trust. At MVP they are near-synonymous in the UI;
architecturally, Gathering is the richer concept. (Resolved: Volume 13.)

---

## Part B — Core Entity Vocabulary

```
TERM              DEFINITION
─────────────────────────────────────────────────────────────────────────
Person            The canonical human entity. One per verified phone number.
Child             A human under the age of majority; distinct consent rules.
Persona           A contextual presentation of a Person (personal,
                  professional, creator, organiser). A Person has many Personas.
Identity          The continuously evolving representation of a Person across
                  their whole participation history. Not a profile.
Relationship      A first-class entity connecting two entities, with weight,
                  trust, lifecycle, and decay. Never a mere foreign key.
Gathering         A community-scale occasion that generates capital. 40+ types.
Event             The product-level / Tier-1 operational term for a Gathering
                  listing. Synonymous in the UI; Gathering is the richer concept.
Venue             A physical or virtual place where Gatherings occur.
Memory            Preserved MEANING derived from participation. Not media.
Media             The evidence layer — bytes (photo, video, audio). Not meaning.
Community         A persistent network of relationships around shared
                  participation. Eight families. Discovered, not manufactured.
Tradition         A recurring Gathering recognised as a cultural pattern.
                  Discovered, never created.
Trust             Confidence established through observed participation.
                  Six dimensions. Never purchased.
Opportunity       The possibility of value creation emerging from trust,
                  participation, relationships, and communities.
Ticket            A provenance-verified right of entry to a Gathering.
```

---

## Part C — The Capital Vocabulary (ADR-005)

```
Memory Capital        Accumulated preserved meaning
Relationship Capital  Depth and breadth of connections
Trust Capital         Earned confidence across six dimensions
Community Capital     Standing within and across communities
Experience Capital    Breadth and depth of gathering experience
Opportunity Capital   Value-creation pathways earned
Legacy Capital        What persists beyond active participation
```

All seven compound. None are transactional. Memory and Legacy capital, once lost, are
irretrievable — which is why preservation is the default posture.

---

## Part D — The Readiness Vocabulary

```
DRL    Data Readiness Level. Can a capability EXIST? Levels 0-5.
CRL    Capability Readiness Level. How DEEPLY does it manifest?
       States: Observing → Learning → Advising → Assisting →
               Coordinating → Intelligence
MRL    (Deprecated synonym for CRL. Use CRL.)
Feature Seed   A dormant capability whose data collection has begun but whose
               output is not yet activated.
Governing Inequality   manifestation_depth ≤ min(DRL_permits, CRL_earned)
DRL Activation Contract   The nine-section form required before any capability
                          activates.
```

---

## Part E — The Intelligence Vocabulary

```
Twin        A persistent, consent-gated, provenance-bound standing
            representation of a specific entity. NOT a chatbot.
Agent       An autonomous process that performs a specialised function
            across entities (Trust, Opportunity, Community, Historian,
            Tradition, Governance, Docking).
Historian   A query interface that answers questions on demand about an
            entity's history. NOT a Twin (which is a standing model).
Copilot     A task assistant that helps an entity with a specific workflow.
Chatbot     A stateless conversational interface. ZUKA does not build these.
ProvenanceRecord   The mandatory explainability attachment on every
                   intelligence output: Why? Based on what? Confidence?
                   Limitations?
```

---

## Part F — The Graph Vocabulary

```
Knowledge Graph     The union of all seven official graphs.
Human Coordination Graph   Synonym for the Knowledge Graph, emphasising its
                           purpose. The strategic moat.
The Seven Graphs    Identity, Community, Memory, Trust, Opportunity,
                    Tradition, Digital Presence.
Temporal Dimension  A cross-cutting dimension over all seven graphs.
                    NOT an eighth graph.
Node                An entity in a graph.
Edge                A typed, weighted relationship between nodes.
```

---

## Part G — The Trust State Vocabulary (human-readable, never numeric)

Per the Trust Doctrine, raw scores are never exposed. These are the canonical
human-readable trust states a Person may see:

```
INTERNAL (never shown)        EXTERNAL (shown to humans)
─────────────────────────────────────────────────────────────
Trust Pyramid Level 1         New Member
Trust Pyramid Level 2         Active Member
Trust Pyramid Level 3         Trusted Member / Community Member
Trust Pyramid Level 4         Reliable Organiser / Verified Creator
Trust Pyramid Level 5         Community Steward
```

---

## Part H — The Lifecycle Vocabulary

```
Identity Lifecycle (8 stages):
  Visitor → Registered → Verified → Participating → Trusted →
  Community → Opportunity → Legacy

Community Lifecycle (5 stages):
  Formation → Growth → Maturity → Transformation → Legacy

Relationship Lifecycle (7 stages):
  Discovery → Interaction → Participation → Familiarity → Trust →
  Collaboration → Legacy

Memory Lifecycle (6 stages):
  Capture → Verification → Preservation → Contextualisation →
  Retrieval → Legacy

Gathering Lifecycle (states):
  Draft → Published → Active → Live → Ended → Closed
  (parallel: Cancelled)

Ticket Lifecycle (states):
  Available → Payment_Pending → Issued → Checked_In
  (branches: Gift escrow, Transfer; terminals: Expired/Refunded/Revoked)
```

---

## Part I — The Identifier Doctrine

```
Every entity receives a Global Unique Identifier:
  Format:  ZKA_<ENTITY>_<UUID>
  Examples: ZKA_PERSON_<uuid>, ZKA_GATHERING_<uuid>, ZKA_COMMUNITY_<uuid>
  Requirements: Immutable · Globally Unique · Non-Reusable · Traceable
```

---

## Part J — The Privacy Classification Vocabulary

```
Level 0  Public      Anyone on the platform
Level 1  Community    Specific community membership
Level 2  Private      Only the person themselves
Level 3  Restricted   Legal hold / under review / steward-only

Visibility levels (for Persona outputs and entities):
  private · family · community · trusted_network · public
```

---

## Part K — Deprecated Terms (do not use)

```
DEPRECATED              USE INSTEAD
"User"                  Person
"Event" (arch context)  Gathering
"Profile"               Identity
"MRL"                   CRL
"Trust Score" (exposed) Trust state (human-readable)
"Group" (as community)  Community
"Followers" (as trust)  Trust / Relationship signals
```

---

> **Volume 03 — The Canonical Language Dictionary**
> One word, one meaning. Terminology drift is architectural drift.
