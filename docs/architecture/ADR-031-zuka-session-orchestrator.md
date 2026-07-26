# ADR-031 — `zuka` session orchestrator (dev tooling for the session chain)

```
Status:   ACCEPTED  (founder greenlit 2026-07-14 — Phase 1 committed, Phase 2
                     authorised. Drafted the same day in the founder-directed
                     orchestrator brainstorm.)
Date:     2026-07-14
Session:  founder-directed (not a numbered S/F/NDS session)
Deciders: Kiyingi James (founder)
Context:  docs/sessions/ session-chain workflow · CLAUDE.md §3 (dev-tooling
          adoption requires an ADR) · ADR-014 (systems flag, humans act)
Stack rule: any new dependency OR dev-tooling adoption requires a formal ADR
            (CLAUDE.md §3 — gstack/Superpowers/agency-agents/Ruflo all did / ADR-024)
```

## Context

ZUKA development runs as an auditable **session chain** across three parallel,
independently-numbered tracks — `S###` (backend), `F###` (flutter), `NDS###`
(founder-directed) — each with a run-next pointer (`NEXT.md` / `FLUTTER_NEXT.md` /
`NEXT_DIRECTED.md`), an append-only ledger (`_INDEX.md`), an archive (`archive/`),
and an independent review series (`RS###`/`RF###`/`RNDS###` → `session-reviews/`).

Today every step is **manual**: the founder copy-pastes a pointer into a fresh
Claude Code session running in a tmux pane (session `zuka-app`; windows
`0:supabase`, `1:flutter`, `2:brainstorming`), runs a separate review session by
hand, applies findings by hand, and finalizes by hand (archive + `_INDEX` row +
pointer refresh + ff-merge). This causes four recurring, documented pains:

1. **Copy-paste toil** + manual tmux pane lifecycle.
2. **ID collisions / lost ideas** — a draft pre-claims an id (e.g. `F007`) that a
   later session also grabs (the F004 trap: an unfinalized draft holds an id
   `NEXT` later hands out); brainstorm ideas have no durable inbox and get lost.
3. **Drift** — sessions wander off the one-task scope.
4. **No S↔F coordination** — the flutter track cannot cleanly tell the backend
   track "build this RPC first" (the create-form-gaps / `GatheringTierCreated`
   situation).

