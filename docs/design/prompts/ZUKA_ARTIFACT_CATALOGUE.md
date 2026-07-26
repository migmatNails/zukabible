# ZUKA Artifact Catalogue

> The prioritized inventory of every design artifact (MVP + near-term backlog
> only — reality before vision; dormant Bible capabilities are out of scope).
> To run a session: pick a row, copy `ZUKA_DESIGN_BRIEF_TEMPLATE.md`, pre-fill
> it from the row, pair with `ZUKA_DESIGN_CONSTITUTION.md`.
>
> Status here mirrors the living doc's Status Index; on conflict the living doc wins.
> Update a row's Status when its session's spec section lands.

Classes: page · card · clip-video · clip-social · clip-listrow · receipt ·
form · template-message · template-poster · template-scaffold ·
template-document

## Phase 0 — Foundation (always first)

| ID | Artifact | Class | Prior art | Status | Proof tie | Money/trust |
|---|---|---|---|---|---|---|
| A-00 | Visual Foundation Sheet — token gallery, card anatomy, universal state set, motion-tier demos; **ratifies the PROPOSED spacing scale** (founder/Vol 25 amendment path, never silently locked) | page (HTML sheet) | Design Foundations + docs/flutter/ZUKA_FLUTTER_DESIGN_TOKENS.md | REVIEWED (2026-07-18 — Variant A "Ember Edge" approved; spacing ratified → Vol 25 Ch5; image budgets still PROPOSED) | none | no |

## Priority 1 — the money path (specified, unbuilt)

| ID | Artifact | Class | Prior art | Status | Proof tie | Money/trust |
|---|---|---|---|---|---|---|
| A-01 | Purchase & Pay flow (MoMo hosted checkout) | page | §7 (REVIEWED) + §7.9 visual pass | REVIEWED (2026-07-18 — approved; build stays blocked on §7.7) | 3-tap-ticket | **yes** |
| A-02 | My Ticket + QR card + PendingMeter | page + card | §8 (REVIEWED) + §8.9 visual pass | REVIEWED (2026-07-18 — approved; meter motion ratified) | 3-tap-ticket | **yes** |
| A-03 | My Tickets list | page | §8.2/§8.3 + §8.10 visual pass | REVIEWED (2026-07-18 — approved; coral-wins badge rule ratified) | none | yes |
| A-04 | Purchase receipt — in-app view | receipt | §12 | REVIEWED (2026-07-18 — approved; masking law ratified) | none | **yes** |
| A-05 | Purchase receipt — WhatsApp-shareable image | clip-social + receipt | §12 + A-04 sheet | REVIEWED (2026-07-18 — approved; story amount default-off) | none | **yes** |
| A-06 | Purchase receipt — document export (PDF) | template-document | §12 + A-04 sheet | REVIEWED (2026-07-18 — approved; light-print exception ratified) | none | **yes** |
| A-07 | Scanner check-in (M0) | page | §10 (REVIEWED) + §10.12 visual pass | REVIEWED (2026-07-18 — approved; verdict full-bleed exception ratified; build blocked on ADR-030 + deactivate RPC) | none | no |

## Priority 2 — near-term backlog on built surfaces

| ID | Artifact | Class | Prior art | Status | Proof tie | Money/trust |
|---|---|---|---|---|---|---|
| A-08 | Go-Live form evolution — poster upload, description, richer validation (**behind** go-live; the 60s path gains zero required fields) | form | §5 (BUILT baseline) + §5 visual pass (A-08/09) | REVIEWED (2026-07-18 — approved) | 60s-go-live | no |
| A-09 | Organiser poster templates (pick-a-layout) | template-poster | §5 + assets/brand/ + A-08 sheet | REVIEWED (2026-07-18 — approved; all 3 templates ship; no-coral-on-posters ratified) | 60s-go-live | no |
| A-10 | Nearby / Map | page | §2 (DRAFTED; F013/S051 groundwork) + §2 visual pass | REVIEWED (2026-07-18 — approved; visual truth for F013) | zero-login-discovery | no |
| A-11 | Shelves (Saved · Following) routing + polish | page | §4 (REVIEWED, built unrouted) + §4.13 visual pass | REVIEWED (2026-07-18 — approved) | none | no |
| A-12 | AccountSheet polish | page (sheet) | §5D law + §4.13 (A-11 sheet) | REVIEWED (2026-07-18 — approved; DPPA-rights stub seam to build track) | none | no |

## Priority 3 — clips & templates

| ID | Artifact | Class | Prior art | Status | Proof tie | Money/trust |
|---|---|---|---|---|---|---|
| A-13 | Compact list rows (search · notifications · inbox) | clip-listrow | §0 contracts + §13 | REVIEWED (2026-07-18 — approved) | zero-login-discovery | no |
| A-14 | Social snippets — ticket flex · gathering announce · "I'm going" | clip-social | §13 + A-05/A-09 (4 ratified, 2 new) | REVIEWED (2026-07-18 — approved; live-share coral exception ratified) | none | no |
| A-15 | Message templates — SMS · push · WhatsApp (ticket confirm, reminder, gift) with Vol 25 Ch3 voice + length rules | template-message | Vol 25 Ch3 + §14 | REVIEWED (2026-07-18 — approved; consent-basis law ratified) | none | yes (confirmations name UGX) |
| A-16 | Screen scaffold templates (list · detail · form · empty/offline/error) codified from built screens | template-scaffold | §1/§6/§5 built patterns + §14 | REVIEWED (2026-07-18 — approved; scaffolds + universal law ratified) | none | no |
| A-17 | Video moment preview card (cellular-autoplay-off default state) | clip-video | §0/§1 + §13 | REVIEWED (2026-07-18 — approved; media pipeline = named backend ask) | zero-login-discovery | no |
| A-18 | Ticket-with-QR document (printable) | template-document | §8 + §15 | REVIEWED (2026-07-18 — approved; treat-like-cash law + §12.3 reconciliation ratified) | none | yes |
| A-19 | Vendor statement document (vendor-direct language law) | template-document | §11 + §15 | REVIEWED (2026-07-18 — approved; declaration-leads law ratified; §9B design-ahead) | none | **yes** |
