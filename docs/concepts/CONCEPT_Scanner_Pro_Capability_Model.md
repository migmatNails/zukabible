# ZUKA — Concept Document
# Scanner Pro: One Capability-Gated Scanner

> **Type:** Feature Concept · **Tier:** 2 (buildable, MVP-adjacent)
> **Status:** Proposed (pre-build) · **Authority:** Subordinate to ZukaBibleV4.
> Reconciles V7's "Scanner Pro" with V4's scanner + scanner-session binding
> (Vol 14) and the Vendor/Gate concepts. Feeds V4 via ADR.
>
> **Implementation reality (Fable Five, 2026-07-02):** the V4 spine this concept
> builds on — the offline manifest, `gate_id`/time binding, JWT session token,
> and staff hierarchy — is **specced in Vol 14 but not yet built**. See
> `docs/fable-analysis/08_scanner_pro_deep_analysis.md` (findings O1-O8): offline
> capability is rated **CRITICAL** and must ship (proposed S012) before this
> capability layer sits on top of it. "Already in V4" below means *canonical in
> the Bible*, not *present in the schema* — read §2 with that distinction.

---

## 1. The Idea in One Paragraph

Every ZUKA account contains the same scanner. What changes is *what that scanner
is allowed to do* at any moment — determined by the person's role, the gathering,
the zone, the time window, and their granted permissions. A vendor's worker
scanning for food pickups, a gate operator checking entry, an usher logging
attendance: one scanner, different capabilities, each bound to its context. No
separate scanner apps, no password sharing, capabilities that expire on their own.

---

## 2. Reconciliation with V4

V4 already has the security spine; V7 adds the capability model on top.

```
ALREADY IN V4 (Vol 14):
  - The scanner session bound to event_id + gate_id + time window (signed JWT)
  - Contextual-blindness: a scanner can't probe outside its session scope
  - Offline manifest, staff hierarchy (organiser→supervisor→gate operator),
    SMS-PIN auth for gate operators without ZUKA accounts

ADDED BY THIS CONCEPT (from V7):
  - The CAPABILITY MODEL: one scanner, layered capabilities granted by context
  - Temporary capability DELEGATION (a team leader hands a worker a scanner
    capability for a time window, auto-expiring) — clean, no password sharing
  - The capability set as a fusion signal feeding Context/Intent (jewel concept)
```

So this is **additive to V4's scanner**, not a replacement. V4 governs the
security binding; this governs what the bound scanner may do.

---

## 3. The Capability Layers (reconciled)

```
LAYER 0 — EVERYONE (any ZUKA account):
  scan a QR · open deep links · basic check-in · receive a delivery

LAYER 1 — ROLE CAPABILITY (granted by the person's role at this gathering):
  vendor · organiser · volunteer · security · usher · teacher · service provider

LAYER 2 — GATHERING CAPABILITY (what this gathering type needs):
  Wedding → gift verification, table allocation, VIP entrance
  Church → attendance, department check-in, children check-in
  Nightclub → age verification, VIP access, drink tokens
  (Gathering-type capability packs — reconcile to GIE in the Dormant Register;
   at MVP these are a simple per-type capability list, not an intelligence engine)

LAYER 3 — TASK/ZONE CAPABILITY (the specific job, here, now):
  "food pickups at Table Zone A, 12PM-5PM" — the tightest scope, time-bound.
```

A scanner's *effective* capability = the intersection of all layers, inside the
V4 session binding. Default-deny: no capability unless granted.

---

## 4. Capability Delegation (the clean V7 idea worth keeping)

```
A team leader (vendor company, Vol vendor-concept) delegates a capability to a
worker:
  leader → assigns "food delivery, Zone A, 12PM-5PM" → worker's scanner
  - No password shared; the worker uses their own ZUKA account.
  - The capability auto-EXPIRES at the window end.
  - Revocable instantly by the leader (and by the unified kill-switch).
  - Every delegation is an append-only event (who granted what, to whom, when).

This maps cleanly onto the Vendor Company/Team model (a company delegates to its
team members) and the staff hierarchy already in Vol 14.
```

