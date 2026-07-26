# ZUKA Architecture Bible
# Unified Dormant Feature Register

> **Status:** Living document — entries graduate out when their domain crosses its
>              DRL/CRL threshold; entries may be added via the Founder Rule process
>              (Volume XI, Ch.13; Volume XII, Ch.12)
> **Supersedes:** Volume VII Appendix A, Volume VIII Part X, Volume IX/X DRL
>                  accounting chapters, Volume XIV-XVI per-engine DRL notes
>                  (all now consolidated here; those sections remain in their
>                  respective volumes as historical context)
> **Governing Principles:** Volume VII's Data-Led Evolution Principle and Feature
>                  Seed Architecture (Ch.22-24); Volume XIV's CRL governing
>                  inequality: manifestation_depth ≤ min(DRL_permits, CRL_earned)
> **First consolidated:** Volume XVII's mandate, Item 3 (Volume XVII, Ch.6)

---

## How to Use This Register

**Entering:** A feature is added here when it is named in the Bible as real and worth designing for, but not yet buildable because its domain's DRL threshold is unmet. The entry names: what the feature is, which volume specifies it, its current DRL, and the specific precondition required for graduation.

**Graduating:** An entry is removed when its domain's DataReadinessAssessment (Volume VII, Part XII) has genuinely, measurably crossed its stated threshold — not when a roadmap date arrives, not under commercial pressure, not for any other reason. When a feature graduates, it moves to an active specification within its governing volume's next revision, and this register is updated with a graduation note rather than a silent deletion.

**Not in this register:** Features held for founder/legal review (Knowledge Federation, the platform-identity brand decision) — these are not Dormant Features; they are governance decisions this Bible correctly routes outside its own authority. They are listed separately in the Founder Review Queue at the end of this document.

---

## Register Table

