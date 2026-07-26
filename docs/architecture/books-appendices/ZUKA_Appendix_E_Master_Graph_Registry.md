# ZUKA — APPENDIX E: MASTER GRAPH REGISTRY

> Source: Volume VIII, Ch.7 | ADR-012 | Volume XII, Ch.2

Seven official graphs. Temporal is a cross-cutting dimension, not an eighth graph.
At MVP: all seven are VIEWS over PostgreSQL. Dedicated graph store: Stage 3 trigger.

| Graph | Nodes | Primary Edges | DRL Status | B2B Eligible? |
|-------|-------|--------------|-----------|---------------|
| Identity | Person, Persona, Verification, Badge | VERIFIES, HAS_PERSONA, EARNED_BADGE | DRL 1 | No |
| Community | Community, CommunityMembership | BELONGS_TO, FEDERATES_WITH, GOVERNS | DRL 1 | No |
| Memory | Memory, Media, Story, Tradition | DOCUMENTS, REFERENCES, LINKED_TO_TRADITION | DRL 1 | No |
| Trust | Person, Community, Organisation, Opportunity | TRUSTS, ENDORSED, VERIFIED, WORKED_WITH | DRL 0-1 | No |
| Opportunity | Opportunity, Person, Community | OPPORTUNITY_FOR, EMERGED_FROM, MATCHED_TO | DRL 0 | No |
| Tradition | Tradition, Gathering, Community | OCCURRENCE_OF, STRENGTHENED_BY, OWNED_BY | DRL 0-1 | No |
| Digital Presence | (unspecified) | (unspecified) | DRL 0 — DORMANT | No |

**B2B eligibility:** Only City Twin outputs (aggregate, k-anonymised) and Community Agent
cross-community patterns (aggregate, k-anonymised) are eligible. All other graph outputs:
never for B2B use. (Volume IX, Ch.11; Volume X, Ch.6)

**The Crown Jewel:** Trust + Opportunity + Community + Memory graphs combined with
historical temporal depth = ZUKA's primary strategic moat. (Volume VIII, Ch.28;
Volume XII, Ch.1; ZUKA_Constitutional_Foundation.md Ch.1)
