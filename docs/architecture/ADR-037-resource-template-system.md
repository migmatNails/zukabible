# ADR-037 — Resource Template system: on-platform display styles for every ZUKA resource, with a non-functional deep-link QR and a plan-gate seam

```
Status:   ACCEPTED  (by the founder, 2026-07-24. Acceptance authorizes the direction
                  below: the three new entities, Option-A on-device render, Model-B
                  bounded editing, the SQL-enforced credential split, the non-functional
                  deep-link magnet QR, the plan-cap seam, and the tier-class binding
                  (decision 9, added at acceptance). A build session still requires the
                  zuka-backend DDL and a zuka-security pass on the deep-link-vs-credential
                  QR split (O3). ACCEPTED alone does not open a build session.)
Date:     2026-07-23 (drafted) · 2026-07-24 (accepted; decision 9 + O4 added)
Deciders: Kiyingi James (founder)
Context:  docs/superpowers/specs/2026-07-23-resource-templates-design.md (this ADR's spec)
          · A-08/A-09 poster templates (docs/design/mockups/a-08-go-live-enrichment/,
            REVIEWED/founder-approved 2026-07-18 — subsumed here) · A-18 printable-ticket
            look (docs/design/mockups/a-18-documents/) · Smart Bucket credential
            (docs/superpowers/specs/2026-07-23-smart-bucket-credential-design.md +
            ADR-036) · ADR-030 (on-device template render + QR painter, client-dep pile)
            · ADR-009 (Memory ≠ Media) · ADR-015 (append-only) · Vol 25 Brand & Identity
Stack rule: any schema change / new entity requires a formal ADR (SOUL red line 7 /
          CLAUDE §3). This ADR is that gate for the durable Resource Template entities.
Prior art: menu_template (20260708100000 — versioned, company-owned, template/snapshot
          split; the versioning pattern is reused, the entity is NOT) · ADR-035
          (vendor menu templates — a DATA model, distinct from this VISUAL/display model)
```

## Context

ZUKA already has three unrelated things called "template": vendor **menu** templates
(`menu_template` / ADR-035 — booth *data*), the three hardcoded **poster** styles
(A-08/A-09 — visual, but founder-curated and not user-manageable), and the A-18
**printable ticket** (one fixed paper credential). None is a general, manageable
**visual display-style system**.

The founder's brainstorm (2026-07-23) asked for exactly that: a system where every
ZUKA *resource* is rendered in a ZUKA-designed **display style** at the platform
moment where it belongs — poster-style in the discovery feed, physical-ticket-style
at the buy decision, and so on for every other resource — with the ZUKA team
authoring the catalog, a bring-your-own upload path for marketing resources, and the
whole capability eventually **plan-gated**.

Two facts shaped the design decisively:

1. **These artifacts are for on-platform display, not print/export.** They render
   inside ZUKA at specific moments; there is no off-platform document to protect.
2. **The QR on a display artifact grants nothing.** It is a **deep-link magnet**
   (unique per event) that only funnels a scanner back into ZUKA. The **only**
   access-granting QR is the purchased ticket, held **exclusively in the Smart
   Bucket** (ADR-036) and reached via "explore bucket" — **outside this system**.
   The same holds for all money-touching artifacts.