```
FEATURE                          GOVERNING    CURRENT   PRECONDITION FOR
                                 VOLUME(S)    DRL/CRL   GRADUATION
───────────────────────────────────────────────────────────────────────────────

── AI SYSTEMS ─────────────────────────────────────────────────────────────────

All 7 Historians (Memory,        VII, XIV,     DRL 0     Platform-wide DRL 2+
Family, Community,               V Ch.23,      CRL:      across relevant
Relationship, Legacy,            VII Ch.25     observing entities; sustained
Personal, City)                                          presence criterion met
                                                         (Vol XIV Ch.4 State 1→2)

All 6 Twin types                 IX, XIV       DRL 0     Per-entity DRL 2+;
(Personal, Family,               per type      CRL:      TwinConsentRecord
Community, Gathering,                          observing confirmed; synthesis
Opportunity, City)                                       cycle (Vol IX Ch.14)
                                                         produces honest output

All 5 Agents                     X, XIV        DRL 0-1   Per-operation effective
(Trust, Opportunity,             per agent     CRL:      DRL ≥ 2 (minimum across
Community, Historian,                          observing all entities in scope);
Tradition)                                               Trust Agent earliest
                                                         candidate; Opportunity
                                                         Agent last (depends on
                                                         Trust Agent output)

Community Copilots               VI Part XVIII, DRL 0    Community-level DRL 3+
(Church, Alumni, Family,         XIV Ch.4       CRL:     + CRL 'assisting' for
Club)                                           learning proactive surfacing;
                                                         CRL 'advising' for
                                                         passive/pull-only surfacing

Gathering Copilots               IV Part XIV,   DRL 0    Gathering-level DRL 3+;
(Gathering, Family,              XIV Ch.4       CRL:     single-occurrence
Organiser, Community,                           learning Gatherings unlikely to
Church)                                                  reach threshold before
                                                         Tradition-series Twins do

Semantic Enrichment Engine       VIII Ch.6      DRL 0-1  DRL 2 across the
                                                CRL:     relevant entity population;
                                                learning Tradition Engine Feature
                                                         Seed data sufficient for
                                                         pattern recognition
                                                         (Vol VIII Ch.6)

── KNOWLEDGE GRAPH SYSTEMS ─────────────────────────────────────────────────────

Community Intelligence Layer     VIII Ch.21     DRL 0-1  DRL 2 for growth/tradition
(full — all 4 questions)         XIV            CRL:     questions; DRL 3+ for
                                                learning "future leaders" (stricter
                                                         bar, VIII Ch.21 note);
                                                         "future leaders" sub-
                                                         question held to DRL 0
                                                         pending separate review

Opportunity Intelligence Layer   VIII Ch.22     DRL 0-1  DRL 3+ + working
(full matching)                  XIV            CRL:     ProvenanceRecord per
                                 XV             advising match; Opportunity Agent
                                                         Mode 2 active; Trust
                                                         Agent output available
                                                         (Vol X Ch.12 handoff)

Community Genome                 VIII Ch.26     DRL 0    Constituent parts
(composite view)                 XVI            CRL:     (Community Trust,
                                                learning Community Knowledge
                                                         Layer) themselves at
                                                         DRL 2+

Digital Presence Graph           VIII Ch.14     DRL 0    ADDITIONALLY gated on
(full specification)             XIV            CRL:     dedicated privacy review
                                                n/a      (Vol VIII Ch.14) —
                                                         not pure DRL gate;
                                                         privacy review FIRST

── OPPORTUNITY SYSTEMS ─────────────────────────────────────────────────────────

Sponsorship Engine               XV Ch.4        DRL 0    DRL 3+ (Vol VIII Ch.22
(matching computation)           XIV            CRL:     provenance bar); Opportunity
                                                advising Agent Mode 2 active;
                                                         CRL 'assisting' min

Mentorship Engine                XV Ch.5        DRL 0    DRL 3+; Opportunity Agent
(matching computation)           XIV            CRL:     Mode 2 active;
                                                advising CRL 'advising' min

Scholarship Engine               XV Ch.6        DRL 0    Criteria-automated path:
(automated distribution          XIV            CRL:     DRL 4+ + CRL
path specifically)                              coordinating 'coordinating' + sustained
                                                         dispute-free track record
                                                         (Vol XIV Ch.4 State 4→5)
                                                         Committee-review path:
                                                         buildable at MVP (schema
                                                         only; see Vol XV Ch.9)

Creator Economy Engine           XV Ch.7        DRL 0    DRL 3+; Opportunity Agent
(matching computation)           XIV            CRL:     Mode 2 active;
                                                advising CRL 'advising' min

Opportunity Emergence            VII Ch.20      DRL 0-1  Trust Agent producing
Detection                        X Ch.7 Mode 1           meaningful TrustEdge
                                                         output (Vol X Ch.3);
                                                         sufficient co-attendance
                                                         density in graph

── COMMUNITY SYSTEMS ─────────────────────────────────────────────────────────

Community Treasury /             VI Part XV     DRL 0    Community-level economic
Community Wallet                 XVI            CRL:     transaction volume
                                                learning justifying closed-loop
                                                         instrument; regulatory
                                                         review per market

Community Knowledge Layer        VIII Ch.17     DRL 0    Schema exists (Vol XVI
(Knowledge module UI,            XVI Ch.3               Ch.3); UI/surfacing:
search, proactive surfacing)                   CRL:     DRL 2+ in contributing
                                                advising community; CRL 'advising'
                                                         for passive; CRL 'assisting'
                                                         for proactive

Community App Store              VII Appendix A DRL 0    Undefined — governance
                                                         model for third-party
                                                         community apps not yet
                                                         specified anywhere in
                                                         the Bible; requires
                                                         its own volume before
                                                         a DRL threshold can
                                                         even be stated

Community Maturity Score         VII Appendix A DRL 0    Community Genome at
(public-facing composite)                                DRL 2+; full 7-module
                                                         Community OS active for
                                                         target communities;
                                                         CRL 'assisting'
                                                         (Vol XIV Ch.4)

── MEMORY SYSTEMS ─────────────────────────────────────────────────────────────

Anniversary Engine               V Ch.21        DRL 0    Personal Timeline:
                                                         sufficient temporal span
                                                         (>1 year of Memory data);
                                                         sensitivity infrastructure
                                                         (Vol V Ch.21 rules) built
                                                         and tested BEFORE any
                                                         notification is sent

Memory Federation                VII Appendix A DRL 0    Individual user-initiated
(user's own photos from          VIII Ch.25              import only (not
Google Photos / Facebook /                               platform-wide Knowledge
Instagram, user-initiated)                               Federation); requires
                                                         per-provider API consent
                                                         review; Memory Vault
                                                         (Vol V) sufficiently
                                                         populated for import
                                                         to be meaningful to user

Tradition Detection (full        IV Ch.11       DRL 0-1  Gathering/Community DRL
lifecycle state machine UI;      V Ch.22                 2+; sufficient occurrence
AT_RISK/LAPSED/REVIVED           VII Ch.14               history for meaningful
surfacing)                                               detection; SEEDED-state
                                                         suggestion already
                                                         partially buildable at
                                                         MVP per Vol VII Ch.14

Memory Time Machine              V Ch.26        DRL 0    Personal Historian active
(natural language query)         VII Part XIV            (DRL 5); 3+ years of
                                                         Memory data with
                                                         meaningful emotional tag
                                                         density

Family Archive /                 V Ch.16        DRL 0    Schema exists; UI/full
Family Vault UI                  VI Part XXI             feature: Family Twin at
                                                         DRL 3+; Family
                                                         governance consent
                                                         (Vol IX Ch.3, Family)

Community Archive UI             V Ch.17        DRL 0    Community Archive schema
(full — beyond basic album)      VI Ch.23                exists; Community-level
                                                         DRL 2+; Archivist role
                                                         assigned and active

Data Portability —               XII Ch.11      DRL 0    Meaningful Memory Vault
'memories', 'communities',                               and Community Archive
'histories' export scopes                                content exists for a
                                                         user to want to export;
                                                         schema fields exist at
                                                         MVP per Vol XII Ch.13

── TRUST SYSTEMS ─────────────────────────────────────────────────────────────

Trust OS (full)                  XI Part V      DRL 0-1  Trust Agent DRL 3+;
                                 XIII                    Stage 3+ infrastructure
                                                         (Vol XI Ch.4-5); Trust
                                                         Timeline UI requiring
                                                         Trust Agent meaningful
                                                         output

Anti-Fraud: Graph Topology       XIII Ch.7-8    DRL 0    Real graph density
Anomaly Detection                                        (sufficient relationship
                                                         and co-attendance edges
                                                         for topology patterns
                                                         to be statistically
                                                         meaningful)

Anti-Fraud: Content Pattern      XIII Ch.7-8    DRL 0    Real review volume
Match Detection                                          (sufficient reviews for
                                                         pattern analysis to
                                                         exceed noise threshold)

Anti-Fraud: Collusion            XIII Ch.7-8    DRL 0    Opportunity Agent
Detection (Opportunity-          X Ch.7                  Mode 2 active (itself
match specific)                                          DRL 3+)

── INFRASTRUCTURE SYSTEMS ──────────────────────────────────────────────────────

Event Bus / Read Models          XI Ch.6        DRL n/a  Stage 1→2 trigger
(Stage 2 infrastructure)         XI Ch.5                 (Vol XI Ch.5): sustained
                                                         read/write contention
                                                         in PostgreSQL, or first
                                                         cross-domain query
                                                         pattern that defeats
                                                         standard indexing

Knowledge Graph as               XI Ch.8        DRL n/a  Stage 2→3 trigger
dedicated store (Stage 3)        VIII Ch.7                (Vol XI Ch.5): multi-hop
                                                         traversals genuinely
                                                         impractical against
                                                         PostgreSQL CTEs

Stage 3 Event Streaming /        XI Ch.4-5      DRL n/a  Stage 2→3 trigger
Data Lake                                                same as above; multiple
                                                         domains independently
                                                         at DRL 2+

Community OS / Trust OS /        XI Part V      DRL n/a  Stage 3→4 trigger
Opportunity OS as dedicated      XI Ch.11                (Vol XI Ch.5): multiple
infrastructure investment                                Twin/Agent types at
(Stage 4)                                                DRL 3+ across meaningful
                                                         entity populations

── DIGITAL GATHERINGS ──────────────────────────────────────────────────────────

Virtual / Digital Gathering      VII Appendix A DRL 0    DigitalGathering entity
as equal citizen to Physical     IV Ch.6                  schema (partially named
Gathering                                                in VII Appendix A, not
                                                         yet fully specified);
                                                         product/UX specification
                                                         owed before DRL threshold
                                                         can be meaningfully stated

Community GDP                    VII Appendix A DRL 0    Trust OS, Opportunity OS,
                                                         and Community Treasury
                                                         all active at meaningful
                                                         scale; economic model
                                                         not yet specified in
                                                         this Bible
```

