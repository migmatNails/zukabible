# ZUKA Design Brief — A-20 Poster Templates · A-21 Tier-Specific Ticket Templates

> **How to use:** paste this ENTIRE file as the first message of a fresh
> claude.ai design chat. It is deliberately self-contained — it carries the
> brand law, the data contract, and the output contract, so the design tool
> needs no repo access. Pair-read nothing else.
>
> Authority: ADR-037 (ACCEPTED 2026-07-24) · Vol 25 Brand & Identity ·
> `ZUKA_DESIGN_CONSTITUTION.md` §2/§4/§6/§7 · A-08/A-09 (poster prior art,
> REVIEWED 2026-07-18) · A-18 (printable-ticket prior art).
> Spec: `docs/superpowers/specs/2026-07-23-resource-templates-design.md`

---

## 0 · Your role and the one-paragraph context

You are ZUKA's design partner. **ZUKA is Human Participation Infrastructure** —
it begins as an events discovery + ticketing product for **Kampala, Uganda**, and
grows into community and trust coordination across Africa. Solo founder. Tagline:
*"The city just came alive."*

You are designing the **visual template prototypes** for ZUKA's on-platform
display-style system. A "template" here is a **ZUKA-authored visual design** that
the app renders **on-device** (native widgets from a small JSON spec — never a
server-baked image), filling in live data about a Gathering. Templates are how a
Gathering *looks* at specific moments in the product.

**Two artifact families in this session:**
- **A-20 — Poster templates** (3 named ZUKA styles; evolves A-09)
- **A-21 — Tier-specific ticket templates** (ordinary · VIP · table · other)

---

## 1 · Non-negotiable brand law (apply; never propose against)

### 1a · Colour — "Colour as Information" (LOCKED)

| Token | Hex | Means | Hard rule |
|---|---|---|---|
| liveCoral | `#FF4D4D` | LIVE right now | **Reserved.** Never decorative, never "featured", never success. |
| emberGold | `#F5A623` | Starting soon · primary action · energy rising | Not a caution colour. |
| kampalaForest | `#1A3A2A` | Tonight · scheduled · calm-but-coming | Not a success colour. |
| midnight | `#0E0C0A` | Primary background — "the city at 2am" | — |
| ink0 | `#0E0C0A` | base background | — |
| ink1 | `#1A1815` | raised surface / cards | — |
| ink2 | `#2A2724` | borders | — |
| ink3 | `#6B6863` | secondary text | — |
| ink4 | `#A8A49E` | tertiary / disabled | — |
| ink5 | `#F2EFEA` | primary text | — |
| success | `#3A9D5C` | confirmations | NOT kampalaForest. |
| warning | `#E08A2B` | cautions | NOT emberGold. |
| error | `#D64545` | failures | NOT liveCoral. |
| info | `#4A90B8` | neutral system messages | — |

> **⛔ THE CORAL LAW — the single most important rule in this brief.**
> **No coral on ANY poster or ticket template.** A poster advertises the
> *future*; coral means live *now* and only the feed's LIVE pill owns it.
> Ratified 2026-07-18 (A-09). A coral accent anywhere in your output is an
> automatic fail. Do not use it for VIP, for emphasis, for "premium", for a
> tear-line, or for a price.

### 1b · Typography — the Type Trio (LOCKED roles)

**Space Grotesk** = display/headlines · **Inter** = body/UI workhorse ·
**Space Mono** = data/timestamps/codes, sparingly.

HTML fallback stacks: `'Space Grotesk','Arial Narrow',sans-serif` ·
`Inter,system-ui,sans-serif` · `'Space Mono',monospace`

| Style | Family | Size/Line | Weight |
|---|---|---|---|
| display | Space Grotesk | 32/38 | 700 |
| h1 | Space Grotesk | 26/32 | 600 |
| h2 | Space Grotesk | 21/28 | 600 |
| h3 | Inter | 18/24 | 600 |
| body | Inter | 16/24 | 400 |
| bodyStrong | Inter | 16/24 | 600 |
| caption | Inter | 14/20 | 400 |
| micro | Inter | 12/16 | 500 |
| data | Space Mono | 14/20 | 400 |

**Money and codes use Space Mono (`data`) with tabular figures** — prices must
not shift width as digits change.

### 1c · Spacing (LOCKED, 8px grid)

`xs 4 · sm 8 · md 16 · lg 24 · xl 32 · xxl 48`

### 1d · Motion

