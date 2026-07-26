# A-20 Poster Templates · A-21 Tier-Specific Ticket Templates — design source

Output of the A-20/A-21 design round-trip (claude.ai/design, 2026-07-24), from the
brief `docs/design/prompts/ZUKA_DESIGN_BRIEF_A20_A21_resource_templates.md`.
Reviewed and accepted 2026-07-24 (see `docs/session-reviews/A20-A21-template-review.md`).

## Files

- `poster-templates.dc.html` — A-20 gallery (all three styles × states, one page).
- `ticket-templates.dc.html` — A-21 gallery (four tier classes × states, one page).
- `ZukaPoster.dc.html` — the poster component (the real per-style CSS).
- `ZukaTicket.dc.html` — the ticket component (the real per-class CSS — ordinary ·
  vip · table · vendor · skeleton).

## ⚠ Format note — these are design SOURCE, not self-contained mockups

Unlike ZUKA's other `docs/design/mockups/*.html` (plain, self-contained, open in any
browser), these are **`dc-runtime` exports**: they use a `<x-dc>` / `import name=…` /
`{{ }}` templating layer compiled at runtime by a `support.js` shim that **fetches
React from `https://unpkg.com`**. That violates the ZUKA output contract §8 (zero
external hosts), so **`support.js` and `.thumbnail` were intentionally NOT committed.**

Consequence: these files **will not render standalone**. Read them as source — every
dimension, colour, and layout rule is in the inline styles (per the handoff's own
guidance and Constitution §6: read the source, don't render). The design is fully and
independently specified in the living-doc section (`ZUKA_DESIGN_LIVING_DOC.md` §A-20/§A-21),
which is the authoritative deliverable; these files are the visual reference behind it.

**Owed:** a faithful self-contained `.html` re-render (Constitution §8 form) if a
browsable mockup is wanted, and the Flutter build (T2). The Flutter renderer already
built in T1 (`lib/features/templates/template_renderer.dart`) is the implementation target.

## Verified at intake (2026-07-24)

- Zero coral anywhere; only locked Vol 25 token hexes used (`#0E0C0A` `#1A1815`
  `#2A2724` `#6B6863` `#A8A49E` `#F2EFEA` `#F5A623` `#1A3A2A`).
- Every QR is a deterministic, non-scannable SVG placeholder labelled "Open in ZUKA" /
  "Scan to open in ZUKA" — never entry language, `painter: deeplink`.
- Tier classes distinct at a glance; `ADMIT 1` / `ADMITS 8`; table price per-table.
