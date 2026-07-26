# ZUKA Constitutional Compendium
### Consolidated AI Audits, Architecture Reviews & Strategic Brainstorms

**Compiled for:** Ying (Kiyingi James) — Solo Founder, ZUKA
**Purpose:** Single reference document consolidating prior AI-assisted architecture reviews, to serve as the base for a deeper, cross-cutting brainstorm session.
**Status:** Working document — not constitutional itself, but summarizes constitutional-review sessions.
**Authority:** Subordinate to `SOUL.md` and ZukaBibleV4, same as every doc in this repo. Nothing here amends the Bible; findings graduate via the normal path — a concept doc in `docs/concepts/` or a candidate ADR — never directly. See [README.md](README.md) for how this directory relates to `docs/concepts/`, `docs/design/`, and `docs/fable-analysis/`.

---

## How to use this document

This compiles **10 prior review sessions** into one navigable file. Each section preserves the original analysis but is reorganized by theme rather than chronology, so a brainstorming pass can move across:

1. **Where ZUKA stands today** (maturity, scores, completion %)
2. **What's been validated as strong** (don't re-litigate these)
3. **What's been flagged as a gap** (candidate brainstorm targets)
4. **The big strategic reframes** (ZMOS, ZPE, Participation OS, Disposable Platform Problem)
5. **Open questions / tensions** for the next brainstorm to resolve

A suggested final section lists **cross-cutting brainstorm prompts** — use these as starting angles.

---

## Table of Contents