Templates are **static surfaces**. No animation on a poster or ticket template.
Money surfaces never animate (instant). If you show a state transition, it is
instant. Everything must collapse gracefully under reduce-motion.

### 1e · Terminology (use exactly)

**Gathering** never "Event" · **Person** never "User" · trust only ever as a
**state label** ("Trusted Member"), never a number · money is **UGX, integer
minor units**, rendered `UGX 25,000` (no decimals, no cents).

---

## 2 · Africa-first constraints (these are design constraints, not caveats)

- Target device: **2GB-RAM Android, 360×800 logical px, on 3G.**
- Rendered **on-device from a JSON spec** into native Flutter widgets — so favour
  **flat fills, solid colours, type, and simple geometry**. Gradients should be
  simple linear ones at most. **No heavy skeuomorphism, no photo-realistic paper
  texture, no drop-shadow stacks, no blur-heavy glassmorphism** — they cost
  frames and can't be expressed as a portable spec.
- The hero photo slot is **optional** and budgeted at **≤80KB**. Every template
  **must look complete and intentional with NO photo at all.** Design the
  photo-less state first; the photo is an enhancement.
- Offline is first-class: a template must render fully from cached data.

---

## 3 · The artifacts

### A-20 · Poster templates — three named ZUKA styles

**Class:** `template-poster` · **Purpose:** an organiser with zero design skill
gets a poster that looks intentional, auto-filled from what they already told us.

Evolve the three founder-approved A-09 styles into full template specs:

| Style name | Palette anchor | Intended feeling |
|---|---|---|
| `midnight` | midnight `#0E0C0A` + ink5 text + emberGold accent | the city at 2am — default, most versatile |
| `ember_gold` | emberGold `#F5A623` ground + midnight text | energy rising, loud, for big nights |
| `kampala_forest` | kampalaForest `#1A3A2A` + ink5 text | calm-but-coming, scheduled, grounded |

**Aspect:** render **4:5** (the Detail hero). Show each style **with** and
**without** the optional photo.

**Slots** (mark each `editable` or `locked` in your annotation):
`hero_photo` (image, editable, optional) · `title` (text, editable, max 60) ·
`datetime` (text, locked) · `venue` (text, locked) · `price` (text, locked —
"from UGX n,nnn") · `brandmark` (locked) · `qr` (locked — see §4).

### A-21 · Tier-specific ticket templates — the physical-ticket feel

**Class:** `template-document` · **Purpose:** at the moment a Person taps
**buy**, the Gathering is shown as a **ticket** — a designed artifact that feels
like a real, physical ticket, so the purchase decision feels tangible.

> **This is a PREVIEW, not a credential.** It grants nothing (§4).

**Design FOUR tier CLASSES (not per named tier). Each must be visually distinct
at a glance** — a Person should know which class they are looking at without
reading the label:

| `tier_class` | What it is | Design intent |
|---|---|---|
| `ordinary` | General admission, 1 seat | The baseline. Clean, honest, unfussy. The other three are read *against* this. |
| `vip` | Elevated admission, 1 seat | Elevated **without coral and without gold-plating clichés**. Earn "premium" through restraint, type weight, and structure — not decoration. |
| `table` | A table/booth seating N (`seats_per_unit > 1`) | Must communicate **"this admits N people"** as a first-class element, not a footnote. |
| `other` | Anything else (incl. vendor credential tiers) | A neutral, safe fallback that never looks broken. |

**Classes have VARIANTS, and variants reuse the class template.** An organiser
names a tier freely ("Early Bird", "VVIP", "Bottle Service"); that name is a
**variant** of one of the four classes and inherits that class's template. You are
designing **one template per class**, and it must hold **any variant label** cleanly:

| Class | Common variant names it must carry | Rule |
|---|---|---|
| `ordinary` | General Admission · **Early Bird** · Presale · Advance · Regular · Gate/Door · Student · Group | Early Bird uses the ORDINARY template. |
| `vip` | VIP · **VVIP** · Premium · Platinum · Backstage · Meet & Greet · Pit / Fan Pit · Lounge | VVIP uses the VIP template. |
| `table` | Table · Booth · Section · Bottle Service · Cabana · Box | seats_per_unit > 1. |
| `other` | Vendor credential · Press/Media · Comp/Guest · Crew/Staff · Season pass | neutral fallback. |

