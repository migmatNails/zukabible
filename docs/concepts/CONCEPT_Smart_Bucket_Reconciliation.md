# ZUKA — Concept Document
# Smart Bucket Reconciliation (resolving a V4↔V7 name clash)

> **Type:** Reconciliation Concept · **Tier:** 2 (name-clash resolution)
> **Status:** Proposed · **Authority:** ZukaBibleV4 governs the term "Smart
> Bucket." This concept resolves the clash and proposes V7's richer reading as a
> FUTURE EXTENSION, not a redefinition. Feeds V4 via ADR.

---

## 1. The Clash

Both documents use "Smart Bucket" — for related but different things:

```
V4 (canonical, Vol 14):
  Smart Bucket = the ENTRY mechanic. A persistent per-person QR that INVERTS
  scanning: the attendee presents one bucket QR; the server matches it to a valid
  ticket for THIS event and admits them. The bucket is the key; the server holds
  the lock. Focus: frictionless entry, anti-fraud, the closed ticketing system.

V7:
  Smart Bucket = a secure, context-aware CONTAINER of all of a participant's
  rights, proofs, permissions, reservations, and operational tokens (tickets,
  food-order tokens, parking passes, delivery tokens…), that answers authorised
  scans with the minimum info. Focus: a universal operational wallet.
```

These are not contradictory — V7's is a **superset** of V4's. But two definitions
of one term is exactly the drift the Canonical Language Dictionary (Vol 03)
forbids. This concept fixes it.

---

## 2. The Resolution

```
"Smart Bucket" KEEPS its V4 meaning as canonical: the per-person entry/credential
mechanic with the inverted-scan + server-side matching + rotating token.

V7's richer "container of all operational tokens" is recognised as a FUTURE
EXTENSION of the same bucket — the bucket GROWS from holding entry rights to
holding other operational tokens (order tokens, delivery tokens, parking) over
time. It is the SAME bucket, extended in scope — NOT a different thing wearing
the same name.

So: one definition, one entity, an extension path. The Dictionary (Vol 03) gets a
single Smart Bucket entry; this concept is the roadmap for extending it.
```

---

## 3. The Extension Path (V4 bucket → V7-style operational container)

```
TODAY (V4, Vol 14): the bucket holds ENTRY rights.
  present bucket QR → server matches a valid TICKET → admit.

EXTENSION 1 — ORDER TOKENS (pairs with the Vendor Marketplace concept):
  when in-app ordering ships, a paid order places a collection/delivery TOKEN in
  the same bucket. A vendor's scanner asks "does this bucket have an order for
  me?" → the bucket answers minimally (order #, table, ready) — nothing else.

EXTENSION 2 — OPERATIONAL TOKENS:
  parking pass, VIP access, drink tokens, backstage — each an item the bucket
  holds and reveals only to an authorised scanner in the right context.

EXTENSION 3 — CONTEXT-ISOLATED REVEAL (V7's best idea, kept):
  a scan answers ONLY the question the scanner is authorised to ask. A food
  vendor scanning the bucket sees the food order and nothing else; the ticket,
  parking, ride token "disappear" from that scan's view. (Least privilege +
  V4 contextual-blindness, they agree.)
```

Each extension is gated by the feature that needs it (ordering needs the vendor
marketplace live first, etc.) — not built up front.

---

## 4. Constraints (V4 governs — and V7 agrees on most)

```
- THE BUCKET NEVER INITIATES (V7 Principle 11): it only answers authorised scans.
  No notifications, no predictions, no nudges from the bucket. Pure responder.
- ROTATING TOKEN (Vol 14): the bucket's QR rotates; it represents the bucket, not
  its contents. Rotation on state change / high-risk scan / manual refresh.
- ONE SCAN, ONE ANSWER (V7 Principle 24 + V4): the scan answers "does this bucket
  satisfy my context?" YES/NO + minimum info — never "show me the bucket."
- LEAST PRIVILEGE + CONTEXT ISOLATION: every scanner is sandboxed to its context;
  sees only what it's authorised to (V4 scanner binding + V7 isolation, aligned).
- PROVENANCE (ADR-015): every bucket item traces from creation to expiry; the
  bucket's contents are event-sourced.
- RLS (V4 security): who can place/read a bucket item is enforced at the database,
  not just the app.
```

---

## 5. New Concepts This Introduces (for eventual ADR)

```
ENTITIES (proposed — extend the V4 bucket, don't replace it):
  BucketItem      a held right/proof/token in a person's bucket. (person_id,
                  kind[ticket|order_token|access|proof], scope[event], status,
                  valid_from, valid_to, provenance_ref). Tickets are the first kind.
  ScanQuery       an authorised scanner's question against a bucket (scanner
                  capability + context → match? → minimal answer). Logged.

EVENTS (append-only, ADR-015):
  BucketItemAdded · BucketItemRevealed (to an authorised scan) ·
  BucketItemConsumed · BucketItemExpired

REUSED: the V4 Smart Bucket entry mechanic + rotating token + matching engine
(Vol 14), scanner capability (Scanner Pro concept), RLS, contextual-blindness.
```

---

## 6. MVP Cut Lines

```
PHASE 1 — NO CHANGE: the V4 bucket holds entry rights, exactly as Vol 14. Ship
  that. This concept changes NOTHING at MVP except locking the vocabulary.
PHASE 2 — ORDER TOKENS: when vendor ordering ships, extend the bucket to hold
  order/collection tokens (Extension 1).
PHASE 3+ — OPERATIONAL TOKENS + full context-isolated reveal (Extensions 2-3).

The point of this concept is mostly RECONCILIATION (one definition) + a roadmap,
not new MVP build. The vocabulary fix is the immediate deliverable.
```

