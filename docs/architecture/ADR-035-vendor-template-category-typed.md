# ADR-035 — Vendor Template: category-typed, multi-template, plan-capped (extend S022 `menu_template`, do not duplicate)

```
Status:   ACCEPTED  (by the founder, 2026-07-21. O1 resolved = explicit push-to-live.
                  Acceptance authorizes the direction below: extend S022 menu_template,
                  category-typing, plan-cap seam, category-lock, issue-time category-match
                  guard, vendor-status grant. A build session still requires the
                  zuka-backend detailed DDL and a zuka-security pass (this touches the
                  money-adjacent credential path, ADR-026); O2 — booth = template vs
                  vendor_team — is settled inside that detailed design. ACCEPTED alone
                  does not open a build session.)
Date:     2026-07-21 (drafted)
Deciders: Kiyingi James (founder)
Context:  docs/superpowers/specs/2026-07-21-vendor-onboarding-multi-template-account-design.md
          (sub-project A) · docs/concepts/CONCEPT_In_Event_Vendor_Marketplace.md
          (vendor entities graduate to Vol 04/14 via ADR, never before) ·
          Vol 14 Ticketing & Commerce · ADR-026 (vendor fee = Zuka B2B revenue,
          settlement-excluded)
Stack rule: any schema change / new entity requires a formal ADR (SOUL red line 7 /
          CLAUDE §3). This ADR is that gate for the durable vendor Template.
Prior art: S022 (menu_template, 20260708100000) · S011 (vendor_company/team,
          20260702120001) · S019/S020 (credential+presence, category, fee)
```

## Context

The vendor UI brainstorm (2026-07-21) specced sub-project **A** — a vendor's durable,
always-editable, **multi-template** account, where a "template" (a "Snacks booth", a
"Makeup booth") drives that booth's listing and dashboard, and where a vendor cannot
underpay by attaching a cheap-category template to an expensive-category tier
(**booth-first binding** + server category-match).

Before writing new schema, we verified the current one. A durable, reusable, versioned,
**company-owned** template concept **already exists** and must not be duplicated:

- **`menu_template`** (`supabase/migrations/20260708100000_vendor_menu_templates.sql:69`)
  — durable, owned by `vendor_company`, versioned, keyed `(vendor_company_id, name)` with
  one active version per name. A company can already own **many** named menus. Children:
  **`vendor_menu_item`** (`:111`) — name · `price_ugx` · category · sort_order · available ·
  `attributes` JSONB.
- **`vendor_event_menu`** (`:145`) — the **ephemeral per-event snapshot**: `import_menu_template()`
  freezes the chosen template into a frozen JSONB `items` array keyed
  `UNIQUE(vendor_team_id, gathering_id)`. The durable template is never mutated by import
  (S022 "plan-gate ruling A").
- Ownership chain: `person → vendor_company → vendor_team → vendor_team_member`
  (`20260702120001`). The **credential** is `ticket(type='vendor')` with
  `vendor_team_id` + `vendor_category_id`; scanning it creates **`vendor_presence`**
  (`20260705110000`). The **vendor tier** is a `gathering_tier` row with a NON-NULL
  `vendor_category_id` (`20260706100000:206`); its price is platform-fixed from
  `vendor_credential_fee(vendor_category_id, trackability_tier)` (`:105`).

**The one real gap:** there is **no link between a `menu_template` and a `vendor_category`**.
Templates are name-keyed only; category lives entirely on the tier/credential side. So today
nothing stops a "Snacks" menu being imported against a "Makeup" credential — which is exactly
the underpayment hole the brainstorm flagged, and exactly what booth-first binding needs closed.

## Decision drivers

- **DRY over duplication.** A parallel "Template" entity would fork the vendor-menu model in
  two. The locked posture is to extend `menu_template`, not shadow it.
- **Booth-first binding needs category-typed templates.** The whole anti-underpayment design
  rests on a template carrying a category the server can match against the tier.
- **Africa-first / always-editable.** The durable library stays editable offline, anytime,
  with no event or payment (spec invariant 7).
- **Money posture (ADR-026).** The credential fee is platform-fixed per category and
  settlement-excluded; nothing here may let a client influence the fee or leak it into
  organiser payout.
- **Three-axis readiness.** No analytics/intelligence rides on this; it is structural data
  capture only. DRL-safe.
- **Plan-cap is a seam, not a product.** The number of templates is capped by a plan field
  that defaults to a free tier; the real paid tiers are sub-project E (its own ADR).

## Decision (DRAFT — pending the two OPEN points)

1. **The durable vendor "Template" IS `menu_template`.** No new parallel entity. The
   brainstorm's "Snacks booth" / "Makeup booth" are distinct named `menu_template` rows on
   one `vendor_company`. The existing many-named-menus-per-company capability already
   delivers "multiple templates for different services."

2. **Category-type the template.** Add a `vendor_category_id` (FK → `vendor_category`,
   nullable until first categorized use) to `menu_template`. This is the single load-bearing
   addition: it lets the client filter templates to the categories an event accepts
   (booth-first pick) and lets the credential-issue path enforce
   `template.vendor_category_id == tier.vendor_category_id` server-side. Exact column,
   backfill posture, and whether it lives on `menu_template` vs a thin typing table is a
   **zuka-backend** detailed-design call.

