# CONCEPT — Distributed Systems Architecture Register

> **Status:** LIVING REGISTER · Tracks 12 distributed-systems concerns from MVP
> through maturity, so each is deliberately considered, brainstormed, and
> planned rather than discovered under load.
> **Authority:** Subordinate to ADR-016 (modular monolith first), the
> Infrastructure & Technology Guide (Migration Ladder), and ADR-021 (Caching).
> **Governing doctrine:** *Adopt scale technology only when an observed metric
> trigger fires — never speculatively.* Capability ≠ activation.
> **Date:** 2026-07-02 · **Version:** 1.0

---

## How to read this register

Each concept is recorded with the same structure:

```
STATUS      — one of: IMPLEMENTED · PLATFORM-INHERITED · POSTGRES-NATIVE ·
                       DEFERRED (with trigger) · GAP (needs work) · N/A
CURRENT     — what exists in the codebase today (cite migration/file if built)
BIBLE/ADR   — where the governing decision lives
TRIGGER     — the observed metric that activates the deferred/migration form
GAP OR RISK — the honest gap, if any
BRAINSTORM  — whether this needs a dedicated design session before it's built
```

A concept marked **GAP** has real, unmitigated exposure and should be
prioritised for a brainstorming session. A concept marked **DEFERRED** is a
conscious not-yet with a named trigger — no action until the trigger fires.

---

## 1 · Load Balancing

```
STATUS      PLATFORM-INHERITED
CURRENT     Managed by the Supabase stack (Kong gateway + Postgres connection
            pooling via the pooler container). Cloudflare fronts production
            for edge distribution. No custom load balancer exists or is needed.
BIBLE/ADR   Vol 20 (Infrastructure & Connectors); ADR-016 (monolith first)
TRIGGER     A dedicated Nginx/HAProxy layer becomes a migration target only
            when: sustained request volume exceeds the managed platform's
            pooling capacity, OR multiple independent backend services are
            extracted (which itself requires an ADR).
GAP OR RISK None at MVP. The managed platform absorbs this concern.
BRAINSTORM  No — revisit only at the migration trigger.
```

---

## 2 · Caching

```
STATUS      IMPLEMENTED (policy) + DEFERRED (infrastructure)
CURRENT     ADR-021 defines the full caching posture:
            • discovery_feed → cacheable at Cloudflare edge, 30s TTL,
              invalidated on LIVE/ENDED transitions via Realtime webhook
            • check_in_ticket → NEVER cached at any layer (constitutional-
              weight security invariant; a cached "valid" response is a
              ticket-cloning attack)
            • all transactional paths → no-cache by default
            No Cloudflare cache rules configured in local dev (by design).
            Redis is NOT used.
BIBLE/ADR   ADR-021 (Caching Strategy); Vol 15 (Discovery); Vol 20
TRIGGER     Cloudflare edge caching activates at production launch.
            Redis (application-level cache) is a migration target triggered
            by: repeated expensive computed reads that Cloudflare edge cannot
            serve (e.g. per-user personalised feeds at DRL 3+).
GAP OR RISK Production Cloudflare cache-rule configuration + the Realtime→
            cache-purge integration are unbuilt (flagged in the Unspecced
            Register §6). Not blocking until production traffic.
BRAINSTORM  Partial — the Realtime-to-edge-invalidation mechanism deserves a
            short design pass before production launch.
```

---

## 3 · CDN (Content Delivery Network)

```
STATUS      PLATFORM-INHERITED (locked stack)
CURRENT     Cloudflare is constitutional (locked stack, Vol 20). Serves the
            edge cache for discovery and static/media asset delivery in
            production. Not configured in local dev.
BIBLE/ADR   Vol 20 (Cloudflare in locked stack); ADR-021
TRIGGER     Configured at production launch. Media-heavy features (Memories
            with photos, venue images) increase its importance at Phase 3.
GAP OR RISK No production configuration exists yet — expected, pre-launch.
BRAINSTORM  No — standard Cloudflare setup; fold into the production launch
            checklist rather than a design session.
```

---

## 4 · Message Queues

```
STATUS      POSTGRES-NATIVE (enabled, unused) + DEFERRED (dedicated broker)
CURRENT     pgmq (Postgres Message Queue) enabled in S002
            (20260630120000_enable_extensions.sql). A real, durable queue
            living inside Postgres — Supabase-native. No producer or consumer
            uses it yet.
BIBLE/ADR   Vol 20; Infrastructure Guide (Migration Ladder); ADR-016
TRIGGER     RabbitMQ/Kafka become migration targets only when: sustained queue
            depth or cross-service throughput exceeds what pgmq-in-Postgres can
            serve, OR event volume demands a dedicated streaming log (Kafka)
            for replay/analytics at scale.
GAP OR RISK No functional gap — nothing yet needs a queue. First real use
            cases: async notification dispatch (FCM/SMS fan-out), payout
            processing, Memory-prompt scheduling.
BRAINSTORM  Yes — a design session on "what runs async and why" should precede
            the first pgmq consumer, to establish the durable-job pattern once,
            correctly (idempotency, dead-letter handling, retry policy).
```

