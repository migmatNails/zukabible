# ZUKA Design Mockups

HTML mockup output from design-prompt sessions (see `../prompts/`).

Conventions:
- One folder per artifact slug, named after its catalogue row,
  e.g. `a-04-purchase-receipt/`.
- Files are self-contained HTML (inline CSS, no external hosts, fallback
  fonts, inline SVG/data-URI images) — viewable offline in any browser.
- One file per variant, e.g. `variant-ledger.html`, `variant-stub.html`;
  the approved variant is renamed `approved.html`.
- All states render on one page (default · loading · empty · offline ·
  error · success + artifact-specific).
- Superseded mockups are deleted, not accumulated — git history keeps them.
- Mockups are visual truth for build sessions, but the spec of record is
  `../ZUKA_DESIGN_LIVING_DOC.md`.
