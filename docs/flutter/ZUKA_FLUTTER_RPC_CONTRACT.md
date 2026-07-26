# ZUKA — Flutter RPC Client Contract

**Purpose:** every Supabase RPC call from Flutter goes through one wrapper,
never a raw `supabase.rpc(...)` scattered in widget/provider code. This is
the client-side mirror of the backend's own "one policy resolver, checked
once" discipline (`ZUKA_DESIGN_LIVING_DOC.md` §0 contract 4). It exists so
the NO-CACHE rule, the error-language mapping, and idempotency handling are
each correct in exactly one place, not re-implemented per screen.
**Status:** Living reference — grows as new RPCs and error codes ship.
**Depends on:** ADR-028 (the wrapper lives at `lib/core/rpc/`), ADR-021
(the NO-CACHE rule this wrapper enforces).

---

## 1. The one rule that must never be violated: NO-CACHE

Per **ADR-021 Rule 2** (verbatim scope): `check_in_ticket()` **and any future
admission-verification RPC** must never be cached, at any layer, ever —
"All layers — Cloudflare, CDN, application, client." This is a fraud control,
not a performance preference: the rotating `qr_token` only protects against
ticket cloning if every scan reads live database state.

```dart
// lib/core/rpc/zuka_rpc_client.dart
const kNoCacheRpcs = {
  'check_in_ticket',
  // any future admission-verification RPC gets added here on day one of
  // the migration that introduces it — not discovered later.
};
```

