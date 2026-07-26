# ZUKA Unified Architecture Bible
# APPENDIX J — DORMANT CAPABILITY REGISTRY

> **Authority:** Zuka-Bible 2, Book I Part C (Chapter 14 — Dormant Capability Doctrine)
>                + Volume VII, Part XIII (Feature Seed Architecture)
>                + Volume XX, Ch.11 (Graduation Procedure)
> **Source:** Canonical zuka-docs/ZUKA_Architecture_Bible_Dormant_Feature_Register.md
>              (the primary register — this file is the root-level mirror)
> **Status:** Living document — entries graduate out when DRL/CRL thresholds clear;
>              entries are added via the Founder Rule process (ADR-017)
> **Canonical source:** Always read zuka-docs/ version first; this file may lag by
>                        one update cycle

---

## Dormant Capability Doctrine (Zuka-Bible 2, Book I Part C, Ch.14)

```
14.1  Architected does not mean activated.

14.2  A capability may be constitutionally valid while remaining inactive.

14.4  A capability may activate only when:
       1. DRL permits (Volume VII, XI, XII, XIII — four confirmed equivalent ladders)
       2. CRL permits (Volume XIV — capability readiness, independently earned)
       3. Governance permits (relevant Council review per Governance Council Framework)
       4. Trust safeguards exist (Volume XIII Part V anti-fraud; ADR-014)
       5. Experience safeguards exist (Volume XXII notification and dark-pattern rules)

14.5  No capability may activate solely because technology exists.
       Readiness must precede activation.
```

---

## Graduation Procedure (Volume XX, Ch.11)

Every entry in this register graduates via the same six-step procedure:

```
1. Run DataReadinessAssessment per Volume XX, Ch.8
   for domain [X] in scope [city/market]
2. Confirm ALL FIVE DIMENSIONS clear DRL [Y] thresholds
   per Volume XX, Ch.7's per-domain table
3. Complete DRL Activation Contract per Volume XX, Ch.10
4. Obtain founder sign-off if Section E/F requires it
5. Fire FeatureSeedActivated DomainEvent
6. Remove this entry from the Register; update all referencing volumes
```

---

## Current Registry

*All entries are at DRL 0-1 and CRL: Observing at this writing.*
*No capability has yet graduated. This is correct for a pre-launch platform.*

---

### AI SYSTEMS — HISTORIANS (7 types)

| Capability | Governing Volume(s) | Current DRL/CRL | Graduation Precondition |
|-----------|---------------------|-----------------|------------------------|
| Memory Historian | V Ch.23, VII Ch.25 | DRL 0, CRL: observing | Memory domain DRL 2+; sustained presence criterion (Volume XIV, Ch.4) |
| Family Historian | V Ch.23, VII Ch.25 | DRL 0, CRL: observing | Family Vault DRL 2+; Family Twin at DRL 2+ |
| Community Historian | V Ch.23, VII Ch.25 | DRL 0, CRL: observing | Community domain DRL 2+; Community Archive populated |
| Relationship Historian | V Ch.23, VII Ch.25 | DRL 0, CRL: observing | Relationship domain DRL 2+; ≥ 3 years relationship history |
| Legacy Historian | V Ch.23, VII Ch.25 | DRL 0, CRL: observing | At least one Memorialised Person with populated Legacy Vault |
| Personal Historian | VII Ch.25 | DRL 0, CRL: observing | Identity domain DRL 2+; ≥ 12 months personal participation history |
| City Historian | VII Ch.25 | DRL 0, CRL: observing | City aggregate DRL 2+; k-anonymity threshold met |

---

### AI SYSTEMS — TWINS (6 types)

