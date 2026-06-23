# ZUKA Architecture Bible

> *"A capability may exist architecturally long before it exists operationally. Preserve the vision. Build only what reality currently requires."*
>
> — Constitutional Reconciliation Audit, v1.0

---

## What This Repository Is

This repository is the **single source of truth and architectural governance** for ZUKA — a real-time entertainment discovery, access, and intelligence platform built Africa-first, launching in Kampala, Uganda.

It is not a codebase. It is not a product spec. It is the constitutional and architectural foundation from which all product decisions, database schemas, API contracts, and engineering choices must be derived.

Every document here is authoritative. When in doubt about any architectural decision, this repository governs.

---

## The Platform in One Sentence

ZUKA tells you what's alive in your city right now, lets you tune in from anywhere, and gets you there safely.

**Market trajectory:** Kampala → Uganda → East Africa → Africa → International  
**Stage:** Pre-development / Active architecture phase  
**Founded:** 2025, Kampala, Uganda  
**Founder:** Kiyingi James

---

## How to Navigate This Repository

The Bible is organised into five layers. Read them in order if you are new. Jump directly if you know what you need.

### Layer 0 — Start Here

| File | Purpose |
|------|---------|
| `README.md` | This file. Entry point and navigation. |
| `ZUKA_Master_Table_of_Contents.md` | Complete file index with one-line descriptions |
| `ZUKA_Constitutional_Glossary.md` | Canonical definitions for every platform term |
| `ZUKA_Constitutional_Reconciliation_Audit_Report_v1.0.md` | The audit that diagnosed contradictions and set the consolidation path |
| `ZUKA_Future_Session_Handover.md` | Current work queue and recommended build order |

### Layer 1 — Constitutional Foundation (Book I)

The supreme layer. Defines purpose, philosophy, principles, and non-negotiable constraints. Nothing overrides this layer.

| File | Contents |
|------|---------|
| `ZUKA_Book_I_Foundational_Constitution_Part_A.md` | Vision, Mission, Human Participation Doctrine |
| `ZUKA_Book_I_Foundational_Constitution_Part_B.md` | Philosophy and design principles |
| `ZUKA_Book_I_Foundational_Constitution_Part_C.md` | Constitutional entities and their definitions |
| `ZUKA_Book_I_Foundational_Constitution_Part_D.md` | Constitutional rules and constraints |
| `ZUKA_Book_I_Foundational_Constitution_Part_E.md` | Constitutional amendments process |

### Layer 2 — Domain Architecture (Books II–VI)

The structural layer. Defines what the platform is made of.

| File | Contents |
|------|---------|
| `ZUKA_Book_II_Canonical_Domain_Model.md` | Core domain entities and their relationships |
| `ZUKA_Book_III_Identity_Civilization.md` | Identity OS — who participants are |
| `ZUKA_Book_IV_Memory_Civilization.md` | Memory OS — what participation leaves behind |
| `ZUKA_Book_V_Relationship_Civilization.md` | Relationship OS — how entities connect |
| `ZUKA_Book_VI_Community_Civilization.md` | Community OS — how groups form and persist |

### Layer 3 — Intelligence & Trust (Books VII–IX)

| File | Contents |
|------|---------|
| `ZUKA_Book_VII_Trust_and_Opportunity_Systems.md` | Trust OS, Opportunity Engine |
| `ZUKA_Book_VIII_Intelligence_Architecture.md` | AI layer, recommendation systems |
| `ZUKA_Book_IX_Temporal_and_Knowledge_Architecture.md` | Time-based intelligence, knowledge graphs |

### Layer 4 — Platform & Governance (Books X–XII)

| File | Contents |
|------|---------|
| `ZUKA_Book_X_Platform_and_Infrastructure.md` | Technical stack, infrastructure principles |
| `ZUKA_Book_XI_Experience_Constitution.md` | UX doctrine, interaction principles |
| `ZUKA_Book_XII_Governance_Survival_Evolution.md` | Platform governance, evolution rules |

### Layer 5 — Engineering Specifications

The operational layer. Concrete, implementation-ready artifacts.