---

## Founder Review Queue

The following items are NOT in the Dormant Feature register because they are not features — they are governance or brand decisions that require explicit founder-level action before any Feature Seed can even be defined. They are tracked here for visibility, not for DRL-driven activation.

```
ITEM                             FIRST RAISED    STATUS

Knowledge Federation             VIII Ch.24      OPEN — requires founder
(Google/Meta/WhatsApp/TikTok/                    decision + legal counsel per
Government/Schools/Churches                      market; no Feature Seed
as data sources)                                 until that review occurs

Platform-Identity reframe        VII Appendix A  CLOSED AS ARCHITECTURAL
("OS for Human Coordination"                     QUESTION (Vol XVII Ch.5);
vs. "OS for Human Gatherings")                   remains OPEN as a brand/
                                                 marketing decision;
                                                 ZUKA.md / Vol I remains
                                                 authoritative until founder
                                                 decides otherwise

Infrastructure & Ecosystem /     XI Ch.10        OPEN — full Connector
Docking full specification       VII Appendix A  Framework and Capability
(Docking Principle)                              taxonomy owed as own
                                                 volume; SafeBoda /
                                                 payment-processor minimum
                                                 ConnectorContract exists
                                                 per Vol XI Ch.9

Legal & Governance               Multiple        OPEN — institutional-scale
Architecture volume              (III, IV, V,    disputes (Vol VI Ch.27-32)
                                 VI, XIII)       not resolved by Vol XIII's
                                                 individual-conclusion
                                                 Appeals mechanism; requires
                                                 dedicated volume + legal
                                                 counsel per market
```

---

## Version History

```
v1.0 — Initial consolidated register
       Source: Volumes VII Appendix A, VIII Part X, IX/X DRL
       accounting, XIV-XVI per-engine DRL notes
       Consolidation mandated by Volume XVII, Chapter 6, Item 3
```

---

> **ZUKA Architecture Bible**
> Unified Dormant Feature Register
> Built in Kampala. Built for Africa. Built for the world.
