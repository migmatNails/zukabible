# ZUKA BIBLE V4 — VOLUME 18
# Distributed Intelligence & Agents

> **Part V — Intelligence** · **Authority Level 4** · **Experimental Zone**
> Synthesises: Canonical Volume X, FounderArchitect Volume X, Volume XIX
> ADR-020: Agent-to-Agent interaction is asynchronous only

---

## Chapter 1 — Mission

Create a federation of specialised intelligence agents. Centralised intelligence
becomes a bottleneck; distributed intelligence scales.

---

## Chapter 2 — The Seven Agents (canonical — resolves 5-vs-7)

The prior layers disagreed (5 agents in some, 7 in others). V4 resolves to **seven**:

```
TRUST AGENT        Continuous cross-entity trust assessment.
                   Produces TrustEdge updates, TrustTimelinePoint entries.
OPPORTUNITY AGENT  Matching + emergence detection (two modes).
                   SOURCE of OpportunityEmerged events.
COMMUNITY AGENT    Cross-community pattern detection.
                   Produces aggregate patterns (B2B-eligible, k-anonymised).
HISTORIAN AGENT    Orchestrates the seven Historians (Volume 10).
                   NOT an eighth Historian — an orchestration layer.
TRADITION AGENT    Cross-tradition lifecycle tracking.
                   Implements "discovered, not created" operationally.
GOVERNANCE AGENT   Monitors governance actions for explainability/auditability
                   compliance across communities. (Added in Volume X.)
DOCKING AGENT      Manages connector health, capability versioning, and
                   reversible integration state. (Added in Volume X.)
```

---

## Chapter 3 — The Agent Contract

```
Every agent must be: Explainable · Auditable · Reversible · DRL-gated.
No agent may operate above the maturity of its supporting data.
An agent's effective DRL = the MINIMUM across all entities in its scope
(never an average — the weakest data governs).
```

---

## Chapter 4 — Agent-to-Agent Interaction (ADR-020)

```
NO synchronous Agent-to-Agent invocation. Asynchronous write-then-read only.
One agent writes a result; another reads it later. This prevents cascading
failures and circular reasoning.

A two-hop AgentProvenanceRecord chain preserves full lineage: any agent output
traces through the Twin syntheses or DomainEvents it consumed, and through those
to raw events. Provenance never terminates at an unverifiable summary.
```

---

## Chapter 5 — The Decision Hierarchy

```
Data → Signals → Knowledge → Recommendations → Human Approval → Automation

Automation is the LAST stage, reached only at DRL 4+ with explicit human
approval in the loop. Revocation of any agent decision is always human (ADR-014).
```

---

## Chapter 6 — The Opportunity Agent's Two Modes

```
MODE 1 — Emergence Detection
  Reads Trust + Relationship graphs to detect latent opportunities.
  Produces OpportunityEmerged events.
  Blocked until Trust domain is at DRL 2+. (ADR-004.)

MODE 2 — Matching
  Matches opportunities to people via high-quality relationship paths.
  Every match carries a complete ProvenanceRecord before reaching any party.
```

---

## Chapter 7 — The Explainability Architecture (ADR-007)

```
Every agent output carries a ProvenanceRecord:
  Why?              evidence_summary (generated FROM events)
  Based on what?    evidence_events[] (specific event IDs)
  Confidence?       0.0-1.0
  Limitations?      computation_method + version

The recursive lineage trace query walks this chain to arbitrary depth.
```

---

## Chapter 8 — Agent Domain Events

```
DataReadinessLevelChanged · FeatureSeedActivated · TwinSynthesisCompleted ·
OpportunityEmerged · TraditionDetected · CommunityPatternDetected
```

---

## Chapter 9 — Founder Notes

```
Centralised intelligence becomes a bottleneck.
Distributed intelligence scales.
No agent operates above the maturity of its data.
```

---

> **Volume 18 — Distributed Intelligence & Agents** · Experimental
> Seven agents. Asynchronous only. Human approval before automation.
