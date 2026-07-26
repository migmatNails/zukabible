# ZUKA Architecture Decision Record Registry

> **Authority:** Zuka-Bible 2, Book I Part D (Chapter 21 — ADR Governance) +
>                  Constitutional synthesis across all canonical volumes
> **Status:** Living document — new ADRs are added as significant architectural
>              decisions are made; no ADR is ever deleted (append-only, per ADR-001's
>              spirit applied to the registry itself)
> **Source Synthesis:** Zuka-Bible 2 established ADR governance as a constitutional
>                  requirement (Book I Part D, Ch.21). The canonical volumes (II–XXII)
>                  produced the specific decisions. This registry is the first unified
>                  register of both.

---

## How ADRs Work in This Bible

Every significant architectural decision that was made in the canonical volumes and cannot
be reversed without a constitutional amendment belongs here. The format is:

- **Number** — permanent, never reused
- **Decision** — the ruling in one sentence
- **Source** — which volume made it canonical
- **Zone** — from Zuka-Bible 2 Book I Part C, Ch.13: Immutable / Stable / Adaptive / Experimental
- **Amendment threshold** — what level of authority is required to change it

---

## Constitutional ADRs (Zone: Immutable)

*These require the highest amendment threshold — founder decision + constitutional
review — before they can be changed. They are the DNA of the platform.*

---

**ADR-001 — Relationships are first-class entities.**
Source: Zuka-Bible 2 ADR Catalog; operationalised in Volume II, Part II.
Implication: Relationship is never a simple foreign key or edge table. It is a full
entity with weight, lifecycle, trust_score, embedding, and decay model. Any future
schema or API that reduces Relationship to a mere join table violates this ADR.
Zone: Immutable
Amendment threshold: Founder decision + constitutional amendment process

---

**ADR-002 — Trust cannot be purchased.**
Source: Zuka-Bible 2 ADR Catalog; operationalised in Volume III, Ch.15; Volume VI,
Ch.14; Volume XIII, Part II (Trust Pyramid).
Implication: No subscription tier, no advertiser arrangement, no fee-for-badge mechanism
may grant any TrustDimension score or Trust Pyramid level to any entity. Trust is
computed exclusively from observed participation evidence. Any product feature that
would allow a payment to improve a trust score violates this ADR.
Zone: Immutable
Amendment threshold: Founder decision + constitutional amendment process

---