---

## 5. Hard Constraints (V4 governs)

```
- The V4 scanner-session binding still wraps everything: a capability is useless
  outside its event/gate/zone/time scope (contextual-blindness, Vol 14).
- Revocation is human/authorised (ADR-014) — capabilities are granted and revoked
  by people, never auto-escalated.
- Least privilege: a scanner receives only the minimum to do its task (V7
  Principle 12 + V4 security, they agree).
- Offline: delegated capabilities work against the cached manifest within their
  window, sync on reconnect (Vol 14 offline model).
- Performance budget: scan→answer ≤ 400ms (Vol 14/16). Capability check is a
  local lookup, not a round trip where avoidable.
```

---

## 6. New Concepts This Introduces (for eventual ADR)

```
ENTITIES (proposed):
  ScannerCapability    (holder_id, layer, capability, scope[event/gate/zone],
                        valid_from, valid_to, granted_by). Default-deny.
  CapabilityDelegation (from_id, to_id, capability, window, revoked_at).
                        Append-only; auto-expires.

EVENTS (append-only, ADR-015):
  CapabilityGranted · CapabilityDelegated · CapabilityExpired · CapabilityRevoked

REUSED: the V4 scanner session + binding + offline manifest (Vol 14), the staff
hierarchy, the unified kill-switch, the Vendor Company/Team model.
```

---

## 7. MVP Cut Lines

```
PHASE 1 — ROLE + SESSION (mostly already V4):
  Role-based capabilities inside the existing V4 scanner session. Gate operators,
  vendor check-in, basic delivery receipt. This is largely Vol 14 + a capability
  lookup.

PHASE 2 — DELEGATION:
  Team-leader → worker time-boxed delegation with auto-expiry. Pairs with the
  Vendor Marketplace's company/team model. (See §10 for the vendor
  multi-gathering refinement + the two schema gaps to close here.)

PHASE 3 — GATHERING CAPABILITY PACKS:
  Per-gathering-type capability sets (wedding/church/nightclub). Reconcile to the
  GIE vision (Dormant Register) — at MVP a static list per type, not an engine.

DORMANT: the full "infinite capabilities" intelligence, cross-gathering capability
  reputation, automated capability suggestion.
```

---

## 8. Open Questions

```
- Capability authoring: who defines the role×gathering capability matrix?
  (Product, encoded as data.)
- Overlap check with Vol 14: confirm this concept only ADDS the capability layer
  and delegation; the binding/offline/hierarchy are already canonical.
- Delegation + DPPA: a delegation records one person granting another a scope —
  minimal PII, but confirm with zuka-legal.
- Delegation depth (see §10): does a vendor deputy only OPERATE (a Layer-3
  capability), or also ADMINISTER their event's team (assign/revoke other
  workers)? Single-level (owner→worker) vs two-level (owner→deputy→worker).
  Recommend operate-only first; two-level is the Phase-2 escalation.
```

---

## 9. PIN-attempt throttle — key blast-radius & the Phase-3 per-gate key

> **Status:** Building (the shared throttle shipped in S013). **Provenance:**
> review **R013** auditing session **S013** (`offline-scanner-security-hardening`),
> 2026-07-04; evidence `20260704100002_pin_hardening_functions.sql:184`,
> `20260704100000_pin_throttle_primitive.sql`. Subordinate to ZukaBibleV4 (Vol 14
> Ch 6/8); a proposal, not law — graduates via `zuka-reality-checker` + ADR.

S013 added a shared PIN-attempt throttle (S010 advisory-lock + windowed-COUNT
pattern) to the three PIN-gated anon RPCs. A throttle "with teeth" must block PIN
*evaluation* during a lockout window (otherwise a lucky correct guess inside the
window still succeeds), so the legitimate operator's correct PIN is denied while
locked. That is inherent — the design variable that matters is the **throttle
key's blast radius**: *when this key locks, who else is denied?*

