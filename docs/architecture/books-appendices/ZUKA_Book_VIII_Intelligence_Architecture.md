# ZUKA Unified Architecture Bible
# BOOK VIII — INTELLIGENCE ARCHITECTURE

> **Constitutional Authority:** Level 2 — Governance & Intelligence
> **Source:** Volume IX (AI Twin Architecture); Volume X (Distributed Intelligence);
>              Volume XIV (Capability Readiness Architecture);
>              Zuka-Bible 2 Book VIII
> **ADRs:** ADR-003 (Reality precedes AI); ADR-007 (Explainability mandatory);
>            ADR-008 (DRL governs intelligence activation)

---

## Constitutional Premise

Intelligence is the structured amplification of human participation through knowledge,
reasoning, and contextual assistance. (Zuka-Bible 2, Book VIII, Ch.1)

Reality precedes intelligence. (ADR-003)
AI may amplify participation. AI may not replace participation. (Zuka-Bible 2, Book I Part A)
Architected does not mean activated. (Zuka-Bible 2, Book I Part C, Ch.14.1)

---

## The Two Governing Axes (Volume XIV)

```
DRL (Data Readiness Level)     — determines WHETHER a capability can exist
                                  Evidence: is the data honest enough?
                                  Ladder: 0-5 (Schemas → Intelligence)

CRL (Capability Readiness Level) — determines HOW DEEPLY it manifests
                                  Relationship: is the relationship mature enough?
                                  States: Observing → Learning → Advising →
                                          Assisting → Coordinating → Intelligence

Governing inequality:
  manifestation_depth ≤ min(DRL_permits, CRL_earned)

Both must independently clear before any output is permitted.
```

---

## Six Twin Types (Volume IX)

```
Personal Twin     — standing representation of one Person; consent always revocable
Family Twin       — Family Vault + family-level data; strategic_layer consent required
Community Twin    — community-owned data + aggregate membership
Gathering Twin    — single occurrence (thin) or Tradition series (richer)
Opportunity Twin  — strictest consent: dual-party; single revocation decommissions whole Twin
City Twin         — aggregate-only; sole Twin type eligible for B2B use
```

All six: DRL 0 at this writing. All: CRL 'observing'. No Twin is producing output.

Default Personal Twin legacy policy: immediate decommission on death.
Requires explicit living opt-in to extend to steward-controlled or preserve-frozen.

---

## Five Agent Types (Volume X)

```
Trust Agent          — PERFORMS continuous cross-entity trust assessment
                        Produces: TrustEdge updates, TrustTimelinePoint entries
Opportunity Agent    — PERFORMS matching + emergence detection (two modes)
                        Produces: OpportunityEmerged events; match candidates
Community Agent      — PERFORMS cross-community pattern detection
                        Produces: cross-community statistical patterns (B2B eligible)
Historian Agent      — PERFORMS narration, orchestrating seven Historians
                        Is: orchestration layer, NOT an eighth Historian
Tradition Agent      — PERFORMS cross-tradition lifecycle tracking
                        Implements: "discovered, not created" (Volume VII Ch.15)
```

Key rule: NO synchronous Agent-to-Agent invocation. Asynchronous write-then-read only.
Two-hop AgentProvenanceRecord chain preserves full lineage depth. (Volume X, Ch.11)

---

## The Explainability Constitution (Zuka-Bible 2 Book VIII, Ch.11; ADR-007)

Every significant intelligence output answers:
```
Why?              — ProvenanceRecord.evidence_summary
Based on what?    — ProvenanceRecord.evidence_events[] (specific DomainEvent IDs)
With what confidence? — ProvenanceRecord.confidence
Subject to what limitations? — ProvenanceRecord.computation_method + version
```

The recursive lineage query (Volume XII, Ch.10) makes this traceable to arbitrary depth —
from any Agent output, through Twin synthesis, through to raw DomainEvents.

---

## Safety Architecture (Zuka-Bible 2 Book VIII, Ch.12)

```
Governance review       — DRL Activation Contract (Volume XX, Ch.10) required
Capability gating       — DRL + CRL governing inequality (Volume XIV)
Auditability            — ProvenanceRecord on all outputs (ADR-007)
Human oversight         — revocation is always human (ADR-014)
Anti-fraud              — Trust Agent detects; Agents never autonomously punish
```

---

*Source: Volumes IX, X, XIV; Zuka-Bible 2 Book VIII*
