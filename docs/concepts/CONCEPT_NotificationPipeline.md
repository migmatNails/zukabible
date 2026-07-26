# CONCEPT — Async Transactional-Notification Pipeline (REFUND_QUEUED → buyer SMS)

- **Type:** Infrastructure / compliance concept (ZUKA's first async-notification seam)
- **Tier:** MVP (the refund-notice slice) + a thin, deliberate reuse seam
- **Status:** **Proposed** — design pass complete; awaiting founder rulings before the S033 build
- **Authority:** Subordinate to ZukaBibleV4 and SOUL.md. Feeds V4 via ADR only. This is
  design space, not law.
- **Provenance:** Design pass 2026-07-14, dispatched from RS032's "next build" analysis
  after grounding revealed the SMS loop is a *from-scratch* pipeline, not an extension.
  Three Bible-aware agents ran in parallel: `zuka-researcher` (architecture),
  `zuka-legal` (DPPA), `zuka-designer` (buyer experience + copy). Evidence anchors:
  `supabase/migrations/20260714090000_fn_confirm_ticket_payment_inflight_gathering_terminal_guard.sql:500-513`
  (emitter/payload) · `supabase/functions/flutterwave-webhook/index.ts:118-127`
  (verify-by-reference precedent) · `supabase/functions/initiate-flutterwave-payment/index.ts:113-118`
  (phone unreliability) · `supabase/migrations/20260710090000_fn_register_person.sql:54-56`
  (no phone on Person) · `supabase/migrations/20260630120000_enable_extensions.sql:24,31`
  (pgmq/pg_cron enabled) · `docs/concepts/CONCEPT_EventStore_PII_Erasure.md` §2 (PII-by-reference).

---

## 1 · The problem

A buyer's MoMo payment succeeds, but the Gathering ends/closes/is cancelled (or the tier
is gone) while the confirm is in flight, so the ticket is routed to `REFUND_QUEUED`
(S030/S031/S032). Today that fact is **passive-only** — surfaced in `my_tickets` if the
buyer happens to open the app. A buyer whose Gathering died will not open it. The refund
machinery exists; nothing tells the human. This concept is the pipe that closes that loop,
Africa-first (SMS is the channel that lands on a 2GB Android over 3G).

## 2 · Architecture (recommended)

**`pg_cron` scans the immutable event store → `pgmq` → an Edge Function consumer calls
Africa's Talking.** The money function is never touched — the notifier is a pure downstream
consumer of the already-committed `PaidTicketRefundQueued` event (the ADR-015 Outbox). This
is RS032's hard lesson applied: **side-effects live downstream of the money path, never
inline in it.**

```
confirm_ticket_payment ──emits──> domain_events (PaidTicketRefundQueued)   [untouched money code]
                                        │
        pg_cron (* * * * *, LIMIT N) ───┤ scan for events with no notification_dispatch row
                                        │ INSERT notification_dispatch ON CONFLICT DO NOTHING  (dedupe)
                                        │ pgmq.send(domain_event_id)        (PII-free payload)
                                        ▼
        Edge Function `send-sms` ── pgmq.read ──> resolve phone by reference
                                        │  (Flutterwave verify, flw_transaction_id) — NOT stored
                                        │  Africa's Talking POST /version1/messaging
                                        ▼  update notification_dispatch state; pgmq.delete on success
```

**Rejected alternatives:** an `AFTER` trigger enqueuing inside the money transaction
(re-couples a side-effect to the money commit — a failing/contending `pgmq.send` could roll
back a payment confirmation); Supabase Realtime *as the drain* (at-most-once fan-out to
connected clients, drops when no consumer is up). Realtime remains the right tool to push
the **passive** `my_tickets` surface live — a different job.

**Delivery guarantee:** at-least-once with a strong dedupe (`notification_dispatch.domain_event_id
UNIQUE` + a `PENDING→SENDING→SENT` guarded transition). Africa's Talking exposes no
idempotency key, so true exactly-once is impossible; the residual (AT-succeeded-but-DB-update-
crashed) yields at most one *duplicate* SMS — a cosmetic annoyance, never a money/trust defect,
and never silent (every attempt is timestamped + counted). Acceptable because the passive
`my_tickets` surface is always the system of record; **SMS is a courtesy accelerator, not the
channel of last resort.**

## 3 · Phone source — the load-bearing decision (researcher + legal agree)

The reachable number is **not** on the Person row (empty by design) and **not** reliable on
`auth.users.phone` (OTP-optional, often empty). It is the **MoMo number that consummated the
payment**, which lives only in the Flutterwave transaction. The consumer resolves it
**just-in-time** via `GET /v3/transactions/{flw_transaction_id}/verify` (the webhook already
trusts this exact endpoint), passes it straight to Africa's Talking, and **discards it**. It
is **never** persisted to `notification_dispatch`, `domain_events`, or any ZUKA table.