---

## 7. Open Questions

```
- Dictionary update: add the single reconciled Smart Bucket entry to Vol 03 via
  ADR (the one place the term is defined).
- Confirm the extension path doesn't bloat the MVP bucket — Phase 1 is unchanged.
- Order-token reveal vs the vendor-direct money model: the bucket holds an order
  TOKEN (proof of a paid order), not money — consistent with NPS-Act posture.
```

---

## 8. Reveal-discipline hardening — rotation cadence, QR-optional, presence

> **Status:** Proposed (hardens §4 constraints). **Provenance:** ZUKA Scanner Pro /
> Smart Bucket hardening brainstorm, 2026-07-09, with a `zuka-security` design audit
> (designated `smart_bucket_qr` agent) the same session. Evidence:
> `20260703100000_qr_token_rotation.sql:145-150, 282-288`,
> `20260705110001_fn_check_in_ticket_vendor_branch.sql:187-209`, Vol 10/11
> (Memory-OS / Trust seed), Vol 14 Ch 4. Subordinate to ZukaBibleV4; a proposal —
> graduates via `zuka-reality-checker` + ADR. The server-side enforcement this
> reveal discipline assumes lives in [[project]] Scanner Pro §11 (the resolver
> boundary + non-destructive-oracle rules).

### (a) Constant-cadence rotation — no client-observable trust-state channel

A rotation cadence that **adapts to risk** and is signalled by a client-rendered
freshness cue would leak trust state out the presentation layer, past RLS — a
direct violation of the **trust-state non-exposure invariant** (raw trust dimensions
readable by no client role). A flagged bucket rotating faster *is* a visible risk
score, readable by the holder **and by the scanner**.

**Constraint (walkaround):** rotation cadence is **constant / state-change-only,
identical for every bucket regardless of risk** — this is exactly today's build
(`qr_token_rotation.sql:145-150, 282-288`; no risk-modulated cadence exists in the
schema). A risk engine (DRL-gated, dormant at MVP) may act **only** on invisible
server-side / human-in-the-loop levers per **ADR-014** — flag for human review,
silent server-side decline, step-up re-auth — and **never** modulates any
client-rendered artifact. The freshness animation is **cosmetic, constant-rate,
decoupled** from real rotation timing. This **supersedes §4's earlier "manual
refresh"**: users cannot manually rotate, and no countdown / next-rotation time is
displayed.

Three residuals to pre-close when the risk engine eventually wakes:
1. **Timing** — a silent server-side decline must return within the **same latency
   envelope** as an accept (≤400ms, Vol 14), or response time is the trust signal.
2. **Step-up visibility** — a re-auth challenge is observable to a counterparty
   watching the holder's screen; it must be **indistinguishable from routine random
   sampling** and never state *why*.
3. **Error codes** — a risk decline must **fold into an existing generic outcome**,
   never a new SQLSTATE that means "declined for risk."

### (b) Interval rotation is Phase-3, online-only — not MVP

Interval / TOTP rotation fights offline-first: a pre-downloaded offline manifest
cannot validate a token it has never seen, and "offline ⇒ rotation off" is a
**network-jam downgrade attack** (jam the gate → strip the defence → replay a
screenshot). Its only unique win over *state-change rotation + single-use check-in*
is defeating a **time-shifted, pre-shared** screenshot — a narrow slice. **MVP keeps
state-change rotation only** (built, offline-safe). True interval rotation → **Phase
3, online-lane only, explicitly documented as never degrading the offline door.**

### (c) "QR optional" splits by STAKES — and presence is value-bearing

Ambient / proximity access (authenticated device, location, workflow) is allowed
**only for zero/low-stakes, non-value-bearing** contexts — the holder re-opening
*their own* credential on *their own* authenticated session, discovery. **Value-bearing
actions always require the signed credential presented to an authenticated scanner;
location / proximity is never sufficient auth for a value action** (GPS spoofs
trivially).

**Value-bearing =** any action that (1) transfers economic value, (2) consumes a
one-time / finite entitlement, (3) grants physical admission, or (4) **writes a
Crown-Jewel ledger — Participation, Payment, Trust, or the Event Store.**

**Sharp carve-out — "presence" is value-bearing, not low-stakes.** A scan emits
`AttendanceSeedRecord` (`fn_check_in_ticket_vendor_branch.sql:187-209`), the
Memory-OS / Trust seed (Vol 10/11). If proximity-based presence emitted that seed,
**location would become sufficient to write the Participation Ledger and feed the
Trust Graph** — the one corruption that cannot be rebuilt. So proximity "presence"
in the low-stakes bucket is **cosmetic-only** (the holder sees their own "you're
here"); it **must never** emit `AttendanceSeedRecord` or any participation/trust
seed. Only a **scanned signed credential on an authenticated scanner** writes the
seed.

### (d) Item-scope discipline (reaffirms §6 Phase 1)

The MVP bucket holds **entry rights only**. A "settlement right" is a **proof/claim
token, never a money instrument** (vendor money stays vendor-direct, NPS-Act posture
— §7). **Identity proofs** are deferred (DPPA sensitive-data; when built, a
verifiable-claim *reference*, never raw ID bytes), and **passwords are not a bucket
item at all** — dropped entirely (pure blast-radius, zero MVP value). These belong to
the dormant "universal wallet" extension, gated by the feature that needs each.

---

> **Smart Bucket Reconciliation — Concept (Tier 2)**
> One term, one definition: the V4 entry mechanic. V7's "operational container"
> is the SAME bucket, extended over time — never a rival. Vocabulary locked,
> roadmap drawn.