# ZUKA Infrastructure Technology Guide
## Supabase-First Architecture — and When Nginx, Redis, RabbitMQ, Kafka, Docker, Kubernetes & Elasticsearch Become Migration Targets

> **Document Authority:** Engineering Reference · Subordinate to ZukaBibleV4
> **Scope:** Infrastructure capability decisions mapped to ZUKA's eight platform layers
> **Governing Principle:** Every technology choice must serve meaningful human participation — not engineering novelty.
> **Stack Law (SOUL.md / ZukaBibleV4 Vol 20):** Flutter · **Supabase/PostgreSQL** · Flutterwave · Africa's Talking · FCM · Mapbox · Sentry · Cloudflare · Smile Identity. *No stack drift. No new dependencies without a formal ADR.*

---

## 0. Read This First — The Reframe

An earlier draft of this guide mapped eight self-hosted infrastructure
technologies (Nginx, Redis, RabbitMQ, Kafka, Docker, Kubernetes, Elasticsearch,
GraphQL via Node/Express) onto ZUKA and recommended deploying most of them from
day one. **That contradicted three things ZUKA has already locked:**

1. **The locked stack is Supabase-first.** Node/Express, MySQL, and MongoDB are
   not in the stack. Introducing them is stack drift, which the SOUL file forbids
   without a formal ADR.
2. **ADR-016 says "start with a modular monolith; extract only when triggers
   fire."** Deploying Nginx + Redis + RabbitMQ + Kafka + Elasticsearch + K8s at
   MVP is the opposite of that — it is the over-engineering trap that sank the
   earlier Genesis Omega design.
3. **A solo founder in Kampala cannot operate eight distributed systems** and
   should not try. Every hour spent running Zookeeper is an hour not spent
   getting 30 venues to launch.

**The correction:** Supabase already provides — natively, in one managed
platform — a capable equivalent of nearly everything the eight technologies do,
at the scale ZUKA's MVP actually operates at. This guide now maps each
**capability** to its **Supabase-native implementation at MVP**, and treats each
of the eight technologies as a **migration target** with one precise, observable
trigger that would justify adopting it later.

> **The discipline:** Don't ask "where does Kafka fit in ZUKA?" Ask "what
> capability do we need, what does Supabase give us today, and what single
> observed signal would tell us we've outgrown it?"

---

## Table of Contents