```
RPC                         throttle key   blast radius when locked        anon-triggerable?
download_scanner_manifest   session_id     one scanner device/session      NO  (session_id = 122-bit UUID, never exposed to anon)
sync_offline_scans          session_id     one scanner device/session      NO  (same)
check_in_ticket             gathering_id   the WHOLE gathering, all gates   YES (gathering_id is public; needs one live ISSUED token)
```

The session-keyed RPCs are correctly scoped. `check_in_ticket` cannot resolve a
session before the PIN, so it keys on the public `gathering_id` — meaning any
holder of one live ISSUED token can lock the whole gathering's **online** check-in
into rolling windows (R013-F1). Severity is capped because **offline manifest
scanning does not call `check_in_ticket`** — a pre-downloaded scanner keeps
admitting offline (the designed Africa-first resilience) — and the lock
auto-clears.

**Design guidance for this capability layer:**
- Prefer the **narrowest key the call can resolve**; state each key's blast radius
  explicitly when adding a throttle.
- For online check-in at MVP: keep the shared throttle but set a **high N** and
  document the brute-force math (the 6-digit PIN sits behind a live 122-bit token).
- **Phase-3 improvement:** when Offline Phase 3 introduces `gate_id`/operator
  binding, re-key the check-in throttle on `(gathering_id, gate_id)` (or the
  resolved operator session) so the door-DoS blast radius collapses to one device.
  The narrower key becomes available exactly when that phase lands — this is the
  throttle's natural Phase-3 follow-up, not a new mechanic.

---

## 10. Vendor multi-gathering delegation — team↔event binding, deputy role, delegation depth

> **Status:** Proposed (refines §4 + §7 Phase 2). **Provenance:** ZUKA
> vendor-scanner design discussion, 2026-07-05 — founder question: *how does a
> vendor admin manage Scanner Pro when the company is vending at 2+ gatherings at
> once?*, proposing per-gathering deputies with limited, event-scoped abilities.
> Grounded in the S011 vendor schema (`20260702120001_vendor_company_team.sql`),
> the organiser-bound `create_scanner_session`
> (`20260630170000_scanner_session.sql:88`), and
> `CONCEPT_In_Event_Vendor_Marketplace.md` §"Why activation is tied to the scanned
> ticket" (the multi-team problem). Subordinate to ZukaBibleV4; a proposal —
> graduates via `zuka-reality-checker` + ADR.

A vendor company vending at several gatherings at once does **not** manage one
scanner across events. The model composes cleanly from pieces that already exist
(or are already specced) — it is §4 delegation applied to the vendor company/team
model:

| Piece | Entity | Role |
|---|---|---|
| Brand / billing / admin | `vendor_company` (`owner_person_id`) | the vendor admin; reusable across all events |
| Per-gathering working unit | `vendor_team` — S011: *"a labelled unit of a company, working a specific event"* | one team per gathering |
| Deputy | a `vendor_team_member` on that event's team | the person the admin puts in charge there |
| "Limited abilities unique to the gathering" | a §4 **capability delegation**, scope `event × zone × time`, auto-expiring, revocable, append-only | the deputy's event-scoped powers |

**Multi-gathering is already clean at the activation layer.** Per the vendor
concept, a brand runs one team per event and each team's listing activates only
when *its own* vendor ticket is scanned at *its own* gathering — "same brand, N
independent activations, zero cross-event leakage." The V4 scanner-session binding
then makes each deputy's scanner **contextually blind** (§5): it cannot act outside
its event/zone/time. "Limited to their gathering" is true by construction, not by
policy.

**The three schema gaps** (designed, not built — close them in the Phase-2 build):

