# ZUKA BIBLE V4 — VOLUME 06
# Knowledge Graph & Semantic Intelligence

> **Part II — The Data Foundation** · **Authority Level 1**
> Synthesises: BLACK XXXI, Volume VIII (Knowledge Graph), Volume XII

---

## Chapter 1 — Mission

Transform data into meaning. The Knowledge Graph (also called the Human Coordination
Graph) is the union of all relationship data across ZUKA. It is the strategic moat.

---

## Chapter 2 — The Seven Official Graphs (ADR-012)

```
Identity Graph         Person · Persona · Verification · Badge nodes
Community Graph        Community · Membership · Federation nodes
Memory Graph           Memory · Media · Story · Tradition nodes
Trust Graph            TrustEdge relationships; weight, decay over time
Opportunity Graph      Opportunity · OpportunityEdge · Emergence signals
Tradition Graph        Tradition · Occurrence · cultural pattern detection
Digital Presence Graph DORMANT — privacy review required before specification

Temporal Dimension     Cross-cutting over all seven; NOT an eighth graph
```

At MVP all seven are VIEWS over the single PostgreSQL database. A dedicated graph
store is built only when Stage 3's observable trigger fires (Volume 20).

---

## Chapter 3 — Core Nodes & Edges

```
NODES:  People · Families · Communities · Gatherings · Memories ·
        Traditions · Trust · Opportunities · Organisations · Venues

EDGES:  ATTENDED · MEMBER_OF · TRUSTS · HOSTED · CREATED · MENTORED ·
        COLLABORATED_WITH · PARTICIPATED_IN · ENDORSED · SPONSORED ·
        ORGANIZED_WITH · KNOWS · ATTENDED_WITH · RECOMMENDED
```

---

## Chapter 4 — The Semantic Layer

Raw data is enriched into meaning:

```
Raw events → Traditions · Relationships · Community culture ·
             Trust signals · Opportunity signals
```

Every entity supports tags, attributes, signals, relationships, and embeddings.
AI readiness begins at MVP — the embedding field exists from the first migration,
even though it is not populated until DRL 1.

---

## Chapter 5 — Knowledge Provenance (ADR-007)

Every insight must answer four questions:

```
Why?                 evidence_summary (generated FROM events)
Based on what?       evidence_events[] (specific event IDs)
At what confidence?  confidence (0.0-1.0)
With what limits?    computation_method + version
```

No conclusion is a black box. The recursive lineage trace (Volume 18) walks the
provenance chain to arbitrary depth.

---

## Chapter 6 — DRL Gates for Knowledge Systems

```
DRL 0-1   Relationship capture
DRL 2     Pattern discovery
DRL 3     Explainable insights
DRL 4     Recommendations
DRL 5     Intelligence systems (Twins, Historians, Copilots)
```

---

## Chapter 7 — The Crown Jewel Declaration

```
The moat is not the application. The moat is:

  Identity Graph + Relationship Graph + Trust Graph +
  Community Graph + Memory Graph + Opportunity Graph
  + decades of temporal historical continuity

Applications can be rebuilt. The graph, and the years behind it, cannot.
```

This is the single most important strategic statement in the entire architecture.
The B2B intelligence products (Volume 23), the Twins (Volume 17), the Agents
(Volume 18), and the Opportunity Engines (Volume 19) all derive their value from
this graph. Protecting it is the first priority of Security (Volume 21).

---

## Chapter 8 — Intelligence Doctrine

```
AI systems never become sources of truth.
Truth flows: Events → Relationships → Graphs → Intelligence
Never backward. A Twin or Agent that contradicts the event record is wrong
by definition; the events are authoritative. (ADR-003)
```

---

> **Volume 06 — Knowledge Graph & Semantic Intelligence**
> The graph is the moat.