So the **tier NAME slot is a free-text variant label** (`tier.name`, editable,
prominent) rendered on the one class template — design it to look right whether it
reads "GA", "Early Bird", or "VVIP". Show at least one variant label per class in
your mockup (e.g. the ordinary template shown once as "General Admission" and once
as "Early Bird" to prove the same template carries both).

**Physical-ticket vocabulary you may use** (expressed cheaply, on-device):
a **stub / tear-line** (dashed or notched divider), a **perforation** motif,
a **serial/code block** in Space Mono, an **admit-count** block, corner
**notches**, a rotated **edge label**. Use these as *structure*, not as texture.

**Aspect:** a landscape stub, roughly **5:2**, fitting inside a 360px-wide
column. Also show how it looks stacked in a list of 3 tiers.

---

## 4 · ⛔ The QR rule — read twice

Every template reserves a **QR slot**, and it is a **deep-link "magnet" only**:

- It is **unique per Gathering but grants NO access whatsoever.**
- Scanned, the best it does is open ZUKA to that Gathering — or, if the scanner
  doesn't have ZUKA, send them to the app store.
- **The real, access-granting ticket QR is NEVER in a template.** It lives only
  in the Smart Bucket, reached by tapping "explore bucket". Same for every
  money-touching artifact.

**Therefore your ticket designs must NOT imply scannable admission.** Do not
label the QR "Scan to enter", "Admit one — scan at door", or anything that reads
as a door credential. Correct framing is discovery/sharing, e.g.
**"Scan to open in ZUKA"**. Render the QR as a **placeholder block** (a simple
deterministic non-scannable pattern or a labelled square) — never a real QR.

---

## 5 · DATA AVAILABLE — no invented data

Show **only** these fields. If a design needs something else, list it under OPEN
QUESTIONS instead of inventing it.

**Gathering:** `title` (text, "Nyege Nyege Warm-Up") · `starts_at` (timestamp →
"Fri 25 Jul · 8:00pm") · `venue_name` (text, "Alliance Française, Kamwokya") ·
`poster_object_path` (optional image reference).

**Tier (`gathering_tier`):** `name` (text, "VIP") · `price_ugx` (**integer minor
units** → `UGX 120,000`) · `seats_per_unit` (integer — 1 for ordinary/VIP; >1 for
a table, e.g. 8) · `capacity` (integer, units available).

**Derived for a poster:** "from UGX n,nnn" = the minimum tier price.

Use **real Kampala** venue and neighbourhood names and **realistic UGX amounts**
(ordinary ~UGX 25,000 · VIP ~UGX 120,000 · table of 8 ~UGX 800,000).

---

## 6 · ⛔ THE TIER-BINDING INVARIANT (new, load-bearing)

**A ticket template is bound to its `tier_class`, and the binding is enforced.**

- A `vip` template **cannot** be selected for an `ordinary` tier. A `table`
  template cannot be selected for a 1-seat tier. And so on.
- Rationale: the tier class is what a Person is *paying for*. A cheap tier
  wearing a VIP-looking ticket is a misrepresentation, and it is the same
  failure class as ZUKA's existing vendor category-lock.

**What this means for your design work:**

1. Each ticket template you produce must **declare exactly one `tier_class`.**
2. The four classes must be **distinguishable at a glance** — that visual
   distinction is what makes the lock meaningful to a Person.
3. Design the **organiser-facing picker state** for this: when choosing a
   template for a tier, **only the matching class is offered.** Show what the
   picker looks like — including the **explanatory state** when a template is
   unavailable because it belongs to another class. Copy must be honest and
   non-punitive (e.g. *"VIP designs are for VIP tiers"*), never a dark pattern
   and never a nag to upsell.

---

## 7 · States required (render ALL of them, on one page)

Per artifact: **default · loading (skeleton) · empty · offline · error · success**

Plus these artifact-specific states:
- **A-20:** with photo · **without photo** (must look complete) · title at max
  length (60 chars) · very long venue name (truncation strategy).
- **A-21:** each of the four `tier_class` designs · a **sold-out** tier ·
  a table tier showing **admits N** · the **picker with a class mismatch
  blocked** (§6.3) · a 3-tier stacked list.

---

## 8 · Deliverables (output contract — follow exactly)

**(a) Self-contained HTML mockups** — one `.html` file per artifact family:
- `poster-templates.html` (A-20) and `ticket-templates.html` (A-21)
- **No external hosts.** Inline CSS only; system/fallback font stacks; all
  imagery as inline SVG or data URIs; render the ZUKA logo as a **labelled
  placeholder box** (real assets live in `assets/brand/`).
