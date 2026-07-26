# ZUKA — Compendium

Consolidated, cross-cutting AI audits, architecture reviews, and strategic
brainstorms live here — NOT in the Bible. This is where prior review sessions
get reorganized by theme (instead of left scattered by chronology) so a
brainstorm can reason across all of them at once.

## How this differs from the other docs/ directories

```text
docs/fable-analysis/   ONE dated deep-audit series, codebase-grounded
                       (ground truth from migrations — narrow, technical)
docs/concepts/         Single-feature proposals, one CONCEPT_<Name>.md each
docs/design/           The living UX/UI spec (zuka-designer maintains it)
docs/compendium/       Multi-session, cross-cutting strategic material —
                       maturity scores, constitutional audits, strategic
                       reframes, consolidated gap registers, brainstorm
                       launchpads. Broader altitude than a single concept
                       or a single audit; narrower than the Bible.
```

## Status

**Working document — not constitutional itself.** Nothing here amends
ZukaBibleV4 or SOUL.md. It summarizes constitutional-review sessions and
proposes brainstorm angles; anything that survives a brainstorm graduates
the normal way — a concept doc (`docs/concepts/`) or a candidate ADR — before
it can touch the Bible. Subordinate to SOUL.md and ZukaBibleV4 like every
other doc in this repo.

## Index

| Document | Purpose |
| --- | --- |
| [ZUKA_Constitutional_Compendium.md](ZUKA_Constitutional_Compendium.md) | Consolidates 10 prior AI review sessions (product/vision audit, Supabase backend audit, Flutter/interaction architecture, three strategic reframes — ZMOS, ZPE, the Disposable Platform Problem) into one themed reference, ending in a 10-prompt brainstorm launchpad. |
| [BRAINSTORM_2026-07-05_Findings.md](BRAINSTORM_2026-07-05_Findings.md) | Nine-ZUKA-agent parallel pass on the compendium's launchpad — corrects several claimed gaps against the real repo, gives a per-engine overbuilding verdict on all 27 proposed ZMOS/ZPE subsystems, and converges on two concrete next actions (a candidate ADR-027, two doc-hygiene fixes). Parts V/VI/VII of the compendium are frozen as "recorded, not scheduled" pending that ADR. |
| [BRAINSTORM_2026-07-05_Decisions.md](BRAINSTORM_2026-07-05_Decisions.md) | The actual brainstorm — one-question-at-a-time decisions with the founder across Threads A–G (enforcement mechanics, the premature-abstraction line, money domain, feature sequencing, the "God table" rule, solo-founder workflow, AI-as-participant). Distinct from the Findings audit: this file is choices made, not claims verified. |

## Conventions

- One file per compendium (a consolidation pass), named descriptively —
  `<Subject>_Compendium.md`.
- Each compendium states its purpose, its sources, and that it is a working
  document, not a constitutional one.
- A brainstorm run from a compendium's launchpad section either: (a) closes a
  question with a decision recorded in the brainstorm's own notes, (b) spawns
  a concept doc in `docs/concepts/`, or (c) spawns a candidate ADR. It never
  writes directly into the Bible.
- Run findings that touch scope through `zuka-reality-checker` before they
  turn into build sessions.