| Capability | Governing Volume(s) | Current DRL/CRL | Graduation Precondition |
|-----------|---------------------|-----------------|------------------------|
| Personal Twin | IX Ch.6, XIV | DRL 0, CRL: observing | Per-entity DRL 2+; TwinConsentRecord confirmed; synthesis cycle honest |
| Family Twin | IX Ch.7, XIV | DRL 0, CRL: observing | Family Vault DRL 2+; strategic_layer governance consent |
| Community Twin | IX Ch.8, XIV | DRL 0, CRL: observing | Community domain DRL 2+; governance-layer consent |
| Gathering Twin (single) | IX Ch.9, XIV | DRL 0, CRL: observing | Specific Gathering DRL 2+; host consent |
| Opportunity Twin | IX Ch.10, XIV | DRL 0, CRL: observing | Dual consent (both parties); Trust domain DRL 2+ |
| City Twin | IX Ch.11, XIV | DRL 0, CRL: observing | City aggregate DRL 2+; k-anonymity met; no individual consent required |

---

### AI SYSTEMS — AGENTS (5 types)

| Capability | Governing Volume(s) | Current DRL/CRL | Graduation Precondition |
|-----------|---------------------|-----------------|------------------------|
| Trust Agent | X Ch.3, XIV | DRL 0-1, CRL: observing | Trust domain DRL 2+; ≥ 500 Person entities with trust events |
| Opportunity Agent | X Ch.7, XIV | DRL 0, CRL: observing | Trust Agent DRL 2+ first; Opportunity domain DRL 2+ |
| Community Agent | X Ch.6, XIV | DRL 0, CRL: observing | Multiple communities DRL 2+; Community Genome DRL 2+ |
| Historian Agent | X Ch.4, XIV | DRL 0-1, CRL: observing | Least mature underlying Historian at DRL 2+ |
| Tradition Agent | X Ch.8, XIV | DRL 0-1, CRL: observing | ≥ 3 SEEDED/ACTIVE Traditions; Tradition domain DRL 2+ |

---

### AI SYSTEMS — COPILOTS (4+ types)

| Capability | Governing Volume(s) | Current DRL/CRL | Graduation Precondition |
|-----------|---------------------|-----------------|------------------------|
| Community Copilot (Church/Alumni/Club/Family) | VI Part XVIII, XIV | DRL 0, CRL: learning | Community domain DRL 3+; CRL 'assisting' for proactive surfacing |
| Gathering Copilot (Organiser/Family/Community) | IV Part XIV, XIV | DRL 0, CRL: learning | Gathering domain DRL 3+; CRL 'advising' minimum |

---

### KNOWLEDGE GRAPH SYSTEMS

| Capability | Governing Volume(s) | Current DRL/CRL | Graduation Precondition |
|-----------|---------------------|-----------------|------------------------|
| Semantic Enrichment Engine | VIII Ch.6 | DRL 0-1 | DRL 2 across relevant entity population; Tradition domain DRL 2+ |
| Community Intelligence Layer (full) | VIII Ch.21 | DRL 0-1 | DRL 2 for growth/tradition questions; DRL 3+ for leader identification |
| Opportunity Intelligence Layer (matching) | VIII Ch.22 | DRL 0-1 | DRL 3+; ProvenanceRecord on 100% of outputs; Opportunity Agent active |
| Community Genome (composite view) | VIII Ch.26, XVI | DRL 0 | Community Trust Profile DRL 2+; Community Knowledge Layer DRL 2+ |
| Digital Presence Graph (full specification) | VIII Ch.14 | DRL 0 | ADDITIONALLY gated on dedicated privacy review — not pure DRL gate |
| Knowledge Federation connectors | VIII Ch.24, XIX Ch.9 | DRL 0 | ADDITIONALLY gated on Founder Review Queue decision per Volume XVIII Ch.5 |

---

### OPPORTUNITY SYSTEMS (4 Engines)

| Capability | Governing Volume(s) | Minimum CRL | Graduation Precondition |
|-----------|---------------------|-------------|------------------------|
| Sponsorship Engine (matching) | XV Ch.4, XIV | 'assisting' | DRL 3+; Opportunity Agent Mode 2 active; never autonomous |
| Mentorship Engine (matching) | XV Ch.5, XIV | 'advising' | DRL 3+; Opportunity Agent Mode 2 active |
| Scholarship Engine (automated distribution only) | XV Ch.6, XIV | 'coordinating' | DRL 4+; fully-objective criteria; sustained dispute-free track record |
| Creator Economy Engine (matching) | XV Ch.7, XIV | 'advising' | DRL 3+; Opportunity Agent Mode 2 active |

