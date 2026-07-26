# Brainstorm Findings — 2026-07-05
### Nine-agent pass on `ZUKA_Constitutional_Compendium.md`

**Method:** all 9 ZUKA project subagents (`zuka-reviewer`, `zuka-reality-checker`,
`zuka-backend`, `zuka-tester`, `zuka-security`, `zuka-legal`, `zuka-designer`,
`zuka-growth`, `zuka-researcher`) ran independently, read-only, in parallel
against the compendium's Part IX gap register and Part X brainstorm launchpad.
Each brought its own domain lens. This file consolidates their findings,
flags where they agree, and states the concrete next actions.
**Status:** Working document — not constitutional. Two items below are
candidates for graduation (an ADR and a doc-hygiene fix); nothing here amends
the Bible directly.

---

## 1. The headline: several "gaps" the compendium names are not real gaps

Four independent agents (`zuka-reviewer`, `zuka-backend`, `zuka-tester`,
`zuka-reality-checker`), reading different parts of the repo, converged
unprompted on the same correction: **the compendium was written against its
own self-assessment, not against the actual repo**, and overstates several
gaps.

| Compendium claim | What's actually true | Source |
|---|---|---|
| "No document exists for what is NOT MVP" (Part II gap #1) | **False as stated.** `docs/architecture/books-appendices/ZUKA_Appendix_J_Dormant_Capability_Registry.md` already names ~40 dormant capabilities with current DRL/CRL + graduation preconditions. The Dormant Capability Doctrine (Ch.14), ADR-016, ADR-017, ADR-008 already govern this. The real gap is narrower: no *mechanical* session-level enforcement of that doctrine. | zuka-reviewer |
| "Idempotency not enforced everywhere / rate limiting only on purchase" (Part III gaps #8/#9) | On the money path, both already exist: `confirm_ticket_payment` is idempotent (keyed on `tx_ref`, terminal-state no-op), `initiate_ticket_purchase` is rate-limited (velocity index + advisory lock, S010). Remaining gap (login/OTP/discovery) doesn't bind yet because auth isn't built. | zuka-backend |
| "No complete double-entry wallet ledger" flagged as *the* most significant backend gap (Part III #3) | **Not MVP-blocking, and the wrong shape.** ZUKA custodies no money (ticket money is Flutterwave-direct to organiser, vendor money is vendor-direct, wallet is frozen). `commission_debt_ledger` (S018) is a correct single-account signed-journal for the one thing ZUKA does account for — its own fee receivable. True double-entry only earns its keep once ZUKA holds money on its own books, which is itself a decision the money posture currently forbids. | zuka-backend, zuka-researcher, zuka-security |
| Constitutional Kernel's 9 rules are aspirational prose | 4 of 9 are already enforced today with cited ADRs/migrations; a further 2 are enforceable with modest effort (a `platform_configuration` table + a CI grep-gate); only 1 (simplicity/invisible complexity) is genuinely not test-shaped. | zuka-reviewer, zuka-tester |
| "6 ADRs" total, "ADR-001 through ADR-026" | The ADR catalog (`ZUKA_ADR_Catalog.md`) is **stale** — it lists only ADR-001–020 and says "Total: 20 ADRs," but ADR-021/022/023/026 exist as standalone files not reflected in the catalog. ADR-024/025 are candidate numbers only (referenced by R011/R002), not ratified files. | zuka-reviewer |

**Implication:** before treating any compendium gap as real work, check it
against the repo. Several of Part III/VI's "missing" items are either already
built in minimal form, or already reserved as a seam (see §2).

---

## 2. Overbuilding verdict — the 20 ZPE engines + 7 ZMOS layers

`zuka-reality-checker`'s full per-engine table is the reference; headline:

- **Zero of the 27 proposed subsystems need to be built as a named "engine."**
  Most already exist in minimal, correct form inside existing RPCs/tables
  (`scanner_session` **is** a capability engine; ticket purchase **is** a
  workflow engine; `domain_events` + Outbox **is** the event mesh substrate).
- A handful genuinely defer, each with a concrete trigger (not a roadmap
  date) — see the kill-criteria table in §3.
- A few are outright **do-not-build**: Trust Engine (DRL-1 forbids scoring;
  see §4), Opportunity/Memory/Digital-Twin/Intelligence engines (dormant,
  correctly seeded, no activation trigger fired yet).
- Even the compendium's own "restrained Phase-1 MVP Kernel" (Constitutional
  Kernel, Experience Engine, Context Engine, Workflow Engine, Capability
  Engine, Event Store, Policy Engine) nets to **zero new builds** once checked
  against what already exists — every item in it is either already shipped or
  already the correct dormant state.
- The reality-checker's blunt read: **producing/expanding this compendium is
  itself an instance of the "architecture outpacing implementation" risk the
  compendium names in its own Part II.** The compendium's own Part III
  concludes "stop adding backend, build the Flutter app" — Parts V/VI then
  propose 27 more subsystems and call a ZMOS constitution "the last artifact
  before implementation." That's the tell to watch for in future sessions
  ("grow into," "future-proof," "additive not disruptive").

**Recommendation (converged, not just reality-checker's):** freeze Parts
V/VI/VII as *recorded, not scheduled*. Do not let any ZPE/ZMOS engine name
become a session prompt. The two items worth graduating are launchpad #1 and
#9 — see §5.

---

## 3. Kill criteria for the 4 dormant systems (Launchpad #9)

`zuka-reality-checker` and `zuka-growth` answered this independently and
landed on compatible, cross-validating signals — both tied to things ZUKA can
actually count, not a calendar date:

| Dormant system | Real trigger (either agent's framing agrees) |
|---|---|
| **Opportunity Engine** | Manual matching demand overflowing what the founder can broker by hand: ≥50 repeat organisers (≥3 gatherings each) AND high `VendorPresenceActivated` volume — i.e., a real two-sided pool, not a cold-start guess. |
| **Digital Twin Engine** | Live operational state stops fitting in one scanner's head: multiple concurrent `scanner_session`s per gathering (multi-gate) and peak check-in rate high enough that organisers ask for live capacity/queue visibility mid-event. |
| **Memory Engine** | A real corpus exists: median attendee has ≥3 check-ins over ≥6–12 months. `AttendanceSeedRecord` is already being emitted — the seed is happening; the surface should stay dormant until there's a corpus worth showing. |
| **Intelligence Engine** | Feed impression→click logging exists (it doesn't yet — that's step one, not modelling) AND DRL reaches 2+. Recommending on DRL-1 data is forbidden regardless of demand. |

**Fold these into the Dormant Capability Registry as each capability's
activation trigger** rather than leaving them in this brainstorm doc — that
keeps Appendix J the single source of truth.

---

## 4. Money domain — wallet, Trust Engine, security/legal red flags

- **[CRITICAL — legal-blocking, not just technical]** A double-entry wallet
  ledger with a held balance and withdrawal path would cross the frozen
  NPS-Act-2020 aggregator line (`zuka-security`). Do not build one. If ZUKA
  ever needs to hold money, that's a legal-clearance decision first, an ADR
  second, and only then a schema. The reservable seam, if wanted at zero
  cost: a nullable `entry_group_id` column on the existing ledger + a
  deferred `SUM(amount)=0` constraint added later — nothing to do today
  (`zuka-researcher`).
- **[HIGH]** Trust Engine, as worded in the compendium, does **not** inherently
  violate "trust never a number" — `trust_profile` already computes six
  internal dimensions behind REVOKE+RLS and that's the correct shipped
  posture (`zuka-security`). But the compendium's *language* is loose enough
  to license a violation if built carelessly: the event name
  `TrustScoreUpdated` and "Event Mesh publishes every event to
  notification/analytics" would leak a raw score to a client. Any future
  Trust Engine work must mandate: raw dimensions never cross to a client,
  only the `trust_state` label; the Event Mesh must never publish raw trust
  dimensions to a client-reachable subscriber.
- **[HIGH — legal]** Of the proposals reviewed, the **Trust Engine is the
  first one that would need real DPPA 2019 clearance before build** (not just
  design) — continuously computing a reputation score from
  attendance/payments/reports/fraud/identity is automated profiling under
  DPPA. The AI data model (empty tables) and aggregate Operational
  Intelligence can stay as reserved/dormant seams without triggering that
  requirement yet; Memory Engine is close behind (a behavioural profile) but
  can stay dormant as an unpopulated schema (`zuka-legal`).
- **[HIGH]** Four Security-Volume items are genuinely absent **and**
  MVP-blocking before real MoMo money moves: an incident-response + DPPA
  breach-notice runbook, a key-rotation runbook (Flutterwave HMAC + Supabase
  service_role/JWT), a *tested* backup/DR restore drill (PITR alone isn't
  proof), and minimal fraud/velocity logging on the payment path
  (`zuka-security`). Six other "Security Volume" items are already
  substantively covered (rate limiting partial, secrets/RLS hygiene, session
  management, audit trail via the event store) — don't re-scope those as
  net-new.

---

## 5. What's actually worth graduating from this brainstorm

Two things, both converged on by multiple agents independently:

1. **A candidate ADR — working title "MVP Boundary & Dormant-Capability
   Enforcement" (next free number: ADR-027; 024/025/026 are already
   spoken for/taken).** This is *not* a new kill-list — Appendix J already is
   one. It is the **mechanical enforcement layer** Appendix J lacks:
   - A session front-matter field (`touches_dormant_capability:
     none|<registry-name>`) mirroring the S019 provenance-block pattern.
   - A preflight assertion that a dormant table/RPC "does not exist" before
     work starts (the S019 pattern), and a migration-header citation
     requirement when a name from the Registry is ever created.
   - A CI grep-gate: fail if a new migration/function matches a Registry
     capability name without a cited ADR/Activation-Contract reference in
     the file header.
   - Fold in two more CI gates `zuka-tester` proposed independently, since
     they close the exact debt classes already on record in `_INDEX.md`
     (R005→R010 NOTICE-only history): fail if a test file has a
     `RAISE NOTICE '[FAIL]'` sink without a following `RAISE EXCEPTION`; fail
     if a new `SECURITY DEFINER` function has no matching test in
     `supabase/tests/`.
   - Fold the §3 kill-criteria table in as the activation triggers for
     Opportunity/Memory/DigitalTwin/Intelligence in the Registry.
   - Single waiver path: only a founder message or a ratified Registry
     graduation crosses the boundary; no session self-authorises.
   - `zuka-reality-checker` is named as the mandatory gate for any session
     whose front-matter declares dormant contact.

2. **Two small doc-hygiene fixes**, both factual corrections `zuka-reviewer`
   found, independent of any brainstorm decision:
   - `ZUKA_ADR_Catalog.md` is stale — add rows for ADR-021/022/023/026 and
     mark 024/025 (and this brainstorm's 027) clearly as candidates, not
     ratified.
   - The three-axis readiness inequality (`manifestation_depth ≤
     min(DRL, BRL, CRL)`) is **not** uniformly canonical — the bible-v4 core
     files (`07_Capability_Readiness_CRL.md`, the Master Index, the Canonical
     Language Dictionary, Book VIII) still state the original two-axis form
     (`DRL × CRL`). BRL is documented only in a non-bible-v4 volume
     (`..._Volume_XX_Data_Readiness_Assessment.md:90`) yet is already used as
     canonical in CLAUDE.md and this project's own review template. Needs
     reconciling — either amend bible-v4 to adopt BRL formally, or correct
     the propagated three-axis references.

Everything else in the compendium (ZMOS's 7 layers, ZPE's 20 engines, the
Daily Value Loop, the wallet ledger, the Trust/Memory/Opportunity/Digital-Twin
engines) is **recorded, not scheduled.**

---

## 6. Retention / Daily Value Loop (Part VIII) — sequencing verdict

`zuka-designer` and `zuka-growth` independently agree:

- **None of the 5 Daily-Value categories need a new MVP screen.** The
  existing feed system (`ZUKA_DESIGN_LIVING_DOC.md` §0's polymorphic
  FeedItem × provenance × lens architecture, plus the already-PLANNED §4
  Saved/Following) already reserves every seam needed — new content
  `kind`/provenance values and lenses, not new codepaths. "Today around you"
  **is** the MVP, not a retention bolt-on; "your reputation" already has a
  home (a trust-state badge on Profile — never a raw number).
- **Sharper point from `zuka-growth`:** the Disposable Platform Problem is a
  real diagnosis for a *scale-stage* ticketing platform, and a **false
  priority for a pre-launch, zero-user, 30-venue product**. You cannot have
  a retention problem before you have users; building against it now
  competes with the actual pre-launch job (winning venues). Building
  retention features for an empty network renders blank screens.
- **The one thing worth a cheap seed now:** the gift/referral loop ("your
  network" done as a mechanic, not a feed surface) — it directly serves the
  30-venue acquisition goal (organisers gift/comp tickets to fill early
  rooms; each gift is an install) rather than being a retention feature.
  Flag: any gift/referral SMS/contact touch needs a DPPA consent check with
  `zuka-legal` before it ships.
- Two invariant risks to hold the line on if any of this is ever designed
  further: a personalized "Living Interface" homepage must never become the
  cold *first-open* render (breaks the zero-login discovery proof — keep
  personalization behind signup); any commercial/retention band (offers,
  vendor promos) must inherit the existing anti-drain cap and provenance
  floor, never rank by "what brings you back" (engagement-in-ranking is
  explicitly forbidden at MVP).

---

## 7. Testability — which Kernel rules are provable today (Launchpad #10)

`zuka-tester`'s full 9-row table is the reference. Headline: 4 of 9 rules are
mechanically provable right now with the project's existing hard-fail DO-block
convention (financial correctness, backend authority, explicit state
transitions, traceable-events-for-wired-emitters); 4 need infrastructure that
doesn't exist yet (an audit_log table, a platform_configuration table); 1
("simplicity at the interface") is a design-review judgment, not test-shaped.
The one standalone recommendation worth acting on regardless of the ADR
above: **a CI teeth-gate** that turns the NOTICE-only test anti-pattern (which
took 7 sessions, S010→S017, to retire) and untested privileged RPCs from
"caught by a diligent reviewer" into "cannot merge." Cheap, and it's already
folded into the ADR-027 proposal in §5.

---

## 8. Open item for the founder — "Bloom"

`zuka-researcher` checked: every "Bloom" hit in this repo is *Bloom filter*
(the unrelated offline-scanner data structure, ADR-023/S012). There is **no
evidence in this repo of a sibling project called Bloom** with the invariants
the compendium's launchpad #2 attributes to it (single-currency-per-
transaction, no wallet/held-balance, fail-closed money parameters). Those are
unverified founder-supplied claims inside the compendium, not researched
fact. Before importing "Bloom philosophy" as a constraint on any future ZUKA
wallet design, confirm directly what Bloom is and whether the quoted
invariants are accurate.

---

## 9. Concrete next actions (converged, ranked)

1. **Draft ADR-027** (§5.1) — the enforcement layer, not a new kill-list.
2. **Fix the stale ADR catalog** (§5.2) — five minutes, closes a factual
   inaccuracy the reviewer flagged.
3. **Reconcile the two-axis vs three-axis inequality** (§5.2) — small but
   real constitutional inconsistency between bible-v4 and its downstream
   documents.
4. Resume the **actual session chain** — none of the above blocks it:
   - S019 (vendor credential + scan-time activation) is already written and
     waiting.
   - The genuinely missing critical-path work, per `zuka-reality-checker`:
     the **first Flutter screen**, wired to `discovery_feed`/
     `discovery_feed_nearby` — the three sacred MVP proofs are unproven until
     they run in a real hand on a 2GB Android on 3G, not in `psql`.
   - Two small, already-known, bounded backend sessions: the paid-at-
     provider-but-EXPIRED reconciliation path (R007/R009-F2), and a plain
     ticket-confirmation notification send (not an "orchestrator").
   - Session D (already flagged in S018/S019 hand-off) still owes the
     `settle_commission_debt()` PSP-amount assert (R018-F1).
5. **Ask the founder about "Bloom"** (§8) before launchpad #2/#8 go further.

Everything else raised in the original 10 launchpad prompts is answered above
and does not need a build session right now.
