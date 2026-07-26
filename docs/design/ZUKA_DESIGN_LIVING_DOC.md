# ZUKA — Living Design Specification
> The authoritative UX/UI/GUI spec for ZUKA. Maintained by the zuka-designer
> agent, appended to as the backend grows. Specs only — no code.
> ZUKA is Human Participation Infrastructure. The city just came alive.

## How to use this document
Frontend and visual-design work starts here. Each screen/flow has its own
section with flow, components, states, copy, colour logic, and UX rules.
The Status Index below shows what's designed and what's pending.
Design sessions are run with the prompt system in `prompts/` (Constitution +
Brief + Catalogue); HTML mockups land in `mockups/`. This document remains
the spec of record.

---

## Status Index
| Screen / Flow | Status | Last updated | Section |
|---------------|--------|--------------|---------|
| Feed System Architecture (contracts) | DRAFTED | 2026-07-04 | §0 |
| City Pulse — Home Feed | DRAFTED | 2026-07-04 | §1 |
| Nearby / Map | REVIEWED | 2026-07-18 | §2 |
| Dynamic Action Matrix | DRAFTED | 2026-07-04 | §3 |
| Saved · Following (secondary) | REVIEWED (RFDES02) | 2026-07-11 | §4 |
| Go Live — Organiser Sign-in → Create → Publish → Live | DRAFTED | 2026-07-19 (F018 §5B Type multi-select amendment) | §5 |
| Reset password (organiser · emailed code) | BUILT (F036) | 2026-07-24 | §5A-R |
| Ticket Tiers — Organiser editor (Tickets step) | BUILT (F006) | 2026-07-13 | §5E |
| Tier amendment after go-live (organiser · "Your gatherings" → Edit tickets) | BUILD-READY (F017) | 2026-07-19 | §5E-A |
| Gathering Detail (the 3-tap launchpad) | BUILT (F008) — reader + FREE claim & tier-select live; PAID path deferred | 2026-07-16 | §6 |
| Purchase & Pay (MoMo via hosted checkout) | REVIEWED (RFDES01/02) | 2026-07-11 (F1 amendment + §7.2b free claim) | §7 |
| My Ticket · My Tickets (QR) | REVIEWED (RFDES01/02) | 2026-07-11 (§8.2 tab amendment) | §8 |
| App Shell & Navigation ("Two Doors") | BUILT (F005) | 2026-07-13 (shell; §4/§6/§7/§8/§10 unchanged) | §9 |
| Scanner (M0 check-in) | REVIEWED (RFDES03, fixes applied in-review) | 2026-07-11 | §10 |
| Vendor Console (browse tiers · register · live-status toggle) | BUILT (F009) — context resolver + N1 confirm (F010) | 2026-07-16 | §11 |
| Visual Foundation Sheet (A-00 · tokens, card anatomy, states, motion) | REVIEWED (Variant A + spacing ratified) | 2026-07-18 | Design Foundations |
| Receipts — in-app · share image · document (A-04/05/06 trio) | REVIEWED | 2026-07-18 | §12 |
| Clips — list rows · social snippets · video previews (A-13/14/17) | REVIEWED | 2026-07-18 | §13 |
| Message templates &amp; screen scaffolds (A-15/16) | REVIEWED | 2026-07-18 | §14 |
| Documents — printable ticket · vendor statement (A-18/19) | REVIEWED | 2026-07-18 | §15 |
| Venue self-service — create (pin-drop) &amp; deactivate, in the VenuePicker sheet | DRAFTED (F021) | 2026-07-20 | §16 |
| Vendor Booths Manager (`/vendor/booths` mode · booths list · 3-band editor · plan-cap · category-lock) | BUILT (F035) · REVIEWED (zuka-designer conformance, 2026-07-24) | 2026-07-22 | §17 |
| Vendor Hub — Orders tab (C3 · live queue · manual-collect · 7-state chip · reject sheet) | BUILT (F033) · REVIEWED (retro-pass, closes F033-F1) | 2026-07-22 | §18 |
| Vendor Discovery — attendee booth feed + booth detail (B1 · read-only, payment-blind) | BUILT (F034) · REVIEWED (design-conformance pass) | 2026-07-23 | §19 |
| A-20 Poster templates (midnight · ember_gold · kampala_forest) | DRAFTED (round-trip) · engine BUILT (T1) | 2026-07-24 | §20 |
| A-21 Tier-specific ticket templates (ordinary · vip · table · vendor) | DRAFTED (round-trip) · blocked on ADR-037 O4 build (T2) | 2026-07-24 | §21 |
| Smart Bucket credential screen (`/tickets/:ticketId` · the holder face) | BUILT (F037, credential seam inert) · SPEC'D retro (closes RF037-8) — 3 rulings + 2 founder calls open | 2026-07-25 | §22 |
| Vendor Settle-balance (VS-4 · fee balance · saved-number picker · STK-push pending · SETTLED/FAILED/HELD · Channel-B notice · kyc_required) | DRAFTED (backend built dark on `session/VS-integ`) | 2026-07-24 | §23 |
| (more appended as backend grows) | — | — | — |

Status values: PLANNED · DRAFTED · REVIEWED · BUILD-READY · BUILT

---

## Design Foundations

### Voice & Tone
Direct · present-tense · local-first · warm, never hype · specific over clever.
Empty states give direction, not mood. Names real places, real numbers.

### Colour as Information (hard rule)
- **Coral** — live right now (reserved; never decorative)
- **Gold** — starting soon / primary action / active state
- **Forest** — tonight / scheduled
- **Neutral** — free / general info
- **Midnight** — base background (the city at 2am)

### Motion as Information (hard rule · founder-approved 2026-07-16)
The motion analogue of the colour law: the app is calm, instant infrastructure
by default, and **motion is reserved for liveness** — when something moves, the
city came alive there. Three tiers:

- **T0 — Structure** (navigation, tabs, chrome): silent or near-instant.
  Nothing springs, nothing bounces.
- **T1 — Feedback** (tap acknowledgment, content swaps, sheets, skeleton→content,
  route pushes): quick (120–280ms), standard curves. Confirms; never entertains.
- **T2 — Life** (LIVE pulse dot · go-live coral bloom · ticket/QR reveal): the
  ONLY expressive motion — longer, characterful. Reserved exactly like coral.

**Never-list:** opacity+transform only (no layout animation on lists, no blur,
no large-area loops — 2GB-RAM/battery doctrine) · money & trust surfaces are
motion-silent (prices never count up, trust states never shimmer) · motion
never gates a sacred proof (transitions interruptible, taps register
mid-transition, the 3-tap path never waits) · no stagger cascades on lists,
ever · everything respects reduce-motion (OS setting honoured; loops render a
static frame, one-shots render their end state).

**Per-surface transition map** (tokens in
`docs/flutter/ZUKA_FLUTTER_DESIGN_TOKENS.md` §5):

| Surface / moment | Motion | Tier |
|---|---|---|
| Pulse ↔ Tickets tab switch (§9) | none — instant swap (tabs are infrastructure) | T0 |
| Feed → Gathering Detail (§1→§6) | fade-through + slight slide-up, `route` 280ms | T1 |
| `/host` · vendor console entry (modes, §5/§9.5) | modal slide-up from bottom, `route` — motion says "you entered a mode" | T1 |
| Sheets (AccountSheet, overflow) | stock bottom-sheet motion, `standard` | T1 |
| Skeleton → content (§1) | 200ms cross-fade, never a layout pop | T1 |
| LensControl lens switch (§1) | 200ms content cross-fade; band headers static | T1 |
| LiveConfirmation (§5) | **coral radial bloom** — one-shot scale+fade ~700ms, then the existing auto-route | **T2** |
| Ticket QR reveal (§8) | gentle ~400ms scale-fade settle — the QR *arrives*. Build-later: add a `settle` token to §5 then (never a hardcoded duration) | **T2** |
| List item entrances | none beyond the skeleton cross-fade (no cascades) | T0 |
| StatusPill *becomes* live in view | RESERVED: one-shot coral pulse — needs Realtime; spec'd now, built later | T2 |

Hero poster flight (feed card → detail) is deliberately deferred: gatherings
have no posters yet; revisit when the create flow gains one.

### Global UX Laws
Invisible sophistication · 3–5 taps per core action · most actions < 30s ·
progressive disclosure · **no login wall to browse** · offline + SMS-fallback
states are first-class · never show raw scores (use human-readable states) ·
AI is invisible · no dark patterns · designed for 2GB-RAM Android on 3G.

### The Three Sacred Proofs (every relevant flow honours these)
1. **60-second Go Live** — organiser taps Go Live → discoverable ≤ 60s.
2. **Zero-friction discovery** — first open shows live events, no signup gate.
3. **3-tap ticket** — detail → pay → confirm. *(Hosted-checkout composition,
   reconciled per RFDES02-C2: 2 in-app decision taps + the external
   Flutterwave approve — every button enumerated in §7.1.)*

### Shared Component Library
Reusable components catalogued here as designed, so screens reference them
instead of redefining.

- **EventCard** (seed) — the core feed unit. Shows: title, venue, distance,
  time/live-status, price or "Free", one information-colour status pill.
  Variants: live · starting-soon · scheduled. Tap → detail.
- **StatusPill** (seed) — small rounded label carrying an information-colour.
  Live (coral, optionally pulsing) · Starting soon (gold) · Tonight (forest) ·
  Free (neutral).
- **CityPulseStrip** (seed) — top-of-feed summary bar: live-count + 6-hour
  activity sparkline. Communicates city energy before any event is read.
- **FeedItem** (2026-07-04) — the ONE polymorphic card the feed renders. Data
  shape `{ id, kind, provenance, state, title, when, where, price, statusPill,
  actions[], badges[] }`. EventCard is its `kind:gathering` render; Signal, Digital,
  Promoted, (future) Vendor/Memory are other `kind`+`provenance` values. New content
  type = new data, never a new card codepath (§0 contract 1).
- **SignalCard** (2026-07-04) — the `provenance:external-unverified` FeedItem
  variant: ghosted/dashed edge, *"Unconfirmed · spotted on [source]"* ribbon,
  confidence shown as a human state ("a few people confirm"), corroborate-first
  action row. Never a number, never above a real Gathering.
- **ActionRow** (2026-07-04) — the dynamic action strip on a FeedItem, resolved
  from the `(kind × provenance × state) → policy` table (§3). Each action declares
  its login requirement + DRL gate; the soft-wall is enforced here, once.
- **LensControl** (2026-07-04) — slim segmented control (`All · Live · Nearby ·
  Tonight · Free`) + Map toggle. Re-emphasises the *same* feed; not page navigation.
- **ProvenanceRibbon / ConfidenceState** (2026-07-04) — the source ribbon and the
  human-readable confidence label used by SignalCard. State words only.
- **ZukaTextField** (2026-07-07) — the single text input. Surface `ink1`, border
  `ink2`, text `ink5`, label/placeholder `ink3`. States: default · focused (gold
  `emberGold` border) · error (`error` red border + FieldError below) · disabled
  (dimmed, during submit). Text-first, no heavy adornment (Africa-first).
- **ZukaButton** (2026-07-07) — the primary action button. Fill `emberGold` (gold =
  primary action, per the hard rule), label `ink0`. States: enabled · disabled
  (`ink2` fill / `ink4` label) · loading (inline spinner + label, NON-tappable —
  this is the disable-on-submit surface). NEVER coral — coral is a live *state*,
  never an action. Secondary variant: outline on `ink2`, `ink5` label. **One
  fenced exception:** the "See it live" doorway on LiveConfirmation
  (§10, `live_confirmation.dart`) — a coral *outline* action permitted because it
  enters an already-true live state, on the bloom surface, with no competing gold
  action. That is not a ZukaButton; ZukaButton itself is never coral. It is the
  only *fenced/sanctioned* coral action, and now the **only** coral action in the
  codebase: the former unfenced one (vendor console "Close for today") was resolved
  to `error` red (F012, 2026-07-17; §10 RESOLVED entry). See the Coral-Action Fence
  in §10.
- **ChoiceSelector** (2026-07-07) — single-select control. Segmented variant for
  `medium` (Physical · Digital · Hybrid); wrap-chip variant for `type`. Selected =
  gold outline/fill; unselected = `ink2` outline. One tap per choice.
- **MultiChoiceSelector** (2026-07-19, F018) — bounded multi-select chip group
  (1..`max`), tap to toggle, selection-ordered. A SEPARATE component from
  ChoiceSelector by design: `medium` is a genuine single-select and its contract
  was not bent to absorb this. Bounds live in the TAP HANDLER, not only in
  validate — the sole remaining chip refuses deselection (min-1) and unselected
  chips dim-disable at the ceiling (max-N), while selected chips stay tappable so
  a slot can be freed. Optional `hint` line under the label. Same gold-selected
  grammar as ChoiceSelector; never coral. Full state/token spec in §5B's F018
  amendment.
- **DateTimeField** (2026-07-07) — opens the NATIVE platform date+time picker
  (low-memory, no custom calendar widget), renders the chosen local time as text.
  Owns the end-after-start rule client-side (see §5 validation).
- **VenuePicker** (2026-07-07 · *amended F021, 2026-07-20*) — searchable
  single-select over EXISTING venues. Hidden when `medium = Digital` (venue_id
  null). Backed by a venue-list source. **Amendment (F021):** the "no
  venue-creation UI in scope" limit is lifted — the sheet now hosts two in-sheet
  modes off a pinned footer (**create via pin-drop** and **manage/deactivate own
  venues**), specced in §16. No new route; the list mode is unchanged.
- **FieldError** (2026-07-07) — inline `error`-red helper line under a field.
  Present-tense, tells the person what to fix ("End time must be after the start").
- **FormBanner** (2026-07-07) — top-of-form banner for NON-field failures (RPC error
  / offline). Distinct from FieldError. Offline variant is calm ("You're offline —
  your details are safe here"), not alarming; never blocks the typed values.
- **LiveConfirmation** (2026-07-07) — the one place `liveCoral` appears as a live
  **state**, and the one fenced surface where it may also be an **action** (the
  "See it live" outline doorway; Coral-Action Fence, §10): a full-bleed coral
  confirmation shown only AFTER `go_live` succeeds, because the gathering IS now
  live. Auto-routes to City Pulse. Not a generic green success — a live state
  wears coral (Vol 25 Ch4; see §5 colour logic).
- **PriceText** (2026-07-10) — THE money renderer, defined once. `price_ugx`
  is whole shillings (BIGINT; the Edge Function passes it to Flutterwave
  unconverted) → render `UGX 25,000` (thousands-separated, no decimals — UGX
  has no working subunit). `0` → **Free** (neutral pill, never "UGX 0"). No
  strikethroughs, no "was/now" theater (Vol 16). Server prices only — the
  client NEVER computes or edits an amount.
- **TierRow** (2026-07-10) — one selectable tier on §6: name · PriceText ·
  capacity state (plain words: *"Selling fast"* only if backend-derived,
  else nothing — no fake scarcity) · trailing radio-select. Sold-out variant:
  dimmed, "Sold out" neutral pill, not tappable. Vendor tiers never appear
  (S026 read-surface isolation — the backend guarantees it; the client never
  filters).
- **AccountSheet** (2026-07-10) — §5D's fields as a modal bottom sheet for the
  attendee auth moment (§7): display name · email · password · DPPA consent
  (required) · marketing consent (default OFF) · [Create account] + "Have an
  account? Sign in" swap. Same validation, same `register_person` chain, same
  states as §5D — ONE identity surface, two skins. Copy is attendee-context
  ("to hold your ticket"), not organiser-context.
- **CheckoutHandoff** (2026-07-10 · amended 2026-07-10 per RFDES01-F1 founder
  ruling) — the honest bridge to Flutterwave's hosted checkout, now a
  **transient state, not a decision screen**: the amount (PriceText) and the
  trust line naming Flutterwave render for a brief beat (~1.5s), then the
  hosted link opens EXTERNALLY on its own — no tap. Back during the beat
  cancels to Detail (the PENDING hold releases on its own TTL; nothing is
  charged) — this answers the review's cancel-point concern. The gold
  **[Continue to payment]** button exists ONLY in the launch-failure fallback
  state (browser refused to open). ZUKA never renders card/MoMo input fields
  itself.
- **PendingMeter** (2026-07-10) — the waiting state after checkout handoff:
  calm progress (indeterminate, no fake %), *"Approve the payment on your
  phone"* guidance, elapsed time in plain words, and a quiet [I've paid —
  check now] manual poke. Never a countdown clock (no urgency theater); TTL
  honesty appears only as the deadline approaches (§7 states).
- **QRCard** (2026-07-10) — the admission surface: full-brightness, max-size
  QR of the CURRENT `qr_token`, `ticket_ref` beneath in `SpaceMono` (the
  human-readable fallback a gate can type), gathering name + venue + start.
  Offline: renders from cache with a stale ribbon (*"Saved Xh ago — refresh
  when you're online"*). After check-in the token is NULL → QRCard swaps to
  the CHECKED_IN state, never a dead QR.
- **TicketStatusBanner** (2026-07-10) — one strip on §8 carrying the ticket
  state in words + state colour: PAYMENT_PENDING (gold, "Waiting for
  payment") · ISSUED (success green, "You're going") · CHECKED_IN (success,
  "You're in") · EXPIRED (error red, honest next step). Coral appears on §8
  ONLY as the gathering's own live pill (the gathering is live — the ticket
  state never wears coral).
- **BoothCard** (2026-07-22, §17) — the durable-manager list unit (one per
  `menu_template` lineage). Ember-Edge card (Variant A anatomy): spine is
  **neutral `ink2` by default**, **`liveCoral` only when the booth is attached
  to a gathering that is live right now** (a truthful live state, mirroring §11
  discipline — never decorative). Body: booth name (h3) · **CategoryBadge** ·
  *"used at N gatherings"* caption (only when N is a real count; nothing when 0
  — no fabricated metric). Trailing overflow (⋯) → S4 sheet with Edit ·
  Duplicate · Delete. Whole card taps → editor. Press feedback = ZUKA press
  highlight (opacity/state layer within ~100ms), **no scale bounce** (T0/list
  law). ≥48dp row height.
- **CategoryBadge** (2026-07-22, §17) — small neutral (`ink2` outline / `ink5`
  label) pill naming the booth's `vendor_category`. **Locked variant** carries a
  small lock glyph + is announced read-only (see CategorySelector). Neutral
  always — a category is classification, not a live/scheduled state, so it never
  wears an information-colour.
- **PlanCapMeter** (2026-07-22, §17) — an honest resource counter on the booths
  list header: *"2 of 3 booths"* (Space Mono `data` figures, tabular). This is a
  **capacity count, not a trust/score meter** — no progress-bar-toward-a-secret,
  no gamified fill. At cap it reads full and pairs with the upgrade seam line.
  Renders only when the cap is known (needs the plan/cap read — see §17 deps);
  absent (not faked) when unknown.
- **CategorySelector** (2026-07-22, §17) — the editor's single-select over
  **active** `vendor_category` rows (segmented/wrap-chip, ZUKA gold-selected
  grammar like ChoiceSelector). **Locked state is READ-ONLY, not disabled**
  (ui-ux-pro-max `read-only-distinction`): the chosen category stays fully
  legible (not dimmed-away like a disabled control), shown as a settled value +
  lock glyph + a reason line, and is announced read-only to screen readers.
  Disabled (dimmed) is wrong here — it would imply "temporarily unavailable /
  your fault"; the category is fixed *by rule* (a paid credential bound a fee).
- **ItemEditorRow** (2026-07-22, §17) — one repeatable line in the editor's Body
  band, rendered from the active category's curated schema (§17.6). Common
  shape: name (**ZukaTextField**) · **PriceInput** (UGX) · availability toggle ·
  one schema-specific field (Food→section · Merch→variant · Service→duration) ·
  a **left drag-handle** (explicit affordance, ≥48dp, `drag-threshold` before
  drag so a scroll/tap never mis-triggers a reorder) · trailing remove (×) with
  undo. Reorder is direct-manipulation drag (functional, finger-tracked) — the
  one sanctioned list-position motion; no decorative list entrance.
- **PriceInput** (2026-07-22, §17) — the vendor's own **goods** price entry:
  UGX integer, numeric keyboard (`input-type-keyboard`), no decimals, thousands
  grouping on blur. Distinct from **PriceText** (read-only render) and never used
  for a ZUKA fee — vendor goods pricing is vendor-set and vendor-direct (the
  platform **credential fee** is platform-fixed and never editable here). No
  strikethrough / "was-now" theatre (Vol 16).
- **SyncStatusChip** (2026-07-22, §17) — the editor's autosave/offline state in
  words: *Saved on this phone* (local draft) · *Saving…* (in flight) · *Saved*
  (synced, brief success tick) · *Couldn't save — retry* (failed, with the
  reason + a [Retry]) · *Offline — saved here, will sync* (calm, `info`, never
  alarming). `aria-live="polite"`; never steals focus; never coral. This is the
  §17 offline-first surface.
- **OrderStatusChip** (2026-07-22, §18) — the 7-state vendor-order status pill
  (`_StatusChip`): `micro` label in a full-strength information-colour over a
  14%-alpha fill of the same colour, fully-rounded. Mapping: New→`emberGold` ·
  Preparing→(see §18 CR-1, currently `warning`) · Ready & Collected→`success` ·
  Rejected→`error` · Cancelled & Expired→`ink3` (muted, terminal). **Never
  `liveCoral`** — an order is not a live gathering (coral fence held). Colour is
  a redundant cue only: the word always disambiguates (so the two `ink3`
  terminals reading identical is acceptable — they sit collapsed in Past orders).
- **OrderCard** (2026-07-22, §18) — one queue row (`_OrderCard`): buyer name
  (bodyStrong, "Guest" fallback — never a fabricated handle) + OrderStatusChip ·
  line items (`N× name`, caption) · the money-blind **"Collect at pickup"** +
  PriceText row (neutral `ink`, no status colour — money is colour- and
  motion-silent) · and ONLY the legal next transitions as OrderActionButtons.
  Card = `ink1` on `ink2` border, Ember-Edge family. Read-only in the Past bucket.
- **OrderActionButtons** (2026-07-22, §18) — the per-row action pair
  (`_ActionButton`): forward actions (Accept · Ready) as `emberGold` FilledButton
  (primary — gold's action monopoly), Reject as an `error`-red OutlinedButton
  (recessive + destructive-emphasis, mirroring §17 Delete grammar). Inline spinner
  on the in-flight action; the whole row disables while one is working
  (commit-once). Never coral.
- **ManualCollectCard** (2026-07-22, §18) — the token-bound collect composite
  (`_ManualCollectCard`), DISTINCT from the per-row buttons because
  `collect_vendor_order` binds by the buyer's pickup TOKEN, not the order id
  (anti-fraud). Header + one line of guidance · a code TextField (gold focus
  border) + `emberGold` [Collect] · inline success (`success` "Collected — handed
  over.") / error notes that self-clear on the next keystroke. Pinned top-of-tab.
  Interim until C4's camera scan makes this the manual fallback. **Build note
  (CR-2):** the code field + any rendered token should use `data`/Space Mono
  (codes are Space Mono, Vol 25 Ch5) with `autocorrect:false`/`enableSuggestions:false`.
- **RejectReasonSheet** (2026-07-22, §18) — the reject picker (`_pickRejectReason`):
  a bottom sheet (`ink1`, top-rounded) over EXACTLY the four backend-legal reason
  codes as ListTiles, no free-text path (so no raw text / PII can reach the
  append-only VendorOrderRejected payload — DPPA ⋂ ADR-015). Tapping a reason
  commits the reject (the pick IS the confirm — see §18 CR-5).
- **BoothLogo** (2026-07-23, §19) — the vendor company avatar. `Image.network`
  with a calm **initial-letter fallback** (first char over `ink2`, `ink4` glyph)
  when the URL is null/empty OR fails (`errorBuilder`) — **never a broken image**
  (Africa-first: imagery is optional enhancement, text is the floor). 8px rounded
  clip; `size` 44 in the feed row, 56 in the detail header (same widget, two
  sizes). No shimmer/placeholder loop (2GB-RAM law — a static box, then the image
  or the letter).
- **BoothStatusChip** (2026-07-23, §19) — the booth's live *presence* status,
  **visible-but-labelled** for the whole returned set: LIVE→**"Open"** (`success`
  green) · PAUSED→**"Paused"** (`warning` amber) · SOLD_OUT→**"Sold out"** (`ink4`
  muted) · unknown→"—" (`ink3`, forward-guard). `micro` label at full colour over
  a 16%-alpha fill of the same colour, 50%-alpha border, fully rounded. **Never
  `liveCoral`** — a booth being open is not a *gathering* being live right now
  (coral fence, FDES-02); the word "Open" carries the meaning, colour is a
  redundant cue. Distinct from §11's vendor-console live toggle and §18's
  OrderStatusChip — a third, attendee-facing status vocabulary.
- **BoothFeedCard** (2026-07-23, §19) — the attendee feed's booth row
  (`_BoothCard`): BoothLogo · company name (bodyStrong `ink5`, ≤2 lines) +
  BoothStatusChip (top-aligned right) · a `category · location` meta caption
  (`ink4`, dot-joined, only the parts that exist — no fabricated meta) · trailing
  chevron (`ink3`). `ink1` fill / `ink2` border, 10px radius; whole card taps →
  booth detail. **DISTINCT from §17's BoothCard** (that is the vendor's own
  durable-manager list unit with a status *spine*; this is the attendee's
  read-only discovery row with a status *chip*) — same domain word, two audiences,
  two components; do not merge.
- **BoothMenuRow** (2026-07-23, §19) — one read-only menu line (`_MenuRow`): item
  name (`body`) + **PriceText** (the sanctioned money renderer; `0`→"Free"). An
  unavailable item is **visible-but-labelled** — name struck through (`lineThrough`)
  + a "Sold out" `micro` tag + both name and price dimmed to `ink4` (redundant with
  the strike, never colour-only). `ink1`/`ink2` card, 10px radius. **No add /
  quantity / order control anywhere** — ordering is B2. No strikethrough price
  theatre (Vol 16 — the strike marks *availability*, never a "was/now" price).
- **FeeBalanceCard** (2026-07-24, §23) — the honest platform-fee display. Company
  name (h3) · the outstanding amount via **PriceText** (money is colour- and
  motion-silent — neutral `ink`, never a status colour, never counts up) · the
  **money-posture line** *"This is ZUKA's platform fee for orders your stall
  collected. It goes to ZUKA — it is not taken from your sales."* · and a calm
  lock line with a lock glyph (neutral `ink3`, **never `error` red** — the lock is
  a gate the vendor holds the key to, not a punishment/failure). `ink1` on `ink2`
  border, Ember-Edge family. No coral anywhere (no live state in a fee surface).
- **PayFromPicker** (2026-07-24, §23) — the saved-number single-select for the
  STK-push target, sourced from `get_vendor_payment_profile`. Grouped radio list:
  **Registered** (primary **default-selected**, then secondaries) · **Frequently
  used** (paid-from numbers seen 3+ times) · a persistent trailing **"Pay from a
  different number →"** free-entry row (opens a numeric `ZukaTextField`, MoMo
  keyboard). Every saved row shows the number **masked** (`077 ••• 456`, `data`/
  Space Mono) + a strongest-status label; a number both registered and
  frequently-used **dedupes to one row**. **Day-one empty:** no usage history →
  the "Frequently used" group is *absent entirely* (not rendered empty/broken); the
  picker collapses to Registered + "different number". ≥48dp rows, gold-selected
  grammar (like ChoiceSelector), never coral.
- **SettlementPendingMeter** (2026-07-24, §23) — a sibling of **PendingMeter** for
  the **direct STK push** (NOT the §7 hosted-checkout handoff — there is no browser
  leg here). Calm indeterminate progress (no fake %, no countdown clock), guidance
  *"Enter your MoMo PIN on <masked number> to approve"*, elapsed time in plain
  words, and a quiet **[I've approved it — check now]** manual poke. Survives app
  backgrounding; resolves via the settlement webhook (poll/Realtime), never assumes
  success from the tap. Money-silent, T1 calm — no T2 bloom.
- **HeldNotice** (2026-07-24, §23) — the honest "money received, not yet applied"
  surface for the **HELD** completion (overpaid/underpaid/paid-twice/balance-grew:
  received but not auto-cleared, held for human reconciliation). **`info` blue, not
  `error` red (nothing failed) and not `success` green (debt not cleared).** States
  plainly that the payment arrived, nothing is lost, a person is matching it, the
  listing clears shortly, and — critically — **"you don't need to pay again"**; the
  settle action is **suppressed** in this state (double-pay guard). Shows the PSP
  reference (`data`/Space Mono) to quote to support. Never alarming, never blaming.
- **OutstandingBalanceNotice** (2026-07-24, §23) — the Channel-B at-next-purchase
  informational banner shown when a vendor with an unsettled balance buys a NEW
  credential for another gathering. States the balance + the honest consequence
  (*"Until it is settled, your listing will not activate at &lt;gathering&gt; —
  you will be asked to pay at the gate"*). Offers TWO **explicit, never-preselected**
  choices — pay both together (bundled) OR just the credential now — full price on
  each. Informs, never coerces; **no forced bundle, no pre-ticked consent, no
  fake urgency** (Vol 23 anti-dark-pattern). `info`/neutral, not red.
- **VerificationRequiredNote** (2026-07-24, §23) — the neutral `kyc_required`
  placeholder. Shows ONLY a calm, non-accusatory *"A quick identity check will be
  needed here soon"* affordance — **never the reason, the match, or which company
  triggered it** (trust-state is an RLS non-exposure invariant). Because no KYC flow
  exists yet, it **gates nothing today** — informational only, degrades to a no-op.
  `info`/neutral, never red, never worded as an accusation.

### Visual Foundation Sheet (A-00 · design-prompt-system Phase 0)

**Status: REVIEWED — founder-approved 2026-07-18: card anatomy Variant A
("Ember Edge") + spacing scale RATIFIED (now Vol 25 Ch5 "The Spacing Scale");
image weight budgets remain PROPOSED.** First session run
under the design prompt system (`prompts/`). Mockup:
`mockups/a-00-visual-foundation/foundation-sheet.html` — self-contained HTML,
renders the full token gallery (colour, type ramp, spacing, motion tiers), the
universal state set, and the card-anatomy variants at 360px reference width.

- **Token gallery** restates locked law only (Vol 25 Ch4/Ch5 via
  `lib/core/theme/`) — no new tokens introduced.
- **Spacing: RATIFIED (founder, 2026-07-18).** The 8px grid
  (xs 4 · sm 8 · md 16 · lg 24 · xl 32 · xxl 48) graduated out of PROPOSED —
  amendment recorded in Vol 25 Ch5 ("The Spacing Scale"); `zuka_spacing.dart`
  and the tokens doc updated to cite it. Image weight budgets (hero ≤ 80 KB ·
  thumb ≤ 25 KB · icon ≤ 8 KB) were a separate decision and remain
  **PROPOSED — still open**.
- **Card anatomy (expressive layer): APPROVED = Variant A "Ember Edge"
  (founder pick, 2026-07-18) — binding prior art for all card-class rows
  (A-02 QR card, A-13 list rows, A-17 video previews).** The three candidates
  were:
  - **A · "Ember Edge"** ✔ — 6px status-colour spine on the card's left edge;
    zero-image-capable, colour law at the scan line; degrades gracefully on
    3G; poster becomes optional enhancement (Variant B's thumb layout is the
    with-poster enhancement of A, not a competing anatomy).
  - **B · "Pulse Thumb"** — poster thumbnail leads (≤ 25 KB budget);
    visual-first.
  - **C · "Ink Frame"** — flat outline, no fill; lightest paint.
  - Shared locked semantics in all three: status pill · title (h3) · venue +
    distance (caption) · time (data) · price (bodyStrong, UGX integer).
- **Universal state set:** default · loading (skeleton, T1 shimmer) · empty
  (direction-not-mood, names real neighbourhoods) · offline (designed state:
  stale-as-of time, QR-still-works reassurance, SMS fallback CTA) · error
  (blame-free, "nothing was charged", retry) · success (success green, never
  coral). These six are the floor for every future artifact spec.
- **Motion demos:** T0 silent / T1 feedback (120–280ms incl. route
  transitions) / T2 bloom on LIVE only; page honours `prefers-reduced-motion`
  (all demos freeze).
- Approval executed 2026-07-18: Variant A + spacing ratification recorded,
  A-00 → REVIEWED, Vol 25 Ch5 amendment landed, `zuka_spacing.dart` /
  tokens-doc / Constitution PROPOSED markers cleared for spacing. Open items
  carried: image-budget ratification; Ember Edge spine token (6px) to be
  formalised when the first card-class artifact (A-02 or A-13) is designed.

---

## §0 — Feed System Architecture (the contracts)

*The feed is a small, data-driven renderer over governed contracts. All
sophistication lives server-side (policy · config · an append-only substrate); the
surface stays simple — one feed, tap to explore, no login. Invisible sophistication
made literal. Each contract below costs ~nothing at MVP and prevents a rewrite
later; where a contract could build dormant capability, we reserve it (define the
shape) rather than build it.*

**1 · One polymorphic FeedItem.** The renderer draws a stream of typed items (see
FeedItem in the component library). New content type = new `kind`+`provenance`
data, never a new card codepath. *Adopt now.*

**2 · Bands are declarative config.** Each band = `{ id, title, query,
layout: rail|stack, cap, provenanceFloor }`. Live Now / Starting Soon / Nearby /
Tonight / Digital / Signal are *data* — reorder/add/A-B without a client release
(eventually server-composed). MVP ships a static config; the seam is that the list
is data. *Adopt now.*

**3 · Ranking is a scored stream; the constitution is a post-ranker.** Score =
`combine(signals)`; at MVP `combine` = rules (proximity · freshness · live-status —
what the backend already returns). The constitutional rules — **provenance floor ·
anti-drain cap · exposure fairness** — run as a **constraint pass AFTER scoring**,
so a future smarter `combine` (DRL-3) **cannot silently lose them**. The invariants
are structural, not baked into the scoring math. *Adopt now — this is the key move.*

**4 · Actions resolve from a `(kind × provenance × state) → policy` table** (§3).
Each action declares `loginRequirement` + `drlGate`; the soft-wall and DRL gating
are enforced once, centrally. New action/type = extend the table. *Adopt now.*

**5 · Provenance is a first-class dimension.** Every item carries
`provenance = { source, verified, confidence_state }`. Real = organiser-verified;
Signal = external-unverified; future partner/friend/AI-suggested = the same governed
weighting. The founder's "weighted and measured impact" is this contract's floor +
confidence rules, applied to *any* source ZUKA ever absorbs. *Adopt the dimension
now; reserve the ingestion (see `CONCEPT_Signal_Gatherings.md`).*

**6 · A `capability(DRL)` gate + a reserved interaction substrate.** One resolver
gates personalization, engagement-in-ranking, heat-map, comment-writes, Signal
ingestion — each feature checks one flag, so "activate when ready" is a config flip
and dormant capability cannot leak early (three-axis readiness, enforced). Paired:
an append-only `Feed*` event schema (impression/tap/save/corroborate, ADR-015),
emitted minimally and **anon/device-aggregate at MVP** (DPPA-safe — no behavioural
profile), so DRL-3 personalization/fairness/fraud-discounting grow from it later
with zero re-instrumentation. *Gate: adopt now · substrate: reserve, don't build.*

> **What these buy (zero renderer rework later):** personalization (DRL-3), the
> Signal ingestion engine, heat-map, comments, vendor/in-event listings, boosted
> slots, new external sources, curated bands.
> **What they deliberately do NOT build now:** no learned ranking, no behavioural
> profiles, no ingestion, no Realtime. The surface stays: one feed · tap · no login.

---

## Screens & Flows

### §1 — City Pulse (Home Feed)

**1. PURPOSE**
Answer one question the instant the app opens: *what is alive in my city right
now that I'd enjoy?* This is the zero-friction-discovery proof made visible. One
feed, many lenses — never a maze of pages.

**2. ENTRY POINTS**
App launch (default screen) · "Home" · push ("a venue you follow just went live")
· deep link from a shared card.

**3. FLOW**
App opens → feed renders immediately, NO login prompt → person scrolls the bands
→ taps a card → detail. A slim **LensControl** (`All · Live · Nearby · Tonight ·
Free`) + **Map** toggle re-emphasises the *same* feed. Browsing needs zero account;
the **soft-wall** triggers only on the first *write* action (§3) and captures that
action optimistically — you never lose the tap to a wall.

**4. SCREEN INVENTORY**
Home feed (scroll) · Map toggle (§2, co-primary) · Saved/Following (§4, secondary,
post-signup) · event detail (future pass).

**5. COMPONENTS**
CityPulseStrip · LensControl (+ Map toggle) · band headers · FeedItem (polymorphic,
incl. SignalCard variant) · StatusPill · ActionRow (dynamic). All from the Shared
Component Library — do not redefine. Bands are declarative config (§0 contract 2).

**6. STRUCTURE (bands, priority order)**
`Live Now` (coral rail, horizontal) → `Starting Soon` → `Nearby (ranked)` →
`Tonight` → `Digital` → `Signal band` (floored). Mapped from the backend:
`discovery_feed_nearby` → Live/Nearby/Digital (+ distance, score, section);
`discovery_feed` → the Starting-Soon/Tonight time buckets (client thresholds,
tunable). The **Signal band always sits beneath real Gatherings** (§0 contract 3/5;
`CONCEPT_Signal_Gatherings.md`).

**7. STATES**
- *Default:* populated bands.
- *Loading:* skeleton cards (never a spinner on blank) — < 1.5s on 4G, < 3s on 3G.
- *Empty:* never blank — relax the tightest filter, show nearest activity →
  *"Nothing live in Ntinda right now. Kabalagala is 2km away and has 8 active spots."*
- *Offline:* last-cached **real** feed + quiet "showing your last view —
  reconnecting" ribbon. Signals are omitted offline (too volatile to show stale).
- *Low-data:* text-first cards, imagery suppressed.
- *Liveness:* MVP polls a **30s edge-cached `discovery_feed` snapshot** (Cloudflare
  edge TTL — see `CONCEPT_Distributed_Systems_Architecture_Register.md`); the client
  consumes a versioned snapshot regardless of transport, so Realtime push can be
  swapped in later (DRL-later) with no client change.
- *First-time vs returning:* first-time = location/popularity rules (DRL 1–2);
  personalisation is **DRL-3**, only after 3–5 real check-ins — seam reserved, not
  built (§0 contract 6).

**8. CONTENT / COPY**
Strip: *"47 events live in Kampala."* Band headers: *Live Now · Starting Soon ·
Tonight · Digital.* Signal ribbon: *"Unconfirmed · spotted on [source]."* No "Buy"
on the feed — the strongest action here is "Going"; buying is on Detail (the 3-tap
proof). No numbers for state/trust — words only.

**9. COLOUR LOGIC**
Live → coral pill (may pulse). Starting-soon → gold. Tonight/scheduled → forest.
Free → neutral. Signal → muted/ghosted, no information-colour claim. Background
Midnight. Strip bars gold→coral as energy climbs.

**10. UX RULES**
- Feed MUST render and scroll with no account (proof 2); MUST render *something*
  offline from cache.
- **Anti-drain:** Promoted ≤ 1 per 10 organic, never out-ranking the organic top 3
  by > 2 positions. **A Signal can never out-rank a live real Gathering.** Both are
  post-ranker invariants (§0 contract 3).
- **Exposure fairness:** a single organiser can't hold more than N cards in the
  first screen; round-robin overflow (Vol 15 Ch 2/7).
- Colour-as-information is literal — a coral card means live, nothing else.
- Engagement does NOT feed ranking at MVP (keeps the feed un-gameable; DRL-3 later).

**11. OPEN QUESTIONS**
- Heat-map view: deferred until venue crowd signals exist (map = pins only for now).
- Exposure-fairness N (cards-per-organiser cap): tune against real Kampala density.

**12. CHANGELOG**
- *2026-07-04:* Rebuilt from the seed into the full feed system — one-feed-with-
  lenses (structure A), the six architecture contracts (§0), declarative bands, the
  provenance floor, and the reserved Signal seam. From the feed brainstorm.
- *(seed)* Initial hand-written example establishing the section format.

---

### §2 — Nearby / Map

**PURPOSE** A spatial lens on the *same* feed — "what's around me?" Co-primary with
Pulse via the LensControl Map toggle; not a separate destination.
**FLOW** Tap Map → the current feed's items plot as **pins by venue geo** (PostGIS,
already in `discovery_feed_nearby`) → tap a pin → the same FeedItem card → detail.
**COMPONENTS** Map canvas · geo pins colour-coded by state (coral live / gold soon
/ forest tonight) · the same FeedItem card in a bottom sheet · Pulse toggle back.
**STATES** Loading (map skeleton) · empty (widen radius, mirroring the backend's
5→10km→city-wide auto-widen) · offline (last-cached pins) · low-data (list fallback,
no tiles). Digital gatherings have no pin — they stay a list affordance.
**DEFERRED** Heat-map (crowd-density shading) waits on crowd signals in the backend
(§Open Questions). Pins now; heat later — same screen, additive.
**UX RULES** Same anti-drain + provenance floor as §1 (a Signal pin never
visually dominates a live real pin). Zero-login (proof 2 holds on the map too).

**VISUAL PASS (A-10 · 2026-07-18 · REVIEWED — founder-approved 2026-07-18;
low-data tile-drop rule ratified)**
Mockup: `mockups/a-10-nearby-map/nearby-map.html` — five frames (default lens ·
pin-tapped bottom sheet · empty/auto-widen · offline + low-data list fallback ·
loading), 360px, per A-00. Design-ahead for the F013 build; geo grounded in
S051's `venue_lat/venue_lng` (live in both feed RPCs).
- **Pins:** state colour + dark contrast ring; the live pin wears the T2 pulse
  ring — the map's only expressive motion (coral is CORRECT on a pin: a live
  pin is a live place). Blue dot = OS location, asked in context, never at
  launch. Radius chip mirrors the backend ladder (5 → 10 km → city-wide).
- **Pin tap = the same FeedItem card** as a bottom sheet, Ember Edge spine =
  the pin's state, [See it] → §6 Detail + [Directions]. The map invents no
  new card (lens law).
- **Digital gatherings** never get a fake pin — an honest chip ("3 digital
  gatherings — list") routes to the list affordance.
- **Low-data mode minted:** tiles are the map's only heavy asset and they are
  droppable — low-data renders the same places as an Ember Edge list with
  distances, no tiles. Offline = cached pins + as-of time ("states may have
  changed").
- **3G load order:** pins first (geo is already in the feed response), tiles
  fill behind, never a full-screen spinner.
- On founder approval: A-10 → REVIEWED; visual truth for F013
  (map-lens-flutter-map-pins) + the queued Kampala tile-seed session; heat-map
  stays deferred with §2's crowd-signal dependency.

**CHANGELOG**
- *2026-07-18 (F013 implementation review — SIGN-OFF-WITH-NOTES):* Reviewed the
  built map lens (`map_lens.dart`, `lens_control.dart`, `city_pulse_screen.dart`)
  against §2, §1 and the slice-2 spec. **Confirmed right:** map is a lens not a
  destination (bool `mapEngagedProvider` + in-place Stack overlay, no route; same
  `cityPulseControllerProvider`, no refetch; segment Lens still narrows pins via
  `applyLens`); colour-as-information literal (`mapPinColour` reuses `colourFor` —
  coral reserved for LIVE, gold soon, forest tonight, ink4 neutral scheduled/free,
  ink3 ghosted Signal @0.55, no brand colour on Signal or free/scheduled); Signal
  provenance floor holds spatially (ghosted, no ring; live pin coral + ink5 ring
  dominates; Signals omitted offline); zero-permission default (Kampala camera,
  OS prompt only on chip tap; location dot uses system `info` blue, not brand);
  Mapbox-down-never-breaks (blank Midnight canvas, pins/sheet/detail survive; feed
  error → list fallback); low-data list fallback (tiles off, honest note, toggle
  stays); calm motion (single T1 200ms cross-fade, MapPin static — no T2 bloom,
  LIVE deliberately un-pulsed). **Carry-forward notes (visual-designer / fast-
  follow, not colour-law violations):** (1) forest "tonight" pin (#1A3A2A) is
  low-contrast against Midnight at 34px — non-live pins ship with an `ink3`
  1.5px hairline (the fix landed in F013 itself; stale "invisible midnight
  border" wording corrected by RF013 filing) — still verify on-device that a
  tonight pin reads and is not out-shouted by the lighter ink4 neutral pin. (2) `_MapSkeleton`
  is a calm placard, not a content-shaped skeleton — acceptable, revisit for
  consistency with §1's skeleton language.

---

### §3 — Dynamic Action Matrix (shared across all feed surfaces)

Actions surface by `(kind × provenance × state)` — the ActionRow reads this policy
(§0 contract 4). **Primary** action bold. Login: *none* / *soft-wall* (optimistic
signup on first write) / *required*. No "Buy" on any feed card.

| Content | Primary | Also | Suppressed |
|---|---|---|---|
| Live · real | **Tune in** (coral) | Share, Going, Save, Comment, Not-interested | — |
| Starting-soon · real paid | **Going** | Save, Share, Follow, Comment | Tune-in |
| Tonight/scheduled · real free | **Going** (=RSVP) | Save, Share, Follow, Comment | Tune-in |
| Sold-out · real | **Notify / waitlist** | Share, Save, Comment | Going/Buy |
| Any · Signal | **Corroborate** | **Claim** (organiser), Share, Save, Comment(=discuss), Not-interested | Going, Tune-in |
| Digital · live | **Tune in** (join) | Share, Going, Save, Comment | distance |
| Past · real | Share | Comment (reaction) | Going, Tune-in |

**Login lines:** Share = never gated (growth). Not-interested = none (session-
ephemeral; persists post-signup, informs ranking only at DRL-3). Save/Going/Follow/
Tune-in/Comment-write = soft-wall. Corroborate = none but device-rate-limited
(Vol 15 Ch 9 anti-brigade). Claim = required + ownership proof.
**Comment posture:** read + count on the feed (zero-login); write thread is a
tightly-scoped fast-follow needing a report→hide path (ADR-014) + DPPA care; a
comment is chatter, **not a Memory** (ADR-009).

---

### §4 — Saved · Following (secondary surfaces)

*Personal shelves: what you kept, and who you want to hear from the moment
they go live. The only genuinely separate surfaces — personal, post-signup,
never first-open chrome. FDES-02, 2026-07-10.*

**1. PURPOSE** Saved answers *"what did I mean to come back to?"*; Following
answers *"tell me the moment the places I care about come alive"* (it powers
the "went live" push). Neither is a discovery destination — Pulse stays the
one feed (§1).

**2. ENTRY POINTS** The **⋯ overflow** in Pulse top chrome (§9) opens a small
sheet: *Saved · Following · Account* (+ the Hosting? affordance stays where
§5.2 put it). Anon: the sheet still opens; Saved/Following rows show the
soft-wall empty state (§3's write-moment law — viewing costs nothing, the
wall was already paid at the first Save/Follow tap). Push ("**<venue> just
went live**") deep-links straight to the gathering Detail, not to this list.

**3. FLOW** ⋯ → sheet → Saved (list) or Following (list) → tap → Detail (§6).
Save/Follow themselves happen elsewhere — on ActionRows (§3) and Detail —
these surfaces only *show* the results and allow removal (unsave/unfollow,
one tap, undoable inline for a beat).

**4. COMPONENTS** FeedItem + ActionRow, filtered (§0 contract 1 — no new card
codepath); StatusPill; a plain entity row for Following (name · venue/organiser
label · its live/next state in words). Nothing new is invented here.

**5. STRUCTURE** *Saved:* upcoming first (tonight pinned, live wearing its
coral pill), past folds under a quiet "Past" divider (saved history is memory-
adjacent, not a feed). *Following:* followed venues/organisers as rows; a row
whose venue is live NOW surfaces its live gathering card directly beneath it.

**6. THE PUSH MOMENT (DPPA-deliberate).** The first successful Follow raises a
**separate, explicit push opt-in** — its own sheet, its own consent record:
*"Want to know the moment they go live? We'll only ping you for places you
follow."* [Yes, tell me] / [Not now]. **Never bundled with account creation**
(AccountSheet's consents stay what they are); declining still follows —
Following works in-app without push. FCM wiring is an M0 backend seam — this
section designs the consent moment, not the plumbing.
*Legal-gate amendments (2026-07-10, zuka-legal):*
- **Withdrawal is as easy as grant:** a "go-live pings" toggle lives on the
  Following surface, **separate from unfollow** (keep following, stop the
  pings); unfollowing a target implicitly stops that target's pings. The
  consent store records the *withdrawal* too — an append-only grant/revoke
  ledger (ADR-015-consistent, F003 pattern).
- **Processor disclosure:** the push rides FCM — the token is personal data
  and delivery routes via Google (cross-border). The opt-in sheet's "how
  this works" line and the privacy notice must name FCM as the delivery
  processor (counsel drafts the wording).
- **Basis hygiene:** Saved/Following lists themselves are person-initiated
  core functionality under the account's data-processing consent; ONLY the
  proactive push needs this separate consent. If this channel ever carries
  anything beyond *"a place you follow went live,"* that is marketing — it
  needs the marketing consent, not this one.

**7. STATES**
- *Default:* populated lists. *Loading:* skeleton rows.
- *Empty (direction, not mood):* Saved — *"Nothing saved yet. Tonight's feed
  has plenty of ideas."* [Back to Pulse]. Following — *"Follow a venue or an
  organiser and it shows up here — and can tell you when it goes live."*
- *Anon (soft-wall):* same empties + the sheet's line *"Save and follow with
  an account — it takes a minute."* (the actual signup still happens at a
  write tap, §3).
- *Offline:* cached lists render with the §1 quiet ribbon; unsave/unfollow
  queue is NOT built (write actions disabled offline — honest, small client).
- *2GB/3G:* text-first rows, no imagery, one page (no infinite scroll —
  a person's shelf is short; paginate only if real data ever demands it).

**8. COPY** Present-tense, directional. Removal is quiet: *"Removed."* +
inline [Undo] for a beat. No guilt copy ever (*"You'll miss out!"* is
banned — Vol 16).

**9. COLOUR LOGIC** Chrome neutral ink; information colours only on pills
(a saved live gathering wears coral because it IS live — never decoration).

**10. UX RULES** Never first-open prominence (this section's own law —
preserved by §9's ⋯ placement). A Save is private (no social exhaust). No
ranking, no anti-drain math — these are the person's own lists, rendered
verbatim. Save/Follow stay §3 soft-wall write actions; this pass adds no new
action semantics. *DPPA (2026-07-10, zuka-legal):* Saved and Following are
**new personal-data collections** (bookmarks + a follow graph) — they ride
the F003 data-subject rights path (access/export/erasure; deleting a Person
deletes their saves and follows), and the follow graph is **purpose-limited**:
never repurposed into ranking, trust, or social-graph signals without a fresh
basis and a DRL gate.

**11. OPEN QUESTIONS (backend dependencies — ALL of them; nothing exists)**
- `save_gathering` / `unsave_gathering` RPCs + a saved-list read (API law 10)
  — no table, no RPC today. **Blocks §4 build.**
- `follow_target` / `unfollow_target` RPCs (venue AND organiser targets) + a
  following read returning each followed entity with its live/next state.
  **Blocks §4 build.**
- "Went live" push fan-out (follower query on `go_live`) + FCM token storage
  + the push-consent record (a DPPA consent, stored like the F003 consents —
  as an append-only grant/revoke ledger, and the privacy notice naming FCM/
  Google as the cross-border delivery processor; counsel wording).
- Push copy budget: one push per follow-target per go-live (no re-ping storms)
  — backend rule, stated here so the design's promise is honest.

**12. CHANGELOG** *2026-07-10 (FDES-02):* stub → full spec — ⋯-sheet
placement (per §9 Two Doors ruling), push-consent-at-first-follow moment
(DPPA-separate), full state set, all backend gaps logged (nothing invented).
*2026-07-18 (A-11/A-12):* visual pass added (§4.13); no §4.1–4.11 semantics
changed.

**13. VISUAL PASS (A-11/A-12 · 2026-07-18 · REVIEWED — founder-approved
2026-07-18)** Mockup: `mockups/a-11-shelves-account/shelves-and-account.html`
(pair sheet — AccountSheet is the ⋯ sheet's third door, so the pair shares
one entry). Both surfaces are BUILT but unrouted; this pass = A-00 visual
conformance + the routing asks.
- **A-11 Shelves (5 frames):** the ⋯ sheet (Saved · Following · Account rows
  with one-line subs) · Saved (tonight pinned, live coral pill, "Removed." +
  inline [Undo], PAST folded + dimmed) · Following (entity rows with state in
  words; a live venue surfaces its live card indented beneath; **go-live
  pings toggle rendered ON the row, separate from unfollow** per the legal
  amendment) · the push moment (own sheet, own consent record, FCM named,
  declining still follows) · empties/anon/offline (writes disabled offline
  honestly — no silent queue).
- **A-12 AccountSheet (3 frames):** attendee skin verbatim (§5D fields,
  "— to hold your ticket.", DPPA required + marketing default-off) ·
  sign-in swap with §5 FormBanner error grammar + the armed-action law
  restated (sheet success fires the kept tap) · **the signed-in "Account"
  door: a plain identity row + [Sign out] + a DPPA-rights row — NAMED SEAM:
  routes to a stub stating the rights + support channel (counsel wording);
  self-serve flows are a later build. No avatars, no bios — identity stays
  quiet at M0.**
- Routing asks (build): register the shelf routes (`/shelf/saved`,
  `/shelf/following` — ShelfScreen exists, unrouted); wire the ⋯ Account row
  to the built AccountSheet; add the DPPA-rights stub screen.
  **DELIVERED (F020, 2026-07-19):** all three shipped — `/shelf/saved` +
  `/shelf/following` are top-level chrome-less routes (Back → Pulse); the ⋯
  Account row now opens `showAccountSheet` in BOTH auth states (a single door,
  no inline sign-in/out); `AccountSheet` gained the signed-in identity skin
  (name · email · joined · [Sign out] · DPPA-rights row) per frame 8; and the
  `/account/rights` DPPA-rights stub (interim, counsel-pending copy) is routed.
  This REVERSED the RF005 "keep Saved/Following off the route map" stance, as
  the founder-approved A-11/A-12 ruling directed.
- On founder approval: A-11/A-12 → REVIEWED; the routing asks + FCM seam +
  DPPA stub route to the build/backend tracks. (Routing asks DONE — F020; FCM
  push-consent seam remains a backend-track candidate.)

---

### §5 — Go Live (Organiser: Sign-in → Create → Publish → Live)

*Sacred Proof #1 made real. An organiser gets an authenticated session, describes
a gathering, and taps it live — and it surfaces in City Pulse (§1) within 60s.
Scope is hard-gated (ADR-027): minimal sign-IN only, one create form mapped
1:1 to `create_gathering`, then `publish_gathering` → `go_live`. No sign-up, no
pricing/tickets, no scanner, no profile/edit/delete, no second discovery screen.
Go Live is FREE to create.*

**1. PURPOSE**
Let a real Kampala organiser put their night in front of the city in under a
minute — describe it, tap live, done. The mirror of the zero-login browse proof:
the shortest honest path from "I'm hosting" to "it's on the feed."

**2. ENTRY POINTS**
A single quiet organiser affordance in the City Pulse chrome — *"Hosting
something?"* (not a competing nav tab; discovery stays the front door). Tapping it
requires an authenticated organiser session → routes to Sign-in (§5A) if not
signed in, else straight to Create (§5B). Deep link `zuka://host` lands the same
way. **(F003 update)** Sign-in now offers a secondary route to **Sign up (§5D)** —
self-serve organiser provisioning is live; the F002 "NO self-serve sign-up" gate is
lifted. First-time hosts create an account, are provisioned a Person, and land in
Create — no dev seed.

**3. FLOW (tap budget marked)**
```
[not signed in]  Hosting? → Sign-in screen → enter email+password → [Sign in] ①
                            → authenticated session
[signed in]      Create screen: title · venue · start · end · type · medium
                            → [Create gathering] ②     (calls create_gathering → DRAFT)
                 Ready screen (this one gathering, DRAFT):
                            → [Go Live now] ③           (publish_gathering → go_live)
                            → LiveConfirmation (coral)  → auto-route to City Pulse
                            → new gathering shows LIVE in Live Now band ≤ 60s
```
Core Go-Live action = **2 taps** from a filled form (Create → Go Live now), plus
one-time Sign-in. Well inside the 3–5 tap budget.

Publish and Go Live are TWO real RPCs but ONE primary intent for the organiser:
**"Go Live now"** chains `publish_gathering(id)` then `go_live(id)`. A secondary
**"Publish only — go live later"** performs just the publish (lists it,
discoverable, not yet live) for organisers scheduling ahead. Rationale below.

**4. SCREEN INVENTORY**
- **§5A Sign-in** — email + password, one primary button, plus two secondary
  escapes: sign-UP (§5D) and password reset (§5A-R). The original A-minimal
  "sign-in only, no reset/provisioning UI" gate was lifted by F003 (§5D) and
  F036 (§5A-R).
- **§5A-R Reset password (F036)** — three steps behind one screen: email → code
  → new password. Emailed CODE, never a deep link. (Detailed spec below.)
- **§5B Create Gathering** — one scrollable form, six fields, one primary button.
- **§5C Ready (single-gathering)** — the just-created DRAFT with the Go-Live
  actions and a plain summary. Not a "my gatherings" list (out of scope).
- **§5D Sign up (F003)** — display name + email + password + DPPA consent, one
  primary button. Self-serve provisioning; siblings §5A. (Detailed spec below.)
- **LiveConfirmation** overlay → City Pulse (§1) with a forced fresh feed fetch.

**5. COMPONENTS**
ZukaTextField · ZukaButton (primary/secondary) · ChoiceSelector · DateTimeField ·
VenuePicker · FieldError · FormBanner · LiveConfirmation — all from the Shared
Component Library (do not redefine). On loop-close, City Pulse reuses its own
FeedItem / StatusPill / CityPulseStrip unchanged.

**6. STRUCTURE (per screen)**
- **§5A Sign-in:** logo mark → one line of context (*"Sign in to host on ZUKA"*)
  → email field → password field (show/hide toggle) → **[Sign in]** primary →
  FormBanner slot above the fields for auth/offline errors → a right-aligned
  *"Forgot password?"* link directly under the password field (§5A-R, F036) →
  *"New here? Create an account"* under the primary (§5D, F003). The banner slot
  is also where §5A-R's success confirmation lands on return (`FormBanner.success`
  — the same slot, never the error tone).
- **§5B Create:** single vertical stack, top→bottom in `create_gathering` param
  order so the mental model matches the write: **Title** (ZukaTextField) → **Medium**
  (ChoiceSelector: Physical · Digital · Hybrid) → **Venue** (VenuePicker; hidden
  when Medium = Digital) → **Starts** (DateTimeField) → **Ends** (DateTimeField) →
  **Type** (**MultiChoiceSelector** chips, 1–6 of: Live music · Nightlife · Comedy ·
  Theatre · Arts · Networking · Sports screening · Other — *amended F018/S060, was a
  single-select ChoiceSelector; hint line "Pick 1–6." sits under the label*). Sticky
  **[Create gathering]** primary at the bottom. FormBanner slot at top.
- **§5C Ready:** plain text summary of what was entered + a DRAFT StatusPill
  (neutral — it is not live yet, so NO coral here) → primary **[Go Live now]** →
  secondary **[Publish only — go live later]** → a tertiary text link back to edit
  is OUT (corrections append, ADR-015; no destructive edit UI this pass).

**7. FIELD VALIDATION (client-side, before any RPC)**
| Field | Rule | FieldError copy |
|---|---|---|
| Title | required, trimmed non-empty, ≤ 120 chars | "Give it a name." |
| Medium | required; one of physical/digital/hybrid; default Physical | (default set, rarely errors) |
| Venue | required when Medium ∈ {Physical, Hybrid}; must pick from list; null when Digital | "Where is it? Pick a venue." |
| Starts | required; valid datetime | "When does it start?" |
| Ends | required (table NOT NULL); MUST be after Starts | "End time must be after the start." |
| Type | required; pick **1–6** chips (maps to `gathering_types text[]`, selection-ordered) — bounds enforced in the tap handler, so validate() is a backstop (F018/S060) | empty → "What kind of gathering?" · over-full → "Pick up to 6 types." |
Client validates end > start BEFORE calling — the backend's `23514` duration guard
is a backstop, not the first line of defence. Invalid fields block submit and mark
each offending field; the primary button stays enabled (so the tap surfaces the
errors) but the RPC does not fire until clean.

**8. STATES (every one reachable — §1 rigour)**
- **form** — clean default; typed values persisted in local screen state so a
  failed submit or a backgrounded app never loses them (3G reality).
- **submitting** — the tapped ZukaButton enters loading (spinner + label, NON-
  tappable); all fields disabled. Disable-on-submit is now UX, not the only guard:
  since **S048** `create_gathering` carries an idempotency `client_key`, so a
  double-tap on 3G converges on the same DRAFT server-side rather than creating
  two. One in-flight write at a time is still the rule — it just no longer carries
  the correctness weight alone.
- **success = LIVE** — only after `go_live` returns: **LiveConfirmation** in
  `liveCoral` (*"You're live."*), because the gathering IS live. Never the green
  `success` token — a live state wears coral (hard rule). Auto-routes to City
  Pulse after ~1.5s or on tap **[See it live]**.
- **validation-error** — field-level (table above); FieldError under each field,
  no banner. Never a single vague "check your inputs".
- **RPC-error** — FormBanner with mapped copy from the client wrapper
  (`ZukaRpcClient.mapPostgrestError`). PRECISION FOR THE BUILDER: these three RPCs
  raise with STANDARD SQLSTATEs, not `P0001`, so their literal PL/pgSQL messages
  are deliberately NOT surfaced (which is correct — it keeps internal text like
  "impersonation blocked" off-screen):
  - `23514` check_violation (ends ≤ starts · gathering not in a valid state to
    publish/go-live, e.g. already published) → *"That's not something you can do
    right now."*
  - `42501` insufficient_privilege (ownership / missing Person record) → generic
    *"Something went wrong — try again."* + Sentry (treated as an app bug).
  - `no_data_found` (gathering vanished) → unmapped → generic + breadcrumb.
  - A future RPC that instead raises `P0001` WOULD surface its own human message
    verbatim — the wrapper already routes P0001 that way; none of §5's three do.
- **offline** — the RPC wrapper's 10s timeout (`kDefaultRpcTimeout`) fires, throws
  `isNetwork = true`; FormBanner shows the calm offline line, form values stay put,
  primary returns to enabled for retry. NEVER a spinner on a blank screen. Sign-in
  offline: Supabase Auth needs the network, so we show the offline banner and block
  submit — no fake "signed in".
- **submit-ambiguity → bounded-then-banner, then RETRY-ONLY (S048 · RS048-F1).**
  A timeout after the write may still have committed — but the write now carries a
  per-form-fill `client_key`, so a replay converges on the same gathering instead
  of double-creating. The posture that follows from that:
  1. **Auto-retry, bounded** — the repository replays a network-class failure up to
     2 extra times, spaced ~600ms–1.2s (jittered). The delay is load-bearing: a
     fast-fail (no route to host / DNS / refused — a dropped 3G radio) returns in
     milliseconds, so unspaced attempts would all burn into the same dead window.
  2. **Then the banner** — *"We couldn't confirm that saved. Tap Try again — your
     details are locked so it can't be created twice."* Primary becomes **[Try
     again]**.
  3. **RETRY-ONLY: the form freezes at the values that were sent.** This is the
     subtle part and it is a correctness control, not polish. The server converges
     on `(organiser_id, client_key)` **alone and ignores the replay's payload**. So
     an editable form here would let an organiser change Starts to 21:00, re-tap,
     converge onto the **20:00** gathering, and see §5C Ready render **21:00** —
     the DB says 20:00, attendees see 20:00, and with no edit RPC and no "your
     gatherings" view (§5F/A2 unbuilt) the divergence is permanent AND invisible.
     So: every field disabled, no edit, [Try again] the only affordance. The
     controller enforces it structurally too — it replays its own frozen snapshot
     and ignores the draft — so the lock cannot be defeated by a UI path.
  4. **A HARD rejection is the opposite case** — `23514` definitively did not land,
     nothing exists to diverge from, so the form stays freely **editable**. Never
     freeze on a hard error.
- **partial-chain (publish ok, go_live failed)** — the gathering is now PUBLISHED
  & discoverable but not LIVE. §5C detects this on return and offers **[Go Live]**
  alone (it must NOT call publish again — `23514` if it did). So the retry is safe
  and idempotent-by-state.

**9. CONTENT / COPY**
Voice: direct, present-tense, warm, no hype. Sign-in: *"Sign in to host on ZUKA."*
Create header: *"What are you putting on?"* Primary buttons: **Sign in** ·
**Create gathering** · **Go Live now** · secondary **Publish only — go live
later**. Auth error: *"That email and password don't match."* Offline (create):
*"You're offline — your details are safe here. Try again when you reconnect."*
Success: *"You're live. Kampala can see it now."* Terminology: this is the
semantic creation surface, so copy uses **gathering**, not the consumer-facing
"Events" label — a deliberate, in-Bible choice (ADR-011), not a deviation.

**10. COLOUR LOGIC**
- Primary actions (Sign in / Create / Go Live now) = **`emberGold`** — gold is the
  primary-action colour; these are actions, never live states.
- DRAFT / summary on §5C = **neutral** (`ink` ramp) — not live, so no coral.
- Field focus = gold border; field error = `error` red (system, not coral).
- **`liveCoral` as a *state* appears exactly once in this flow**: the
  LiveConfirmation after `go_live` succeeds — the single moment the gathering IS
  live. This is the whole point of reserving coral: when it blooms, it MEANS
  something.
- Background Midnight throughout. Rejected: a coral **[Go Live now]** button — the
  button means "make it live", it is not itself live; coral before the RPC returns
  would cheapen the rule. Gold action → coral outcome is the correct grammar.
- **RATIFIED EXCEPTION (2026-07-17, F011) — the one sanctioned coral *action*
  (Coral-Action Fence).** The "See it live" control on LiveConfirmation is a coral
  *outline* button. It is the only *fenced/sanctioned* coral action on the organiser
  Go-Live surface, and it does not weaken the rule — it honours it: the gathering is
  already live when this renders, so coral tells the truth here. Permitted only
  because ALL five clauses hold; any coral action failing even one is forbidden
  (use gold fill, or ink-outline secondary):
    1. State is already true — a succeeded write, not pending or intended.
    2. Outline only, never fill — gold keeps its monopoly on action-*fill*.
    3. On the LiveConfirmation bloom surface only — the already-fenced expressive
       surface (coral bloom + licensed overshoot motion, tokens §5).
    4. No competing gold action on the surface.
    5. The action is a *doorway into* the live thing — it navigates, it does not
       create, buy, or trigger.
  A coral "Buy ticket" fails 1, 2 and 5; a coral [Go Live now] fails 1. The fence,
  not a blanket ban, is what stops leakage. Do not re-flag this button.
- **RESOLVED (2026-07-17, F012) — was OPEN VIOLATION (logged 2026-07-17, RF011).**
  A codebase sweep (`grep -rn liveCoral lib/`) had found a **second** tappable coral
  action outside this surface: the "Close for today" confirm button in
  `vendor_console_screen.dart` (then at :231-237). It was **not** a sanctioned
  exception — it failed the Fence on clause 1 (tapping *causes* the CLOSED state, it
  does not confirm an already-true one), clause 3 (it renders in a vendor-console
  `AlertDialog`, not the LiveConfirmation bloom surface), and clause 5 (it is a
  destructive, terminal transaction — CLOSED has no reopen RPC, S023 — not a
  doorway). Coral on a destructive confirm also broke the Bible's own hard rule
  (Vol 25 Ch4: coral = live, "never used for anything that is not currently live").
  **Ruling (founder-pinned option a — restyle off coral; the Vol 25 Ch4 "destructive
  confirmation" amendment (option b) was rejected at the spec gate as a Bible change,
  not a headless one): the confirm button now uses `ZukaColors.error`** — the
  existing danger token, which Vol 25 Ch4 explicitly routes failure/danger to ("Coral
  means live, not error"). `error` over `warning` because the close is terminal and
  irreversible (danger-weight, not the softer reversible "caution" register); over
  `ink4`/`ink5` because the neutral cancel sibling already sits at `ink4`, and a
  terminal action must chromatically out-weigh its safe sibling. Foreground text-colour
  swap only — no fill (gold keeps its action-fill monopoly), no new component; the
  "Keep it open" cancel stays at neutral `ink4` (no contrast tweak needed). With this,
  coral appears as an *action* in exactly one fenced place codebase-wide — the F011
  "See it live" outline on LiveConfirmation.

**11. UX RULES**
- Sign-in is minimal by mandate: NO sign-up, reset, or provisioning UI in this pass.
- Exactly one in-flight write at a time (disable-on-submit) — a first line of
  defence against double-create (S048's `client_key` is the real one) and the only
  guard against double-publish (`publish_gathering` is idempotent-by-state, not
  keyed).
- Typed form values survive an error/offline/background — never cleared on failure.
  After an ambiguous failure they are also **frozen** (retry-only, STATES above):
  surviving and being editable are different promises, and only the first is safe.
- No destructive edit/delete UI — corrections append (ADR-015); §5C offers no
  "delete draft" this pass.
- The 60s proof: on return to City Pulse, force a fresh (cache-bypassing)
  `discovery_feed` fetch so the organiser sees THEIR gathering live immediately,
  rather than waiting out the 30s edge-cached poll (§1). Other people may see it
  within ~30–60s as the edge TTL rolls — inside the proof window.
- Africa-first: text-first form, native OS date/time pickers (low memory), no
  images required to create, 10s timeout → honest offline state.

**12. OPEN QUESTIONS**
- **Person-id resolution:** `create_gathering` needs `p_organiser_id = person.id`
  (it re-checks `auth.uid()` → person). The client must know its own `person.id`
  post-sign-in. Is it returned at sign-in, or does it need a lookup RPC? Dependency.
- **Missing Person record:** the RPC raises `42501` if the authed account has no
  `person` row — with no provisioning UI in scope, how is an organiser's Person
  seeded? Founder/backend call. Until resolved this path shows the generic error.
- **Venue source:** VenuePicker needs a list of existing venues (search) — there is
  no venue-creation UI in scope. Which RPC/query backs it, and what if the
  organiser's venue isn't listed? Dependency.
- **~~Idempotency~~ — CLOSED by S048.** `create_gathering` (and `create_tier`) now
  take a `p_client_key` and converge a replay onto the existing row, emitting no
  second domain event. The submit-ambiguity mitigation is replaced by the
  bounded-then-banner + retry-only posture in STATES above. One residual, deferred
  by design: convergence is on the key alone, so the server cannot tell a replay
  from an edited resubmit — the client closes that by freezing the payload
  (RS048-F1). The rigorous server-side form (a payload fingerprint that rejects
  key-reuse-with-different-payload) is gated on an edit path existing: with no edit
  RPC it would dead-end the organiser at a correct error they cannot act on.
  Revisit when a "your gatherings" view + edit/correction flow lands.

**§5D — SIGN UP (self-serve organiser provisioning · F003)**

*The write side of organiser identity. F002 proved a seeded organiser can go live;
F003 lets a real first-time host create their own account and be provisioned a
Person — retiring the dev seed. Scope stays hard-gated (ADR-027): one sign-up
screen mapped to Supabase Auth + `register_person`, nothing more. No profile edit,
no avatar, no OAuth. (F003's "no password reset" scope note is RETIRED — reset
shipped as its own surface in F036; see §5A-R.)*

- **PURPOSE.** Turn "I want to host" into a real, self-provisioned organiser
  identity in one short screen — then drop them straight into Create (§5B).

- **ENTRY / EXIT.** A secondary text link on **§5A Sign-in** — *"New here? Create an
  account"* → §5D. §5D carries the mirror link back — *"Already have an account?
  Sign in"* → §5A. On success, the session flips (same host gate as sign-in) and
  routes to Create (§5B). No separate confirmation screen in the local/no-confirm
  posture; **coral is still reserved for `go_live`**, never for account creation.

- **FIELDS (top→bottom, `register_person` param order).**
  | Field | Component | Rule | Maps to |
  |---|---|---|---|
  | Display name | ZukaTextField | required, trimmed non-empty, ≤ 80 | `p_display_name` |
  | Email | ZukaTextField (email kbd) | required, valid email | Supabase Auth |
  | Password | ZukaTextField (show/hide) | required, ≥ policy min (8) | Supabase Auth |
  | Data-processing consent | Checkbox | **REQUIRED** — submit blocked until ticked | `p_consent_data_processing` |
  | Marketing consent | Checkbox | optional, **default OFF** (never pre-ticked) | `p_consent_marketing` |

- **FLOW (2 steps behind one intent).** Fill → **[Create account]** →
  `supabase.auth.signUp(email, password, data:{display_name})` → then
  `register_person(display_name, consentData, consentMarketing)` → resolve
  `person.id` → session flips → **Create (§5B)**. The two backend calls are ONE
  organiser intent; disable-on-submit guards the whole chain (a double-tap must not
  double-provision — `register_person` is idempotent, but the UI still serialises).

- **EMAIL-CONFIRMATION POSTURE (build note).** Local dev has
  `enable_confirmations=false`, so `signUp` returns a **session immediately** →
  `register_person` fires right away with the in-hand display name → straight to
  Create. Under a hosted posture with confirmations ON, `signUp` returns **no
  session** → §5D shows a **check-your-email** state and provisioning is deferred to
  the **first confirmed sign-in**, where the idempotent `register_person` is called
  with the `display_name` stashed in the auth user's metadata at sign-up. Same RPC,
  same idempotency — only *when* it fires differs. F003 builds the local immediate
  path + the metadata stash; the confirmation interstitial is a hardening-posture
  follow-on.

- **STATES (every one reachable — §1 rigour).**
  - **form** — clean; typed values persist in local screen state (3G reality).
  - **submitting** — button loading + fields disabled across the whole
    signUp→register_person chain (one in-flight write; no double-provision).
  - **success** — session flips → auto-route to Create (§5B). No coral.
  - **validation-error** — field-level FieldError (table above); consent-required
    is a field error on the checkbox, not a banner.
  - **email-already-registered** — Supabase Auth returns the duplicate signal →
    FormBanner: *"That email's already registered — sign in instead."* + a nudge
    back to §5A. (Never leak whether an email exists beyond this deliberate,
    standard sign-up affordance.)
  - **RPC-error** — `register_person` raises STANDARD SQLSTATEs, surfaced via
    `ZukaRpcClient.mapPostgrestError` (never the literal PL/pgSQL text): `23514`
    check_violation (consent refused / blank name — client validates first, so this
    is a backstop) → *"That's not something you can do right now."*; `42501` (no
    auth) → generic + Sentry (should not happen post-signUp).
  - **offline** — Supabase Auth needs the network; 10s timeout → calm offline
    banner, values kept, no fake "account created".
  - **provision-ambiguity (signUp ok, register_person dropped)** — the account
    exists but the Person may not. Because `register_person` is **idempotent**, the
    safe recovery is to retry it (not re-signUp): the banner offers a retry that
    calls `register_person` alone. On the next authenticated session the idempotent
    ensure also closes this. NEVER a duplicate account.

- **COLOUR LOGIC.** Primary **[Create account]** = `emberGold` (an action). Consent
  checkboxes + links = neutral `ink` ramp. `error` red for field errors. **No
  `liveCoral`** anywhere on §5D — coral belongs to `go_live` alone.

- **CONTENT / COPY.** Header *"Host on ZUKA."* Sub *"Create your organiser
  account."* Display-name hint *"Your name, or your venue's."* Consent line, plain
  and honest (DPPA §12): *"I agree ZUKA can process my account data to run my
  events."* Marketing (unticked): *"Send me occasional ZUKA updates."* Primary
  **Create account**. Terminology: **gathering**/**host**, never "user".

- **UX RULES (F003).**
  - **Data-processing consent is a hard gate** — the DPPA §12 requirement is made
    visible; submit is blocked until ticked and `register_person` refuses without
    it. This is the consent-before-processing invariant expressed in the UI.
  - **Marketing consent is OFF by default and never pre-ticked** (anti-dark-pattern,
    SOUL red line 8; DPPA freely-given consent).
  - One in-flight write across the whole signUp→provision chain.
  - Typed values survive error/offline/background — never cleared on failure.
  - No PII beyond name + email + consent; no phone (separately-gated verification,
    Vol 08 Ch10); text-first, low-memory, 10s timeout → honest offline.

**§5A-R — RESET PASSWORD (organiser · emailed code · F036)**

*The recovery path for a host who cannot get in. F003 gave people a way to create
an account; without this, forgetting the password is a permanent lockout — not an
edge case. Client-only: no migration, no new RPC, no new dependency. It consumes
Supabase Auth (`resetPasswordForEmail` → `verifyOTP(recovery)` → `updateUser`)
the same way §5A/§5D consume it.*

- **PURPOSE.** Turn "I'm locked out" back into a working sign-in, in three short
  steps, without ever telling a stranger whether an email is registered.

- **A CODE, NOT A DEEP LINK — the load-bearing decision.** Kampala reality: the
  reset email is routinely opened on a *different device* than the one signing in
  (a cybercafé desktop, a borrowed laptop, a shared phone). A deep link only works
  where it is tapped; a 6-digit code crosses devices by being read out or retyped.
  This is the same Africa-first reasoning that makes offline and SMS first-class,
  applied to account recovery. A deep-link reset was considered and rejected.

- **ENTRY / EXIT.** A right-aligned *"Forgot password?"* link under §5A's password
  field → §5A-R (pushed). On success it pops back to **§5A** with a
  `FormBanner.success`: *"Password changed. Sign in with your new one."* It does
  NOT route into the app: the flow signs its own recovery session out (below), so
  the host re-enters with the password they just chose — which is also how they
  find out it took.

- **STRUCTURE — three steps, ONE screen.** `email → code → new password` as
  in-screen steps, not three routes. The steps share state that must not be lost
  between them (the email the code was issued for, the resend cooldown, and after
  step 2 a live recovery session); a route stack makes it far too easy to strand
  someone half-way with a verified code and no way back to it.
  | Step | Field | Primary | Secondary |
  |---|---|---|---|
  | 1 email | ZukaTextField (email kbd) | **[Send code]** | — |
  | 2 code | ZukaTextField (numeric, 6, digits-only) | **[Check code]** | *Send another code* (cooldown-gated) |
  | 3 password | ZukaTextField (show/hide) | **[Set new password]** | — |
  Back from step 2 returns to step 1 (retyping a mistyped address is the commonest
  real recovery). Back from step 3 leaves the flow — the code is already spent, so
  offering step 2 again would be a dead action.

- **NON-ENUMERATION (the invariant of this surface).** Step 1 shows **one string
  for both outcomes** — *"If that email has a ZUKA account, we've sent it a 6-digit
  code. It expires in about an hour."* — and advances to step 2 either way.
  `resetPasswordForEmail` answers identically for a known and an unknown address
  and the client preserves that: no provider text is ever echoed, and the resend
  cooldown starts after **every** send attempt, registered or not, so the cooldown
  itself cannot be read as a signal. Step 2 collapses *wrong code*, *expired code*,
  *already-used code* and *code issued for another email* into a single message —
  distinctions an attacker would pay for and a legitimate user cannot act on
  differently.

- **RESEND COOLDOWN.** 60s (matching GoTrue's own per-email send interval), shown
  as a live countdown in the button label — *"Send another code in 43s"* — and the
  button is disabled for its duration, so a dead affordance is never unexplained.
  When the server's own rate limit answers anyway, its `Retry-After` seconds are
  adopted verbatim and surfaced as *"Too many requests — wait Xs and try again."*,
  never the raw *"For security purposes…"* sentence.

- **THE RECOVERY SESSION (shared-device posture).** `verifyOTP` mints a real
  authenticated session. Both exits dispose of it: on success `setNewPassword`
  signs out after the password is set, and on the **abandon** path (a verified code
  with no new password) the screen's `dispose` signs out too. Leaving a logged-in
  organiser on a borrowed device is precisely the outcome the code-not-deep-link
  posture exists to avoid. A failing sign-out is swallowed — the password IS
  changed by then, and reporting failure would push the host to re-run a reset they
  do not need.

- **STATES (every one reachable).** form (per step) · submitting (button loading,
  field disabled) · sent-confirmation (the generic banner) · validation-error
  (field-level: unparseable email, empty code, password < 8) · bad-or-expired-code
  (one message, as a field error next to the thing the user can fix) ·
  cooldown (countdown label, disabled resend) · rate-limited (friendly wait) ·
  expired-recovery-session (*"That code expired — start again."*) ·
  same-password · weak-password · offline (per step, calm info tint, typed values
  kept) · success (pop → §5A confirmation banner).

- **COLOUR LOGIC.** Primary buttons `emberGold`. `error` red for field errors,
  `info` for offline, `success` green for the §5A confirmation banner only. **No
  `liveCoral` anywhere on §5A-R** — coral belongs to `go_live` alone.

- **UX RULES (F036).**
  - Never reveal whether an email is registered — see NON-ENUMERATION above. Do
    not "improve" the step-1 copy into a confirmation that an account exists.
  - One in-flight call at a time; no network fires on a client-invalid field.
  - Offline reuses `AuthRepository`'s existing `SocketException`/`TimeoutException`
    mapping — the same calm copy §5A/§5D already use, per step.
  - No new backend throttle table. Supabase's built-in email rate limit is the
    server-side control; the client cooldown is a UX + symmetry measure on top of
    it, not a security boundary in its own right.

- **OPEN QUESTIONS.**
  - **Residual GoTrue-level oracle.** GoTrue does not mail an unknown address, so
    its *per-email* limit may only trip for real accounts. The unconditional client
    cooldown keeps that off the screen in normal use, but it is a client-side
    mitigation of a server-side property — not a fix. No client change can close
    it; closing it properly is a GoTrue/back-office concern.
  - **Code length + expiry are GoTrue defaults** (6 digits, ~1 hour), stated in the
    copy rather than configured by ZUKA. If the hosted project changes either, the
    step-1 copy must change with it.

**13. CHANGELOG**
- *2026-07-07:* Section created for Flutter F002 — the 60-second Go Live organiser
  flow (Sacred Proof #1). Drafted §5A sign-in, §5B create form (mapped 1:1 to the
  post-S011 7-arg `create_gathering`), §5C ready + `publish_gathering`→`go_live`
  chaining, and the LiveConfirmation loop-close to City Pulse. Added eight shared
  form/confirmation components. Scope hard-gated per the F002 brief.
- *2026-07-08 (F003):* Added **§5D Sign up** — self-serve organiser provisioning
  mapped to Supabase Auth + the new `register_person` RPC (display name + email +
  password + DPPA data-processing consent (required) + marketing consent (optional,
  off by default)). Lifted the F002 "no self-serve sign-up" gate in §2, added §5D to
  the screen inventory (§4), and documented the email-confirmation posture fork
  (local immediate provision vs hosted confirm-then-provision, both via the same
  idempotent RPC). Resolves the §5 Person-id-resolution + Person-provisioning open
  questions below. No coral on §5D — coral stays reserved for `go_live`.
- *2026-07-24 (F036):* Added **§5A-R Reset password** — the emailed-CODE recovery
  path off §5A. Retires F003's "no password reset" scope note in §5D and the §5A
  "no forgot password in this pass" structure note. Records the code-not-deep-link
  ruling (cross-device Kampala reality), the non-enumeration invariant (one string
  for both outcomes; one message for wrong-and-expired), the 60s resend cooldown
  started after every send, and the shared-device recovery-session disposal on both
  the success and the abandon path. Client-only — no migration, no new RPC, no new
  dependency. No coral on §5A-R.
- *2026-07-13 (F006):* Added **§5E Ticket tiers (organiser editor)** — the
  create-side surface that fills the attendee-side TierRow/PriceText already
  specced in §6. A "Tickets" pane inside the Go-Live flow at `GoLivePhase.ready`
  (after `create_gathering`, before publish/go-live). Encodes founder ruling (A)
  (honest Skip = one `General entry` free tier), the PriceText Free law, the R1
  live-but-unticketable truth state, and the seven-magic-tier-name hazard. No
  coral on §5E — the tier step is pre-live; coral still belongs to `go_live` alone.

**§5E — TICKET TIERS (organiser editor · F006)**

*The organiser side of Vol 14 Ch2 (≤ 8 tiers per Gathering: name · price ·
capacity). §6 designed how a person BUYS/CLAIMS a tier (TierRow, PriceText, the
Free pill); this designs how an organiser CREATES one. Scope stays hard-gated:
create-only — there is no edit or delete RPC this session, so a committed tier is
permanent. Go-live is NEVER blocked on the tier RPC (the 60s proof outranks it).*

> **AMENDED (F017, 2026-07-19) — the "create-only … a committed tier is
> permanent" language above is now STALE.** The S059 `amend_tier` RPC exists: an
> organiser CAN correct a LIVE gathering's tier name/price/capacity post-go-live,
> from **"Your gatherings" → [Edit tickets]**. That amendment surface is specced
> in **§5E-A** below. Inside the create/go-live flow a committed card still stays
> view-only (you just typed it — get it right, or Remove an uncommitted row);
> post-go-live correction lives on the dedicated §5E-A surface. Delete/deactivate
> a tier remains out of scope.

- **PURPOSE.** Between "it exists" and "it's live", let the organiser say what
  people can buy or claim — one to eight tiers — or take the honest one-tap
  default (free entry). Never a gate: an organiser who skips or fails still goes
  live; the flow just tells the truth about what that means.

- **ENTRY / EXIT.** A pane in the existing single-screen Go-Live flow (no new
  route — ADR-028). Reached when `gatheringId != null && !tierStepDone`, i.e.
  immediately after §5B's `create_gathering` returns the DRAFT, BEFORE §5C Ready.
  Exits forward to §5C Ready via **[Done — continue]** or **[Skip …]**. It is
  ALSO re-enterable after go-live (`reopenTierStep`) from the R1 truth state and
  the §5C add-tier affordance — `create_tier` legally accepts a LIVE gathering.

- **FLOW (tap budget).**
  ```
  create_gathering returns DRAFT  →  Tickets pane (§5E)
    add a tier:   [Add a ticket tier] → type name · price · capacity
                  → [Add this tier] ①   (create_tier → committed, locked card)
                  → repeat up to 8, or
    footer:       [Done — continue] → §5C Ready
    honest skip:  [Skip — free entry, anyone can claim a ticket] ①
                  → creates ONE "General entry" free tier → §5C Ready
  ```
  A committed tier costs 1 tap ([Add this tier]); Skip is 1 tap. The whole step
  is optional to interact with — the tap budget for Go Live (§5 §3) is unchanged.

- **STRUCTURE (top→bottom).** Header *"Ticket tiers"* + one caption line
  (*"Set what people can buy or claim. Enter 0 for a free tier. Up to 8 tiers."*)
  → committed tiers as locked cards (top) → editable rows below → an
  **[Add a ticket tier]** / **[Add another tier]** text button (hidden at the
  8-tier cap) → sticky footer: primary **[Done — continue]** and, only while
  nothing is committed, secondary **[Skip — free entry, anyone can claim a
  ticket]**.

- **COMPONENTS** (organiser-local; reuse the shared **PriceText**,
  **ZukaTextField**, **ZukaButton**, **FormBanner** — do not redefine).
  - **TierEditor** — the pane. Owns the row list, the add/cap logic, the footer,
    and a transient hint banner (*"Add or remove the tier you're still editing
    first."*) when Done is refused with a dirty draft row.
  - **EditableTierRow** — one uncommitted tier: **Ticket name** (ZukaTextField,
    ≤ 120 chars) · **Price (UGX)** (number keyboard, digits-only IME, `0` = free)
    · **Capacity** (number keyboard, digits-only, blank = unlimited) · a row
    FormBanner for row-level RPC/offline failure · **[Add this tier]** primary
    (becomes **[Try again]** after a failure) + **[Remove]** text button. Failed
    rows wear an `error`-red border.
  - **CommittedTierCard** — a locked, non-editable tier: check glyph · name ·
    capacity in words (*"Unlimited"* / *"N tickets"*) · **PriceText**. NO edit,
    NO delete (no backend RPC exists — the client must not pretend one tier can
    be un-created). *(Superseded in part, F017: `amend_tier` (S059) now exists,
    so a committed tier's name/price/capacity CAN be corrected — but NOT here. In
    the create/go-live flow the committed card stays view-only by design;
    post-go-live amendment lives on §5E-A's `AmendTierCard`. Delete still has no
    RPC — the un-created caveat holds.)*
  - **TicketSummary** (renders on §5C) — the committed tiers as name + PriceText
    rows, plus an add-tier link; OR the R1 no-tier warning card (below).
  - **LiveButUnticketable** (the R1 truth pane) — specced under STATES.

- **FIELD VALIDATION (client-side, BEFORE `create_tier`).** The client is the
  FIRST line of defence here, not a backstop: `create_tier` never checks the
  name, permits `capacity = 0`, and there is no edit RPC — so a bad row would be
  permanent on the attendee's §6 Detail. All numbers go through ONE integer
  parser (price AND capacity) — money is integer UGX (SOUL.md).
  | Field | Rule | Error copy |
  |---|---|---|
  | Ticket name | required, trimmed non-empty, ≤ 120 | "Name this ticket." / "Keep the name under 120 characters." |
  | Price (UGX) | required; clean non-negative **integer**; `0` legal (= free) | "Set a price. Enter 0 for free." / "Whole shillings only — no decimals, commas or minus." |
  | Capacity | blank = unlimited (legal); else clean integer **> 0** | "Whole number of tickets, or leave blank for unlimited." / "A capacity of 0 sells nothing. Leave blank for unlimited." |
  The parser rejects decimals · thousands separators · negatives · exponents ·
  leading zeros · int64 overflow. A digits-only IME formatter is a belt; the
  parser is the authority. Invalid fields block that row's submit and mark the
  offending field; the RPC does not fire until clean.

- **STATES (every one reachable — §1/§5 rigour).**
  - **empty** — no rows yet: only the **[Add a ticket tier]** button and the
    footer (Done + Skip). Never a blank pane.
  - **editing** — one or more EditableTierRows; values live in flow state (not
    widget state), so a backgrounded app or a failed submit never loses them.
  - **committing** — the tapped row's **[Add this tier]** spins and its fields
    disable; the Add-another and footer buttons disable (`anyBusy`). **Exactly
    one in-flight tier write at a time.** [committed] is terminal — a row with a
    `tier_id` is NEVER resubmitted, by any path (including Skip). Since **S048**
    that is a first line of defence rather than the whole guard: it keeps the
    replay off the wire at all, while `create_tier`'s per-row `client_key` makes a
    replay that does reach the server converge on the same tier.
  - **committed** — the row becomes a locked CommittedTierCard. No edit/delete.
  - **row RPC-error** — a hard `create_tier` rejection maps via
    `ZukaRpcClient` (never literal PL/pgSQL text); the row goes `failed`, keeps
    its typed values, shows the mapped line, and offers **[Try again]** (safe —
    the write did not land).
  - **row offline / submit-ambiguity → bounded-then-banner, then RETRY-ONLY** —
    exactly §5's `create_gathering` posture (S048 · RS048-F1), per row. The
    repository auto-retries the network class (bounded, jittered spacing) because
    the row's `client_key` makes a replay converge; if it still fails the row goes
    `failed` with honest copy — *"We couldn't confirm that saved. Tap Try again —
    this tier's values are locked so it can't be added twice."* — and **freezes**:
    fields disabled, **[Try again]** the only affordance, and **no [Remove]**.
    Remove is withheld deliberately: the tier may exist server-side, so dropping
    the row would hide a real tier and let its replacement duplicate it under a new
    key. Money-adjacent and worth the bluntness: a row edited 3,000 → 5,000 and
    re-tapped would converge onto the **3,000** tier and then render **5,000** on a
    locked, immutable CommittedTierCard — the buyer charged the DB's 3,000. A hard
    rejection stays editable and removable (it did not land).
  - **dirty-draft-on-Done** — Done with a non-empty uncommitted row is refused
    with the hint banner; empty draft rows are dropped silently. Zero committed
    tiers IS allowed to proceed (R1 — do not block go-live).
  - **at-cap** — at 8 tiers the add button is hidden (Vol 14 Ch2 ceiling,
    mirrored client-side; the server also raises at 8).
  - **R1 live-but-unticketable** — the honest failure state (see UX RULES R1).

- **CONTENT / COPY.** Header *"Ticket tiers."* Price hint *"0 = free."* Capacity
  hint *"blank = ∞."* Primary **Add this tier** → **Try again** on failure.
  Footer **Done — continue**; Skip reads, in full, **"Skip — free entry, anyone
  can claim a ticket"** — never a bare "Skip" (ruling (A): the affordance must
  SAY what it does). Committed capacity in words: *"Unlimited"* / *"120 tickets."*
  Terminology: this is a semantic creation surface, so copy is **tier** /
  **ticket** / **claim**, never "user".

- **COLOUR LOGIC.**
  - Primary **[Add this tier]** and **[Done — continue]** = **`emberGold`** —
    actions, never live states.
  - Add-tier text links = gold (an action affordance).
  - Committed card = neutral `ink` ramp + a `success` check glyph (a settled
    fact, not a live state).
  - Failed row border + field errors = `error` red (system, not coral).
  - R1 warning cards = `warning` amber border.
  - **NO `liveCoral` anywhere on §5E.** The tier step is entirely pre-live (or a
    post-live recovery) — coral stays reserved for the `go_live` moment (§5 §10).

- **PriceText law (hard, restated for the builder).** A `0` price renders **"Free"**
  (neutral pill), NEVER "UGX 0". No strikethroughs, no "was/now", no fake
  scarcity (Vol 16). The organiser types `0`; every downstream render (the
  committed card, the §5C summary, the attendee §6 TierRow) is PriceText —
  defined once, never recomputed client-side.

- **⚠ THE SEVEN MAGIC TIER NAMES (a build hazard the copy must respect).**
  `claim_free_ticket` (and the paid path) derive the ticket's CLASS from the tier
  name: `ticket_type := lower(tier_name)`, kept when it lands in the set
  **{standard · group · table · vip · streaming · comp · staff}**, else coerced to
  `standard`. So a tier literally named **"Comp"** or **"Staff"** MINTS comp/staff
  tickets — a real financial/access consequence, not a label. Design rule:
  - The Skip default is **`General entry`** on purpose — it derives `standard`
    (safe), and does not collide with the magic set.
  - Placeholders/examples must NOT casually suggest "Comp" or "Staff" as if they
    were ordinary names (current hint uses *"Early bird, VIP, General entry"* —
    VIP is deliberate and understood; Comp/Staff are not offered as examples).
  - This is the S007/S008 `ticket_type`-FK gap, left open server-side. The client
    does not fight it — it just must not surprise the organiser into it. Flagged
    as an open question below for a future "this name grants comp tickets — sure?"
    confirmation once an edit RPC exists.

- **UX RULES.**
  - **R1 — Skip is honest, not silent (founder ruling (A)).** Skipping does NOT
    create a blank/tier-less gathering. It creates EXACTLY ONE tier —
    `General entry`, price 0, unlimited — through the SAME commit-once submit
    path (so a re-tapped Skip cannot mint two). The affordance's copy states this
    outright.
  - **R1 — the live-but-unticketable truth state.** If Skip's `create_tier`
    fails (e.g. on 3G) and the organiser proceeds to go-live anyway, the flow
    must NOT show the celebratory coral LiveConfirmation and must NOT auto-route.
    It renders the **LiveButUnticketable** pane: a neutral LIVE chip (not coral)
    + *"It's live and on the feed — but it has no ticket tier, so nobody can
    claim a ticket yet. Add one now and people can start claiming."* + primary
    **[Add a ticket tier]** (reopens §5E — valid post-live) + secondary **[See it
    live anyway]**. The add-tier path stays reachable AFTER go-live.
  - **R1 — §5C Ready also tells the truth.** With no committed tier, the Ready
    pane's TicketSummary shows a `warning`-amber card — *"No ticket tier yet —
    nobody can claim a ticket."* + **[Add a ticket tier]** — and STILL offers
    **[Go Live now]**. Go-live is never blocked on the tier RPC.
  - **Commit-once.** Exactly one in-flight tier write; a committed row is
    terminal and never resubmitted. Since S048 this is the FIRST line of defence,
    not the only one — it keeps a replay off the wire entirely, while the row's
    `client_key` converges any replay that does reach `create_tier`.
  - **No edit/delete this session.** A committed tier is a locked card. Corrections
    would be a future append-style flow, not an in-place mutation (ADR-015 posture).
    *(SUPERSEDED F017 → see §5E-A: the S059 `amend_tier` RPC now exists. Post-go-live
    correction of name/price/capacity lives on the "Your gatherings" → [Edit tickets]
    surface; in-create-flow committed cards stay view-only. Delete is still out of scope.)*
  - **Africa-first.** Text-first rows, native number keyboards, no imagery, values
    survive error/offline/background, 10s timeout → honest offline copy.

- **OPEN QUESTIONS (backend dependencies).**
  - *AMENDMENT (F017, 2026-07-19): the "No edit/update tier RPC" question below is
    RESOLVED — `amend_tier` (S059) ships, consumed by §5E-A. The magic-name
    "confirm?" guard it asked for is still open (tracked in §5E-A's OPEN QUESTIONS).*
  - ~~**No edit/update tier RPC.**~~ **RESOLVED (S059 · F017).** A typo in a tier
    name/price/capacity CAN now be corrected post-go-live via `amend_tier`, on the
    §5E-A "Your gatherings" → [Edit tickets] surface. Still open: the "this name
    grants comp/staff tickets — confirm?" guard (the magic-name hazard above is
    unchanged by amend), carried into §5E-A.
  - **`ticket_type`-FK gap (S007/S008).** The seven magic names are derived, not
    validated; a future backend fix (explicit tier→class mapping) would let the
    UI stop depending on organiser name-hygiene. Flagged, not owned here.
  - ~~**`create_tier` idempotency.** No key — the submit-ambiguity state is a
    mitigation, not a fix~~ **RESOLVED (S048)**: `p_client_key` on `create_tier`
    (UNIQUE per gathering; replay converges, no duplicate) + the retry-only
    frozen state after a network-ambiguous failure (RS048-F1). Still open here:
    the reconcile view (server tier list on re-open) — tracked as the
    `hosting-your-gatherings-rpc` backlog row.
  - **No server tier read on re-open.** `reopenTierStep` shows only tiers created
    in THIS session's state; a post-restart re-open would not list prior tiers
    until a `gathering_tiers(gathering_id)` read exists (organiser-scoped).

**BUILT (F014) — §5-A2 "Your gatherings" recovery surface.** The organiser read
half of Hosting Robustness A2 (spec `2026-07-16-hosting-robustness-design.md` §2;
backend `list_my_gatherings()` delivered S052). Closes the §5C single-gathering
gap: a crash after create, or a publish-without-go-live, previously left NO way
back to the gathering.

- **Routing fork (Hosting entry, `HostScreen`).** A signed-in organiser with ≥1
  gathering now lands on **"Your gatherings"**; with **0** they go **straight to
  Create** (byte-identical, no extra tap); anon still hits Sign-in first. The fork
  keys off `list_my_gatherings()`; the entry read is a fast STABLE query and does
  not gate go-live — the 60-second proof is unregressed.
- **The list.** Server-ordered rows (live → upcoming → draft → past) grouped under
  section headers (reuses **BandHeader**; a stable partition, never a client
  re-sort — RPC contract §6). Each row: title · venue/when · tier COUNT, with the
  status **in words** via a new **GatheringStatusPill** — the ONE shared coral
  **StatusPill** for `LIVE` (Colour-as-Information), a neutral word-pill for every
  other lifecycle state (same distinction §5C's `_StatusChip` draws). No new
  feed-card codepath. **Scope fence (ADR-027):** recovery surface, not a dashboard
  — no edit, delete, analytics, attendee counts, or pagination (the ≤50 cap is
  silent by design).
- **Row tap → the existing §5C Ready screen, reused.** A recoverable gathering
  (DRAFT/PUBLISHED/ACTIVE) resumes into §5C Ready via a `resumed` seeding of the
  Go-Live controller: DRAFT → **[Go Live now] + [Publish only]**; PUBLISHED/ACTIVE
  → **[Go Live now] alone** (partial-chain recovery — a second publish would
  23514). The resumed Ready view renders honestly from only the recovery read's
  fields: it **hides Type/Medium** (not carried) and shows the **tier count**
  rather than per-tier editor rows. A LIVE or terminal row opens the public
  reader (`/g/:id`) instead — already live or done, nothing to go-live.
- **"+ New gathering"** (AppBar action) is one tap back to a fresh Create.
- **STATES** — loading skeleton · offline (mapped copy + Retry) · error · the
  honest empty zero-state (rare — the entry routes 0-gatherings to Create).
- **Closes the re-open open question above** for the LIST case: the recovery read
  now surfaces prior gatherings and their tier COUNT on cold start. A per-tier
  server read on `reopenTierStep` (tier NAMES/prices) remains open.

**AMENDED (F014, RF014 F014-2 review carry) — two robustness fixes to the above:**

- **Zero-tier recovery is no longer a dead-end.** A gathering resumed with **0
  tiers** — the "crashed right after create, before adding a tier" case this
  screen exists to recover — now shows an **[Add a ticket tier]** affordance on the
  resumed §5C Ready view (mirroring the create-flow empty state, same emberGold),
  which reopens the tier editor (`reopenTierStep`, verbatim reuse) so the organiser
  can add a ticket instead of being forced live with nothing to sell. Once a tier
  exists the resumed line reads "N ticket tiers" and the add control disappears.
  **This is NOT an ADR-027 breach:** that fence forbids turning the list into a
  *dashboard* (attendee counts, revenue, edit/delete); it does not govern *pre-live
  tier creation on your own gathering*, which is the same action the ordinary create
  flow already offers for the identical state.
- **60-second-proof guard on the entry fork.** The entry read is gated behind a
  loader, so a slow `list_my_gatherings()` could delay a **first-time** organiser's
  Create form. `HostScreen` now bounds this: if the **list read** is still pending
  **2s after it begins**, the fork falls through to Create and latches there for
  that entry (a late list arrival can't yank them onto the list). The budget is
  **armed on the observed `signed-in && list-loading` state, not at screen mount**
  (RF014 F014-1): the sign-in resolve that precedes the list read is itself a
  network round-trip that can, on the same slow link, exceed 2s — anchoring the
  budget to mount would let it lapse before the list read even started, leaving
  that read unbounded. Anchoring to the loading state bounds the list read's own
  wait regardless of how long sign-in took. A returning organiser on a
  pathologically slow link then lands on Create for that entry only — the provider
  is `autoDispose`, so recovery re-resolves on the next `/host` entry; deferred,
  never lost. The sacred proof outranks a marginally faster recovery.

**DESIGN-REVIEW RATIFICATION (2026-07-18, RF014 F014-2 designer gate).** The as-built
surface (`your_gatherings_screen.dart` + `widgets/gathering_status_pill.dart`) was
reviewed against ZUKA's design law and **conforms** — no code change requested:

- **Colour-as-Information holds literally.** Coral appears in exactly two places,
  both meaning *live now*: the shared `StatusPill` on a `LIVE` row and the
  `BandHeader` "Live now" accent dot. Every other lifecycle state
  (DRAFT/PUBLISHED/ACTIVE/ENDED/CLOSED/CANCELLED) renders a neutral word-pill —
  a DRAFT/PUBLISHED is never coralled. Gold (`emberGold`) is confined to primary
  actions ("New", the empty-state CTA). No decorative brand colour.
- **Token discipline clean.** Only `ZukaColors` / `ZukaSpacing` / `ZukaTypography`
  across both files — no raw hex, no bare `TextStyle`, no hardcoded spacing. (Radii
  and icon sizes are numeric literals, consistent with the existing shared
  `StatusPill`/`BandHeader`; ZUKA has no radius/size token family.)
- **State completeness holds** — shaped loading skeleton (4 ghost rows, never a bare
  spinner), honest empty zero-state with direction + CTA, and a split error state
  (offline "Can't reach Kampala" + Retry vs hard failure) keyed off the mapped
  `ZukaRpcException`. Offline is first-class, matching the app's discovery copy.
- **ADR-027 fence respected** — title · venue/when · tier COUNT · status pill; tap
  resumes §5C or opens the reader. No edit, delete, analytics, attendee counts, or
  pagination surfaced.
- **Two consistency notes (neither a defect).** (1) The neutral word-pill is a
  faithful copy of the shared discovery `StatusPill`'s neutral branch (fully-rounded
  radius, `ink1`/`ink2`, `micro` w600) — the *right* reference, since it sits beside
  the coral `StatusPill` in the same row; the code comment's "mirrors §5C
  `_StatusChip`" is loosely worded (§5C's chip is a squarer 6px rect) but the visual
  choice is correct. (2) The recovery list renders a `LIVE` row coral while §5C's
  post-go-live control screen renders its own `LIVE` state neutral — a defensible,
  arguably more colour-law-faithful divergence: a *list* row follows the discovery
  feed convention (coral = live), whereas §5C is a single-gathering control view.
  Left as-is; flagged only for the record.

**§5E-A — TIER AMENDMENT AFTER GO-LIVE (organiser · F017)**

*Consumes the S059 `amend_tier` RPC. Where §5E lets an organiser CREATE a tier
(pre-live, in the go-live flow) and §5-A2 gives them a way BACK to their own
gatherings, §5E-A lets them CORRECT a LIVE gathering's ticket tiers — name,
price, capacity — from "Your gatherings". This is the surface that retires §5E's
"a committed tier is permanent" language. It is recovery of your OWN gathering,
NOT a dashboard: no attendee list, no revenue, no per-claim view.*

- **PURPOSE.** After a gathering is live, let its organiser fix a tier they got
  wrong — a mistyped name, a wrong price, a capacity that needs raising — without
  taking the gathering down. One person, their own tiers, in place.

- **ENTRY POINT.** On the §5-A2 **"Your gatherings"** recovery list, a row for a
  gathering that HAS ≥ 1 of its own ticket tiers AND is NOT terminal (status ∉
  `ENDED` / `CLOSED` / `CANCELLED`) gains a trailing text affordance
  **[Edit tickets]** (`emberGold`, `Icons.edit` / `confirmation_num`, size 18).
  It does **NOT** change the row's primary tap (LIVE → public reader; recoverable
  → resume §5C). It pushes a new top-level, chrome-less route **`/host/tiers`**
  (a mode, like `/host`), carrying the `MyGathering` via `extra`. Terminal rows
  and zero-tier rows show **no** affordance. This stays inside the **ADR-027
  recovery-surface fence**: fixing your OWN gathering's tiers is recovery, not a
  dashboard — the same reasoning RF014 F014-2 used to allow pre-live tier
  creation on this surface.

- **FLOW (tap budget).**
  ```
  "Your gatherings" row  →  [Edit tickets] ①  →  /host/tiers (TierManagementScreen)
    per tier card:  [Edit] ①  → change name / price / capacity → [Save changes] ①
                    (amend_tier → card re-renders from the returned row)
                    or [Cancel] → back to VIEW mode (no write)
  ```
  Reaching an edit is 2 taps ([Edit tickets] → [Edit]); committing a correction
  is 1 more ([Save changes]). Well inside the 3–5-tap law for a non-core action.

- **SCREEN — `TierManagementScreen`** (AppBar title **"Edit tickets"**). Loads
  the organiser's OWN active, non-vendor tiers via a direct **RLS-scoped
  `gathering_tier` table read** (`id`, `name`, `price`, `capacity`,
  `tickets_sold`). It renders a list of **AmendTierCard**. States below.

- **COMPONENTS** (reuse the shared **ZukaTextField**, **ZukaButton**,
  **FormBanner**, **PriceText** — do not redefine).
  - **AmendTierCard** — the "reopened-context equivalent" of §5E's
    **CommittedTierCard**, now WITH an edit affordance. Two modes:
    - **VIEW mode** — name · **PriceText**(price) · a capacity/sold line
      (*"Unlimited"* or *"N tickets"*, with *"· M sold"* appended when
      `tickets_sold > 0`) · a trailing **[Edit]** text button.
    - **EDIT mode** — three **ZukaTextField**s pre-filled with current values:
      **Ticket name** (≤ 120) · **Price (UGX)** (digits-only IME) · **Capacity**
      (digits-only IME); a **FormBanner** for the row's mapped RPC/offline error;
      **[Save changes]** primary + **[Cancel]** text button. **Frozen-while-busy:**
      the three fields disable and **[Save changes]** spins during the amend call
      — the same commit/frozen pattern as §5E's `_EditableTierRow`.

- **GUARD COPY (client-side pre-validation — honest, never a raw `23514`).** The
  client is the FIRST line of defence; the server constraints are the backstop.
  | Guard (S059) | Rule | Error copy |
  |---|---|---|
  | Capacity floor (reject-only, ADR-014) | capacity may NOT drop below `tickets_sold` | *"You've already sold {N} — capacity can't go below that."* |
  | Make-unlimited-once-finite is IMPOSSIBLE (NULL-sentinel gap) | a finite Capacity field must NOT be clearable to blank/unlimited | *"Capacity can't be cleared back to unlimited."* |
  | Free↔paid boundary | a price change crossing `price_ugx = 0` in EITHER direction is rejected | *"You can't switch a tier between free and paid."* |
  - **Capacity direction.** An UNLIMITED tier MAY be GIVEN a finite capacity
    (allowed). A finite tier may be raised or lowered (but not below sold, and not
    back to unlimited). Never offer a "make unlimited" path.
  - **Free↔paid.** Same-side paid→paid changes are allowed; a `0` price always
    renders **"Free"** (the PriceText law, §5E), never "UGX 0".
  - **Partial update.** Only edited fields are sent. If nothing changed, the amend
    RPC is **NEVER fired** — an all-NULL call is a client no-op, and **[Save
    changes]** on an unchanged form just closes edit. The card re-renders from
    `amend_tier`'s **returned row** (no re-read).
  - **Vendor tiers never appear.** The read filters `vendor_category_id IS NULL`;
    no client path amends a vendor tier. **Deactivate/reactivate (`is_active`) is
    out of scope.**
  - **Access.** Non-organiser / anon cannot reach this: `/host` is auth-gated, and
    both the RLS read and `amend_tier` bind to `auth.uid()`.

- **STATES (every one reachable).**
  - **loading** — a skeleton while the tier read is in flight (never a bare
    spinner; matches §5-A2's shaped loading).
  - **offline / error** — keyed off `ZukaRpcException.isNetwork`, with a **Retry**
    — reuse the §5-A2 `your_gatherings` `_ErrorState` copy shape (offline "Can't
    reach Kampala" + Retry vs hard failure).
  - **empty** — an honest empty state (no amendable tiers), never a blank screen.
  - **list** — one **AmendTierCard** per active non-vendor tier.
  - **card VIEW / card EDIT** — per-card modes above.
  - **card committing** — fields disabled, **[Save changes]** spinning; on success
    the card returns to VIEW rendered from the returned row; on a mapped failure
    the row's FormBanner shows the honest line and stays editable.

- **COLOUR LOGIC.**
  - **NO `liveCoral` anywhere on §5E-A.** Amendment is post-live *recovery*, not
    the go-live moment — coral stays reserved for `go_live` (§5 §10) and for the
    `LIVE` status pill on the §5-A2 list row itself.
  - **`emberGold`** for the actions: **[Edit tickets]** (list affordance),
    **[Edit]**, **[Save changes]**.
  - **`error`** red for field- and row-level errors (the FormBanner, invalid
    fields) — system, not coral.
  - Neutral **`ink`** ramp for the card body (a settled fact being corrected).

- **UX RULES.**
  - **Recovery, not a dashboard (ADR-027).** Name/price/capacity of your OWN
    tiers only. No attendee identities, no revenue totals, no per-claim view —
    `tickets_sold` shows ONLY as the honest "· M sold" capacity-floor context.
  - **Reject-only corrections (ADR-014).** The client blocks the illegal move
    with plain copy BEFORE the RPC; the server constraint is the race backstop,
    never surfaced raw.
  - **No-op is silent.** An unchanged Save fires nothing and just closes edit.
  - **Terminology.** This is a semantic tier surface — copy is **tier** /
    **ticket** / **claim**, never "user".
  - **Africa-first.** Text-first cards, native number keyboards, values survive a
    failed/offline submit, honest offline copy with Retry.

- **OPEN QUESTIONS (backend dependencies).**
  - **Magic-name "confirm?" guard still open (carried from §5E).** `amend_tier`
    lets a tier be RE-named into the seven magic names
    ({standard · group · table · vip · streaming · comp · staff}) — renaming a
    tier to *"Comp"* / *"Staff"* still derives comp/staff ticket class on future
    claims. A "this name grants comp/staff tickets — sure?" confirmation is the
    remaining guard; not built this session.
  - **Make-unlimited is a backend gap, not a policy.** The "can't clear capacity
    back to unlimited" copy reflects the S059 NULL-sentinel gap, not a product
    decision — if a future `amend_tier` gains an explicit unlimited sentinel, the
    UI can offer the direction and this guard relaxes.
  - **Delete / deactivate a tier.** Still no RPC and out of scope; a tier can be
    corrected but not removed post-commit.

- **CHANGELOG.**
  - *2026-07-19 (F017):* Section created. Formalises the as-implemented
    `amend_tier` (S059) consumption: `[Edit tickets]` on §5-A2 → `/host/tiers`
    `TierManagementScreen` → `AmendTierCard` VIEW/EDIT. Retires §5E's "a committed
    tier is permanent" language (amendment notes added in §5E's scope paragraph,
    `CommittedTierCard`, the "No edit/delete this session" UX rule, and the
    "No edit/update tier RPC" open question). Three client guards specced
    (capacity floor · no-clear-to-unlimited · free↔paid boundary), no coral,
    partial-update no-op, vendor tiers excluded.

**VISUAL PASS — ENRICHMENT + POSTER TEMPLATES (A-08/A-09 · 2026-07-18 ·
REVIEWED — founder-approved 2026-07-18: no-coral-on-posters law ratified;
all three templates ship)** Mockup:
`mockups/a-08-go-live-enrichment/enrichment-and-posters.html` (one sheet for
the pair — templates are picked from inside enrichment).
- **Iron rule, stated and rendered:** the 60-second path gains zero required
  fields, zero screens, zero taps — frame 1 is §5B untouched, as the proof
  statement. Enrichment attaches ONLY behind go-live: a skippable offer card
  on the live confirmation (*"Make it easier to find?"*, dismisses per
  gathering, never nags twice) + quiet rows on "Your gatherings" (§5-A2).
- **Description (A-08):** one optional field, ≤ 400 chars, length-only
  validation, voice guidance (*"write it like you'd tell a friend"*); offline
  = saved-on-phone, posts-when-online, gathering stays live. Renders on §6
  Detail under the facts, body type, plain text. **Backend ask:** the
  `gathering.description` column exists but has NO write path —
  `p_description` on create (unused by the 60s form) or an enrich RPC
  (ADR-015-friendly correction-append posture); backend track decides shape.
- **Poster (A-08):** two doors — template (A-09) or own photo. Photo path is
  budget-honest: on-device compression to the PROPOSED ≤ 80 KB hero budget
  with the size shown (*"2.1 MB → 74 KB — loads 28× faster on 3G"*); failure
  copy routes to templates. **Poster = Media (bytes, ADR-009 — never
  conflated with Memory); no backend home exists: Storage bucket + media
  reference + attach RPC are the named asks.**
- **Poster templates (A-09):** three named styles, auto-filled entirely from
  existing gathering data (title · type · venue · starts_at · tier price) —
  the organiser edits nothing, one tap: **T1 "Midnight Pulse"** (midnight +
  gold energy) · **T2 "Ember"** (full gold — sunlight/boda-distance loud) ·
  **T3 "Forest Gate"** (scheduled-state calm). Each renders 4:5 (Detail hero
  + feed) and 9:16 story (WhatsApp/IG). **Colour law extension minted: NO
  CORAL ON POSTERS — a poster advertises the future; coral means live NOW
  and the feed's pill owns it.** Founder picks which templates ship.
- On founder approval: A-08/A-09 → REVIEWED (incl. the no-coral-on-posters
  law + template set); backend asks route to the backend track; build also
  needs on-device render/compression deps (ADR-030 pile).

**AMENDED (F016, 2026-07-18) — the Ticket-name field becomes a COMBOBOX.**
Closes the founder ask ("*set their types of tickets so the next gathering is
less hassle*") with the YAGNI shape S058 already shipped: the organiser's OWN
past tier names ARE their presets — `my_tier_name_suggestions()` (≤ 20, most-
recently-used first, own tiers only, caller-scoped). **No preset table, no write
path, no management UI** (explicitly out of scope — the history is the preset).
The **EditableTierRow** `Ticket name` field (still a `ZukaTextField`, same label ·
hint · `error` · `enabled`/frozen · `maxLength 120` · digits/validation wiring)
is now the field-view of a `RawAutocomplete<String>` — free-typing stays the
primary path; suggestions are an *offer*, never a constraint.

- **STATES (all four reachable):**
  - **zero-history** — the organiser has never named a tier (or the fetch is
    still loading, or it failed): the field renders **exactly as before** — a
    plain typable `ZukaTextField`, no dropdown affordance, no "no suggestions"
    empty box. Indistinguishable from the F006 field. (RPC contract explicitly
    permits silently doing nothing here.)
  - **has-suggestions** — on focus/typing, a dropdown of the organiser's past
    names appears (matching = case-insensitive substring; an **empty query shows
    the full history**, so tapping into the field reveals every past name). The
    list uses the shared surface language — `ink1` panel, `ink5` rows, 10px
    radius, capped at ~220px tall and the field's width.
  - **typing-a-new-name** — a name **not** in the list is always accepted and
    committed verbatim; the suggestions never limit `create_tier(p_tier_name…)`.
    A non-matching query simply shows no dropdown.
  - **suggestion-selected** — tapping a row fills the field with that name and
    pushes it into flow state (identical to typed input); the field **stays
    editable** afterwards (the organiser can tweak "VIP" → "VIP (table)").
- **Fetch discipline:** resolved **once per tier-step entry** (watched at the
  **TierEditor** level, not per row, not per keystroke), via an `autoDispose`
  provider — a **point-in-time read, never cached across visits** (the organiser
  may have created tiers since; the R1 post-live `reopenTierStep` re-fetches).
  A loading/error state **degrades silently to the plain field** — never a
  spinner over the field, never a fetch-error banner: the suggestions are an
  enhancement, never a gate on tier entry, and never block the 60-second proof.
- **No new colour law, no coral.** The combobox is a pre-live convenience; §5E's
  no-`liveCoral` rule stands. Reuses `ZukaTextField`/`ZukaTypography`/`ZukaColors`
  only — no new shared component minted.

**AMENDED (F018, 2026-07-19) — §5B Type becomes a BOUNDED MULTI-SELECT (1–6).**
Consumes S060, which widened `gathering.gathering_type TEXT` →
`gathering_types TEXT[]` with a CHECK of cardinality BETWEEN 1 AND 6, and moved
`create_gathering` to `p_gathering_types text[]`. A gathering is rarely one thing
("Live music" *and* "Nightlife" is the honest description of most Kampala nights);
forcing one chip made organisers file a real night under a lie. The field-stack
line and the validation-table Type row above are amended in place; this block
carries the reasoning, the states, and the guard.

- **THE SACRED-PROOF GUARD (ruling, in writing).** The group **starts empty and ONE
  tap satisfies submit**. The 60-second Go Live path therefore gains **zero required
  interactions** versus the old single-select — the organiser who taps one chip and
  hits [Create gathering] performs the byte-identical number of taps they did
  before. **The multi-select is strictly opt-in depth**: taps 2–6 are an
  affordance an organiser may reach for, never a cost the form imposes. This is the
  same iron rule the A-08/A-09 enrichment pass ships under (zero required fields,
  zero screens, zero taps), applied to a field that already existed. Any future
  change that makes a second chip *required*, or that lands the group pre-seeded
  with a default the organiser must clear, breaks Proof #1 and is forbidden here.

- **CHIP STATES (three, all reachable — `MultiChoiceSelector` / `_Chip`).**
  | State | Fill | Border | Text | Tappable |
  |---|---|---|---|---|
  | unselected | `ink1` | `ink2` | `ink5`, w400 | yes |
  | selected | `emberGold` | `emberGold` | `midnight`, w600 | yes (deselects) |
  | max-reached-disabled | `ink1` | `ink2` | `ink5`, w400 — whole chip wrapped in `Opacity 0.4` | **no** (`onTap: null`) |
  Gold-on-selected is unchanged ChoiceSelector grammar: a chosen chip is a *choice*,
  not a live state, so this is gold's action/active register — **no coral anywhere on
  this field**. The dimmed state deliberately does NOT borrow the `error` treatment:
  "you already have six" is a boundary, not a mistake.

- **BOUNDS ARE ENFORCED IN THE TAP HANDLER, not merely in validate().** This is the
  load-bearing choice, not a detail. An out-of-range selection is made *unreachable*
  rather than *reported*:
  - **min-1** — deselecting the SOLE remaining chip is a silent no-op. The organiser
    can never empty a field they have already satisfied, so the empty-state error
    cannot be re-entered by accident once passed.
  - **max-6** — at six, unselected chips dim and refuse taps; **a selected chip stays
    tappable at the ceiling**, so the ceiling is a swap, not a wall. Without that,
    six taps would trap you.
  - Consequence to know when building: `validate()`'s over-full copy **"Pick up to 6
    types."** is a backstop for a state the UI cannot currently produce. Keep it —
    it is the guard if a future entry path (deep link, draft restore, paste) ever
    seeds the list from outside the chip group — but do not design around it firing.
  - Empty-state copy stays **"What kind of gathering?"**. Distinct copy per bound is
    mandatory: an empty selection and an over-full one are different mistakes and
    must never share a message.

- **Selection ORDER is meaning, not incidental.** The list is stored and sent in the
  order the organiser tapped, and every reader renders that order. First chip tapped
  reads first everywhere — it is the organiser's own ranking of what the night mostly
  is. Never client-re-sort the array alphabetically or by the catalogue order.

- **Downstream reads (already built this session).**
  - **§5C Ready** summary row "Type" joins the chosen labels with `", "`.
  - **§6 Gathering Detail** (`_kindLine`) reads the `gathering_types` array and joins
    into the kind line — e.g. *"Music, Comedy · In person"*. Blank/whitespace tokens
    are dropped rather than rendered as an empty segment.

- **§5-A2 RESUMED READY STILL HIDES TYPE — unchanged, and correct.** The
  `list_my_gatherings()` recovery read does not carry the type field, so the resumed
  §5C Ready view continues to omit the Type row entirely (as it already omits
  Medium). It renders honestly from only what the recovery read returns. **Do not
  fabricate it** — not from the local draft (a cold start has none), not with a
  placeholder, not as "Type: —". A hidden row is honest; an invented one is a lie
  about the organiser's own gathering. Revisit only if the recovery RPC is widened
  to return `gathering_types`.

- **NAMING RULING (S060 · recorded here so the UI layer cannot drift).** The
  schema, the RPC parameter, and the event payload all say **`gathering_type(s)`**
  — **never `experience_type`** — so Vol 03's "Experience Capital" is never
  shadowed by an unrelated taxonomy field. **"Experiences" is permitted as a
  UI-ONLY label**, under exactly the latitude ADR-011 already gives the UI to say
  "Events" over `gathering`. So: consumer-facing chrome may read "Experiences";
  every semantic, structural, and code-level name stays `gathering_types`. This is
  a *use* of an existing allowance, not a new deviation — nothing to log in the
  Bible Deviations register.

- **NOT changed:** `ChoiceSelector` remains single-select and still owns **Medium**
  (Physical · Digital · Hybrid) — a genuine single-select whose contract was
  deliberately not bent to absorb multi-select. Two components, two honest contracts.

- **CARRIED FORWARD (design notes for the next pass on this field, none blocking).**
  1. **The silent min-1 refusal has no explanation.** Tapping your last chip does
     nothing and says nothing. Cheapest honest fix if it ever confuses anyone: on a
     blocked deselect, briefly swap the hint line to *"Keep at least one."*
  2. **The dim ceiling likewise refuses silently.** Consider swapping the hint line
     to *"That's 6 — tap one to swap."* while `atMax`. Directional, not scolding.
  3. **`Opacity 0.4` on `ink5`-over-`ink1` is a contrast risk** under Kampala
     sunlight on a cheap panel. Worth measuring against WCAG before this pattern is
     reused elsewhere; a dedicated dim token would beat a blanket opacity.
  4. **`_Chip` is a bare `GestureDetector` with no `Semantics`** — a screen reader
     gets no selected/disabled state and no toggle role. Pre-existing (it predates
     this change and affects ChoiceSelector equally), logged here because
     multi-select is where the missing state actually costs comprehension.

- **CHANGELOG.**
  - *2026-07-19 (F018):* Type amended single-select → bounded multi-select 1–6
    (S060 `gathering_types text[]`). §5B field-stack line and validation-table Type
    row edited in place (both error strings); `MultiChoiceSelector` catalogued in the
    Shared Component Library. Sacred-proof guard and the S060 naming ruling recorded.
    §5-A2 resumed-Ready Type omission confirmed unchanged. Four non-blocking notes
    carried (silent min/max refusals · dim-state contrast · chip semantics).

---

### §6 — Gathering Detail (the 3-tap launchpad)

*Sacred Proof #3 starts here. The feed said "this looks alive"; Detail answers
"do I go?" and carries the only Buy affordance in the app (§3: no Buy on any
feed card). FDES-01, 2026-07-10.*

**1. PURPOSE** Give a person everything needed to commit in one screen-length:
what, where, when, who's hosting, what it costs — then one gold action.

**2. ENTRY POINTS** Feed card tap (§1) · map pin sheet (§2) · deep link /
shared card `zuka://g/<id>` · push. Zero-login: Detail renders fully without
an account (proof 2 extends here); identity enters only at the Get-ticket
write (§7).

**3. FLOW** Card tap → Detail renders (hero + facts + tiers) → person taps
**[Get ticket — UGX 25,000]** (or **[Count me in — Free]**) → §7 takes over.
Single-tier gatherings preselect the tier — the primary button IS the tier.
Multi-tier: TierRows select (1 tap), primary button reflects the selection.
*Free tiers (FDES-02):* a `price_ugx = 0` tier renders its TierRow with the
**Free** neutral pill (PriceText law — never "UGX 0"); when the selected tier
is free the primary button reads **[Count me in — Free]** and arms the §7
free-claim branch (§7.2b) instead of the paid flow. The auth moment is
identical — no session → the same lazy AccountSheet, the armed claim fires
on sheet success. Mixed gatherings (free + paid tiers) just work: the button
follows the selected tier.

**4. SCREEN INVENTORY** Detail (scroll) · tier selection inline (no separate
screen — a screen would spend a tap).

**5. COMPONENTS** StatusPill · PriceText · TierRow · ZukaButton ·
ProvenanceRibbon (Signal variant only) · ActionRow (§3 secondary actions:
Share · Save · Going · Follow).

**6. STRUCTURE (top→bottom)** Hero block (title, StatusPill live/soon/tonight,
venue + distance, start–end in local words: *"Tonight · 9 PM – late"*) →
organiser line (name + trust STATE in words when it exists — never a number,
never a placeholder score) → description (organiser text, read-more fold) →
**Tiers** (TierRow list or the single price) → sticky footer: primary
**[Get ticket — <PriceText>]** + the ActionRow. Signal items (§3) show
**Corroborate** here instead of any ticket affordance — a Signal sells nothing.

**7. STATES**
- *Default* · *Loading:* skeleton (hero + three ghost rows), <1.5s/4G.
- *Offline:* render from feed-cached fields + quiet ribbon; tiers need the
  network → tier area shows *"Connect to see tickets"*; Get-ticket disabled.
- *Sold out (all tiers):* primary swaps to **Notify/waitlist** posture (§3
  row) — backend seam, see Open Questions.
- *Ended/cancelled:* honest banner, actions collapse to Share.
- *Paid-gated (ADR-026 gates still open):* see §7.6 — tiers visible, price
  shown, primary disabled with *"Paid tickets open soon — free entry works
  now."* **Founder ruling logged (recommended: visible-but-gated, because
  hiding prices trains distrust; hiding is one config flip if counsel says
  otherwise).**
- *2GB/3G budget:* one image max (hero, low-res first, suppressed on
  low-data); everything else text. Target: interactive <2.5s on 3G from a
  warm feed.

**8. CONTENT / COPY** Facts before adjectives. *"Hosted by <name>"*, *"2.1 km
· Kabalagala"*. Never "Hurry", never "Only X left" unless X is a true
backend-derived count AND X ≤ 10 (then: *"8 tickets left"* — a fact, not a
push). Terminology: UI may say "ticket/event"; semantics stay Gathering.

**9. COLOUR LOGIC** Pills per the hard rule (coral only if live now). Primary
button gold always — buying is an action, not a state.

**10. UX RULES** No Buy on feed (pinned) — Detail is the only purchase
surface. One image budget. No comment thread this pass (§3 posture). Signal
detail never shows tiers.

**11. OPEN QUESTIONS (backend dependencies)**
- **Detail read RPC missing (API law 10):** rendering Detail today would
  compose `discovery_feed*` fields + a direct `gathering_tier` read (RLS
  public branch, S026). One `gathering_detail(p_gathering_id)` RPC returning
  gathering + venue + public tiers in one round-trip is the ask — one call,
  one screen, 3G-honest. **Blocks §6 build.**
- **Waitlist/notify:** §3 promises it for sold-out; no backend exists. Design
  degrades to Share until it does.

**BUILT (F007) — the reader half.** F007 shipped §6 as a **pure, read-only
reader**; the §6 vision above stands unchanged, this records what of it is
live vs still ahead. All client-only, zero SQL — it wires the existing
`gathering_detail(uuid)` RPC (S029).

*Live now:*
- Route `/g/:gatheringId` — top-level and chrome-less (like `/host`). A feed
  card tap now **pushes Detail** (was a `_detailLater` snackbar). Signal cards
  keep a later-build snackbar, not Detail — a Signal sells nothing (§6 rule 10).
- Renders the fact stack: title · venue (name + address; digital →
  **"Online"**) · date-time range (*"Jul 16, 8:00 PM – 11:30 PM"*,
  crosses-midnight aware) · type · medium · description (**shown only when
  non-blank** — no empty box) · ticket tiers (display-only).
- Tier facts honour the laws: **PriceText** (`0` → **"Free"**, never "UGX 0");
  sold-out (`is_sold_out`) shows **"Sold out"** with the price hidden;
  **"N left"** renders ONLY for a true `remaining` in 1..10, never inferred
  from NULL; only the 8 cheapest tiers return (no implied 9th).
- States rendered: *loading* (static **skeleton**, not a spinner) · *loaded* ·
  *not-found* (zero-row DRAFT/stale id) · *ended* · *cancelled* · *live* pill ·
  *offline/timeout* (calm message + **Retry**, no auto-retry).

*Copy shipped (owned here — the screen follows these exact strings):*
- **Not-found:** *"This gathering isn't available."*
- **Ended banner:** *"This gathering has ended."*
- **Cancelled banner:** *"This gathering was cancelled."*
- **Sold-out (per-tier):** *"Sold out"* (neutral, not coral; price suppressed).
- **"N left" (per-tier, true 1..10 only):** *"8 left"* — the compact per-TierRow
  realization of §6.8's *"8 tickets left"*; the tier context already supplies
  "tickets", so the tighter fact is correct here. Same fact, no push.
- **Empty tiers (no tiers returned):** *"Tickets aren't listed yet."* (new line —
  §6 had none; honest, present-tense, promises nothing.)
- **Offline / timeout:** a calm line + **[Retry]**, no auto-retry (a person on
  3G decides when to spend data).

*Two §6 refinements F007 makes explicit (not deviations):*
- §6.7 spec'd sold-out as an **all-tiers** waitlist posture; F007 does honest
  **per-tier** sold-out (price hidden, "Sold out"). The all-tiers Notify/waitlist
  posture is still deferred (no backend, and no CTA ships yet anyway).
- §6.8's *"N tickets left"* was written for the single-price hero; the reader
  renders it per-TierRow as **"N left"** (above).

*DEFERRED to the next session (the write half):* the **Buy/claim CTA** and
inline **tier SELECTION** from §6.3 are NOT built. No **[Get ticket]** /
**[Count me in]** button ships yet, and TierRows are **display-only** (no
selection). F007 is the reader; the 3-tap claim/buy (proof #3) is next. Until
then Detail answers *"do I go?"* but does not yet carry the *"go"*.

**BUILT (F008) — the write half (FREE).** F008 gives Detail the *"go"*: inline
tier **selection** + the **FREE claim** end-to-end. The §6 vision above stands
unchanged; this records what is now live. All client-only, zero SQL — it wires
the existing unmodified `claim_free_ticket` (S028) + `register_person` (F003)
RPCs.

*Live now:*
- **Tier selection (§6.3):** single-tier **preselects** (the primary IS the
  tier — no needless tap). Multi-tier selects by tapping a **TierRow** (1 tap),
  trailing **gold radio** marking the choice. Selection **locks** once a claim
  is in flight/issued. **Sold-out tiers are not selectable** (§6 rule holds).
- **Primary CTA reflects the selected tier** (sticky footer, §6.6):
  a FREE (`price_ugx==0`) tier arms **[Count me in — Free]** (PriceText law —
  never "UGX 0"); a PAID tier shows the §6.7/§7.6 paid-gated **DISABLED**
  **[Get ticket — UGX N]** + the honest line *"Paid tickets open soon — free
  entry works now."* (price shown, never hidden); multi-tier with nothing
  chosen → disabled **[Select a ticket]**; ended/cancelled → **no CTA** (the
  banner already tells the truth).
- **The auth moment (§7.1):** tapping the free CTA with **no Person session**
  raises the lazy **AccountSheet** — a modal bottom sheet **over** Detail (the
  gathering never leaves the background). Attendee skin: header *"One quick
  account."*, sub *"We'll hold your ticket against it."*, consent line
  *"…to hold my ticket."*, create + **"Have an account? Sign in"** swap. On
  success `register_person` provisions the Person and the **armed claim fires
  on sheet success with NO re-tap**; a dismiss leaves the primary **still
  armed** (the tap is never lost). A returning person skips the sheet →
  **1-tap claim**.
- **Claim → confirmation moment:** `claim_free_ticket` → success shown in
  **green** (never coral — a ticket is not a live state), *"You're going."* +
  the `ticket_ref` in **SpaceMono** + **[See my ticket]** → `/tickets`. (The
  rich `/tickets/:id` face is a later session; F008 proves the ticket exists
  and is reachable.)
- **Failure set (each test-backed, §7.2b):** *ZK429* velocity · *ZK410* sold
  out · **ZK409 = a SOFT SUCCESS** (*"You already have a free ticket for this
  gathering."* + [See my ticket] — **never a red error**) · status-not-
  claimable / paid-tier-to-claim / vendor (`23514` → *"That's not something you
  can do right now."*) · offline/timeout (calm, **no auto-retry** — *"We
  couldn't confirm your ticket went through — check Tickets, or try again."* +
  a path to Tickets).

*Honest tap counts:* returning-person free claim = **1 tap**; first-time = the
AccountSheet form + **[Create account]**, then the claim **auto-fires** (no
re-tap). **Zero-login discovery is UNREGRESSED** — Detail still renders fully
signed-out; identity enters only at the primary tap.

*One shared surface, two skins:* the AccountSheet is the account moment the
future **PAID** path will reuse **unchanged**; the **ConsentCheckbox** was
extracted to `lib/core/widgets/` and is now shared by organiser §5D Sign-up and
the attendee sheet — one identity surface, two skins.

*Explicitly NOT built (deferred):* the **PAID path**
(`initiate_ticket_purchase`/Flutterwave), the **rich ticket-detail face**, and
**all-tiers-sold-out waitlist/notify** (degrades to a disabled state — no
backend).

**12. CHANGELOG** *2026-07-10 (FDES-01):* created. *2026-07-10 (FDES-02):*
free-tier arming made explicit — Free pill TierRow, [Count me in — Free]
primary, §7.2b free-claim branch, same lazy AccountSheet auth moment.
*2026-07-16 (F007):* reader half BUILT — route `/g/:gatheringId`, feed tap
now pushes Detail, fact stack + display-only tiers rendered off
`gathering_detail(uuid)` (S029); loading/loaded/not-found/ended/cancelled/
offline states shipped with owned copy (above); "N left" reconciled to the
per-TierRow compact form; empty-tiers line *"Tickets aren't listed yet."*
added; Buy/claim CTA + tier-selection deferred to the write session. Status →
BUILT (F007), read-only.
*2026-07-16 (F008):* the write half (FREE) BUILT — inline tier selection
(single preselects, multi taps a TierRow + gold radio, locks on claim, sold-out
unselectable), tier-aware primary CTA (free arms [Count me in — Free]; paid
disabled + honest line; nothing-chosen → [Select a ticket]; ended/cancelled →
no CTA), lazy AccountSheet auth moment (armed claim auto-fires on sheet success,
no re-tap; dismiss keeps the tap), `claim_free_ticket` → green "You're going." +
ticket_ref + [See my ticket] → /tickets, full §7.2b failure set incl. ZK409 as
a soft success; ConsentCheckbox extracted to shared. Zero-login unregressed.
PAID path, rich ticket face, all-sold waitlist deferred. Status → BUILT (F008).

---

### §7 — Purchase & Pay (the 3-tap proof · MoMo via hosted checkout)

*The money moment. Grounded in the real rails: `initiate_ticket_purchase
(p_gathering_id, p_tier_id)` → `(ticket_id, ticket_ref, amount_ugx, tx_ref)`
→ Edge `initiate-flutterwave-payment` → **Flutterwave HOSTED checkout link**
(`mobilemoneyuganda,card`) → webhook → `confirm_ticket_payment` → the client
learns ISSUED by reading its own ticket (`ticket_select_own`). ZUKA never
touches card/MoMo credentials — the hosted page does (correct posture, keep).*

**1. THE AUTH MOMENT (decision).** Discovery is zero-login;
`initiate_ticket_purchase` is authenticated. **Decision: lazy account — the
soft-wall's write-moment, honoured exactly as §3 already promises.** Tapping
[Get ticket] without a session raises the **AccountSheet** (§5D's fields,
attendee copy) over the Detail screen — the gathering never leaves the
background, the tap is never lost. `register_person` (F003) provisions the
Person inline; sheet dismiss returns to a still-armed [Get ticket].
*Rejected:* sign-in-before-Detail (walls proof 2) and guest checkout (a
ticket must bind to a Person for check-in, transfer, DPPA rights — there is
no honest guest).
**Tap counts (amended 2026-07-10 per RFDES01-F1 founder ruling — every
in-app button on the path enumerated; the count and the flow now agree):**
- **Returning buyer (proof 3): 3 taps, arithmetically true** — ① [Get
  ticket] on Detail → CheckoutHandoff passes as a TRANSIENT beat (zero taps
  — it auto-opens the hosted link; see the component entry) → ② approve in
  Flutterwave checkout (the out-of-app pay moment) → ③ [See my ticket] on
  the confirmed screen. **In-app buttons on the happy path: [Get ticket] ·
  [See my ticket] — two, plus the external approve = 3 taps.** (Multi-tier
  adds the tier tap; single-tier is the pure proof. The launch-failure
  [Continue to payment] fallback is off the happy path by definition.)
- **First-time buyer: 4 taps + one short form** — AccountSheet (form +
  ① [Create account]) → ② [Get ticket] re-tap is NOT required (the armed
  action fires on sheet success) → checkout approve → [See my ticket].
- **Free (scheduled as S028): 1 tap returning** — ① [Count me
  in] → issued → ticket.

**2. FLOW (paid)**
```
[Get ticket] → (no session? AccountSheet → register_person → resume)
→ initiate_ticket_purchase            ← PENDING ticket + tx_ref exists NOW
→ CheckoutHandoff (TRANSIENT beat)    "You'll pay UGX 25,000 with Mobile
                                       Money or card, on Flutterwave."
→ the beat auto-opens the hosted link EXTERNALLY — no tap (system browser /
  custom tab — never an embedded ZUKA-skinned webview: the person must see
  Flutterwave's real URL; anti-phishing posture + smallest client).
  Back during the beat cancels to Detail; a failed launch surfaces the gold
  [Continue to payment] fallback (RFDES01-F1 founder ruling, 2026-07-10)
→ person pays (MoMo push arrives from Flutterwave on this same phone —
  they may leave the browser to approve; both orders work)
→ return to app (redirect deep link AND simple foregrounding both land on:)
→ PendingMeter — poll own ticket every 4s (first 2 min) then 10s
→ status ISSUED → Confirmed moment (success green, NOT coral) →
  [See my ticket] → §8
```
The PENDING ticket is the anchor: if the app dies anywhere past initiate,
reopening finds it (§8 list) and resumes the PendingMeter — nothing is lost,
nothing double-fires (`initiate` is fresh-intent; resume never re-initiates).

**2b. FLOW (free claim · the S028 contract — FDES-02, 2026-07-10)**
*Grounded in the scheduled backend session prompt — the S028 contract, now
promoted to `docs/sessions/NEXT.md` (formerly `NEXT_FREE_TICKET_PATH.md`;
line refs verified against NEXT.md). This spec pins ONLY what S028 pins; the
one open plan-gate item is marked contingent below.*
```
[Count me in — Free] → (no session? AccountSheet → register_person → resume)
→ claim_free_ticket(p_gathering_id, p_tier_id)   (S028 §2, :104)
→ returns (ticket_id, ticket_ref) — the ticket is born ISSUED with a live
  qr_token (:66-68, :124-126). There is NO payment moment: no PENDING, no
  TTL, no CheckoutHandoff, no PendingMeter.
→ Confirmed moment (success green — a ticket is not a live state) →
  [See my ticket] → §8, QR ready immediately.
```
**Tap counts (every in-app button enumerated):** returning — **1 tap**:
① [Count me in — Free] → confirmed ([See my ticket] is the optional second
tap to view the QR; issuance itself took one). First-time — **2 taps + one
short form**: AccountSheet (form + ① [Create account]) → the armed claim
fires on sheet success → confirmed ② [See my ticket].
**Failure states (copy reuses the wrapper's ZK-language; S028 guard order
:107-126):**
- *ZK429 velocity* (checked first, :108-109): wrapper copy, Detail stays.
- *Paid tier sent to claim* (the price-space partition, :112-117 — free and
  paid exactly partition `price_ugx`): honest inverse copy — *"This is a
  paid ticket — use Get ticket instead."* The client should never trigger
  this (the button arms by tier price); the state exists because the server
  is the authority.
- *Vendor tier* (:118-119, `check_violation`): never presented (S026 read
  isolation) — generic wrapper copy if it ever fires.
- *Sold out / capacity full* (:120-122, `check_violation` today): *"That's
  not something you can do right now."* — blunt; **ZK410 waitlist-honest
  copy is S029's cross-cutting swap** (§7.7 row). NULL capacity = unlimited,
  never sold out.
- *Already claimed (dedupe — RULED per-gathering, S028, 2026-07-11):* the
  guard is **one free ticket per (Person, Gathering)**, keyed on the immutable
  `original_purchaser_id`; a repeat claim raises **`ZK409`**. Copy: *"You
  already have your ticket — it's in Tickets."* + [Show my ticket]. (Per-tier
  was rejected: a person needs one free entry, not one-per-tier.) The
  `ZK409` → this-copy client mapping in `ZukaRpcClient` lands with S029's
  cross-cutting client-contract change (batched with ZK410), not S028.
- *Offline / timeout:* calm FormBanner, tap preserved (§5 grammar).
**Language law (S028 severance, :159-160):** this is a **free ticket claim**.
The UI never says "RSVP"; **"Going" (§3) remains an attendance signal and
never issues a ticket** — a person can be Going without a ticket and hold a
ticket without tapping Going. The two are severed by design.

**3. STATES & FAILURE SET (each with its copy — reuse the wrapper's
ZK-language, never parallel copy)**
- *ZK429 velocity:* wrapper copy (*"You're going a bit fast…"*), Detail
  stays; retry allowed after a beat.
- *Sold out between Detail and pay* (`check_violation` today): wrapper copy
  *"That's not something you can do right now."* — **honest but blunt; see
  Open Q: a distinct code would let us say "Sold out while you decided —
  join the waitlist."**
- *Offline / 10s timeout at initiate:* calm FormBanner (§5 grammar), tap
  preserved.
- *Checkout abandoned* (returned, still PENDING, no payment): PendingMeter
  offers [Try payment again] → re-opens the SAME hosted link (same tx_ref)
  and [Let it go] → honest release note (*"This hold expires on its own —
  you won't be charged."*).
- *TTL pressure:* PENDING holds expire at **30 min** (backend default). At
  T-5 min the PendingMeter says so, plainly: *"This hold expires in a few
  minutes. Paid already? We're still confirming — don't pay twice."* **No
  countdown clock.**
- *EXPIRED mid-payment (the R009-F2 case — money left the phone, sweep
  expired the hold):* the poll lands on EXPIRED. Screen says the truth:
  *"Your hold expired before we could confirm the payment. If money left
  your phone, it is NOT lost — we're reconciling it. Keep your MoMo SMS;
  we'll sort it from our side."* + a persistent SUPPORT ribbon on §8. Never
  "payment failed" (it may have succeeded), never silent. **Backend Open Q:
  TTL 30 min vs real MoMo confirm latency (~90 min worst) is a live founder
  ruling — design survives either, but longer TTL shrinks this state.**
- *Payment declined/failed at Flutterwave:* PendingMeter poll stays PENDING;
  the [I've paid — check now] poke + eventual TTL closes it; copy offers
  [Try payment again].

**4. WHAT §7 NEVER DOES** Render a price the server didn't return · retry
`initiate` automatically (fresh intent = fresh tap) · embed the checkout in
ZUKA chrome · show urgency theater · touch vendor tiers (S026 wrapper
rejects them; the client never even sees them, §6).

**5. COLOUR LOGIC** Gold for every action; **success green** for the
confirmed moment (a ticket is not a live state — coral stays reserved);
error red only on EXPIRED/declined.

**6. PAID-GATING POSTURE (until ADR-026's counsel + Vol 14 Ch 13 onboarding
clear):** paid tiers render with true prices, primary disabled, one honest
line: *"Paid tickets open soon — free entry works now."* Free path fully
live. **Founder ruling logged; recommended over hiding (§6.7).**

**7. OPEN QUESTIONS (backend/build dependencies — several BLOCK the build)**
- **FREE-CLAIM PATH DOES NOT EXIST YET (blocker, launch-critical —
  scheduled as S028; heading renamed per RFDES02-C1: a free claim is never
  "RSVP", §7.2b severance):**
  `initiate_ticket_purchase` hard-rejects zero-price tiers ("use comp/staff
  issuance") and `issue_ticket` is deprecated — yet the ADR-026-gated launch
  is free-first. Ask: `claim_free_ticket(p_gathering_id, p_tier_id)` —
  authenticated, rate-limited like initiate, capacity-checked, ISSUED
  immediately, emits the registered events. **Blocks M0.**
- **Opening the hosted link needs `url_launcher` — a NEW dependency → ADR
  required before the build session (pubspec is constitutionally bounded).**
- **Redirect return:** `FLW_CHECKOUT_REDIRECT_URL` must point at a ZUKA deep
  link (`zuka://payment-return?tx_ref=…`) + Android App Link config.
  Dependency; foreground-resume polling makes it non-fatal if late.
- **Ticket polling read:** today = a direct `ticket_select_own` table read —
  the second non-RPC read after `person_select_own`. Recommend a
  `my_tickets()` RPC (API law 10) serving both the poll and §8's list;
  sanction the direct read only as an interim.
- **Distinct sold-out error code** (ZK-class) so the wrapper can say
  waitlist-honest copy (API law 6).
- **TTL ruling:** founder decision, 30 → 60–90 min recommended (R009-F2).

**8. CHANGELOG** *2026-07-10 (FDES-01):* created — auth-moment decision
(lazy AccountSheet), hosted-checkout handoff grammar, full failure set incl.
EXPIRED-mid-payment honesty, paid-gating degrade, free-path gap surfaced.
*2026-07-10 (FDES-02, RFDES01-F1 founder ruling):* CheckoutHandoff made a
transient auto-advance beat (back-cancel during the beat; [Continue to
payment] only as launch-failure fallback) — returning-buyer 3 taps is now
arithmetically true; tap counts restated with every in-app button enumerated.
*2026-07-10 (FDES-02):* §7.2b free-claim branch added against the S028
contract (born-ISSUED, no payment moment; full failure set incl. the
contingent dedupe copy; Going ≠ ticket severance stated).
*2026-07-16 (F008):* §7.1 auth moment + §7.2b free-claim flow + §7.6 paid-
gating REALISED on Detail (the build record lives in §6's "BUILT (F008)"
block). Live: lazy AccountSheet (shared, attendee skin; armed claim auto-fires
on sheet success, dismiss keeps the tap), `claim_free_ticket` → green "You're
going." + ticket_ref + [See my ticket], the full failure set with **ZK409
resolved to a soft success** ("You already have a free ticket…" + [See my
ticket], never red), paid tiers render true price with a disabled CTA + the
honest line. STILL DEFERRED: the whole PAID rail (§7.2 — `initiate_ticket_
purchase`, CheckoutHandoff, PendingMeter, url_launcher/ADR, redirect deep link)
and all-tiers-sold waitlist. Free path fully live end-to-end.
*2026-07-18 (A-01):* visual pass added (§7.9); no §7.1–7.7 semantics changed.

**9. VISUAL PASS (A-01 · 2026-07-18 · REVIEWED — founder-approved 2026-07-18)**
Mockup: `mockups/a-01-purchase-pay/purchase-pay.html` — the PAID rail in 7
frames (AccountSheet attendee skin · CheckoutHandoff transient beat ·
launch-failure fallback · PendingMeter · abandoned/TTL-pressure · Confirmed
moment · failure-set strip), 360px, per the A-00 foundation. **Design-ahead:**
the paid rail's build remains blocked on §7.7 (url_launcher ADR, redirect
deep link) — this pass gives the build session its visual truth, it unblocks
nothing by itself.
- **Ember Edge applied (A-00 binding):** gold spine while money is in flight
  (handoff, pending, abandoned), success spine on the Confirmed moment —
  the spine tracks the money moment's state, coral never appears.
- **Tap arithmetic rendered on-sheet** (RFDES01-F1 enumeration verbatim):
  ① Get ticket → beat (zero taps) → ② approve on Flutterwave → ③ See my
  ticket; AccountSheet adds one form, no re-tap.
- **Money honesty:** the only price shown is `amount_ugx` from
  `initiate_ticket_purchase`, rendered `UGX 25,000` (PriceText); trust line
  names Flutterwave ("their page, their padlock"); fallback copy states ZUKA
  never asks for MoMo/card details in-app.
- **Motion (per the ratified A-02 adjudication):** the handoff beat
  (~1.5s determinate strip) and the PendingMeter drift are the only motion;
  amounts and buttons static; everything freezes under reduce-motion.
- **No dark patterns verified frame-by-frame:** no countdown clock (TTL
  honesty as words at T-5), abandoned path offers [Let it go] with
  "you won't be charged", double-payment protection stated plainly
  ("don't pay twice"), EXPIRED copy never says "payment failed".
- On founder approval: A-01 → REVIEWED; visual truth for the eventual paid
  rail build session (post url_launcher ADR + S029 client-contract batch).

---

### §8 — My Ticket · My Tickets (the QR is the product)

**1. PURPOSE** The thing they paid for: a QR that gets them through a gate on
a bad-network night, and the honest state of every ticket they hold.

**2. ENTRY POINTS** [See my ticket] after purchase/claim · the **Tickets tab**
(§9 — *amended 2026-07-10, FDES-02 founder ruling:* promoted from a quiet
chrome affordance to a **quiet permanent tab**; the tab stays neutral and
un-badged until a true state exists — gold badge on PENDING, coral only while
the ticket's gathering is live NOW. UX reason: on gate night the QR must be
one thumb-tap away, and the retained tab spares a 3G feed re-fetch on return.
The original "appears only when a ticket exists" wording is superseded; the
*emphasis*-only-on-true-state spirit is preserved) · push (payment confirmed)
· deep link.

**3. STRUCTURE** *List:* upcoming first (tonight pinned), each row = gathering
name, start, TicketStatusBanner state; PENDING rows resume the PendingMeter
on tap. *Ticket:* QRCard full-screen-ish, TicketStatusBanner, gathering
facts + the gathering's own live pill (coral iff live), [Directions] (map
seam), Share = the gift seam — **note only** (transfer engine unbuilt;
no give/refund/resell UI this pass).

**4. STATES**
- *ISSUED:* QRCard live token, brightness raised while visible.
- *PENDING:* no QR — PendingMeter resume (§7).
- *CHECKED_IN:* token is NULL (backend rotation) → success state, *"You're
  in. Enjoy the night."* — never a dead QR render.
- *EXPIRED:* error banner + the §7 reconcile copy + support ribbon.
- *OFFLINE (the state that matters — the gate has no signal):* QRCard renders
  the **cached** token + stale ribbon (*"Saved 3h ago"*). **Cache posture
  (ADR-021-consistent, stated explicitly):** the ticket display is cacheable
  — `check_in_ticket` (scanner side) is the NO-CACHE surface and the ONLY
  admission authority; a stale displayed token simply fails the live scan and
  the gate re-syncs. Residual risk (stale token shown after an off-device
  state change) → **logged for zuka-security review below.**
- *2GB/3G:* QR is locally generated from the token string — zero images, zero
  network to render.

**5. COPY** *"Show this at the gate."* · ticket_ref as the spoken fallback:
*"or give them this code: ZKA-…"* No screenshot scolding — token rotation is
the control, not user shame.

**6. COLOUR LOGIC** Banner per TicketStatusBanner. Coral only as the
gathering's live pill. QR always max-contrast on white — readability beats
brand (scanners at night).

**7. OPEN QUESTIONS** `my_tickets()` RPC (shared with §7) · offline-QR
stale-token window → zuka-security sign-off · QR local-generation package
(pure-Dart QR painter — likely a new dep → same ADR as `url_launcher`) ·
push on ISSUED (FCM wiring — M0 item, not this build).

**8. CHANGELOG** *2026-07-10 (FDES-01):* created. *2026-07-10 (FDES-02):*
§8.2 entry points amended — Tickets promoted to a quiet permanent tab per the
§9 Two Doors founder ruling (badge-only emphasis; deviation logged in place).
*2026-07-18 (A-02):* visual pass added (§8.9); no §8.1–8.7 semantics changed.

**9. VISUAL PASS (A-02 · 2026-07-18 · REVIEWED — founder-approved 2026-07-18;
meter adjudication ratified: PendingMeter drift + QR settle stay, amounts and
buttons static)**
Mockup: `mockups/a-02-my-ticket-qr/my-ticket.html` — the single-ticket screen
in 7 states (ISSUED · ISSUED+live pill · PENDING/PendingMeter · CHECKED_IN ·
EXPIRED · OFFLINE cached-QR · loading/error), 360px frame, per A-00 foundation.
- **Ember Edge applied (A-00 binding):** the 6px spine carries the *ticket
  state* colour (PENDING gold · ISSUED/CHECKED_IN success · EXPIRED error ·
  neutral ink2 while loading/error). Coral appears only as the gathering's
  live pill — the spine never wears coral (a ticket state is not a live state).
- **Data honesty:** every rendered field maps to a `my_tickets()` column
  (ticket_ref, status, qr_token, gathering_title, venue_name, starts_at,
  ends_at, is_live). **Tier name and price are NOT in the RPC** — not shown,
  logged in §8.7 (do we want them on the ticket? needs an RPC column, not
  invented copy).
- **Motion adjudication (recorded):** the money/trust never-animate rule is
  read as *amounts, prices, and action buttons are static*; the PendingMeter's
  calm indeterminate drift is the state's own information signal (prior §7
  spec) and the QR settle (~400ms, T2 per the motion table) marks the
  admission artifact *arriving*. Both freeze under reduce-motion. If the
  founder wants PENDING fully static, the meter drops to a static bar +
  elapsed words with no other change.
- **Loading keeps the QR's footprint** (240px square skeleton) — no layout
  jump at the gate; error state re-shows a cached QR when one exists, never a
  blank gate screen.
- Copy verbatim from §8.5 ("Show this at the gate.", spoken-code fallback,
  no screenshot scolding); EXPIRED carries the §7 reconcile promise ("if money
  left your account, it reconciles — keep the MoMo SMS") + WhatsApp support
  ribbon with the ticket code.
- On founder approval: A-02 → REVIEWED; QR-painter dependency ADR and the
  tier/price RPC question go to the backend track.

**10. VISUAL PASS — LIST (A-03 · 2026-07-18 · REVIEWED — founder-approved
2026-07-18; coral-wins badge priority ratified)** Mockup:
`mockups/a-03-my-tickets-list/my-tickets-list.html` — four
frames (default grouped list · tab-chrome badge trio · empty · offline/
loading/error), 360px, per A-00/A-02.
- **Grouping rendered:** TONIGHT pinned → UPCOMING → EARLIER (dimmed, 50%
  opacity — history present but quiet). Rows are Ember Edge compact: title +
  start on the first line, venue + state chip on the second; spine = ticket
  state; the gathering's live pill is the only coral.
- **Tab chrome (§8.2 ruling) visualised as a trio:** neutral un-badged ·
  gold dot (a PENDING exists) · coral dot (a held ticket's gathering is live
  NOW). **Priority rule minted: coral wins when both are true** — the night
  beats the paperwork; gold is one tap away inside. No counts on the badge,
  ever.
- **Empty state** gives direction with real numbers ("12 gatherings are on
  across Kampala tonight") + [See what's on] back to Pulse — supersedes the
  built M0 placeholder empty state when this ships.
- **Offline:** cached list + as-of time + "every saved QR still works at the
  gate"; error keeps showing the last list ("your saved tickets are safe") —
  the list never blanks over a fetch failure.
- Data: every row field maps to `my_tickets()`; PENDING rows resume the
  PendingMeter on tap per §8.3 (no new navigation invented).
- On founder approval: A-03 → REVIEWED (incl. the coral-wins badge priority
  rule); P1 catalogue design side complete.

---

### §9 — App Shell & Navigation ("Two Doors")

*The connective tissue. Founder ruling 2026-07-10 (FDES-02): Option B of the
shell brainstorm — two tabs, everything else stays quiet chrome or a pushed
route. Discovery remains the sole front door; the paid product gets a
thumb-reachable home for gate night. FDES-02, 2026-07-10.*

**1. PURPOSE** Give every DRAFTED surface (§1–§8) one navigational skeleton —
and reserve seats for what comes after M0 — without adding a single
dependency (ADR-028: go_router's `StatefulShellRoute.indexedStack` + Riverpod
already adopted; nothing new).

**2. SHELL STRUCTURE**
```
┌─────────────────────────────┐
│ [Hosting?]   ZUKA   [ ⋯ ]   │ ← top chrome: Hosting (§5.2, unchanged)
│  47 events live in Kampala  │   ⋯ overflow → Saved · Following · Account
│  … one feed, lenses (§1) …  │
├─────────────────────────────┤
│   ◉ Pulse       ○ Tickets   │ ← the only two tabs
└─────────────────────────────┘
```
Two `StatefulShellRoute` branches: **Pulse** (`/`, §1) and **Tickets**
(`/tickets`, §8). `indexedStack` retains both — returning to Pulse costs no
re-fetch (on 3G, data is the scarcer resource; two text-first lists are cheap
RAM). Tab selection renders in **neutral ink** (`ink5` selected / `ink3` dim)
— information colours are never tab decoration.

**3. TICKETS-TAB BADGE LAW** The tab is permanent but *quiet*: neutral,
un-badged, until a **true state** earns emphasis — **gold** dot while a
PENDING payment exists · **coral** only while a held ticket's gathering is
live NOW (a real live state, the only legal coral in chrome). Never a count,
never an engagement lure (Vol 16).

**4. ZERO-LOGIN POSTURE (per slot)** Pulse: full browse, no wall (Proof 2).
Tickets (anon): renders a direction-giving empty state — *"No tickets yet —
find something for tonight."* [Back to Pulse] — viewing is never walled; the
wall stays at the write moment (§3/§7 AccountSheet). ⋯ sheet: opens for
everyone; Saved/Following show §4's soft-wall empties. Hosting: §5A/§5D auth
(creating is not browsing). *DPPA guardrail (2026-07-10, zuka-legal):* the
anon browse path collects no personal data — §0 contract 6's "device-
aggregate" events must stay **non-re-identifying** (a persistent per-device
identifier would turn anonymous browsing into personal-data processing and
undercut the posture Proof 2 rests on).

**5. ROUTE MAP (go_router)**
```
/                    City Pulse (§1) — branch 0
/tickets             My Tickets list (§8) — branch 1
/tickets/:ticketId   one ticket, STATE-driven: PENDING → PendingMeter ·
                     ISSUED → QRCard · CHECKED_IN/EXPIRED per §8.4
/g/:id               Gathering Detail (§6), pushed over its opener.
                     AccountSheet (modal) · CheckoutHandoff (transient beat) ·
                     purchase states live ON this stack — purchase is never
                     a tab, never a destination.
/host                Organiser §5 (unchanged today; joins the shell chrome-
                     -less — it is a mode, not a tab)
/scan                RESERVED (FDES-03) — top-level, OUTSIDE the person
                     shell: the scanner backend admits PIN-only gate
                     operators (S012/S013), so the shell must not force it
                     behind person auth. FDES-03 decides; zero spec here.
Deep links:          zuka://g/<id> · zuka://host ·
                     zuka://payment-return?tx_ref=… → resolve tx_ref →
                     /tickets/:ticketId in PENDING state (= the PendingMeter,
                     §7); late/foreground-resume lands the same place.
RESERVED (named stubs, zero spec, no shell rework to add):
  /inbox             Communication OS (announcements/bulletins)
  /gift/:token       gift-ticket landing (claim over Pulse)
  /g/:id/vendors     in-event vendor marketplace (off the Detail stack)
```

**6. BACK-STACK RULES** Cold deep link to `/g/:id` or `/tickets/:id` seeds
Pulse beneath — Back always → Pulse, never app-exit. CheckoutHandoff: Back
during the beat cancels to Detail (§7.2; hold releases on TTL). `/scan` is
its own stack — Back exits the scan session, never falls into person nav.
`zuka://payment-return` seeds under the Tickets branch → Back → Tickets.

**7. AFRICA-FIRST BUDGET** Only the two branch roots are retained; Detail /
host / scan / purchase screens are lazy pushed routes, built on demand,
disposed on pop. The Map lens (§2) stays a lens inside Pulse — Mapbox tiles
load only while engaged and dispose on exit. Shell chrome is text + two
glyphs: no imagery, no measurable cost on a 2GB device.

**8. COPY** Tab labels: **Pulse · Tickets** (UI may say "Tickets"; semantics
stay Gathering/ticket per the dictionary). ⋯ sheet rows: *Saved · Following ·
Account*. Anon Tickets empty per §9.4.

**9. UX RULES** Discovery is ONE feed — no option ever splits it (§1 ruling,
pinned). Saved/Following never gain first-open prominence (§4 law — the ⋯
placement is the enforcement). No third tab without a founder ruling +
deviation note (the RESERVED surfaces hang off routes precisely so the bar
never needs to change). Badges only for true state — never engagement bait.

**10. OPEN QUESTIONS** None new — §9 consumes existing seams only. (The
Tickets badge needs `my_tickets()` (§7.7) to know PENDING/tonight state
cheaply; already logged.)

**11. CHANGELOG** *2026-07-10 (FDES-02):* created from the shell brainstorm —
founder ruled Option B "Two Doors" over A "Single Surface" (§8.2-literal,
lightest RAM, worse gate-night reach + 3G re-fetch) and C "You-anchored
Three" (strained §4's never-first-open law). §8.2 amended in place (the one
logged deviation); Bible Deviations Register untouched — this deviates from a
living-doc section, not the Bible. *2026-07-13 (F005 · BUILT):* the shell
shipped exactly as specced — `StatefulShellRoute.indexedStack` with two
branches (`/` Pulse · `/tickets`), `/tickets/:ticketId` registered as an
honest placeholder, the ⋯ sheet (Saved · Following · Account) over §4.7/§5A
surfaces, and §9.6 back-stack (PopScope → Pulse, never app-exit). Zero new
dependencies. Tickets-tab badge left a quiet comment-only seam (awaits
`my_tickets()`/S029). Saved/Following/Account are reached imperatively and
kept OFF the route map per §9.5 (RF005 nav ruling — a conscious accept within
ADR-028, gates PASS). Gates: zuka-reviewer PASS · zuka-tester PASS
(mutation-proven) · zuka-reality-checker CLEAN. Review owed: RF005. (Session
renumbered F004→F005 at the close gate: F004 was already held by the
unfinalized "discovery-cheap-wins" draft + its RF004 review — founder ruling
2026-07-13.)

---

### §10 — Scanner (M0 check-in)

*The gate on a bad-network night. Grounded in the built rails: scanner
sessions + PIN (S006, `20260630170000_scanner_session.sql`), the offline
manifest + sync (S012, `20260703100001/100002_…`), the PIN throttle contract
(S013, `20260704100000/100002_…`), and `check_in_ticket` (NO-CACHE admission
authority, ADR-021/ADR-023). Founder rulings 2026-07-10 (FDES-03): entry =
"two doors, one PIN gate"; result grammar = five states. M0 scope = one
gate-operator role — staff hierarchy/delegation/Pro tiers are OUT
(`CONCEPT_Scanner_Pro_Capability_Model.md` is Tier-2 concept space, fenced).*

**1. PURPOSE** Admit real tickets fast, in the dark, with or without signal —
and tell the operator the truth about every scan in one glance.

**2. THE TWO DOORS (founder ruling — every path lands on the same PIN gate)**
- *Organiser door:* after go-live, the organiser's gathering surface (§5)
  gains a quiet **[Set up gate]** → creates the scanner session
  (`create_scanner_session(p_gathering_id, p_pin)` — authenticated organiser
  only, PIN is exactly 6 digits, bcrypt-hashed, never stored plain:
  `20260630170000:88-186`, cost raised `20260704100002:56-61`) → a
  **hand-off screen** shows the session code + the PIN the organiser chose.
- *Operator door:* `/scan` — top-level, its OWN stack, outside the person
  shell (§9.5): any phone, **no ZUKA account** (`download_scanner_manifest`
  + `sync_offline_scans` are GRANTed to anon: `20260703100002:223-224,
  441-442`; Vol 14 Ch 8 — "no Zuka account required for the physical
  scanner"). Cold entry = deep link or a quiet ⋯ row ("Gate scanner") →
  session-code + PIN form.
- Organiser auth never skips the PIN — the session PIN is the security
  control, not the account (contextual-blind, Vol 14 Ch 6).
- **[Set up gate] generates the manifest at setup** (session + manifest in
  one flow) so an active session always has a list — otherwise a correct
  PIN on a manifest-less session returns empty and the operator hits the
  wrong-PIN copy on a bad-network night (security-gate LOW; the copy must
  stay neutral, so the flow carries the fix).

**3. THE HAND-OFF SCREEN (a security surface, treated as one)** Shows the
session code (share affordance) and the PIN **once, on explicit reveal**
([Show PIN] hold-to-reveal; auto-hides). Copy: *"Give this code and PIN to
whoever runs your gate. Anyone with both can scan tickets for this gathering
— share them like keys."* **Revocation (security gate, 2026-07-10; RPC
landed S070 2026-07-20):** a shared code+PIN CAN now be revoked mid-event —
`deactivate_scanner_session(p_session_id)` (operator-only, auth.uid()-bound,
`20260720120000`) flips the session inactive, after which its PIN admits
nothing on any path (`check_in_ticket` / `download_scanner_manifest` /
`sync_offline_scans` all resolve only on `is_active = true`). PIN rotation is
deactivate-then-recreate with a fresh PIN — no separate rotate RPC (the
active-PIN-uniqueness check ignores the retired session). So the hand-off is
no longer an un-revocable key; the plain line to the organiser becomes
(*"Share these like keys — and if they leak, close the gate to cut them
off."*). This RPC is the leak-response control the security gate named. The
hand-off screen and the operator PIN-entry screen are screenshot-blocked
(`FLAG_SECURE`) — for the PIN, the controls are the throttle + revocation
(now real), NOT token rotation (rotation protects ticket QRs, not PINs).
PIN delivery is manual at M0 — the SMS-PIN delivery of Vol 14 Ch 8 is a
backend seam, not built (Open Questions).

**4. SESSION ENTRY + THE THROTTLE CONTRACT (S013, founder-confirmed — the
UX must be honest without aiding brute force):** code + PIN form → on
submit, `download_scanner_manifest(p_session_id, p_operator_pin)`.
- *Wrong PIN:* the RPC returns **empty, no error raised** (S013 contract,
  `20260704100002:13-24`) → UI shows *"That code and PIN don't match an
  active gate."* — one neutral message for wrong-PIN / unknown-session /
  expired-session. **Never reveal which part failed, never show remaining
  attempts.**
- *Locked (ZK423):* 5 failures / 10 min on this endpoint, assert-before-
  crypt — while locked even the right PIN is refused
  (`20260704100002:34-40,49-52`) → *"Too many tries. This gate is paused
  for a few minutes — it unlocks on its own."* (auto-clear, ADR-014 — no
  human unlock to design).
- *Success:* manifest caches locally; scanning starts.

**5. MANIFEST DOWNLOAD (the offline authority, sized for 3G)** One call
returns the Bloom pre-filter + the exact `ticket_hashes[]` authority set +
`capacity_snapshot`, `admitted_count`, `generated_at`, `valid_until`
(default 24 h — `20260703100002:177-221,128-150`). Text-only payload
(hashes, no PII beyond ticket membership). UI states: downloading (size +
progress on 3G) · ready (*"Gate list saved — 214 tickets. Works with no
signal."*) · **ageing** (banner past a freshness threshold: *"List is 3 h
old — refresh when you have signal"*) · **expired** (`valid_until` passed:
scanning stops accepting offline; online scans still work). The organiser
can regenerate (`generate_scanner_manifest`, organiser-only upsert
`20260703100002:25-172`); the scanner re-downloads on demand — no silent
auto-refresh over the gate's own data budget. **At-rest posture (security
gate):** the cached manifest and the offline queue live in platform-
encrypted storage (Keystore/Keychain-backed) — never plaintext prefs or a
world-readable file; the manifest is the full valid-ticket index and the
queue holds raw tokens, so a lost gate phone must leak neither.

**6. SCAN SCREEN (operator-first: big, glanceable, night-legible)** Camera
full-screen, torch toggle, one result card per scan — colour + one line +
`ticket_ref` in SpaceMono. **The five states (founder ruling):**
| State | When (online) | When (offline) | Colour · copy |
|---|---|---|---|
| **VALID — admit** | `check_in_ticket` succeeds (token burns: `qr_token` → NULL + fp, `20260703100000:23-27`) | hash ∈ `ticket_hashes[]` and not locally seen → queue | **success green** · *"Go ahead. ZKA-…"* |
| **ALREADY CHECKED IN** | **effectively unreachable online (RFDES03-F1):** check-in NULLs the token, so an online re-scan of a used ticket returns `no_data_found` → lands in NOT-IN-LIST. The `23505` guard ("AMBER + escalate", `20260630170001:65-66`) fires only in a same-moment race. Backend gap logged: an online `redeemed_token_fp` lookup (as sync already does, `20260703100002:394-412`) would restore this state online. | locally-seen duplicate; on sync: `ALREADY_IN`/`DUPLICATE` (`20260703100002:386-412`) | **warning amber** · *"Already in. If that wasn't them, call the organiser."* (no timestamp — the backend returns none on either already-in path, RFDES03-F5; a local-queue duplicate may show its own local scan time) (ADR-014: the system flags, the human acts) |
| **NOT IN THIS GATE'S LIST** | `no_data_found` (`20260630170001:59-60`) — an unknown token **or an already-used one**, indistinguishable online today (RFDES03-F1) | hash ∉ manifest | **error red** · *"Not on this gate's list."* + the honest next lines: if they say they already used it, their own ticket screen shows "You're in" (§8.4 CHECKED_IN) — the attendee's phone is the disambiguator; otherwise see stale, below |
| **STALE LIST / STALE TICKET** | `check_violation` (`20260630170001:71-79`) — non-ISSUED status or an expired token: the ticket's state changed since issue (RFDES03-F2) | miss + manifest ageing/expired | **warning amber** · online: *"This ticket isn't valid any more — expired or cancelled."* · offline: *"The gate list is 3 h old — their ticket may be newer. Ask them to open their ticket fresh, or refresh the list."* (§8's cached-QR posture agreed: a stale displayed token simply fails the live scan and re-syncs — §8.4) |
| **OFFLINE-ACCEPTED — pending sync** | n/a | the queued VALID | **success green + quiet sync chip** · *"In — will confirm when you're back online."* |
NOT-IN-LIST never accuses (*no "fake ticket"* — it may be a just-issued
ticket the manifest predates); the stale line is the honest fork. **Coral
never appears on a scan result** — coral is a live *gathering* state, not a
scan verdict (Vol 25 Ch 4).

**7. OFFLINE QUEUE + SYNC** Queued admissions persist locally
(scan-log shape mirrors `offline_scan_log`); a quiet counter chip shows
*"4 waiting to sync."* On reconnect: `sync_offline_scans` batches, and the
UI reconciles each row by the server's own outcome enum — `CONFIRMED`
(with `capacity_overrun` **surfaced, never blocked** — the banner says
*"Over the venue count by 6 — worth a look,"* it never locks the gate:
`20260703100002:281-350`, ADR-014) · `ALREADY_IN`/`DUPLICATE` (revealed
double — amber, listed for the organiser) · `INVALID_STATE` (expired —
or refunded/cancelled — **after the list was made**: admitted offline,
flagged on sync, `:325-330`; forward-note: when refunds ever land, a
refunded ticket can still admit offline until the organiser regenerates
the manifest — a control the refund builder must own) ·
`TOKEN_NOT_FOUND`. Clock honesty: `scanned_at`
is recorded but the server clamps bad client timestamps into
`[starts_at, now()]` (in-force sync body: `20260704100002:459-473` —
re-cited per RFDES03-F3; the degrade-to-now EXCEPTION fallback is
`20260703100002:291-298`) — client clocks never decide admission
(capsule trap 10).
Sync failures keep the queue; nothing is dropped silently.

**8. CHECK-IN THROTTLE (online path — different knob, by design)**
`check_in_ticket` is CRYPT-FIRST: a correct PIN is never throttle-gated
(10 / 10 min keyed on the shared `gathering_id` — one bad phone must not
lock every gate at the venue: `20260704100002:41-52`). The operator UX is
identical to §10.4's lockout copy if ZK423 ever fires; mistypes get the
neutral mismatch line.

**9. 2GB/3G BUDGET** The camera is the only heavy surface. The manifest is
text (hashes); no imagery anywhere; result cards are colour + type. QR
decode runs on-device (the pure-Dart decode/painter dep is the same
ADR-030 candidate as §8's — Open Questions). Cache posture stated exactly:
**`check_in_ticket` is NO-CACHE (ADR-021, in `kNoCacheRpcs`) — the ONLY
admission authority; the manifest is the sanctioned offline exception**
(ADR-023's whole point), reconciled by sync.

**10. UX RULES** One result at a time, full-attention (no scan-while-
showing). Torch + brightness controls one tap. Every reject shows
`ticket_ref` entry as the fallback (type the code a person reads out —
mirrors §8.5). No counts of remaining attempts, ever. No admit/deny sounds
that shame (a quiet distinct tone pair is fine; the screen is the verdict).
**Response hygiene (security gate, Identity-Graph MED):** the client treats
the `check_in_ticket` response as write-once-to-screen — **`person_id` is
never logged, persisted, or written to the offline queue/scan-log**; only
`ticket_ref` + the verdict are retained (a gate phone must never accumulate
a scan→Person presence map; backend follow-up logged to drop `person_id`
from the anon-facing return entirely).
Session exit = [Close this gate] → confirms, clears the local manifest +
queue only after a successful final sync (or an explicit *"discard 4
unsynced admissions"* double-confirm — never silent loss). **[Close this
gate] is specified to call `deactivate_scanner_session`** (S070,
`20260720120000`) — closing the gate revokes the session server-side, not
just on this device, so the code+PIN stop working on every ONLINE path at
once (previously it was device-local only and the server session stayed
downloadable until `expires_at`). *Honesty note (security gate MED):* a
leaked device that already cached an offline manifest can still show local
VALID at the gate until it reconnects — but `sync_offline_scans` then rejects
those scans (it resolves on `is_active`, test [4c]), so the ledger is never
corrupted; the physical door is only closed instantly for online scanners.
Bounding a cached manifest's offline lifetime (short `expires_at` / heartbeat)
is a manifest-layer follow-up, not this RPC. *Build note: wiring the button
to the RPC is the remaining client-side task — owed RF070.*

**11. OPEN QUESTIONS (backend/build dependencies)**
- **Camera + QR-decode package** — new dependency → fold into the ADR-030
  candidate (`url_launcher` + QR painter, §7.7/§8.7). **Blocks §10 build.**
- **Session deactivate/PIN-rotate RPC — RESOLVED (S070, 2026-07-20):**
  `deactivate_scanner_session(p_session_id)`
  (`20260720120000_fn_deactivate_scanner_session.sql` — operator-only,
  auth.uid()-bound, `FOR UPDATE`, idempotent, emits `ScannerSessionDeactivated`
  after the durable UPDATE) flips `is_active = false`, so a leaked code+PIN can
  now be revoked mid-event — the NAMED M0 security dependency is closed. PIN
  rotation is delivered by composing deactivate + `create_scanner_session` (no
  separate rotate RPC — the RS061-F2 active-PIN-uniqueness check ignores the
  retired session). §10.3's revocation copy and §10.10's [Close this gate]
  updated accordingly. **Remaining build-side task:** wire the [Close this gate]
  button to call the RPC (owed: RF070).
- **Drop `person_id` from `check_in_ticket`'s anon-facing return** (backend
  follow-up; security gate, Identity-Graph MED) — the spec-side discard
  rule (§10.10) holds the line meanwhile.
- **SMS PIN delivery** (Vol 14 Ch 8): manual hand-off at M0; Africa's
  Talking wiring is a later backend seam.
- **Manifest freshness threshold** for the "ageing" banner (design suggests
  2 h; tune against real gate practice).
- **ZK423 client mapping:** register the lockout copy in the RPC contract's
  error map same-build (`ZUKA_FLUTTER_RPC_CONTRACT.md`).
- **Online used-token detection (RFDES03-F1):** `check_in_ticket` cannot
  distinguish a used token from an unknown one (`no_data_found` for both,
  token NULLed on check-in) — an online `redeemed_token_fp` lookup
  (mirroring sync's branch, `20260703100002:394-412`) would restore the
  amber ALREADY-IN verdict online. Backend item.

**12. VISUAL PASS (A-07 · 2026-07-18 · REVIEWED — founder-approved 2026-07-18;
full-bleed verdict-card fills ratified as the operator-screen exception to
Ember Edge)**
Mockup: `mockups/a-07-scanner-checkin/scanner.html` — six operator-first
frames (organiser hand-off · operator entry incl. neutral-mismatch + ZK423
lockout · manifest lifecycle downloading/ready/ageing/expired · scan screen
with green/amber verdicts + sync chip · red/stale verdicts + typed
ticket_ref fallback · sync reconcile + close-gate double-confirm), 360px,
per the A-00 foundation. Design-ahead: the deactivate RPC dependency is now
resolved (S070, §10.11); build stays blocked only on the camera/QR-decode dep
(ADR-030 candidate).
- **Night-gate type decision:** verdict words render at display size (32px
  Space Grotesk — GO AHEAD · ALREADY IN · NOT ON THIS LIST) so the verdict
  reads at arm's length in the dark; one verdict at a time per §10.10.
- **Expressive-layer variation (logged for founder eyes):** status/progress
  cards carry the Ember Edge spine, but scan-verdict cards use full-bleed
  state-colour fills instead — glanceability at a gate beats card anatomy;
  this is proposed as the operator-screen exception, not a new default.
- **Security grammar rendered exactly:** one neutral mismatch line (never
  which part failed, never attempts left), lockout self-heals in copy,
  hold-to-reveal PIN + "share them like keys" + the un-revocable honesty
  line, verdicts carry ticket_ref only (no person_id on any surface — the
  §10.10 discard rule visualised), coral absent from every verdict.
- **Honest-fork copy on red:** NOT-ON-THIS-LIST never accuses — "their
  ticket may be newer than the list"; the attendee's own CHECKED_IN screen
  (§8.4) is the disambiguator; typed ticket_ref entry offered on every
  reject.
- **Sync truthfulness:** per-row server outcomes rendered (Confirmed ·
  Already in — listed for organiser · State changed after list — flagged);
  capacity overrun surfaced as "worth a look", never blocking (ADR-014);
  close-gate clears only after final sync or an explicit discard
  double-confirm.
- On founder approval: A-07 → REVIEWED (incl. ruling on the full-bleed
  verdict-card exception); visual truth for the §10 build session
  (post ADR-030 + deactivate RPC).

**12. CHANGELOG** *2026-07-10 (FDES-03):* created — two-doors/one-PIN-gate
+ five-state result grammar (founder rulings); throttle UX per the S013
wrong-PIN contract; capacity-overrun surfaced-never-blocked (ADR-014);
manifest ageing honesty; Pro-tier scope fenced out. *Same day:* zuka-security
gate (real subagent, PASS-WITH-NOTES — 0 CRIT/0 HIGH, 3 MED/4 LOW) and all
spec-side fixes applied: person_id write-once-to-screen discard rule ·
revocation honesty (un-revocable key at M0; deactivate RPC = named M0
security dependency) · manifest/queue at-rest encryption · manifest-at-setup
flow fix · FLAG_SECURE on hand-off + PIN entry · INVALID_STATE broadened to
refund/cancel-after-generation.
*2026-07-11 (RFDES03, fixes applied in-review):* online column of the
five-state table corrected against independent re-derivation — amber
ALREADY-IN marked effectively unreachable online (token NULLed; re-scan =
`no_data_found` → NOT-IN-LIST, with the §8 "You're in" disambiguation copy;
online `redeemed_token_fp` lookup logged as the backend restore) ·
`check_violation` given its online home in the STALE/NO-LONGER-VALID row ·
clock-clamp re-cited to the in-force sync body · amber copy drops the
timestamp the backend never returns.
*2026-07-20 (S070, `scanner-session-deactivate-rpc`):* the NAMED M0 security
dependency is CLOSED — `deactivate_scanner_session(p_session_id)`
(`20260720120000`, operator-only, auth.uid()-bound, `FOR UPDATE`, idempotent,
emits `ScannerSessionDeactivated`) lands, so a leaked code+PIN can be revoked
mid-event and its PIN then admits nothing on any path (`check_in_ticket` /
`download_scanner_manifest` / `sync_offline_scans` all resolve on `is_active`).
§10.3 drops the un-revocable-key honesty line; §10.10 [Close this gate] now
revokes server-side (was device-local); §10.11 marks the dependency resolved.
PIN rotation is deactivate-then-recreate (no rotate RPC). Client wiring of
[Close this gate] to the RPC is owed as RF070. Suite:
`supabase/tests/s070_scanner_session_deactivate_rpc.sql` (16 assertions).

---

### §11 — Vendor Console (`/g/:id/vendors`)

*The vendor's own back-of-house for one Gathering: browse the OPEN vendor
tiers, register a credential against one, and — once checked in at the gate —
toggle the stall's live status. Built F009 (`vendor_console_screen.dart`);
its context resolver (`vendor_console_context`, F010) and the N1 close-confirm
dialog are the F010 additions. A **mode** like `/host` — a chrome-less
top-level route, NOT a person-shell tab. Authenticated-only and
membership-gated server-side (every RPC binds `auth.uid()`). It is NOT the
attendee vendor feed (`vendor_feed`, attendance-gated), and it does NOT build
terms-acceptance, team-creation, fee-settlement, or payment — it reacts to the
credential/terms/fee/presence state those produce.*

**1. PURPOSE** Give a vendor company member one screen to get onto a Gathering
(register) and to run the stall on the night (open · pause · sold-out · close).

**2. ENTRY POINTS** The route `/g/:id/vendors`, its own stack outside the
person shell (a mode, not a tab). No first-party in-app affordance is built
this pass — reached by deep link. Back = `pop()`, else `go('/')`.

**3. STRUCTURE (top→bottom, single ListView on Midnight)**
- **Your stall** (`_LiveStatusSection`) FIRST — the one thing an already-live
  vendor opens the console to change fast.
- **Open vendor tiers** (`_TierList`) below — browse + register.

**4. FLOW (tap budget marked)**
```
Register:  tap [Register] on a tier ①  (register_vendor_credential → PAYMENT_PENDING)
           → _RegisterConfirmation (green): credential ref + amount due + "Payment opens soon"
Live-status (once checked in at the gate → a presence exists):
  Open/Pause/Sold out:  tap the chip ①  (vendor_status_toggle → new status)   single-tap
  Close for today:      tap [Close for today] → N1 confirm dialog → [Close for today] ②
```
Register = 1 tap; a routine status change = 1 tap; the irreversible close = 2
taps (the confirm is the guard, not friction). All inside the 3–5 budget.

**5. COMPONENTS** (reuse shared **PriceText**, **ZukaButton**, **FormBanner** —
do not redefine).
- **`_LiveStatusSection`** — the stall control. Renders ONE of four shapes
  (see STATES): skeleton · null-context note · no-presence note · the toggle.
- **`_StatusHeadline`** — a status dot + *"Your stall — <label>"*. The dot is
  **`liveCoral` only when status == LIVE** (live right now); every other status
  (Paused · Sold out · Closed · Not open yet) uses neutral `ink3`. Labels via
  `vendorStatusLabel` (LIVE→*Open now*, PAUSED→*Paused*, SOLD_OUT→*Sold out*,
  CLOSED→*Closed*, null→*Not open yet*).
- **`_CommandChip`** — a gold-outline `OutlinedButton` per available command,
  showing an inline gold spinner while its own command is in flight. Commands
  come from `availableCommands(status)` = every `VendorStatusCommand` whose
  resulting status differs from the current one; a **CLOSED presence is
  terminal → no chips** (replaced by the line *"This stall is closed for the
  gathering."*).
- **`showCloseForTodayConfirm`** — the **N1 confirm dialog** (F010) gating the
  DONE command only. See COPY. OPEN/PAUSE/SOLD_OUT stay single-tap.
- **`_TierRow`** — one OPEN tier: name · *"N slots left"* (gold caption, ONLY
  when `slotsRemaining != null` — unlimited shows nothing, never a fabricated
  count) · **PriceText** (vendor tiers are always `> 0`, so never "Free" here)
  · a secondary **[Register]** / **[Set up your team to register]** ZukaButton.
- **`_RegisterConfirmation`** — the registered moment: **success-green** border
  + check glyph, credential `ticket_ref` (mono `data` style), *"Due: "* +
  PriceText, *"Payment opens soon…"*. The `23505` already-registered variant
  drops the credential detail and just states the team already holds one.
- **`_StatusNote` / `_EmptyTiers` / `_InlineError`** — the calm ink-bordered
  note card used by every non-populated state (never a raw error, never blank).

**6. STATES** (every one reachable)
*Live-status section:*
- **loading** — `_StatusCardSkeleton` bar while the context resolves.
- **null-context** — resolver returned no acting team (caller is in no vendor
  company, or none for this gathering) OR the read errored → a storefront-icon
  note: *"We couldn't load your vendor details for this gathering yet."* No
  toggle fires with a placeholder id.
- **no-presence** (registered, not yet checked in) — `hasPresence == false` →
  a clock-icon note: *"You'll be able to open, pause, or close your stall here
  once you've checked in at the gathering gate."* Presence is created ONLY by
  gate check-in (backend dependency).
- **toggle** — a presence exists → headline + command chips.
- **toggling** — the tapped chip spins; **all chips non-tappable** while any
  command is in flight (disable-on-submit / commit-once).
- **toggle-error** — `FormBanner` above the chips with mapped copy
  (`classifyToggleError`); `offline` variant when the failure is network. Form
  values/status untouched; the vendor retries.
- **closed** — terminal; no chips, closed line shown.

*Tier list:*
- **loading** — `_TierSkeleton` (two bars).
- **empty** — `_EmptyTiers`: *"No open vendor tiers for this gathering right
  now."*
- **error** — `_InlineError`: mapped message + a **[Retry]** that invalidates
  `openVendorTiersProvider`.
- **register-disabled** — no registerable context (`!canRegister`: no team, or
  team but no published terms template) → every tier button reads **[Set up
  your team to register]** and is disabled (never fires with a null terms id).
- **registering** — the tapped tier's button spins; ALL tier buttons disabled
  (commit-once across the list).
- **success** — `_RegisterConfirmation` (green) renders above the tiers; the
  register action is spent for this gathering, whichever tier.
- **already-registered (`23505`)** — soft success (same green card, no
  credential detail) — never a red error.
- **register-error** — `FormBanner` above the tiers (`classifyRegisterError`);
  network-ambiguous copy tells the vendor to re-check, never auto-retries (a
  landed register returns `23505` on re-tap → soft success).

**7. CONTENT / COPY** Direct, present-tense, warm. Headline *"Your stall —
<label>"*. Section header *"Open vendor tiers"*. Register: **[Register]** /
disabled **[Set up your team to register]**. Confirmation: *"You're
registered as a vendor."* / already: *"Your team already has a credential for
this gathering."* + *"Due: <amount>"* + *"Payment opens soon — your slot is
reserved."* Toggle chips: **Open · Pause · Sold out · Close for today**.
**N1 dialog** — title *"Close your stall for today?"*; body *"This closes your
stall for the whole gathering. You can't reopen it here afterwards — only
open, pause, or sold-out can be changed back."*; actions **[Keep it open]**
(cancel, neutral `ink4`) and **[Close for today]** (confirm, **`error` red** —
F012 ruling; danger, not coral, see §8). The
irreversibility is stated because CLOSED is terminal server-side (S023) and no
reopen RPC exists. Error copy is domain-refined but never leaks a raw
constraint (e.g. `23514` on register → *"…settle any outstanding platform fee
and accept the current vendor terms, then try again."*).

**8. COLOUR LOGIC** (the hard rule, held exactly)
- **`liveCoral` appears in exactly one place, truthfully:** the status dot when
  the stall is **LIVE** (live right now). Nowhere else — no coral chip, no coral
  header, and (F012, 2026-07-17) **no coral on the destructive [Close for today]
  confirm** — coral is a *state*, not an action; the terminal close is danger, not
  live. See §10 (Go-Live flow) COLOUR LOGIC for the resolved OPEN VIOLATION.
- **`error` red** — the destructive **[Close for today]** confirm action in the N1
  dialog (F012 ruling): a terminal, irreversible close (CLOSED has no reopen RPC,
  S023) earns the danger token, out-weighing the neutral `ink4` **[Keep it open]**
  cancel without touching coral or gold's action-fill monopoly.
- **`success` green** — the register confirmation (border + check + the "Due"
  block). A credential minted is a green success, never a live state.
- **`emberGold`** — affordances: the command-chip outlines/spinners, the
  Register button (secondary ZukaButton), and the *"N slots left"* caption.
- Neutral `ink` ramp — every note card, the non-live status dot, chrome,
  Midnight background throughout.

**9. UX RULES**
- **Only DONE is confirm-gated** — because it is the one irreversible command
  (no reopen from CLOSED). OPEN/PAUSE/SOLD_OUT are reversible → single-tap.
- **Commit-once everywhere** — one in-flight register across all tiers; one
  in-flight toggle across all chips (`register_vendor_credential` is NOT
  blind-idempotent — the unique guard is the only backstop; the client serialises).
- **Never fire with a placeholder id** — register needs a resolved team + terms
  (`canRegister`); toggle needs a `vendorPresenceId`. Missing → an explanatory
  disabled/note state, never a call.
- **No fabricated numbers** — slots-left renders only for a real count; unlimited
  shows nothing (mirrors §6 Detail discipline). Prices are server-only via PriceText.
- **Browsing tiers is independent of team resolution** — the list is
  membership-gated server-side and needs no team argument, so a null context
  still browses; only register/toggle degrade.
- **Locked terminology** — this is a semantic vendor surface: *stall*, *vendor
  company/team*, *gathering*; Person, never "user".

**10. OPEN QUESTIONS (backend/build dependencies)**
- **No reopen-from-CLOSED RPC** — CLOSED is terminal (S023); the N1 dialog and
  the closed-state line tell that truth. If "reopen" is ever wanted, it needs a
  new RPC + a state-machine ruling.
- **Presence is created only by gate check-in** — a registered-but-not-checked-in
  vendor sees the no-presence note; there is no self-serve "open my stall
  before the gate" path by design. Confirm this is intended for M0.
- **First-party in-app entry point is deferred** — the console is deep-link/
  route-only this pass; where the vendor affordance lives (a `/host`-style quiet
  entry, a vendor-team home) is unspecified.
- **Payment/settlement is a separate money-gated session** — register mints a
  PAYMENT_PENDING credential + an amount owed; the MoMo/Flutterwave settlement
  (the "Payment opens soon" promise) is not built here.

**11. CHANGELOG**
- *2026-07-16 (F010):* Section created, documenting the F009-built console as it
  stands after F010 — the **`vendor_console_context` resolver went real** (team ·
  terms · presence · status in one row, null when no acting team → register/toggle
  degrade to explanatory states rather than firing with placeholder ids), and the
  **N1 "Close for today" confirm dialog** was added (DONE is irreversible — CLOSED
  is terminal, no reopen RPC — so it alone is confirm-gated; OPEN/PAUSE/SOLD_OUT
  stay single-tap). Coral held to LIVE + the destructive confirm only; register
  success is green.

---

### §12 — Receipts (in-app · share image · document — the A-04/05/06 trio)

**Status: REVIEWED (A-04/A-05/A-06 · founder-approved 2026-07-18 — masking
law ratified; A-06 light-print exception ratified; story-image amount stays
default-off).**
Mockup: `mockups/a-04-purchase-receipt/receipt-trio.html` — one sheet for the
trio, deliberately: a receipt is ONE identity across three surfaces, and the
system (facts, masking law, copy) must be designed once. Design-ahead: **no
receipt-read RPC exists** (Open Questions).

**1. PURPOSE** The honest record of what was paid (or claimed free), kept
forever, shareable without ever becoming an admission credential.

**2. ENTRY POINTS** [Receipt] row on the ticket (§8) · the confirmed moment's
quiet secondary action (§7) · resurfaced from My Tickets history.

**3. THE TWO LAWS (bind all three surfaces)**
- **Money facts are server-returned verbatim:** `amount_ugx` and `tx_ref`
  render exactly as the backend supplied them at initiate/confirm — the
  client never computes, sums, or reformats beyond `UGX n,nnn`.
- **Admission credentials never leave the app:** the QR never appears on any
  receipt surface; `ticket_ref` renders FULL only on the in-app view (A-04)
  and is MASKED (`ZKA_TCK_••••`) on every exported surface (A-05 image,
  A-06 document) — ticket_ref is a typed gate fallback (§10.10), so an
  exported receipt must admit no one. `tx_ref` (Flutterwave payment ref) is
  the receipt's public identity: full everywhere, useless at a gate.

**4. A-04 — IN-APP VIEW.** Ember Edge success spine; the amount in display
type; facts as rows (gathering · when · venue · paid-at · payment ref ·
ticket ref); [Share image] + [Save PDF]. States: paid · free claim (*"Free
entry · no payment made"*, `UGX 0`, no payment-ref row — an empty row never
renders; no FREE!!! theater) · offline (*"saved on this phone — it doesn't
change"*: a receipt is history, cacheable by nature) · loading skeleton ·
error (*"your ticket and money are untouched"*).

**5. A-05 — SHARE IMAGE.** Generated on-device from receipt facts (never a
screenshot); two aspects: story 1080×1920 (leads social: *"I'm going."*, no
amount) and square 1080×1080 (leads proof: *"Ticket secured ✓"*, amount
shown). Both: wordmark, gathering, date/venue, footer `tx_ref · masked ref ·
zukaapp.com`. No QR, no person name — a shared image admits no one and
identifies no one (DPPA-min). Amount-on-story is a founder-flippable choice
(default off: flaunting price is not the vibe).

**6. A-06 — DOCUMENT (PDF/print).** **The one deliberate light surface in
ZUKA** — paper is white and receipts are read in daylight; midnight ink on
paper is a toner joke at a Kampala print shop. Brand carries via wordmark +
print-safe darker gold + Grotesk headings. Facts table + total; boundary
line printed on every copy: *"This document records a payment; it does not
admit entry — your QR ticket lives in the ZUKA app and works offline."*
Support routes on the payment reference. Logged like A-07's exception: the
light-print theme needs an explicit founder ruling.

**7. COLOUR LOGIC** Success green for the paid/claimed state line; neutral
ink for facts; coral never (a receipt is never a live state); A-06 flips to
light per above.

**8. OPEN QUESTIONS (backend/build dependencies)**
- **Receipt-read RPC (blocker for build):** the paid facts (amount_ugx,
  tx_ref, paid_at, method) live server-side but no RPC returns them
  post-purchase — `my_tickets()` carries none of them. Ask:
  `my_ticket_receipt(p_ticket_id)` returning the §12.4 fact set; free
  claims return the claim record (no payment fields).
- Tier name on the receipt = the same missing-column question as §8.7.
- On-device image/PDF generation packages — fold into the ADR-030
  candidate list (§7.7/§8.7/§10.11).
- EAT timezone rendering on the document (backend returns timestamptz;
  display rule needed once, here).

**9. CHANGELOG** *2026-07-18 (A-04/05/06):* created — trio designed as one
system; masking law (full ref in-app only) and the light-print exception
proposed.

---

### §13 — Clips (list rows · social snippets · video previews — A-13/14/17)

**Status: REVIEWED (A-13/A-14/A-17 · founder-approved 2026-07-18 — live-share
coral exception + timestamp-honesty rule ratified).**
Mockup: `mockups/a-13-clips/clips.html` — one sheet for the clip class.

**1. A-13 — THE CANONICAL LIST ROW (Ember Edge compact, codified).** Already
live in A-03 (tickets) and A-10 (low-data map list); now law: 6px state
spine · line 1 = title + one right-aligned context-owned fact (distance in
search/map, time in tickets, nothing in inbox) · line 2 = context line + AT
MOST one state chip. A row is a glance, not a card. Contexts rendered:
search results; inbox/notification rows (the "went live" push landing, ticket
system rows) — unread is a quiet gold dot, never a count badge; row tap = the
push's own deep link. `/inbox` stays a reserved route (§9) — rows are
designed now, the screen assembles when it registers.

**2. A-14 — THE SHARE FAMILY (consolidated, 6 snippets, 2 new).** Referenced
as already ratified: "I'm going"/ticket-flex story + square (A-05) ·
gathering-announce story (A-09). New: **"LIVE NOW" venue share** (from
feed/Detail ⋯ while live) and **CITY PULSE count share** (the count as
display-type hero). **These two are the only exported surfaces that may wear
coral — they share a genuinely live state, and every live snippet carries
its timestamp so a stale reshare never lies about "right now."** Family
rules: on-device generation from feed data only; the count share renders the
REAL pulse count only (launch-status honesty); §12.3 masking law
family-wide; tagline verbatim; no engagement-bait copy (Vol 16).

**3. A-17 — VIDEO MOMENT PREVIEW (design-ahead).** Ember Edge card + media
frame. **Cellular default: a still poster frame (part of the feed thumb
budget), a play button, and the data cost as the consent chip ("12 MB") —
zero video bytes load until tapped.** Wi-Fi: muted autoplay, loops once,
sound is a tap. Reduce-motion: never autoplays anywhere. Loading keeps the
frame; unavailable degrades to the fully-working text card ("video is
garnish, never the meal"). **No media/moment pipeline exists** — poster
frame + video are Media (ADR-009, never conflated with Memory); Storage/CDN
pipeline is the named backend ask.

**4. OPEN QUESTIONS** `/inbox` route registration (§9 reserved) · media
pipeline (A-17 blocker) · on-device share rendering (ADR-030 pile) ·
moment capture/upload flow is NOT designed here (organiser/venue side —
future artifact, needs its own catalogue row when the pipeline is real).

**5. CHANGELOG** *2026-07-18 (A-13/14/17):* created — clip class designed as
one system; live-share coral exception + timestamp-honesty rule proposed.
*2026-07-22 (RF019-F2):* **the codified prose is authoritative — line 1 shows
"nothing in inbox".** The `docs/design/mockups/a-13-clips/clips.html` inbox rows
still paint a right-aligned `.r-meta` timestamp (`20:00`/`17:02`/`19:48`) beside
the title, which **contradicts** the "nothing in inbox" rule above and is
**stale/superseded** — do not read those timestamps as spec. F019's `/inbox`
build correctly follows the prose (title alone on line 1); flagged here so the
next reader of `clips.html` isn't misled.

---

### §14 — Message Templates &amp; Screen Scaffolds (A-15/A-16)

**Status: REVIEWED (A-15/A-16 · founder-approved 2026-07-18 — consent-basis law + universal scaffold law ratified).**
Mockup: `mockups/a-15-messages-scaffolds/messages-and-scaffolds.html`.

**1. A-15 — MESSAGE TEMPLATES (10 messages · 3 channels · 3 consent bases).**
Every template carries its consent basis — **transactional** (services a
ticket/payment the person holds) · **follow-push** (§4.6's separate consent,
one per follow-target per go-live) · **marketing** (its own consent, STOP in
every message). Channel laws: present-tense + real names/times · UGX verbatim
· one idea per message · no emojis in SMS (GSM-7 ≤160) · a channel earns only
what its consent covers — never marketing on a transactional basis (DPPA).
- *Push (FCM):* P1 went-live · P2 ticket-secured · P3 still-pending-at-T+5
  (once, factual) · P4 same-day doors reminder (holders only, "ready
  offline").
- *SMS (Africa's Talking):* S1 ticket confirm **with the holder's code — THE
  no-smartphone admission path** (boundary law: the holder's own credential
  to their own registered number ONLY, §12.3-consistent) · S2
  expired-mid-payment (R009-F2 honesty in 160 chars) · S3 "tonight by SMS"
  discovery digest (**marketing basis**, reply-TONIGHT pull-first, STOP
  always).
- *WhatsApp (share-prefills, person-initiated — the person is the sender):*
  W1 "I'm going" · W2 live-now (pairs with A-14's coral snippet) · W3 gate
  hand-off (**deliberately splits code from PIN across channels**; §10.3
  security copy travels with the key).
- Delivery seams named, not built: FCM wiring, Africa's Talking SMS, STOP/
  HELP handling. Counsel touchpoints: S3 wording, FCM/AT processor
  disclosure in the privacy notice.

**2. A-16 — SCREEN SCAFFOLDS (five skeletons, traced to built screens).**
A new screen picks a scaffold and fills zones — placement is never invented:
- **S1 FEED/LIST** (chrome · ribbon slot · groups · Ember Edge rows · tab
  bar) — §1, A-03, §4, A-10 low-data.
- **S2 DETAIL** (back+⋯ · ribbon · identity zone · body · sticky primary) —
  §6, §8 (the QR is the identity zone).
- **S3 FORM** (title · FormBanner · field stack in RPC param order · consent
  rows · sticky primary that surfaces errors) — §5A/B/D, A-08.
- **S4 SHEET** (grab · title+context · content · ≤2 actions; dismiss
  preserves the armed tap) — §5D, §4's ⋯, §4.6 consent, A-10 pin card.
- **S5 VERDICT/MOMENT** (small context · display-type word in state colour ·
  one line + mono ref · one next action) — A-07 verdicts, §7/A-01 confirmed,
  §8 "You're in".
Universal scaffold law: every scaffold reserves the ribbon slot at content
top; states render in-place (never a blank page); the primary action lives
in the thumb zone; one primary per screen.

**3. CHANGELOG** *2026-07-18 (A-15/16):* created.

---

### §15 — Documents (printable ticket · vendor statement — A-18/A-19)

**Status: REVIEWED (A-18/A-19 · founder-approved 2026-07-18 — treat-like-cash + declaration-leads laws ratified). CATALOGUE COMPLETE: A-00–A-19 all designed and founder-reviewed.**
Mockup: `mockups/a-18-documents/ticket-and-vendor-docs.html`. Both ride
A-06's ratified light-print exception; coral appears on neither (paper is
never a live state).

**1. A-18 — PRINTABLE TICKET (the credential, on paper).** Holder-initiated
only ([Save PDF] as §8's quiet secondary — never auto-generated, never
emailed unasked). Full QR + full `ZKA_TCK_…` + the spoken-code line;
**no person name by design** (the scanner judges tokens, not identity, at
M0 — a printed name is DPPA surface with zero admission value).
- **Load-bearing line, printed on every copy:** *"Treat this like cash.
  Anyone holding it can enter — once. After the gate scans it, it's used; a
  photocopy won't get a second person in."* Protections are token rotation
  (used = void) + the scanner's sole authority — not masking.
- **§12.3 reconciliation (recorded):** the masking law governs documents
  *about* a ticket (receipts, shares); A-18 *is* the ticket — the holder
  exporting their own credential equals showing their screen at the gate.
- Why it exists: dead battery · borrowed phone · no smartphone (paper
  complements A-15/S1's SMS code path). Footer states the void conditions
  (refunded/used ⇒ paper void; the app's ticket always wins).

**2. A-19 — VENDOR STATEMENT (design-ahead for Vendor Marketplace §9B
Phase-2; the declaration is law now).** No vendor order flow is live — this
template fixes the document's shape and its NPS-posture declaration so the
paper is already lawful when orders exist. Nothing invents money semantics:
- **Load-bearing declaration, leading the document:** *"Your money never
  passed through ZUKA. Every order below was paid by the buyer directly to
  your MoMo via Flutterwave. This statement is a record — not a payout, not
  a settlement. There is nothing to 'release': you already have it."*
- Rows = Flutterwave-confirmed transactions verbatim (tx ref · item · UGX
  integer); **the platform-fee row is RESERVED, empty, and says why** (fee
  mechanics land with §9B and its own agreement — never invented here).
- Gates on the eventual build: payment_rails domain — zuka-security +
  zuka-legal before ship; counsel wording for the declaration.

**3. CHANGELOG** *2026-07-18 (A-18/19):* created — catalogue complete
(A-00–A-19 all designed).

---

### §16 — Venue self-service (create via pin-drop · deactivate — in the VenuePicker sheet · F021)

*Map-lens slice-3b. Plugs the real launch gap the §5B VenuePicker leaves open:
an organiser at an unseeded venue cannot go live (a physical/hybrid gathering
needs a venue, and today's picker is seed-only — §5 Open Question "Venue
source"). Source spec: `docs/superpowers/specs/2026-07-16-mvp-map-lens-design.md`
§5.1, venue self-service **Variant A** (creator-owned create + deactivate inside
go-live; **no standing venue pins** — Variant B deferred). Backend is shipped
(S051): `create_venue(name, address_text, lat, lng) → {venue_id, slug}` and
`deactivate_venue(venue_id) → bool`. The §5.1 spec explicitly deferred "exact
placement of the deactivation surface" to this pass — resolved below. This is an
**in-sheet mode swap inside the existing VenuePicker bottom sheet — NO new
go_router route** (ADR-028 posture, like §5E's in-flow Tickets pane).*

**1. PURPOSE**
Two jobs, both off the go-live happy path: (a) let an organiser whose venue
isn't seeded **create it by dropping a pin**, name it, and have it selected —
without leaving the create form; (b) let an organiser **deactivate a venue they
created** (retire a mistake / a closed place), human-confirmed (ADR-014).

**2. ENTRY POINTS**
Both live in the **VenuePicker** bottom sheet (reached from §5B Create when
`Medium ∈ {Physical, Hybrid}`), as a **pinned footer** beneath the scrolling
venue list (so they never scroll away under a long search and never intercept a
tap meant for a real venue row):
- **"My venue isn't here"** — always present → starts the create sub-flow (pin-drop).
- **"Manage your venues"** — shown **only when the organiser has ≥1 of their own
  *active* venues** (needs a `list_my_venues()` read — see Open Questions); a
  quiet, secondary affordance below the create entry → opens the deactivate list
  mode. Hidden entirely when there is nothing to manage (never a dead row).

**3. FLOW (tap budget — this whole branch is OPTIONAL and off the 60s path)**
```
CREATE (pin-drop):
  VenuePicker → footer [My venue isn't here] ①
    → VenuePinDrop mode (taller, isScrollControlled): Kampala camera (or GPS
      if already granted), FIXED centre reticle, [Use my location] chip,
      instruction line
      → pan map under the reticle · optionally [Use my location]
      → [Confirm this spot] ②           (captures camera-centre lat/lng)
    → VenueDetailsMiniForm (still in-sheet): Name (required) · Address (optional)
      → [Create venue] ③                 (create_venue → {venue_id, slug})
    → venue is SELECTED in the create form + sheet closes → back in §5B, venue filled

MANAGE (deactivate):
  VenuePicker → footer [Manage your venues] ①
    → ManageVenuesList mode: the organiser's own ACTIVE venues (name · address)
      → a row's [Deactivate] ②
      → DeactivateVenueDialog (confirm) → [Deactivate] ③   (deactivate_venue → true)
    → row leaves the list; venue no longer appears in any picker
```
Create sub-flow ≈ 3 deliberate taps beyond opening the picker; deactivate ≈ 3.
**Neither counts against the 60-second Go Live proof** — the proof measures the
happy path (pick a *seeded* venue, unchanged). UX rule §16.9 protects this.

**4. SCREEN INVENTORY**
All are **modes of the one VenuePicker modal sheet** — no route, no new screen:
- **VenuePicker (list mode)** — existing; gains the two-affordance pinned footer.
- **VenuePinDrop (map mode)** — `isScrollControlled` taller sheet; Mapbox dark
  placement canvas + reticle + chip + Confirm.
- **VenueDetailsMiniForm (form mode)** — name + address + Create.
- **ManageVenuesList (list mode)** — own-active-venue rows with per-row Deactivate.
- **DeactivateVenueDialog** — a Material `AlertDialog` over the sheet (ADR-014
  human confirm).

**5. COMPONENTS** (reuse the shared library; do not redefine ZukaTextField,
ZukaButton, FormBanner, FieldError, PriceText-not-needed-here, or the §2/F013 map
canvas — `map_lens.dart`'s `flutter_map` + Mapbox raster **dark** tiles + own
disk tile cache + Kampala default camera. New pieces, all venue-local:)
- **VenuePinDrop** — the placement map mode. Reuses the F013 map widget but shows
  **NO gathering pins and NO standing venue pins** (Variant B deferred; decision
  #5) — it is a clean placement canvas, not a discovery map. A **fixed centre
  reticle** sits in screen space; the **map moves under it** (chosen over a
  draggable marker: no tiny drag hit-target to miss on a 2GB touch device, and
  the aim point is always dead-centre). Elements: the map · the reticle · a
  **[Use my location]** chip (top-left over the map) · a one-line instruction ·
  a sticky **[Confirm this spot]** primary. Confirm reads the **camera-centre**
  lat/lng — so it works even with tiles absent (Mapbox-down, §16.6).
- **VenueDetailsMiniForm** — appears after Confirm, same sheet: **Name**
  (ZukaTextField, required, trimmed non-empty, ≤ 120) · **Address**
  (ZukaTextField, optional free text — **no reverse-geocode at MVP**, the
  organiser types it, e.g. *"Plot 12, Kira Road, Kamwokya"*; the pin carries the
  precise geo) · a top **FormBanner** slot for RPC/offline failure · sticky
  **[Create venue]** primary (spinner + non-tappable while submitting) · a
  **[Back to map]** secondary that returns to VenuePinDrop **preserving the typed
  name** and the confirmed spot (edit the pin, keep the name).
- **ManageVenuesList** — creator-scoped rows of the organiser's own **active**
  venues: name (h3) · address (caption, `ink3`) · a trailing **[Deactivate]**
  text action. Needs a `list_my_venues()` read (Open Questions).
- **DeactivateVenueDialog** — the ADR-014 human-revocation confirm (copy in §16.7).

**6. STATES (every one reachable — §1/§5 rigour)**
*VenuePinDrop:*
- **default** — Kampala metro camera (or the person's location if already
  granted), reticle centred, tiles filling behind.
- **loading (tiles)** — the calm **map placard** (reuse §2/F013 `_MapSkeleton`
  language — never a spinner on blank); reticle + Confirm are usable the moment
  the camera is set, before tiles finish.
- **out-of-Uganda** — a **client-side bbox mirror** of the backend Uganda box
  detects the camera centre leaving Uganda → an inline line appears and
  **[Confirm this spot] disables** (greyed). Copy §16.7. The mirror MUST be a
  **single shared constant** with the server's box so the pre-check and the
  server's `23514` never disagree — do not invent a second boundary.
- **location-permission** — [Use my location] tap → OS prompt at tap time only
  (never at sheet open — DPPA-clean, mirrors §2). Grant → camera recentres on the
  `info`-blue location dot. **Deny → nothing changes**, the chip stays available.
- **tile-server failure** — blank **Midnight** (`ink0`) canvas; reticle, Confirm,
  and lat/lng capture all still work. **Mapbox down never breaks venue creation**
  (mirrors §2's "Mapbox down never breaks discovery").

*VenueDetailsMiniForm:*
- **form** — clean; typed Name/Address persist in local sheet state (survive a
  failed submit, a backgrounded app, or a [Back to map] round-trip — 3G reality).
- **submitting** — [Create venue] loading (spinner + label, non-tappable), fields
  disabled. One in-flight write at a time.
- **success** — the returned `venue_id` is selected in the §5B form; the whole
  sheet **closes** back to Create with the venue filled. No coral (pre-live).
- **validation-error** — Name empty/too long → FieldError under the field; primary
  stays enabled so the tap surfaces the error; no banner for a field problem.
- **RPC-error (FormBanner, mapped copy — NEVER raw Postgres text)**:
  - `23514` Uganda-bbox reject → *"Drop the pin inside Uganda."* (normally
    pre-empted by the client mirror; this is the backstop.)
  - `ZK429` rate limit (5 venues/hour/Person) → *"You've added a lot of venues in
    the last hour. Give it a moment and try again."*
  - `23505` slug/name collision (rare) → *"There's already a venue with that name.
    Add a detail to tell it apart — a neighbourhood, say."*
  - unmapped → generic *"Something went wrong — try again."* + Sentry breadcrumb.
- **offline** — filling the pin-drop + form works offline (camera-centre capture
  is local), but **[Create venue] needs the network** (authed RPC): 10s timeout →
  calm FormBanner *"You're offline — your venue details are safe here. Try again
  when you reconnect."*, values kept, primary returns to enabled.

*ManageVenuesList:*
- **loading** — skeleton rows (needs `list_my_venues()`).
- **default** — the own-active-venue rows.
- **empty** — reachable only if a race retired the last venue between entry and
  read: *"Nothing to manage — you haven't added a venue yet."* (The footer entry
  is normally hidden when the set is empty, §16.2.)
- **deactivate-blocked (no-orphan, `23514`)** — the row's own inline line, calm and
  actionable: *"This venue still has a live or upcoming gathering. End or move
  those first, then you can deactivate it."* The row stays; nothing is destroyed.
- **deactivate-success** — the row leaves the list (a one-line *"Deactivated."*
  confirm); no undo (no reactivate RPC — Open Questions).
- **creator-only (`42501`)** — should never surface (we list only the caller's own
  venues) → generic + Sentry if it ever does (treated as an app bug).
- **read-error / offline** — FormBanner + retry; **deactivate is disabled offline**
  (honest small client — no silent write queue, mirroring §4's offline stance).

**7. CONTENT / COPY** (voice: direct, present-tense, Kampala-real, honest — no hype)
- Picker footer: **"My venue isn't here"** · **"Manage your venues"**.
- VenuePinDrop instruction: *"Move the map so the pin sits on your venue."*
  Chip: **"Use my location"**. Primary: **"Confirm this spot"**.
  Out-of-Uganda inline: *"That spot's outside Uganda — pan back inside to drop the pin."*
- VenueDetailsMiniForm: header *"Name your venue."* · Name placeholder *"e.g.
  The Alchemist, Lugogo"* · Address label *"Address (optional)"* placeholder
  *"Plot / street / neighbourhood"* · Name FieldError *"Give the venue a name."*
  / *"Keep the name under 120 characters."* · primary **"Create venue"** ·
  secondary **"Back to map"**.
- DeactivateVenueDialog: title **"Deactivate [venue name]?"** · body *"It stops
  showing when anyone creates a gathering. Gatherings already at this venue aren't
  affected. You added it, so only you can do this."* · confirm **"Deactivate"** ·
  cancel **"Keep it"**.
- Terminology: this is a semantic creation surface → **venue**, **gathering**,
  **organiser**; never "user", never "event".

**8. COLOUR LOGIC** (colour-as-information is a hard rule — Vol 25 Ch4)
- **NO `liveCoral` anywhere in §16.** Every surface here is pre-live or
  administrative — coral stays reserved for the `go_live` bloom (§5 §10). A venue
  is a *place*, never a live *state*.
- Primary actions **[Confirm this spot]** and **[Create venue]** = **`emberGold`**
  (gold = primary action).
- The **reticle/pin is neutral high-contrast `ink5`** with a dark contrast ring —
  deliberately **NOT** a state colour: coral/gold/`kampalaForest` are the
  gathering-state palette and would *misinform* on a placement marker (a pin that
  wears forest reads "tonight/scheduled", which it is not). Colour-as-information
  cuts both ways — the reticle carries no information-colour claim.
- Location dot = system **`info`** blue (`ZukaColors.info`), asked in context —
  not a brand colour (matches §2/F013).
- Map base when tiles are absent = **Midnight `ink0`**.
- Field focus = gold border; FieldError / failed-field = **`error`** red.
- **DeactivateVenueDialog confirm = `error`-red foreground text** (no fill — gold
  keeps its action-*fill* monopoly), cancel at neutral **`ink4`**. Rationale
  follows the F012 Coral-Fence precedent (§5 §10): deactivation is **terminal from
  the UI's view — there is no reactivate RPC** — so it is danger-weight, and a
  terminal action must chromatically out-weigh its safe sibling. `error` over
  `warning` for that irreversibility; never coral (coral means live, not danger).

**9. UX RULES**
- **The 60s Go Live proof is unregressed.** The two footer affordances are purely
  additive; the seeded-venue happy path (search → tap a row → selected) is
  byte-identical. zuka-reality-checker re-verifies the venue-creation branch
  on-device per the slice-3 gate.
- **Spacing / type / layout tokens** (`ZukaSpacing` · `ZukaTypography`, ratified
  8px grid): sheet content padding `md` (16); footer affordances separated from the
  list by a `ZukaColors.ink2` hairline + `md` above; row vertical rhythm `sm`–`md`;
  reticle and chip inset from map edges `md`. Row title `h3`, address `caption`
  (`ink3`), instruction line `bodyStrong`. Reuse the **A-16 S4 SHEET scaffold**
  (grab handle · title+context · content · ≤2 actions; dismiss preserves the
  armed values).
- **All interactive targets ≥ 48×48** (Material min): footer rows, the
  [Use my location] chip, [Confirm this spot], [Create venue], list [Deactivate],
  and the dialog buttons.
- **Sheet dismiss during create keeps nothing half-written server-side** — no
  venue is created until [Create venue] returns; a mid-flow swipe-down just closes
  back to the picker (the create form's prior state intact). Dismiss preserves the
  typed values *within* the sheet session (S4 law).
- **No standing venue pins** anywhere (decision #5) — the placement map shows no
  pins at all; duplicates are tolerated at MVP (disputes are human, ADR-014).
- **Africa-first:** text-first, native components, no imagery required to create a
  venue, tiles are the only heavy asset and the flow survives without them, 10s
  timeout → honest offline copy.
- **DPPA:** precise location is untouched until an explicit [Use my location] tap;
  a created venue's name/address/geo are venue metadata (not personal data), but
  `created_by_person_id` links the venue to the organiser — that ownership row
  rides the F003 data-subject-rights path like any other Person-linked record.

**10. ACCESSIBILITY**
- The **reticle carries a semantics label**: *"Venue location. Pan the map to
  position the pin, then confirm."* [Confirm this spot] announces a plain
  read-back on activation (*"Spot confirmed"*) — screen readers get an event, not
  a silent geo change.
- The map itself is largely opaque to screen readers by nature; the **Address
  text field is the accessible fallback** for specifying *where* — Name + Address
  are fully screen-reader-complete, and the geo defaults to camera-centre/Kampala.
  A fully non-visual precise-placement path is a known M0 limitation (logged).
- The **[Use my location] chip announces that it will request location
  permission** before firing the OS prompt.
- **DeactivateVenueDialog** is a real focus-trapped dialog (announced, ESC/back
  cancels to the safe **[Keep it]**); the destructive **[Deactivate]** is never
  the default-focused action.
- All information carried by colour (the `error`-red deactivate, the out-of-Uganda
  disable) is **also carried by text** — colour is never the sole signal.

**11. OPEN QUESTIONS & BACKEND DEPENDENCIES**
- **`list_my_venues()` read RPC — CLOSED client-side (F021), no RPC needed.**
  ManageVenuesList needs the organiser's own **active** venues (name · address ·
  id), creator-scoped. S051 shipped `create_venue`/`deactivate_venue` but **no
  own-venue read** — so F021 closed the gap **without** a new RPC, via a direct
  RLS-scoped table read filtered on `created_by_person_id`
  (`organiser_repository.dart` `listMyVenues`); the pre-existing
  `venue_select_active` policy already restricts rows to `is_active = true`.
  The Manage mode therefore **shipped** in F021 — it is not blocked. A dedicated
  `list_my_venues()` RPC remains a possible future tidy-up (it would move the
  own-rows filter server-side), not a dependency.
- **No reactivate RPC** — `deactivate_venue` sets `is_active=false` with no inverse;
  a mis-deactivation cannot be undone in-app. Hence the terminal `error`-red confirm
  and the explicit dialog. Flag for a founder call on whether reactivation is needed.
- **DRAFT-orphan question** — does an **unpublished DRAFT** gathering (created in
  §5B, referencing this venue, not yet published) count toward `deactivate_venue`'s
  no-orphan `23514` block? If not, an organiser could deactivate a venue their own
  in-progress draft still points at. Backend to confirm the block's exact predicate
  (live/upcoming *published* only, vs any referencing gathering incl. DRAFT).
- **Shared Uganda-bbox constant** — the client out-of-Uganda mirror MUST reference
  the *same* constant the server's `23514` uses (exported once), so the two never
  disagree at the boundary. Backend/build to expose it.
- **Reverse-geocode (deferred nicety, not a dependency)** — prefilling Address from
  the dropped pin needs a geocoding call; out of scope at MVP (Address is manual
  free text). Revisit if it earns its cost.

**12. CHANGELOG**
- *2026-07-20 (F021):* Section created for map-lens slice-3b. Specced venue
  self-service Variant A as **in-sheet modes of the existing VenuePicker** (no
  route): a pinned two-affordance footer, VenuePinDrop (fixed-reticle placement on
  the reused F013 Mapbox map), VenueDetailsMiniForm, ManageVenuesList, and the
  ADR-014 DeactivateVenueDialog. Mapped all four RPC error paths (23514 bbox,
  ZK429 rate limit, 23505 collision, deactivate no-orphan 23514) to actionable
  copy. Logged the `list_my_venues()` read gap, the no-reactivate terminality, the
  DRAFT-orphan predicate question, and the shared-bbox-constant requirement.
  Resolved the §5.1 "deactivation surface placement" deferral. **Placement changes
  from the proposed structure (2, refinements):** (a) both affordances placed in a
  **pinned sheet footer** rather than as a trailing list *row*, so they never
  scroll away or get mis-tapped during venue selection; (b) "Manage your venues"
  gated on **≥1 own *active* venue** (not merely ≥1 *created*) and **hidden**
  otherwise — surfacing the `list_my_venues()` read as a hard build dependency.

### §17 — Vendor Template Manager (`/vendor` mode · durable booths · 3-band editor — sub-project A1)

*The vendor's durable, always-editable home for their **booths** — the named,
category-typed menu/goods/service templates a company reuses across gatherings.
Free and always available; **no active gathering, no payment, no online
connection required to build a booth** (spec invariants 5 & 7). This is the
first load-bearing piece of the vendor world (sub-project A, spec
`docs/superpowers/specs/2026-07-21-vendor-onboarding-multi-template-account-design.md`),
built on the **S075 backend** (ADR-035): a "booth" IS a category-typed S022
`menu_template` lineage — NOT a new entity. Binds to real RPCs only:
`save_menu_template(company, name, items[], category)`,
`push_menu_template_to_live(team, gathering)`, and (read) the RLS-scoped
`menu_template` / `vendor_menu_item` tables. The paid per-event attach (Flow 2,
sub-project **A2**) and the live dashboard (**C**) are separate sections — this
is the durable manager only. It is NOT the §11 per-gathering console.*

*Design method reconciled with **ui-ux-pro-max** (Flutter/App-UI pass): its
`read-only-distinction`, `form-autosave`, `disabled-states`, `empty-nav-state`
("explain why, don't silently hide"), `drag-threshold`, and `error-clarity`
guidelines shape the interaction; ZUKA's locked tokens, colour law, motion
tiers, and voice win on every visual/brand decision.*

**1. PURPOSE**
Give a vendor one calm place to create and maintain the booths they bring to
gatherings — name it, type it to a category, build its menu/products/services,
keep it current — so that acquiring a per-event credential later (A2) is just
"pick a booth."

**2. ENTRY POINTS**
- **Account → "Your vendor booths"** → `/vendor`. The row is shown **only when
  `vendor_company.vendor_status = true`** (ADR-035 §5 — durable, auto-granted on
  first credential issue by `register_vendor_credential`). No status → no row
  (never a dead/greyed entry). *Client can't read `vendor_status` today — hard
  dependency, §17.10.*
- `/vendor` is a **chrome-less top-level mode like `/host`** (its own Navigator
  stack, NOT a person-shell tab), entered with the mode's T1 modal slide-up
  ("you entered a mode", per the Motion map). Back = `pop()`, else `go('/')`.
- **First-timer chicken-and-egg (spec §5):** a brand-new person has no
  `vendor_status`, so no `/vendor` entry — they reach the vendor world only via
  **Flow 2** (A2, a gathering's vendor CTA), which grants status on first issue.
  So in A1, `/vendor` lights up for anyone who already holds/held a credential
  (e.g. §11-console registrants) — that is the correct, testable A1 audience.
- Routes inside the mode: `/vendor` (booths list) · `/vendor/booth/new` ·
  `/vendor/booth/:id/edit` (deep-linkable per `deep-linking`).

**3. STRUCTURE & FLOW (tap budgets marked; none of this is a sacred-proof path)**
```
BOOTHS LIST (/vendor):
  [Top App Bar] "Your booths"  ·  PlanCapMeter "2 of 3 booths"
  → scrolling BoothCard list (one per active menu_template lineage)
  → sticky bottom primary [+ New booth]   (thumb zone; disabled+why at cap)

CREATE a booth (~3 taps + typing, off any proof path):
  [+ New booth] ①  → /vendor/booth/new (editor, Core band focused)
    → type Name · pick Category (CategorySelector) · Body: add ≥1 item ②(+ per item)
    → [Save booth] ③  (save_menu_template → {template_id, version})
    → returns to list, new BoothCard on top (success tick)

EDIT a booth:
  tap a BoothCard ①  → /vendor/booth/:id/edit (pre-filled, autosaving)
    → change fields; SyncStatusChip tracks Saved/Saving/Offline
    → [Save booth] ② commits a new version (save is versioned: supersede-in-place)

DUPLICATE / DELETE (from BoothCard ⋯ → S4 sheet):
  Duplicate ① → editor pre-filled as a NEW lineage, name "<name> (copy)" → Save
  Delete ① → confirm → row leaves with an Undo toast (grace window)
```
Create ≈ 3 deliberate taps beyond entering the mode; every action sits inside
the 3–5 budget. **No sacred proof is touched** — this is durable management, not
discovery/go-live/ticket (UX rule §17.9).

**4. SCREEN INVENTORY**
- **BoothsListScreen** (`/vendor`) — Top App Bar + PlanCapMeter + BoothCard list
  + sticky [+ New booth].
- **BoothEditorScreen** (`/vendor/booth/new` · `/…/:id/edit`) — the 3-band
  editor on the **S3 FORM** scaffold (title · SyncStatusChip/FormBanner slot ·
  band stack · sticky primary that surfaces errors).
- **BoothOverflowSheet** (S4) — Edit · Duplicate · Delete.
- **DeleteBoothConfirm** (dialog) — human confirm (ADR-014); Delete in `error`
  red, Cancel neutral (destructive-emphasis, separated from primary).

**5. COMPONENTS** (reuse shared **BoothCard · CategoryBadge · PlanCapMeter ·
CategorySelector · ItemEditorRow · PriceInput · SyncStatusChip · ZukaTextField ·
ZukaButton · FormBanner · FieldError · PriceText** — catalogued above; not
redefined). The editor is **three bands** (progressive disclosure —
`progressive-disclosure`; complexity revealed band by band, never a wall):
- **Band 1 · Core** — **Name** (ZukaTextField, required, the lineage key) ·
  **Category** (CategorySelector, required to sell; locks after first paid use).
  *Tagline / photo are design-ahead (no schema home yet — §17.10); rendered as
  a clearly-labelled "coming soon" placeholder, never a field that silently
  fails to persist.*
- **Band 2 · Body** — the **curated per-category schema** (§17.6): an
  **ItemEditorRow** list with **[+ Add item]**, reorder-by-handle, and per-row
  remove+undo. This is the only band that grows; `ListView.builder` (memory
  doctrine). Empty body = the first-run nudge (§17 STATES).
- **Band 3 · Settings** — accept-orders toggle · prep-time note · "how buyers
  pay you". **All design-ahead / non-persistable in A1** (no schema; and "how
  buyers pay you" is money-posture-sensitive — vendor-direct only, no wallet UI,
  legal/security-gated). Shown as read-only placeholders with an honest "coming
  soon" line so the band's shape is fixed without inventing money semantics.

**6. THE CURATED PER-CATEGORY SCHEMAS (first three categories — the Body band)**
One generic persisted shape (`vendor_menu_item`: name · `price_ugx` · `category`
· `available` · `attributes` JSONB — ADR-035 decision 6), rendered with
category-specific labels/helpers. **No per-category tables; all category-specific
fields ride `attributes`.** Client passes the items array to `save_menu_template`.

| Category | Body noun | ItemEditorRow fields (label → storage) |
|---|---|---|
| **Food & Drink** | *Menu items* | Dish name→`name` · Price→`price_ugx` (PriceInput) · Section→`category` (Mains/Sides/Drinks…) · In stock→`available` · Note→`attributes.note` |
| **Merch / Goods** | *Products* | Product→`name` · Price→`price_ugx` · Variant/size→`attributes.variant` · In stock→`available` |
| **Services** | *Bookables* | Service→`name` · Price→`price_ugx` · Duration→`attributes.duration_min` · Taking bookings→`available` |
Section/variant/duration are lightweight text/number inputs — no heavy pickers
(Africa-first). A 4th+ category is added by extending this table + the schema
map, never a new codepath.

**7. STATES** (every one reachable; nothing renders blank)
*Booths list:*
- **loading** — BoothCard skeletons (T1 shimmer).
- **empty / first-time** (status true, zero active templates) — a storefront
  glyph + *"Set up your first booth."* + one line (*"A booth is a menu, product
  list, or service you bring to gatherings — build it once, reuse it anywhere."*)
  + primary **[Create your first booth]**. Direction, not mood (voice law).
- **populated** — the BoothCard list + PlanCapMeter.
- **at-cap** (active templates ≥ plan cap; free = 3 per the RPC) — **[+ New
  booth] is disabled AND explains why** (`disabled-states` + `empty-nav-state`):
  a line *"You're using all 3 booths on the free plan."* + an **honest** seam
  *"More booths are coming with vendor plans — for now, edit or remove a booth
  to free a slot."* **No fake "Upgrade" button to a screen that doesn't exist**
  (plans = sub-project E, not built; no dark pattern). The server also rejects a
  raw over-cap `save` (check_violation) — the disable is courtesy, the server is
  the guarantee.
- **error** — inline note card + [Retry] (never a raw constraint).
- **offline** — the cached booth list renders (booths are local-first) with a
  calm *"Offline — showing your saved booths"* ribbon; [+ New booth] still opens
  the editor (creation drafts locally, syncs later — §17 offline rule).

*Editor:*
- **default / editing** — the three bands; SyncStatusChip live.
- **autosaving** — draft written locally on change; SyncStatusChip *Saving… →
  Saved* (`form-autosave`: no work is ever lost to an accidental dismiss).
- **offline** — edits captured to the local draft; chip *"Offline — saved here,
  will sync"*; [Save booth] queues **one** reconcile save on reconnect
  (commit-once, not a per-keystroke queue — avoids version spam, since every
  `save_menu_template` mints a new version).
- **sync-failed** — chip *"Couldn't save — retry"* + reason; values untouched.
  If a queued **new-lineage** create is rejected on reconnect because the cap
  filled meanwhile → honest *"Couldn't add this booth — you're at your plan
  limit now"* (the draft is preserved, not destroyed).
- **category-locked** — CategorySelector renders **read-only** (not disabled):
  the settled category value + lock glyph + *"This booth has sold at a paid
  event, so its category is fixed. Create a new booth for a different category."*
  (ADR-035 §3). Proactive when the read exposes a `category_locked`/paid-use
  signal; otherwise **reactive** — an attempted category change is accepted
  optimistically and the server's check_violation is surfaced as this same calm
  banner on save (never a raw error). Everything else in the editor stays fully
  editable.
- **validation** — inline, on blur not keystroke (`inline-validation`): name
  required; ≥1 item (server enforces both); item needs name + non-negative
  price. Errors as FieldError below the field; on a failed save, focus the first
  invalid field (`focus-management`).
- **save-error** — FormBanner above the bands (mapped copy); offline variant
  calm. No values lost.
- **push-to-live** (design-ahead, primarily sub-project C) — when a booth is
  attached to a **currently-live** gathering and its active version is newer
  than the imported snapshot, a quiet row offers *"[Event] is live — push your
  latest menu"* → `push_menu_template_to_live(team, gathering)` (O1 explicit
  push; attendees never see prices shift silently). Needs a "which live
  gatherings use this booth" read — §17.10; shown here only when that read
  confirms it, never speculatively.

**8. COLOUR LOGIC** (the hard rule, held exactly — this is a *management*
surface, calm by default)
- **`liveCoral` appears in exactly one truthful place:** the BoothCard spine (and
  any push-to-live row) **only** when the booth is attached to a gathering that
  is **live right now**. Nowhere else — a booth is durable data, not a live
  state. No coral on badges, headers, or the cap/lock affordances.
- **`emberGold`** — primary actions only: [+ New booth], [Save booth], [+ Add
  item], the CategorySelector's selected chip. Gold's action monopoly is intact;
  never coral.
- **`error` red** — the **Delete** confirm action only (destructive, ADR-014),
  separated from the neutral Cancel. **The at-cap and category-locked states are
  NOT red** — a plan limit and a rule-fixed category are honest facts, not
  failures; they use neutral `ink` + a lock/limit glyph (`color-not-only`).
- **`info` blue** — the calm offline/sync ribbon and SyncStatusChip offline
  variant.
- **`success` green** — the brief post-save tick, never coral.
- Neutral `ink` ramp — CategoryBadge, PlanCapMeter figures, cards, chrome, the
  Midnight background throughout.

**9. UX RULES** (constraints a developer must honour)
- **No sacred proof is on this path** — go-live, discovery, and the 3-tap ticket
  are untouched; the booths mode is a separate durable surface. Do not let it
  regress the entry into any of them.
- **Offline-first is a designed state, not an error** — the booth library reads
  from cache, the editor drafts locally and autosaves, and one reconcile save
  runs on reconnect. A vendor on 3G in a market must be able to build a booth
  with no bars.
- **Commit-once on save** — `save_menu_template` is versioned, NOT
  blind-idempotent (each call mints a new version); serialise one in-flight save
  per booth, disable-on-submit (`loading-buttons`). Never auto-retry a save in a
  loop.
- **Read-only ≠ disabled for the locked category** (`read-only-distinction`) —
  keep the value legible with a reason; never a greyed-out dead control.
- **Honest limits, no dark patterns** — at-cap explains and offers a real
  in-app action (edit/remove); no fake urgency, no phantom Upgrade button, no
  invented plan pricing (E is ADR-gated).
- **Vendor money is vendor-direct** — goods prices are the vendor's own
  (PriceInput); the platform credential fee is never editable/shown here; no
  wallet UI anywhere (SOUL red line 4 · Constitution §2.6).
- **Never fabricate a number** — *"used at N gatherings"* and PlanCapMeter render
  only from real reads; absent (not zero-faked) when unknown.
- **Touch & reorder** — ≥48dp rows/handles; reorder starts only past a
  `drag-threshold` via an explicit handle (no long-press-anywhere that fights
  scroll/tap-to-edit); remove is undoable.
- **Locked terminology** — semantic vendor surface: *booth*, *vendor
  company/team*, *gathering*; **Person**, never "user". (UI may say the category
  "Events" elsewhere, but this surface speaks vendor-domain nouns.)

**10. OPEN QUESTIONS & BACKEND DEPENDENCIES** (see the register below)
- **Client read of `vendor_status` + `plan`/cap** — REVOKED on `vendor_company`;
  the S075 migration explicitly defers exposing them to a
  **vendor-template-manager-flutter** follow-up that extends `get_vendor_company`'s
  `RETURNS TABLE` (+ echoes the RPC contract). **This blocks both the /vendor
  entry gate AND the at-cap affordance.** Recommend the read return
  `{ vendor_status, plan, max_active_templates, active_template_count }` so the
  client gates entry and shows "N of M booths" without hardcoding the free-tier 3.
- **No booth read RPC beyond the raw tables** — the list needs
  `{ template_id, name, vendor_category_id, category_name, version,
  used_at_count, category_locked, live_gathering_refs[] }`. `menu_template` /
  `vendor_menu_item` are RLS-readable, but *category name*, *used-at count*,
  *category_locked* (paid-use signal), and *which live gatherings use this booth*
  are derived reads not yet provided — recommend a curated `my_vendor_booths()`
  read RPC.
- **No template-level `tagline` / `photo` columns** — Core band's tagline/photo
  and the whole Settings band have no schema home; A1 persists **name + category
  + items[]** only. Everything else is design-ahead pending a schema follow-up.
- **No delete/deactivate-booth RPC** — `menu_template` is versioned+deactivated,
  never deleted (FK RESTRICT). "Delete" needs a `deactivate_menu_template(id)`
  RPC with the attached-to-upcoming/live-gathering guard the spec requires;
  **Duplicate works today** (a fresh-name `save_menu_template`).
- **`push_menu_template_to_live` needs its trigger context** — the RPC exists and
  is bound, but the manager needs the "this booth is live at gathering X, snapshot
  is stale" read to know *when* to surface the push affordance (primarily a
  sub-project C surface).
- **DPPA** — booth/menu data is business, not personal; the personal-data +
  consent + terms-retention surface lives in the A2 credential path, not A1.
- **Money/security posture** — the Settings "how buyers pay you" and any A2
  payment leg are ADR-026 / vendor-direct territory: zuka-security + zuka-legal
  before build; nothing in A1 touches the fee or settlement.

**11. CHANGELOG**
- *2026-07-22 (vendor sub-project A1):* Section created. Designed the durable
  Vendor Template Manager against the **built S075 backend** (ADR-035) — the
  `/vendor` mode (vendor_status-gated, `/host`-style), the booths list with the
  **plan-cap** at-cap affordance (honest, no fake upgrade), the **3-band editor**
  (Core / curated per-category Body for Food·Merch·Service / design-ahead
  Settings), the **category-lock** as a read-only (not disabled) state, and the
  **offline-first** autosave/sync taxonomy. Bound to real RPCs only
  (`save_menu_template`, `push_menu_template_to_live`, RLS reads); flagged the
  hard client-read dependency (`get_vendor_company` must expose
  `vendor_status`/`plan`/cap), the missing curated booth-list read, the
  no-tagline/photo/Settings schema gap, and the missing delete RPC. Applied the
  **ui-ux-pro-max** Flutter/App-UI pass (`read-only-distinction`, `form-autosave`,
  `disabled-states`, `empty-nav-state`, `drag-threshold`, `error-clarity`);
  ZUKA's colour law, motion tiers, and voice held throughout. Added shared
  components BoothCard · CategoryBadge · PlanCapMeter · CategorySelector ·
  ItemEditorRow · PriceInput · SyncStatusChip. No Bible deviation.
- *2026-07-24 (F035 — BUILT + REVIEWED, zuka-designer conformance):* the A1
  Flutter build shipped at **`/vendor/booths`** (a chrome-less mode ALONGSIDE the
  F032 `/vendor` "My events" list — the route moved from the spec's bare `/vendor`
  to avoid the live collision; editor routes `/vendor/booths/new` ·
  `/vendor/booths/:id/edit`). Entry via a new Account "Your vendor booths" row,
  `vendor_status`-gated. All §17 states reachable + copy-verbatim (empty/first-time,
  populated, at-cap **with no fake Upgrade**, error, category-lock read-only). Bound
  to the real S075/S077 RPCs only. zuka-reviewer PASS (ADR-028). **Logged deviations
  / refinements (all honest, none breaking design intent):**
  - **(a)** the curated 3-category Body schema is resolved by a **name heuristic +
    generic fallback**, because production `vendor_category` rows carry no canonical
    kind/slug (only a free-text `name`). A future backend `category_kind` column
    makes it exact; until then an unrecognised category falls back to a
    name+price+stock generic row (never a *wrong* schema).
  - **(b)** an **offline cold load** shows the truthful *"Offline — reconnect to see
    your booths"* (not §17's *"showing your saved booths"*) and **`[+ New booth]` is
    unavailable offline** — both because **A1 has no local-first booth cache**
    (deferred, §17.10). The library-cache + offline-draft-persistence state returns
    with that work.
  - **(c)** the editor's save-state chip says **"Unsaved changes"** / **"Offline —
    tap Save to retry"** rather than §17's *"Saved on this phone / saved here, will
    sync"* — the verbatim copy would falsely promise durable local persistence A1
    does not have (an honest-states / no-dark-pattern fix; the §17 copy returns with
    local-draft persistence). Post-save feedback is a **"Booth saved."** toast on
    the returned list; **item-row remove is undoable** via a SnackBar Undo (§17.9).
  - **(d)** **booth Delete has no Undo toast** (§17.3 flow): `deactivate_menu_template`
    is a one-way soft-deactivate with no reactivate RPC, so the human-confirm dialog
    + *"Removed X."* toast is the honest substitute (ADR-014).
  - **Tracked refinements carried (LOW, not blocking REVIEWED):** on-blur (vs
    on-submit) validation + focus-first-invalid-field; skeleton shimmer (T1); the
    load-error card uses `error` red for a genuine system failure (app-wide
    FormBanner parity, distinct from the neutral at-cap/lock states); the Food
    per-item *Note* field is plumbed (`attributes.note`) but the row renders only
    the one schema-specific field (per the ItemEditorRow "one schema-specific field"
    catalogue entry — a spec-internal tension to reconcile); the BoothCard shows a
    small coral *"Live now"* dot+label in the body in addition to the spine (a
    truthful live-state amplification for `color-not-only`, never decorative).

---

### §18 — Vendor Hub · Orders tab (C3) — retroactive design-conformance pass (F033)

*A retroactive `zuka-designer` pass over the **already-shipped** Orders-tab
surface (F033, commit `1fa91c8`, `lib/features/vendor/vendor_event_hub_screen.dart`),
closing review gate **F033-F1** (RF033, MEDIUM — new pixels shipped with no
design pass; RF032 predicted exactly this). This is a conformance verdict on a
live surface, not a redesign: the composition is token-clean, money-blind, and
holds the coral fence, so the findings below are refinements, not rebuilds.
Grounded in spec `2026-07-21-vendor-live-dashboard-design.md` §5/§7/§11
(functional, not layout) and the locked tokens (`zuka_colors/typography/spacing`).
C3 = the live queue **online** (accept · ready · reject + manual-token collect);
offline + camera scan are **C4** (deliberately out of scope here).*

**VERDICT — conforms; keep as shipped, with 7 tracked change-requests (all
refinements: 0 blocking, 2 MED, 5 LOW).** Money-blindness (SOUL red line 4) is
structural, not cosmetic — the only figure is the listed value framed as
"Collect at pickup," there is no PAID state, and a widget test asserts
"paid"/"Paid"/"Total" never render. The coral fence holds (coral absent — an
order is not a live gathering). Action affordances are gated 1:1 to the legal
server transitions. Nothing new was invented in the visual language; every piece
reuses the ratified tokens.

**1. PURPOSE** The vendor's live fulfilment heartbeat for ONE gathering: see the
orders coming in, move each through accept → prepare → ready, hand it over
against the buyer's pickup code, and reject cleanly when they can't fulfil.

**2. ENTRY POINTS** `/vendor` → "My events" row → the per-event hub (§ spec) →
the **Orders** tab (third of Status · Menu · Orders). No polling / Realtime —
**pull-to-refresh only** (ADR-021, Africa-first). The queue keeps its last list
visible during a refresh (reads `valueOrNull`), so a refetch never blanks the
screen.

**3. FLOW (as shipped)** New order lands (PLACED) → **[Accept]** (→PREPARING) →
**[Ready]** (→READY) → buyer shows their pickup code → vendor types it into the
top **Hand over an order** card → **[Collect]** (→COLLECTED). **[Reject]** on a
PLACED/PREPARING row opens the reason sheet; picking a reason commits it. Each
per-row action is one tap; collect is type-code + one tap. All well inside the
3–5 budget; **no sacred proof is on this path** (durable live-ops, not
discovery/go-live/ticket).

**4. SCREEN INVENTORY & COMPONENTS** One tab body (`_OrdersTab`) = a
pull-to-refresh `ListView` of: **ManualCollectCard** (pinned top) → **Live queue
(N)** header + **OrderCard**s → collapsed **Past orders (N)** ExpansionTile of
terminal OrderCards. Sheets/notes: **RejectReasonSheet**, `_OrdersNote`
(empty/error), `_InlineNote` (per-card success/error). New shared components —
**OrderStatusChip · OrderCard · OrderActionButtons · ManualCollectCard ·
RejectReasonSheet** — are catalogued in the Shared Component Library above.

**5. WHAT'S RIGHT (affirmed — do not change)**
- **Money-blindness is structural.** Only `listedValueUgx` shows, via the
  sanctioned `PriceText`, labelled "Collect at pickup," in neutral `ink` (no
  status colour, no motion). No PAID state exists in the model. Correct.
- **Coral fence held.** `liveCoral` appears nowhere on this surface — an order,
  even a brand-new one, is not a *live gathering*. `emberGold` is the correct
  maximum-urgency colour for a New order. Right call.
- **Action hierarchy is correct.** Forward actions (Accept/Ready) are `emberGold`
  filled primaries; Reject is an `error`-red *outline* — recessive and
  destructive-flagged, exactly the §17 Delete grammar. The eye goes to "move it
  forward," not to "deny."
- **Legal-transition gating.** Buttons render ONLY the transitions the server
  accepts (`legalActionsFor`), so no tap can earn a `23514`. READY exposes no
  per-row button (collection is token-bound) — correct, if under-cued (CR-4).
- **Closed reject vocabulary.** The reason sheet is EXACTLY the four backend
  codes, no free-text — DPPA-clean (no PII into the append-only reject payload).
- **Progressive disclosure of terminals.** Cancelled/Expired (`ink3`) and the
  rest fold into a collapsed "Past orders" expansion — the live queue stays the
  focus; the two muted `ink3` states reading identical is *fine* because the word
  disambiguates and they're low-emphasis-by-design.
- **Guest fallback + frozen line items** never fabricate data. Correct.

**6. CHANGE REQUESTS**
- **CR-1 (MED · colour + loud-venue legibility) — Preparing = `warning` is a
  semantic-token misuse and a glance collision with New = `emberGold`.**
  `warning` (#E08A2B) is defined as *caution — NOT emberGold*; "Preparing" is a
  healthy in-progress state, not a caution. Worse, `warning` and `emberGold`
  (#F5A623) are near-identical oranges, so a **New** pill and a **Preparing**
  pill differ only by 12px (`micro`) text — in a loud, dim venue at arm's length,
  the vendor scans colour first and cannot tell "act on this" from "already
  cooking." Recommend re-mapping **Preparing → `info` blue** (the one unused
  system token; reads as neutral "in progress," maximally distinct from gold and
  from green-Ready) so the queue reads as a legible ramp gold → blue → green.
  Owner: **design + founder**.
- **CR-2 (MED · type-token conformance) — the pickup-code field renders in
  `Inter body`, but codes are `Space Mono` (`data`).** Vol 25 Ch5 reserves Space
  Mono for "data · timestamps · counts · codes"; QRCard's `ticket_ref` already
  obeys this. The manual-collect `TextField` (and any on-screen token) should use
  the `data` style, plus `autocorrect:false` / `enableSuggestions:false` so a
  cheap keyboard doesn't mangle an opaque code. Owner: **design (build note)**.
- **CR-3 (LOW · state-set conformance) — cold-load uses a spinner, not the
  ratified skeleton.** The A-00 universal state set (and §1's "never a spinner on
  blank") makes skeleton cards the loading language. Recommend two–three OrderCard
  skeletons on genuine first load (refresh already keeps the last list, so this
  only bites cold entry). Owner: **design / build**.
- **CR-4 (LOW · discoverability) — a READY order in the live queue has no
  on-card cue toward collection.** It shows a green "Ready" chip and no button;
  the only collect affordance is the token card at the top. Add a quiet on-card
  line (*"Ready — enter the buyer's pickup code above to hand it over."*) so a
  new vendor isn't left guessing the next step. Owner: **design**.
- **CR-5 (LOW · founder ruling) — Reject commits on a single reason-tap, with no
  confirm and no explicit sheet-cancel.** This is asymmetric with §17's
  Delete-confirm dialog, yet reject is terminal, append-only (ADR-015), and
  denies a waiting buyer. Defensible for live-queue speed (the two intentional
  taps — Reject, then a specific reason — are the friction) but should be a
  *recorded* decision, not an accident. Add an explicit "Never mind" row to the
  sheet regardless. Owner: **founder (ruling)** + design.
- **CR-6 (LOW · Africa-first / memory) — the live queue builds eagerly.**
  `_OrdersTab` is a plain `ListView` with spread children (all live OrderCards
  materialise), and the Past-orders expansion builds every terminal card when
  opened. §17 deliberately used `ListView.builder` for exactly this reason. On a
  2GB Android over a long shift (dozens–hundreds of orders) this is a real
  footprint risk. Recommend lazy building once a queue can plausibly grow large.
  Owner: **design / build (verify against real queue sizes)**.
- **CR-7 (LOW · honest states) — no true offline state; a load failure reads as
  "wifi off."** The error note uses a `wifi_off` icon and offline-flavoured copy,
  but C3 has no cached queue (offline manifest is C4). This is spec-consistent,
  but the icon over-claims "you're offline" for what may be any RPC failure, and
  a vendor who loses signal mid-shift loses the whole queue view. Keep the
  blame-free retry copy but use a neutral error glyph until C4 brings the real
  offline surface. Owner: **C4 build** (record the gap now).

**7. STATES (as shipped)**
| State | Shipped behaviour | Verdict |
|---|---|---|
| default | ManualCollectCard + Live queue + collapsed Past orders | good |
| loading (cold) | centred `emberGold` spinner | **CR-3** (should be skeleton) |
| refreshing | last list stays visible (`valueOrNull`), spinner is the pull ring | good |
| empty (no orders) | `_OrdersNote` "No orders yet. When someone orders, it lands here." | good, on-voice |
| empty-live (only terminals) | "Nothing waiting right now. You're all caught up." | good — distinct 2nd empty |
| error | `_OrdersNote` wifi_off + "We couldn't load your orders — pull down to try again." | blame-free + retry good; **CR-7** icon |
| offline | none — collapses into `error` (C4 territory) | **CR-7** — record gap |
| per-card working | inline spinner on the tapped action, row disables (commit-once) | good |
| per-card error | `_InlineNote` `error`-red under the card | good |
| collect success | `success` "Collected — handed over.", self-clears on next keystroke | good |

**8. COLOUR LOGIC (the 7-state chip — verdict)**
| Status | Chip colour | Verdict |
|---|---|---|
| New (PLACED) | `emberGold` | correct — max urgency without breaking the coral fence |
| Preparing | `warning` | **CR-1** — semantic misuse + collides with New; → `info` blue |
| Ready | `success` | ok — "done my side, ready to hand over" |
| Collected | `success` | ok — terminal done; word disambiguates from Ready |
| Rejected | `error` | correct — a denial reads red even in Past orders |
| Cancelled | `ink3` | correct — muted terminal, word carries meaning |
| Expired | `ink3` | correct — muted terminal (identical to Cancelled by design) |
Money row: neutral `ink`, no status colour, no motion (money is colour- and
motion-silent). `liveCoral`: absent (fence held).

**9. COPY (affirmed, on-voice — direct, present, warm)** "Hand over an order" /
"Enter the pickup code the buyer shows you to mark it collected." · "Collect at
pickup" · "Collected — handed over." · empties and error as tabled. All pass.
One add from CR-4 (the Ready on-card cue).

**10. UX RULES (a developer must honour)**
- Money-blind forever: only the listed value, only as "Collect at pickup," only
  via `PriceText`. Never introduce a paid/received/total state or word.
- Never render an action the server would refuse — keep `legalActionsFor` the
  single source of the button set.
- Reject stays a closed four-code enum — no free-text path, ever (DPPA).
- Coral is forbidden on this surface (an order is not a live gathering).
- Pull-to-refresh only; no polling/Realtime; never blank the queue on refetch.
- Codes render in `data`/Space Mono (CR-2); loading is skeleton not spinner (CR-3).

**11. OPEN QUESTIONS** (filed in the register below)
- Is `order_qr_token` a long opaque token or a short human-typeable code? The
  whole manual-collect path's usability on a cheap keyboard in a loud venue turns
  on this; if long, C3's typed collect is painful and a short human code (or
  paste-friendly `data` field) is warranted before C4's camera lands.
- CR-1 (Preparing colour) and CR-5 (reject-confirm posture) need a founder ruling.

**12. CHANGELOG**
- *2026-07-22 (retro pass, closes RF033 F033-F1):* Section created as a
  conformance verdict over the shipped C3 Orders tab. **PASS** — money-blindness
  structural, coral fence held, action hierarchy and legal-transition gating
  correct, closed reject vocabulary, terminal progressive-disclosure right.
  Filed 7 change-requests (CR-1 Preparing=`warning` colour collision [MED]; CR-2
  code field should be `data`/Space Mono [MED]; CR-3 skeleton-not-spinner load;
  CR-4 Ready-row collection cue; CR-5 reject one-tap-commit ruling; CR-6 lazy
  list build for memory; CR-7 no true offline / mis-implying error icon) and two
  open questions (token typeability; two founder rulings). Catalogued five new
  shared components. No Bible deviation (coral, Person/Gathering terminology, and
  money posture all held).

---

### §19 — Vendor Discovery (attendee: booth feed + booth detail — B1)

*A design-conformance pass over the two **newly-shipped** attendee-facing screens
of F034 · B1 (`lib/features/vendor/attendee/vendor_feed_screen.dart`,
`vendor_booth_screen.dart`), read from the attendance-gated `vendor_feed` RPC.
This is the attendee's first sight of the vendor layer: "who's here selling, and
what do they have?" — read-only and payment-blind by design (ordering,
pay-at-booth, the composer are all **B2**). Distinct surface from the vendor's own
console (§11) and hub (§17/§18): a different audience (a checked-in guest), a
different RPC (attendance-gated read), a different status vocabulary
(BoothStatusChip's Open/Paused/Sold out). Grounded in the locked tokens
(`zuka_colors/typography/spacing`), the coral fence (FDES-02), and the money
posture (SOUL red line 4).*

**VERDICT — conforms; keep as shipped, with refinements (0 blocking, 2 MED,
4 LOW).** The two screens are token-clean, money-blind (menu prices are LISTED
values via the sanctioned PriceText, no cart / no "Order" / no total anywhere),
image-optional (initial-letter fallback), and hold the coral fence (a booth's
"Open" wears `success` green, never coral). The gate discipline is right — the
whole surface is reachable ONLY from a CHECKED_IN ticket, mirroring the RPC's
zero-rows-for-non-attendees contract, so a wall is never hit and an empty is never
an error. Refinements below sharpen legibility, tap targets, and the seam B2
builds on.

**1. PURPOSE** For a guest who is *already inside* a gathering: discover the
booths trading here and read each booth's menu. Answers "what can I get, and where
is it?" — not "let me order" (that is B2). A private, attendance-earned lens, not
part of zero-login discovery.

**2. ENTRY POINTS** Exactly one, by design: the **"See vendors"** affordance on a
**CHECKED_IN** ticket card (§8, `tickets_screen.dart`) → `context.push`
`/g/:gatheringId/booths`. The affordance renders ONLY when
`ticket.kind == checkedIn` — it mirrors the RPC gate (a non-checked-in caller gets
zero rows), so the button never leads to a dead screen. The booth detail is
reached only from a feed row (`/g/:gatheringId/booths/:presenceId`). Deep-linking
either route while not checked in resolves to the honest empty / booth-gone note,
never an error. (The live Gathering Detail (§6) is a sanctioned *future* second
entry — noted, not built.)

**3. FLOW (tap budget)**
```
CHECKED_IN ticket (§8) → [See vendors] ①  → Booth feed (list of BoothFeedCards)
                       → tap a booth     ②  → Booth detail (header + Menu, read-only)
                       → back               → feed (pull-to-refresh for live status)
```
2 taps from a checked-in ticket to a booth's menu. **No sacred proof is on this
path** (this is in-event live-ops discovery, not go-live / zero-login discovery /
the 3-tap ticket) — but it stays well inside the 3–5 budget regardless. Read-only:
the flow terminates at "read the menu"; there is no forward action (that is B2).

**4. SCREEN INVENTORY**
- **Booth feed** (`VendorFeedScreen`) — AppBar "Vendors" (`h3` `ink5`, `ink4`
  back) on Midnight; a pull-to-refresh `ListView.builder` of BoothFeedCards.
- **Booth detail** (`VendorBoothScreen`) — AppBar "Booth"; a `ListView`: header
  (BoothLogo · name · BoothStatusChip · category · location row) → "Menu" section
  of BoothMenuRows → optional `menu_truncated` note.

**5. COMPONENTS** BoothFeedCard · BoothLogo · BoothStatusChip · BoothMenuRow ·
PriceText (reused) · VendorInlineError + VendorStatusNote (reused from the §11
console — same note/error language across the vendor domain). All catalogued in
the Shared Component Library above; nothing new was invented in the visual
language.

**6. WHAT'S RIGHT (affirmed — do not change)**
- **Coral fence held, and the label makes it honest.** A booth open for business
  reads **"Open"** in `success` green, not "Live" in coral — because a *booth's*
  presence is not a *gathering* being live right now (FDES-02). The `BoothStatus`
  enum even documents this. Correct call; the question the brief raised is
  answered — **keep the green "Open", never coral.**
- **Money-blind, structurally.** Menu prices are LISTED values rendered only
  through PriceText (`0`→"Free", UGX integers, no decimals); there is no cart, no
  quantity control, no "Order", no total, no PAID state anywhere. B2's ordering
  surface is entirely absent, not stubbed. Correct.
- **Visible-but-labelled everywhere.** Paused/Sold-out booths and sold-out menu
  items stay *on screen*, labelled, rather than vanishing — the attendee sees the
  real state of the ground. Redundant cues (colour + word; strike + "Sold out"
  tag), never colour-only — WCAG-clean.
- **Image-optional (Africa-first).** BoothLogo degrades to an initial letter on
  null/empty/failed URL — never a broken-image glyph, never a shimmer loop.
- **Gate mirrored, not enforced client-side.** The client passes only ids and
  trusts the RPC's attendance gate; a zero-row result is the *expected* empty, not
  an error (SOUL red line 5 — discovery stays anon and untouched; this is a
  separate, earned surface).
- **Truncation is honest.** "Showing N of M — ask at the booth for the full menu"
  uses the server's real `menu_item_total`, never a fabricated count.

**7. REFINEMENTS (change requests)**
- **CR-1 (MED · tap target) — the "See vendors" entry may fall below 48dp.** In
  `tickets_screen.dart` the affordance is a `TextButton.icon` with
  `MaterialTapTargetSize.shrinkWrap`, `minimumSize: Size.zero`, and `xs` vertical
  padding — visually compact is fine, but the *hit target* must stay ≥48dp
  (Material / WCAG 2.5.5). Recommend enforcing a ≥48dp touch height on the
  affordance (transparent padding is acceptable) without growing its visual box.
  Owner: **design / build.**
- **CR-2 (MED · menu legibility · B1-or-B2) — the menu renders flat, ignoring the
  `category` each item already carries.** `VendorMenuItem.category` is modelled
  and server-ordered (`sort_order, name`) but `BoothMenuRow` never groups by it —
  a 30-item food menu is one long undifferentiated stack. Recommend grouping into
  quiet section headers (`caption`/`ink4`) when categories are present (fall back
  to flat when all null — never a fabricated "Other" heading). This mirrors §17's
  Food→section schema and pays off doubly when B2 adds quantity. Owner: **design.**
- **CR-3 (LOW · honest framing) — the menu has no "pay at booth" cue.** With no
  Order button and bare prices, a guest can reasonably wonder whether they pay
  in-app. Add one quiet caption under the "Menu" header (e.g. *"Prices are
  pay-at-the-booth."*) so B1 sets the correct expectation and B2's flow feels
  continuous rather than a surprise. Owner: **design.** *(Keep it a statement of
  fact, not a nudge — no urgency, no "order now soon" teaser.)*
- **CR-4 (LOW · state-set conformance) — skeletons are calm static bars, not the
  A-00 skeleton language.** The feed's three 72px bars and the detail's
  header+cards bars are honest and Africa-first (no shimmer = good on 2GB), but
  they are generic rectangles, not content-shaped BoothFeedCard/BoothMenuRow
  silhouettes, and there is no skeleton→content cross-fade (§ Motion T1). Low
  priority — acceptable as shipped; revisit for consistency when the skeleton
  language is unified across §1/§18/§19. Owner: **design / build.**
- **CR-5 (LOW · freshness) — booth detail has no pull-to-refresh.** The detail
  reads from the cached feed (`boothByPresenceProvider`), so a booth's
  status/menu can go stale while the attendee sits on the detail (a booth pauses,
  the guest still sees "Open"). The feed refreshes on pull; the detail does not.
  Acceptable for B1 (back-then-refresh covers it, and stale-then-corrected is
  low-harm on a read surface), but note it — and it becomes more important in B2
  (ordering a sold-out item). Recommend adding pull-to-refresh to the detail when
  B2 lands. Owner: **design / build (B2).**
- **CR-6 (LOW · offline honesty) — no designed offline state.** Like §18 (CR-7),
  a load failure collapses into VendorInlineError; there is no cached-feed offline
  surface. Reasonable for an in-venue read where the guest is physically present
  and likely on some signal, but record the gap: if the vendor layer ever needs to
  survive a signal drop mid-event, this surface wants the §1/§8 stale-cache-with-
  as-of-time treatment. Owner: **record now; revisit with the offline manifest
  work (C4-adjacent).**

**8. STATES (as shipped)**
| State | Shipped behaviour | Verdict |
|---|---|---|
| feed · loading | 3 static `ink1` bars (72px) | ok — **CR-4** (not content-shaped) |
| feed · default | BoothFeedCards, pull-to-refresh (gold ring / `ink1` bg) | good |
| feed · refreshing | RefreshIndicator; list stays visible | good |
| feed · empty / not-checked-in | VendorStatusNote "No vendors live yet. Check in at the gate…" | good — one copy serves both the true-empty and the (edge) non-attendee case; never an error |
| feed · error | VendorInlineError + Retry (`invalidate`) | good, blame-free |
| feed · offline | none — collapses into error | **CR-6** (record gap) |
| detail · loading | header + 2 card bars | ok — **CR-4** |
| detail · default | header + Menu rows | good |
| detail · empty menu | VendorStatusNote "This booth hasn't posted a menu yet." | good, on-voice |
| detail · truncated menu | "Showing N of M — ask at the booth…" (real counts) | good |
| detail · booth-gone | VendorStatusNote "This booth isn't listed right now — head back…" | good — honest, not an error |
| detail · error | VendorInlineError + Retry | good |
| menu item · sold-out | name lineThrough + "Sold out" tag + dimmed | good — redundant, not colour-only |

**9. COLOUR LOGIC**
| Element | Colour | Verdict |
|---|---|---|
| Booth "Open" chip | `success` green | correct — a booth open ≠ a gathering live; coral fence held |
| Booth "Paused" chip | `warning` amber | correct here — "Paused" IS a caution/attention state (can't buy now), unlike §18's misuse of `warning` for a healthy "Preparing" (CR-1 there); the semantics fit |
| Booth "Sold out" chip | `ink4` muted | correct — a spent state, low-emphasis |
| Menu price | neutral `ink` via PriceText | correct — money is colour- and motion-silent |
| Sold-out item | `ink4` + strike | correct — availability, not price theatre |
| `liveCoral` | absent | fence held — a booth is not a live gathering |
Background Midnight; cards `ink1` on `ink2` border (Ember-Edge family, chip-not-spine variant).

**10. UX RULES (a developer must honour)**
- Reachable ONLY from a CHECKED_IN ticket (and, later, live Gathering Detail);
  never from the zero-login feed. The entry affordance must mirror the RPC's
  attendance gate.
- A zero-row feed is an **empty**, never an **error** (the gate is silent and
  non-leaky by design).
- Payment-blind forever in B1: LISTED prices only, via PriceText; no cart, no
  quantity, no "Order", no total. Ordering is B2 and lands as a *new* surface.
- Coral is forbidden here (a booth's presence is not a live gathering); "Open"
  stays `success` green.
- Images are optional — the initial-letter fallback is the floor; never a broken
  image, never a blocking image load.
- Every status is visible-but-labelled with a redundant word cue (never
  colour-only).
- Tap targets ≥48dp (CR-1), including the compact "See vendors" entry.

**11. WHAT B2 (ordering · pay-at-booth) BUILDS ON**
- **Where the Order affordance lives:** recommend a booth-level composer opened
  from the booth detail (a bottom sheet cart), NOT an in-feed action — keep the
  feed a scan surface. Sold-out items (already struck) must be non-addable.
- **Pay-at-booth framing:** CR-3's caption is the honest seam — B2 continues it
  ("Pay at the booth when you collect"), echoing §18's "Collect at pickup"
  vocabulary so buyer- and vendor-side language agree.
- **The pickup token:** B2 binds an order to `vendor_presence_id` (already the
  detail's address) and mints the `order_qr_token` that §18's manual-collect types
  by hand — the **open question on token typeability (register)** must be resolved
  before B2's buyer-side surfaces it (a long opaque token is painful to read
  aloud / type in a loud venue).
- **Coral fence + money-blindness continue:** B2 adds a cart and a paid intent but
  vendor money is vendor-direct (SOUL red line 4 / money posture) — the buyer
  surface shows LISTED values and a "pay at booth" posture; no ZUKA-aggregated
  total, no coral.
- **Category grouping (CR-2) pays off more with quantity** — worth landing in B1
  or as B2's first move.

**12. OPEN QUESTIONS** (filed in the register below)
- Booth-detail freshness (CR-5) — pull-to-refresh on detail, needed by B2.
- Offline posture for the vendor layer (CR-6) — record; revisit with offline work.
- `order_qr_token` typeability — shared with §18; blocks B2 buyer-side collect.

**13. CHANGELOG**
- *2026-07-23 (F034 · B1 design-conformance pass):* Section created over the two
  newly-shipped attendee screens (booth feed + booth detail). **PASS** —
  money-blind and cart-free (B2 absent, not stubbed), coral fence held (booth
  "Open" = `success` green, confirmed correct per the brief's question),
  visible-but-labelled statuses with redundant cues, image-optional with
  initial-letter fallback, attendance-gate mirrored (empty ≠ error). Catalogued
  four new shared components (BoothLogo · BoothStatusChip · BoothFeedCard ·
  BoothMenuRow), distinguishing the attendee BoothFeedCard from §17's manager
  BoothCard. Filed 6 refinements (CR-1 ≥48dp entry tap target [MED]; CR-2 menu
  category grouping [MED]; CR-3 pay-at-booth caption; CR-4 content-shaped
  skeletons; CR-5 detail pull-to-refresh; CR-6 no offline state) and a B2
  build-on note. No Bible deviation (coral fence, Person/Gathering terminology,
  and money posture all held).

---

## Bible Deviations Register
| Date | Bible rule | What we did instead | UX reason | Ying's call |
|------|-----------|---------------------|-----------|-------------|
| 2026-07-25 (§22) | **Midnight is the base background** (Vol 25 Ch4) — every surface in the app is dark | The Smart Bucket credential paints **dark modules on a light `ink5` chip** — a 232dp light rectangle, the only light surface in ZUKA | A QR needs dark-on-light contrast to scan. A cheap gate camera at arm's length in a dark Kampala venue is the design target; inverted (light-on-dark) codes fail on low-end sensors. Scannability is functional, not stylistic — the door beats the palette. Confined to the code chip itself; the card, ring and all chrome stay Midnight. | ratify (already shipped, F037) |

---

## Open Questions & Backend Dependencies
| Date | Question / dependency | Blocks | Owner |
|------|----------------------|--------|-------|
| 2026-07-04 | Live-status: **resolved → poll a 30s edge-cached snapshot at MVP**; Realtime push is a DRL-later transport swap (client model unchanged) | Live Now auto-update | backend |
| seed | Venue geo + crowd signals | Heat-map view (§2 pins → heat) | backend |
| 2026-07-04 | Signal ingestion: per-source ToS + DPPA + misinformation clearance | Signal engine Phase 1 (feed seam is unblocked) | zuka-legal |
| 2026-07-04 | `Feed*` interaction event schema (append-only, anon-aggregate) | DRL-3 personalisation/fairness substrate | backend |
| 2026-07-04 | Exposure-fairness cards-per-organiser cap (N) | first-screen ranking | backend + design |
| 2026-07-07 | ~~Person-id resolution after sign-in~~ **RESOLVED (F003)**: the client resolves its own `person.id` via a `person_select_own` lookup on session establish (`AuthRepository._resolvePersonId`), and `register_person` returns it directly at sign-up | §5 Create → Go Live | ~~backend~~ done |
| 2026-07-07 | ~~Person-record provisioning — 42501 if no `person` row~~ **RESOLVED (F003)**: self-serve `register_person` RPC provisions a linked Person at sign-up (§5D); the F002 dev seed is now a convenience, not a requirement | §5 whole flow | ~~founder + backend~~ done |
| 2026-07-07 | Venue list source for VenuePicker; behaviour when organiser's venue isn't listed → **ADDRESSED (F021 §16)**: create-via-pin-drop in-sheet (`create_venue`, S051). Search-list source itself still a dependency | §5B Create form | backend |
| 2026-07-20 | **`list_my_venues()`** read RPC (own **active** venues: id · name · address, creator-scoped RLS) — S051 shipped create/deactivate but no own-venue read | §16 Manage/deactivate mode build (create mode ships without it) | backend |
| 2026-07-20 | No **reactivate** RPC — `deactivate_venue` has no inverse; a mis-deactivation is unrecoverable in-app (hence §16's terminal error-red confirm). Founder call on whether reactivation is needed | §16 deactivate reversibility | founder + backend |
| 2026-07-20 | Does an unpublished **DRAFT** gathering count toward `deactivate_venue`'s no-orphan `23514` block? Confirm the predicate (published live/upcoming only vs any referencing gathering) | §16 deactivate safety | backend |
| 2026-07-20 | Export the **Uganda-bbox constant** once so §16's client out-of-Uganda mirror and the server `23514` share one boundary (never disagree) | §16 pin-drop pre-check | backend |
| 2026-07-07 | ~~Idempotency key (or reconcile/"my gatherings" view) for `create_gathering` — closes the submit-ambiguity gap on 3G timeouts~~ **RESOLVED (S048)**: `p_client_key` idempotent converge on `create_gathering` + `create_tier`; the reconcile/"my gatherings" view stays open as `hosting-your-gatherings-rpc` | §5 create safety | ~~backend~~ done |
| 2026-07-10 | ~~Free path missing → `claim_free_ticket(p_gathering_id, p_tier_id)`~~ **SHIPPED (S028, 2026-07-11)**: RPC live, price-space partition proven both ways, cap race + teeth green | §7 free branch · M0 free-first launch | ~~backend~~ done |
| 2026-07-10 | `gathering_detail(p_gathering_id)` read RPC — gathering + venue + public tiers in one round-trip (API law 10) | §6 build | backend (S029) |
| 2026-07-10 | `my_tickets()` RPC — serves §7 PendingMeter poll + §8 list; direct `ticket_select_own` read sanctioned interim-only | §7/§8 build | backend (S029) |
| 2026-07-10 | `url_launcher` + pure-Dart QR painter = NEW dependencies → dep-ADR (ADR-030 candidate) before the Flutter purchase build | §7/§8 Flutter build | founder + backend (S029) |
| 2026-07-10 | Distinct sold-out ZK code (ZK410) so the wrapper can say waitlist-honest copy; today `check_violation` = blunt copy | §7 sold-out copy | backend (S029) |
| 2026-07-10 | TTL ruling: PENDING 30 min vs real MoMo confirm latency — 60–90 min recommended (R009-F2) | §7 EXPIRED-mid-payment surface | founder |
| 2026-07-10 | Paid-gating ruling: visible-but-gated (recommended §6.7/§7.6) vs hidden, until ADR-026 counsel clears | §6/§7 paid tier presentation | founder + zuka-legal |
| 2026-07-10 | Offline-QR stale-token display window → real zuka-security gate at the §8 BUILD session (RFDES01-F5) | §8 build | zuka-security |
| 2026-07-10 | `save_gathering`/`unsave_gathering` RPCs + saved-list read — nothing exists | §4 build (Saved) | backend |
| 2026-07-10 | `follow_target`/`unfollow_target` RPCs (venue + organiser) + following read (entity + live/next state) — nothing exists | §4 build (Following) | backend |
| 2026-07-10 | "Went live" push fan-out on `go_live` + FCM token storage + push-consent record (DPPA consent, F003-style; grant/revoke ledger; FCM cross-border disclosure — counsel wording) | §4 push moment | backend + zuka-legal |
| 2026-07-10 | ~~**Dedupe ruling (S028 plan-gate):** per-gathering vs per-tier — §7.2b copy CONTINGENT~~ **RULED per-gathering (S028, 2026-07-11)**: one free ticket per (Person, Gathering) keyed on `original_purchaser_id`; repeat = `ZK409`. §7.2b copy de-contingent'd; `ZK409`→copy `ZukaRpcClient` mapping batches with S029 (ZK410) | ~~§7.2b free-claim copy~~ done (map S029) | ~~founder~~ done |
| 2026-07-10 | Camera + QR-decode package (scanner) — new dep, fold into the ADR-030 candidate with `url_launcher`/QR-painter | §10 build | founder + backend (ADR-030) |
| 2026-07-10 | **Scanner session deactivate/PIN-rotate RPC — M0 SECURITY DEPENDENCY** (security gate MED: a leaked code+PIN cannot be revoked mid-event; [Close this gate] is device-local until this lands) | §10 leak response | backend (M0) |
| 2026-07-10 | Drop `person_id` from `check_in_ticket`'s anon-facing return (security gate MED; §10.10 client discard rule holds the line meanwhile) | §10 build · Identity Graph | backend |
| 2026-07-10 | ZK423 (PIN lockout) copy → register in the RPC-contract error map at the §10 build | §10 build | backend + design |
| 2026-07-16 | No reopen-from-CLOSED RPC — CLOSED is terminal (S023); N1 dialog states the irreversibility. A "reopen" would need a new RPC + a state-machine ruling | §11 close-confirm / state machine | backend + founder |
| 2026-07-16 | Vendor presence is created ONLY by gate check-in — no self-serve "open my stall before the gate" path; registered-but-unchecked vendor sees the no-presence note | §11 live-status availability | backend (confirm M0 intent) |
| 2026-07-16 | First-party in-app entry point to `/g/:id/vendors` is deferred — console is deep-link/route-only; where the vendor affordance lives (a `/host`-style entry, a vendor-team home) is unspecified | §11 discoverability | design + founder |
| 2026-07-16 | Vendor-credential payment/settlement — register mints a PAYMENT_PENDING credential + amount owed; the MoMo/Flutterwave settlement ("Payment opens soon") is a separate money-gated session, not built | §11 register → pay | backend (money-gated) + zuka-security |
| 2026-07-22 | **Extend `get_vendor_company()`** to expose `vendor_status` + `plan` + a derived cap (recommend `{ vendor_status, plan, max_active_templates, active_template_count }`) — S075 REVOKED direct `vendor_company` SELECT and deferred this to the flutter session. **Blocks the `/vendor` entry gate AND the at-cap affordance** | §17 entry gate + PlanCapMeter/at-cap | backend (vendor-template-manager-flutter) |
| 2026-07-22 | **Curated `my_vendor_booths()` read RPC** — the booths list needs derived fields the raw tables don't give: category *name*, *used-at count*, `category_locked` (paid-use signal, for the proactive locked state), and *which live gatherings use this booth* (for push-to-live). Raw `menu_template`/`vendor_menu_item` are RLS-readable but underived | §17 booths list + editor category-lock + push-to-live | backend |
| 2026-07-22 | **No template-level `tagline`/`photo` columns + no Settings schema** — `save_menu_template` persists name + category + items[] only; the Core tagline/photo and the whole Settings band (accept-orders, prep-time, "how buyers pay you") are design-ahead pending a schema follow-up ("how buyers pay you" is also money-posture/legal-gated, vendor-direct only) | §17 Core photo/tagline + Settings band | backend + zuka-legal/security |
| 2026-07-22 | **No delete/deactivate-booth RPC** — `menu_template` is versioned+deactivated, never deleted (FK RESTRICT). §17 Delete needs `deactivate_menu_template(id)` with an attached-to-upcoming/live-gathering guard. (Duplicate works today via a fresh-name `save_menu_template`) | §17 BoothCard Delete | backend |
| 2026-07-22 | **Paid per-event attach (A2) rides the unbuilt paid-purchase client rails** — Flow 2 (booth-first pick → server category-match `register_vendor_credential(…, p_menu_template_id)` → terms → Flutterwave leg) waits on `paid-ticket-purchase-client`; A1 (this manager) ships independently, no payment dependency | A2 (separate section) | backend (money-gated) + zuka-security |
| 2026-07-22 | **Is `order_qr_token` a long opaque token or a short human-typeable code?** §18's manual-collect path (`collect_vendor_order` binds by token) is typed by hand in C3, on a cheap keyboard in a loud venue — a long token makes it error-prone. Confirm the format; if long, warrant a short human pickup code and/or a paste-friendly `data`/Space Mono field before C4's camera scan lands | §18 manual-collect usability | backend + design |
| 2026-07-22 | **§18 CR-1 — Preparing status chip uses `warning` (a caution token), which is near-identical to New's `emberGold`** → two orange pills the vendor can't tell apart at a glance. Recommend re-map Preparing → `info` blue (gold→blue→green ramp). Founder colour ruling | §18 OrderStatusChip legibility | design + founder |
| 2026-07-22 | **§18 CR-5 — Reject commits on a single reason-tap, no confirm** (asymmetric with §17's Delete-confirm) on a terminal, append-only, buyer-affecting action. Defensible for live-queue speed but should be a recorded decision. Founder ruling | §18 RejectReasonSheet friction | founder |
| 2026-07-25 | **§22 FOUNDER CALL — the TicketStatusPill's PENDING colour + ISSUED/CHECKED_IN labels.** The shipped pill (`warning` / "Payment processing" / "Ticket ready" / "Checked in") reverses ratified **A-03** (2026-07-18: `emberGold` / "Waiting for payment" / "You're going" / "You were in") and the shared **TicketStatusBanner** entry (2026-07-10, gold + "Waiting for payment"). §22.12 rules **restore A-03**. Founder confirms the restore (or ratifies the shipped labels, which would amend A-03) — RF031 MED-1, now entrenched in a two-screen shared widget | §22 + §8 tickets list · every screen showing a ticket state | founder + design |
| 2026-07-25 | **§22 FOUNDER CALL — coral grammar on the pill (RF031 MED-2, still open).** A-03 renders live as its own coral **LIVE** pill (word carrier); the build merges a bare 6dp coral dot inside the state chip — live communicated by **colour alone** (WCAG 1.4.1). §22.12 recommends: A-03's separate LIVE pill in list rows, **no dot at all** on the credential card header | §22 + §8 list rows | founder + design |
| 2026-07-25 | **§22 — `my_tickets()` exposes no check-in timestamp.** Design §3.4 specifies CHECKED-IN as "dimmed check **+ time**"; the built card can only say "You're in." A `checked_in_at` column on the `my_tickets()` row would complete the state (and let the pill tense itself without clock guessing) | §22 CHECKED-IN state completeness | backend |
| 2026-07-25 | **§22 — no SIGNED-OUT state.** A signed-out deep link to `/tickets/:id` resolves through `myTicketsListProvider`'s empty-list-for-anon rule into **EMPTY** — "We can't find that ticket in your bucket", which is false. Needs its own state (§22.6): *"Sign in to open your bucket."* + [Sign in]. Small client change, no backend | §22 deep-link correctness | design (spec'd) + flutter |
| 2026-07-25 | **§22 — no offline credential, by construction today.** The provider is `autoDispose` with no persistence, so a holder who loses signal at the gate has no code. SB-2 closes it. The forward guard is spec'd in §22.6/§22.11: a cached bucket credential **must never** carry A-03's "Saved Xh ago" staleness ribbon — that ribbon is legal for the legacy static per-ticket QR and is a **freshness channel** on a rotating bucket credential | §22 offline state · SB-2 | backend (SB-2) + zuka-security |
| 2026-07-22 | **§18 CR-7 — Orders tab has no true offline state** (C3 has no cached queue; offline manifest + camera collect are C4, S027). A load failure currently reads as "wifi off." The tab is online-only today, unlike §17's offline-first manager — recorded as a state gap C4 closes | §18 offline queue | C4 build (backend/design) |
| 2026-07-24 | **§23 — owner-scoped outstanding-balance READ** — to show the amount BEFORE commit (full-price-before-commit, no dark pattern) the /vendor entry + the Channel-B notice need a read of the own-company `commission_debt_ledger` outstanding balance. The gate-lock flag supplies X at scan time, but the console/checkout surfaces need their own read. Confirm the RPC (and that it exposes ONLY the balance, not other companies') | §23 balance display + Channel-B notice | backend (VS-integ) |
| 2026-07-24 | **§23 — settlement-status poll/Realtime read** — SettlementPendingMeter must resolve SETTLED/FAILED/HELD after backgrounding via the webhook, so it needs a client poll (like §7 `my_tickets`) or a Realtime channel keyed to the `tx_ref`/company. Confirm the read that surfaces the three terminal states to the vendor | §23 pending → completion | backend (VS-integ) + zuka-security |
| 2026-07-24 | **§23 — HELD is an unstyled real state, not an error** — confirm `complete_vendor_fee_settlement` surfaces HELD distinctly (vs FAILED) to the client, and that no client-visible field leaks WHY it was held (overpaid/double-pay is reconciliation detail, not vendor-facing). Also confirm no auto-refund copy is implied (money is held, not returned) | §23 HELD copy honesty | backend + zuka-legal |
| 2026-07-24 | **§23 — masked-number source of truth** — does `get_vendor_payment_profile` return numbers pre-masked (`077 ••• 456`) + an opaque selection token (so raw secondary/third-party msisdns never sit in the client, DPPA minimisation), or raw numbers the client masks? The STK push needs the full number server-side; only "different number" free-entry should put a raw msisdn in the client | §23 PayFromPicker privacy | backend + zuka-legal |
| 2026-07-24 | **§23 — `kyc_required` owner-read is boolean-only** — the owner affordance must read ONLY "verification required = true/false", never the reason/match/triggering company (trust-state RLS non-exposure invariant). Confirm the narrow owner-scoped read exists and exposes nothing else. Also: there is **no real KYC flow yet** — VerificationRequiredNote is a placeholder that gates nothing today; confirm the flag is not (yet) wired to block settle/register | §23 VerificationRequiredNote | zuka-security + founder |
| 2026-07-24 | **§23 — Channel-B `bundled` opt-in wiring** — VS-2 builds the `bundled` purpose (credential + balance in one charge). Confirm the client can offer it as an *explicit, unticked* second choice on the new-credential checkout (never auto-bundled) and that declining it still completes the credential purchase cleanly | §23 OutstandingBalanceNotice | backend (VS-2) + founder |
| 2026-07-24 | **§23 — first-party entry to the Settle-balance screen** — inherits §11's deferred vendor-affordance question. The gate-lock flag and the /vendor console/account are the two entry points named in the spec; where the /vendor "account" surface with the balance banner lives (vs the §17 booths mode) needs a placement ruling | §23 entry points | design + founder |

---

### §20 · A-20 Poster Templates + §21 · A-21 Tier-Specific Ticket Templates (round-trip, 2026-07-24)

> Source of the visual: `docs/design/mockups/a-20-a-21-resource-templates/` (dc-runtime
> design source — see that README; not self-contained). Brief:
> `docs/design/prompts/ZUKA_DESIGN_BRIEF_A20_A21_resource_templates.md`. Review verdict +
> open items: `docs/session-reviews/A20-A21-template-review.md`. Backend/engine: ADR-037,
> T1 built (`lib/features/templates/`), T2 (ticket-preview) blocked on ADR-037 O4.

## A-20 · Poster templates

### A-20.1 Flow
1. Organiser publishes a Gathering (60-second Go Live — no template step).
2. **After** go-live: Gathering settings → "Poster" → pick one of three styles → optional hero photo drop → title auto-filled, editable (max 60).
3. Poster renders on-device as the 4:5 Detail hero, for logged-out Persons too (zero-login discovery).

### A-20.2 Components
- **Styles (3):** `midnight` (ink0 ground, ink5 text, emberGold accent — default), `ember_gold` (emberGold ground, midnight text/blocks), `kampala_forest` (forest ground, ink5 text; tints of ink5 only, no gold).
- **Composition:** V1 "type block" is canonical (photo-less first). V2 "photo forward" (full-bleed top image) offered only when a photo exists; degrades to V1. V3 "split band" documented, not shipped. Composition is per-style; organisers never rearrange slots.
- **Slots:** brandmark (locked, assets/brand/) · hero_photo (editable, optional, ≤80KB) · title (editable, max 60, clamp 4 lines / 2 with photo) · datetime (locked, Space Mono) · venue (locked, 1 line + ellipsis) · price (locked, "from UGX n,nnn" = min tier) · qr (locked, deep-link painter).

### A-20.3 States
default (with/without photo per style) · loading = **static skeleton** (no shimmer) · empty = no template chosen → heroless Detail, publishing never blocked · offline = full render from cache; uncached photo → photo-less composition · error = photo load failure → photo-less composition, no broken-image chrome · success = "Poster attached to the Gathering", instant.

### A-20.4 Copy
QR caption: **"Scan to open in ZUKA"** (never entry language). Empty state: "No poster — and that's fine." Title truncation is a belt-and-braces clamp; input capped at 60.

### A-20.5 Colour logic + depth
No coral, ever (A-09 law). emberGold appears only in `midnight` (accent + aura) and as `ember_gold` ground. kampala_forest is gold-free (ink5 tints). success/warning/error/info appear only in app chrome around templates, never inside them. Depth: single ambient shadow, linear sheen + vignette, 24px hairline grid texture, concentric circle motif bleeding off the top-right — all linear gradients and simple geometry.

### A-20.6 UX rules
4:5 at 320px in the 360 frame; body ≥16px, nothing <12px; venue never wraps; all states static; QR is a deterministic placeholder pattern, never scannable.

### A-20.7 JSON spec sketches

```jsonc
{
  "layout": "poster.4x5",
  "palette": "midnight",
  "tier_class": null,
  "composition": {"default": "type_block", "withPhoto": ["type_block", "photo_forward"]},
  "slots": [
    {"id":"brandmark","kind":"brand","editable":false,"locked":true,"source":"assets/brand/"},
    {"id":"hero_photo","kind":"image","editable":true,"optional":true,"source":"fill|gathering.poster_object_path","maxKB":80},
    {"id":"title","kind":"text","editable":true,"source":"fill|gathering.title","maxLen":60,"clampLines":{"noPhoto":4,"withPhoto":2}},
    {"id":"datetime","kind":"text","editable":false,"source":"gathering.starts_at","format":"EEE d MMM · h:mma","font":"data"},
    {"id":"venue","kind":"text","editable":false,"source":"gathering.venue_name","truncate":"1line-ellipsis"},
    {"id":"price","kind":"text","editable":false,"source":"derived.min_tier_price","format":"from UGX n,nnn","font":"data","tabular":true},
    {"id":"qr","kind":"qr","editable":false,"locked":true,"painter":"deeplink","label":"Scan to open in ZUKA"}
  ],
  "colorSet": ["midnight","ink5","emberGold"],
  "locked": ["typography","spacing","qr","brandmark"],
  "brandLaw": {"noCoralOnPosters": true, "qrGrantsNothing": true, "static": true}
}
```

`ember_gold`: same slots; `"palette":"ember_gold"`, `"colorSet":["emberGold","midnight","ink5"]` (price block = midnight fill, ink5 text; all text midnight).
`kampala_forest`: same slots; `"palette":"kampala_forest"`, `"colorSet":["kampalaForest","ink5"]` (accents are ink5 tints only).

### A-20.8 Backend dependencies
- `gathering.poster_object_path` cached for offline; ≤80KB enforced at upload.
- Derived `min_tier_price` available on the public (logged-out) Gathering read.
- Deep-link QR painter: unique per Gathering, opens app / falls back to store. **Grants nothing.**

---

## A-21 · Tier-specific ticket templates

### A-21.1 Flow
1. Person taps a tier in the buy sheet → tier is rendered **as its ticket** (preview, not credential). Zero added taps (3-tap proof).
2. Buy → instant confirmation → "Your entry QR is in your Bucket." Real QR only in the Smart Bucket.
3. Organiser side: tier → "Ticket design" → picker offers **only designs of the tier's class**.

### A-21.2 Components
- **Classes (4) — the luxury ladder (2026-07-24 founder direction: vibrant, graded high-end feel):**
  - `ordinary` — quiet ink1 card, top sheen, hairline-textured stub. The honest baseline.
  - `vip` — noir foil: ink0 gradient ground, **gold double hairline frame**, die-cut corners (clipper), ghost VIP letterform (≤8% gold), foil-gradient wordmark, gold price. V1 "noir foil" recommended; V2 "flat signature rule" (budget render) and V3 "inverted" documented as named variants.
  - `table` — **kampalaForest ground** (grounded/communal, borrowed from the poster anchor), gold **seat-tick band** (N ticks), ghost ×N watermark, gold "ADMITS N" chip, price **per table** ("UGX 800,000 · per table").
  - `vendor` — hatched safety strip + rotated **VENDOR band** over a crosshatched utility card; credential feel, no gold. (Platform taxonomy: the brief's `other` class is administered as **Vendor**; see Open items.)
- **Depth & texture system:** one ambient shadow per card (single, never stacked), linear sheen overlays, hairline/security-line textures, ghost letterforms, die-cut corners — all flat linear-gradient/typography primitives; no blur, no bitmap texture, spec-expressible in Flutter painters.
- **Stub structure:** V1 right stub + dashed tear + edge notches (recommended); V2 bottom tear band; V3 notch-only solid hairline. Structure, not texture — flat fills, no paper skeuomorphism.
- **Serial:** painter `preview_serial` (e.g. PRV-0425), Space Mono, no entry value.

### A-21.3 States
default ×4 classes · loading = static skeleton · empty = no tiers → buy sheet hidden · offline = full cached render (buying waits; preview doesn't) · error = spec load failure → plain fallback row (name/title/price) — purchase path never breaks for decoration · success = "Ticket secured — your entry QR is in your Bucket", instant · sold out = neutral SOLD OUT chip (capacity 0), price stays visible · stacked list of 3 in buy sheet · picker with blocked mismatch.

### A-21.4 Copy
QR: **"Open in ZUKA"** — never "scan to enter". Blocked picker (honest, non-punitive, no upsell):
- ordinary → "Ordinary designs are for Ordinary tiers"
- vip → "VIP designs are for VIP tiers"
- table → "Table designs are for tiers that seat more than one"
- vendor → "Vendor designs are for vendor tiers"

### A-21.5 Colour logic
Graded grounds: ordinary/vendor = inks · vip = ink0 + emberGold foil (gold = energy/premium action, never caution) · table = kampalaForest (grounded/communal — an aesthetic anchor like the forest poster style, not a success colour). No coral anywhere. Sold-out uses neutral inks (fact, not warning).

### A-21.6 UX rules
Stub 5:2 at 320px in a 360 column; every slot locked; money Space Mono tabular, never animates; tap targets ≥44px (stub rows 128px, buy bar 48px, picker button 44px); locked picker rows: 45% opacity + lock glyph, non-interactive.

### A-21.7 JSON spec sketches

```jsonc
{
  "layout": "ticket.stub",
  "palette": "ink",
  "tier_class": "ordinary",            // binding ENFORCED; set by platform administration
  "structure": {"stub": "right", "tear": "dashed", "notches": true},
  "slots": [
    {"id":"tier_name","kind":"text","editable":false,"source":"tier.name","font":"data"},
    {"id":"admit","kind":"text","editable":false,"source":"tier.seats_per_unit","format":"ADMIT {n}"},
    {"id":"title","kind":"text","editable":false,"source":"gathering.title","truncate":"1line-ellipsis"},
    {"id":"meta","kind":"text","editable":false,"source":"gathering.starts_at + gathering.venue_name","font":"data","truncate":"1line-ellipsis"},
    {"id":"price","kind":"text","editable":false,"source":"tier.price_ugx","format":"UGX n,nnn","font":"data","tabular":true},
    {"id":"serial","kind":"text","editable":false,"locked":true,"painter":"preview_serial","font":"data"},
    {"id":"qr","kind":"qr","editable":false,"locked":true,"painter":"deeplink","label":"Open in ZUKA"}
  ],
  "colorSet": ["ink0","ink1","ink2","ink3","ink4","ink5"],
  "depth": {"shadow":"ambient-1","sheen":"linear","texture":"hairline"},
  "locked": ["typography","spacing","qr","brandmark","all_slots"],
  "brandLaw": {"noCoral": true, "qrGrantsNothing": true, "static": true, "moneyNeverAnimates": true}
}
```

Class deltas:
- `vip`: `"tier_class":"vip"`, `"structure":{"stub":"right","tear":"dashed","notches":false,"frame":"double_hairline_gold","corners":"die_cut","ghost":"VIP","foil":"emberGold"}`, ground ink0 gradient. Variants: `vip.v2_flat_rule` (4px gold rule, no sheen/clipper — budget render), `vip.v3_inverted` (ink5 ground, midnight text).
- `table`: `"tier_class":"table"`, `"palette":"kampala_forest"`, `"structure":{...,"seatTicks":"tier.seats_per_unit","ghost":"×{n}"}`, gold ticks + gold `"ADMITS {n}"` chip, price note `"per table"`. Valid only where `seats_per_unit > 1`.
- `vendor`: `"tier_class":"vendor"`, `"structure":{...,"edgeBand":{"label":"VENDOR","rotated":true}}`, price note `"per stall"`.

### A-21.8 Backend dependencies
- **NEW:** `gathering_tier.tier_class` enum (`ordinary|vip|table|vendor`) — set by **platform administration**, not the organiser. Template↔tier binding validated server-side on assignment (same failure class as vendor category-lock).
- `capacity` on the tier read (sold-out chip). Tier data cached for offline preview.
- Painters: `preview_serial` (deterministic, valueless) and `deeplink` QR.

---

## Decisions taken this session (from founder answers)
1. `tier_class` is platform-administered: Ordinary · VIP · Table · Vendor.
2. Table price shown **per table** (UGX 800,000), with "admits N" as a first-class element.
3. The fourth class is a distinct **vendor-credential** look (not a neutral `other`).
4. Posters now carry the deep-link QR magnet — supersedes A-09's QR-free poster.
5. Mockups use strict local fallback stacks (no external hosts), per §8.
6. Vibrancy/depth pass ratified: luxury ladder ordinary→vip via material (gold foil, sheen, ghost type); table = forest ground; single-ambient-shadow depth system on tickets and posters.

## Open items
- Contract naming: brief fixes the enum key as `other`; administration taxonomy calls it **Vendor**. Recommend renaming the key to `vendor` (or `"other" + adminLabel:"Vendor"`) — founder call before BUILD-READY.
- Whether `vendor` needs sub-looks per vendor category (food, craft, drink) or one credential look covers all.
- Recommended defaults to ratify: poster composition V1, stub structure V1, VIP treatment V1 (noir foil).
- A-09/A-18 texture law: this pass stays within "simple linear gradients at most" — founder to confirm the ghost-letterform and die-cut clipper are acceptable spec primitives.

---

### §22 — Smart Bucket Credential Screen (`/tickets/:ticketId` · the holder face)

> **Status: BUILT (F037, `dc37b18`) · spec written retroactively 2026-07-25 to close
> RF037-8** (a brand-new primary screen shipped with no `zuka-designer` pass and no
> entry here). Source of the behaviour: `docs/superpowers/specs/2026-07-23-smart-bucket-credential-design.md`
> §3.4 / §4 and the shipped `lib/features/tickets/`. This section is the design of
> record from now on; where it differs from the shipped pixels, §22.12/§22.13 say so
> and why. **The credential seam is inert in production today** —
> `get_bucket_credential()` is unbuilt, so every real device currently lands in the
> *not-available* sub-state. The screen was built above the seam deliberately; that
> is the right call and it does not change the design.

#### §22.1 Purpose
One line: **"Let me in."** The person is standing at a door (or checking, hours
earlier, that they really are going). This is the holder face of the Smart Bucket —
one bucket, one code, the server disambiguates by the scanner session's gathering.

It is also the app's only true **security surface for a Person**: it is the screen a
stranger may be holding the phone next to, in a queue, in the dark.

#### §22.2 Entry points
- **Tickets tab → a row tap** (`/tickets` → `/tickets/:ticketId`) — the primary path.
- **Cold deep link** (`zuka://tickets/<id>` / notification landing). Back-stack law
  §9.6 still lands Pulse beneath — never an app exit.
- **Never** from a "choose your ticket" chooser. There is no chooser (§22.11 R4).

#### §22.3 Flow & tap budget
```
Tickets tab → row → CREDENTIAL SCREEN (code already on screen)     2 taps
Cold open  → Tickets → row → CREDENTIAL SCREEN                     3 taps
Reveal the code                                                    0 taps
```
**Zero taps to reveal is a rule, not a target.** No tap-to-reveal, no "show my code"
button, no biometric gate in front of the code (device unlock already happened). At a
gate, in a queue, with a phone in one hand, every gesture is a failure mode.
Brightness raises itself; the holder holds the phone out and that is the whole
interaction.

#### §22.4 Screen inventory
One screen. **No sheets, no modals, no dialogs, no bottom bar.** The only chrome is a
back arrow. Nothing may be added to this screen that is not the credential, the
identity of the gathering it belongs to, or the truth about why there is no code.

#### §22.5 Components
| Component | Spec (one line) |
|---|---|
| **BucketCard** | The single primary card: `ink1` surface, `ink2` 1px border, radius 16, `lg` (24) padding. One per screen, always the first thing under the app bar. |
| **BucketCardHeader** | Title (`h3`, `ink5`, max 2 lines) + right-aligned **TicketStatusPill**; second line `venue · when` (`caption`, `ink4`). Answers "which gathering is this code for" before the code is looked at. |
| **BucketQr** | The credential painted at gate size — dark `ink0` **square** modules on a light `ink5` chip (radius 12, 12dp quiet-zone padding), EC level **M**, **no** embedded logo. 232dp in READY, 168dp in UPCOMING. Separate widget from the magnet `DeepLinkQr` on purpose: a magnet is a public funnel, a credential is not. |
| **CredentialRing** (shipped as `BucketShimmer`) | A 1.5dp breathing ring, radius 18, inset 6dp, around the code. Constant rate, constant colour, **carries no information** (§22.12 R1/R2). Renamed in this spec: "shimmer" implies a loading placeholder, which is exactly what it must never be mistaken for. |
| **CredentialAbsentBlock** | The honest no-code treatment: `info_outline` glyph (**never** a QR-shaped glyph), the reason, the `ticket_ref` in Space Mono `data`, and the safety line. No retry — there is nothing to retry. |
| **CredentialUnreachableBlock** | Same container, `wifi_off` glyph, one line, and **[Try again]**. The only difference from the block above is that this failure may pass. |
| **CredentialSkeletonChip** | A static `ink1` square, `ink2` border, radius 12, at the exact footprint of the code it will replace. **No spinner, no shimmer, no code-like fill** (§22.11 R7). |
| **AlsoInYourBucketList** | Informational list of the holder's *other* live/upcoming tickets. `ink1` rows, radius 12, `md` padding: title (`body`/`ink4`) + when (`caption`/`ink3`) + TicketStatusPill. **No tap target of any kind.** |
| **TicketStatusPill** *(shared — see §22.12 R3)* | The canonical ticket-state pill, now used by both §8's list and this screen. Fully-rounded, `micro` w600, 0.4 letter-spacing, colour at 0.16 fill / 0.5 border. |

*Shared Component Library additions (to fold into Design Foundations at its next
revision):* **BucketQr**, **CredentialRing**, **CredentialAbsentBlock**,
**CredentialSkeletonChip**. **TicketStatusPill** is promoted from a §8-local widget to
a shared component — build note: it still lives at `lib/features/tickets/`; RF031
MED-5's Rule-of-Three move to `lib/core/widgets/` is still owed.

#### §22.6 States
**Five primary-card states** (a pure function of hard signals off `my_tickets()` —
ticket status, `is_live`, `starts_at`/`ends_at`, `gathering.status`; **no inference,
DRL-1**):

| State | When | Code? | Body | "Also in your bucket" |
|---|---|---|---|---|
| **READY** | ISSUED + gathering LIVE, or within the 2h doors-open lead | **Yes, 232dp** | lede under the code | **Suppressed** |
| **UPCOMING** | ISSUED, window not open yet | **Yes, 168dp** ("present but quieter") | lede under the code | Shown |
| **UPCOMING (window closed)** | ISSUED, gathering ENDED / CLOSED / CANCELLED | **No** | closed/cancelled lede | Shown |
| **CHECKED-IN** | CHECKED_IN | **No** | `success` tick (44dp) + "You're in." | Shown |
| **PENDING** | PAYMENT_PENDING · REFUND_QUEUED · a status this client version has not been taught | **No** | one context line; the pill carries the verdict | Shown |
| **EMPTY** | the routed ticket is not in this caller's bucket | **No** | glyph + line + [Your tickets] | Shown (if any) |

**Three credential-area sub-states** (inside READY / UPCOMING only):

| Sub-state | Treatment |
|---|---|
| **Loading** | CredentialSkeletonChip — static, exact footprint, no motion, no text. Brightness is already raised (the code may arrive a frame later and the holder is already at the door). |
| **Not available** (permanent) | CredentialAbsentBlock. Says the code is not available, confirms the ticket is real, gives the reference, and states plainly that the reference is **not** an entry code. **No retry** — nothing here is transient. |
| **Unreachable** (transient) | CredentialUnreachableBlock + [Try again]. A 3G blip at a door must never render the permanent copy to someone holding a valid ticket. |

**Screen-level states:** loading (a single static 380dp card-shaped `ink1` block — no
spinner on blank) · read failure (glyph + line + [Try again]) · **offline** (see
below) · **signed-out** (see below).

- **Offline — first-class, and currently a gap.** There is no cached credential today
  (SB-2), so offline degrades to the read-failure face. Two things must change: (1)
  offline must be *named* as offline rather than wearing a generic failure line, and
  (2) when SB-2 lands a cached credential, the screen must render it **with no
  staleness ribbon** — §8/A-03's "Saved Xh ago" line is legal for the legacy static
  per-ticket QR and is a **forbidden freshness channel** on a rotating bucket
  credential (§22.11 R2). Copy in §22.7.
- **Signed-out.** Today an anon deep link resolves to EMPTY — "We can't find that
  ticket in your bucket", which is untrue and unhelpful. It needs its own state
  (copy in §22.7). No login wall is being introduced: browsing stays open, but *your*
  bucket is by definition yours.
- **First-time vs returning:** identical. A credential screen that teaches on first
  use is a credential screen that is slower at a door. Whatever the holder needs to
  know is one sentence under the code, every time.

#### §22.7 Content / copy
| Slot | Copy | Note |
|---|---|---|
| READY lede | **"Raise this at the door."** | The whole instruction. Present tense, physical verb. |
| UPCOMING lede | **"Not yet — raise this when you arrive."** | Says both the "no" and the "when". |
| Window closed | **"This gathering has finished."** | |
| Cancelled | **"This gathering was cancelled."** | Never "finished" — nothing happened. |
| CHECKED-IN | **"You're in."** | Warm, past-the-door, present tense. |
| PENDING | **"No entry code yet — this ticket is still being settled."** | |
| PENDING (untaught status) | **"No entry code for this ticket."** | Claims nothing about a state this client cannot read. |
| EMPTY | **"We can't find that ticket in your bucket."** + [Your tickets] | |
| Credential absent | **"Your bucket code isn't available yet."** / **"Your ticket is confirmed. Keep this reference:"** / `TKT-…` / **"This is not an entry code."** | Stops there. **No copy on this screen may name an operational fallback that is not built** — the earlier "ask staff to look you up by name" was exactly that and was cut (RF037-1). |
| Credential unreachable | **"Couldn't load your bucket code."** + [Try again] | |
| Screen read failure | **"Can't load your bucket right now."** + [Try again] | |
| **Offline (spec'd, to build)** | **"You're offline. Your bucket code needs a connection right now."** + [Try again] | Names the real cause. When SB-2 caching lands this becomes: show the cached code, and say **nothing at all** about how old it is. |
| **Signed-out (spec'd, to build)** | **"Sign in to open your bucket."** + [Sign in] | |
| List header | **"Also in your bucket"** | `caption`, `ink3`, 0.6 letter-spacing. |
| Retry button | **"Try again"** — one label, everywhere | The screen currently says "Retry" at screen level and "Try again" inside the card. Pick "Try again": warmer, and one label for one action. |

**Voice check:** no hype, no exclamation, no "Oops", no scolding about screenshots, no
promise about timing. The screen never says the code *will* work — the server decides
admission and the phone must not assert a verdict it cannot make.

#### §22.8 Colour logic
| Element | Token | Why |
|---|---|---|
| Screen + app bar | `midnight` | base |
| Card / rows / blocks | `ink1` surface, `ink2` border | raised surface grammar |
| Code chip | **`ink5` field, `ink0` modules** | the one light surface in ZUKA — logged in the Bible Deviations Register |
| CredentialRing | **`ink4`, alpha 0.10 → 0.32** | **RULED — see §22.12 R1.** Neutral = general/information. Not gold, not coral, not green. |
| CHECKED-IN tick | `success` | confirmation — never `liveCoral` |
| Pill: PAYMENT_PENDING | **`emberGold`** | **RULED — restore A-03, §22.12 R3** |
| Pill: ISSUED / CHECKED_IN | `success` | a ticket is not a live state |
| Pill: REFUND_QUEUED | `warning` | a genuine exception: money in, ticket not delivered |
| Pill: untaught status | `ink3` outline / `ink4` label | no state claimed |
| Live-now marker | `liveCoral` | **only** for "the gathering is live right now", and never colour-alone (§22.12 R3, coral grammar) |
| Lede text | `ink5` in READY, `ink4` in UPCOMING | the quieting between the two states is carried by type colour + code size, not by a different layout |

**Nothing on this screen is coloured decoratively.** If an element does not carry a
state, it is neutral.

#### §22.9 Type & spacing
- Grid: `ZukaSpacing` 8px scale. Screen padding `lg` (24). Header → code `lg`. Code →
  lede `md` (16). Card → "Also in your bucket" `xl` (32). EMPTY block `xxl` (48)
  vertical.
- Type: title `h3` · lede/rows `body` (16, the accessibility floor) · sub-lines
  `caption` (14) · pill `micro` (12) · `ticket_ref` in `data` (Space Mono) — codes are
  exactly what Space Mono is for.
- **Contrast fix (RULED):** *"This is not an entry code."* currently renders in `ink3`
  at `caption` — the **lowest-contrast ink at the smallest size on the single most
  load-bearing safety line on the screen** (`ink3` on `ink1` is ≈3.2:1, below the 4.5:1
  floor). Raise it to **`ink4` minimum**, and it must not be the smallest text in that
  block. Inverted hierarchy on a safety line is a defect, not a style.
- **Tap targets ≥48dp.** The only interactive elements are: back, [Try again], [Your
  tickets], [Sign in]. Stock `OutlinedButton` height is under the floor — the developer
  must pin it.

#### §22.10 Motion
| Moment | Motion | Tier |
|---|---|---|
| **CredentialRing breath** | constant-rate loop, **2400ms**, `ink4` alpha ramp, `reverse: true`; reduce-motion renders a static frame | **T2** |
| **Code arrival** | **none.** The code paints instantly at full contrast | T0 |
| Skeleton chip | none — static | T0 |
| Everything else | screen-level defaults (§9) | T0/T1 |

Two rows to fold into the Foundations *per-surface transition map* at its next
revision (appended here, not edited in): the ring row above, and an explicit
carve-out — **§8's `ZukaMotion.settle` "ticket/QR reveal" does NOT apply to the bucket
credential.** The settle reveal is right for the legacy per-ticket QRCard; on the
bucket credential the code's opacity must never be animated (a camera can catch a
mid-fade frame at reduced contrast) and the holder is often already at the gate. If
arrival motion is ever wanted here, it animates the chip, never the code.

The ring is the **only** motion on this screen, and it is legitimate under the Motion
law: it is a liveness marker (T2) that distinguishes a live app render from a
screenshot at a glance — a small, real anti-replay social cue for a gate operator.

#### §22.11 UX rules (non-negotiable — a developer must honour these)
1. **Exactly one path paints a QR:** a real, server-issued credential string that
   passes the shape gate. Every other path renders **words**, never a symbol. A
   placeholder that looks like a code is a code someone will try to scan.
2. **Rotation cadence is never a channel.** No countdown, no "next code in…", no
   freshness indicator, no as-of/staleness time, no manual refresh affordance, and no
   pull-to-refresh on this screen. Any invisible re-read (e.g. on app resume) must be
   unobservable to the holder.
3. **The ring is constant.** One colour, one rate, one period — identical in every
   state, for every Person, on every device, compile-time. It must never be
   parameterised, derived, or varied at runtime (§22.12 R2).
4. **Never a selector.** "Also in your bucket" has no `InkWell`, no `GestureDetector`,
   no `onTap`. The holder never picks a ticket — an affordance here would teach exactly
   the wallet-searching habit the Smart Bucket exists to delete.
5. **"Also in your bucket" is suppressed in READY.** In READY the phone is held out to
   (or handed to) a gate operator who is not a trusted party; "which other gatherings
   does this person attend" is Community-Graph data and the door is not where it should
   be legible. It stays on the Tickets tab and in every other state.
6. **No dead QR, ever.** EMPTY, PENDING, CHECKED-IN and a closed window show no code. A
   code that returns RED at the door is worse than no code.
7. **The loading placeholder never resembles a code** and never animates.
8. **The screen never asserts admission.** It says what is true (status, time,
   "raise this at the door"), never "this will get you in".
9. **FLAG_SECURE is unconditional** for the whole screen and reference-counted, and it
   engages in *every* state — including the ones with no code, because every state shows
   the holder's gathering titles, which do not belong in a recents thumbnail either.
   Max brightness is scoped to where a code can actually show (battery, 2GB Android,
   night venue).
10. **Nothing may be added to this screen** that is not the credential, the identity of
    the gathering it belongs to, or the truth about why there is no code. No upsell, no
    vendor strip, no recommendations, no share button.

#### §22.12 Rulings on the open divergences
**R1 — the CredentialRing must not be `emberGold`. RULED: neutral `ink4`.**
The ratified colour law reserves gold for *energy / starting-soon / primary action*. A
credential ring is not an action. The decisive case is UPCOMING: the same gold ring
paints around a code whose own lede says *"Not yet"* — gold there says "act now" while
the words say "don't". Rejected alternatives, with reasons worth keeping: **coral**
(reserved for the gathering being live; the ring is not about the gathering);
**`success` green** (would read as *"valid"* — the phone would be asserting an
admission verdict only the server can make; see R8/UX rule 8); **gold only in READY,
neutral in UPCOMING** (a state-varying ring is a second thing to read, and invites
exactly the "the ring means something about my code" interpretation the no-freshness
rule exists to prevent). Neutral, always, at the existing 0.10→0.32 alpha ramp: the
ring means *"this is a live credential surface"* and nothing else. This restores a hard
colour law — **not** a founder reversal.

**R2 — the ring's period should be a named motion token. RULED: add one; keep the
value constant.**
The security property is that the period is a **fixed compile-time constant, identical
everywhere** — a token is exactly that, so tokenising costs nothing and buys the rule a
home. Add to `ZukaMotion` as a **T2** token, suggested name **`credentialBreath` =
2400ms**, carrying this sentence in the token file: *"Constant by contract. Identical in
every state, for every Person, on every device. Never parameterise, derive, or vary this
at runtime — a varying rate leaks rotation cadence."* Explicitly **not** `settle`:
`settle` is a 400ms one-shot reveal for the §8 ticket QR; this is a 2400ms loop with a
different job, and §22.10 rules that `settle` does not apply to the bucket credential at
all. Reduce-motion continues to render a static frame.

**R3 — the TicketStatusPill (A-03 divergence, RF031 MED-1, now entrenched).**
The pill is now the shared, two-screen canonical widget, so this must be settled rather
than carried again. What it **should** be:

| Ticket state | A-03 (ratified 2026-07-18) | Shipped F037 | **Ruling** |
|---|---|---|---|
| PAYMENT_PENDING | `emberGold` · "Waiting for payment" | `warning` · "Payment processing" | **Restore A-03.** Nothing is wrong — the money is in flight awaiting the Person's own MoMo prompt. That is *energy / your action*, which is gold; `warning` says "something is broken" about a normal state. Two ratified places already say gold (A-03 and the shared **TicketStatusBanner**), and §18 CR-1 records that `warning` and `emberGold` are near-indistinguishable anyway — so the divergence bought no legibility and cost the semantic. |
| ISSUED | `success` · "You're going" | `success` · "Ticket ready" | **Restore the A-03 label.** ZUKA is participation infrastructure: the pill states the *Person's* state, not the artefact's inventory status. "Ticket ready" is a system talking about itself. |
| CHECKED_IN | `success` · "You were in" | `success` · "Checked in" | **Restore A-03's words, tensed off a hard signal:** **"You're in"** while the gathering has not ended, **"You were in"** once it has. A-03's past tense was designed for the EARLIER group and is wrong on a row for a gathering happening right now. Both readings are hard signals the row already carries — no inference. |
| REFUND_QUEUED | *(not covered — postdates A-03)* | `warning` · "Refund pending" | **Ratify as shipped.** This *is* a genuine exception (paid, undelivered, a human processing it), so `warning` is correct here and now carries a distinct job from PENDING's gold. Copy stays "refund" — never credit, balance, or wallet. |
| untaught status | *(not covered)* | `ink3` · **"—"** | **Change: neutral pill reading "Status unavailable"** (`ink3` border / `ink4` label). A bare dash is not a label — a screen reader announces nothing, and a sighted Person reads a glyph with no meaning. Say the honest thing in words. |

On the credential card specifically: **suppress the header pill in CHECKED-IN.** The
card body already carries the tick and "You're in."; a second copy of the same fact in
the header is duplicated hierarchy (and would clash in tense). The pill stays in every
other card state and in every "Also in your bucket" row.

Because the shipped pill reverses a ratified founder decision, **restoring A-03 is the
default and needs only confirmation; keeping the shipped labels would be the reversal**
and would require an explicit A-03 amendment. Both are logged as a founder call in Open
Questions. **Adjacent and still open (RF031 MED-2):** the live state is carried by a bare
6dp coral dot *inside* the state chip — colour alone, no text carrier. Recommendation:
use A-03's separate coral **LIVE** pill in list rows (there is room), and drop the dot
entirely from the credential card header, where the card's whole state and lede already
say the door is open now.

#### §22.13 Open questions & dependencies
All five are registered in *Open Questions & Backend Dependencies* (2026-07-25 rows):
the two founder calls above (pill labels/colour; coral grammar), the missing
`checked_in_at` for the CHECKED-IN state, the missing SIGNED-OUT state, and the
offline/SB-2 cache with its no-staleness-ribbon forward guard.

One further design note for whoever builds SB-2 (the lock-screen widget): every rule in
§22.11 travels with the credential. A widget that shows a code on a lock screen inherits
the reveal discipline, the no-freshness rule, and the constant ring — and it cannot
inherit FLAG_SECURE, which is a `zuka-security` question, not a design one.

#### §22.14 Changelog
- **2026-07-25** — Section created (retro design pass, closes RF037-8). Specified the
  five card states, three credential sub-states, copy, colour, type/spacing, motion and
  ten UX rules of the shipped screen. **Ruled:** CredentialRing → neutral `ink4`, not
  `emberGold` (R1); ring period → a named T2 `ZukaMotion` token, value stays constant,
  and `settle` explicitly does not apply to the bucket QR (R2); TicketStatusPill →
  restore A-03 colour + labels, ratify REFUND_QUEUED as shipped, replace the "—" with
  words, suppress the pill on the CHECKED-IN card (R3). **Also ruled:** the "This is not
  an entry code" safety line must clear the contrast floor; one retry label. **Logged:**
  the light QR chip as a Bible deviation (Midnight base); five open items, two of them
  founder calls.
### §23 — Vendor Settle-balance (VS-4 · pay ZUKA's platform fee → instant unlock)

*The client face of the commission-settlement + anti-dodge back half — all built,
dark, and security-clean on `session/VS-integ` (spec
`docs/superpowers/specs/2026-07-23-vendor-commission-settlement-antidodge-design.md`,
§3.2 Channel A, §3.3–3.4 picker, §5 anti-dodge). The accrual, the gate lock, and
the tamper-proof `settle_commission_debt` terminal already exist; this section
designs the **front half** — how a vendor sees the balance, pays it from a chosen
MoMo number via a **direct STK push** (not the §7 hosted-checkout browser leg),
and watches the debt clear and the listing unlock. It also covers the Channel-B
at-next-purchase notice and the `kyc_required` placeholder. This is NOT the §11
per-gathering console and NOT the §17 booths manager — it is the money surface
those two link into. Money-domain: `zuka-security` + `zuka-legal` lead any build.*

**1. PURPOSE**
Let a vendor company member clear an outstanding **ZUKA platform fee** and get an
already-locked listing back online — honestly framed (ZUKA's own B2B receivable,
never the vendor's sales money), in the fewest calm taps, on a flaky 3G handset.

**2. ENTRY POINTS**
- **(a) The gate lock flag** (built) — a locked company's team scans at the gate,
  presence does NOT activate, and the phone shows *"Listing locked. &lt;Company&gt;
  has an unsettled platform fee of UGX X. Settle to go live."* That surface gains a
  gold **[Settle balance]** action → this screen. (Lives on the §11/§10 scan
  result.)
- **(b) The /vendor console / account** — a calm balance banner in the vendor world
  (placement between §17 booths mode vs a dedicated account surface is an open
  ruling, §23.10) with **[Settle balance]**. Shown ONLY when a real outstanding
  balance is read; **absent when the balance is zero** (never a dead/greyed entry).
- **(c) Channel-B, indirect** — the §23 OutstandingBalanceNotice at a NEW-credential
  checkout can route here (or bundle in place — see §23.7).
- Entered as a **mode** (its own stack, T1 modal slide-up "you entered a mode",
  per the Motion map), like `/host` and `/vendor`. Back = `pop()`, else `go('/')`.

**3. FLOW (tap budget marked — a sacred-proof-adjacent money path, kept honest)**
```
SETTLE NOW (Channel A):
  [Settle balance]  ①  (from gate lock OR /vendor account)
    → Settle-balance screen: FeeBalanceCard (amount + posture line + lock line)
                             + PayFromPicker (primary number PRE-SELECTED)
    → (common case: default number is right → no extra tap)
    → [Settle UGX X]  ②   (initiate_vendor_fee_settlement(company, payer_msisdn);
                            server derives amount + mints tx_ref → STK push)
    → STK push → MoMo PIN prompt on the vendor's handset  (external approve,
                            mirrors the §7 "3rd action is off-app" honesty)
    → SettlementPendingMeter (patient; survives backgrounding)
    → webhook → complete_vendor_fee_settlement → SETTLED  → instant unlock
```
Two in-app taps + the external PIN approve — inside the 3–5 budget, and the amount
is shown in full **before** commit (no dark pattern, no surprise total). Choosing a
different number adds one tap (pick) + typing, never forced.

**4. SCREEN INVENTORY**
- **Settle-balance screen** (the mode's root) — FeeBalanceCard + PayFromPicker +
  the gold [Settle UGX X] primary.
- **SettlementPendingMeter** (in-place state swap on the same screen after commit —
  not a new route, so a back-grounded return lands here).
- **Completion states** rendered in place: **SETTLED** card · **FAILED** banner +
  retry · **HeldNotice**.
- **Instant-unlock confirmation** (SETTLED payoff) — routes back to the origin
  (gate scan result / vendor account) showing the now-unlocked state.
- **OutstandingBalanceNotice** — a banner/sheet on the *other* gathering's
  new-credential checkout (§7 family), not a screen of its own.
- **VerificationRequiredNote** — an inline placeholder note, not a flow.

**5. COMPONENTS** (reuse shared **PriceText**, **ZukaButton**, **ZukaTextField**,
**FormBanner**; the six new ones are catalogued in the Shared Component Library):
- **FeeBalanceCard** — company · amount (PriceText, money-silent) · money-posture
  line · calm lock line (neutral, never red).
- **PayFromPicker** — registered (primary default-selected) · frequently-used (3+)
  · "Pay from a different number →" free entry; masked rows, day-one-empty collapse.
- **SettlementPendingMeter** — STK-push wait (PIN on handset, no browser),
  background-safe, [I've approved it — check now].
- **HeldNotice** — the `info`-blue "received, being matched, don't pay again"
  surface; settle action suppressed.
- **OutstandingBalanceNotice** — Channel-B informational banner + two unticked
  choices.
- **VerificationRequiredNote** — neutral `kyc_required` placeholder, gates nothing.

**6. STATES** (every one reachable; empty + offline are first-class)
*Settle-balance screen:*
- **loading** — static skeleton for FeeBalanceCard + picker (T1, no shimmer loop).
- **default** — balance read + picker with the primary number pre-selected.
- **no-balance** — read returns zero outstanding → *"Nothing to settle —
  &lt;Company&gt; is all clear."* (success green), no [Settle] button. (Reachable
  if a teammate cleared it first — instant unlock is company-wide, §5 spec.)
- **picker day-one-empty** — first-ever settlement, no usage history → the
  "Frequently used" group is **absent** (not an empty box); picker = registered +
  "different number". Must not read as broken.
- **different-number** — free-entry `ZukaTextField` (MoMo numeric keyboard);
  light client validation (digits / length), a non-match is **never a block**
  (anti-drain — pay from any wallet).
- **balance-read error / offline** — FormBanner, calm: *"You're offline — we
  couldn't load your balance. It's safe; try again when you're back."* The [Settle]
  button is withheld rather than firing on a stale/unknown amount.
- **committing** — [Settle UGX X] shows inline spinner, NON-tappable; the picker
  locks (commit-once — `initiate_vendor_fee_settlement` is not blind-idempotent).
- **initiate error** — mapped FormBanner (offline variant on network); no STK push
  fired = nothing charged; the vendor retries. Values untouched.

*After commit (in-place swaps):*
- **pending (STK push)** — SettlementPendingMeter: *"Enter your MoMo PIN on
  077 ••• 456 to approve."* Indeterminate, no countdown, elapsed time in words.
  **Background-safe:** if the app is backgrounded during the (possibly slow) MoMo
  round-trip, returning lands back on this state; resolution is webhook-driven, not
  tap-driven. Quiet [I've approved it — check now] re-polls.
- **SETTLED** — success green: *"Fee settled — &lt;Company&gt;'s listing is
  unlocked."* Then the instant-unlock beat (§23 below). Calm T1 confirmation, **not**
  a coral bloom (unlock ≠ live).
- **FAILED** — the charge didn't succeed (declined / no PIN / timeout): `error` red,
  blame-free — *"The payment didn't go through. Nothing was charged. Try again, or
  use a different number."* [Try again] returns to default (self-correcting: no
  charge = no settlement).
- **HELD** — money received but not applied (over/under/twice/balance-grew):
  **HeldNotice** (`info` blue). *"We've received your payment — we're just matching
  it to your balance. Nothing is lost, and you don't need to pay again. Our team is
  on it and your listing will clear shortly."* + the PSP reference to quote. The
  **[Settle] action is suppressed** here — a hard double-pay guard. No auto-refund
  is promised (money is held for reconciliation, not returned).
- **pending-timeout / indeterminate** — if the poke returns "still no charge seen",
  stay patient (MoMo latency is real); never flip to FAILED prematurely. Offer
  [Check again] + a plain *"Taking longer than usual — you can leave this and we'll
  update the listing when it clears."*

*Instant-unlock moment (SETTLED payoff):*
- On SETTLED the backend emits `VendorCompanyUnlocked` → **every team member unlocks
  instantly, no appeal queue** (§5 spec / §11 discipline). The screen routes back to
  the origin: from the gate lock → the scan result now reads *"Settled — scan again,
  your stall can open now"* (the stall still opens via the §11 toggle after gate
  check-in; settling **unlocks**, it does not itself set LIVE — so **no coral**).
  From /vendor account → the balance banner is gone, replaced by *"All clear."*

*Channel-B (at next-purchase, on the OTHER gathering's checkout):*
- **notice** — OutstandingBalanceNotice: balance + honest consequence, TWO unticked
  choices (pay both together / credential only). Declining the fee still completes
  the credential purchase.
- **empty (no balance)** — the notice simply does not render (no manufactured
  nudge).

*kyc_required:*
- **flagged** — VerificationRequiredNote appears as a calm inline note. It **gates
  nothing today** (no KYC flow exists) — settle/register proceed normally; it is a
  "coming" placeholder only.
- **not flagged** — nothing renders.

**7. CONTENT / COPY** (direct, present-tense, warm, no hype; money honest)
- **Screen title:** *"Settle your platform fee"*.
- **FeeBalanceCard posture line (load exactly):** *"This is ZUKA's platform fee for
  orders your stall collected. It goes to ZUKA — it's not taken from your sales."*
- **Lock line:** *"Your listing stays locked until this is settled. Settle it and
  you're back on instantly."* (gate the vendor holds the key to — not a threat).
- **Primary CTA:** **[Settle UGX X]** (amount in the label — full price before
  commit).
- **Picker labels:** section heads *"Registered"* · *"Frequently used"*; rows show
  masked number + status (e.g. *"Primary"* / *"Also used often"*); *"Pay from a
  different number →"*.
- **Pending:** *"Enter your MoMo PIN on 077 ••• 456 to approve."* / poke
  **[I've approved it — check now]**.
- **SETTLED:** *"Fee settled — your listing is unlocked."* (gate origin adds
  *"Scan again to open your stall."*)
- **FAILED:** *"The payment didn't go through. Nothing was charged."* / **[Try
  again]**.
- **HELD (load exactly):** *"We've received your payment — we're just matching it to
  your balance. Nothing is lost, and you don't need to pay again. Our team is on it;
  your listing will clear shortly."* + *"Reference: &lt;tx_ref&gt;"*.
- **Channel-B notice:** *"Heads up — &lt;Company&gt; has an unsettled ZUKA platform
  fee of UGX X. Until it's settled, your listing won't activate at &lt;gathering&gt;
  — you'll be asked to pay at the gate."* Choices: **[Pay both together]** /
  **[Just the credential for now]** (neither preselected).
- **VerificationRequiredNote:** *"A quick identity check will be needed here soon."*
  (Never the reason, never who/what triggered it.)

**8. COLOUR LOGIC** (the hard rule, held exactly — **no coral anywhere in §23**)
- **No `liveCoral`, ever** — this whole flow contains no "live right now" state.
  Settling *unlocks*; it does not make a stall LIVE (that is the §11 dot). The coral
  fence holds cleanly here.
- **Money is colour- and motion-silent** — the fee amount and all PriceText render
  neutral `ink`, never a status colour, and never count up/animate (Vol 16 / §18).
- **The lock is neutral, not red** — `ink3` + lock glyph. `error` red would frame a
  gate the vendor controls as a failure/punishment (Vol 23) — wrong.
- **`emberGold`** — the [Settle UGX X] primary and picker selection (gold's action
  monopoly).
- **`success` green** — SETTLED and no-balance "all clear" (a fee cleared is a
  success, mirroring §11 register green).
- **`error` red** — FAILED only (a genuine charge failure) and offline/RPC
  FormBanners.
- **`info` blue** — HELD (in-progress reconciliation, not a failure) and the
  VerificationRequiredNote (routine, non-accusatory) — matching §17 SyncStatusChip's
  calm `info`.

**9. UX RULES**
- **Money-posture honesty (SOUL / NPS Act):** always framed as *paying ZUKA's own
  B2B fee to ZUKA*, never as ZUKA moving the vendor's sales money. No `subaccounts`
  language, no "we deduct from your earnings".
- **Full price before commit** — the amount is on the card AND in the button label.
- **Anti-drain (Vol 23):** never enforce "pay only from a registered number" — a
  non-match is a signal, never a block; "different number" is always present.
- **Double-pay guard:** in HELD (and while pending) the settle action is suppressed;
  the pending state never flips to FAILED prematurely on MoMo latency.
- **Background-safe pending:** the STK-push wait resolves via the webhook; the client
  polls but never assumes success from the tap. A cheap 2GB handset on 3G that
  backgrounds mid-PIN must return to a correct state.
- **Commit-once:** one in-flight `initiate_vendor_fee_settlement`; picker + button
  lock while committing.
- **No dark patterns:** Channel-B never auto-bundles, never pre-ticks, never invents
  urgency; declining the fee still completes the credential buy.
- **Trust-state non-exposure:** `kyc_required` shows only an actionable affordance —
  never the reason, the match, or the other company. No trust number, ever.
- **Locked terminology:** *stall / vendor company / gathering / platform fee*;
  Person, never "user"; UGX integer via PriceText.
- **Big tap targets, few taps** — ≥48dp rows/buttons; picker default-selected so the
  common path is 2 taps.

**10. OPEN QUESTIONS (backend/build dependencies — see the register for full rows)**
- **Owner-scoped balance READ** for the /vendor account + Channel-B display (the
  gate flag supplies X at scan, but the console needs its own read; must expose only
  the own-company balance).
- **Settlement-status poll/Realtime** so the pending meter resolves SETTLED/FAILED/
  HELD after backgrounding (like §7 `my_tickets`).
- **HELD surfaced distinctly** (vs FAILED) with **no leak of WHY** it was held; no
  auto-refund implied.
- **Masked-number source of truth** — does `get_vendor_payment_profile` return
  pre-masked numbers + an opaque token (DPPA minimisation, so raw third-party
  msisdns never sit in the client), or raw numbers the client masks?
- **`kyc_required` owner-read is boolean-only** and (today) wired to gate nothing.
- **Channel-B `bundled` opt-in** wiring (VS-2) as an explicit unticked choice.
- **First-party entry placement** for the /vendor account balance banner (inherits
  §11's deferred vendor-affordance question).

**11. CHANGELOG**
- *2026-07-24 (VS-4 design pass):* Section created against the built-but-dark
  `session/VS-integ` back half. Designed: the honest FeeBalanceCard (ZUKA's own fee,
  not vendor sales money), the saved-number PayFromPicker (masked, primary-default,
  day-one-empty collapse, always-present "different number"), the **STK-push**
  SettlementPendingMeter (distinct from §7's hosted-checkout handoff — no browser
  leg, background-safe), the SETTLED instant-unlock beat, the honest **HELD**
  HeldNotice (`info` blue, "don't pay again", settle suppressed, no auto-refund
  promise), FAILED retry, the Channel-B OutstandingBalanceNotice (informational, two
  unticked choices, no forced bundle), and the neutral **VerificationRequiredNote**
  placeholder (gates nothing today, never exposes the reason). Coral is absent by
  design — settling unlocks, it does not make a stall live. No Bible deviation
  logged; the "Events" label question does not arise (this is a semantic vendor
  surface — *platform fee / stall / gathering*). Seven backend dependencies filed
  to the register.