1. [The Capability Map: Supabase-Native at MVP](#1-the-capability-map)
2. [Traffic Gateway & TLS — *(Nginx capability)*](#2-traffic-gateway--tls)
3. [Speed & Caching — *(Redis capability)*](#3-speed--caching)
4. [Async & Background Work — *(RabbitMQ capability)*](#4-async--background-work)
5. [The Immutable Event Log — *(Kafka capability)*](#5-the-immutable-event-log)
6. [Local Dev & Environment Consistency — *(Docker capability)*](#6-local-dev--environment-consistency)
7. [Orchestration & Scaling — *(Kubernetes capability)*](#7-orchestration--scaling)
8. [Discovery, Search & Geo — *(Elasticsearch capability)*](#8-discovery-search--geo)
9. [The API Layer — *(GraphQL capability)*](#9-the-api-layer)
10. [How It Fits Together: The Supabase-First Data Flow](#10-the-supabase-first-data-flow)
11. [The Scale Migration Ladder](#11-the-scale-migration-ladder)
12. [Technology Decision Authority](#12-technology-decision-authority)

---

## 1. The Capability Map

Each row is a **capability** ZUKA needs. The MVP column is what you build on
**today**, entirely within the locked stack. The migration column is the
self-hosted technology you'd move to **only** when the trigger fires.

| Capability | Supabase-Native at MVP | Migration Target | Trigger to Migrate |
|---|---|---|---|
| Traffic gateway, TLS, static | **Supabase API gateway + Cloudflare** (both in stack) | Nginx / dedicated LB | Self-hosting compute outside Supabase |
| Speed / hot-path cache | **Postgres + Cloudflare edge cache + Realtime** | Redis / Valkey | Measured p95 read latency breaches budget under load |
| Async / background jobs | **Supabase Queues (pgmq) + Edge Functions + pg_cron** | RabbitMQ | Queue depth / throughput exceeds pgmq comfort at sustained load |
| Immutable event log | **Append-only Postgres event table + Realtime + Outbox** | Kafka | Multi-consumer replay + cross-region streaming needed (Stage 3) |
| Local dev consistency | **Supabase CLI (already containerised) + Docker** | Docker (custom services) | A service exists that Supabase can't host |
| Orchestration / scaling | **Supabase managed platform** | Kubernetes | You self-host compute and need multi-node orchestration |
| Search / discovery / geo | **Postgres FTS (tsvector) + pg_trgm + PostGIS** | Elasticsearch / OpenSearch | Relevance/scale needs exceed Postgres FTS (Stage 3) |
| API layer | **PostgREST (auto REST) + pg_graphql (native GraphQL) + Realtime** | Custom Apollo/Node GraphQL | A resolver need exists that pg_graphql + RPC can't express |

**The governing inequality for all of these (ADR-016):** a technology may only be
introduced when it solves a *real, observed* problem at ZUKA's *current* scale.
Until then, the Supabase-native column is not a compromise — it is the correct
architecture.

---

## 2. Traffic Gateway & TLS
### *(the Nginx capability)*

**What ZUKA needs:** every request authenticated and rate-limited, TLS
everywhere (Mobile Money callbacks require verified HTTPS), static assets served
fast on 3G, and a shield in front of application logic.

### Supabase-Native at MVP

- **TLS + gateway:** Supabase fronts every Postgres/PostgREST/Edge Function call
  with a managed API gateway (Kong) over enforced HTTPS. You write zero proxy
  config. MoMo webhooks hit a Supabase Edge Function URL — already a verified
  HTTPS endpoint.
- **Static assets + edge:** **Cloudflare** (already in the locked stack) is the
  CDN and edge cache for Event thumbnails, venue images, and the Flutter web
  build. Cloudflare also provides the WAF, DDoS protection, and gzip/Brotli
  compression the Africa baseline needs (ZukaBibleV4 Vol 16, Ch 10) — at the
  edge, closer to Kampala users than any origin Nginx.
- **Rate limiting (anti-fraud first line):** enforce per-IP / per-token limits at
  **Cloudflare** (edge, before traffic reaches your DB) and per-Person limits in
  Edge Functions / RLS policies for the `/scan` and `/payment` paths. This feeds
  the Trust OS velocity-anomaly substrate (Vol 11, Ch 9) without a self-hosted
  proxy.

### Migrate to Nginx when

You move application compute *off* Supabase onto your own VMs/containers (Stage
3-4). At that point a dedicated reverse proxy / load balancer in front of your
own fleet becomes necessary. **Not before** — running Nginx in front of Supabase
at MVP adds an operational hop that Cloudflare already covers better.

---

## 3. Speed & Caching
### *(the Redis capability)*

**What ZUKA needs:** the City Pulse strip, the Go Live crowd meter, and the
Smart Bucket QR scan (≤ 400 ms, Vol 16) must be fast, and hot reads must not
hammer the database.

### Supabase-Native at MVP

- **Live updates without polling — Supabase Realtime, not Redis pub/sub.** The
  crowd meter, City Pulse counts, and "a venue you follow just went live" are
  **Realtime subscriptions** over Postgres changes. The client subscribes; the
  DB pushes. This is the natural fit for ZUKA's live features and removes the
  need for a separate pub/sub system at MVP.
- **Crowd meter state** lives in a small Postgres table updated on each check-in,
  with a `pg_cron` sweep to expire stale "PACKED" statuses (the TTL behaviour the
  Redis design wanted). Realtime broadcasts the level change.
- **QR hot path (≤ 400 ms):** the Smart Bucket match is a single indexed Postgres
  query (token → valid ticket for this scanner's event window). At MVP volumes a
  well-indexed Postgres lookup is comfortably inside 400 ms. Token rotation
  happens in the same transaction as the state-machine transition — atomic, no
  cache-coherency problem to manage.
- **Feed cache:** the ranked discovery feed is a Postgres **materialized view**
  (or a cached Edge Function response at Cloudflare), refreshed by `pg_cron` /
  on live-status change — not a Redis-cached blob.
- **Rate-limit counters & velocity windows:** Postgres rows + a `pg_cron` sweep,
  or Cloudflare's edge rate limiting. Sorted-set-style velocity windows
  (50 check-ins / 10 min from one device) are expressible as windowed SQL over
  the append-only event table.

### Migrate to Redis (or Valkey) when

You *measure* — not guess — that p95 read latency on a specific hot path breaches
its budget under real load, and the cause is database read pressure that
indexing and materialized views can't fix. Redis then becomes a read-through
cache for that *specific* path. Durability note still holds: **Redis would be a
speed layer, never a source of truth** — every meaningful state change stays in
Postgres and emits a DomainEvent (ADR-015), consistent with Temporal Truth
(Vol 05).

---

## 4. Async & Background Work
### *(the RabbitMQ capability)*

**What ZUKA needs:** ticket checkout must return in < 30 s while downstream work
(MoMo confirmation, receipt, fraud signal, trust ledger, memory seed) happens
asynchronously. Gift-ticket expiry, payout initiation, and notification fan-out
must run in the background with guaranteed delivery and retries.

### Supabase-Native at MVP — **Supabase Queues (pgmq)**

Supabase ships **Supabase Queues**, a Postgres-native durable message queue
built on the `pgmq` extension, with **guaranteed, exactly-once delivery within a
visibility window**, message archival, and observability — zero extra
infrastructure. This covers the RabbitMQ use cases directly:

- **Payment workflow:** checkout enqueues a `payment.confirm` message → an Edge
  Function (or `pg_cron`-scheduled worker) consumes it → verifies the MoMo
  webhook → advances the ticket state machine (`PAYMENT_PENDING → ISSUED`) →
  rotates the QR token → enqueues a notification message. MoMo's USSD callbacks
  are inherently async; a durable queue is the right shape, and pgmq provides it.
- **Notification fan-out:** one `notification.dispatch` message; consumers send
  via **FCM** and **Africa's Talking SMS** (each in the locked stack). The
  Notification Constitution batching rules (Vol 16, Ch 8) are enforced in the
  consumer.
- **Memory seeds:** `AttendanceSeedCreated` enqueues a job; the consumer creates
  the Memory entity and schedules the post-event prompt, honouring grief-
  sensitivity rules (Vol 10, Ch 10).
- **Gift expiry & retries:** pgmq **visibility timeouts** give you retry-on-
  failure for free; a `pg_cron` job scans for expired gift escrow (48-72 h,
  Vol 14, Ch 9) and transitions `GIFT_PENDING → EXPIRED`, returning the ticket to
  the donor. (This is the dead-letter/TTL behaviour, done in Postgres.)
- **Payout initiation:** post-event, a queued job verifies `ENDED` state,
  computes deductions, and initiates the Flutterwave transfer — never inline.

**Why this is better at MVP than RabbitMQ:** the queue lives *inside* the same
Postgres transaction boundary as your data, so "write the ticket and enqueue the
receipt" is atomic. With an external broker, that becomes a distributed-
transaction problem (the very problem the Outbox pattern exists to paper over).

### Migrate to RabbitMQ when

Sustained queue throughput or fan-out topology outgrows what pgmq handles
comfortably (a Stage 2-3 signal: consistently deep backlogs, or many independent
consumer groups with complex routing). Until you see that in metrics, pgmq is the
right tool.

---

## 5. The Immutable Event Log
### *(the Kafka capability)*

**What ZUKA needs:** ADR-015 — "all significant actions emit immutable
DomainEvents." The Event Store (Vol 04, Ch 7) is append-only. Multiple
subsystems (Trust, Relationship, Memory, Analytics) must independently process
the same events. Tradition detection needs long-window replay over months.

### Supabase-Native at MVP — Append-Only Table + Realtime + Outbox

- **The event store is a Postgres table** with no UPDATE/DELETE grants — append-
  only enforced at the database privilege level, which is *stronger* than a
  convention in application code. This is the literal implementation of Temporal
  Truth (Vol 05).
- **Multi-consumer fan-out via the Outbox + Realtime:** each significant state
  change writes its row and an event in the **same transaction** (the Outbox
  pattern — exactly-once, no dual-write race). Consumers react via **Realtime**
  subscriptions or by draining a pgmq topic. The `GatheringWentLive` example —
  push notification, discovery re-index, city-pulse increment, trust signal,
  event-store append — all fire from the one committed event, each as an
  independent consumer, exactly as the Kafka design intended, without Kafka.
- **Replay** is a `SELECT … WHERE recorded_at > $cursor ORDER BY recorded_at` over
  the append-only table. A new consumer replays history by reading from the
  beginning. At MVP/Stage-2 volumes Postgres does this comfortably.
- **Tradition detection** (Vol 05, Ch 8) reads the same event table over a 90-day
  window; the Tradition Agent maintains its sliding-window state in Postgres.
- **DRL assessment & staleness** (Vol 05) are queries over the event table:
  no new events for N days → `staleness_risk = frozen` → DRL advancement blocked.

### Migrate to Kafka when

Stage 3 (the Bible's "Scale" infra stage): graph traversals and event volume
make single-Postgres replay impractical, **or** cross-region streaming is needed
for Knowledge-Graph continuity across East-Africa regions. Kafka's durable,
ordered, replayable, cross-region log is then the right backbone. The append-only
table makes this migration clean: the table *is* the event history Kafka would
replay, so you backfill topics from it.

---

## 6. Local Dev & Environment Consistency
### *(the Docker capability)*

**What ZUKA needs:** a solo founder must avoid "works on my machine." The dev
environment must match production.

### Supabase-Native at MVP — the Supabase CLI (already Docker)

- **`supabase start` runs the entire stack locally in Docker** — Postgres,
  PostgREST, Auth, Realtime, Storage, Edge runtime — pinned to the same versions
  as your hosted project. You already have containerised dev consistency without
  authoring a compose file.
- **Migrations are forward-only SQL** under version control (consistent with the
  forward-only migration discipline); `supabase db reset` rebuilds from scratch,
  guaranteeing prod parity.
- **Extensions enabled via migration:** `pgmq`, `pg_graphql`, `postgis`,
  `pg_cron`, `pg_net`, `pg_trgm` are enabled with a SQL migration so local and
  hosted match exactly.
- **Docker proper is still in your toolbox** — but for the *one* thing Supabase
  can't host (e.g. a custom audio-fingerprint worker for the streaming feature,
  Vol 13), you containerise *that single service*, not a 9-service compose stack.

> **Important correction from the earlier draft:** the old `docker-compose.yml`
> listed `postgres` **+ `mysql` + `mongodb`** plus Kafka, Elasticsearch, etc. as
> the dev environment. **Three databases for a pre-launch ticketing MVP violates
> the single-source-of-truth principle and the locked stack.** ZUKA is one
> Postgres (Supabase). There is no MySQL and no MongoDB.

### Migrate to a custom Docker footprint when

You need to run a service Supabase cannot host. Containerise that service alone.

---

## 7. Orchestration & Scaling
### *(the Kubernetes capability)*

**What ZUKA needs:** Go Live creates unpredictable spikes (a venue goes live,
2,000 notifications fire). The platform must absorb the spike and scale back.

### Supabase-Native at MVP — Managed Scaling, No K8s

- **Supabase scales the database and connection pooling** (Supavisor) for you.
  Edge Functions scale **per-invocation automatically** — a notification spike
  scales out without a HorizontalPodAutoscaler because there are no pods to
  autoscale; the function platform handles concurrency.
- **The Go Live spike** is absorbed by: Realtime fan-out (managed), a pgmq
  notification queue drained by auto-scaling Edge Functions, and Cloudflare
  absorbing the read flood on cached discovery assets. No cluster to operate.
- **Self-healing / HA** of the database (the append-only Event Store included)
  is Supabase's responsibility on the Pro tier (daily backups; PITR and read
  replicas available when needed).

### Migrate to Kubernetes when

You self-host compute (Stage 4, or a decision to leave managed Supabase) and need
multi-node orchestration, namespaced domain separation, and rolling deploys of
*your own* services. The domain namespacing the earlier draft sketched
(`zuka-core`, `zuka-events`, `zuka-commerce`, `zuka-trust`, `zuka-intel`) is a
fine *logical* design to keep — it just maps to schemas/modules at MVP, not to
K8s namespaces until you actually self-host.

---

## 8. Discovery, Search & Geo
### *(the Elasticsearch capability)*

**What ZUKA needs:** Vol 15 (Discovery & the Attention Economy) and MVP Proof 2
(zero-login discovery) require fast, fuzzy-tolerant (Ugandan spellings vary),
geo-aware search and ranking.

### Supabase-Native at MVP — Postgres FTS + pg_trgm + PostGIS

- **Full-text search:** Postgres `tsvector` / `tsquery` with GIN indexes gives
  ranked full-text search over gathering titles, venues, and vibe tags — no
  external engine. `to_tsvector` ranking covers the relevance baseline.
- **Fuzzy matching (Ugandan spelling variance):** the **`pg_trgm`** extension
  provides trigram similarity and fuzzy `ILIKE` — "Kololo" vs "Kololo" both
  match. This is exactly the fuzzy tolerance the Elasticsearch section wanted.
- **Geo discovery ("events near me"):** **PostGIS** gives true spatial queries —
  radius search, distance ranking, the 5 km Go Live notification fence — as
  indexed SQL. Geo-aware discovery is a Postgres extension, not a reason to add
  Elasticsearch.
- **The six-dimension ranking model** (Vol 15) is computed in SQL / an Edge
  Function over these indexes, then materialised. Mapbox (in-stack) renders the
  geo results.
- **Fairness-of-exposure / minimum-exposure constraints** (Vol 15) are ranking
  logic in the query, independent of the search engine underneath.

### Migrate to Elasticsearch / OpenSearch when

Stage 3: catalogue size and query concurrency make Postgres FTS relevance or
latency insufficient *as measured*, or you need advanced relevance tuning,
cross-cluster search for pan-African discovery, or heavy faceting at a scale
Postgres GIN can't serve. Until then, FTS + pg_trgm + PostGIS is the right tool
and keeps discovery in one system.

---

## 9. The API Layer
### *(the GraphQL capability)*

**What ZUKA needs:** precise, mobile-efficient data fetching on 3G (no
over-fetch), one-round-trip dashboards, and — critically — a schema that makes it
**structurally impossible to expose a raw trust score** (Vol 16, Ch 7).

### Supabase-Native at MVP — PostgREST + **pg_graphql** + Realtime

The earlier draft's GraphQL designs are largely **valid** — they just don't need
Node/Express/Apollo. Supabase provides the GraphQL layer natively:

- **`pg_graphql`** reflects a GraphQL API straight from your schema (exposed at
  the `graphql_public` schema). The `DiscoveryFeed`, `OrgDashboard`, and
  `ScanResult` queries from the original draft work against pg_graphql, resolved
  in-database — no resolver server to run or scale.
- **PostgREST** auto-generates a typed REST API from the same schema for the
  cases where REST is simpler (and it's what the Supabase client libraries speak
  by default).
- **Complex/transactional operations** (the 3-tap `purchaseTicket` mutation) are
  **Postgres RPC functions** (`SECURITY DEFINER` where appropriate) called over
  PostgREST — the purchase logic lives in one auditable DB function alongside the
  state machine, not in a separate API tier.
- **Realtime** is the subscription transport (the live half of what a GraphQL
  subscription would do).

#### The Trust-state rule, enforced at the database (an upgrade, not a port)

The original draft's best idea — *"the numeric trust score field simply does not
exist in the schema, so a client cannot request it"* — becomes **stronger** in
Supabase. Don't rely only on omitting a field:

- Expose Person data through a **view** that selects only `trust_state`
  (human-readable) and never the underlying dimensional scores.
- Enforce with **Row Level Security (RLS)**: the raw `trust_profile` table is
  readable by no client role at all; only the view (or a `SECURITY DEFINER`
  function) can derive the human-readable state. pg_graphql and PostgREST both
  honour RLS, so *every* API path — REST, GraphQL, Realtime — inherits the
  guarantee. The "Trusted Member, never a number" rule is enforced by the
  database, not by remembering to leave a field out of a resolver.

This is the kind of innovation the Bible rewards: a constitutional rule (trust
state is never exposed numerically) implemented as a **database-level invariant**
that no future API code can accidentally break.

### Migrate to a custom GraphQL server when

You hit a resolver requirement pg_graphql + RPC genuinely can't express (e.g.
stitching a third-party schema server-side). That's a real but distant trigger.

---

## 10. The Supabase-First Data Flow

```
                    FLUTTER APP / WEB CLIENT
                            │
                    ┌───────▼────────────┐
                    │     CLOUDFLARE      │  ← TLS edge, WAF, DDoS, rate limit,
                    │   CDN + Edge Cache   │    gzip/Brotli, static assets
                    └───────┬─────────────┘
                            │
                    ┌───────▼─────────────┐
                    │   SUPABASE GATEWAY   │  ← managed API gateway (Kong), HTTPS
                    └──┬──────────┬────────┘
                       │          │
        ┌──────────────▼──┐   ┌───▼─────────────────────────┐
        │  PostgREST /     │   │  Edge Functions             │
        │  pg_graphql      │   │  (MoMo webhooks, workers,    │
        │  (REST+GraphQL   │   │   queue consumers)           │
        │   from schema,   │   └───┬─────────────────────────┘
        │   RLS-enforced)  │       │
        └────────┬─────────┘       │
                 │                 │
        ┌────────▼─────────────────▼───────────────────────┐
        │                  POSTGRES (Supabase)             │
        │  • Canonical entities + RLS (trust-state views)  │
        │  • Append-only Event Store (no UPDATE/DELETE)     │
        │  • Supabase Queues (pgmq) — async + retries       │
        │  • FTS (tsvector) + pg_trgm + PostGIS — discovery │
        │  • pg_cron — scheduled sweeps (expiry, refresh)   │
        │  • pg_net — outbound calls from DB                │
        └────────┬─────────────────────────────────────────┘
                 │
        ┌────────▼─────────┐     pushes changes to
        │  SUPABASE        │ ───────────────────────►  subscribed clients
        │  REALTIME         │     (City Pulse, crowd meter, Go Live, live feed)
        └──────────────────┘

   In-stack external services:
   Flutterwave (payments) · Africa's Talking (SMS) · FCM (push) ·
   Mapbox (maps) · Smile Identity (KYC) · Sentry (errors)
```

### The Go Live Journey (end-to-end, Supabase-first)

1. Organiser taps **Go Live** → PostgREST RPC / pg_graphql mutation.
2. Cloudflare edge-checks rate limits; Supabase gateway terminates TLS.
3. A Postgres function advances the Gathering state **and** appends
   `GatheringWentLive` to the append-only Event Store **in one transaction**
   (Outbox — exactly once).
4. The crowd-meter row is initialised; `pg_cron` will expire it if heartbeats stop.
5. Consumers fire independently from the committed event:
   - **pgmq** notification job → Edge Function → **FCM** push + **Africa's
     Talking** SMS fallback.
   - Discovery indexes (FTS/PostGIS) reflect the new live status.
   - Trust signal for the organiser recorded (append-only).
6. **Realtime** pushes the live pin + crowd level to subscribed nearby clients;
   followers within 5 km (PostGIS fence) get the push.
7. New users open the app → **Cloudflare** serves cached assets, **pg_graphql**
   returns the live feed, **Realtime** streams the crowd level.
8. **≤ 60 seconds. MVP Proof 1 satisfied — zero self-hosted infrastructure.**

---

## 11. The Scale Migration Ladder

Each technology has **one observable trigger**. No migration happens on a
calendar date; it happens when a metric crosses a line. This is ADR-016 made
concrete. (Infra stages per ZukaBibleV4 Vol 20, Ch 3.)

| Stage | Trigger (observed, not guessed) | What you adopt | What you leave behind |
|---|---|---|---|
| **1 — MVP (Kampala)** | — | **100% Supabase** + Cloudflare, Flutterwave, Africa's Talking, FCM, Mapbox, Smile Identity, Sentry | nothing |
| **2 — Growth (Uganda)** | Sustained Postgres contention on specific hot paths; pgmq backlogs under load | Read replicas (Supabase); **Redis** for a measured hot path; **RabbitMQ** only if pgmq routing/throughput is genuinely exceeded | — |
| **3 — Scale (East Africa)** | Cross-region continuity needed; FTS relevance/latency insufficient as measured; graph traversals impractical in single Postgres | **Kafka** (cross-region event backbone); **Elasticsearch/OpenSearch** (search); dedicated graph store | single-region replay; Postgres-only FTS |
| **4 — Platform** | You self-host compute outside managed Supabase | **Kubernetes** + **Nginx**/LB; containerised services; namespaced domains | managed-only scaling |

**Reading the ladder:** most ZUKA instances may never leave Stage 1-2. That is a
feature, not a limitation. The eight technologies are not a roadmap to climb;
they are insurance you buy *only* when a specific metric tells you to.

---

## 12. Technology Decision Authority

Per ZukaBibleV4 Vol 02 (Governance Constitution), infrastructure technology
choices sit in the **Adaptive Zone** (Architecture Council review). Trust-state
exposure sits in the **Stable Zone** (Architecture Council + Founder).

| Decision | Zone | Authority |
|---|---|---|
| Adopting any of the 8 techs (leaving a Supabase-native capability) | Adaptive (ADR-016) | Architecture Council |
| The migration trigger for each (the metric line) | Adaptive | Architecture Council |
| RLS / view design enforcing trust-state non-exposure | **Stable** | Architecture Council + Founder |
| Discovery ranking & fairness-of-exposure logic | Adaptive | Architecture Council |
| Adding a Postgres extension (pgmq, postgis, pg_trgm, etc.) | Adaptive | Architecture Council |
| Any new external connector (e.g. new MoMo SDK) | Stable | Arch + Security + Governance + Founder |
| Any move off managed Supabase (Stage 4) | **Stable** | Arch + Security + Governance + Founder |

### The Founder Rules Applied to This Stack

1. **Reality before AI** — build for the load you observe, not the load you imagine.
2. **Simplicity before feature count** — one managed Postgres beats eight
   self-hosted systems until a metric proves otherwise.
3. **Security before convenience** — trust-state non-exposure is an RLS
   invariant; MoMo endpoints are TLS-verified Edge Functions.
4. **Trust before scale** — the append-only Event Store and Trust Ledger keep
   their integrity in Postgres at every stage; migrations preserve history, never
   rewrite it.
5. **A solo founder's time is the scarcest resource** — every system you don't
   operate is time returned to shipping the product.

---

*This document is subordinate to ZukaBibleV4. In any conflict, the Bible prevails.*
*The locked stack is Supabase-first; the eight technologies here are Adaptive-Zone migration targets, adopted only when an observed trigger fires.*
*Last updated: June 2026 · Built in Kampala, for Africa and the world.*

> **The city just came alive.**