| File | Contents |
|------|---------|
| `ZUKA_Readiness_Constitution.md` | The three-axis readiness system: DRL (data) · BRL (build) · CRL (autonomy) — master activation governance |
| `ZUKA_CRL_Constitution.md` | The Capability Readiness Ladder — permitted autonomy from observing to coordinating |
| `ZUKA_Trust_Engine_Specification.md` | Trust signal catalogue, score computation, tier thresholds, capability gates |
| `ZUKA_Canonical_Schema_Pack.md` | Field-level entity schemas for all MVP domains |
| `ZUKA_Event_Catalog.md` | Domain event catalog with payload contracts |
| `ZUKA_API_Registry.md` | API endpoint catalogue |
| `ZUKA_Canonical_Data_Dictionary.md` | Every data field defined with type, constraints, consent tier |
| `ZUKA_Schema_Registry.md` | Schema group registry and evolution rules |
| `ZUKA_Service_Registry.md` | Platform services and their responsibilities |
| `ZUKA_Service_Dependency_Registry.md` | Service dependency graph |
| `ZUKA_Technical_Reference_Manual.md` | Full technical reference |
| `ZUKA_Reference_Architecture.md` | System architecture diagrams and decisions |

### Layer 6 — Registries & Appendices

Cross-cutting reference material.

| File | Contents |
|------|---------|
| `ZUKA_Appendix_A_Master_Entity_Registry.md` | Every entity in the platform |
| `ZUKA_Appendix_B_Master_Domain_Registry.md` | Every domain |
| `ZUKA_Appendix_C_Master_Ownership_Registry.md` | Who owns what |
| `ZUKA_Appendix_D_Master_Event_Registry.md` | All domain events |
| `ZUKA_Appendix_E_Master_Graph_Registry.md` | Relationship graph |
| `ZUKA_Appendix_F_Master_Capital_Registry.md` | Participation capital types |
| `ZUKA_Appendix_G_Stewardship_Registry.md` | Data stewardship |
| `ZUKA_Appendix_H_Provenance_Registry.md` | Origin tracking rules |
| `ZUKA_Appendix_I_Explainability_Registry.md` | AI explainability requirements |
| `ZUKA_Appendix_J_Dormant_Capability_Registry.md` | Architecturally defined but not yet built |

### Layer 7 — Governance Documents

| File | Contents |
|------|---------|
| `ZUKA_ADR_Catalog.md` | All Architecture Decision Records |
| `ZUKA_Authority_Matrix.md` | Who can decide what |
| `ZUKA_Final_Authority_Matrix_Expanded.md` | Expanded authority mapping |
| `ZUKA_Constitutional_Amendment_Registry.md` | History of constitutional changes |
| `ZUKA_Governance_Charter_Registry.md` | Governance rules |

### Layer 8 — Roadmaps & Planning

| File | Contents |
|------|---------|
| `ZUKA_MVP_Extraction_Blueprint.md` | What is in-scope for Phase 1 |
| `ZUKA_Capability_Activation_Roadmap.md` | When capabilities activate by DRL stage |
| `ZUKA_Multi_Phase_Implementation_Roadmap.md` | Full multi-phase delivery plan |
| `ZUKA_Production_Expansion_Backlog.md` | Queued work not yet scheduled |

---

## The MVP Scope (Phase 1 — Kampala)

The MVP proves three things and only three things:

1. A venue can go live and be discoverable within 60 seconds
2. A user can find something happening near them right now with zero friction
3. A ticket can be bought in under 3 taps

**In scope for Phase 1:**
- Identity (phone + OTP, Google/Apple OAuth)
- Events and Gatherings (create, publish, Go Live)
- Discovery (home feed, search, heat map)
- Ticketing (Mobile Money + card, Smart Bucket QR)
- Trust Foundations (venue verification, basic fraud prevention)
- Communities (basic follow system)
- Memories (check-in, Pulse Points earn)

**Deferred (architecturally defined, operationally dormant):**
- AI Twins
- Federation
- Civilization Systems
- Audio/Video streaming (Phase 2)
- Gift ticketing (Phase 2)
- Scanner Pro (Phase 2)
- B2B data products (Phase 3)

---

## Architectural Hierarchy

```
Layer 0 — Constitution
  Master Constitution · Canonical Language · DRL Doctrine · CRL Doctrine

Layer 1 — Core Operating Systems
  Identity OS · Relationship OS · Memory OS · Trust OS

Layer 2 — Collective Systems
  Community OS · Attention OS

Layer 3 — Economic Systems
  Opportunity OS · Economic Systems

Layer 4 — Infrastructure
  Data · Security · Anti-Fraud · Governance · Resilience

Layer 5 — Applications
  Events · Venues · Ticketing · Communities · Digital Gatherings
```

---

## Key Architectural Principles (Non-Negotiable)

**1. Invisible Sophistication**  
Internal complexity must increase. External complexity must decrease. The user never sees the system; they see the result.

**2. Relationship-First**  
Network value lives in relationships, not in profiles or content. Every feature is evaluated by whether it strengthens or weakens meaningful relationships.

**3. Participation Over Profiles**  
The platform prioritises what people do above what people claim. Behavioural data outranks self-reported data.