Fact 2 dissolves the obvious objection ("won't user-designed tickets enable
forgery?"): no template ever renders a credential, so the template system carries no
admission authority at all.

## Decision drivers

- **DRY over duplication.** This is a *visual/display* model; `menu_template` is a
  *data* model. They must not be conflated. The **versioning pattern** of
  `menu_template` (append a version, one active per name) is reused; the entity is new.
- **Africa-first render.** On-device spec interpretation (Option A) keeps feed thumbs
  within the ≤25KB budget and works offline — no server-baked images at MVP.
- **Brand law must be structural.** Vol 25 + the no-coral-on-posters law cannot rely on
  user goodwill; `locked`/`editable` slots in the template spec make off-brand output
  impossible by construction (Model B — bounded editing, never a free canvas).
- **Credential integrity.** The access-granting QR is Smart-Bucket-only. The template
  QR is a *different painter* and is not verifiable as admission.
- **Money posture (NPS).** T1–T3 touch no money. Plan-gating is a **seam** here; real
  paid tiers are a separate sub-project with its own ADR + legal + security, frozen
  until NPS clearance.
- **Three-axis readiness.** Pure structural rendering — no analytics, no trust scoring.
  DRL-safe.
- **60-second Go Live is sacred.** Template selection lives *behind* go-live
  (enrichment); it never enters the create critical path.

## Decision (DRAFT)

1. **New entities — a visual Resource Template model, not an extension of
   `menu_template`.** Three: `resource_type` (lookup of styled surfaces),
   `resource_template` (the versioned catalog entry, ZUKA-authored or user-uploaded),
   `gathering_resource` (a gathering's chosen template + bounded `fill` for a surface).
   Exact DDL is a **zuka-backend** detailed-design call; the spec (§4) fixes the shape.

2. **On-device render (Option A).** A template is a lightweight **JSONB `spec`**
   (layout · slots · approved color set · locked zones · brand-law flags); Flutter
   renders it, filling live gathering data. No server-baked images at MVP. A server-bake
   layer may be added later **only** if an off-platform share/download need is proven —
   it is additive and does not change the data model.

3. **Model B — bounded editing, never a free canvas.** Users pick a ZUKA template and
   edit only `editable` slots (photo, headline, a color from the approved set, toggles).
   `locked`/`editable:false` slots cannot be overridden by any `fill`. Full free-form
   design is rejected as off-brand and Africa-hostile.

4. **The credential split, enforced in SQL.** `resource_type.is_credential_bearing =
   true ⟹ resource_template.authorship = 'zuka'` (CHECK). Credential-bearing surfaces
   (e.g. `ticket_preview`) permit no user upload and reserve a locked QR zone. Marketing
   surfaces (`allows_user_upload = true`: poster, flyer, social card) permit upload.

5. **Every template QR is a non-functional deep-link magnet.**
   `zuka://g/<gathering_id>?r=<resource_type>&n=<nonce>` (plus an https universal-link
   twin), painted by the **`deeplink`** painter, **never** the `credential` painter.
   Resolver: has-ZUKA+upcoming → open gathering; has-ZUKA+over → upcoming-events (MVP) /
   memories (post-MVP); no-ZUKA → app store. The magnet grants nothing and is not
   verifiable as admission. **The credential QR remains Smart-Bucket-only (ADR-036),
   outside this system** — as does every money-touching artifact.

6. **Plan-cap is a seam, not a product.** `resource_template.plan_min` (default free) +
   a `can_use_template(person, template)` check. No tiers, no entitlements, no billing.
   Real paid plans are **sub-project T5** — its own ADR + legal + security, NPS-frozen.

7. **A-08/A-09 posters are subsumed.** The three founder-approved poster styles become
   the first seeded `resource_template` rows of type `poster`; the no-coral-on-posters
   law carries over as a `spec.brandLaw` flag. A-18's paper-ticket look informs the
   `ticket_preview` layout (minus any functional QR).

8. **Phasing.** MVP = **T1** (render foundation + feed poster-style) + **T2**
   (ticket-style purchase preview). Fast-follow = **T3** (deep-link QR magnet). Later =
   **T4** (bounded editor + upload), **T5** (plan tiers), **T6** (more resource types).
   The foundation is laid so T3–T6 are additive (a resource type = a row + a spec).

9. **Tier-class binding, with a class→variant model (added at acceptance, 2026-07-24).**
   A `ticket_preview` template declares exactly one **`tier_class`** —
   `ordinary` | `vip` | `table` | `vendor` — and may only be bound to a `gathering_tier`
   of that class. (The 4th class was `other` at acceptance; **renamed `other` → `vendor`
   per the A-21 round-trip, O4-resolved** — a distinct vendor-credential look. `tier_class`
   is a platform-administered enum column on `gathering_tier`, not organiser-set.) **A VIP template cannot be selected for an ordinary tier**; a `table`
   template cannot be selected for a 1-seat tier. Enforced two ways, mirroring the
   ADR-035 category-lock: the client picker offers only matching-class templates, and
   `set_gathering_resource` **rejects a mismatch server-side** (client filter is
   convenience; the server check is the invariant).

   **Variants inherit their base class's template.** A `tier_class` is a *base class*;
   the many named tiers an organiser creates are **variants** of one class and share its
   template. Industry norm is 3–4 tiers (research 2026-07-24), with named tiers being
   variants: **Early Bird / Presale / GA → `ordinary`** · **VVIP / Premium / Backstage /
   Pit → `vip`** · **Booth / Section / Bottle Service → `table`** · **vendor credential /
   press / comp / crew → `vendor`**. So an Early-Bird tier uses the ordinary template and a VVIP tier
   uses the VIP template — exactly as intended. The lock binds at the *class* level; any
   number of variants freely reuse the one class template.

   Rationale: the tier class is what a Person is paying for — a cheap tier wearing a
   VIP-looking ticket is a misrepresentation, the same failure class the vendor
   category-lock closes. The four classes MUST be distinguishable at a glance, which is
   what makes the lock meaningful.

## Consequences

- **Positive:** one render engine serves every current and future resource; brand law is
  structural; discovery + ticket surfaces gain real designed styles without regressing
  the three MVP proofs; shared artifacts become a growth loop; credential integrity is
  strengthened (templates carry zero admission authority).
- **Negative / cost:** a new client render interpreter + three tables + seed + RLS to
  build and test; the deep-link resolver adds a small surface; the plan-gate seam is
  dead weight until T5 lands (accepted — it is one field + one function).
- **Neutral:** server-bake (Option C) is deferred, not foreclosed.

## OPEN — status

- **O1 · `resource_type` as PG enum vs. lookup table.** Lookup table preferred (carries
  `is_credential_bearing` / `allows_user_upload` flags and extends without a type
  migration), but a **zuka-backend** detailed-design call.
- **O2 · Upload storage + moderation + DPPA hook** for `authorship='user_upload'`
  marketing images — Storage bucket, validation, content-moderation and personal-data
  posture. zuka-backend + zuka-legal detail at T4; not required for the T1+T2 MVP.
- **O4 · How `tier_class` is determined (decision 9).** It is NOT a column today.
  **Recommended resolution (leaning explicit):** since variants are unbounded free-text
  names (you cannot tell "VVIP" from "Early Bird" from a string), make `tier_class` an
  **explicit 4-option field** the organiser sets per tier, **auto-suggested** to keep it
  low-friction: default `table` when `seats_per_unit > 1`, default `other` when
  `vendor_category_id IS NOT NULL`, else default `ordinary` with a one-tap "make VIP"
  toggle. This preserves the 60-second Go-Live (a sensible default is pre-filled) while
  keeping the binding unambiguous. Final call is the **founder's, informed by the A-21
  round-trip** (design brief §10 Q1). Also open: for a `table` tier, is price shown per
  table or per seat (brief §10 Q2)?
  **RESOLVED 2026-07-24 (founder, via the A-21 design round-trip):** `tier_class` is a
  NEW **platform-administered** enum column on `gathering_tier` —
  **`ordinary | vip | table | vendor`** — NOT derived and NOT organiser-set (an organiser
  cannot self-assign VIP; ZUKA administration sets the class, anti-abuse). This
  **supersedes** the "explicit organiser choice w/ auto-suggest" recommendation above and
  **renames the 4th class `other` → `vendor`** (a distinct vendor-credential look, not a
  neutral fallback). **Table price is shown per-table** (brief §10 Q2), with "ADMITS N"
  first-class. Two follow-ups remain before T2 is BUILD-READY: (a) propagate the
  `other → vendor` rename through this ADR's decision 9, the spec, and the plan (no shipped
  T1 code uses `other`, so it is doc-only today); (b) define who administers `tier_class`
  and when in the tier lifecycle (T2 backend + ops). See
  `docs/session-reviews/A20-A21-template-review.md`.
- **O3 · Deep-link nonce derivation** (must be per-event-unique yet grant nothing and
  leak no PII) — zuka-security detail, aligned with the ADR-036 / smart-bucket posture.

## What ACCEPTED authorizes (and what it does not)

ACCEPTED authorizes the direction: the three new entities, Option-A on-device render,
Model-B bounded editing, the SQL-enforced credential split, the non-functional deep-link
magnet QR, and the plan-cap seam. It does **not** by itself open a build session — that
requires the **zuka-backend DDL** and a **zuka-security pass** on the QR painter split
(O3). T5 (real plan tiers) is explicitly **out of scope** and needs its own ADR + legal
+ security under the NPS-frozen money posture.

---

*Subordinate to SOUL.md and ZukaBibleV4. Register in the ADR Registry on ACCEPTED.*
