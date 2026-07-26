# ZUKA — ARCHITECTURE DECISION RECORDS (ADR CATALOG)

> Source: Zuka-Bible 2 ADR Catalog (ADRs 001-008) + Canonical Volumes (ADRs 009-020)
> Full specifications: zuka-docs/ZUKA_Architecture_Decision_Record_Registry.md
> This file is a quick-reference index. Read the full registry for context,
> amendment thresholds, and volume cross-references.

---

## Immutable Zone ADRs (require Evolution Council + founder constitutional amendment)

```
ADR-001  Relationships are first-class entities
ADR-002  Trust cannot be purchased
ADR-003  Reality precedes AI
ADR-004  Participation precedes prediction
ADR-005  Capital Ontology is constitutional (seven capitals; no competing taxonomy)
ADR-006  Legacy remains cross-domain
ADR-007  Explainability is mandatory (ProvenanceRecord on all intelligence outputs)
ADR-008  DRL governs intelligence activation
```

## Stable Zone ADRs (require council review + founder approval)

```
ADR-009  Memory and Media are permanently separate entities
ADR-010  The canonical human entity is Person, not User
ADR-011  The canonical community-scale event is a Gathering, not an Event
ADR-012  The Knowledge Graph consists of exactly seven official graphs
ADR-013  Every external integration connects through a ConnectorContract
ADR-014  Revocation of any trust decision is always a human act
```

## Adaptive Zone ADRs (require Architecture Council review)

```
ADR-015  All significant actions emit immutable DomainEvents
ADR-016  Start with a modular monolith; extract only when triggers fire
ADR-017  Every capability activation requires a completed DRL Activation Contract
ADR-018  Data quality controls detect aggressively and correct conservatively
```

## Experimental Zone ADRs (require Architecture Council review)

```
ADR-019  AI Twins are representations, not chatbots
ADR-020  Agent-to-Agent interaction is asynchronous only
```

## Adaptive Zone ADRs, continued — project-level (Architecture Council = founder, solo)

> This catalog previously stopped at ADR-020 and said "Total: 20 ADRs,"
> which had gone stale — ADR-021/022/023/026 existed as standalone files
> under `docs/architecture/` but were never indexed here (caught by
> `zuka-reviewer` during the 2026-07-05 compendium brainstorm). Fixed below.
> ADR-024 and ADR-025 are **candidate numbers only** (raised by R011-F1 and
> R002-F2 respectively) — not yet drafted as files; do not treat them as
> ratified.

```
ADR-021  Caching Strategy: Discovery Feed vs Transactional Paths
ADR-022  SECURITY DEFINER Helper to Break Circular RLS Recursion
ADR-023  Offline Scanner Mode: Manifest Design & Frozen Hashing Contract
ADR-024  [candidate — dev-tooling governance, raised by R011-F1; not drafted]
ADR-025  [candidate — Event-Store PII erasure, raised by R002-F2; not drafted —
          see docs/concepts/CONCEPT_EventStore_PII_Erasure.md]
ADR-026  Ticket-Payment Settlement & NPS-Act Aggregator Posture (PROPOSED —
          product cut-line accepted 2026-07-04; compliance gate open pending
          Ugandan counsel)
ADR-027  MVP Boundary & Dormant-Capability Enforcement (Mechanical Layer)
ADR-028  Flutter App Shell: State Management, Navigation & Project Structure
          (ACCEPTED 2026-07-05 — Riverpod + go_router + feature-first structure)
```

Total: 25 ratified/proposed ADRs at this writing (001–023, 026, 027, 028),
plus 2 reserved candidate numbers (024, 025) not yet drafted.
New ADRs are added via the process in zuka-docs/ZUKA_Architecture_Decision_Record_Registry.md