1. **`vendor_team` has no `gathering_id`.** S011's table is `(vendor_company_id,
   label)` — "working a specific event" is a *label*, not an enforced binding. A
   team↔gathering link is needed to scope a team (and its deputy) to one event.
2. **`vendor_team_member` has no role.** It is a flat `(team, person)` join — no way
   to mark a *deputy/lead* vs. a plain worker. This idea needs at least a
   lead/member distinction.
3. **`ScannerCapability` / `CapabilityDelegation` are unbuilt** (§6) — Scanner Pro
   is pre-build; the delegation layer is its Phase 2.

**Delegation depth — the one real decision** (also in §8). "A deputy who manages"
is ambiguous between two very different powers:

- **Operate** — the deputy runs the vending scanner (fulfil/validate orders) at
  their event. A Layer-3 capability only. Single-level: `owner → worker`.
- **Administer** — the deputy *also* assigns/revokes the other workers on that
  event's team. Two-level: `owner → deputy → worker`.

Recommendation: **ship single-level operate-only first**; keep "a deputy can
administer their event's team" as the Phase-2 escalation — two-level delegation adds
a delegation-of-delegation surface `zuka-security` must bound (a deputy must not
out-scope themselves or grant beyond their own window).

**Sequencing & guardrails.** This flow is **vendor-marketplace Phase 2 (ordering) ×
Scanner-Pro Phase 2 (delegation)** — at marketplace Phase 1 (presence + discovery,
no ordering) a deputy has nothing to fulfil beyond the entrance activation, so the
full delegation engine is not MVP. **Cheap now, worth it for accountability:** the
team↔gathering binding + a lead/member role (gaps 1–2). **Deferred:** the
time-boxed capability-delegation entities (gap 3). **Do not** build the "infinite
capabilities" intelligence (§7 Dormant). When built, §5 holds: delegation
grant/revoke is **human** (ADR-014, never auto-escalated), **least-privilege /
default-deny**, every grant/expiry/revoke an **append-only event** (ADR-015), and
`zuka-legal` confirms the DPPA basis for recording one Person delegating another.

---

## 11. The enforcement boundary — where authorization must live (MVP), and the non-destructive oracle

> **Status:** Proposed (hardens §3 + §5 into a buildable MVP boundary).
> **The `authorized_kinds` MVP cut line below is BUILT** — S061, migration
> `20260719160000_scanner_authorized_kinds.sql`, hardened per review RS061
> (`docs/session-reviews/RS061.md`). Rules 6 and 7 and the mid-event note were
> written by that remediation and are statements of shipped behaviour, not
> proposals. The wider capability model (§4 delegation, gate/zone scope) remains a
> proposal awaiting `zuka-reality-checker` + ADR.
> **Provenance:** ZUKA Scanner Pro / Smart Bucket hardening brainstorm, 2026-07-09,
> with a `zuka-security` design audit (designated `smart_bucket_qr` agent) run the
> same session. Evidence: `20260630170000_scanner_session.sql:36-44`,
> `20260705110001_fn_check_in_ticket_vendor_branch.sql:139-145, 187-209, 299-302`,
> `20260703100000_qr_token_rotation.sql`, S013 throttle
> (`20260704100000_pin_throttle_primitive.sql`). Subordinate to ZukaBibleV4;
> a proposal — graduates via `zuka-reality-checker` + ADR.

The whole "minimum-disclosure, context-isolated scanner" claim rests on ONE
property: a scanner can only ask the bucket the questions its **server-verified
context** authorizes. The *kind* axis of that property was built by S061; the
gate/zone/time axes remain design goals. The diagnosis below is the **pre-S061**
state, kept because it is what the MVP fix was written against:

- `scanner_session` carries **no scope columns** — only `gathering_id` +
  `operator_person_id` + `pin_hash` (+ optional `expires_at`). No `gate_id`, zone,
  time-of-day, or capability/kind scope (`scanner_session.sql:36-44`).
- `check_in_ticket` filters auth on `gathering_id` **only**; `ticket_type` is read
  *after* auth purely to branch behaviour, **never to gate authority**
  (`fn_check_in_ticket_vendor_branch.sql:139-145`). So today any valid gathering PIN
  redeems **any** `ticket_type` at that gathering — standard, VIP, staff, vendor
  alike. "A food-vendor scanner can't inspect a VIP pass" is currently vacuous.

**MVP fix — the boundary in one place.** Add a **server-set scope** to
`scanner_session` (`authorized_kinds text[]` at MVP; `gate_id` / zone / time-window
later) and make the resolver filter the credential lookup **in the WHERE clause**,
default-deny:

```
... WHERE t.gathering_id = <session.gathering_id>
      AND t.ticket_type = ANY(<session.authorized_kinds>)   -- server-set, never a client arg
