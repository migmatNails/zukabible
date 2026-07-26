# ZUKA — APPENDIX H: PROVENANCE REGISTRY

> Source: Volume VIII, Ch.20 | Volume IX, Ch.15 | Volume X, Ch.11 | ADR-007

Explainability is mandatory (ADR-007). Every significant intelligence output must
answer: Why? Based on what? With what confidence? Subject to what limitations?

---

## ProvenanceRecord Structure (Volume VIII, Ch.20)

```typescript
ProvenanceRecord {
  conclusion_id:          UUID
  conclusion_type:        ConclusionType   // see registry below
  entity_id:              UUID             // entity this conclusion is about
  evidence_events:        DomainEventReference[]   // specific event IDs
  evidence_summary:       TEXT             // generated FROM events; never authored
  computation_method:     TEXT             // method identifier
  computation_method_version: SemVer
  confidence:             Float(0.0-1.0)
  created_at:             Timestamptz
  expires_at:             Timestamptz | null
}
```

---

## ConclusionType Registry (all registered types)

| ConclusionType | Introduced | Description |
|---------------|-----------|-------------|
| trust_score | Volume VIII | Trust dimension score computation |
| tradition_recognition | Volume VIII | Tradition detected/confirmed |
| recommendation | Volume VIII | General platform recommendation |
| reputation_tag | Volume VIII | Character tag awarded |
| twin_insight | Volume IX | Personal/Family/Community/Gathering/Opportunity/City Twin insight |
| twin_recommendation | Volume IX | Twin-generated suggestion |
| twin_third_party_response | Volume IX | Twin responding to external query |
| agent_trust_assessment | Volume X | Trust Agent cross-entity assessment |
| agent_opportunity_match | Volume X | Opportunity Agent match result |
| agent_community_pattern | Volume X | Community Agent cross-community pattern |
| agent_historian_synthesis | Volume X | Historian Agent multi-Historian synthesis |
| agent_tradition_lifecycle_update | Volume X | Tradition Agent lifecycle state change |

---

## AgentProvenanceRecord (Volume X, Ch.11 — the two-hop chain)

```typescript
AgentProvenanceRecord extends ProvenanceRecord {
  layered_provenance: {
    direct_evidence_events:     DomainEventReference[]   // direct graph reads
    inherited_twin_provenance:  ProvenanceRecordReference[]  // Twin syntheses consumed
  }
  // Rule: a TWO-HOP trace is always available from any Agent output
  // → to the Twin synthesis or DomainEvents that produced it
  // → from that Twin, to the DomainEvents that produced THAT
  // Provenance never terminates at an unverifiable summary
}
```

---

## Lineage Trace Query (Volume XII, Ch.10)

The recursive SQL query that walks the full provenance chain to arbitrary depth:
```sql
WITH RECURSIVE lineage_trace AS (
  SELECT conclusion_id, conclusion_type, evidence_events,
         inherited_twin_provenance, 0 AS depth
  FROM provenance_records WHERE conclusion_id = :insight_id
  UNION ALL
  SELECT pr.conclusion_id, pr.conclusion_type, pr.evidence_events,
         pr.inherited_twin_provenance, lt.depth + 1
  FROM provenance_records pr
  JOIN lineage_trace lt ON pr.conclusion_id = ANY(lt.inherited_twin_provenance)
  WHERE lt.depth < 10
)
SELECT * FROM lineage_trace ORDER BY depth;
```

This turns the structural guarantee into an operationally usable capability.