Adopting a tool to automate this is a **dev-tooling adoption** — the same class
as gstack, Superpowers, agency-agents, and Ruflo, each of which was recorded as
an ADR (Ruflo's retirement is ADR-024). CLAUDE.md §3 therefore requires this ADR
before the orchestrator is adopted.

## Decision drivers

- **Preserve the human gate.** The founder confirmation gate + the finalize gate
  (`_TEMPLATE §5`) exist because they caught real defects — S013-F0 was a
  CRITICAL a founder review surfaced, and S013/S016 both stalled at
  `PLANNED`/uncommitted. Automation must not remove that gate; ADR-014 posture is
  "systems flag, humans act."
- **Preserve review independence.** The review is a deliberately fresh, adversarial
  pass over the builder's work; it must not become the builder grading itself.
- **No new stack / dependency.** Africa-first minimalism and the locked stack: the
  orchestrator must use only what is already on the box.
- **File-based truth.** The session state already lives in `docs/sessions/` files.
  The orchestrator must move that state, never become a competing source of truth
  — it stays stateless-restartable by re-reading the files.
- **Reversible.** Adoption must be undoable by deleting the tool.

## Decision (PROPOSED — pending founder)

Adopt **`zuka`**, a thin, file-based orchestrator over the existing session chain,
implemented as a **Bash + `jq`** CLI in `scripts/zuka/` (plus a new
`docs/sessions/_BACKLOG.md` idea inbox). It uses only tools already present:
`tmux`, `git`, `jq`, and the `claude` CLI. **No new dependency, no stack change.**

It is delivered and adopted in **three phases**, each usable on its own:

- **Phase 1 — plumbing (no model autonomy):** the idea backlog with **stable
  slugs** (a permanent kebab identity) and **ID minting only at promotion** from
  the true high-water mark (max across `_INDEX` + `archive/` + live worktree
  drafts, never reused) — the concrete fix for the F004 collision trap; tmux
  session layout (`zuka up`/`down`/`attach`); and an advisory cross-track
  dependency board (`needs:`/`unblocks:`).
- **Phase 2 — the gated loop (headless + human gate):** builder/reviewer run as
  **headless `claude -p` jobs** (real exit codes + a finalize-gate sentinel; panes
  are the dashboard, never screen-scraped). Build → **independent** review →
  remediation-in-the-original-track → finalize. A **10-minute countdown** gate
  auto-proceeds on normal sessions but is **disabled** for high-blast-radius
  sessions (`task_domain ∈ {payment_rails, trust_os, smart_bucket_qr,
  schema_change}`), which require an explicit confirm.
- **Phase 3 — feeders:** brainstorm output and un-applied review carries
  auto-append to the backlog; `zuka next` uses the dependency board to suggest
  ordering across tracks.

### Governing rulings baked into the tool (founder-set 2026-07-14)

| Ruling | Value |
|---|---|
| Autonomy | Semi-auto; the founder gate is **kept**, with a 10-min auto-proceed |
| Gate exemption | `payment_rails`/`trust_os`/`smart_bucket_qr`/`schema_change` **never** auto-proceed |
| Run mechanism | Headless `claude -p` jobs; tmux is the dashboard, not scraped |
| Review loop | **Independent** review → separate remediation job (no self-review) |
| Cross-track deps | **Advisory** (reorder/suggest), never a hard block |

## Consequences

- The copy-paste toil, ID collisions, lost ideas, and manual pane lifecycle are
  eliminated at Phase 1 with near-zero blast radius (pure file/tmux manipulation).
- All existing invariants are **mechanized, not changed**: worktree isolation,
  explicit-path staging, append-only `_INDEX`, forward-only, no-AI-trailer commits,
  and the founder + finalize gates all remain exactly as `_TEMPLATE`/`CLAUDE.md`
  define them.
- The orchestrator is **not** a source of truth: if it is deleted, the workflow
  reverts to the current manual flow with no data loss (the files are unchanged).
- Phase 2 introduces headless model runs; their cost/latency and the WSL2 memory
  ceiling (a known constraint) are operational concerns to watch, not blockers —
  Phase 2 is only built after Phase 1 is in use.
- **Not in scope of this ADR:** any change to what a session *does*; any Bible/ADR
  amendment; a fully-autonomous auto-apply loop (rejected — the gate is kept); a
  builder-reviews-itself shortcut (rejected — independence is kept); a
  hard-blocking dependency gate (rejected — advisory only).

## Status note

**ACCEPTED 2026-07-14.** The founder greenlit adoption: Phase 1 (`scripts/zuka/`,
`docs/sessions/_BACKLOG.md`, the additive template/README/index wiring) is committed
to `main`, and Phase 2 (the gated headless build→review→remediate loop) is
authorised to be built. Reversal remains cheap: delete `scripts/zuka/` and
`docs/sessions/_BACKLOG.md` and the workflow reverts to the manual flow with no
data loss (the session files are unchanged).

## Amendment — model-assisted planning (2026-07-15)

**Status:** ACCEPTED (founder, brainstorm 2026-07-15). Spec:
`docs/superpowers/specs/2026-07-15-zuka-intelligent-backlog-design.md`.

The orchestrator gains an intelligent backlog layer. The deterministic spine
(`zuka triage` ranking/routing/Cat-1 retire, `zuka go`, `zuka route`,
`zuka digest`) is a pure extension of existing file-based commands — no new
dependency — and needs no separate ADR. Putting a model in the **planning**
layer (`zuka brainstorm [S|F|NDS]`, via `claude -p`) is the Adaptive-Zone move
this amendment records. `zuka brainstorm NDS`
produces its recommendation into the staging file with a founder-approval-required
note appended (§6 gate); it is a human-run command, not an automatic trigger — a
scheduled/auto-fired NDS recommend step is intentionally out of scope for this
amendment. Five binding invariants:

1. **The model writes only `docs/sessions/_ADVISOR.md`** — never `_BACKLOG.md`,
   a pointer, `_INDEX.md`, or a migration. Real-mode runs use `--permission-mode
   plan` (read-only) as defense in depth.
2. **Every id-mint / backlog / pointer mutation is a deterministic command a
   human ran** (`zuka go`, `zuka backlog import/promote`, the `triage` Cat-1
   sweep). No auto-promote; no self-directed NDS build.
3. **Category-1 auto-retire is the only autonomous backlog write**, gated on
   `status=done` AND the cited id showing `DONE` in `_INDEX.md` — mechanically
   certain and git-reversible.
4. **The Category-2 premise gate lives at promotion** and refuses to mint an id
   for a slug already delivered as a `DONE` session (closes the F004/F005
   empty-session trap from the scheduling side; overridable only via
   `ZUKA_FORCE=1`).
5. **Money/trust/NDS keep their existing gates** — the Phase-2 money/trust
   exemption and the NDS §6 founder-approval gate are untouched; the layer adds
   no autonomy near them.

**Reversibility:** disabling `zuka brainstorm` leaves the deterministic spine
fully functional; the amendment adds no dependency.

---

## Amendment (2026-07-16) — spec gate · model policy · hardened loop

Founder-directed brainstorm + implementation, same session. Design record:
`docs/superpowers/specs/2026-07-16-zuka-v2-spec-gate-models-design.md`.
No new dependency (bash + jq + git only) — the ADR's stack constraint holds.

**1 · Spec gate (idea → approved spec → build).** Minted prompts carry
`spec_state: "draft"`; `zuka spec <ID|track>` has a model draft §2/§4/domain into
a STAGING file which zuka validates and splices itself (the advisor invariant —
the model never touches authoritative files; real mode is `--permission-mode plan`).
`zuka approve <ID>` is the human sign-off; `zuka run` refuses unapproved specs
(legacy prompts warn; `ZUKA_FORCE=1` overrides). `zuka auto <track>` chains
promote-top-ranked + spec-draft and STOPS at the human gate.

**2 · Model policy (role + risk + escalate).** Per-role defaults
(`ZUKA_MODEL_ADVISOR=haiku · SPEC=sonnet · BUILD=opus · REVIEW=sonnet`); risky
domains force `ZUKA_MODEL_STRONG=opus` for build/review; remediation and the
re-review run one tier up (haiku→sonnet→opus). Chosen models are recorded in
job state and shown at the gate.

**3 · Hardened loop.** (a) After remediation the suite is RE-EXECUTED and an
independent RE-REVIEW runs; the gate auto-proceeds ONLY on
`verdict=PASS ∧ pgTAP∈{PASS,NONE} ∧ non-risky` — a surviving CHANGES now HOLDs
(previously it auto-merged with only test-shaped findings verified).
(b) Reviewer containment is ENFORCED: post-review worktree changes outside
`docs/session-reviews/R<ID>.md` are reverted and logged. (c) `_finalize` takes a
lock (tick × confirm race), asserts root is on `main`, and commits by explicit
pathspec (a bare `git commit` used to sweep pre-staged founder files).
(d) `cmd_digest` shells out `date -d` only for strict `YYYY-MM-DD` ledger cells.
(e) Dead code removed (`_gate_signal`, `GATE_POLL`).

**4 · Operability.** `zuka doctor` (hard: binaries/runner/templates/ledger
markers → rc1; soft: tmux/DB/docker/supabase-go/branch/ticker) — run in full by
`zuka start`, hard-subset before every `zuka run`. `ZUKA_NOTIFY_CMD` hook fires
on gate-armed/hold/spec-ready/finalized/failed/blocked (best-effort, 10 s cap).

**5 · Tests.** Phase 2 finally has coverage: `test/t_80_phase2.sh` (full loop on
a mock `ZUKA_RUNNER`: finalize, escalation, HOLD matrix, containment, lock,
off-main block) + `test/t_85_spec_model.sh` (spec lifecycle, `zuka auto`,
doctor). Suite: 135 passing.

**Autonomy posture unchanged:** two human checkpoints (spec approve, merge gate);
money/trust exemptions, the NDS founder gate, and the Cat-2 premise gate are
untouched.

## Amendment (2026-07-19) — compound-domain gate exemption fix

`_is_risky` matched the WHOLE domain string against `RISKY_DOMAINS`, so any
compound domain ("schema_change / storage", "payment_rails + ticketing") silently
lost both the never-auto-proceed gate exemption and the STRONG-model forcing.
Caught auditing S057 (`risky=false` despite schema_change; no harm — its gate
HELD anyway on an UNKNOWN verdict, and a state-file survey shows no other
compound domain ever ran). Fixed: `_is_risky` now tokenizes and flags if ANY
token is risky; regression case §4b in `test/t_80_phase2.sh` (suite: 186 passing).
Autonomy posture unchanged — this only widens what HOLDs.
