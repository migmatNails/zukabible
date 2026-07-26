# ZUKA Unified Architecture Bible
# BOOK X — PLATFORM & INFRASTRUCTURE

> **Constitutional Authority:** Level 6 — Platform Kernel
> **Source:** Volume XI (Infrastructure Architecture); Volume XIX (Docking Architecture);
>              Zuka-Bible 2 Book X

---

## The Infrastructure Constitution (Volume XI, Ch.1)

```
PRINCIPLE 1  MODULAR MONOLITH FIRST
             Optimise for speed of learning, not distributed complexity

PRINCIPLE 2  DOMAIN ISOLATION
             No domain leakage. Eight domains. Strict module boundaries.

PRINCIPLE 3  EVENT-FIRST ARCHITECTURE
             All meaningful actions emit DomainEvents. (ADR-015)

PRINCIPLE 4  DRL GOVERNANCE
             No infrastructure capability may activate beyond data maturity. (ADR-008)
```

---

## Four Infrastructure Stages (Volume XI, Ch.4-5)

**Not time-bound. Observable-trigger-bound.**

```
STAGE 1 — MVP
  Flutter + Modular Monolith + PostgreSQL + Redis + Object Storage

STAGE 2 — GROWTH (trigger: sustained PostgreSQL contention or cross-domain query limits)
  + Event Bus + Search Layer + Read Models (CQRS-adjacent)

STAGE 3 — SCALE (trigger: DRL 2+ across multiple domains; graph traversals impractical)
  + Event Streaming + Data Lake + Knowledge Graph (dedicated store)

STAGE 4 — PLATFORM (trigger: multiple Twin/Agent types at DRL 3+)
  + Community OS + Trust OS + Opportunity OS + AI Infrastructure
```

---

## The Eight Domains (Volume XI, Ch.3)

```
Identity | Community | Gathering | Memory |
Trust | Opportunity | Payments | Notifications
```

No domain may read or write another domain's tables directly.
Cross-domain access: through defined interfaces only.
Knowledge Graph, Twins, Agents: cross-cutting views, NOT additional domains.

---

## The ISS Docking Principle (Volume XIX)

All external systems connect via ConnectorContract. Never through direct coupling.

Five properties every connector must have:
```
LOOSELY COUPLED    — no domain imports external SDK directly
CAPABILITY-BASED   — enumerable list of named capabilities only
REVERSIBLE         — disableable without domain code changes
OBSERVABLE         — every call logged as observability event
VERSIONED          — independently versioned from platform releases
```

**Three committed connectors:**
```
SafeBoda (mobility)      — 2 capabilities: request_ride_quote, deep_link_to_booking
Flutterwave (payments)   — 3 capabilities: initiate_payment_intent, verify_payment,
                            initiate_payout
Africa's Talking (SMS)   — 2 capabilities: send_sms_otp, send_sms_notification
```

**Pending approval:** Smile Identity (biometric PIA required first),
Mapbox, FCM, Sentry, Cloudflare.

**Gated by Founder Review Queue:** Knowledge Federation connectors.

---

## The Founder Rule (Volume XI, Ch.13)

Every feature proposal must define:
```
Domain         — which of the eight?
Events         — which DomainEvent types?
Data Contracts — entity schemas?
DRL Level      — which ladder level targeted?
Graph Edges    — which of the seven graphs touched?
Future Dependencies — Dormant Register cross-references?
```

If unanswered: feature rejected before sprint planning.

---

*Source: Volumes XI, XIX; Zuka-Bible 2 Book X*
