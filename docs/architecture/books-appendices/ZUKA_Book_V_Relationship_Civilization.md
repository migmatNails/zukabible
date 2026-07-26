# ZUKA Unified Architecture Bible
# BOOK V — RELATIONSHIP CIVILIZATION

> **Constitutional Authority:** Level 1 — Domain Constitution
> **Source:** Volume II Part II (Relationship entity); Volume VII Part XI
>              (Relationship Evolution); Zuka-Bible 2 Book V
> **ADR:** ADR-001 — Relationships are first-class entities

---

## Constitutional Premise

Relationships are not edges. They are not join tables. They are not foreign keys.

A Relationship is a first-class entity with its own identity, lifecycle, trust score,
weight, decay model, and embedding. (ADR-001)

From Zuka-Bible 2, Book I Part B (Ch.6): *"Participation creates relationships."*
Relationships are the second-order product of participation — what people become
to each other through the act of showing up together.

---

## Relationship Entity (Volume II, Part II)

```typescript
Relationship {
  relationship_id:      UUID
  from_entity_id:       UUID     // Person, Community, or Organisation
  to_entity_id:         UUID
  relationship_type:    RelationshipType

  // Strength — decays without interaction (Volume VII Part XI)
  weight:               Float(0.0-1.0)
  trust_score:          Float(0.0-1.0)
  interaction_count:    Integer
  last_interaction_at:  Timestamptz

  // Lifecycle
  lifecycle_stage:      RelationshipLifecycle
  // 'forming' | 'active' | 'dormant' | 'ended'
  started_at:           Timestamptz
  ended_at:             Timestamptz | null

  // Context of origin
  origin_gathering_id:  UUID | null   // where the relationship formed
  origin_community_id:  UUID | null

  // Temporal envelope (Volume VII Ch.3 — mandatory)
  valid_from:           Timestamptz
  valid_to:             Timestamptz
  observed_at:          Timestamptz
  recorded_at:          Timestamptz
  confidence:           Float(0.0-1.0)
  version:              Integer

  embedding:            Vector(1536)
}
```

---

## Relationship Type Registry

```
PERSONAL:
  friend            — peer social connection
  family            — biological or chosen family
  couple            — romantic partnership
  neighbour         — geographic proximity bond

PROFESSIONAL:
  colleague         — shared workplace
  business_partner  — formal commercial relationship
  mentor            — developmental guidance relationship
  student           — receiving developmental guidance
  employer          — employment authority
  employee          — employment subordinate

COMMUNITY:
  community_member  — shared community belonging
  follows           — asymmetric interest connection
  co-organiser      — shared event/gathering responsibility

CREATIVE:
  collaborator      — joint creative output
  client            — commercial creative engagement
  agent             — representation relationship
```

---

## Relationship Decay Model (Volume VII, Part XI)

Relationships that are not maintained decay in weight — this is reality, not punishment
(the same principle as Volume VII's Trust decay, Ch.18):

```
HIGH interaction rate (≥ 1 shared gathering/month): weight stable or growing
MEDIUM interaction rate (1 shared gathering/quarter): weight slowly declining
LOW interaction rate (< 1 shared gathering/6 months): weight declining
DORMANT (no interaction > 12 months): weight floor at 0.1 (relationship existed;
  not deleted — per ADR-015 and Volume VII Temporal Truth Principle)

The relationship record is NEVER deleted — only weight decays.
History of all interactions is preserved in the Event Store.
```

---

## Relationship and Capital

Relationships generate two forms of capital simultaneously:

```
Relationship Capital  — the network itself; who you are connected to,
                        and how strongly (Volume II, Ch.17)
Trust Capital         — the confidence earned within each relationship
                        over time (Volume XIII)
```

Strong Relationships are the precondition for Opportunity emergence
(Volume VII, Ch.20: trust-precedes-opportunity, which precedes opportunity
which precedes value creation — the full pipeline confirmed in Volume XIII, Ch.10).

---

## Domain Events

```
RelationshipProposed     — one entity initiates a connection
RelationshipAccepted     — both entities confirmed
RelationshipWeightUpdated — decay or growth applied (Trust Agent output)
RelationshipDormant      — weight crossed the dormant threshold
RelationshipEnded        — formal end (divorce, estrangement, employment end)
```

---

*Source: Volume II Part II; Volume VII Part XI; Zuka-Bible 2 Book V*
