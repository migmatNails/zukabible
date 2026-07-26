# Brainstorm Decisions — 2026-07-05
### Threads A–G, decided in dialogue with the founder

**Relationship to the other two files in this directory:**
`ZUKA_Constitutional_Compendium.md` is the raw material (10 prior AI reviews).
`BRAINSTORM_2026-07-05_Findings.md` is the 9-agent *audit* pass — verifying
compendium claims against the repo and producing verdicts. **This file is the
actual brainstorm** — genuine decisions made in one-question-at-a-time
dialogue with the founder, going beyond what any audit could settle on its
own. Where the audit said "here's what's true," this file says "here's what
we chose."

**Status:** Working document — not constitutional. Decisions here become real
once implemented as an ADR, a Registry update, a session, or a doc edit —
listed per-thread below.

---

## Thread A — Enforcement mechanics (Launchpad #1, #10)

**Decided: hybrid — cheap automation for checkable things, ritual for
judgment calls.** Concretely:

- **Automated (git pre-commit hook — new infra, none exists today):**
  1. NOTICE-only test-sink check (closes the S010→S017 debt class).
  2. Untested-privileged-RPC check (every `SECURITY DEFINER` function must
     appear in a `supabase/tests/*.sql` file).
  3. Dormant-collision check (a migration creating a name that matches an
     Appendix J Dormant Capability Registry entry fails unless the file
     header cites the graduating ADR).