---

### COMMUNITY SYSTEMS

| Capability | Governing Volume(s) | Current DRL/CRL | Graduation Precondition |
|-----------|---------------------|-----------------|------------------------|
| Community Treasury / Wallet | VI Part XV, XVI | DRL 0, CRL: learning | Community economic transaction volume; regulatory review per market |
| Community Knowledge Layer (UI/surfacing) | VIII Ch.17, XVI Ch.3 | DRL 0 | Schema exists (Volume XVI); UI: DRL 2+; CRL 'advising' for passive |
| Community App Store | VII Appendix A | DRL 0 | Governance model not yet specified; requires dedicated volume first |
| Community Maturity Score (public-facing) | VII Appendix A | DRL 0 | Community Genome DRL 2+; full 7-module OS active; CRL 'assisting' |

---

### MEMORY SYSTEMS

| Capability | Governing Volume(s) | Current DRL/CRL | Graduation Precondition |
|-----------|---------------------|-----------------|------------------------|
| Anniversary Engine | V Ch.21, XXII Ch.3 | DRL 0-1 | Memory domain DRL 2+; 6-sensitivity-rule infrastructure built and tested first |
| Memory Federation (user-initiated) | VII Appendix A, VIII Ch.25 | DRL 0 | Per-provider API consent review; Memory Vault sufficiently populated; privacy review per Vol XVIII |
| Tradition Detection (full lifecycle UI) | IV Ch.11, VII Ch.14 | DRL 0-1 | SEEDED-state suggestions partially buildable at MVP; AT_RISK/LAPSED UI requires DRL 2+ |
| Memory Time Machine (NL query) | V Ch.26 | DRL 0 | Personal Historian active (DRL 5); 3+ years Memory history |
| Family Archive UI (full) | V Ch.16, VI Part XXI | DRL 0 | Family Twin DRL 3+; family governance consent |
| Community Archive UI (full, beyond basic album) | V Ch.17, VI Ch.23 | DRL 0-1 | Community DRL 2+; Archivist role assigned and active |
| Data Portability — memories/communities/histories scopes | XII Ch.11 | DRL 0 | Schema fields exist (Volume XII, Ch.13); meaningful content for export first |

---

### TRUST SYSTEMS

| Capability | Governing Volume(s) | Current DRL/CRL | Graduation Precondition |
|-----------|---------------------|-----------------|------------------------|
| Trust OS (full) | XI Part V, XIII | DRL 0-1 | Trust Agent DRL 3+; Stage 3+ infrastructure |
| Graph Topology Anomaly Detection | XIII Ch.7-8 | DRL 0 | Real graph density — sufficient relationship edges for topology analysis |
| Content Pattern Match Detection | XIII Ch.7-8 | DRL 0 | Real review volume — sufficient reviews for pattern analysis above noise |
| Collusion Detection (Opportunity-specific) | XIII Ch.7-8, X Ch.7 | DRL 0 | Opportunity Agent Mode 2 active (DRL 3+) |

---

### INFRASTRUCTURE SYSTEMS

| Capability | Governing Volume(s) | Trigger (not DRL) | Graduation Precondition |
|-----------|---------------------|-------------------|------------------------|
| Event Bus / Read Models (Stage 2) | XI Ch.5-6 | Stage 1→2 trigger | Sustained PostgreSQL read/write contention; or first cross-domain query that defeats standard indexing |
| Knowledge Graph as dedicated store (Stage 3) | XI Ch.8 | Stage 2→3 trigger | Multi-hop traversals impractical against PostgreSQL CTEs |
| Stage 3 Event Streaming / Data Lake | XI Ch.4-5 | Stage 2→3 trigger | Multiple domains at DRL 2+ |
| Community OS / Trust OS / Opportunity OS as dedicated infra (Stage 4) | XI Part V | Stage 3→4 trigger | Multiple Twin/Agent types at DRL 3+ across meaningful entity populations |