---

## 5 · Publish / Subscribe

```
STATUS      POSTGRES-NATIVE (implemented)
CURRENT     Two native mechanisms exist:
            • domain_events (append-only) + Outbox pattern — durable, ordered,
              in-database pub-sub. The event spine of the whole system.
            • Supabase Realtime (Postgres WAL-based) — live push to clients.
              The confirmed discovery design uses it to flip the "Live Now"
              badge without a feed re-fetch.
BIBLE/ADR   ADR-015 (append-only events); Vol 20; DESIGN_Discovery_Feed.md
TRIGGER     A dedicated event bus (Kafka/NATS) is a migration target only at
            multi-service, multi-region scale — far future.
GAP OR RISK No gap for in-system pub-sub. The Outbox→external-consumer bridge
            (delivering domain events to downstream systems) is unbuilt but
            unneeded until there's a downstream consumer.
BRAINSTORM  No — the pattern is sound. Revisit only when an external consumer
            of domain events appears.
```

---

## 6 · API Gateway

```
STATUS      PLATFORM-INHERITED (implemented)
CURRENT     Kong ships inside the Supabase stack (supabase_kong container,
            port 54321). All PostgREST, RPC, and Edge Function traffic already
            routes through it — auth, routing, and request handling. ZUKA did
            not build a gateway; it inherited a production-grade one.
BIBLE/ADR   Vol 20; the locked Supabase stack
TRIGGER     A custom gateway layer is a migration target only if backend
            services are extracted beyond what Kong-in-Supabase serves —
            requires an ADR.
GAP OR RISK None at MVP. Custom gateway policies (advanced rate limiting,
            request transformation) are available in Kong if needed later.
BRAINSTORM  No — inherited and sufficient.
```

---

## 7 · Circuit Breaker

```
STATUS      GAP (real, partially mitigated)
CURRENT     No circuit breaker exists on external calls. The Flutterwave
            Edge Functions (initiate + webhook) call an external API with no
            breaker, no formal retry/timeout policy. Current mitigations are
            COMPENSATING, not preventive:
            • fail-loud errors (no silent failure)
            • idempotency keyed on Flutterwave's transaction id
            • the S009 expiry sweep cleans up orphaned PAYMENT_PENDING state
              if an external call dies mid-flow
BIBLE/ADR   Vol 20 Ch 6 (ISS Docking Principle — all external systems connect
            via ConnectorContract, never direct coupling). This is the
            architectural home where breaker/retry/timeout policy belongs.
TRIGGER     Needed before the connector count grows (Africa's Talking SMS,
            FCM, Smile Identity, future Zuka Ride, streaming) — each new
            external dependency compounds the risk of a hanging call.
GAP OR RISK REAL. A hung or failing Flutterwave/SMS/FCM call today has no
            breaker to trip — it relies on timeouts and cleanup. Under load
            or during a provider outage, this could cascade.
BRAINSTORM  YES — HIGH PRIORITY. A design session on the ConnectorContract
            pattern (breaker + retry + timeout + fallback, per external
            dependency) should happen before more connectors are added.
            Candidate Fable Five follow-up analysis.
```

---

## 8 · Service Discovery

```
STATUS      N/A (by constitution)
CURRENT     There are no services to discover. ADR-016 mandates a modular
            monolith on one Postgres. Kubernetes and its discovery machinery
            are explicitly never-at-MVP.
BIBLE/ADR   ADR-016 (monolith first); Vol 20 (scale-stage tech is target-only)
TRIGGER     Becomes relevant only if/when a service is extracted from the
            monolith — which itself requires an observed trigger AND an ADR.
            Not before.
GAP OR RISK None. Adopting service discovery now would be premature complexity
            the doctrine explicitly forbids.
BRAINSTORM  No — revisit only if a service extraction is ever proposed (with
            its own ADR).
```

---

## 9 · Sharding

```
STATUS      DEFERRED (natural key identified)
CURRENT     One Postgres is the single source of truth. No sharding exists or
            is planned at MVP. Notable latent asset: ZUKA's city-by-city
            expansion model (Kampala → Nairobi → …) provides a NATURAL shard
            key — the city — anticipated in the Vol 24 dormant register.
BIBLE/ADR   Vol 24 (Scaling & Dormant Register); ADR-016
TRIGGER     Geo-partitioning / sharding by city becomes relevant only at
            multi-city scale with data volume that a single Postgres cannot
            serve. Explicitly a mature-phase concern.
GAP OR RISK None at MVP. The city-as-shard-key insight should be preserved so
            that schema decisions today don't accidentally make future
            geo-partitioning harder (e.g. avoid cross-city hard dependencies
            in core tables).
BRAINSTORM  Not yet — but flag as a design consideration in any schema change
            that could span cities, so future sharding stays clean.
```

---

## 10 · Rate Limiting