- **Legal (DPPA):** the payment number may be reused for **this refund notice only** — a
  directly-connected, compatible purpose (DPPA §9/§10 + fair-processing §3). Copying it into a
  ZUKA table (a new erasable-PII store with its own consent/retention/breach duties) is a
  **HIGH-severity anti-pattern** — resolve-at-send, do not copy-and-hold.
- **Net:** no raw PII enters an immutable row; no new erasable-PII table; the DPPA surface stays
  minimal. What persists is the AT `messageId` (a non-PII correlator).
- **Build caveat (PLAUSIBLE):** the exact field `data.customer.phone_number` on the v3 verify
  response inherits the webhook's "field names unverified against live sandbox" caveat — S033
  must confirm it against a real sandbox transaction.

## 4 · Legal posture (inputs for qualified Ugandan counsel — not final advice)

| # | Question | Ruling |
|---|---|---|
| 1 | Refund SMS = transactional? | **ALLOWED** under `consent_data_processing`; it is a service message about the buyer's own transaction, **not** marketing. `consent_marketing` is **not** the gate. **Guardrail: zero cross-sell** — one promo clause re-characterises it as marketing (Reg 34). |
| 2 | Source phone from PSP record | **ALLOWED** for this refund only; resolve just-in-time; no reuse/promotion/verification-promotion. |
| 3 | Store the phone? | **MINIMISE — default do-not-store.** If a transient send-queue holds it, bounded TTL then purge. Keep masked audit metadata only. |
| 4 | Human-executor contact access | **OBLIGATION** — minimised view + a who/when/what/why **append-only access log**; refuse access without an open refund. *Binds the separate human manual-refund tool, not this automated path (see §6).* |
| 5 | SMS content + sender/opt-out | Allowed: refund fact, **amount**, `ticket_ref`, Gathering name. **Registered alphanumeric Sender ID** (UCC; lead time). **No STOP opt-out** on a transactional message — a buyer must not be able to opt out of being told about their own money. |
| 6 | Null-owner / no consent on file | **ALLOWED** on the transactional lawful basis (own transaction, money owed, fairness), **not** the app consent flag. This is the highest-value counsel confirmation. |

**Standing counsel items** (do not block the build; block the first *live* send): confirm the
transactional-vs-marketing classification, the retention period, Sender-ID/opt-out reliance,
and the no-consent-path basis. These fold into the founder's standing "engage qualified
Ugandan counsel before scale" item (CLAUDE.md §8, Vol 23).

## 5 · Buyer experience + copy (designer)

One buyer truth for all four internal `reason` values: *"We took your payment, the Gathering
is no longer available, your refund is on the way."*

- **Copy (GSM-7, ~1 segment), parameterised on `{gathering_name}`, `{amount}`, `{N}`:**
  > `ZUKA: Your payment for {gathering_name} went through, but it is no longer happening. We
  > are refunding {amount} to your MoMo within {N}. See details in the ZUKA app.`
  A long-name fallback variant drops the name to guarantee one segment. Keep all fixed copy in
  GSM-7 (no emoji/curly-quote/en-dash → those force UCS-2 at 70 chars/segment, 3× cost).
- **`{amount}` renders `UGX 20,000`** — **CONFIRMED: `amount_ugx` is BIGINT integer *minor*
  units** (SOUL.md/NPS-Act invariant, `ticket.sql:9`, `gathering_tier.sql:28`), so the renderer
  **must ÷100** for display. A raw print is 100× wrong. S033 confirms the multiplier against a
  real transaction.
- **States mirror `my_tickets` (SMS points, app is source of truth):** `Queued → Refunded`,
  plus `SMS undeliverable` (≤1 resend then stop) and `No phone` (passive card is the only
  channel). Card tone: **Neutral** colour — a refund is a calm administrative state; an
  urgency colour would manufacture anxiety. Card sub-line sets the ADR-014 expectation
  honestly: *"A person on our team processes this — it is not instant."*
- **Anti-dark-pattern:** states the fact, names the money, names the window, nothing to buy,
  no fake urgency, no STOP; the refund happens whether or not the buyer acts.

## 6 · What binds this merge vs the separate human path

The **automated** SMS path never shows a human the buyer's number — the Edge Function fetches
and discards it. The refund itself is executed at the PSP by `tx_ref`/`refund_ref` (the human
never needs the phone). So the **executor contact-data access-log** obligation (§4 row 4) binds
the *separate* manual go-live tooling (where a human might view/call a buyer), **not** this
pipeline — which materially de-risks S033. The access-log ships when the human-executor tool
does; it is a distinct go-live workstream.

## 7 · New entities (only the genuinely new)

1. **`public.notification_dispatch`** — dispatch ledger + dedupe. Sketch: `id`,
   `domain_event_id UNIQUE`, `ticket_id`, `event_type`, `template_key`, `channel DEFAULT 'sms'`,
   `state`, `attempt_count`, `at_message_id`, `last_attempt_at`, `created_at`. **No phone
   column.** RLS: service_role/ops only, no client role.