**4. Triple Readiness Governance — DRL · BRL · CRL**  
Every capability is governed by three independent readiness ladders. All three bind; the minimum governs, per the master inequality `manifestation_depth ≤ min(DRL_permits, BRL_built, CRL_earned)`.

- **DRL (Data Readiness Ladder):** Is the *data* mature enough? Assessed **per entity, per operation** — the effective DRL of any operation is the minimum across all entities in its scope, so the sparse side always governs. Stages: Schema → Collection → Patterns → Confidence → Automation → Intelligence.
- **BRL (Build Readiness Ladder):** Is the capability *built and operationally proven*? Stages: Defined → Designed → Prototype → Operational Pilot → Controlled Production → Constitutional.
- **CRL (Capability Readiness Ladder):** Is it *permitted to act on participants*, and how far? Stages: Observing → Learning → Advising → Assisting → Coordinating. This is the autonomy brake — intelligent capabilities observe long before they advise, and anti-fraud is pinned permanently at Observing (it flags to humans, never punishes autonomously).

No axis may skip a stage. Advancement is evidence-gated, never pressure-gated. See `ZUKA_Readiness_Constitution.md` (the unified system) and `ZUKA_CRL_Constitution.md` (the autonomy ladder in full).

**5. Memory OS as Moat**  
The memory layer is a long-term strategic differentiator. It accumulates value passively with every interaction and becomes more defensible over time.

**6. Trust Scarcity**  
Trust is not inflated. It is earned through participation and protected through governance. Trust visibility is internal; only human-readable translations are exposed to users.

**7. Africa-Native, Not Africa-Adapted**  
Mobile Money is a primary rail, not a fallback. Informal events are first-class citizens, not edge cases. Local languages and neighbourhood-level geography are built-in, not retrofitted.

**8. Free Events Are Always Free**  
Permanently. Publicly stated. Non-negotiable.

---

## The Colour System (Information Architecture)

Colours are information, not decoration. This is constitutional.

| Colour | Meaning |
|--------|---------|
| 🔴 Live Coral `#FF4D4D` | Live right now — reserved, never decorative |
| 🟡 Ember Gold `#F5A623` | Starting soon / primary action |
| 🟢 Kampala Forest `#1A3A2A` | Tonight / scheduled |
| ⚪ Neutral | Free / general info |

---

## Technology Stack

| Concern | Technology |
|---------|-----------|
| Mobile | Flutter (iOS + Android, single codebase) |
| Web | Next.js (SSR for event page SEO) |
| Backend / Database | Supabase (PostgreSQL + Realtime + Auth + Storage) |
| Real-time | Supabase Realtime subscriptions |
| Payments | Flutterwave (cards + Mobile Money) |
| Notifications | Firebase Cloud Messaging + Africa's Talking (SMS) |
| Maps | Mapbox |
| Error tracking | Sentry |
| Email | Resend |
| CDN / Edge | Cloudflare |
| Identity verification | Smile Identity |
| Audio fingerprinting | ACRCloud (Phase 2) |

---

## Governance Rules for This Repository

**1. This repository is the source of truth.** No architectural decision made outside this repository is binding until it is reflected here.

**2. Constitutional documents (Books I–XII) require founder review before any change.**

**3. Engineering specifications (Layer 5) may be updated by the architect but must reference the constitutional document they derive from.**

**4. Dormant capabilities (Appendix J) are not deleted. They are preserved architecturally and activated when the DRL threshold is reached.**

**5. Every Architecture Decision Record (ADR) must be logged in `ZUKA_ADR_Catalog.md` with date, decision, rationale, and consequences.**

**6. Contradictions discovered between documents are logged in `ZUKA_Constitutional_Amendment_Registry.md` and resolved before implementation begins.**

---

## Current Work Queue

See `ZUKA_Future_Session_Handover.md` for the active queue.

Recommended build order for production-grade specifications:

1. ✅ README (this file)
2. ✅ Canonical Schema Pack (`ZUKA_Canonical_Schema_Pack.md`)
3. ✅ Event Catalog (`ZUKA_Event_Catalog.md`)
4. ✅ Readiness Constitution — DRL · BRL · CRL (`ZUKA_Readiness_Constitution.md`)
5. ✅ CRL Constitution — autonomy ladder (`ZUKA_CRL_Constitution.md`)
6. ✅ Trust Engine Specification (`ZUKA_Trust_Engine_Specification.md`)
7. Opportunity Engine specification
7. Identity OS specification
8. Memory OS specification
9. Community OS specification
10. API Specifications
11. Database Blueprint (Supabase migrations)
12. Security Blueprint
13. Deployment Blueprint

---

*ZUKA — Built in Kampala. Built for Africa. Built for the world.*  
*The city just came alive.*