---

## Founder Review Queue
*(Items requiring founder decision — not pure DRL gate; not in the Dormant Register proper)*

```
ITEM                              FIRST RAISED   STATUS         PROCESS
Knowledge Federation              VIII Ch.24     OPEN           Volume XVIII, Ch.5
  (Google/Meta/WhatsApp/TikTok)                                 Legal Brief → Impact
                                                                 Assessment → Decision

Full Docking / Connector          XI Ch.10       OPEN — partial Volume XIX provides
  Framework (beyond 3 committed                  ConnectorContract exists;     architectural
  connectors)                                    full Capability               shape; founder
                                                 taxonomy owed                 decision unlocks
                                                                               full build

Legal & Governance per-market     XVIII Part V   OPEN           Engage qualified legal
  addenda (Addendum 001 Uganda                                   counsel in Uganda before
  DPPA 2019 as first)                                           significant user growth

Platform-Identity brand decision  VII Appendix A CLOSED as      Constitutional Foundation
  ("Human Gatherings" vs.                        architectural  Ch.1 adopts "Human
  "Human Participation           question        Participation Infrastructure"
  Infrastructure")               (Vol XVII)      as architectural identity.
                                                 Brand/marketing decision
                                                 remains founder's alone.
```

---

## Compendium Cross-Reference (2026-07-05) — Kampala-MVP-stage concrete triggers

`docs/compendium/ZUKA_Constitutional_Compendium.md` (Part VI, the "ZPE"
reframe) used four shorthand names — **Opportunity Engine, Digital Twin
Engine, Memory Engine, Intelligence Engine** — that are **not canonical
Registry terms**. They are non-canonical shorthand invented by that
document, each corresponding to one or more rows already in this Registry.
Per CLAUDE.md's canonical-language governance, they are NOT added here as
new entries; this section maps them to their real Registry rows and adds a
concrete, **numeric, Kampala-pre-launch-stage** trigger — supplementing, not
replacing, the volume-cited Graduation Preconditions above, which remain
authoritative. Source: `BRAINSTORM_2026-07-05_Findings.md` §3
(`zuka-reality-checker` and `zuka-growth`, converging independently), folded
in per ADR-027 Decision §4.

| Compendium shorthand | Maps to (Registry rows above) | Added concrete pre-launch trigger |
|---|---|---|
| "Opportunity Engine" | Opportunity Agent · Opportunity Twin · Sponsorship/Mentorship/Creator-Economy Engines (OPPORTUNITY SYSTEMS) | ≥50 repeat organisers (≥3 gatherings each) AND high `VendorPresenceActivated` volume — a real two-sided pool, not a cold-start guess. |
| "Digital Twin Engine" | Gathering Twin (single) · City Twin | Multiple concurrent `scanner_session`s per gathering (multi-gate operation) AND organisers requesting live capacity/queue visibility mid-event. |
| "Memory Engine" | Memory Time Machine · Personal Historian · Family/Community Archive UI (full) | Median attendee has ≥3 check-ins over ≥6–12 months — a real corpus. `AttendanceSeedRecord` is already being emitted; only the surface stays dormant until then. |
| "Intelligence Engine" | Semantic Enrichment Engine · Community Intelligence Layer · Opportunity Intelligence Layer | Feed impression→click logging exists (it doesn't yet — that's the actual first step) AND DRL reaches 2+. Recommending on DRL-1 data is forbidden regardless of demand. |

---

*Source: zuka-docs/ZUKA_Architecture_Bible_Dormant_Feature_Register.md (primary)*
*Constitutional basis: Zuka-Bible 2, Book I Part C, Ch.14*
*Operational basis: Volume VII Part XIII, Volume XX Ch.11*
*Zuka-Bible 2, Appendix J (original stub — now substantive)*