3. **Category-lock after first paid use.** Once a template has been used to issue a paid
   credential, its `vendor_category_id` is immutable (it bound a fee). A different service =
   a new template. Freely editable before first paid use.

4. **Plan-cap seam.** A `plan` field (on `vendor_company`) → a max number of *active*
   `menu_template`s, defaulting to a free-tier value, enforced server-side on create.
   No tiers, no billing, no entitlement matrix here — that is **sub-project E** (separate
   ADR + legal + security).

5. **Vendor status is durable, auto-granted on first vendor-credential purchase**, and is
   what unlocks the `/vendor` template manager thereafter. (Mechanism — a flag/derived
   check — is a zuka-backend call; the invariant is what this ADR ratifies.)

6. **Generic body across categories.** `vendor_menu_item` (name · price · available ·
   sort_order · `attributes` JSONB) already generalizes to Merch (products) and Service
   (bookables); category-specific fields ride in `attributes`, rendered by a client-side
   per-category schema. No new item tables are ratified here; the curated per-category
   schema is a **client + `attributes`** concern, revisited only if a category proves it
   needs its own columns.

## OPEN — status

O1 is **resolved** (below). O2 is a **zuka-backend detailed-design** detail, not a founder
gate. With O1 settled, this ADR awaits only the founder's formal **ACCEPTED** sign-off plus
the zuka-backend DDL and zuka-security pass.

- **O1 · Live-edit vs snapshot — RESOLVED 2026-07-21 (founder): explicit push-to-live.**
  The **built** model freezes a per-event snapshot at import (`vendor_event_menu`, S022);
  the brainstorm's invariant 10 said edits reflect live. Reconciled as: the durable library
  template is **always editable**; the attendee-facing listing is the **frozen snapshot**;
  the vendor triggers an explicit **"push updates to this live event"** action from the
  dashboard to refresh `vendor_event_menu`. Attendees never see prices shift out from under
  them mid-event; the vendor stays in control. Invariant 10 is hereby **refined** from
  "edits auto-reflect" to "edits reflect on explicit push." (This makes the O1 "push to live"
  action part of the A1 backend + C dashboard scope.)

- **O2 · Is the "booth" the template or the team?** `vendor_team.label` already reads like a
  booth name, and the credential + presence are **per-team**. So a "booth" could be modeled
  as `vendor_team` (+ its category-typed template) rather than the template alone. This
  affects whether "New booth" creates a team, a template, or both. **zuka-backend** to
  resolve during detailed design; flagged here so the ADR isn't read as pre-deciding it.

## Alternatives considered

- **A new standalone `vendor_template` entity** (the spec's original framing) — **rejected**:
  duplicates `menu_template`, forks the import/snapshot machinery S022 already built, and
  creates two sources of truth for "a vendor's reusable menu."
- **Category on `vendor_company`/`vendor_team`** instead of the template — **rejected**: a
  company/team is deliberately NOT bound to one category (the same team vends different
  categories at different events; verified — no `vendor_category_id` on company/team). Typing
  the template is the correct grain.
- **Free-text / builder templates** — rejected in the brainstorm (config is data-driven by
  category, not a builder).
- **Live-linked listing (drop the snapshot)** — rejected under O1: it removes attendee price
  stability and undoes a deliberate S022 decision.

## Consequences

- The A1 backend build shrinks from "a new Template subsystem" to: **category-type
  `menu_template` + the plan-cap seam + the category-lock invariant + the issue-time
  category-match guard + vendor-status grant**, plus the O1 "push to live" action. Much less
  new surface; it rides S022's existing import/snapshot machinery.
- **`register_vendor_credential` / the issue path gains a hard guard:**
  `template.vendor_category_id == tier.vendor_category_id`, rejecting mismatches even from a
  raw client call. pgTAP must prove the reject, the plan-cap reject, the vendor-status grant,
  and that ADR-026 §5 settlement-exclusion still holds.
- **Vendor entities still have not graduated to Vol 04/14 by prose** — accepting this ADR is
  the authorization to do so for the category-typed template; the Bible edit is a build-session
  byproduct, not this document.
- **Sub-project E (paid plans) is unblocked to be designed** but not built: this ADR only
  ratifies the cap *field*, defaulting free.
- The spec (`2026-07-21-...-design.md`) is refined by this ADR on two points (Template =
  extend `menu_template`, not new; invariant 10 → O1). The spec should carry a pointer to
  ADR-035; it is not rewritten.

## Status note

**ACCEPTED** (founder, 2026-07-21; O1 resolved). Not yet a build authorization — a build
session still requires: (1) a **zuka-backend** detailed schema design that turns §2–§6 into
forward-only migration DDL grounded in the S022 tables (and settles O2), and (2) a
**zuka-security** pass on the issue-time guard and the fee/settlement invariants. The
`vendor-template-schema-backend` backlog row is now UNBLOCKED (its ADR gate is met); those
two remain as the build session's own gates.
