# ZUKA — APPENDIX I: EXPLAINABILITY REGISTRY

> Source: Volume VIII, Ch.20 | ADR-007 | Zuka-Bible 2, Book VIII, Ch.11
> Constitutional rule: "Every significant intelligence output should answer:
>   Why? Based on what? With what confidence? Subject to what limitations?"

---

## Explainability Requirements by Output Type

| Output Type | ProvenanceRecord Required? | User-Facing Explanation Required? | Governing Volume |
|------------|--------------------------|----------------------------------|-----------------|
| Trust score computation | YES — always | YES — "why is my trust level X?" | III, XIII |
| Trust dimension change | YES — always | YES — specific event cited | VII, XIII |
| Badge award | YES — always | YES — displayed at award | III, VIII |
| Tradition detection | YES — always | YES — "this pattern was detected because..." | VII, X |
| Opportunity match | YES — always, STRICTER bar | YES — both parties see reasoning | VIII Ch.22, X |
| Twin insight (any type) | YES — always | YES — visible in explanation view | IX |
| Twin recommendation | YES — always | YES — no recommendation without reasoning | IX |
| Agent community pattern | YES — always | Advisory only; B2B eligible if aggregate | X |
| Agent trust assessment | YES — always | Not directly user-facing; feeds Trust Profile | X |
| Data quality flag | YES (as DomainEvent trail) | NO — internal operational; human review | XII |
| Appeals decision | YES (human-authored rationale) | YES — appellant sees full reasoning | XIII |
| DRL Activation Contract | YES — nine-section form | YES — documented before any feature launches | XX |

---

## The Four Explainability Questions (Zuka-Bible 2, Book VIII, Ch.11)

Applied to every output in the registry above:

```
1. WHY?
   → ProvenanceRecord.evidence_summary (generated FROM events; never authored independently)

2. BASED ON WHAT?
   → ProvenanceRecord.evidence_events[] (specific DomainEvent IDs, never abstract categories)

3. WITH WHAT CONFIDENCE?
   → ProvenanceRecord.confidence (Float 0.0-1.0; inherits from TemporalEntity chain)

4. SUBJECT TO WHAT LIMITATIONS?
   → ProvenanceRecord.computation_method + computation_method_version
     (what algorithm; what version; what it cannot account for)
```

---

## What Is Explicitly NOT Explainable (and Why That Is Intentional)

```
Observability events (Volume XI, Ch.12):
  "Why did the server return a 500 error?" is an operational question, not an
  intelligence-layer conclusion. Observability data has no ProvenanceRecord.
  It has logs, metrics, and traces. These are separate from DomainEvents and
  from the Intelligence layer's ProvenanceRecord system.

Aggregate B2B outputs (City Twin, Community Agent cross-community patterns):
  These are k-anonymised. Explainability at the individual level is deliberately
  impossible by construction — that is how k-anonymity protects privacy.
  The aggregate output is explainable at the population level; individual
  contribution to it is not and must not be traceable.
```

Explainability applies to conclusions about identifiable entities.
It is not a requirement that would accidentally break privacy-protective aggregation.
