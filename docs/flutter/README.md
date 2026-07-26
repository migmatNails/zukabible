# ZUKA — Flutter Engineering Docs

Engineering reference for the Flutter app itself — not UX/UI (that's
`docs/design/`), not backend architecture (that's `docs/architecture/`). This
is where a session building Flutter screens looks first, before inventing
its own conventions.

## How this differs from the other docs/ directories

```text
docs/design/       WHAT the app looks like and how it behaves — screens,
                    flows, states, copy, colour logic. Living doc, UX-owned.
docs/architecture/ WHAT the backend is and the hard-to-reverse technical
                    decisions (ADRs) that govern it, Flutter included —
                    ADR-021/022/023 froze backend contracts a Flutter client
                    must match exactly; ADR-028 (below) does the same for
                    the app's own shell.
docs/flutter/       HOW the Flutter codebase itself is built — engineering
                    conventions, ready-to-paste design-token/RPC-contract
                    reference material, and the register of what NOT to
                    build yet so it never gets re-litigated per session.
```

## Status

**Working reference — the living UX spec (`docs/design/`) and the ADRs
(`docs/architecture/`) remain authoritative wherever they overlap with
anything here.** This directory exists so a Flutter build session doesn't
have to re-derive engineering conventions or re-run a 51-item overbuilding
audit from scratch every time.

## Index

| Document | Purpose |
| --- | --- |
| [ZUKA_FLUTTER_DESIGN_TOKENS.md](ZUKA_FLUTTER_DESIGN_TOKENS.md) | Vol 25 (Brand & Identity, Ch 4/5) ported into ready-to-paste Dart `Color`/`TextStyle` constants — colours, type scale, a proposed spacing scale (Vol 25 doesn't lock one). No values invented; every token cites its Vol 25 line. |
| [ZUKA_FLUTTER_RPC_CONTRACT.md](ZUKA_FLUTTER_RPC_CONTRACT.md) | How every Supabase RPC call from Flutter must be wrapped: the `ZK4xx` custom-SQLSTATE convention (`ZK423`/`ZK429`, already real in the backend) plus standard Postgres codes, mapped to human copy (Part IV's "Unified Error Language"); the NO-CACHE list (ADR-021); idempotency-key conventions for money-moving RPCs. |
| `docs/architecture/ADR-028.md` (lives in architecture/, not here — it's a frozen decision, not a living reference) | State management, navigation/router, and project-folder-structure choices for the Flutter shell — the genuinely hard-to-reverse decisions, same pattern as ADR-023's frozen offline-scanner contract. |

## The Flutter Dormant/Deferred Register (from the 2026-07-05 Flutter audit)

`docs/compendium/ZUKA_Constitutional_Compendium.md` Parts IV/V proposed 51
distinct Flutter/"ZUKA Mobile OS" ideas. A `zuka-designer` + `zuka-reality-
checker` pass (full findings: `docs/compendium/` brainstorm thread, same
date) classified every one of them. The two "don't build this" buckets are
recorded here so no future session re-proposes them from a fresh reading of
the compendium:

**NOT-NEEDED (13) — DRL-1/zero-user violations; do not build regardless of
how the idea is later reworded:**
Intelligence-Engine-flavoured feed personalization/recommendations · Adaptive
Interface (UI matures by usage history) · Living Interface (narrative
homepage — actively **contradicts** the shipped §1 City Pulse scannable-feed
design, not just premature) · Emotional Operating System · full 9-role
Human-Layer/Context-Engine (battery/calendar/wallet-state fusion) ·
Contextual-Intelligence-as-a-generalized-engine · Feature-flags-over-forks as
a build (the seam is already reserved, §0 contract 6) · Growth Architecture
(A/B/experiment infra — zero users, nothing to test against) · AI Experience
Layer · Search Framework (no search screen spec'd yet) · i18n/regionalization
framework (Kampala-English MVP; a real trigger, not now) · Experience DNA as
a formal process artifact (the living doc's per-screen template already does
this job) · ZUKA Participation Loop as a Flutter build item (it's product
strategy, not a frontend engineering decision).

**NOT-YET (9) — real and correctly urgent, but blocked on a later screen
existing first, not on more design work:**
Offline Sync Engine (ADR-023's Bloom-filter manifest contract is real and
frozen server-side; a client can't be built against it until the scanner
screen exists) · Notification Framework (FCM is in the stack; needs the
ticketing flow to have something to notify about) · Error Recovery Framework
for payments (needs the purchase flow built first) · Security-on-device
token/session handling (needs auth to exist) · Contextual permission engine
beyond what §0/§3 already reserve · Production Hardening beyond Sentry
wiring · Robust-RPC-integration's full retry/timeout policy (start with the
`ZUKA_FLUTTER_RPC_CONTRACT.md` shape; harden once a real flow exists) ·
Sync-Engine-as-a-generic-layer (build the one real case — scanner — first) ·
Operational Readiness beyond crash reporting (kill-switches, maintenance
mode — no real ops yet).

**Do not write a "Frontend Constitution" or "ZUKA Mobile OS Constitution"
document.** Both agents in the audit independently flagged this as the exact
"one more constitution before we build" pattern already caught once in this
project's history (Part III of the compendium literally recommends the
opposite: stop designing, build the app). The living doc's §0 contracts are
the constitution that matters, and they're data-contract-shaped, not
aspirational prose.

## What survived — the actual pre-work

Six things were classified DECIDE-BEFORE-SESSION-1 (expensive to reverse,
worth locking in before writing code): the app-shell skeleton, state
management, navigation/routing, design tokens, the RPC-client wrapper shape,
and the build target itself (§1 City Pulse — the only DRAFTED screen). The
first four now have answers in this directory and ADR-028; the RPC-client
shape is in `ZUKA_FLUTTER_RPC_CONTRACT.md`; the build target is unchanged —
wire real screen, real RPC, prove zero-login discovery in a real hand.

Everything classified EMERGES or ALREADY-ANSWERED in the full audit needs no
separate document — it resolves correctly by building the first real screen,
or is already covered by the living doc / an existing ADR.