```

Authorization keys on the **server-verified session row**, never on a client-passed
"which credential-kind am I asking about" argument, and never in the APK. If the
kind is a trusted function argument, a modified APK enumerates by iterating it and
the boundary collapses. (Identity comes from the session, not `current_user` —
known-trap #2; the resolver's REVOKE-EXECUTE hygiene is runtime-verified, see
[[project_supabase_auto_grant]].)

**The non-destructive oracle — the trap a read-style resolver introduces.** Today's
scanner is enumeration-resistant *because the scan is destructive*: it burns the
token (`qr_token = NULL`) and is guarded by the CHECKED_IN status check. A
minimum-disclosure resolver that answers *"is this the credential I need?"* **before**
redeeming is a **pollable oracle** — a modified APK can ask the kind of every token
it holds without consuming anything. Any read resolver MUST therefore:

1. **Throttle per session** on the S013 primitive — or it is a free enumeration
   surface.
2. **Return one bit, never the taxonomy** — *admit-here / not-for-you*. An
   out-of-context kind returns an **indistinguishable "NO,"** never "wrong kind."
3. **Kind-scope in the WHERE clause** off the server-set session scope (above).

**Minimum-disclosure nit (LOW, cheap fix).** The gate scanner is currently handed
the vendor's **exact outstanding UGX balance** (`fn_check_in_ticket_vendor_branch.sql:299-302`).
The gate needs the *fact* of a lock, not the *amount* — return a boolean/lock-reason
to the scanner and surface the figure only on the vendor's authenticated console
(the `VendorPresenceBlocked` event may retain it server-side).

**The `authorized_kinds` scope model (MVP shape).** At MVP the "kinds" a session may
resolve are a subset of `ticket_type` ∈ {standard, group, table, vip, streaming,
comp, staff, vendor}. Concrete rules:

1. **`NULL` = unscoped (all kinds); a non-null array = a restricted subset.**
   Backward-compatible (every existing session is NULL → behaves as today). A
   general gate uses NULL/all; a *specialized* scanner is explicit — food-pickup =
   `{vendor}`, VIP desk = `{vip}`, staff entrance = `{staff}`. That is where "a
   food-vendor scanner can't redeem a VIP pass" becomes true by construction. **MVP
   posture:** NULL=all at the main gate is an accepted pragmatic trade; any
   *delegated* sub-scanner (§4) MUST carry an explicit array, never NULL —
   default-deny where isolation actually matters. **RATIFIED (founder, 2026-07-19,
   S061 spec approval):** *"NULL = unscoped at the main gate; any delegated
   sub-scanner (§4) must carry an explicit non-null array."* The backlog's pending
   "NULL=all vs strict" decision is therefore CLOSED — NULL=all is the settled MVP
   posture. Note what is and is not enforced: the *validity* of a non-null array is
   enforced (column CHECK + create-time rejection), but the §4 rule that a delegated
   sub-scanner MUST be non-null is a **convention, enforced nowhere** — there is no
   delegation surface yet to enforce it in. It becomes an enforceable precondition
   when §4 delegation ships, and must be built as one then.
2. **Validate at creation; reject unknown kinds.** A typo'd `{'VIP'}` must fail
   loudly at `create_scanner_session`, not silently match nothing at the gate.
   Fail-closed is only safe if misconfiguration is loud.
3. **Enforce by WHERE-fold, never a post-fetch "wrong kind" raise** — wrong-kind ≡
   not-found, identical outcome and latency (the one-bit rule above).
4. **The manifest MUST be kind-scoped too.** Offline, the scanner matches the
   downloaded hash-set *without* the DB kind check; if a `{vendor}` scanner's
   manifest carries all ticket hashes, the scope is bypassed the moment the network
   drops (the Kampala norm). `generate_scanner_manifest` / `download_scanner_manifest`
   must filter `ticket_hashes` by the session's `authorized_kinds`. The scope model
   is incomplete — and misleading — without this.
5. **Immutable + audited.** Set once at creation (a change = deactivate + new
   session, append-only spirit); carry `authorized_kinds` in the
   `ScannerSessionCreated` event payload (emitted at `event_version = 2` from S061 —
   the payload shape changed, so the version moves with it).
6. **PIN uniqueness within a gathering is a PRECONDITION of the whole scope model**
   (RS061-F2, added 2026-07-19). `check_in_ticket` resolves whichever *active*
   session on the gathering matches the presented PIN. Once sessions carry
   *different* authority, a PIN reused across an unscoped main-gate session and a
   `{vendor}`-scoped one means the unscoped row answers and the kind boundary is
   silently void — no error, and nothing in the event stream distinguishes it.
   No constraint can express this (bcrypt salts defeat
   `UNIQUE(gathering_id, pin_hash)`), so `create_scanner_session` crypt-compares
   the new PIN against every active session on the gathering and rejects a
   collision (`check_violation`), under the gathering row's `FOR UPDATE` so
   concurrent creates serialise. **Residual:** sessions created before S061 were
   never collision-checked; a gathering with pre-existing active sessions should
   start scoping from a fresh session.
7. **Out-of-scope ≠ wrong PIN for telemetry purposes** (RS061-F1). The kind is
   evaluated *after* the session resolves on PIN alone, not folded into the
   resolution `WHERE` clause. Folding it in was the natural reading of "enforce by
   WHERE-fold" (rule 3) but it routed out-of-scope scans — which carry a *correct*
   PIN — through the wrong-PIN branch, consuming the gathering-wide S013 throttle:
   routine mis-scans polluted the only brute-force PIN telemetry there is, burned
   the lockout budget, and after exhaustion made a correct PIN raise `ZK423`,
   re-coupling the correct-PIN path that S013 deliberately decoupled. Rule 3 still
   holds for the *response* (one bit, no "wrong kind" raise); it must not be read
   as a requirement to share the wrong-PIN *accounting*. The residual is that the
   two deny paths are no longer latency-identical — a local timing oracle could
   separate them, disclosing only "this PIN is valid for some session here", which
   whoever holds the scanner already knows.

**A mis-scoped session cannot be fixed mid-event** (RS061-F7). `authorized_kinds` is
immutable after create and nothing deactivates a scanner session — `is_active` and
`expires_at` exist but no RPC toggles them (tracked as the
`scanner-session-deactivate-rpc` backlog item). S061 does not worsen this and
correctly did not invent a revocation surface (ADR-014: revocation is human), but it
**raises the cost**: pre-scope, a wrongly-created session was merely redundant;
post-scope, a wrongly-*scoped* one either refuses valid tickets at a gate or admits
invalid ones, and the organiser's only remedy is to create another session and hope
operators switch PINs. That gap is now higher-priority than when it was logged.

Deferred (don't build now): `gate_id` / zone / time-window (need multi-gate infra
that doesn't exist yet). Generalization: when the bucket holds order tokens, scope
becomes `(item_kind, sub_type)` — YAGNI at MVP; `text[] ⊆ ticket_type` is the shape.

**MVP cut line:** build the `scanner_session.authorized_kinds` column + the
WHERE-predicate default-deny resolver (oracle-aware from line one) + the kind-scoped
manifest. Footprint: 1 nullable column · 1 param/validation/event on
`create_scanner_session` · kind predicate in `check_in_ticket` · kind filter in
manifest generation. Reuses the S013 throttle, S012 state-change rotation, and the
runtime-verified EXECUTE hygiene. No new intelligence, no dormant capability — this
is the foundation every later layer (delegation §4, rotation, risk) sits on.

---

> **Scanner Pro: One Capability-Gated Scanner — Concept (Tier 2)**
> One scanner, capabilities granted by context, delegated cleanly, expiring on
> their own. Additive to V4's secure scanner — the "what may this scan do?" layer.