**What this means concretely in Flutter:** no `FutureProvider` wrapping a
`kNoCacheRpcs` call may use `.family` result memoization beyond the single
in-flight call, no local Hive/SharedPreferences persistence of its response,
and no Riverpod `keepAlive`/cache-lifetime tuning that would let a stale
result be reused. Every call is a fresh network hit or it fails loud
(offline → explicit "can't verify offline" state, never a stale "looks
valid" cached response — the reality-checker's own read of the offline
scanner problem, ADR-023, is that offline admission uses the **manifest**,
a different, explicitly-designed-for-offline-use mechanism, not a cache of
`check_in_ticket()`'s own response).

**Discovery is the opposite case, and both are correct:** `discovery_feed()`
is cached at the **Cloudflare edge** in production (ADR-021 Rule 1, 30s TTL)
— the Flutter client does *not* need its own network-level cache on top of
that. The client *does* need a **last-successful-response store** purely for
the offline fallback UI `ZUKA_DESIGN_LIVING_DOC.md` §1 mandates ("Offline:
render last-cached feed from storage with a quiet... ribbon"). Keep these
conceptually separate: one is a performance cache (Cloudflare's job, not
Flutter's), the other is an offline-display fallback (Flutter's job,
explicitly required by the design doc, not a stale-data risk since it's
visually marked as last-known-good, never presented as live).

## 2. Error language — the `ZK4xx` convention + standard Postgres codes

The backend already uses a real, established custom-SQLSTATE convention —
document it here so the client maps it correctly instead of falling through
to a generic error:

| SQLSTATE | Meaning (real, from the backend) | Human copy (Part IV "Unified Error Language") |
|---|---|---|
| `ZK429` | Velocity/rate-limit rejection (HTTP-429-flavoured) — e.g. `initiate_ticket_purchase`'s purchase-rate limit (S010) | "You're going a bit fast — try again in a moment." |
| `ZK423` | Locked out (HTTP-423-flavoured) — the PIN-throttle primitive (S013), e.g. scanner PIN entry | "Locked for a few minutes after too many tries — wait and try again." |
| `ZK409` | Already claimed (HTTP-409-flavoured) — one free ticket per Person per gathering (`claim_free_ticket` dedupe, S028) | "You already have a free ticket for this gathering." |
| `ZK410` | Sold out (HTTP-410-flavoured) — a tier's capacity is exhausted; raised by BOTH `claim_free_ticket` and the paid engine behind `initiate_ticket_purchase` (S029). The backend message carries no raw count — render this copy, never the message. | "That ticket type just sold out." |

Standard Postgres/PostgREST codes that will show up regardless of ZUKA-
specific ones — map these generically, don't invent ZUKA-flavoured meaning
for them:

| SQLSTATE | Meaning | Human copy pattern |
|---|---|---|
| `P0001` | Generic `RAISE EXCEPTION` — the message text itself is usually already human-relevant (backend authors write these deliberately) | Surface the RPC's own message text; don't re-wrap it generically. |
| `23505` | `unique_violation` | Context-specific ("Someone else just claimed that ticket" — the exact example already named in Part IV). |
| `23514` | `check_violation` | A business-rule rejection (e.g. invalid state transition) — surface as "That's not something you can do right now," never the raw constraint name. |
| `42501` | `insufficient_privilege` (RLS/REVOKE denial) | Never expose that this happened — RLS denials should be rare/impossible for legitimate client calls; treat as an app bug, log to Sentry, generic "Something went wrong" to the user. |

**Rule:** the wrapper inspects `error.code` (Supabase surfaces the Postgres
SQLSTATE), checks it against this table first, and only falls back to a
generic "Something went wrong, try again" for anything unmapped — which
should also fire a Sentry breadcrumb with the raw code, since an unmapped
code showing up in production means this table is out of date.

## 3. Idempotency — money-moving RPCs

`initiate_ticket_purchase` and `confirm_ticket_payment` already carry
backend-side idempotency (keyed on `tx_ref`; a repeat call against a
terminal state is a no-op, not a duplicate charge — S007/S009). The Flutter
client's job is narrower but still real: **never let a double-tap on "Buy"
generate two separate `initiate_ticket_purchase` calls with two different
client-generated references.** Disable the buy button on tap, re-enable only
on a definitive response (success or a mapped error), and if a retry is
needed after a timeout, retry the *same* logical request rather than
starting a fresh one. This is standard "disable on submit" UI hygiene, not a
new mechanism — stated explicitly here because a duplicate-tap race is
exactly the kind of thing that's invisible in a demo and real at a real
event with a real crowd and real 3G latency.

### The S074 client idempotency key (`p_idempotency_key`) — the paid client contract (F029)

`initiate_ticket_purchase(p_gathering_id, p_tier_id, p_idempotency_key uuid
DEFAULT NULL)` (S074, `20260721100002…`). The trailing key is what makes a
same-attempt retry SAFE beyond the button-disable hygiene above: when a key is
supplied, a repeat call converges on the existing PENDING ticket instead of
minting a second one + a second `tx_ref`. The client rule (`PaidPurchaseController`):

- **Mint ONE key per purchase attempt**, when the person taps "Get ticket"
  (`mintClientKey()`, the same v4-UUID mint as the organiser `client_key`), hold
  it in the phase state, and **reuse the SAME key** on a same-attempt re-tap
  after a timeout → the server converges (no double-mint, no double-charge).
- The key lives **only in in-memory state** — an autoDispose rebuild (leave
  Detail) or an app-kill drops it. It is **never persisted across app-kill** and
  **never** reused across the ~90-min PENDING expiry. A *resumed* purchase keys
  off the existing `ticket_id` (below), not a fresh key.
- **Commit-once stays load-bearing** (mirrors the free-claim guard): an in-flight
  or already-handed-off purchase is never re-initiated. The key is belt; the
  guard is braces.

**Client half is the untrusted half (build-dark behind `FeatureFlags.paidTicketingEnabled`,
default OFF).** The client starts a purchase, calls the `initiate-flutterwave-payment`
Edge Function with the `ticket_id` → hosted checkout link, and opens it in the
**external system browser** (`url_launcher` `LaunchMode.externalApplication`,
**never a WebView** — ADR-034 anti-phishing, supersedes ADR-030). It **never
claims payment succeeded**: on foreground/return it re-queries `my_tickets()` and
discovers the ISSUED transition from the server, with a *bounded* poll then a
calm "processing — we'll notify you" rest state (no infinite spinner, no deep
link / Realtime / FCM in v1). A still-PENDING ticket on return is **resumed** —
re-call the Edge Function with the existing `ticket_id` (it rebuilds checkout from
the ticket's stored `tx_ref`), **not** a fresh `initiate_ticket_purchase`. An
Edge-Function payment-unavailable failure maps to **calm** fail-closed copy
("free entry still works"), never a red error.

## 4. What this wrapper is not

It does not retry silently on failure (Part IV's "Robust RPC Integration"
named retry policy as a real need — but a blanket auto-retry on a payment
RPC is dangerous; retries are per-RPC-category decisions, not a global
default). It does not implement the offline **manifest** sync (that's
ADR-023's job, a separate, larger piece of work scoped to the scanner
screen, not this wrapper). It is not a state-management library — it sits
underneath Riverpod providers (ADR-028), which call it and handle loading/
error/data state on top.

## 5. Read-RPC output bounds (the ceilings the UI must design around)

Every ZUKA read RPC is **bounded** (API law 3 — no unbounded anon/authenticated
read). The purchase/ticket build must treat these caps as part of the contract,
not surprises:

| RPC | Cap | What the UI must know |
|---|---|---|
| `gathering_detail(uuid)` | **≤ 8 attendee tiers** (the 8 *cheapest* active non-vendor tiers, ordered `price_ugx, tier_name`) | A gathering with >8 attendee tiers renders only its cheapest 8 on the Detail screen — a 9th+ tier is **silently absent**. This is a deliberate MVP bound (S029); if a real gathering ever needs >8 visible tiers, that is a backend change (raise the LIMIT / paginate), not a client workaround. Vendor tiers are excluded entirely (reader-sweep, law 9). |
| `gathering_detail(uuid)` — sold-out shape | per tier: `is_sold_out` BOOLEAN **always**; `remaining` present **only** as a true count in **1..10**, else `NULL` | Render "sold out" from `is_sold_out`; render "N left" **only** when `remaining` is non-null (a real 1..10 fact). `NULL remaining` means either >10 left, unlimited capacity, or sold out — never infer a number from its absence. The backend never puts a raw inventory count on the wire (founder ruling ②); do not reconstruct one. |
| `my_tickets()` | **≤ 100 tickets** (PAYMENT_PENDING/ISSUED/CHECKED_IN/REFUND_QUEUED; upcoming-first) | The Tickets tab shows at most 100 active/held tickets. **`REFUND_QUEUED` (S030): the buyer's payment was verified but the tier filled before the late webhook landed — a refund is owed and a human is processing it.** Render it as a distinct "refund pending" state (copy says *refund*, never credit/balance/wallet — legal condition); it disappears once the refund is executed (→ REFUNDED, which is not returned). Other terminal/transfer states (EXPIRED/REFUNDED/GIFT_*/…) are intentionally not returned yet (a later feature). |
| `gathering_detail(uuid)` — visibility | returns a row **only** when the gathering is discoverable **and not a DRAFT** | An unpublished/DRAFT gathering returns **zero rows** to anon (RS029-F2 floor). All post-publish statuses (PUBLISHED/ACTIVE/LIVE/ENDED/CLOSED/CANCELLED) **are** returned so the client can render the honest ENDED/CANCELLED banner — branch on `status`, don't assume a returned row is purchasable. |
| `gathering_detail(uuid)` — types (S060) | `gathering_types TEXT[]`, **1–6 tokens**, NOT NULL at the table | **Breaking change — the scalar `gathering_type TEXT` column is GONE**; reading it yields `null` forever (this is what broke Go Live between S060 and F018). Parse the array and render **all** of it, joined (`"Music, Comedy · In person"`), in the stored order — never `types.first`, which silently hides what the organiser chose. The same column and rule apply to `discovery_feed` / `discovery_feed_nearby`, which also project `gathering_types` (the Pulse feed card does not currently render it). Degrade quietly on an absent/empty array: show the medium alone, never a dangling separator. |
| `gathering_detail(uuid)` — poster (S057) | one nullable column `poster_object_path TEXT` (Storage object key, ADR-009 — a reference, never bytes) | `NULL` = no poster → render a template (A-09) or the neutral hero, never a broken image. When present, the poster is served **public, zero-login** at `<SUPABASE_URL>/storage/v1/object/public/gathering-poster/<poster_object_path>` (CDN-cacheable; the object is already ≤ 80 KB — see the upload contract below, so no on-fetch resize needed). The path is opaque; do not parse the `<gathering_id>/<uuid>.<ext>` shape client-side. |
| `list_my_gatherings()` | **≤ 50 gatherings** (the caller's own, ordered live → upcoming → draft → past; id tiebreak) | The "Your gatherings" recovery screen (S052) shows at most **50** of the organiser's gatherings. A 51st is **silently absent** — a deliberate MVP bound; at MVP an organiser with >50 gatherings is not a real case, and a fix is a backend change (raise the `LIMIT` / paginate), not a client workaround. `tier_count` counts only the organiser's **active own** ticket tiers (`is_active AND vendor_category_id IS NULL`) — vendor tiers excluded (reader-sweep, RS021). |
| `my_vendor_events()` | **≤ 50 gatherings** (the caller's OWN registered gatherings, ordered live → upcoming → past; `gathering_id` tiebreak) | The `/vendor` "My events" list (F032 · C1). Returns only gatherings the caller's vendor team(s) hold a **vendor credential** in (own-events-only — never another team's; a non-vendor / signed-out caller gets **zero rows** = the honest empty list, not an error). A 51st is **silently absent** (deliberate MVP bound; a fix is a `LIMIT` bump, not a client workaround). One row per gathering (collapsed across the caller's teams). `presence_status` is the team's `vendor_presence.status` (`LIVE`/`PAUSED`/…) or **`NULL`** when registered-but-not-checked-in — only `LIVE` earns the coral "live now" cue. The client buckets rows into Upcoming/Live/Past from `gathering_status` + `ends_at`; the hub re-resolves the acting team via `vendor_console_context(gid)` on open. |
| `my_team_vendor_orders(uuid, text)` | **≤ 200 orders** (the caller's OWN team orders for ONE gathering, **live-queue-first**: PLACED/PREPARING/READY before terminal states, soonest `placed_at` first within each bucket; `id` tiebreak) | The `/vendor/g/:id` **Orders tab** live queue (S076 · C3). `p_gathering_id` scopes to one gathering; optional `p_status` narrows to a single status (one of `PLACED`/`PREPARING`/`READY`/`COLLECTED`/`REJECTED`/`CANCELLED`/`EXPIRED` — an **unknown value is a `22023` error**, not a silent empty), `NULL` returns every status and the **client buckets by `status`**. Own-team-only (another team's/company's orders never appear, even sharing the gathering — proven adversarially in `s076`; a non-vendor / signed-out caller gets **zero rows** = honest empty, not an error). A 201st order is **silently absent** (a `LIMIT` bump, not a client workaround) — and because ordering is live-first, a trim drops **terminal** rows first, never a live one. `buyer_display` is the buyer's `person.display_name` only (never raw PII / phone / id). **Money-blind**: `listed_value_ugx` is the in-app LISTED value, never a paid/received amount; there is **no PAID state** anywhere in the row (SOUL red line 4). |
| `my_vendor_orders(uuid)` | **≤ 200 orders** (the caller's OWN placed orders, **live-queue-first**: PLACED/PREPARING/READY before terminal states, soonest `placed_at` first within each bucket; `id` tiebreak) | The attendee's **"My order"** status list (S078 · B2 — the buyer-side mirror of `my_team_vendor_orders`). `p_gathering_id` is **optional**: non-`NULL` scopes to one gathering; **`NULL` (the default) returns the caller's orders across ALL gatherings** — the full order history. There is **no `p_status` filter** on this side (B design spec §6 asks only for the gathering filter); the "My order" screen **buckets by `status` client-side**. Own-buyer-only (another buyer's orders never appear, even at the same gathering / same booth — proven adversarially in `s078` across two buyers; a caller with no orders gets **zero rows** = honest empty, not an error). A 201st order is **silently absent** (a `LIMIT` bump, not a client workaround) — and because ordering is live-first, a trim drops **terminal** rows first, never a live one. `vendor_company_name` is the booth's `vendor_company.name` (who the buyer is waiting on); `order_qr_token` is the pickup QR shown at the booth. **Money-blind**: `listed_value_ugx` is the in-app LISTED value ("pay this to the vendor at pickup"), never a paid/received amount; there is **no PAID state** anywhere in the row (SOUL red line 4). |
| `vendor_feed(p_gathering_id, p_limit, p_menu_item_limit)` | **≤ 200 booths AND ≤ 200 menu items per booth** (`p_limit` clamped `[1,200]` default 100; `p_menu_item_limit` clamped `[1,200]` default 50) | The ATTENDEE vendor feed (F034 · B1) at `/g/:id/booths` + booth detail (`vendor_feed`, S023). **Attendance-gated & non-leaky**: a caller without a **CHECKED_IN** ticket for the gathering gets **zero rows** — render the honest "check in to see vendors" empty state, **never an error** (identical shape to "no vendors live yet"). Returns only the **visible** set — `display_status` ∈ `LIVE`/`PAUSED`/`SOLD_OUT` — so a paused/sold-out booth stays **visible-but-labelled**, never hidden. `menu_items` is the frozen `vendor_event_menu` snapshot (`{name, price_ugx, category, sort_order, available}`), pre-ordered `sort_order, name` and **capped to `p_menu_item_limit`** — render in array order; when **`menu_truncated` is true** the on-screen list is partial, so surface `menu_item_total` ("Showing N of M"), never imply it is whole. A 201st booth is silently absent (a `LIMIT` bump, not a client workaround). authenticated-only (anon `REVOKE`d). **Read-only in B1 — no order action; `place_vendor_order` is B2.** |
| `my_vendor_booths()` | **UNBOUNDED** (one row per ACTIVE `menu_template` lineage across all the caller's companies, ordered `vendor_company_id, name`) | The `/vendor/booths` list (F035 · A1). **No `LIMIT`** — a company's active-booth count is already capped by the plan (free = 3), so the set is small by construction; a future large-plan company would want a `LIMIT`/paginate (a backend change, not a client workaround). Own-companies-only (SECURITY DEFINER + `_vendor_company_ids_for_caller()`); a non-vendor / person-less caller gets **zero rows** = the honest first-time state, not an error. Derived per-lineage signals: `category_name` (nullable — uncategorized booth), `used_at_count` (render only when `>0`; never "used at 0"), `category_locked` (paid-use → the read-only category state AND which delete-guard copy to show), `live_gathering_refs UUID[]` (non-empty ⟺ live right now → the one truthful coral spine + the push-to-live affordance). |
| `get_vendor_company(uuid)` | **≤ 1 row** (the one company, member-visible columns) | The `/vendor/booths` entry gate + PlanCapMeter (F035, extended by S077). Adds `vendor_status BOOLEAN` (the durable ADR-035 §5 capability flag — gates the "Your vendor booths" account row; NOT the per-event `VendorPresence` status), `plan TEXT`, `max_active_templates INT` (**nullable** — `null` = uncapped, so PlanCapMeter/at-cap render nothing, never a faked limit; free = 3), `active_template_count INT` (the figure compared to the cap to disable "+ New booth"). `momo_number`/`owner_person_id` are **NULL for a non-owner member**. Zero rows for a caller not in the company. The client has no param-less "my companies" RPC — it enumerates company ids from the RLS-scoped `vendor_team` SELECT (below) and calls this per id. || `my_tier_name_suggestions()` | **≤ 20 tier names** (the caller's OWN past ticket-tier names, DISTINCT on trimmed value, most-recently-used first) | Seeds the §5E tier-name **combobox** (S058) — the organiser's history IS their presets (no preset table). The 21st distinct name is **silently absent**; a deliberate MVP bound — an organiser is very unlikely to have >20 distinct names, and a fix is a backend change (raise the `LIMIT`), not a client workaround. De-dup is **case-sensitive** (`'VIP'` and `'vip'` are both offered); the combobox is a **suggestion list, not a constraint** — free-typing any name (incl. one not in the list) is always allowed. Vendor tier names excluded (RS021); retired (`is_active=false`) names ARE still suggested. Empty history → zero rows (not an error). |
| `discovery_feed(p_limit_n, p_offset_n)` | **≤ 50 rows** (`p_limit_n` clamped to `[1,50]`, default 20; `NULL`/0/negative floored, S014) | The zero-login Pulse feed. **Map-lens columns (S051): `venue_lat DOUBLE PRECISION`, `venue_lng DOUBLE PRECISION`, both nullable.** A venue with a stored point yields its exact WGS-84 coords (`ST_Y`=lat, `ST_X`=lng); a **null-geo venue OR a digital gathering yields `venue_lat=venue_lng=NULL` — no pin** (`FeedItem.venueLat/venueLng` nullable; the map plots only rows where both are non-null; the list still shows the item). Anon+authenticated EXECUTE; caps/columns are the same across both roles. |
| `discovery_feed_nearby(p_lat, p_lng, p_limit_n)` | **each section ≤ 50**; `ranked` honours `p_limit_n` clamped `[1,50]` (S014); radius auto-widens 5→10km→city-wide | Same two nullable **`venue_lat`/`venue_lng`** map-lens columns (S051), per section: `live_now`/`ranked` carry the venue point; **`digital_only` rows are always `NULL`/`NULL`** (no venue). Null-geo venues in `live_now` are `NULL`/`NULL` too. Plot pins only where both are non-null; the section/`is_live`/`score` ordering is unchanged. |

## 6. Write RPCs the organiser flow calls

The Go-Live flow (`create_gathering` → `create_tier` → `publish_gathering` →
`go_live`) writes through these. Wrap every one in `ZukaRpcClient` — never a raw
`supabase.rpc` in a widget/provider.

> **S048 — both organiser writes are now idempotent.** `create_gathering` and
> `create_tier` each take a trailing `p_client_key uuid NOT NULL`: the client mints
> ONE key **per form-fill** (a new key when the form opens/resets; the SAME key
> across every retry of that submission), passed via `mintClientKey()` (a
> dependency-free v4 UUID — `lib/features/organiser/models.dart`). On a replay the
> backend **converges on the existing row** (returns the same id, emits no second
> domain event) instead of double-creating. Consequences for the client:
> `OrganiserRepository` now **auto-retries a network failure** a bounded number of
> times (safe because the write is idempotent), then surfaces the banner —
> *bounded-then-banner*, not the old submit-ambiguity dead-end. The gathering key
> lives on `GoLiveController` (minted in `build`/`reset`); each `TierEntry` carries
> its own key, preserved by `copyWith` across edits/retries. Disable-on-submit and
> commit-once stay as first-line UX, but are no longer the only guard.
>
> **⚠ Convergence is on the KEY ALONE — the replay's payload is IGNORED (RS048-F1).**
> The server returns the row it already has; it does not compare, merge, or reject
> the values you resend. So a key that stays stable across an *edit* is a silent
> stale-write: change the price and re-tap, and you converge onto the OLD tier while
> the UI renders the NEW price. With no edit RPC (deferred) and no
> `list_my_gatherings`, that divergence is permanent and invisible. **The client
> contract that follows is mandatory, not optional:** after a network-ambiguous
> failure the payload MUST freeze alongside the key — retry-only, no edit, no
> discard (`GoLiveState.retryOnly` / `TierEntry.isRetryOnly` implement this; the
> controller replays its own snapshot rather than trusting the draft). A HARD
> rejection (`23514`) definitively did not land, so that path stays editable. If a
> future session needs key-reuse-with-a-different-payload to be an *error* rather
> than a silent converge, that is a server-side payload fingerprint — gate it on an
> edit path existing, or it dead-ends the organiser.

### `create_venue(p_name text, p_address_text text, p_lat double precision, p_lng double precision) → TABLE(venue_id uuid, slug text)` — S051

Map-lens venue self-service (design spec §5.2): mints a venue from a pin-drop inside
the go-live flow ("My venue isn't here"), returns its id + minted slug, and selects it
so the organiser continues without leaving the flow. SECURITY DEFINER, `authenticated`
EXECUTE = t, `anon` = f (verified live). Sets `created_by_person_id = auth.uid()`'s
Person; emits `VenueCreated` (ADR-015).

| Property | Contract the client MUST honour |
|---|---|
| **Uganda bounding box (bounds echoed — RS029-F3)** | `p_lat` must be in **`[-1.5, 4.3]`** and `p_lng` in **`[29.5, 35.1]`** (padded UG extent). Outside → `23514` (`check_violation`). The pin-drop map should **clamp/reject off-box drops client-side** and surface *"place the pin inside Uganda"* — the RPC is the backstop, not the first line. |
| **Per-Person rate limit** | At most **5 venues per Person per rolling hour**; the 6th raises **`ZK429`**. Render a *"you've added a lot of venues — try again shortly"* banner (same `ZK4xx` convention as purchase throttling); do not auto-retry a `ZK429`. |
| **Name required; slug is server-minted** | `p_name` blank/whitespace → `23514`. The returned `slug` is `slugify(name)-<6hex>` — **distinct even when two venues share a name** (duplicates are tolerated at MVP; disputes are human, ADR-014). Use the returned `venue_id` to select the venue in the go-live form; treat `slug` as opaque. |
| **`p_address_text` optional** | Blank → stored `NULL`. `p_lat`/`p_lng` are **required** (a physical pin); a null coordinate → `23514`. |

### `deactivate_venue(p_venue_id uuid) → boolean` — S051

Retires a self-service venue the caller created (removes it from the venue picker).
SECURITY DEFINER, `authenticated` EXECUTE = t, `anon` = f. Sets `is_active = false`
**only** (the row + its geo are retained); emits `VenueDeactivated`.

| Property | Contract the client MUST honour |
|---|---|
| **Creator-only** | Only the venue's `created_by_person_id` may deactivate it; anyone else (and a founder-seeded, ownerless venue) → **`42501`** (`insufficient_privilege`). Only surface the deactivate affordance on venues the Person created. |
| **No-orphan block** | Hard-blocked while **any non-terminal (live or upcoming) gathering** still uses the venue (status not in ENDED/CLOSED/CANCELLED) → **`23514`**. Surface *"end or move its gatherings first"*; the map is unaffected either way (pins are gathering-driven, not venue-driven). |
| **Idempotent** | Deactivating an already-inactive venue returns `true` with no second event. |

### `create_gathering(p_organiser_id, p_venue_id, p_title, p_starts_at, p_ends_at, p_gathering_types text[], p_gathering_medium, p_client_key uuid) → uuid`

Creates one DRAFT `gathering` (is_discoverable=false) and returns its id.
SECURITY DEFINER, auth-bound + impersonation-guarded (`p_organiser_id` must equal
the `auth.uid()` person), emits `GatheringCreated` (ADR-015). Grant posture
verified live: `authenticated` EXECUTE = t, `anon` = f (8-arg signature).

| Property | Contract the client MUST honour |
|---|---|
| **Idempotent via `p_client_key`** | UNIQUE(organiser_id, client_key). A replay with the same key returns the **same** gathering id and emits **no** second event. A `NULL` key raises `23514`. Different organisers may reuse the same key (the scope is per-organiser) — a non-collision by construction. |
| **Retry is safe — with the payload frozen** | A network-ambiguous failure may be re-tapped (or is auto-retried by the repo, bounded and jittered): a landed write converges. Keep the key stable across the retry **and resend the same values** — convergence ignores the payload, so an edited replay silently returns the original row (see the ⚠ above). `reset()` (a genuinely new gathering) re-mints. |
| **A converge that finds no row RAISES** | If `ON CONFLICT DO NOTHING` fires but the re-SELECT finds nothing (only reachable above READ COMMITTED), the function raises `internal_error` rather than returning NULL — a NULL would surface as a Dart cast failure on `id as String`, three layers from the cause. Unreachable at PostgREST's default isolation; do not code against it. |
| **`p_gathering_types` is a `text[]` of 1–6 tokens (S060)** | **Breaking change — the scalar `p_gathering_type TEXT` no longer exists**; sending it fails every Go Live (the old 8-arg signature was DROPped, not kept alongside). Send a Dart `List<String>` of lowercase tokens (`music`/`nightlife`/`comedy`/`theatre`/`arts`/`networking`/`sports`/`other` — `kGatheringTypes`), in the organiser's selection order; the array is stored and read back in that order. Cardinality **must be 1..6** and **no element may be NULL** — both raise `23514` with a readable message, and the table's `gathering_types_cardinality_chk` is the backstop. The client enforces both in the chip group's *tap handler* (§5B `MultiChoiceSelector`), so neither is reachable from the UI; the RPC guard is **a backstop, not the first line of defence**. There is **no** DB vocabulary CHECK — an off-list token would be accepted, so the token set is a client-side contract. |

### `create_tier(p_gathering_id uuid, p_tier_name text, p_price_ugx bigint, p_capacity int, p_client_key uuid, p_seats_per_unit int = 1) → uuid` — F006 / S048 / S072

Creates one `gathering_tier` row and returns its id. SECURITY DEFINER,
auth-bound to `auth.uid()`, **ownership-guarded** (only the gathering's organiser
may call it), **8-tier-capped** (Vol 14 Ch2 — the 9th raises `23514`), emits
`GatheringTierCreated` (ADR-015). Grant posture verified live: `authenticated`
EXECUTE = t, `anon` = f (6-arg signature).

| Property | Contract the client MUST honour |
|---|---|
| **Idempotent via `p_client_key`** | UNIQUE(gathering_id, client_key). Each `TierEntry` mints ONE key at creation and keeps it across retries, so a dropped-response re-tap **converges** on the existing tier (same id, no second event) instead of duplicating. The idempotency check runs BEFORE the 8-cap, so **replaying the 8th tier converges** rather than tripping the cap. A `NULL` key raises `23514`. The same key on a DIFFERENT gathering is a non-collision (scope is per-gathering). Commit-once stays as a first line of defence; N tiers are still N separate non-atomic calls (tier 3 can fail after 1–2 land) — track per-row state, never "retry the whole list". |
| **`p_price_ugx` is BIGINT integer UGX** | Whole shillings (SOUL.md; UGX has no subunit). Parse the field to `int` (`parseIntField`) or reject — **no `double`/`num`/float anywhere on the price path**. The client rejects decimals/separators/negatives/blank BEFORE the call; the RPC's `23514` (`price_ugx >= 0`) is a **backstop, not the first line of defence**. `0` renders **"Free"**, never "UGX 0" (PriceText law). |
| **`p_capacity` null = unlimited** | Blank field → `NULL`. Same integer parser as price. The schema permits `capacity = 0` (a tier sold out from birth → `ZK410` on first claim) — the **client rejects 0**; `create_tier` does not. |
| **`p_seats_per_unit` = party size per unit ("Capacity" in the UI, S072)** | How many people ONE purchase unit of this tier admits (Couple=2, Family=4, Table of 8=8). **Integer 1..50** — same integer-only parse/reject as price; the RPC's `23514` is a backstop. **Omit the arg (or the whole field) → the RPC defaults it to `1`** (byte-identical to today's single admission); do NOT send `0` or `NULL` (both are `23514`). Do NOT confuse this with `p_capacity`, which is **"Units" = number-of-tables/units on sale** — a distinct column, unchanged by S072. This slice ships the tier authoring surface only; ticket inheritance + scanner party admission are later slices (the ticket does not yet carry a party size). |
| **⚠ `p_tier_name` is LOAD-BEARING, not a label** | `claim_free_ticket` and the paid path's `_issue_pending_ticket` derive the ticket's class: `ticket_type := lower(tier_name)`, kept iff it is one of **`standard · group · table · vip · streaming · comp · staff`**, else coerced to `standard`. So a tier literally named **"Comp"** or **"Staff"** mints comp/staff tickets. This is the deliberately-open S007/S008 `ticket_type`-FK gap — do **not** "fix" it in the client, but any tier-naming UI must know those seven names are magic. `General entry` (the Skip default) → `standard`, the intended safe class. |
| **Legal states** | Accepts `DRAFT/PUBLISHED/ACTIVE/LIVE`; rejects `CANCELLED/ENDED/CLOSED` (`23514`). So a tier can be added **even after go-live** — the R1 recovery path (a gathering that went live with no tier is still fixable). |
| **Listed price only — no rail** | This sets a *listed* price; **no funds move, no payment rail is touched**. Ticket money settles via Flutterwave; ZUKA is not an aggregator (NPS Act 2020). |
| **Error codes** | `insufficient_privilege` (not signed in / not the organiser / no Person) → treat as app-state error, re-auth; `no_data_found` (gathering id unknown); `23514` (`check_violation`: bad price/capacity sign, dead status, or the 8-tier cap) → surface "That's not something you can do right now," never the raw constraint. Network/timeout → the write MAY have landed: retry with the **same `p_client_key` and the same values** (bounded+jittered auto-retry, then the retry-only frozen state — RS048-F1) and a landed write converges on the existing tier, no duplicate. Never re-mint the key for the same tier; never edit values mid-retry (convergence ignores the payload — see the ⚠ above). |

Consistency note: `gathering_detail` returns only the **8 cheapest** attendee
tiers (§5) and the cap here is **8**, so nothing an organiser creates through
`create_tier` can be silently invisible on the attendee Detail screen.

### `amend_tier(p_tier_id uuid, p_tier_name text = NULL, p_price_ugx bigint = NULL, p_capacity int = NULL, p_seats_per_unit int = NULL) → TABLE(tier_id uuid, tier_name text, price_ugx bigint, capacity int, seats_per_unit int, tickets_sold int)` — S059 / S072

Edits ONE of the organiser's OWN ticket tiers **after go-live** — fix a name /
price / capacity typo. Before S059 `gathering_tier` had no client UPDATE path at
all. SECURITY DEFINER, auth-bound to `auth.uid()`, **ownership-guarded** (only the
gathering's organiser may call it), emits `TierAmended` (ADR-015). Grant posture
verified live in `s059`: `authenticated`=t, `anon`=f, `service_role`=f.
**Listed price only — no funds move, no payment rail is touched** (ZUKA is not an
aggregator, NPS Act 2020). Returns the **authoritative post-amend row** — render
from it, no re-read needed.

| Property | Contract the client MUST honour |
|---|---|
| **Partial update — NULL = leave unchanged** | Send only the field(s) the organiser edited; a `NULL` param leaves that column as-is. **At least one non-NULL field is required** — an all-NULL call is `23514` (`check_violation`), not a no-op. Build the call from a diff of the edited form against the loaded tier, not the whole form. |
| **⚠ Capacity can be LOWERED or RAISED but never set back to unlimited** | `p_capacity = NULL` means *unchanged*, and unlimited is *also* represented as `NULL` on the row — they collide. So this RPC can set a finite capacity or change it, but **cannot** turn a finite capacity back into unlimited. If the UI offers "make unlimited," disable it for now and surface why; a future RPC revision (a `p_clear_capacity` flag) will close this. Known gap, S059. |
| **Capacity floor — reject-only, never revokes** | A `p_capacity` **below `tickets_sold`** is rejected `23514` (`check_violation`); it is **never** clamped and **never** revokes an issued ticket (ADR-014 — revocation is human). `capacity == tickets_sold` is allowed (freezes further sales). The RPC locks the tier row `FOR UPDATE`, so a racing purchase cannot slip the count under the floor (`s018_15`). To go below the sold count, the organiser must refund first — there is no force path. |
| **Price partition — no free↔paid crossing** | A price change that crosses the `price_ugx = 0` boundary in **either** direction (free→paid or paid→free) is rejected `23514`. Same-side changes (a paid amount → a different paid amount) are allowed. `p_price_ugx` is **BIGINT integer UGX** — same integer-only parse/reject as `create_tier` (no `double`/float on the price path); `0` renders **"Free"**. Crossing the boundary is deferred (it would strand issued receipts) — surface "you can't switch a tier between free and paid after it exists," not the raw constraint. |
| **`p_seats_per_unit` = party size, NULL = unchanged (S072)** | The new trailing sentinel field: `NULL` leaves the tier's party size as-is; a non-NULL value **1..50** changes it (out-of-range → `23514`). Counts toward the "at least one non-NULL field" rule — an amend that changes ONLY the party size is valid. The returned row now carries `seats_per_unit`; render from it. Same "Capacity" (seats/unit) vs "Units" (`capacity` = number-of-tables) distinction as `create_tier`. **B2 read note:** the organiser tier-load (`OrganiserRepository.listMyTiers`) reads `gathering_tier` directly with an **explicit column list** (`id,tier_name,price_ugx,capacity,tickets_sold`); the new column is table-SELECT-granted (anon+authenticated) but will NOT appear until B2 adds `seats_per_unit` to that select and to `AmendableTier.fromRow`. |
| **Receipts are immutable** | Amending the tier's live price does **not** touch any already-issued `ticket.price_ugx` — a sold ticket keeps its paid snapshot. The organiser is changing the *listed* price for future sales only. |
| **Vendor tiers are not amendable here** | A vendor tier (`vendor_category_id` set) is rejected `23514` — vendor tier amendment is separate future work. This RPC is for the organiser's own ticket tiers only. |
| **Error codes** | `insufficient_privilege` (not signed in / not the organiser / no Person) → app-state error, re-auth; `no_data_found` (unknown tier id); `23514` (`check_violation`: all-NULL, capacity below sold, free/paid crossing, bad price/capacity sign, or a vendor tier) → surface "That's not something you can do right now," never the raw constraint. **Not idempotent / no `client_key`** — the RPC SETs absolute values (`price_ugx = COALESCE(p_price_ugx, …)`), so a re-fire with an *identical* payload is state-idempotent (same final row) — it does NOT stack a second price change. The real cost of a blind re-fire is a redundant `TierAmended` event whose `before == after` (event-log noise), so it must still NOT be blindly auto-retried; on timeout, **re-read the tier and reconcile against the intended values** before any retry. Idempotent double-submit handling (dedup of the redundant event) is a known deferred gap (S059). |

### `list_my_gatherings() → TABLE(id uuid, title text, status text, starts_at timestamptz, ends_at timestamptz, venue_name text, tier_count int)` — S052

The organiser **recovery-read**: the caller's OWN gatherings, render-ready for the
"Your gatherings" screen (Hosting A2). **No parameters** — caller-scoped by
construction (`auth.uid()` → person; nothing to inject). Read-only, STABLE, no funds
move, no event emitted. SECURITY DEFINER, `authenticated`-only (grant verified live
in `s052`: `anon`=f / `authenticated`=t / `service_role`=f — the 4-role lock). This
is the surface that recovers a crashed-app orphaned DRAFT and the publish-ok/
go-live-failed partial chain: a row tap routes to the existing §5C Ready screen,
which already renders DRAFT/PUBLISHED/LIVE by `status`.

| Property | Contract the client MUST honour |
|---|---|
| **Caller-scoped, zero cross-visibility** | Returns ONLY the caller's own gatherings; another organiser's are never visible (proven adversarially in `s052`, two organisers, cross-visibility asserted zero). The client passes no person/organiser id — the server binds it. |
| **Bounded ≤ 50 (see §5)** | At most 50 rows, ordered live → upcoming → draft → past (id tiebreak). A 51st is silently absent — a deliberate MVP bound, not a client workaround. |
| **Ordering is load-bearing for the screen** | `live` (status LIVE) → `upcoming` (PUBLISHED/ACTIVE, still ahead) → `draft` (status DRAFT, **even if its date has passed** — an orphaned draft must surface for recovery, never buried in past) → `past` (ENDED/CLOSED/CANCELLED, or elapsed). Live+upcoming soonest-first; draft+past most-recent-first. Render section headers off this order; don't re-sort client-side. |
| **`status` is the branch key** | One of DRAFT/PUBLISHED/ACTIVE/LIVE/ENDED/CLOSED/CANCELLED. Render the StatusPill **in words** (§2 A2 scope fence — recovery surface, not a dashboard). No edit, no delete, no analytics, no attendee counts on this screen. |
| **`tier_count`** | Count of the organiser's **active own** ticket tiers (`is_active AND vendor_category_id IS NULL`) — 0/1/n. Vendor tiers excluded. `0` is a real, honest state (a live gathering with no tier yet — the R1 recovery case); render it plainly, it is not an error. |
| **`venue_name` is nullable** | `NULL` when the gathering has no venue (venue is optional). Render a neutral placeholder; never fabricate a name. |
| **Empty list = the honest zero state, NOT an error** | A caller with no gatherings **or a person-less authed caller** gets **zero rows** and **no exception** (never a `42501` leak-through). `OrganiserRepository` maps empty → the "you haven't hosted anything yet → Create" entry, exactly as the Hosting entry does when the organiser has no gatherings. Anon can't EXECUTE (`42501` → the wrapper's generic error). |
| **No cache** | A point-in-time read; re-resolve on screen (re)entry rather than caching across visits — a gathering's status can change from another device or from the go-live path itself. Not in `kNoCacheRpcs` (that list is admission-verification only, ADR-021), but treat it as live-read. |

### `my_vendor_events() → TABLE(gathering_id uuid, title text, starts_at timestamptz, ends_at timestamptz, gathering_status text, presence_status text)` — F032

The vendor **"My events" read**: the gatherings the caller's vendor team(s) are
registered for, render-ready for the `/vendor` list (C1 — the door F009's
deep-link-only console lacked). **No parameters** — caller-scoped by construction
(`auth.uid()` → `_vendor_team_ids_for_caller()`; nothing to inject). Read-only,
STABLE, no funds move, no event emitted. SECURITY DEFINER, `authenticated`-only
(grant verified live in `s075`: `anon`=f / `authenticated`=t / `service_role`=f —
the 4-role lock). A row tap routes to `/vendor/g/:id`, the per-event hub (Status ·
Menu · Orders), which re-resolves the acting team via `vendor_console_context(gid)`.

| Property | Contract the client MUST honour |
|---|---|
| **Own-events-only, zero cross-visibility** | Returns ONLY gatherings the caller's own team(s) hold a **vendor credential** in; another team's are never visible (proven adversarially in `s075`, two companies + a non-vendor, cross-visibility asserted zero). The client passes no team/company id — the server binds it. |
| **Bounded ≤ 50 (see §5)** | At most 50 rows, ordered live → upcoming → past (`gathering_id` tiebreak). A 51st is silently absent — a deliberate MVP bound, not a client workaround. |
| **Bucketing** | The client buckets each row into **Upcoming / Live / Past** from `gathering_status` (`LIVE` → live; `ENDED`/`CLOSED`/`CANCELLED` **or** `ends_at < now` → past; else upcoming) — the same rule as the server's `sort_rank`. No DRAFT bucket (a vendor never holds a credential in a draft). |
| **`presence_status` is nullable** | The team's `vendor_presence.status` (`LIVE`/`PAUSED`/`SOLD_OUT`/`CLOSED`) or **`NULL`** when registered-but-not-yet-checked-in (no presence row). Only `LIVE` earns the coral "live now" cue (Vol 25 Ch4); never fabricate a status from `NULL`. |
| **One row per gathering** | A gathering the caller is registered in via two teams collapses to a single row (the presence of the team with the most recent presence). |
| **Empty list = the honest zero state, NOT an error** | A non-vendor **or a person-less authed caller** gets **zero rows** and **no exception** (never a `42501` leak-through). The client maps empty → the "you're not a registered vendor yet" empty state. Anon can't EXECUTE (`42501` → the wrapper's generic error). |
| **No cache** | A point-in-time read; re-resolve on screen (re)entry — a gathering's status / the team's presence can change from the gate or another device. |

### `my_team_vendor_orders(p_gathering_id uuid, p_status text = NULL) → TABLE(id uuid, buyer_display text, status text, items jsonb, listed_value_ugx bigint, order_qr_token uuid, placed_at timestamptz, ready_at timestamptz, collected_at timestamptz, terminal_at timestamptz)` — S076

The vendor **Orders-tab read**: the caller's team orders for one gathering,
render-ready for the `/vendor/g/:id` **Orders** queue (C3). `p_gathering_id` scopes
to the open event; the caller passes no team id — the server binds it
(`auth.uid()` → `_vendor_team_ids_for_caller()`). Read-only, STABLE, no funds move,
no event emitted. SECURITY DEFINER, `authenticated`-only (grant verified live in
`s076`: `anon`=f / `authenticated`=t / `service_role`=f — the 4-role lock). This is
the **read** side of fulfilment; the transitions (accept / ready / reject / collect)
are the S025 write RPCs, unchanged.

| Property | Contract the client MUST honour |
|---|---|
| **Own-team-only, zero cross-visibility** | Returns ONLY orders whose `vendor_team_id` is in the caller's own team set; another team's or another company's orders are never visible, even when both vend at this gathering (proven adversarially in `s076`, two companies sharing one gathering). |
| **Live-queue-first ordering, bounded ≤ 200 (see §5)** | Active states (`PLACED`/`PREPARING`/`READY`) sort before every terminal state; within each bucket the **oldest `placed_at` is first** (top of the queue). At most 200 rows — a 201st is silently absent, and a trim drops terminal rows first, never a live one. Render in the server order; do not re-sort the queue client-side. |
| **`p_status` filter** | `NULL` → every status (the client buckets by `status`). Non-`NULL` → exactly that status; it **must** be one of the seven domain values — an unknown value raises `22023` (surface the generic error, don't send free text). |
| **`buyer_display` is the only buyer PII** | The buyer's `person.display_name`, nothing more — no phone, no email, no raw `buyer_person_id`. Render it as the order's name; never expect a contact handle here. |
| **Money-blind** | `listed_value_ugx` is the in-app **LISTED** order value (server-computed at placement), **never** a paid/received/confirmed amount, and there is **no PAID state** — the status set has none (SOUL red line 4 · NPS Act). Show it as "listed", never as "paid"/"received"; the buyer→vendor money is off-platform and ZUKA is blind to it. |
| **`items` is frozen product data** | The line-item snapshot at placement (`[{name, unit_price_ugx, quantity, line_value_ugx}]`) — product data, not PII. Render the ticket; don't re-price it. |
| **Empty list = the honest zero state, NOT an error** | A team with no orders in this gathering — or a non-vendor / person-less authed caller — gets **zero rows** and **no exception**. Map empty → the "no orders yet" state. Anon can't EXECUTE (`42501`). |
| **No cache** | A live queue — re-resolve on (re)entry and on realtime/poll ticks; an order's status changes from the write RPCs on this or another device. |

### `my_vendor_orders(p_gathering_id uuid = NULL) → TABLE(id uuid, vendor_company_name text, status text, items jsonb, listed_value_ugx bigint, order_qr_token uuid, placed_at timestamptz, ready_at timestamptz, collected_at timestamptz, terminal_at timestamptz)` — S078

The attendee's **"My order" read**: the caller's OWN placed orders, render-ready for
screen 4 of the B2 ordering client — the **buyer-side mirror** of
`my_team_vendor_orders`. The caller passes no person/buyer id — the server binds it
(`auth.uid()` → `person.auth_user_id` = the same identity `vendor_order_select_buyer`
uses). `p_gathering_id` is **optional**: non-`NULL` scopes to one gathering's orders;
**`NULL` (the default) returns every gathering's orders** — the buyer's full history.
Read-only, STABLE, no funds move, no event emitted. SECURITY DEFINER,
`authenticated`-only (grant verified live in `s078`: `anon`=f / `authenticated`=t /
`service_role`=f — the 4-role lock). This is the **buyer read** side of fulfilment;
the transitions (place / cancel, and the vendor's accept/ready/collect) are the
S025/S027 write RPCs, unchanged.

| Property | Contract the client MUST honour |
|---|---|
| **Own-buyer-only, zero cross-visibility** | Returns ONLY orders whose `buyer_person_id` resolves to the caller; another buyer's orders are never visible, even at the same gathering and the same booth (proven adversarially in `s078`, two buyers sharing one gathering). |
| **Live-queue-first ordering, bounded ≤ 200 (see §5)** | Active states (`PLACED`/`PREPARING`/`READY`) sort before every terminal state; within each bucket the **oldest `placed_at` is first**. At most 200 rows — a 201st is silently absent, and a trim drops terminal rows first, never a live one. Render in the server order. |
| **Optional gathering filter, NO status filter** | `p_gathering_id = NULL` → all of the caller's orders across every gathering; non-`NULL` → just that gathering's. There is **no `p_status` param** on this side (unlike the team RPC) — the "My order" screen buckets by `status` **client-side**. |
| **`vendor_company_name` identifies the booth** | The booth's `vendor_company.name` (via `vendor_order.vendor_company_id`) — "who am I waiting on / paying". No vendor phone/id on the wire. |
| **`order_qr_token` is the pickup QR** | The UUID the buyer shows at the booth; the vendor scans it to `collect_vendor_order` (READY→COLLECTED). Render it as the pickup code/QR while the order is live. |
| **Money-blind** | `listed_value_ugx` is the in-app **LISTED** order value (server-computed at placement), **never** a paid/received/confirmed amount, and there is **no PAID state** (SOUL red line 4 · NPS Act). Frame it as "pay this to the vendor at pickup", never as "paid"; the buyer→vendor money is off-platform and ZUKA is blind to it. |
| **`items` is frozen product data** | The line-item snapshot at placement (`[{name, unit_price_ugx, quantity, line_value_ugx}]`) — product data, not PII. Render it; don't re-price it. |
| **Empty list = the honest zero state, NOT an error** | A caller who has placed no orders (or none in the filtered gathering) gets **zero rows** and **no exception**. Map empty → the "no orders yet" state. Anon can't EXECUTE (`42501`). |
| **No cache** | A live status view — re-resolve on (re)entry and on realtime/poll ticks; the status changes from the write RPCs on this or another device. |

### `my_tier_name_suggestions() → TABLE(tier_name text)` — S058

The organiser's **own tier-name history**, as presets for the §5E create-tier name
field (which becomes a **combobox**). **No parameters** — caller-scoped by
construction (`auth.uid()` → person; nothing to inject). Read-only, STABLE, no funds
move, no event emitted. SECURITY DEFINER, `authenticated`-only (grant verified live
in `s058`: `anon`=f / `authenticated`=t / `service_role`=f — the 4-role lock). The
YAGNI shape of the founder's "remember their ticket types" ask: **the organiser's past
tier names ARE their presets** — no preset table, no write path, no management UI.

| Property | Contract the client MUST honour |
|---|---|
| **Caller-scoped, zero cross-visibility** | Returns ONLY the caller's own past tier names; another organiser's are never visible (proven adversarially in `s058`, two organisers, cross-visibility asserted zero). The client passes no id — the server binds it. |
| **Bounded ≤ 20 (see §5)** | At most 20 names, most-recently-used first (recency = the newest `created_at` among tiers sharing that name; name ASC tiebreak). A 21st distinct name is silently absent — a deliberate MVP bound. |
| **Suggestions, not a constraint** | Render as combobox suggestions. The organiser can always **free-type** a brand-new name not in the list — this RPC never limits what `create_tier(p_tier_name …)` will accept. Do not disable free entry when the list is non-empty. |
| **DISTINCT on the TRIMMED name; case-sensitive** | `'VIP '` and `'VIP'` collapse to one suggestion (server trims). But `'VIP'` and `'vip'` are **distinct** offerings — the server can't know the organiser's preferred casing, so both appear. Blank/whitespace-only names are never suggested. |
| **Own ticket tiers only; retired names kept** | Counts the caller's `gathering_tier` rows with `vendor_category_id IS NULL` (vendor tier names excluded — RS021, same as `create_tier`'s surface). `is_active` is NOT filtered: a name from a **retired** tier is still a valid preset and IS suggested. |
| **Empty list = honest zero state, NOT an error** | A caller with no tier history **or a person-less authed caller** gets **zero rows** and **no exception** (never a `42501` leak-through). Render "no suggestions yet" and leave the field free-type. Anon can't EXECUTE (`42501` → generic error). |
| **No cache** | A point-in-time read; re-resolve when the create-tier field opens rather than caching across sessions — the organiser may have created new tiers since. Not in `kNoCacheRpcs` (admission-verification only), but treat it as live-read. |

### `attach_gathering_poster(p_gathering_id uuid, p_object_path text) → TABLE(gathering_id uuid, poster_object_path text, poster_updated_at timestamptz)` · `clear_gathering_poster(p_gathering_id uuid) → void` — S057

The **poster upload is two steps**, both organiser-only (`authenticated`; `anon` gets
`42501`). This RPC does **not** carry bytes — bytes go through the Storage API.

1. **Compress on-device to ≤ 80 KB** (the hero budget), then **upload the bytes to the
   `gathering-poster` bucket** via `supabase.storage` at path
   **`<p_gathering_id>/<v4-uuid>.<ext>`** (jpeg/webp/png). Storage RLS lets an organiser
   write only under a gathering id they own; the bucket hard-caps at 96 KiB.
2. **Call `attach_gathering_poster`** to link the uploaded object. It re-validates
   ownership, that the object exists, is **≤ 80 KB** (`ZK413` if not — the client must
   compress before uploading, never rely on the server to shrink), and has an allowed
   image mime; then it sets `gathering.poster_object_path` and returns it. Render the
   poster from the §5 serve URL once this returns.

| Property | Contract the client MUST honour |
|---|---|
| **Two-step, ordered** | Upload bytes **first**, then attach. Attaching a path with no uploaded object → `no_data_found` (`ZK404`-class). Never call attach against a path you haven't finished uploading. |
| **Path shape is fixed** | `<p_gathering_id>/<uuid>.<ext>` — the first segment MUST be the gathering id or attach rejects it (`check_violation`); this is what binds the object to the gathering. Mint the `<uuid>` client-side (`mintClientKey()`); keep the real file extension. |
| **≤ 80 KB is the client's job** | The server rejects an over-budget object (`ZK413`) — it does not resize. Show the honest "2.1 MB → 74 KB — loads 28× faster on 3G" compression result (A-08) and route to templates (A-09) on a compression/upload failure. |
| **Replace = re-upload + re-attach** | To change the poster, upload a new object and attach it (the reference just re-points; orphan bytes are cleaned by a later janitor). `clear_gathering_poster` drops the reference (human takedown, ADR-014) → `poster_object_path` becomes `NULL`; delete the bytes via the Storage API `remove()` (owner-only DELETE policy). |
| **Behind go-live only** | Never on the 60-second path — the poster is an enrichment offered *after* go-live (A-08 iron rule). No funds move; not in `kNoCacheRpcs`. |

## 7. Write RPCs the attendee free-claim flow calls (F008)

The free-claim flow (Detail tier-select → `[Count me in — Free]` → lazy
AccountSheet → claim) writes through these two **existing, unmodified** RPCs. Both
go through `ZukaRpcClient` (except the auth legs, which are Supabase-Auth calls, not
`rpc()` — see `AuthRepository`). The **paid** path
(`initiate_ticket_purchase` → Flutterwave → `confirm_ticket_payment`) is a
separate, money-gated session and is NOT part of F008.

### `claim_free_ticket(p_gathering_id uuid, p_tier_id uuid) → TABLE(ticket_id uuid, ticket_ref text)` — S028

Issues ONE born-**ISSUED** free ticket for a **FREE** (`price_ugx = 0`) attendee
tier and returns its `(ticket_id, ticket_ref)` as a **single-row table** (decode
`rows.first`; an empty result is a contract violation → generic error, never a
blank-ref "success"). SECURITY DEFINER, **`authenticated`-only** (grant verified
live in s025: `anon`=f / `authenticated`=t), binds the Person via `auth.uid()`
itself — the client passes only the two ids, never a person id. A free ticket has
**no payment step**: `qr_token` is set at issue, there is no PENDING/confirm
round-trip, no `tx_ref`. Money posture: no funds move (Vol 14; NPS Act 2020).

| Property | Contract the client MUST honour |
|---|---|
| **Idempotent per (Person, Gathering)** | One LIVE free ticket per `(original_purchaser_id, gathering)` — a repeat claim raises **`ZK409`**. **`ZK409` is a SUCCESS-shaped state, not an error**: the client lands the person on/through to their ticket ("You already have a free ticket for this gathering." + a path to Tickets), never a red banner. |
| **Commit-once (client-side, belt-and-braces)** | `claim_free_ticket` is idempotent server-side, but the client STILL fires it **exactly once**: disable-on-submit + a single in-flight guard (`ClaimPhase.claiming`/`claimed` blocks re-entry — `TicketClaimController.claim`). A double-tap / rebuild never spawns a second call. Mutation-teeth pinned (`ticket_claim_test.dart`, `free_claim_flow_test.dart`). |
| **No blanket retry** (ADR-021) | A claim is not safely blind-retryable beyond the server's own idempotency. On a **network-ambiguous** failure the client shows honest copy (*"We couldn't confirm your ticket went through — check Tickets, or try again."*) and a path to Tickets — the person re-taps by hand (safe: a landed claim returns `ZK409` → soft success). The wrapper never auto-retries. |
| **Error codes → copy (§7.2b)** | `ZK429` velocity (checked FIRST, server-side) → *"You're going a bit fast — try again in a moment."* · `ZK410` sold out → *"That ticket type just sold out."* · `ZK409` already-claimed → **soft success** (above) · `23514` `check_violation` (gathering not PUBLISHED/ACTIVE/LIVE · a **paid** tier sent to the free path · a **vendor** tier · capacity full) → *"That's not something you can do right now."* · `no_data_found` (unknown gathering/tier) · `insufficient_privilege` (no session / no Person — should not happen post-AccountSheet; log to Sentry, generic copy) · offline/timeout → the network-ambiguous posture above. |
| **The client never sends a paid/vendor tier** | The primary button arms by tier price (`price_ugx == 0` → `[Count me in — Free]`; `> 0` → the §6.7 paid-gated disabled state, never a claim). The paid-tier / vendor-tier `23514`s exist because the **server** is the authority, not because the client should reach them. Vendor tiers never even appear on Detail (S026 read isolation). |

### `register_person(p_display_name text, p_consent_data_processing boolean, p_consent_marketing boolean) → uuid` — F003

Self-provisions the caller's own `person.id` (idempotent via
`ON CONFLICT (auth_user_id)` — a repeat returns the existing id, never a second
Person), called right after Supabase-Auth sign-up once a session exists. This is
the **shared** provisioning chain the organiser §5D Sign-up AND the attendee
AccountSheet (§7.1) both call — one identity surface, two skins; the F008
AccountSheet reuses it unchanged (never a parallel auth path), and the future
paid path will reuse the same AccountSheet.

| Property | Contract the client MUST honour |
|---|---|
| **DPPA consent is a TYPED argument, never implied** | `p_consent_data_processing` is captured as an **explicit, un-pre-checked** opt-in in the sheet and passed as a boolean; a refused consent (or blank name) raises `23514` → mapped copy. The client validates consent BEFORE the call (a refused consent is a clean dead-end: no account, no ticket, no dark pattern) — the `23514` is a backstop. **Never fabricate consent** (DPPA §12): the confirm-then-provision path only provisions when consent was explicitly stashed `true`. |
| **`40001` retry-once — the ONLY safe retry** | The extreme-race branch marks `40001` retriable; because `register_person` is idempotent, `OrganiserRepository.registerPerson` retries it **once**. This does NOT generalise (ADR-021) — `claim_free_ticket`/`create_tier` get no such retry. |
| **No PII on any wire the client adds** | Password goes straight to Supabase Auth (never logged, never a Sentry breadcrumb, never in a `domain_events` payload). `register_person` writes no raw PII to the append-only store. |

### `register_device_token(p_token text, p_platform text) → uuid` · `unregister_device_token(p_token text) → boolean` — S049

The FCM push registry. Call `register_device_token` right after the OS push-
permission grant (and on every token refresh the FCM SDK reports), passing the
current registration token and `platform` (`android`|`ios`|`web`). Call
`unregister_device_token` on sign-out / push-permission revocation. Both are
authenticated-only (a device token belongs to a signed-in Person); the caller
must have a provisioned Person (`register_person` first) or `register_device_token`
raises `insufficient_privilege`.

| Property | Contract the client MUST honour |
|---|---|
| **Per-person cap = 5 (evicts oldest)** | The backend keeps at most **5** device tokens per Person, evicting the least-recently-registered when a 6th arrives (abuse control). A user signed in on a 6th device silently loses push on their oldest device — by design. The client does not manage the cap; it just registers the current device's token and lets the server evict. Do **not** expect all historical devices to remain registered. |
| **Idempotent upsert keyed on the token** | Re-registering the SAME token (app relaunch, no refresh) returns the **same** row and only refreshes `last_seen_at` — never a duplicate. Safe to call on every launch. `platform` is updated on re-register. |
| **`platform` is a 3-value allowlist** | `android`\|`ios`\|`web` only; anything else raises `23514`. |
| **`unregister` returns whether a row was removed** | `true` = the caller's token was hard-deleted; `false` = nothing matched (already gone / not owned). A second unregister is a clean no-op (`false`). Scoped to the caller's own Person — you can never unregister another account's token. |
| **The token never leaves the token table** | The FCM token is personal data (DPPA): it is stored ONLY in `person_device_token`, never written to `domain_events` or any queue payload, and is resolved just-in-time by the `send-fcm` worker. Erasure is hard-delete (unregister + person-cascade). |
| **Errors → copy** | `insufficient_privilege` (no session / no Person) → re-auth, generic copy; `23514` (blank token / bad platform) → treat as a client bug, do not surface raw. Network/timeout → safe to retry with the **same** token (idempotent). |

## 8. The vendor-console RPCs (F009/F010)

The vendor console (`/g/:id/vendors`, `lib/features/vendor/`) resolves the
caller's own acting context, browses open vendor tiers, registers a credential,
and toggles a team's live status. All four RPCs are **`authenticated`-only**
(anon REVOKEd from EXECUTE), SECURITY DEFINER, and **membership-guarded**
server-side via `_vendor_company_ids_for_caller()` — the client passes only
ids, never a company/person/team-membership assertion. Wrap every one in
`ZukaRpcClient` (`VendorRepository`), never a raw `supabase.rpc`.

> **Console-context seam — CLOSED by F010.** `vendor_console_context(gid)` (below)
> now resolves *my* vendor team + terms template + presence for a gathering —
> the vendor-self lookup `vendor_feed` never was. `vendorConsoleContextProvider`
> calls it on console load; `null` (no acting team) still degrades register/toggle
> to the explanatory states. The F009-era injected-context seam survives only as
> the tests' override point.
>
> **What is live today.** All three console capabilities are reachable
> end-to-end: **browse** (`list_open_vendor_tiers`), and — now that the resolver
> produces a real context in-app — **register** (`register_vendor_credential`)
> and **toggle** (`vendor_status_toggle`). The F009 "register/toggle inert in
> production" caveat is retired by F010.
>
> **Reachability / distribution posture (deliberate MVP).** `/g/:id/vendors` is a
> top-level, **chrome-less deep-link mode** (like `/host`), and by design it has
> **no in-app tap target yet** — no screen navigates to it. The intended MVP
> distribution is **out-of-band**: the organiser shares the deep link
> (`zuka://g/<id>/vendors`) directly with the vendor, mirroring how the vendor
> `tier_id` is already distributed out-of-band from the organiser today (S037).
> A first-party in-app entry point (an organiser-side "vendor link" affordance,
> or a vendor account-menu item once one exists) is a later, deliberate add —
> not a gap this increment silently left. Registering the route without a
> reachability path is therefore the *chosen* posture here, distinct from the
> `/inbox`·`/gift`·`/scan` reserved-but-unregistered routes in §9.5.

### `vendor_console_context(p_gathering_id uuid) → TABLE(vendor_team_id, terms_template_id, vendor_presence_id, current_status text)` — F010

The console's **self** read: the caller's OWN acting context for one gathering
(acting team · current terms template · presence + status if one exists).
Read-only, STABLE, no funds move, no event emitted. `auth.uid()`-bound and
self-scoped to `_vendor_company_ids_for_caller()` — **own-team-only**, so
cross-vendor leakage is impossible by construction (proven adversarially in
`s034_vendor_console_context.sql`). Grant posture: `REVOKE … FROM PUBLIC, anon`
+ `GRANT authenticated`.

| Property | Contract the client MUST honour |
|---|---|
| **At most one row; zero rows = `null` context, NOT an error** | A caller in no vendor company (or with no acting team for this gathering) gets **zero rows** — `VendorRepository.resolveConsoleContext` maps that to `null` and the console renders the explanatory "set up your team" state, never an error banner. Anon can't EXECUTE (`42501` → the wrapper's generic error). |
| **`vendor_presence_id`/`current_status` are nullable** | A team that has never toggled has no presence row — render the pre-live state; **never fabricate a status**. The toggle controller seeds from these and re-keys by presence id (RF009-N3). |
| **Server picks the acting team** | Deterministic priority (presence-bearing > credential-bearing > terms-accepted) is a SERVER rule — the client never chooses among companies or sends a team id to this RPC. |
| **Refresh on return** | Context is a point-in-time read; re-resolve on console (re)entry rather than caching across visits — presence/status can change from another device. |

### `list_open_vendor_tiers(p_gathering_id uuid) → TABLE(tier_id, tier_name, vendor_category_id, price_ugx bigint, capacity int, tickets_sold int, slots_remaining int)` — S037/S040

The vendor's **discovery** read: a gathering's OPEN vendor tiers for the calling
company. Read-only, no funds move, no event emitted. Grant posture verified live:
`authenticated`=t, `anon`=f.

| Property | Contract the client MUST honour |
|---|---|
| **Zero rows = clean empty state, NOT an error** | A **non-vendor** authenticated caller (not a member of any vendor company) gets **zero rows**, not a raise (S037). Render the neutral empty ("No open vendor tiers…"), never an error banner. Anon can't EXECUTE (`42501` → the wrapper's generic error). |
| **"Open" is pre-filtered server-side** | The RPC already applies the engine's availability predicate — `is_active` · capacity slack · **`price_ugx > 0`** (S040 discovery/registerable parity — a zero/negative-price vendor tier is *never* listed, so the console never shows a "Free" vendor tier) · gathering status ∈ `PUBLISHED/ACTIVE/LIVE`. Every tier here is one `register_vendor_credential` could actually acquire. |
| **`slots_remaining` is a real count or NULL** | `NULL` when capacity is unlimited — render nothing, **never a fabricated number** (same discipline as `gathering_detail`'s `remaining`). |
| **Bound** | Naturally bounded — one gathering, tiers capped at 8 (Vol 14 Ch2). No artificial `LIMIT`, so **no extra ceiling to design around** beyond the 8-tier cap already in §5. |

### `register_vendor_credential(p_gathering_id uuid, p_tier_id uuid, p_vendor_team_id uuid, p_terms_template_id uuid, p_menu_template_id uuid DEFAULT NULL) → TABLE(ticket_id, ticket_ref, amount_ugx bigint, tx_ref, vendor_team_id)` — S023/S026, **booth arg S075/ADR-035**

Mints a **PAYMENT_PENDING** vendor credential (it charges through the purchase
engine) and returns the amount owed + `tx_ref`. **F009 reports the pending
credential only — it does NOT build the MoMo/Flutterwave settlement** (a separate
money-gated session). **Param order matches the LIVE signature** (gid, tid, team,
terms, **booth**) — named params, so order is documentary, but pinned in
`VendorRepository`.

| Property | Contract the client MUST honour |
|---|---|
| **`p_menu_template_id` is the booth-first binding — the client MUST pass it once the vendor owns booths** | The optional 5th arg links the credential to a categorized `menu_template` (booth). When supplied, the server enforces **booth category == tier category** at issue time, rejecting a mismatch **`23514`** *before* any capacity is reserved (an uncategorized booth can never bind a paid credential). Passing it is what closes the fee-underpayment path (ADR-035 §2): the console picks the **booth first**, and the booth's category drives which tier it may attach to. Omitting it (the `DEFAULT NULL`) skips the guard and leaves the underpayment path open — so a booth-owning vendor's console **must** send its selected `menu_template_id`, never `null`. `p_menu_template_id` is also stamped onto the issued ticket (`ticket.vendor_menu_template_id`, owner-only). |
| **NOT idempotent beyond the one-credential unique guard** | A repeat for a team that already holds an active credential raises **`23505`** (`unique_violation`). The console treats `23505` as a **SOFT "already registered"** state (not a red error), mirroring `claim_free_ticket`'s `ZK409`. |
| **Commit-once (client-side, load-bearing)** | Because it creates a real pending ticket and is not blind-idempotent, the client fires it **exactly once** per gathering: an in-flight or terminal-success registration is never resubmitted (`VendorRegisterController` guard, mutation-teeth pinned). **No blanket retry** (ADR-021). |
| **Documented hard-fails → vendor copy (keyed on CODE)** | `23505` → soft "already registered" · `23514` (`check_violation`: **fee not cleared** · **terms not current/accepted** · **tier is not a vendor tier** · terms/organiser mismatch · **booth category ≠ tier category** (S075 — the selected booth doesn't match the tier's category) — all share this code, so the copy names fee + terms, and for the booth case *"That booth doesn't match this tier's category — pick the matching tier or booth."*) → *"…settle any outstanding platform fee and accept the current vendor terms…"* · `no_data_found` (P0002/02000: tier/team/template/**booth** gone) → *"That tier isn't available anymore."* · `42501` (no session / no Person / not a member / **booth not owned by your company**) → *"Your account isn't set up…"* · network/timeout → the register **MAY** have landed → *"…check before registering again."*, no auto-retry (a landed re-tap returns `23505` → soft success). |
| **Money settles VENDOR-DIRECT** | The pending credential's `amount_ugx` settles via Flutterwave to the vendor's own rail (Route A prepaid platform fee); ZUKA is **not** an aggregator (NPS Act 2020). The console renders the amount owed, never moves or holds funds. |

### `vendor_status_toggle(p_vendor_presence_id uuid, p_command text) → TABLE(vendor_presence_id, old_status, new_status)` — S023

The vendor **live-status console**. Requires a `vendor_presence` row (created at
gate check-in); a team that registered but hasn't checked in has **no presence
row** → the toggle is hidden behind explanatory copy, never a raw
`no_data_found`. Emits exactly one `VendorStatusChanged` (ADR-015, append-only).

| Property | Contract the client MUST honour |
|---|---|
| **Command vocabulary is the wire contract** | Send one of **`OPEN` / `PAUSE` / `SOLD_OUT` / `DONE`** (→ status `LIVE` / `PAUSED` / `SOLD_OUT` / `CLOSED`). Never send a lifecycle status directly. Modelled as `VendorStatusCommand` whose `.wire` is the exact string. |
| **`DONE`/CLOSED is TERMINAL** | A CLOSED presence cannot be re-toggled (`23514` server-side). The client guards it too — a closed listing shows no command buttons (`availableCommands` returns empty). |
| **Commit-once** | An in-flight toggle is never re-fired (`VendorToggleController` guard, mutation-teeth pinned); disable-on-submit across all command buttons while one is in flight. No auto-retry (ADR-021). |
| **Hard-fails → copy** | `no_data_found` (P0002/02000) → *"This listing isn't active…"* · `23514` (CLOSED terminal / unknown command — the console never sends the latter) → *"This listing is closed…"* · `42501` (not a member) → *"You're not allowed…"* · network/timeout → *"…check your connection and try again."* |
| **No cache** | Not in `kNoCacheRpcs` (that list is admission-verification only, ADR-021), but a status change is a live write — the console reads its result directly, never a cached prior status. |

### `save_menu_template(p_vendor_company_id uuid, p_name text, p_items jsonb, p_vendor_category_id uuid DEFAULT NULL) → TABLE(template_id uuid, version int)` — S022, **category arg S075/ADR-035**

Authors (or re-versions) a durable vendor **booth** — a named `menu_template`
lineage the vendor owns and later imports into a live gathering. Supersede-by-
version: each save creates a new active version of the `(company, name)` lineage
and retires the prior one. Emits `MenuTemplateSaved` (ADR-015). Grant posture:
`REVOKE … FROM PUBLIC, anon` + `GRANT authenticated`.

| Property | Contract the client MUST honour |
|---|---|
| **`p_vendor_category_id` types the booth (nullable; inherited on re-save)** | The optional 4th arg binds the lineage to an **active** platform `vendor_category`. On a **re-save** of an existing name, omitting it (the `DEFAULT NULL`) **inherits the current version's category** — a re-version never silently un-types a booth. Supply it to set the category on a **new** lineage (or to change it while still editable). An **uncategorized** booth (`null` category, never set) cannot bind a paid credential downstream (`register` guard) — the console should prompt the vendor to pick a category before the booth is registrable. A supplied inactive/nonexistent category is rejected **`23514`**. |
| **Category-lock after first paid use** | Once the lineage has issued a paid (non-terminal) vendor credential, its category is **immutable** — a `save` that changes the category raises **`23514`** (*"…create a new booth for a different category"*). Freely editable (category included) before first paid use. The console surfaces this as "this booth is in use — start a new booth for a different category," not a raw error. |
| **Plan-cap on new lineages (free = 3 active booths)** | A brand-new named lineage on a `free`-plan company is rejected **`23514`** once the company already has 3 active templates (*"…at its free plan cap of 3 active templates…"*). A **re-version** of an existing name is never capped (active count unchanged). The console shows the cap state and points to deactivating a booth or upgrading (real paid plans are sub-project E, not built yet). |
| **Hard-fails → copy** | `23514` (`check_violation`: empty name / non-array or empty items / negative price / inactive category / **plan cap** / **category locked**) → the specific message above · `42501` (no session / no Person / not a member of the company) → *"Your account isn't set up…"* · network/timeout → the save **MAY** have landed (a new version) → re-read the lineage before retrying rather than blind-resubmitting. |

### `push_menu_template_to_live(p_vendor_team_id uuid, p_gathering_id uuid) → uuid` — S075/ADR-035 (O1)

Re-imports a booth's **current active version** into an already-imported
`(team, gathering)` snapshot — the "push my latest menu to this live event"
action. Rides `import_menu_template`'s supersede-by-replace machinery and emits
`MenuTemplateImported`. Requires a prior import (the first import is
`import_menu_template`'s job; this only refreshes a live listing). Returns the new
snapshot id. Grant posture: `REVOKE … FROM PUBLIC, anon` + `GRANT authenticated`.

| Property | Contract the client MUST honour |
|---|---|
| **Refresh-only — a prior import must exist** | No imported menu for the `(team, gathering)` → `no_data_found` (P0002/02000). The console shows this only where an import already happened; render *"Import a menu to this event before pushing updates,"* never a raw error. |
| **Pushes the CURRENT active version** | It always re-imports whatever is the booth lineage's active version *now* — the client passes only team + gathering, never a version. A lineage with no active version → `no_data_found`. |
| **Commit-once** | It replaces the live snapshot; fire it once per push (disable-on-submit), no auto-retry — a re-push simply re-imports the same active version idempotently in effect, but the client should not blind-retry on network/timeout (`42501` for non-membership → *"You're not allowed…"*). |

## 9. The vendor booths-manager RPCs + reads (F035 · A1, §17)

The Vendor Booths Manager (`/vendor/booths`, `lib/features/vendor/vendor_booths_*`
+ `booths_list_screen.dart` / `booth_editor_screen.dart` / `vendor_booth_widgets.dart`)
is the durable home for a vendor's **booths** (category-typed `menu_template`
lineages, ADR-035). It reuses `save_menu_template` / `push_menu_template_to_live`
(§8) and adds the reads/writes below. **`VendorBoothsRepository` is deliberately
separate from `VendorRepository`** (the F009/F032 event-hub/console code). All
RPCs go through `ZukaRpcClient`; the three direct table reads are done in the
repository (never a widget/provider), mapping errors to `ZukaRpcException` exactly
like an RPC so offline/error states key off `isNetwork`.

> **No param-less "my vendor companies" RPC exists**, and `vendor_company`'s own
> SELECT is REVOKEd. The client enumerates its company id(s) from the **RLS-scoped
> `vendor_team` SELECT** (`GRANT SELECT authenticated`, policy
> `USING (vendor_company_id IN _vendor_company_ids_for_caller())`), then calls
> `get_vendor_company(id)` per id. This works even for a **zero-booth** vendor —
> the A1 entry-gate audience (a credential-holder who hasn't built a booth yet).

### `my_vendor_booths() → TABLE(template_id uuid, vendor_company_id uuid, name text, vendor_category_id uuid, category_name text, version int, used_at_count int, category_locked boolean, live_gathering_refs uuid[])` — S077

The booths list read (bounds/semantics in §5). Read-only, STABLE, no funds move,
no event. `auth.uid()`-bound, own-companies-only. Grant: `REVOKE … FROM PUBLIC,
anon, authenticated, service_role` + `GRANT authenticated`.

| Property | Contract the client MUST honour |
|---|---|
| **Lineage-derived signals survive a re-version** | `used_at_count` / `category_locked` / `live_gathering_refs` are counted across the WHOLE `(company, name)` lineage, not the single active row — a re-save never resets them. Render `used_at_count` only when `> 0`; `category_name` may be `null` (uncategorized). |
| **`live_gathering_refs` is the ONLY coral source here** | Non-empty ⟺ the booth backs a stall that is live right now — the BoothCard spine goes `liveCoral` and the push-to-live row appears. A booth is durable data; coral appears nowhere else on this surface (a category/cap/lock is never coral). |
| **Empty = honest first-time, not an error** | Zero active booths → zero rows → the "Set up your first booth" state. Anon can't EXECUTE. |

### `deactivate_menu_template(p_template_id uuid) → void` — S077

The delete affordance — a **soft deactivate** (`menu_template` is versioned+
deactivated, never hard-deleted; FK RESTRICT). Emits `MenuTemplateDeactivated`
only on a real state change (idempotent). `auth.uid()`-bound, member-guarded.
Grant: `REVOKE … FROM PUBLIC, anon, authenticated, service_role` + `GRANT
authenticated`.

| Property | Contract the client MUST honour |
|---|---|
| **Two guards, both `23514` — distinguished by the booth's `category_locked`** | A **paid-used** lineage (`category_locked = true`, the guard checked first) → *"this booth stays for its credential; you can't remove it (only its category is locked)."* A lineage **attached to an upcoming/live gathering** (`category_locked = false`) → *"remove it from that event's listing before you delete the booth."* Both surface as SPECIFIC copy, never a generic toast and never a raw server string — the client picks the message off the booth's own `category_locked` signal (no raw message parsing). |
| **Other codes** | `42501` (not a member) · `no_data_found` (already gone → refresh) · network/timeout → the delete MAY have landed → re-check, never auto-retry. |
| **No reactivate / no Undo** | There is no reactivate RPC — a removed booth cannot be un-removed in-app (the design's "Undo toast" is deferred until a reactivate exists); the confirm dialog is the human gate (ADR-014). Its category-lineage history is kept. |

### `vendor_team` / `vendor_category` / `vendor_menu_item` — direct RLS-scoped table reads

| Read | Shape / bound | Contract |
|---|---|---|
| `vendor_team.select('vendor_company_id')` | the caller's own team rows (RLS-scoped) | The company-id source for the entry gate (see the note above). Distinct the ids client-side; usually one at MVP. |
| `vendor_category.select('id,name,sort_order').eq('active',true)` | active platform categories, `sort_order` then `name` | The CategorySelector options. A shared lookup (`GRANT SELECT authenticated`, no per-caller RLS). Production category **names are free-text with no canonical kind** — the curated Body schema (Food/Merch/Service) is resolved by a **name heuristic** with a **generic fallback**; a future `category_kind` column would make it exact. |
| `vendor_menu_item.select(...).eq('menu_template_id', id)` | one booth's items, `sort_order` | Editor prefill (no read RPC exposes items). RLS scopes to the caller's own company templates. Maps to the `save_menu_template` items shape: `name` · `price_ugx` · `category` (Food section) · `available` · `attributes.{note,variant,duration_min}`. |