**ADR-003 — Reality precedes AI.**
Source: Zuka-Bible 2 Book I Part B, Ch.5.2 ("No model, algorithm, prediction, AI system,
reputation system, or recommendation engine may supersede reality"); operationalised
in Volume VII, Part XII (DRL framework — data maturity gates all AI capability);
Volume IX, Part II (Twin consent and DRL gating); Volume X, Ch.13 (Agent effective DRL
is minimum, never average, across contributing entities).
Implication: No AI-generated conclusion may be treated as more authoritative than a
directly observed DomainEvent. The DRL ladder exists precisely to enforce this — a
system claiming to produce trust intelligence (DRL 5) when only collection-level data
(DRL 1) exists violates this ADR. Confirmed as ADR-003 in Zuka-Bible 2 ADR Catalog.
Zone: Immutable
Amendment threshold: Founder decision + constitutional amendment process

---

**ADR-004 — Participation precedes prediction.**
Source: Zuka-Bible 2 ADR Catalog + Book I Part B, Ch.6.3 (Participation Hierarchy:
Presence → Engagement → Contribution → Stewardship → Legacy).
Implication: No recommendation, opportunity match, or AI-generated insight may be
surfaced for an entity that has not yet generated observed participation evidence.
Corollary: "What people do" always outweighs "what people claim" in every scoring and
matching algorithm. The DRL 1 FORBIDDEN list (Volume XII, Ch.4) operationalises this
directly — predictions and trust scoring are forbidden until DRL 2's participation
evidence threshold is met.
Zone: Immutable
Amendment threshold: Founder decision + constitutional amendment process

---

**ADR-005 — Capital Ontology is constitutional.**
Source: Zuka-Bible 2 ADR Catalog; operationalised in Volume II, Ch.17 (seven capitals:
Memory, Relationship, Trust, Community, Experience, Opportunity, Legacy).
Implication: The seven-capital model is constitutional. No future product, data model,
or business intelligence layer may introduce a competing capital taxonomy without
going through the constitutional amendment process. Capabilities that generate or
consume capital (Twins, Agents, Historians) must specify which capital type(s) they
affect in their CapabilityDeclaration (Volume XIX, Ch.3).
Zone: Immutable
Amendment threshold: Founder decision + constitutional amendment process

---

**ADR-006 — Legacy remains cross-domain.**
Source: Zuka-Bible 2 ADR Catalog; operationalised across Volume III, Part X
(Identity-level Legacy), Volume V, Part XVII (Memory-level Legacy), Volume VI, Part XXII
(Community-level Legacy), Volume XVII, Ch.2 (the first synthesis of all three levels
into a single cross-cutting principle).
Implication: Legacy must never be architecturally siloed into a single domain. There is
no "Legacy Domain" in the eight-domain model (Volume XI, Ch.3) because Legacy is a
cross-cutting state that multiple domains produce and must all handle. Any refactoring
that creates a dedicated Legacy microservice or database schema without cross-domain
integration violates this ADR.
Zone: Immutable
Amendment threshold: Founder decision + constitutional amendment process

---

**ADR-007 — Explainability is mandatory.**
Source: Zuka-Bible 2 ADR Catalog (ADR-007); operationalised in Volume VIII, Ch.20
(ProvenanceRecord — every conclusion must be traceable to its source DomainEvents);
Volume IX, Ch.15 (Twin outputs inherit ProvenanceRecord unconditionally); Volume X,
Ch.11 (Agent outputs carry two-hop AgentProvenanceRecord chain); Volume XII, Ch.10
(lineage trace query as the operational implementation); Volume XX, Ch.10, Section G
(Provenance Check as a mandatory gate in the DRL Activation Contract).
Implication: No intelligence output — Twin insight, Agent match, recommendation,
trust score — may be surfaced to any human without a complete ProvenanceRecord showing
which DomainEvents it is based on, what computation method produced it, and with what
confidence. "Black box" outputs violate this ADR unconditionally. The explainability
requirement does not relax at any DRL level.
Zone: Immutable
Amendment threshold: Founder decision + constitutional amendment process

---

**ADR-008 — DRL governs intelligence activation.**
Source: Zuka-Bible 2 ADR Catalog (ADR-008); fully operationalised across Volumes VII
(DRL framework), XI (infrastructure capability gate), XII (data use permission gate),
XIII (trust-specific gate), XIV (CRL governing inequality), XX (DataReadiness Assessment
Operational Procedure).
Implication: No intelligence capability — Twin, Agent, Historian, Copilot — may activate
before its domain has independently cleared the required DRL threshold for that
capability level, as assessed per Volume XX's operational procedure. "The technology
is ready" is never a sufficient justification for activation; "the DATA is ready" is
the only justification. This ADR is the constitutional root of the entire five-volume
(VII, XI, XII, XIII, XX) DRL governance framework.
Zone: Immutable
Amendment threshold: Founder decision + constitutional amendment process

---

## Domain ADRs (Zone: Stable)

*These govern specific domain decisions. They may evolve through governance
council review and founder approval, but not through engineering discretion alone.*

---

**ADR-009 — Memory and Media are permanently separate entities.**
Source: Volume V, Ch.4 ("Memory ≠ Media" declared as constitutional in that volume).
Implication: A Media entity holds evidence (bytes — a photo, a video, an audio
recording). A Memory entity holds meaning (significance, emotions, life_chapter,
tradition_id, tagged relationships). They reference each other but never merge into
one record. Any schema migration that adds a `media_url` field directly to the Memory
entity without maintaining a separate Media Layer violates this ADR.
Zone: Stable
Amendment threshold: Architecture Council review + founder approval

---

**ADR-010 — The canonical human entity is Person, not User.**
Source: Volume II, §3.1; Volume III, Ch.1.
Implication: "User" is rejected as an entity type. "Participant" is a role, not an
entity. All schemas, DomainEvents, APIs, and documentation must use Person-centric
language. Any new schema that introduces a `user_id` field where a `person_id` is
meant drifts from the canonical model and must be corrected before the relevant
migration is applied.
Zone: Stable
Amendment threshold: Architecture Council review + founder approval

---

**ADR-011 — The canonical community-scale event is a Gathering, not an Event.**
Source: Volume IV, Ch.1 ("A gathering is not an event").
Implication: "Event" is a broad logistical construct. ZUKA models Gatherings — entities
with a host, a community context, a lifecycle, and a capacity to generate Memory,
Tradition, and Trust capital. Any domain model, API endpoint, or UI label that uses
"event" where "gathering" is meant introduces conceptual drift. The DomainEvent type
name `GatheringCreated` is correct; `EventCreated` is not.
Zone: Stable
Amendment threshold: Architecture Council review + founder approval

---

**ADR-012 — The Knowledge Graph consists of exactly seven official graphs.**
Source: Volume VIII, Ch.7 (the reconciliation chapter that settled the count after
earlier versions proposed five, six, and seven graphs in different documents).
Graphs: Identity, Community, Memory, Trust, Opportunity, Tradition, Digital Presence.
Temporal is a cross-cutting dimension, not an eighth graph.
Implication: Any future graph proposal — e.g., a "Skills Graph" or "Commerce Graph" —
requires a formal Volume VIII Chapter-level extension via the Architecture Council,
not a unilateral engineering decision. Digital Presence (the seventh) is filed Dormant
pending privacy review and remains one of the seven regardless.
Zone: Stable
Amendment threshold: Architecture Council review + founder approval

---

**ADR-013 — Every external integration connects through a ConnectorContract.**
Source: Volume XIX, Ch.2-4 (the full Connector Framework).
Implication: No domain module may import or directly call an external system's SDK.
All external calls route through the Connector layer. A breaking change to an external
API produces a new ConnectorContract version, never a domain code change. The
five Docking Principle properties (loosely coupled, capability-based, reversible,
observable, versioned) are all required for every connector.
Zone: Stable
Amendment threshold: Architecture Council review + founder approval

---

**ADR-014 — Revocation of any trust decision is always a human act.**
Source: Volume XIII, Ch.3 (Anti-Fraud Architecture, REVOKED status); Volume XXI,
Ch.10, Commerce Integrity Rule 4 (ticket revocation is always human).
Implication: No automated fraud detection system, Agent, or Twin may directly set
any entity to a REVOKED, SUSPENDED, or permanently-excluded status. These systems
detect and flag; humans act. This applies across the trust domain (trust score
revocation), the ticketing domain (ticket REVOKED status), and the community domain
(member expulsion). Any automated pipeline that writes a terminal negative status
without a human identity_id in the `actioned_by` field violates this ADR.
Zone: Stable
Amendment threshold: Trust Council review + founder approval

---

## Operational ADRs (Zone: Adaptive)

*These govern operational decisions that may be updated through normal
Architecture Council review without requiring founder approval for every change.*

---

**ADR-015 — All significant actions emit immutable DomainEvents.**
Source: Volume II, Ch.8-9; Volume VII, Ch.6-8 (event sourcing mandate).
Implication: The append-only DomainEvent table is the source of truth for what has
happened on the platform. No business logic may mutate the current state of any entity
without first writing a DomainEvent to record that mutation. The DomainEvent table
never has UPDATE or DELETE operations against existing records.
Zone: Adaptive
Amendment threshold: Architecture Council review

---

**ADR-016 — Start with a modular monolith; extract only when triggers fire.**
Source: Volume XI, Ch.2-5 (the four Infrastructure Stages and their observable triggers).
Implication: Service extraction is trigger-driven, not calendar-driven. A stage
transition requires a documented trigger (per Volume XI, Ch.5) to have demonstrably
fired before the new infrastructure is built. "We should move to microservices because
that's best practice" is not a valid trigger.
Zone: Adaptive
Amendment threshold: Architecture Council review

---

**ADR-017 — Every capability activation requires a completed DRL Activation Contract.**
Source: Volume XX, Ch.10 (the complete nine-section DRL Activation Contract form).
Implication: No DRL-gated capability may be activated without all nine sections of the
Activation Contract completed and signed. Founder sign-off is specifically required for
DRL 4+ capabilities, CRL 'coordinating'+ capabilities, financial transaction capabilities,
and external B2B data consumers. Building a capability without completing the contract
first is not a shortcut — it is a governance violation.
Zone: Adaptive
Amendment threshold: Architecture Council review

---

**ADR-018 — Data quality controls detect aggressively and correct conservatively.**
Source: Volume XII, Ch.9 (the explicit restraint principle).
Implication: All five DataQualityCheck types (duplicate detection, missing field
detection, broken relationship detection, invalid timestamp detection, orphaned entity
detection) flag for human review. None auto-correct. Auto-merge is specifically
forbidden for Person entities. This is the data-layer expression of ADR-003
(Reality precedes AI) — a quality system that silently "corrects" data is a system
that silently rewrites reality.
Zone: Adaptive
Amendment threshold: Architecture Council review

---

## Experimental ADRs (Zone: Experimental)

*These govern the most nascent architectural decisions — the ones most likely
to evolve as the platform learns from real usage.*

---

**ADR-019 — AI Twins are representations, not chatbots.**
Source: Volume IX, Ch.1 (the structural distinction between a Twin, a Historian, a
Copilot, and a chatbot).
Implication: A Twin is a persistent, consent-gated, provenance-bound standing
representation of a specific entity. It is not a stateless conversational interface.
Any future product feature that presents a Twin as "chat with your AI" or "ask your
Twin anything" risks violating this ADR — the Twin's outputs are DRL-gated insights
with ProvenanceRecords, not open-ended conversational responses.
Zone: Experimental (the Twin architecture itself is new enough that its operational
definition may be refined by real-world usage)
Amendment threshold: Architecture Council review

---

**ADR-020 — Agent-to-Agent interaction is asynchronous only.**
Source: Volume X, Ch.12 (the explicit prohibition on synchronous Agent-to-Agent
invocation).
Implication: No Agent may call another Agent's function live, mid-operation, and
wait on the result. One Agent's output is data written to the shared graphs; another
Agent reads that data when it next executes. This prevents DRL-gating violations
(a more mature Agent borrowing confidence from a less mature one via a live call)
and preserves the two-hop provenance guarantee.
Zone: Experimental
Amendment threshold: Architecture Council review

---

## Project-Level Adaptive ADRs (021+) — standalone files, indexed here

*Unlike ADR-001–020 (derived from a pre-existing canonical volume citation),
ADR-021 onward were discovered and authored during real build/review sessions
(S007, S012, S018, the 2026-07-05 compendium brainstorm) — Adaptive Zone,
Architecture Council review, which for this solo project is the founder.
Each lives as a full standalone file at `docs/architecture/ADR-0NN.md`; this
registry indexes them rather than duplicating their content, to avoid two
copies drifting apart. This catch-up entry itself was prompted by
`zuka-reviewer` finding the registry (and the quick-reference catalog) had
gone stale at ADR-020 while four more ADRs already existed on disk.*

```
ADR-021  Caching Strategy: Discovery Feed vs Transactional Paths
         → docs/architecture/ADR-021.md
ADR-022  SECURITY DEFINER Helper to Break Circular RLS Recursion
         → docs/architecture/ADR-022.md
ADR-023  Offline Scanner Mode: Manifest Design & Frozen Hashing Contract
         → docs/architecture/ADR-023.md
ADR-024  [candidate only — dev-tooling governance, raised by R011-F1;
          not yet drafted as a file]
ADR-025  [candidate only — Event-Store PII erasure, raised by R002-F2;
          not yet drafted as a file — see
          docs/concepts/CONCEPT_EventStore_PII_Erasure.md]
ADR-026  Ticket-Payment Settlement & NPS-Act Aggregator Posture
         → docs/architecture/ADR-026.md (PROPOSED; product cut-line accepted
           2026-07-04; compliance gate open pending Ugandan counsel)
ADR-027  MVP Boundary & Dormant-Capability Enforcement (Mechanical Layer)
         → docs/architecture/ADR-027.md
ADR-028  Flutter App Shell: State Management, Navigation & Project Structure
         → docs/architecture/ADR-028.md (ACCEPTED 2026-07-05 — Riverpod +
           go_router + feature-first; companions in docs/flutter/)
```

Zone: Adaptive (all six ratified/proposed entries above)
Amendment threshold: Architecture Council review — solo project, founder disposes

---

## ADR Amendment Log

*All amendments to any ADR are recorded here, append-only.*

```
No amendments to date.
Version 1.0 — Initial registry established.
Sources: Zuka-Bible 2 ADR Catalog (ADRs 001-008) +
         canonical volumes II-XXII (ADRs 009-020).

Version 1.1 — 2026-07-05. Added the "Project-Level Adaptive ADRs (021+)"
section indexing ADR-021/022/023/026/027 (standalone files that existed on
disk but were never registered here) and naming ADR-024/025 as reserved
candidate numbers, not yet drafted. Discovered by zuka-reviewer during the
2026-07-05 compendium brainstorm (docs/compendium/BRAINSTORM_2026-07-05_
Findings.md §5.2). No existing entry's content was altered — this is
catch-up indexing, not an amendment to any prior ADR's substance.

Version 1.2 — 2026-07-05. Added ADR-028 (Flutter App Shell: state
management, navigation, project structure) — the first Flutter-specific
ADR, produced by a zuka-designer + zuka-reality-checker audit of the
compendium's Part IV/V Flutter proposals. PROPOSED, awaiting founder review
of the state-management alternative (Riverpod vs Bloc). Companion living
references (design tokens, RPC-client contract, the Flutter dormant/
deferred register) live in the new docs/flutter/ directory, not duplicated
here.
```

---

## Adding a New ADR

Any significant architectural decision not already covered by this registry should be
proposed as a new ADR using this process (per Zuka-Bible 2, Book I Part D, Ch.20-21):

```
STEP 1 — PROPOSAL
  State the decision in one sentence.
  Identify which Zone it belongs to (Immutable/Stable/Adaptive/Experimental).
  Identify which canonical volume(s) it is derived from, or note it is new.

STEP 2 — IMPACT ANALYSIS
  Which existing ADRs does this interact with?
  Which Dormant Features does this affect?
  Which canonical volumes need updating?

STEP 3 — REVIEW
  Immutable/Stable: Architecture Council review minimum
  Operational/Experimental: Architecture Council review minimum
  Immutable: additionally requires founder decision

STEP 4 — REGISTRY ENTRY
  Add to this registry in the appropriate Zone section.
  Add to the Amendment Log.
  Update relevant canonical volume(s) to cross-reference the ADR number.
```

---

> **ZUKA Architecture Bible**
> Architecture Decision Record Registry
> Constitutional source: Zuka-Bible 2, Book I Part D, Ch.21
> Operational source: Volumes II–XXII
> Built in Kampala. Built for Africa. Built for the world.
