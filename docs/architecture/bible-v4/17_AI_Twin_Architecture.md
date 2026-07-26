# ZUKA BIBLE V4 — VOLUME 17
# AI Twin Architecture

> **Part V — Intelligence** · **Authority Level 4** · **Experimental Zone**
> Synthesises: Canonical Volume IX, FounderArchitect Volume IX
> ADR-019: AI Twins are representations, not chatbots

---

## Chapter 1 — Mission

Create contextual intelligence, not generic AI. A Twin is a persistent, consent-gated,
provenance-bound standing representation of a specific entity. It consumes the graph;
it never becomes the source of truth.

```
Twins consume the graph. Twins never become the source of truth. (ADR-003)
A Twin may never claim confidence greater than its supporting data. (Twin Constitution)
```

---

## Chapter 2 — The Six Twin Types

All six are DRL 0, CRL Observing at this writing. None produce output yet.

```
PERSONAL TWIN     One Person's data; Digital Presence excluded by default.
                  Consent always revocable immediately.
FAMILY TWIN       Family Vault + family-level data; strategic_layer consent.
COMMUNITY TWIN    Community-owned data; governance-layer consent.
GATHERING TWIN    Single occurrence (thin) or Tradition series (richer).
OPPORTUNITY TWIN  Strictest: dual-party consent; one party revokes →
                  the whole Twin is decommissioned.
CITY TWIN         Aggregate-only; the sole Twin type eligible for B2B use,
                  always k-anonymised.
```

---

## Chapter 3 — Twin vs Historian vs Copilot vs Chatbot

```
HISTORIAN  Query interface — answers questions ON DEMAND about an entity's history
TWIN       Standing representation — IS a persistent model OF an entity
COPILOT    Task assistant — HELPS an entity with a specific workflow
CHATBOT    Stateless conversational interface — ZUKA DOES NOT BUILD THESE
```

---

## Chapter 4 — The DRL Activation Matrix for Twins

```
DRL 0-2   Historical representation only (no prediction)
DRL 3     Insights
DRL 4     Recommendations
DRL 5     Historians, copilots, predictive systems
```

A Twin's depth is also gated by CRL (governing inequality): a DRL 5 Twin with a
CRL Observing relationship still produces only shallow output.

---

## Chapter 5 — The Twin Constitution

```
A Twin may never claim confidence greater than the supporting data.
Every Twin output carries a ProvenanceRecord (ADR-007).
Twins cannot make legal, financial, or governance decisions without human
oversight. (Safety boundary.)
```

---

## Chapter 6 — Twin Governance & Consent

```
Users can: Enable · Disable · Export · Delete — at any time.

TwinConsentRecord: always revocable immediately.
TwinLegacyBinding: default 'immediate_on_death'. Requires explicit living
  opt-in to extend to steward-controlled or preserve-frozen modes.

Child Twins: guardian consent, most-restrictive scope; auto-decommissioned at
majority unless the now-adult re-confirms. (Volume 08.)
```

---

## Chapter 7 — Twin Domain Events

```
TwinSynthesisCompleted (carries provenance_record_id, method version)
TwinConsentGranted · TwinConsentRevoked · TwinDecommissioned
```

---

## Chapter 8 — Founder Notes

```
Twins consume the graph. Twins never become the source of truth.
Representation, not simulation. Insight, not authority.
```

---

> **Volume 17 — AI Twin Architecture** · Experimental
> A Twin represents. It never replaces.
