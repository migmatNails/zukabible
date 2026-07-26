# ZUKA — Concept Document
# The Gift Ticket Growth Loop ("a ticket from someone you trust")

> **Type:** Growth-Mechanism Brief (build-scoping, not a new proposal) ·
> **Tier:** MVP-adjacent (Phase 0 cut defined below) · **Status:** Ratified —
> already Bible-canonical (Vol 14 Ch 9 "Gift Tickets — the viral acquisition
> path"); this document does not propose a new entity or amend the Bible. Its
> job is the one thing Vol 14 leaves THIN: the acquisition mechanics around an
> already-specified state machine, so it is buildable as a real session.
> **Authority:** Subordinate to ZukaBibleV4. Reuses Vol 14 Ch1/Ch3/Ch4/Ch9/
> Ch10/Ch15 verbatim; adds no new canonical entity, state, or event — see §4.
> **Provenance:** Named as the pre-launch growth priority in the 2026-07-05
> compendium brainstorm (`docs/compendium/BRAINSTORM_2026-07-05_Decisions.md`,
> Thread D) — chosen over the (already-designed) Poster Acquisition Engine
> because it is digital-only, has zero venue dependency, and its data model
> already exists. Formalises the gap already logged as **THIN, Priority HIGH**
> in `CONCEPT_Unspecced_Features_Register.md` ("Gift Ticket — Viral
> Acquisition Mechanics").
> **Shared design challenge:** the non-user "what do they see before
> installing" problem is the same one `CONCEPT_Poster_Acquisition_Engine.md`
> §4 (Web Preview) and §6 (Deep-Link Survival) already solved for posters.
> This document reuses those patterns rather than re-deriving them — see §5.

---

## 1. The idea in one paragraph

A ticket a Person already owns can be gifted to someone else — a non-user
most of the time — via SMS/WhatsApp. The recipient sees a landing page (event,
donor's name, a message, a visual), taps "Claim your ticket," installs ZUKA,
and their **first experience of the app is the gift itself**. This is
already fully specified as a state machine in Vol 14 Ch 9 and already exists
in the `ticket` schema (`is_gift`, `original_purchaser_id` vs `owner_id`, the
`GIFT_INITIATED → GIFT_PENDING → ACCEPTED/REJECTED/EXPIRED` states, QR
rotation on escrow entry) — it has simply never been built. Per Vol 14 Ch 9,
it is Zuka's **highest-trust, near-zero-cost acquisition channel**: a
recommendation that isn't a recommendation, because it arrives as an actual
gift from a real person, not an ad.

## 2. What's already built vs. what's genuinely missing

**Already canonical, do not re-derive or re-propose:**
- The lifecycle: `ISSUED → GIFT_INITIATED → GIFT_PENDING → ACCEPTED (→
  ISSUED, new owner) / REJECTED (→ returns to donor) / EXPIRED (72h →
  returns to donor)` (Vol 14 Ch 3).
- `provenance_origin = 'gift_transfer'` is already a valid closed-system
  origin (Vol 14 Ch 1); `is_gift`, `owner_id` (nullable — escrow), and
  `original_purchaser_id` already exist on `ticket`
  (`supabase/migrations/20260630130003_ticket.sql`).
- QR handling: token invalidated on entering escrow, re-issued fresh on
  reject/expiry (Vol 14 Ch 4).
- **Rule 5 (Vol 14 Ch 10): gift expiry is non-negotiable — no override.**
  48–72h, non-extendable.
- The four domain events are **already registered**: `TicketGiftPending`,
  `TicketGiftAccepted`, `TicketGiftDeclined`, `TicketGiftExpired` (Vol 14
  Ch 15). No Bible edit needed to ship this.
- The one-line UX flow (Vol 14 Ch 9): SMS/WhatsApp → landing page → "Claim
  your ticket — download Zuka" → deep link with a transfer token → gift
  surfaces as the first app experience on signup.
- Pulse Points' "gift accepted 75" reward (Vol 14 Ch 14) is **Phase 2,
  DRL-gated, dormant** — explicitly out of scope here (see §6).

**Genuinely missing (the actual job of this document and its eventual build
session)** — verbatim from the Unspecced Register's THIN entry, now scoped:
1. The **non-user landing page** — what a recipient sees before installing.
2. The **viral flywheel mechanics** — gift → install → the new user's first
   experience is their gift, concretely.
3. **Group/table gifting as seat-level individual gifts** (Vol 14 Ch 9 names
   this but doesn't design it).
4. The **monetisation question** — is there a commission on a gifted ticket?
   (Open — see §6.)
5. The **exact WhatsApp/SMS link format** and what it previews.
6. No RPC exists yet for any of the four states. `expire_pending_tickets()`
   (S009) explicitly does **not** touch `GIFT_PENDING` — its own migration
   comment notes gift-expiry is "a different real-world case," deliberately
   left unhandled (`supabase/migrations/20260702100000_fn_expire_pending_
   tickets.sql:20-27`). A parallel sweep (or an extension) is net-new work.

## 3. Why this is the right pre-launch growth investment (not the poster engine)

From the 2026-07-05 brainstorm: digital-only, zero venue dependency, works
day one, and the data model is already built — the cheapest real next step
available, versus the Poster Acquisition Engine which needs physical venue
relationships to have anywhere to put a poster. Sequencing, not rejection:
Poster Acquisition Engine remains a strong Phase-2 candidate once venues are
live (`docs/concepts/CONCEPT_Poster_Acquisition_Engine.md`).

## 4. New entities this adds — none. Reuse is total.

No new table, no new column, no new domain event, no new ticket state. This
is the rare concept whose entire "new entity" section is empty — the design
work is 100% acquisition-flow and delivery-mechanics, not data modelling:

- **Net-new RPCs (SECURITY DEFINER, mirroring existing patterns):**
  `initiate_gift_ticket(ticket_id, recipient_phone, message)`,
  `accept_gift_ticket(transfer_token)`, `reject_gift_ticket(transfer_token)`.
- **Net-new sweep:** a `GIFT_PENDING` → `EXPIRED` cron job, parallel to (or
  extending) `expire_pending_tickets()` — Rule 5 says non-extendable, so this
  cannot be soft; the sweep is load-bearing, not cosmetic.
- **Net-new delivery:** an Edge Function to send the SMS/WhatsApp message
  (reuses whatever provider ZUKA already integrates for OTP/notifications;
  no new dependency — confirm before adding one, per ADR-016).
- **Net-new surface:** the gift landing page (web, non-user, read-only) and
  the app-side "claim" screen (post-install, first-open).

## 5. The gift landing page — reusing the Poster Engine's already-solved pattern

The non-user "what do they see before installing" problem was already solved
for posters (`CONCEPT_Poster_Acquisition_Engine.md` §4). The gift page is the
*same pattern*, different content — reuse it rather than re-litigate:

```
ZUKA · You've been gifted a ticket 🎁
<Event Name> · <date/time> · from <Donor First Name>
"<optional donor message>"

[ Claim your ticket — download ZUKA ]   ← ONE primary CTA, deep-link + token
(small) What is ZUKA? →                   ← secondary, never forces install
```

Same hard rules as the Poster preview apply directly: loads fast on 3G,
useful even if never installed (you can *see* what you were gifted before
committing to install), exactly one primary CTA, no dark patterns (no fake
countdown pressure beyond the real, honest 48–72h Rule-5 expiry — which
**should** be shown, since it's true and creates real, not fake, urgency),
honest framing (Vol 25 voice).

**Deep-link survival** (install → app open → the gift is still attached) is
the same hard problem the Poster Engine named in its §6 and already weighed
options for (App Links/Universal Links with a fallback token exchanged post-
install). Reuse that analysis rather than re-deriving it; the `transfer_token`
here plays the same role the poster's attribution token plays there.

## 6. Explicitly out of scope for the first build (Phase 0)

- **Pulse Points integration** ("gift accepted 75," Vol 14 Ch 14) — Phase 2,
  DRL-gated economy feature. A Phase-0 gift flow emits the four already-
  registered events regardless; wiring them to a points ledger is separate,
  later, and gated on Pulse Points' own activation, not this concept.
- **Group/table seat-level gifting** — Vol 14 Ch 9 names it, doesn't design
  it, and it's a materially different UX (N recipients, N landing pages, N
  claim flows) — a real Phase-1 extension, not part of the first cut.
- **Monetisation on gifted tickets** (commission?) — an open founder/
  `zuka-growth` decision, not an engineering one. A Phase-0 gift is a pure
  ownership transfer of an *already-paid* ticket — no new money moves, so no
  new ADR-026 (settlement) exposure exists unless a commission is later
  layered on top. Do not invent a fee structure here; log it as an open
  question (§7) until decided.
- **Organiser-initiated comp-as-gift** — Vol 14 Ch 9 describes *Person-to-
  Person* gifting of an owned ticket. An organiser issuing free/comp tickets
  through the gift *mechanism* (vs. the existing `comp` ticket_type/
  `comp_issue` provenance) is a different flow; do not conflate.

## 7. Open questions (carried from the Register — still genuinely open)

- Exact landing-page copy/visual system (owned by `zuka-designer` +
  `zuka-growth` — Vol 25 brand voice applies).
- Exact WhatsApp/SMS message format and what the platform preview (link
  unfurl) shows — depends on the chosen SMS/WhatsApp provider's capabilities.
- Group/table seat-level gifting design (Phase 1, not blocking Phase 0).
- The monetisation-on-gift model (§6) — needs a founder decision before any
  Phase beyond 0 that touches money.
- Anti-abuse: rate-limiting gift-initiation per Person (a free acquisition
  channel is also a spam vector — `zuka-security` should gate the build
  session, mirroring the existing rate-limit pattern from S010).

---

> **Gift Ticket Growth Loop — Concept (build-scoping, MVP-adjacent).** The
> idea is not new — Vol 14 Ch 9 already named it and the schema already
> carries it. What was missing was the acquisition-mechanics depth needed to
> turn a specified state machine into a real session. A ticket from someone
> you trust, arriving as itself — not an ad, not a notification, a gift.