- **Ritual (extends the S019 front-matter pattern, no new infra):**
  4. New front-matter field `touches_dormant_capability: none|<registry-name>`.
  5. Non-`none` → preflight must assert the capability's current state before
     work starts (mirrors S019 §1's `# expect: does not exist`).
  6. Non-`none` → `zuka-reality-checker` becomes a mandatory gate.
  7. Single waiver path: only an explicit founder message crosses the
     boundary; no session self-authorises.

**Becomes:** the body of candidate **ADR-027**.

---

## Thread B — The premature-abstraction line (Launchpad #3, #9)

**Decided: a layered 3-question checklist**, applied in order to any future
architecture proposal:

1. **DRL/CRL floor** — does the readiness inequality already permit this
   capability? No → stop, it's dormant, not a candidate.
2. **Rule-of-Three + zero-cost seam** — do 3 concrete real instances of the
   need exist? No → reserve a zero-cost seam only (column/enum/naming
   convention), don't build. Yes → build-eligible.
3. **Opportunity-cost** — is this a better use of *this* session than the
   critical path (Flutter screens, venue onboarding)? No → backlog. Yes →
   build now.

The kill-criteria table for the 4 already-named dormant engines
(Opportunity/Memory/Digital-Twin/Intelligence — see Findings §3) doesn't need
to run through this checklist again; it's already decided. This checklist is
for *future* proposals nobody has evaluated yet.

**Becomes:** folded into ADR-027 and/or `docs/sessions/_TEMPLATE.md` as a
standing pre-proposal checklist.

---

## Thread C — Money domain (Launchpad #2, #8)

**Resolved, not brainstormed — the question dissolved.** "Bloom" is an
unrelated project of the founder's; its invariants have no bearing on ZUKA's
wallet design (confirmed directly with the founder, 2026-07-05). No transfer
question remains. The underlying wallet-ledger question was already settled
by the audit (Findings §4: no double-entry ledger; reserve `entry_group_id`
if ever needed) and there was no live creative tension left to brainstorm.

**Becomes:** nothing new. Findings §4 stands as-is.

---

## Thread D — Feature sequencing / Daily Value Loop (Launchpad #4)

**Decided: activate the gift-ticket flow first.** Of two candidate growth
loops already sitting in different readiness states —
the `is_gift` boolean already on `ticket` (schema-ready, unused) vs. the
fully-designed-but-unbuilt `CONCEPT_Poster_Acquisition_Engine.md` (physical
poster referral/attribution) — gift-ticket wins: digital-only, zero venue
dependency, cheapest possible next step since the data model already exists.
Poster Acquisition Engine is not rejected, just sequenced later (naturally
arrives once physical venues are live, per Findings §6's growth-agent read).

**Becomes:** a candidate future session — "activate the gift-ticket growth
loop" (UI flow for gifting a ticket via WhatsApp/SMS + tracking redemption as
an attributed install). Not scheduled yet; a backlog candidate, not a
build-now.

---

## Thread E — Bounded contexts / the "God table" risk (Launchpad #5)

**Decided: a hard rule, effective immediately.** `person` holds *only* what's
true of a human regardless of role — name, contact, auth, locale, consent.
Every role-specific or state-specific fact (vendor prefs, organiser settings,
trust dimensions, driver license, anything role-gated) lives in its own
satellite table keyed by `person_id`. No exceptions, checked at review time.
`trust_profile` is the template — already built this way; the rule just
names the pattern so it's enforced by convention going forward, not by
accident.

**Becomes:** a one-line addition to CLAUDE.md's domain-modeling guidance
(or Bible Vol 04/Vol 02 Domain Model) and a standing review-checklist item for
`zuka-reviewer`: "does this migration add a role/state-specific column to
`person`? If yes, flag it."

---

## Thread F — Solo-founder workflow (Launchpad #6)

**Decided: both fixes, not either/or.** The real bottleneck (confirmed
directly with the founder) is domain-switching cost, not tooling friction or
review-debt tracking.
1. **Batch same-domain sessions before switching** — finish an open chain
   (e.g. S016→S019, all vendor/backend) before starting an unrelated-domain
   session, rather than interleaving by whatever feels urgent that day.
2. **A "parking lot" habit** — when an off-domain idea surfaces mid-session
   (a growth idea during a backend session — this exact compendium
   brainstorm is a live example of the pattern it's meant to prevent), write
   a one-line backlog note instead of acting on it immediately; switch
   domains only at a natural chain boundary.

**Becomes:** a workflow habit, not a doc or code change — worth a short note
in CLAUDE.md's "how to work in this repo" section so future sessions (mine
included) reinforce it rather than re-interrupt a chain.

---

## Thread G — AI-as-participant, minimal real version (Launchpad #7)

**Decided: name one DRL-safe assistive sliver as a future candidate, don't
build it now.** The product-facing "AI flowing through Experience/Context/
Workflow/Trust engines" (ZPE Innovation 4) stays fully shelved — DRL-1
forbids anything that scores, predicts, or decides for a Person. But a pure
suggest-and-edit tool with zero scoring/trust/decision-making — e.g.
AI-drafted gathering-description suggestions an organiser can accept, edit,
or ignore — never touches a Person's standing or a recommendation, and is
arguably DRL-0 safe. Recorded as a candidate, not scheduled.
Separately, and not in tension with this: the founder's own Claude-Code-agent
workflow (this session included) already *is* the real, running instance of
"AI as participant flowing through ZUKA's engines" — just on the build side
(the S00x/R00x chain, the 9-agent dispatch, `zuka-reality-checker` gating
scope), not the product side.

**Becomes:** one line in the Dormant Capability Registry or a concept-doc
stub for "AI-assisted organiser copy" — candidate only, no DRL change, no
build.

---

## Summary — what actually moves next

| Thread | Concrete artifact | Status |
|---|---|---|
| A + B | Candidate **ADR-027** (enforcement mechanics + the 3-question abstraction checklist) | Ready to draft |
| C | Nothing — resolved by clarification | Closed |
| D | Candidate session: activate gift-ticket growth loop | Backlog, not scheduled |
| E | One-line domain-modeling rule + `zuka-reviewer` checklist item | Ready to write |
| F | One-line workflow note in CLAUDE.md | Ready to write |
| G | One-line Registry/concept stub for "AI-assisted organiser copy" | Ready to write, no build |

Everything else from the original 10 launchpad prompts was already answered
in `BRAINSTORM_2026-07-05_Findings.md` and doesn't need further brainstorming.