```
STATUS      IMPLEMENTED (application layer) + DEFERRED (infra layer)
CURRENT     S010 built per-Person velocity limiting on initiate_ticket_purchase
            (20260702110001_fn_initiate_ticket_purchase_rate_limit.sql):
            • windowed SQL COUNT over domain_events (not a Redis counter)
            • per-Person pg_advisory_xact_lock (concurrency guard)
            • custom ZK429 SQLSTATE for client-renderable rejection
            • backed by a composite index on domain_events
            Known caveat: the advisory-lock TOCTOU fix is unproven under real
            concurrency (needs a two-session harness) — S010 watch-out.
BIBLE/ADR   Vol 14; S010 session record; Fable Five analysis §3D
TRIGGER     Infra-level rate limiting (Cloudflare rules on the Edge Functions,
            per-IP throttling) is a future layer, activated at production /
            under observed abuse patterns.
GAP OR RISK Two open items: (1) the concurrency proof for the advisory lock;
            (2) no edge/IP-level limiting yet — the current limit is per-Person
            (identity-based), so an unauthenticated flood on the Edge Function
            surface isn't yet throttled at the edge.
BRAINSTORM  Partial — the infra-level (edge/IP) rate-limiting design is worth a
            short pass before production; the DB-level design is settled.
```

---

## 11 · Consistent Hashing

```
STATUS      N/A (no current use case)
CURRENT     Not used anywhere. There is nothing to distribute across nodes —
            single Postgres, no distributed cache, no sharded storage.
BIBLE/ADR   Implied by ADR-016 (monolith); no dedicated decision needed yet
TRIGGER     Becomes relevant ONLY if a distributed cache (Redis cluster) or
            sharded storage is adopted — both are post-trigger, mature-phase
            technologies. Consistent hashing is the mechanism those would use
            to distribute keys/shards evenly.
GAP OR RISK None. Would be pure premature complexity today.
BRAINSTORM  No — dependent on sharding (§9) or a distributed cache (§2/§4)
            being triggered first. Not a standalone concern.
```

---

## 12 · Auto Scaling

```
STATUS      PLATFORM-INHERITED
CURRENT     Delegated entirely to the managed platform. Supabase scales
            Postgres (vertically) and Edge Functions (horizontally) on its own.
            No Kubernetes HPA, no custom autoscaling logic — explicitly
            rejected at MVP per ADR-016.
BIBLE/ADR   Vol 20; ADR-016
TRIGGER     Custom autoscaling (K8s, dedicated infra) is a migration target
            only if the platform's managed scaling is outgrown — a
            high-scale, likely multi-region concern requiring an ADR.
GAP OR RISK None at MVP. The managed platform's scaling envelope comfortably
            exceeds Kampala-launch volume.
BRAINSTORM  No — revisit at the platform-scaling-limit trigger.
```

---

## Summary Matrix

```
CONCEPT               STATUS                    NEEDS BRAINSTORM?
──────────────────────────────────────────────────────────────────
1  Load Balancing     PLATFORM-INHERITED        No
2  Caching            IMPLEMENTED + DEFERRED     Partial (pre-launch)
3  CDN                PLATFORM-INHERITED         No (launch checklist)
4  Message Queues     POSTGRES-NATIVE (unused)   YES (before 1st consumer)
5  Pub/Sub            POSTGRES-NATIVE (built)    No
6  API Gateway        PLATFORM-INHERITED         No
7  Circuit Breaker    GAP (partially mitigated)  YES — HIGH PRIORITY
8  Service Discovery  N/A (by constitution)      No
9  Sharding           DEFERRED (city = key)      Not yet (design-aware)
10 Rate Limiting      IMPLEMENTED + DEFERRED     Partial (infra layer)
11 Consistent Hashing N/A                        No (dependent)
12 Auto Scaling       PLATFORM-INHERITED         No
──────────────────────────────────────────────────────────────────
```

## Priority actions arising from this register

1. **Circuit Breaker (§7)** — the one genuine, unmitigated architectural gap.
   Design the ConnectorContract pattern (Vol 20 Ch 6) with breaker/retry/
   timeout/fallback before adding more external connectors. Candidate for a
   dedicated Fable Five follow-up analysis and a subsequent build session.

2. **Async pattern establishment (§4)** — before the first pgmq consumer is
   built, run a design session to fix the durable-job pattern once
   (idempotency, dead-letter, retry), so every async job follows it.

3. **Infra-level rate limiting (§10)** — the edge/IP throttling layer, to
   complement the existing per-Person DB-level limiting, before production.

4. **Concurrency proof (§10)** — build the two-session test harness to prove
   the rate-limit advisory lock actually serialises concurrent calls
   (carried from S010).

5. **Caching invalidation mechanism (§2)** — design the Realtime→Cloudflare
   cache-purge integration before production launch.

Everything else is either correctly platform-inherited, correctly deferred
with a named trigger, or constitutionally N/A. The discipline holds: no
speculative distributed-systems complexity, every not-yet has a trigger.

---

*Subordinate to ADR-016 and the Infrastructure & Technology Guide.*
*Adopt scale technology only when a real trigger fires — never before.*
*ZUKA — Human Participation Infrastructure.*