- [Part I — Maturity & Completion Snapshot](#part-i--maturity--completion-snapshot)
- [Part II — Constitutional Audit (Product/Vision Layer)](#part-ii--constitutional-audit-productvision-layer)
- [Part III — Supabase Backend Audit (Implementation Layer)](#part-iii--supabase-backend-audit-implementation-layer)
- [Part IV — Frontend / Flutter Architecture (Interaction Layer)](#part-iv--frontend--flutter-architecture-interaction-layer)
- [Part V — ZUKA Mobile OS (ZMOS) — Strategic Reframe #1](#part-v--zuka-mobile-os-zmos--strategic-reframe-1)
- [Part VI — ZUKA Participation Engine (ZPE) — Strategic Reframe #2](#part-vi--zuka-participation-engine-zpe--strategic-reframe-2)
- [Part VII — Impact Analysis of Full Implementation](#part-vii--impact-analysis-of-full-implementation)
- [Part VIII — The Disposable Platform Problem — Strategic Reframe #3](#part-viii--the-disposable-platform-problem--strategic-reframe-3)
- [Part IX — Consolidated Gap Register](#part-ix--consolidated-gap-register)
- [Part X — Brainstorm Launchpad](#part-x--brainstorm-launchpad)

---

## Part I — Maturity & Completion Snapshot

### Overall completion estimates (as assessed across sessions)

| Area | Completion | Notes |
|---|---:|---|
| Product Vision | 99% | Stable identity: "Human Participation Infrastructure" |
| Product Strategy | 98% | |
| Business Model | 95% | |
| Architecture (conceptual) | 93% | |
| Domain Modeling | 90% | |
| Documentation | 95% | 274-file bible, 25 volumes, appendices A–H, 6 ADRs |
| UX Philosophy | 90% | |
| Technical Specifications | 88% | |
| **Production Backend** | 15–25% → revised to **65–75%** after Supabase inspection | See revision note below |
| Production Frontend | 10–20% | Flutter not yet built out |
| Production APIs | 10–20% | |
| Testing | 0–5% | |
| Deployment | 0% | |

**Revision note:** Initial estimate placed overall project maturity at **30–35%** toward a shippable MVP. After direct inspection of the `supabase/` directory (migrations, RLS, event store, payment integration, scanner subsystem), this was revised upward to **40–50% overall**, with the **backend specifically at 65–75%**.

### Constitutional maturity scores

**Product/Vision layer — Overall: 9.2/10**

| Area | Score |
|---|---:|
| Vision & Product Strategy | 10/10 |
| UX Philosophy | 9.5/10 |
| System Architecture | 9.5/10 |
| Backend Design | 8.5/10 |
| Data Model | 8.5/10 |
| Security | 8/10 |
| AI Integration | 10/10 |
| Scalability | 9.5/10 |
| Documentation Quality | 10/10 |
| Solo Developer Feasibility | 7.5/10 |
| MVP Readiness | 8/10 |

**Supabase backend layer — Overall: 9.5/10**

| Area | Score |
|---|---:|
| Database Architecture | 9.8/10 |
| Domain Modeling | 9.7/10 |
| Migration Discipline | 10/10 |
| Security (RLS) | 9.2/10 |
| Edge Functions | 9.4/10 |
| Payment Architecture | 9.4/10 |
| Event Sourcing | 9.8/10 |
| Offline Capability | 9.5/10 |
| Performance Planning | 8.8/10 |
| Production Readiness | 8.8/10 |

### Estimated roadmap to MVP (if scope is frozen now)

| Phase | Scope | Duration | Cumulative Progress |
|---|---|---|---:|
| 1 — Foundation | Auth, Supabase setup, schema, RLS, storage, edge functions, CI/CD, error logging | 3–5 wks | ~45% |
| 2 — Core Events | Creation, discovery, search, event pages, categories | 4–6 wks | ~60% |
| 3 — Ticketing | QR tickets, purchases, check-in, validation | 3–5 wks | ~75% |
| 4 — Wallet | Payments, MoMo, ledger, refunds | 4–6 wks | ~85% |
| 5 — Organizer Dashboard | Analytics, sales, attendance, reports | 3–4 wks | ~95% |
| 6 — Polish | Bug fixing, perf, security review, beta, store prep | 2–4 wks | 100% |

**Estimate:** functional private beta in **4–8 months**; polished public MVP with full payment/ticketing/testing/ops in **6–12 months**, solo, AI-assisted, assuming scope discipline.

---

## Part II — Constitutional Audit (Product/Vision Layer)

### Validated strengths (treat as settled — don't re-litigate)

- **Stable product identity**: "Human Participation Infrastructure" holds consistently across all documents. Progression: Entertainment discovery → Ticketing → Communities → Trust → Opportunity → City Infrastructure.
- **UX philosophy** consistently emphasizes trust, human language, progressive disclosure, invisible complexity, real-world participation.
- **System architecture** already thinks in domains, constitutional rules, append-only history, authority hierarchy, immutable principles.
- **AI integration philosophy**: AI treated as reviewer/designer/architect/implementation assistant/operating system, not chatbot. Authority chain: SOUL.md → CLAUDE.md → Bible → Engineering docs → Implementation — reduces AI drift.
- **Scalability path** anticipates Kampala → Uganda → East Africa → Africa without requiring architectural rewrites.
- **Documentation** reads like an engineering organization's, not a solo project's: consistent terminology, authority hierarchy, identity preservation.

### Flagged gaps (candidate brainstorm targets)

1. **Missing ADR: "What is NOT part of MVP"** — no document currently exists to stop future AI agents/sessions from implementing dormant systems prematurely.
2. **No Event Bus** in backend design (only single backend today) — every important action should emit an event so downstream systems (wallet, analytics, notifications, recommendations, achievements) can react independently.
3. **"God table" risk** — `Users` entity is on a trajectory to absorb identity, wallet, trust, reputation, roles, creator/organiser/vendor/driver/business-owner state, community, preferences, history, verification, and AI profile. Needs bounded-context splitting early.
4. **No dedicated Security Volume** — key rotation, secrets management, device trust, session management, rate limiting, fraud detection, abuse prevention, Edge Function security, audit logging, incident response, backup policy, disaster recovery, encryption policy are not yet constitutional.
5. **No document versioning scheme** (Draft / Accepted / Deprecated / Superseded) across the 274-file bible.
6. **Solo developer feasibility (7.5/10)** — the concern isn't technical complexity, it's context-switching across social platform, payments, ticketing, trust, AI, live vendors, wallets, identity, moderation, recommendations, communities, analytics, business dashboards simultaneously.
7. **MVP scope creep risk** — architecture already supports far more than an MVP needs; recommendation was to hard-limit V1 UI to Home / Discover / Tickets / Event Page / Wallet / Profile, with everything else dormant/feature-flagged.

### Named constitutional risks

1. Architecture outpacing implementation (designing faster than building).
2. Feature creep — rich architecture makes "just one more feature" easy to justify.
3. AI overbuilding — powerful coding assistants generating sophisticated systems not yet needed.
4. Documentation/code synchronization drift over time.

---

## Part III — Supabase Backend Audit (Implementation Layer)

### Structure observed

```
supabase/
├── config.toml
├── functions/
│   ├── flutterwave-webhook/
│   └── initiate-flutterwave-payment/
├── migrations/
│   ├── enable_extensions.sql
│   ├── event_store.sql
│   ├── person.sql
│   ├── venue.sql
│   ├── gathering.sql
│   ├── ticket.sql
│   ├── trust_profile.sql
│   ├── scanner_session.sql
│   ├── vendor_company_team.sql
│   ├── commission_debt_ledger.sql
│   └── 30+ additional migrations
└── tests/
    ├── RLS verification
    ├── scanner tests
    ├── Flutterwave tests
    ├── discovery tests
    ├── vendor tests
    ├── offline scanner tests
    └── commission ledger tests
```

### Validated strengths (treat as settled)

- **Migration discipline (10/10)** — schema evolved through timestamped migrations, not direct edits.
- **Event sourcing foundation** — `event_store.sql`, `domain_events_velocity_idx.sql` already present.
- **Row Level Security** — dedicated migrations per domain: `rls_person`, `rls_ticket`, `rls_venue`, `rls_discovery`, `rls_vendor`.
- **Payment architecture** — Flutterwave initiation, webhook, confirmation, expiry, commission ledger already implemented; correct separation of concerns (Postgres = transactional work, Edge Functions = outbound HTTP + secrets).
- **Scanner subsystem** — QR token rotation, scanner manifest, offline scan log, PIN throttling, PIN hardening. Explicitly called out as evidence of designing for *real-world event operations*, not just QR generation.
- **Vendor foundation** — Vendor Company → Vendor Team → Commission Ledger → RLS → RPC functions, matching prior architecture discussions.
- **RPC philosophy** — business operations exposed as functions (`fn_create_gathering`, `fn_publish_gathering`, `fn_go_live`, `fn_issue_ticket`, `fn_confirm_ticket_payment`, `fn_check_in_ticket`) rather than raw table access — correct architectural boundary.
- **Domain-driven schema** — tables reflect business aggregates (`person`, `venue`, `gathering`, `ticket`, `trust_profile`, `vendor_company_team`, `commission_debt_ledger`), not UI concepts.

### Flagged gaps (10 concrete recommendations — candidate brainstorm targets)

| # | Gap | Recommendation |
|---|---|---|
| 1 | Everything lives in `public` schema | Introduce bounded schemas: `identity`, `events`, `ticketing`, `wallet`, `vendors`, `trust`, `analytics`, `notifications`, `platform` (not urgent for MVP) |
| 2 | Event sourcing exists but isn't comprehensive | Require every significant business transition to emit a domain event (`GatheringCreated`, `TicketPaid`, `WalletCredited`, `TrustScoreUpdated`, etc.) |
| 3 | **No complete double-entry wallet ledger** (flagged as *the most significant gap*) | Introduce `wallet`, `wallet_account`, `wallet_transaction`, `wallet_entry`, `escrow`, `settlement`, `withdrawal`, `refund` |
| 4 | Hard-coded business constants | Add `platform_configuration` (key/value/version/effective_from) for commission %, refund windows, discovery radius, QR lifetime, vendor limits, transfer rules |
| 5 | Limited observability | Add `api_request_log`, `edge_function_log`, `security_event`, `fraud_event`, `audit_log`, `background_job_log` |
| 6 | No dedicated AI data model | Add `ai_context`, `ai_memory`, `ai_embedding`, `ai_recommendation`, `ai_summary`, `ai_feedback` — isolate AI concerns from core business data |
| 7 | No feature flag system | Introduce simple feature-flag table to keep dormant functionality (Live Vendors, Communities, AI Assistant, Marketplace) in production without exposing it |
| 8 | Idempotency not enforced everywhere | Extend to all critical RPCs: create gathering, publish, ticket purchase, transfer, check-in, refund, withdrawal |
| 9 | Rate limiting only on ticket purchase | Extend to login, OTP, discovery, search, check-in, vendor onboarding, wallet operations |
| 10 | No documented disaster recovery | Add failed-jobs table, dead-letter queue, reconciliation jobs, replay jobs + documented procedures |

### Recommendation from this session
Stop adding major database features; shift focus to: complete Flutter app → wire every screen to existing RPCs → validate end-to-end workflows → test with real event scenarios → harden from operational feedback rather than new architecture. **Conclusion: the Supabase backend is no longer the bottleneck.**

---

## Part IV — Frontend / Flutter Architecture (Interaction Layer)

### Core principle proposed

> **Every screen is a state machine. Every business action is an RPC. Every state transition is observable, recoverable, and replayable.**

Pattern for every experience:
`User Intent → UI State → Domain Action (RPC) → Event → Backend Response → UI Update → Analytics → Recovery`

### The 10 layers proposed

1. **Experience Layer** — user-facing simplicity hides multi-step backend orchestration (e.g., ticket purchase = 15+ internal steps compressed into "Tap → Choose Quantity → Review → Pay → Success").
2. **Screen Architecture** — `Screen → Controller → ViewModel → Repository → RPC Client → Supabase`; no business logic in UI.
3. **Smart Loading States** — beyond Loading/Success/Failure: Idle, Offline, Retrying, WaitingForPayment, Syncing, Refreshing, Expired, PermissionDenied, ValidationFailed, Completed.
4. **Dynamic Forms** — declarative field metadata (type, validation, visibility rule, default, dependency, RPC mapping) rendered dynamically — enables A/B testing, localization, future AI-generated forms without code changes.
5. **Offline-First Thinking** — actions queued locally (e.g., "Like" tap → queued → badge "Pending" → synced on reconnect); applies to ticket scanning, check-in, profile edits, drafts.
6. **Unified Error Language** — translate technical errors (HTTP 409, constraint violations) into human language ("Someone else just claimed that ticket").
7. **Contextual Intelligence** — same action (scanning a stage QR) produces different UI depending on role/context (organizer sees attendance summary; attendee sees schedule/nearby vendors).
8. **Robust RPC Integration** — every RPC contract defines input model, validation, timeout, retry policy, idempotency key, success payload, business error codes, telemetry.
9. **End-to-End Workflow Testing** — test full journeys (Consumer, Organizer, Vendor, Scanner) not isolated screens.
10. **Production Hardening** — every failure must answer: Can the user recover? Can the system recover automatically? Is it logged? Can it be replayed/reconciled?

### Proposed Frontend Constitution (8 principles)

1. Backend authority — business rules live in Supabase RPCs, not the client.
2. State-driven UI — every screen is a finite state machine.
3. Offline-first — actions queued and synced.
4. Human-centered feedback — every state communicates clearly.
5. Context-aware experiences — adapts to role/location/activity.
6. Observability by design — every interaction emits telemetry.
7. Feature flags over forks — one binary, progressive capability enablement.
8. Progressive disclosure — surface only what's needed now.

### Flagged gap
This entire layer was later assessed as only **~20–25%** of what a full "Flutter application constitution" should contain (see Part V).

---

## Part V — ZUKA Mobile OS (ZMOS) — Strategic Reframe #1

### The reframe

> ZUKA is not a Flutter app. Flutter is the **first client** of a **ZUKA Mobile Operating System (ZMOS)** — the constitutional operating environment governing every mobile interaction regardless of feature, platform, or future technology.

```
                    ZUKA Constitution
                            │
                  ZUKA Mobile OS Constitution
                            │
        ┌───────────────────┼───────────────────┐
   Flutter App         Future Web         Future Desktop
        │                   │                   │
        └───────────────────┼───────────────────┘
                   Supabase Backend
```

### First constitutional law proposed

> **A user never interacts with screens. A user participates in experiences.**
(e.g., not "Create Event Screen" but "Host an Event Experience"; not "Wallet Screen" but "Manage Participation Value")

### Seven Operating Layers proposed

| Layer | Function |
|---|---|
| 0 — Human Layer | Determines who the user is and what context they're in (guest, consumer, organizer, vendor, scanner, moderator, business, driver, admin) |
| 1 — Experience Engine | Decides what experience a user should have *right now* — same page renders differently by role/state |
| 2 — Context Engine | Continuously computes location, time, event status, relationship, role, history, connectivity, battery, device, activity, nearby people, notifications, calendar, wallet state, trust level, permissions |
| 3 — Capability Engine | Asks "what are you capable of doing?" rather than "who are you?" — capabilities can be time/scope-limited (e.g., temporary scan rights for a volunteer, Gate 2 only, 8am–10pm) |
| 4 — Interaction Engine | Governs animations, gestures, loading, transitions, errors, forms, dialogs, navigation, voice, haptics |
| 5 — Synchronization Engine | Manages offline, retry, conflict, merge, queue, replay, rollback, confirmation, eventual consistency |
| 6 — Intelligence Engine | Recommends, suggests, optimizes, surfaces opportunities, reduces taps, predicts intent, prevents mistakes (pre-AI "domain intelligence") |

### Additional concepts introduced

- **Experience DNA** — every experience must answer: Who? Why? Where? When? What? How? Recovery? Analytics? Permissions? Synchronization? (10 questions — incomplete if any is unanswered)
- **Adaptive Interface** — UI evolves based on usage maturity, not manual customization (e.g., new organizer sees Simple Dashboard → after 6 months, Advanced Dashboard → power organizer sees Operations Center)
- **Invisible Complexity** as constitutional principle — 47 tables / 29 RPCs / 11 edge functions / 4 queues / 7 notification types / 2 payment providers collapse to "Ticket Purchased ✅" for the user
- **Living Interface** — homepage becomes situational narrative rather than static tabs (e.g., "Your event starts in 3 hours. 127 tickets sold. Sarah Snacks just checked in...")
- **Emotional Operating System** — platform is explicitly emotion-aware (excitement on purchase, anticipation while waiting, empathy on missed event, reassurance on refund) driving animation/language/sound/timing/color choices
- **ZUKA Participation Loop** — Discover → Trust → Commit → Participate → Connect → Capture Memories → Grow Reputation → Create Opportunities → Discover Again. All features should reinforce this loop rather than exist as isolated features.

### 20 missing Flutter-constitution areas identified (only ~20–25% covered so far)

1. Application Shell Architecture (bootstrap, DI, config, feature flags, auth, permissions, localization, theme, connectivity, session, analytics, crash handling, navigation)
2. Navigation Constitution (role-specific navigation trees, deep links, universal links, QR nav, notification nav, contextual nav)
3. Design System (spacing, typography, elevation, animation, loading states, dark mode, accessibility, responsive rules)
4. Component Library (Event Card, Ticket Card, Trust Badge, Wallet Card, Payment Sheet, QR Card, Vendor Tile, etc.)
5. State Management Constitution (local vs server vs cached vs session state; restart/logout survival rules)
6. Caching Strategy (per-screen cache duration, refresh strategy, offline behavior, invalidation, prefetch)
7. Synchronization Engine (queue → retry → conflict detection → merge → resolve → confirm → audit)
8. Permission Engine (capability-based, not role-boolean: `CanCreateEvent`, `CanRefund`, `CanScan`, etc.)
9. Notification Framework (push, in-app, SMS, email, inbox, banner, snackbar, modal, badge, reminder, scheduled, silent, digest)
10. Search Framework (global, nearby, event, vendor, creator, community, voice/image/semantic/AI search — future)
11. Accessibility Constitution (screen readers, large fonts, color blindness, motion reduction, one-handed use, foldables, voice control)
12. Performance Constitution (cold start <2s, nav <150ms, 60fps animation, image load <300ms, memory/battery/network budgets)
13. Security on Device (encrypted local storage, token lifecycle, biometrics, root/tamper detection, screenshot/clipboard policy, cert pinning, session timeout)
14. AI Experience Layer (when AI appears/stays silent/suggests/automates; explainability; permission-asking; undo)
15. Growth Architecture (experiments, feature flags, A/B testing, remote config, canary releases, cohorts, regional behavior)
16. Analytics Constitution (richer than event logging — what/who/why/preceded/followed/success/improvement)
17. Error Recovery Framework (payment interrupted mid-call, battery dies mid-scan, network drops mid-publish, callback race conditions, duplicate-tap concurrency, offline/online conflict resolution)
18. Internationalization & Regionalization (languages, currency formatting, date/time conventions, timezones, MoMo providers by country, regional tax, local customs)
19. Developer Experience / DX (feature-based structure, codegen, shared design tokens, linting, widget test templates, mock RPC clients, storybook-style previews, one-command setup, AI-code conventions)
20. Operational Readiness (crash reporting, perf monitoring, remote log collection, kill switches, feature deactivation, maintenance mode, health checks, support diagnostics)

**Overarching missing artifact identified:** a single **"ZUKA Mobile Operating System Constitution"** document unifying all of the above — described as "the last major constitutional artifact before moving into full-scale implementation."

---

## Part VI — ZUKA Participation Engine (ZPE) — Strategic Reframe #2

### The reframe
Reorganize the backend from "Supabase + Edge Functions" into a collection of specialized **engines**, with Supabase as persistence layer, not "the brain."

```
                ZUKA Participation Engine (ZPE)
                       Experience Engine
        ┌──────────────┬──────┴───────────────┬──────────────┐
 Context Engine   Capability Engine    Workflow Engine  Intelligence Engine
        └──────────────┴──────────────┬───────┴──────────────┘
                             Command Router
                              Domain Services
                     RPCs / Edge Functions / Workers
                     PostgreSQL + Event Store + Storage
```

### The 20 proposed innovations

| # | Engine/Concept | Core idea |
|---|---|---|
| 1 | Experience Engine | Flutter requests "Build Experience" (with location/time/device/intent/capabilities/context) instead of raw data; backend composes UI-ready response |
| 2 | Context Engine | Single source of truth for current situational context (`Context.getCurrentParticipation()`), avoiding recomputation per feature |
| 3 | Capability Engine | Dynamic, scoped, time-boxed capabilities instead of static roles |
| 4 | Workflow Engine | Every business process (ticket purchase, event publishing) modeled as an explicit workflow with state, timeout, retry, rollback, compensation, audit, replay |
| 5 | Command Bus | Flutter sends immutable commands (`CreateGatheringCommand`, `PurchaseTicketCommand`) that validate → execute → produce events → return result |
| 6 | Event Mesh | Evolves existing event store so every domain event is publishable/subscribable across notification, analytics, achievements, recommendation, wallet, AI, fraud systems |
| 7 | Intelligent Query Layer | Single "Experience Query" (e.g. `HomeExperience()`) replaces 20 separate RPCs |
| 8 | State Engine | Explicit state machines for every aggregate (Gathering: Draft→Review→Scheduled→Published→Live→Paused→Ended→Archived; Ticket; Wallet) — never boolean flags |
| 9 | Participation Graph | Graph-based modeling of people/events/companies/relationships to power recommendations, communities, networking, trust |
| 10 | Dynamic Experience Composer | Homepage assembled server-side from live signals (countdown, volunteer alert, vendor promo, wallet reminder, weather, friend nearby) rather than hardcoded |
| 11 | Policy Engine | All business rules (refund window, commission %, trust threshold, vendor rules, withdrawal delay) centralized and versioned, not hardcoded |
| 12 | Temporal Engine | Every object is time-aware beyond `created_at` (effective_from/until, published_at, live_at, settlement_at, visibility/discovery/reminder/archive windows) |
| 13 | Notification Orchestrator | Notifications modeled as Intent→Priority→Audience→Timing→Channel→Retry→Analytics, decided by backend rather than fired immediately |
| 14 | Trust Engine | Continuously computed trust score from attendance, payments, reports, reviews, organizer history, verification, community activity, vendor reliability, fraud, identity |
| 15 | Opportunity Engine | Domain-intelligence (non-AI) detection of volunteer/vendor/job/networking/sponsor/creator matches |
| 16 | Memory Engine | Structured (not just photo) memories: attended/met/hosted/purchased/liked/shared/won/volunteered/donated → powers timeline, achievements, annual recap |
| 17 | Digital Twin Engine | Every event exists physically and digitally in sync (attendance, capacity, queue, weather, security, vendors, incidents, announcements) |
| 18 | Operational Intelligence Layer | "Mission control" measuring auth success rate, payment funnel completion, QR scan latency, publication errors, vendor throughput, wallet backlog, fraud indicators, regional service quality; can auto-detect anomalies and trigger recovery workflows |
| 19 | Autonomous Recovery Engine | Every critical workflow defines detection → auto-retry → compensation → escalation → human intervention → reconciliation → audit (e.g., payment succeeds at provider but callback fails — engine reconciles and issues ticket without support involvement) |
| 20 | Constitutional Kernel | Enforces immutable platform-wide rules: user safety before growth; financial correctness before speed; backend authority over client assumptions; auditability; recoverability; policy-driven capability; explicit state transitions; traceable events; simplicity at interface / sophistication underneath |

### Phasing recommendation (avoid overbuilding)

| Phase | Engines |
|---|---|
| **Phase 1 — MVP Kernel** | Constitutional Kernel, Experience Engine, Context Engine (basic), Workflow Engine, Capability Engine, Event Store, Policy Engine |
| **Phase 2 — Growth** | Trust Engine, Notification Orchestrator, Synchronization Engine, Operational Intelligence, Analytics Engine |
| **Phase 3 — Intelligence** | Intelligence Engine, Opportunity Engine, Memory Engine, Digital Twin Engine, advanced AI integration |

---

## Part VII — Impact Analysis of Full Implementation

Assessed impact if the ZMOS/ZPE ideas above were fully implemented (with the explicit caveat that they should **not** all precede MVP — they are the architecture the MVP should *grow into*):

| Dimension | Without these ideas | With ZMOS/ZPE | Impact |
|---|---|---|---|
| UX | One-size-fits-all interface | Role/context-adaptive experience per user type (student, organizer, vendor, scanner) | Very high |
| Performance | ~20 API calls per screen | Single Experience Request returns composed response | High (esp. for variable network regions) |
| Developer productivity | "Which table/screen/SQL?" | "Which engine/experience/workflow owns this?" — lower cognitive load as codebase grows | High, personally |
| AI integration | Bolted on | AI becomes a participant flowing through Experience/Context/Workflow/Trust engines | Differentiating |
| Reliability | Failures require support contact | Recovery Engine retries/reconciles automatically, often invisibly | High trust impact |
| Scalability | Comfortable for Kampala only | New cities/countries become config + localized policy, not rewrites | High long-term |
| Feature growth | Each new vertical (hotels, tourism, sports, etc.) needs new backend logic | New verticals reuse Trust/Wallet/Notification/Experience/Context/Capability engines — additive, not disruptive | High long-term |
| Business agility | Rule changes require code deploys | Policy Engine makes rule changes into config changes | Shortens release cycles |
| Operational visibility | Issues discovered via user complaints | Operational Intelligence layer detects degradation proactively | High |
| Maintainability | Risk of tightly-coupled service sprawl | Each engine has single responsibility; engines are independently replaceable | Very high long-term |

### Costs explicitly acknowledged
1. **More engineering effort** — building dedicated engines costs more than exposing CRUD endpoints directly.
2. **More onboarding/learning** — future contributors need architectural literacy before contributing.
3. **Overengineering risk** — explicitly flagged as "the biggest concern" (e.g., an Opportunity Engine is premature before there's enough user activity to generate real opportunities).

---

## Part VIII — The Disposable Platform Problem — Strategic Reframe #3

### The core diagnosis
Most ticketing/event platforms have **transactional, not ongoing, value** — users open the app only around a purchase, then disappear:

```
Hear about Event → Open App → Buy Ticket → Attend → Leave → Never Open App Again
```

This causes: ignored notifications, low DAU, weak retention, weak ad/network effects, no community formation, shallow personalization (too little behavioral data), and dependence on continuous, expensive user reacquisition.

### The reframed question
Not *"How do people discover events?"* but:

> **"Why should someone open ZUKA today if they aren't buying a ticket?"**

### The strategic conclusion
ZUKA's core domain isn't "events" — it's **participation**. Events are the highest-energy expression of participation, but participation also includes discovering places, meeting people, supporting local businesses, building trust, volunteering, learning, working, creating memories, and joining communities.

```
Old framing: Events → Tickets → Payments
New framing: Life → Participation → Experiences → Relationships → Opportunities → Growth
```

### Proposed Daily Value Loop (reasons to open the app without a live transaction)

- **Today around you**: live events, local offers, friends nearby, community activity, volunteer opportunities
- **Your network**: friends attending events, new organizer announcements, communities, past connections
- **Your reputation**: trust updates, reviews, achievements, milestones
- **Your opportunities**: vendor staff needed, organizer needs volunteers, nearby business promo, collaboration requests
- **Your memories**: "one year ago today..." / photo collections / personal timeline

### Event Flywheel (replacing linear funnel)
```
Discover → Trust → Commit → Attend → Share → Connect → Remember → Grow → Discover Again
```

### The "Local Operating System" idea
Positions ZUKA as answering: *"What's happening around me, and how can I participate?"* — spanning events, vendors, restaurants, clubs, community meetings, sports, classes, volunteering, pop-up markets, cultural festivals.

### Explicit risk flagged
Easy to overreach into "let's add restaurants, transport, jobs, tourism, education" — this dilutes the product. Every addition must be filtered through: **"Does this strengthen the participation loop?"**

### Proposed purpose statement
> "ZUKA exists to help people discover, join, contribute to, and grow through meaningful participation in their local communities. Events are the starting point — not the destination."

This is proposed as a filter for every future feature decision (Live Vendors → yes, enriches participation; Trust Profiles → yes, makes participation safer; Memories → yes, extends participation beyond the event; etc.)

---

## Part IX — Consolidated Gap Register

A single table of every concrete "missing thing" flagged across all sessions, for prioritization.

| Gap | Source Section | Priority Class (suggested) |
|---|---|---|
| ADR: "What is NOT part of MVP" | II | MVP-blocking |
| Event Bus / comprehensive domain-event emission | II, III | Phase 1 |
| Bounded contexts to prevent "God table" (Users) | II, III | Phase 1–2 |
| Security Volume (rotation, secrets, fraud, incident response, DR) | II, III | MVP-blocking (security) |
| Document versioning (Draft/Accepted/Deprecated/Superseded) | II | Low urgency |
| Double-entry wallet ledger | III | MVP-blocking (money) |
| Platform configuration table (versioned business constants) | III | Phase 1 |
| Observability tables (request/edge/security/fraud/audit/job logs) | III | Phase 1–2 |
| AI-specific data model (context/memory/embedding/etc.) | III | Phase 3 |
| Feature flag system | III, V | Phase 1 |
| Idempotency across all critical RPCs | III | MVP-blocking |
| Rate limiting beyond ticket purchase | III | MVP-blocking |
| Disaster recovery tables/procedures | III | Phase 2 |
| Full Flutter/Mobile OS constitution (20 sub-areas, Part V) | IV, V | Mixed — some MVP-blocking (state mgmt, error recovery, security-on-device), some Phase 2–3 |
| Experience/Context/Capability/Workflow engines (ZPE Phase 1 kernel) | VI | Phase 1 (lightweight versions) |
| Purpose statement / participation-loop filter for feature decisions | VIII | Immediate (costs nothing, prevents drift) |

---

## Part X — Brainstorm Launchpad

Use these as starting angles for the next deep-dive session. Each is intentionally framed as a live tension rather than a settled answer.

1. **Scope-freeze mechanics**: What does an actual, enforceable "MVP boundary" ADR look like in practice for a solo AI-assisted founder — not just a wish, but something Claude Code sessions are structurally prevented from violating?
2. **Wallet ledger design**: Given Bloom's existing invariant set (single-currency-per-transaction, no wallet/held-balance, fail-closed money parameters), how much of that philosophy should transfer to ZUKA's wallet, versus ZUKA needing its own model given tickets/refunds/commissions differ from nail-service deposits?
3. **Engine-vs-CRUD threshold**: For each of the 20 ZPE engines, what is the *minimum viable version* that's justified pre-launch vs. purely aspirational? Where exactly is the line between "lightweight function that does the engine's job" and "premature abstraction"?
4. **Participation loop vs. MVP discipline**: The Disposable Platform Problem argues for daily-value features (memories, opportunities, network) that are explicitly *not* in the 6-screen MVP scope. How do you sequence toward habitual use without violating the "freeze scope" recommendation?
5. **Bounded contexts timing**: Is now (pre-scale) actually the cheapest time to introduce schema separation (identity/events/ticketing/wallet/etc.), given it was labeled "not urgent," or does deferring it compound risk given the God-table trajectory already identified?
6. **Solo-feasibility vs. ambition gap**: Solo Developer Feasibility scored lowest (7.5/10) of all constitutional areas. What specific operating changes (not architecture — workflow/discipline) would move that score, independent of any new engine?
7. **AI-as-participant**: Section VI's Innovation 4 (AI flowing through Experience/Context/Workflow/Trust engines) — what would the *smallest possible* real implementation of this look like using only what's already built (Claude Code sessions, existing RPCs)?
8. **Cross-project leverage**: Given Bloom's parallel development, which ZPE engines (Trust, Wallet, Notification, Policy) are genuinely shareable infrastructure across ZUKA and Bloom vs. superficially similar but domain-specific enough to warrant separate implementations?
9. **Kill criteria**: For dormant/future systems (Opportunity Engine, Digital Twin Engine, Memory Engine, Intelligence Engine) — what specific user/usage signal should trigger "now build this," so it's demand-driven rather than roadmap-driven?
10. **Constitutional Kernel enforceability**: The Kernel's 9 rules (safety before growth, financial correctness before speed, backend authority, auditability, recoverability, policy-driven capability, explicit state, traceable events, invisible complexity) — which of these are testable/lintable today vs. purely aspirational prose?

---

*End of compendium. Source: 10 prior AI review sessions on ZUKA's constitutional documents, Supabase backend, and Flutter/interaction architecture.*
