# ZUKA Design Handoff Pack

> **What this is:** the one-paste package for running a ZUKA design session in
> the claude.ai designer tool (or any Claude with no repo access) to
> **improve, polish, and make responsive** the already-approved designs.
> Updated: 2026-07-18 — catalogue A-00–A-19 fully designed and
> founder-reviewed.
>
> **How to use (3 steps):**
> 1. Paste `ZUKA_DESIGN_CONSTITUTION.md` (the standing brief — it is
>    self-contained: identity, red lines, tokens, motion, responsive rules).
> 2. Paste THIS pack (the current state: ratified laws + artifact index).
> 3. Paste the HTML source of the mockup you want improved (from
>    `docs/design/mockups/<slug>/…`), then give the mandate below.
>
> **The mandate (copy verbatim, fill the blank):**
> "Polish and improve the ____ mockup: keep every law in the Constitution and
> the Ratified Laws Register exactly; improve visual refinement, hierarchy,
> and micro-detail; convert fixed 360px frames to fully responsive layouts
> per the Constitution's responsive rules (320 floor · 360–430 reference ·
> ~480 column cap · ≥600 rules, money flows one-column always); keep the file
> self-contained (inline CSS, no external hosts, fallback fonts, canvas/SVG
> placeholders); keep ALL states rendered; return the complete improved HTML
> file so it can be committed back and re-reviewed."

---

## Ratified Laws Register (founder-approved 2026-07-18 — none negotiable)

**Anatomy & layout**
1. **Ember Edge (Variant A)** is THE card anatomy: 6px status-colour spine on
   the left edge; compact list-row variant = spine + two lines + at most ONE
   state chip, right-aligned fact slot is context-owned.
2. Ratified exceptions to Ember Edge: **scanner verdict cards** are full-bleed
   state-colour (glanceability at a night gate); **documents (A-06/18/19)**
   are light-print (paper is white, receipts/tickets read in daylight).
3. **Five scaffolds** (S1 Feed/List · S2 Detail · S3 Form · S4 Sheet ·
   S5 Verdict/Moment) — screens assemble from these, never invent placement.
   Universal law: ribbon slot at content top; states render in-place (never a
   blank page); ONE primary action, in the thumb zone.
4. **Spacing is Vol 25 law:** 8px grid (xs4 sm8 md16 lg24 xl32 xxl48).
   Image budgets stay PROPOSED (hero ≤80KB · thumb ≤25KB · icon ≤8KB).

**Colour**
5. **Coral (#FF4D4D) = LIVE RIGHT NOW, nothing else.** Applications ratified:
   no coral on posters (they advertise the future) · no coral on scan
   verdicts, tickets states, receipts, buttons, or paper · the ONLY exported
   coral is the live-share snippet family, always timestamped ·
   tab badge priority: coral (live now) beats gold (pending).
6. System colours never substitute brand: success≠forest, warning≠gold,
   error≠coral.

**Money & credentials**
7. Money renders ONLY server-returned values, `UGX 25,000` integer format;
   no wallet UI; vendor money is vendor-direct ("nothing to release — you
   already have it" leads the vendor statement).
8. **Credential masking:** QR and full `ticket_ref` render in-app only;
   every exported receipt/share masks to `ZKA_TCK_••••`; `tx_ref` is the
   public payment identity. Exception (ratified): the printable ticket A-18
   IS the credential — full QR + code + the "treat this like cash / works
   once" line printed on every copy; no person name on it.
9. Money/trust surfaces are static: amounts and buttons never animate; the
   PendingMeter's calm drift and the 400ms QR settle are the ratified
   exceptions (state information, frozen under reduce-motion).

**Motion**
10. T0 structure silent · T1 feedback (quick 120 / standard 200 / route 280 /
    routeBack 220ms) · T2 bloom (700ms) ONLY for liveness. Never-list: no
    stagger cascades; opacity+transform only; motion never gates a sacred
    proof; everything collapses under reduce-motion. Cellular media honesty:
    no autoplay on cellular, data cost shown as the consent ("12 MB");
    low-data map drops tiles and becomes a list.

**Messages & consent**
11. Every off-screen message carries a consent basis — transactional ·
    follow-push · marketing — and a channel earns only what its consent
    covers; SMS ≤160 GSM-7, no emojis; STOP on every marketing SMS; SMS may
    carry the holder's own ticket code to their own number ONLY.

**The sacred proofs (UX budgets, never regressed)**
12. 60-second Go Live (enrichment lives BEHIND go-live, zero new required
    fields) · zero-login discovery (feed, detail, map — no auth) · 3-tap
    ticket (enumerated: Get ticket → approve on Flutterwave → See my ticket).

**Voice**
13. Present-tense, specific, local (real Kampala venues/neighbourhoods),
    warm never hyped, no urgency theater, no guilt copy, empty states give
    direction not mood. "Gathering" in semantic copy; "Person" never "user";
    trust is a state label, never a number.

---

## Artifact Index (all REVIEWED — source file → spec section)

| ID | Artifact | Mockup source (repo) | Spec |
|---|---|---|---|
| A-00 | Visual Foundation Sheet | `mockups/a-00-visual-foundation/foundation-sheet.html` | Design Foundations |
| A-01 | Purchase & Pay (paid rail, 7 frames) | `mockups/a-01-purchase-pay/purchase-pay.html` | §7.9 |
| A-02 | My Ticket + QR + PendingMeter (7 states) | `mockups/a-02-my-ticket-qr/my-ticket.html` | §8.9 |
| A-03 | My Tickets list + tab badges | `mockups/a-03-my-tickets-list/my-tickets-list.html` | §8.10 |
| A-04/05/06 | Receipt trio (in-app · share · PDF) | `mockups/a-04-purchase-receipt/receipt-trio.html` | §12 |
| A-07 | Scanner (6 frames, 5 verdicts) | `mockups/a-07-scanner-checkin/scanner.html` | §10.12 |
| A-08/09 | Go-Live enrichment + 3 poster templates | `mockups/a-08-go-live-enrichment/enrichment-and-posters.html` | §5 visual pass |
| A-10 | Nearby / Map lens (5 frames) | `mockups/a-10-nearby-map/nearby-map.html` | §2 visual pass |
| A-11/12 | Shelves + AccountSheet (8 frames) | `mockups/a-11-shelves-account/shelves-and-account.html` | §4.13 |
| A-13/14/17 | Clips: rows · snippets · video | `mockups/a-13-clips/clips.html` | §13 |
| A-15/16 | Messages (10) + scaffolds (5) | `mockups/a-15-messages-scaffolds/messages-and-scaffolds.html` | §14 |
| A-18/19 | Printable ticket + vendor statement | `mockups/a-18-documents/ticket-and-vendor-docs.html` | §15 |

(All paths relative to `docs/design/`. Specs live in
`docs/design/ZUKA_DESIGN_LIVING_DOC.md` — the spec of record; paste the
relevant § alongside the mockup when polishing, per the brief template's
prior-art rule.)

## Round-trip rule (how improved designs come home)

An improved file from the designer tool is a PROPOSAL until it lands:
1. It returns as one complete self-contained HTML file.
2. A repo session diffs it against the laws (the §7 quality bar + this
   register), commits it to the same `mockups/<slug>/` path (git keeps the
   old version — superseded mockups are deleted, not accumulated), and
   updates the artifact's living-doc § if anything semantic changed.
3. Anything that touches a law is a founder ruling, never a silent change.