- A **360×800 mobile frame** as the reference viewport.
- **All states rendered on one page**, each clearly labelled.
- Annotate every slot as `editable` or `locked`.

**(b) A living-doc spec section** — ready to append to
`docs/design/ZUKA_DESIGN_LIVING_DOC.md`: a Status Index row (PLANNED · DRAFTED ·
REVIEWED · BUILD-READY · BUILT) then flow, components, states, copy, colour
logic, UX rules, backend dependencies.

**(c) A JSON spec sketch per template** — the machine-readable form the app
actually renders, shaped like:

```jsonc
{
  "layout": "poster.4x5",            // or "ticket.stub"
  "palette": "midnight",
  "tier_class": null,                // A-21 only: "ordinary"|"vip"|"table"|"other"
  "slots": [
    {"id":"title","kind":"text","editable":true,"source":"fill|gathering.title","maxLen":60},
    {"id":"price","kind":"text","editable":false,"source":"gathering.tier.price_ugx"},
    {"id":"qr","kind":"qr","editable":false,"locked":true,"painter":"deeplink"}
  ],
  "colorSet": ["midnight","ink5","emberGold"],
  "locked": ["typography","spacing","qr","brandmark"],
  "brandLaw": {"noCoralOnPosters": true}
}
```

**Where the expressive layer is involved, present 2–3 named variants with a
one-line rationale each — never silently lock a choice.**

---

## 9 · Quality bar — self-check before presenting

Merged from the ZUKA Constitution §7 and app-UI critical rules.

**Brand law**
- [ ] **Zero coral** anywhere in any template (§1a).
- [ ] Only locked tokens used; no invented brand colours.
- [ ] Type trio roles respected; money/codes in Space Mono with tabular figures.
- [ ] 8px spacing grid held throughout.
- [ ] Terminology clean: Gathering · Person · trust as state label.
- [ ] Money as `UGX n,nnn`, integer, no decimals.

**The two load-bearing invariants**
- [ ] **No template implies scannable admission**; QR framed as "open in ZUKA" (§4).
- [ ] **Every ticket template declares one `tier_class`**, the four are
      distinguishable at a glance, and the blocked-picker state is designed (§6).

**Craft**
- [ ] Every template looks complete **without a photo**.
- [ ] Contrast ≥ 4.5:1 for body text, ≥ 3:1 for large text/UI glyphs — verified
      in both the light-surface and dark-surface templates independently.
- [ ] Body text ≥ 16px at the 360px frame; nothing below 12px.
- [ ] Any interactive element in the picker is ≥ 44×44pt with ≥ 8px spacing.
- [ ] No horizontal scroll at 320px; sensible at ≥ 600px (no stretched cards).
- [ ] Truncation strategy stated for long titles and venue names.
- [ ] **No emoji used as icons** — inline SVG only, one consistent icon family.
- [ ] No skeuomorphic paper texture, no blur stacks, no shadow stacks (§2).
- [ ] All required states present, including **offline**.
- [ ] No invented data — every field appears in §5.
- [ ] No dark patterns; the class-mismatch copy is honest, not an upsell.

**Sacred proofs (must not regress)**
- [ ] 60-second Go Live: choosing a template is **behind** go-live, never a
      required step to publish a Gathering.
- [ ] Zero-login discovery: a poster renders for a logged-out Person.
- [ ] 3-tap ticket: the ticket preview adds **no extra tap** to the purchase path.

---

## 10 · Open questions — ask before designing if they block layout or copy

1. `tier_class` is **new**. It is not yet a column in the database. Do you want
   it derived (`seats_per_unit > 1 ⇒ table`; `vendor_category_id NOT NULL ⇒
   other`) with only `ordinary` vs `vip` needing an explicit organiser choice —
   or should it be a fully explicit field the organiser sets per tier?
2. For `table` tiers, is the price shown **per table** (UGX 800,000) or **per
   seat** (UGX 100,000 × 8)? This changes the price block's design materially.
3. Should the `other` class cover **vendor credential tiers** with their own
   look, or stay deliberately neutral?
4. A-20 posters currently carry **no QR** in the shipped A-08/A-09 design. Adding
   the deep-link magnet is an evolution — confirm posters should now carry it.

---

*Subordinate to SOUL.md and ZukaBibleV4. Prior art is binding: this session
evolves A-09 and A-18 and marks superseded parts — it never writes a competing
spec. Output returns to the repo for founder review before any build.*
