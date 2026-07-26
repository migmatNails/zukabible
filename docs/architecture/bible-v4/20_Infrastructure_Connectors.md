# ZUKA BIBLE V4 — VOLUME 20
# Infrastructure, Storage & Connectors

> **Part VI — Platform** · **Authority Level 5**
> Synthesises: Canonical Volume XI, BLACK XXXII-XXXIV, Volume XIX (Docking)
> ADR-013: Every external integration connects through a ConnectorContract
> ADR-016: Start with a modular monolith; extract only when triggers fire

---

## Chapter 1 — The Infrastructure Constitution

```
PRINCIPLE 1  MODULAR MONOLITH FIRST   Optimise for speed of learning, not
             distributed complexity.
PRINCIPLE 2  DOMAIN ISOLATION         No domain leakage. Strict boundaries.
PRINCIPLE 3  EVENT-FIRST               All meaningful actions emit DomainEvents.
PRINCIPLE 4  DRL GOVERNANCE            No infra capability activates beyond data
             maturity.
```

---

## Chapter 2 — The Eight Domains

```
Identity · Community · Gathering · Memory ·
Trust · Opportunity · Payments · Notifications

No domain reads or writes another's tables directly. Cross-domain access goes
through defined interfaces. Knowledge Graph, Twins, Agents are cross-cutting
views, NOT additional domains.
```

---

## Chapter 3 — The Four Infrastructure Stages (trigger-bound, not time-bound)

```
STAGE 1 — MVP
  Flutter + Modular Monolith + PostgreSQL + Redis + Object Storage

STAGE 2 — GROWTH
  Trigger: sustained PostgreSQL contention OR cross-domain query limits
  Add: Event Bus + Search Layer + Read Models (CQRS-adjacent)

STAGE 3 — SCALE
  Trigger: DRL 2+ across multiple domains; graph traversals impractical
  Add: Event Streaming + Data Lake + Knowledge Graph (dedicated store)

STAGE 4 — PLATFORM
  Trigger: multiple Twin/Agent types at DRL 3+
  Add: Community OS + Trust OS + Opportunity OS + AI Infrastructure
```

---

## Chapter 4 — Storage Architecture (BLACK XXXIV)

```
Operational DB   PostgreSQL (Supabase) — system of record
Event Store      Immutable historical record (append-only)
Object Storage   Photos, videos, documents (tiered hot/warm/cold)
Search Layer     Indexes and retrieval (Stage 2+)
Knowledge Graph  Relationships (views at MVP; dedicated store at Stage 3)

Data Lifecycle: Create → Validate → Store → Analyze → Archive.
History is an asset. Never design storage as an afterthought.
```

---

## Chapter 5 — The Recommended Tech Stack

```
Mobile:          Flutter (iOS + Android, single codebase)
Web:             Next.js (SSR for event-page SEO)
Database:        PostgreSQL via Supabase (+ Realtime, Auth, Storage)
Cache:           Redis
Payments:        Flutterwave (cards + MoMo) + Iotec (Uganda MoMo direct)
SMS / OTP:       Africa's Talking
Identity Verify: Smile Identity (pending PIA + Addendum 001)
Push:            Firebase Cloud Messaging
Maps:            Mapbox (heat map, pins)
Audio streaming: WebRTC (board-out) + HLS (low-bandwidth fallback)
Video sync:      WebSocket playback synchronisation
Audio fingerprint: ACRCloud or AudD
Error tracking:  Sentry
Email:           Resend
CDN / Security:  Cloudflare
Mobility:        SafeBoda (Zuka Ride connector)
```

---

## Chapter 6 — The ISS Docking Principle (Volume XIX)

All external systems connect via ConnectorContract — never direct coupling.

```
Five properties every connector must have:
  LOOSELY COUPLED   No domain imports an external SDK directly
  CAPABILITY-BASED  Enumerable named capabilities only
  REVERSIBLE        Disableable without domain code changes
  OBSERVABLE        Every call logged as an observability event
  VERSIONED         Independently versioned from platform releases
```

---

## Chapter 7 — The Three Committed Connectors

```
SafeBoda (mobility)      request_ride_quote · deep_link_to_booking
Flutterwave (payments)   initiate_payment_intent · verify_payment · initiate_payout
Africa's Talking (SMS)   send_sms_otp · send_sms_notification

Pending approval: Smile Identity (biometric PIA required first), Mapbox, FCM,
  Sentry, Cloudflare.
Gated by Founder Review Queue: Knowledge Federation connectors (Google, Meta,
  WhatsApp, TikTok).
```

---

## Chapter 8 — Zuka Ride Connector (ZUKA.md §12, §32)

```
Revenue model: charge the DRIVER, not the company. Drivers pay a flat monthly
listing fee (UGX 20,000) to appear at event-exit times. No per-trip commission.
Compounds with user growth: more users → more valuable listing → more drivers
pay → better coverage → safer exits → more user trust.

SafeRoute safety: Share My Route · Arrive Safe · Driver Report · Emergency Alert.
```

---

## Chapter 9 — The MVP Service Boundaries (BLACK XXXII)

```
Identity Service · Event Service · Ticketing Service · Venue Service ·
Community Service · Memory Service · Notification Service ·
Payment Service · Analytics Service

At MVP these are modules within the monolith, not separate services. The
boundaries are logical (enforced by domain isolation), extracted into services
only when Stage 2-3 triggers fire.
```

---

## Chapter 10 — API Constitution (BLACK XXXIII)

```
Versioned APIs · Backward Compatibility · Contract Testing · Auditability.
APIs are products, not implementation details.

Canonical Event Envelope: event_id · event_type · actor_id · timestamp ·
  source · payload · version.
All endpoints: Person-centric (not user-centric), Gathering-centric, with
  temporal envelopes and ProvenanceRecords on intelligence outputs.
```

---

## Chapter 11 — Observability

```
Every domain emits: Logs · Metrics · Events · Traces.
Always answerable: What happened? Why? When? Who?
(Observability events are distinct from DomainEvents and have no
ProvenanceRecord — they are operational, not intelligence conclusions.)
```

---

## Chapter 12 — The Founder Rule

```
Every feature proposal defines: Domain · Events · Data Contracts · DRL Level ·
Graph Edges · Future Dependencies. If unanswered, rejected.
```

---

> **Volume 20 — Infrastructure, Storage & Connectors**
> Modular monolith first. Dock, never couple. Scale by trigger, not by calendar.