2. **`pgmq` queue** `notify_sms` — carries `domain_event_id` only.
3. **`pg_cron` job** `drain_notification_dispatch` — `* * * * *`, `LIMIT N`/tick.
4. **Edge Function `send-sms`** — pgmq consumer; resolves phone by reference; calls AT; holds
   `AT_API_KEY`/`AT_USERNAME`/`AT_SENDER_ID` (Edge secrets only, mirroring the Flutterwave-key
   boundary).
5. **(Deferrable) Edge Function `sms-delivery-report`** — receives AT DLR callbacks, `SENT→
   DELIVERED/FAILED`. If deferred, dispatch stops at `SENT` and the passive surface covers it.

No existing entity is redefined; `confirm_ticket_payment`, `domain_events`, `ticket`, `person`
are untouched.

## 8 · Reuse seam (thin, built now; framework deferred)

MVP ships one-event→one-SMS. The **only** reuse structure built now is (a) the cron filters an
*allowlist* of event types seeded with one value, and (b) `notification_dispatch` carries
`template_key`/`channel` the consumer switches on — one branch today. That is the whole cut-line:
a column and a switch. A template engine, a preferences/quiet-hours system, a multi-channel
router, per-Person channel prefs — **all dormant** (three-axis: DRL-0 operational notice; no
intelligence). Adding ticket-issued confirmations or gift notices later = add an event type + a
template, reusing queue/consumer/table unchanged.

## 9 · MVP cut-line for the S033 build

**S033 builds:** `notification_dispatch` (+RLS) · `pgmq` queue · `pg_cron` drain (scan
`domain_events` for `PaidTicketRefundQueued`, `LIMIT N`, `ON CONFLICT` dedupe, per-(buyer×
gathering) coalescing) · Edge Function `send-sms` (phone-by-reference, AT call, state machine
to `SENT`) · the parameterised copy (÷100 amount) · hard-fail tests (dedupe, no-double-send,
no-phone branch, coalescing, minor-unit display, PII-never-stored). Gates: `zuka-security` +
`zuka-legal` **plan AND diff** (payment_rails-adjacent), `zuka-tester`, `zuka-reviewer`,
`zuka-reality-checker`.

**Deferred (S034 candidates):** the DLR callback function (`SENT→DELIVERED`); a
confirmation-on-execution SMS; a `RefundNotificationSent` provenance event; localization.

## 10 · Constitutional touchpoints

- **ADR-014** — SMS is a **NOTICE only**; copy says "refunding / on the way," never "you have
  been refunded"; no auto-refund; a human executes; wallet frozen.
- **ADR-015** — money function untouched; the notifier only *reads* the immutable event and
  writes its own mutable ledger. A `RefundNotificationSent` event, if adopted, is additive.
- **Money posture (NPS Act 2020)** — SMS moves no money; posture unaffected.
- **Three-axis readiness** — DRL-0 operational notice; the §8 seam must not creep into a dormant
  framework.
- **DPPA 2019** — §4 counsel items; the by-reference design (§3) minimises exposure either way.
- **Locked stack** — pgmq · pg_cron · Edge Functions · Africa's Talking are all in CLAUDE.md §3;
  **no new ADR.** An ADR *would* be required only for a non-stack queue (Rabbit/Kafka), a
  non-stack SMS vendor (Twilio/SendGrid), or a dedicated worker runtime — none used here.

## 11 · Founder decisions required (before S033)

| # | Decision | Recommendation |
|---|---|---|
| 1 | **Window `{N}`** — the honest worst-case refund turnaround (BLOCKS the copy) | `within 5 business days` (under-promise, over-deliver; ADR-014 human execution) |
| 2 | **Sender ID** string + start Africa's Talking registration (lead time; gates go-live independently of code) | registered alphanumeric `ZUKA` |
| 3 | **SMS grain** — one per ticket, or one per (buyer × gathering) with aggregated amount + count | one per **(buyer × gathering)**, aggregated |
| 4 | **Confirmation-on-execution SMS** — send one when the refund is executed, or rely on the telco's own reversal SMS | defer to S034 (keeps S033 tight); the telco SMS covers the gap short-term |
| 5 | **Batch cap `N`/tick** + **queue-depth alert threshold** (burst-spend + ops signal) | founder sets; seed conservative, tune |
| 6 | **`RefundNotificationSent` provenance event** — emit or keep state in `notification_dispatch` only | keep in `notification_dispatch` (the append-only store stays money/trust facts, not delivery telemetry) |
| 7 | **Localization** — English-only MVP, or English/Luganda | English-only MVP (post-MVP seam) |

**Also pin (operational go-live carries, not code):** the named refund-queue owner, and the
queue-depth alert — both already standing carries from S030/S031/S032.
