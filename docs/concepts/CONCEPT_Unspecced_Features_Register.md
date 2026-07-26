# ZUKA — Unspecced & Thin Features Register
# The Living Gap Tracker

> **Type:** Living concept register · **Status:** Continuously updated
> **Authority:** Subordinate to ZukaBibleV4. This document captures features that
> exist in ZUKA.md or other source layers but have not yet received a full concept
> document or Bible volume. It is the backlog of things that are KNOWN but not yet
> DESIGNED in any governed document.
>
> **How to use this file:**
> — When a gap is discovered, ADD it to the relevant section below.
> — When a gap is promoted to its own concept doc or Bible volume, mark it GRADUATED
>   with a link and the date. Do not delete it — keep the audit trail.
> — When a gap is deliberately parked as dormant, mark it DORMANT + reason.
> — Run new sessions through `zuka-reality-checker` before promoting any item here
>   into a build.
>
> **Source key:**
>   [Z] = ZUKA.md original brainstorm (the founding product doc)
>   [V7] = V7 Architecture Bible (June 2026)
>   [V4] = ZukaBibleV4 (this project's canonical constitution)
>   [C] = Concept document (in docs/concepts/)
>   [S] = Session/conversation (discovered in development discussion)

---

## How to read the status column

```
UNSPECCED   mentioned in a source but no concept doc or Bible coverage exists
THIN        partially covered but missing key mechanics, UX, or entity model
GRADUATED   promoted to a concept doc or Bible volume — link provided
DORMANT     deliberately parked — activation trigger stated
BLOCKED     cannot proceed until a dependency is resolved
```

---

## Section 1 — Consumer Product Features

### Following System & Social Graph (product mechanics)
```
Status      UNSPECCED
Sources     [Z] §14 — full following system spec
Gap         The Social Graph is central to how Go Live notifications, the City
            Pulse feed, and "friend attending" signals work. V4 has a
            Relationship Graph (Vol 09) but the product-level mechanics are
            completely absent: the follow button UX, follower/following counts
            (or their ethical equivalent), one-way vs mutual follows, privacy
            controls (public/private follows), "followed venue just went live"
            push notification flow, the "friends attending" signal on event cards.
Priority    HIGH — underpins Go Live, City Pulse, and discovery personalisation
Blocked by  Nothing. Can be specced now.
Notes       V4 deliberately avoids "follower count as vanity" (Vol 15) — the
            social graph should feed trust + discovery signals, not a count.
            Resolve how "follows" relate to Relationship OS (Vol 09) and whether
            following a venue creates a Relationship edge.
```

### Lock Screen Widget
```
Status      UNSPECCED
Sources     [Z] §9
Gap         The bucket QR on the phone lock screen — "raise your phone at the
            gate." UX flow, how it's activated, how it refreshes (rotating token
            still applies), how it works offline, Android vs iOS implementation
            differences, accessibility. This is the 3-tap-ticket flow's last step
            and a genuine product differentiator.
Priority    MEDIUM — MVP-relevant, but the gate works without it at launch
Blocked by  Nothing. Android widget API + iOS Lock Screen widget (iOS 16+).
```

### Gift Ticket — Viral Acquisition Mechanics (depth)
```
Status      CONCEPT EXISTS — see docs/concepts/CONCEPT_Gift_Ticket_Growth_Loop.md
            (2026-07-05, from the compendium brainstorm's growth-loop pick).
            Scopes the gap below into a Phase-0 cut + explicit out-of-scope
            list; the open questions (link format, group/table gifting,
            monetisation) remain OPEN there, not resolved — a build session
            still hasn't happened.
Sources     [Z] §11 · [V4] Vol 14 Ch 9
Gap         Vol 14 covers the state machine well. Missing: the landing page
            experience for a non-user receiving a gift (what they see before
            installing), the viral flywheel mechanics (gift → install → the new
            user's first experience is their gift), group/table gifting as
            individual seat-level gifts, the monetisation model on gifting
            (commission on gifted tickets?), the exact WhatsApp/SMS link format
            and what it shows on preview.
Priority    HIGH — the gift ticket is the highest-trust, zero-cost acquisition
            channel; the viral mechanics determine how much acquisition it drives
Blocked by  Nothing. Relates to Poster Acquisition Engine concept (shares the
            "non-user first experience" design challenge) — see the new
            concept doc §5 for how it reuses that pattern.
```

### Audio & Video Streaming — Full Feature Spec
```
Status      CONCEPT EXISTS — see docs/concepts/CONCEPT_Live_Streaming_System.md
            (2026-07-16) + ADR-032 (ACCEPTED 2026-07-16: Mode-4 segment-push HLS)
Sources     [Z] §7 · [V4] Vol 13 Ch 8 · CONCEPT_Live_Streaming_System.md
Gap         Closed by the concept doc: per-mode latency budgets (§3), the
            conversion funnel + its metrics (§3), Mode-1 board-out setup flow
            (§3), Mode-3 sync protocol (§3), listener discovery UX (§4), and
            the Mode-4 spike architecture (§4). Still open there (§6): Mode-2
            vendor choice (ACRCloud/AudD, own ADR), Mode-3 content licensing,
            Option-A (Cloudflare Stream Live) adoption timing, and the blocking
            counsel clearance of §5 (DPPA incl. s.19 + music copyright/UPRS +
            UCC licensing — zuka-legal pre-review already folded in).
Priority    MEDIUM-HIGH — ADR-032 ACCEPTED; Mode-4 spike is buildable the
            moment the legal co-gate clears
Blocked by  Qualified-counsel §5 clearance ONLY (ADR-032 accepted 2026-07-16;
            former blockers Go Live + ticketing are BUILT).
```

### Festival Mode — Full Feature Spec
```
Status      THIN
Sources     [Z] §31 · [V4] Vol 13 Ch 11
Gap         Vol 13 names Festival Mode; ZUKA.md §31 specifies it fully. Missing:
            the multi-stage map UX (how an organiser sets up stages, how an
            attendee navigates them), the schedule builder with clash detection
            (how conflicts surface), artist paging ("Fireboy on stage in 15 min"),
            Lost & Found flow, Emergency Broadcast (organiser → all checked-in
            users, how fast, what the UX is), multi-day pass with per-day tracking.
Priority    MEDIUM — relevant for larger events, not the Kampala small-event MVP
Blocked by  The core ticketing + check-in flow (Phase 1).
Notes       Relates to the GatePlan concept (multi-gate, multi-stage). Consider
            whether Festival Mode is an extension of GatePlan or separate.
```

### City Pulse Score — the Published Metric
```
Status      THIN
Sources     [Z] §29
Gap         ZUKA.md §29 specifies City Pulse Score as a distinct, published metric
            about a city's event health — what it measures (event count, check-ins,
            crowd levels, venue participation, growth trend), how it's computed,
            how it's surfaced (in the app, to press, to city authorities), and how
            it's monetised (tourism boards, government, sponsors). V4 mentions it
            only in passing (Vol 24). No entity model, no update frequency, no
            privacy/aggregation spec.
Priority    LOW-MEDIUM — a growth/PR asset, not a core product mechanic
Blocked by  Enough real events to compute a meaningful score (post-launch).
```

---

## Section 2 — Organiser-Side Features

### Organiser Analytics Dashboard
```
Status      UNSPECCED
Sources     [Z] §33 · [V7] §13 (OIOS post-event)
Gap         Completely absent from V4 and all concept docs. ZUKA.md §33 specifies
            a full dashboard: Standard tier (verified venues — attendance, ticket
            sales, revenue, check-in timeline, crowd peak, no-show rate, vibe
            score) and Pro tier (retention cohorts, revenue forecast, audience
            demographics, heat map, vendor breakdown, comparative benchmarking).
            Missing: entity model for analytics events, what's computed vs raw,
            DPPA implications for demographic analytics, the access control model
            (who on an organiser team sees what).
Priority    HIGH — core retention mechanic for organisers; "organisers who see
            data keep coming back"
Blocked by  Enough events to have data. Spec can be written now; activation
            waits for real events. DPPA review required for demographics.
Notes       Relates to Vol 23 B2B Intelligence — the organiser dashboard is the
            "personal" tier of analytics; B2B is the "platform-wide" tier. Clarify
            the boundary.
```

### Organiser Growth Flywheel & Journey Progression
```
Status      UNSPECCED
Sources     [Z] §21 (implied) · [V7] §21
Gap         V7 §21 specifies a seven-stage organiser progression (Starter →
            Verified → Professional → Elite → Regional → Partner → Ambassador)
            and a first-100-organisers acquisition plan with named target segments
            (nightclub owners, DJs, churches, hotels, comedy promoters). V4 Vol 24
            has the city onboarding model but nothing on the ORGANISER journey —
            how they grow on the platform, what each stage unlocks, what metrics
            advance them, and how ZUKA retains them.
            The flywheel: organiser joins → creates event → PPN/vendors notified →
            users discover → tickets sold → check-ins → settlement → analytics →
            "organiser never leaves."
Priority    HIGH — this is the primary retention + advocacy mechanism
Blocked by  Nothing. This is a product + growth design question, not a technical
            dependency.
Notes       Organiser journey status (Starter/Verified/Pro…) should map to the
            Identity Lifecycle (Vol 08) for the Organiser Persona. Resolve whether
            organiser progression is a separate track or an extension of the same
            Persona trust/CRL system.
```

### Payout System — Full UX & Mechanics
```
Status      THIN
Sources     [Z] §23 · [V4] Vol 14 Ch 13
Gap         Vol 14 states the timing (48-72h) and methods (MoMo/bank). Missing:
            the full payout UX (where does an organiser see their pending payout,
            track its status, see deductions itemised before release), the failure
            path ("failed payouts held in Zuka balance indefinitely until claimed"
            — how does the organiser claim them?), the payout dashboard, the
            "funds never lost" guarantee implementation, partial payouts for
            multi-day events, disputed ticket refunds reducing the payout.
Priority    HIGH — money flows; trust in ZUKA depends on payouts being
            transparent, predictable, and visible
Blocked by  Nothing. Core to MVP financial trust.
```

### Boost / Campaign System — Mechanics
```
Status      THIN
Sources     [Z] §21 · [V4] Vol 15 Ch 5 (boost cap)
Gap         V4 states the boost cap (max 2 positions, can't displace top 3). What
            it doesn't spec: how an organiser creates a boost (self-serve UX, what
            they set — duration, budget, target audience), what "boosted" looks
            like in the feed (the label, the design, the 1-in-10 cap enforcement
            in UI), the reporting an organiser gets on a boost (impressions, clicks,
            tickets sold attributed to boost), the pricing model (flat fee? per-
            click? per-impression?), and the anti-abuse rules (preventing a vendor
            from gaming the queue through paid placement alone).
Priority    MEDIUM — revenue surface, but fairness rules make it sensitive
Blocked by  Discovery feed live (Phase 1). Boost is a Phase 2 revenue surface.
Notes       The Attention Economy fairness rules (Vol 15) constrain this heavily.
            Boost design must be reviewed against the minimum-exposure constraints
            to ensure paid boosts don't starve emerging organisers.
```

### Guest List Import (Scanner Pro)
```
Status      UNSPECCED
Sources     [Z] §10
Gap         Completely absent. An organiser imports an external guest list (CSV or
            manual) into Scanner Pro; the scanner then validates against it at the
            gate. UX for the import (file format, field mapping, conflict
            resolution), how the guest list interacts with the ticket provenance
            system (does an imported guest get a ticket, or is the list a separate
            gate?), privacy (DPPA implications of importing a list of names/emails),
            the real-time status view (who's checked in, who hasn't arrived), and
            what happens when someone on the list arrives without a ticket.
Priority    MEDIUM — important for corporate/private events with curated guest lists
Blocked by  The core scanner flow (Phase 1). This is a Phase 2 enhancement.
Notes       Tension with the closed-system ticket provenance rule (Vol 14 Ch 1) —
            resolve whether a guest-list check-in creates a lightweight attendance
            record or a full ticket entity.
```

---

## Section 3 — Platform & Business Model

### Revenue Model — Full Per-Stage Detail
```
Status      THIN
Sources     [Z] §17 · [V4] Vol 23 Ch 7
Gap         Vol 23 sketches the stages; ZUKA.md §17 has full per-stage detail:
            exact commission percentages per stage (5% MVP, 6.5% Uganda scale),
            all revenue streams with projected contribution percentages, the
            specific triggers that advance between stages. Also completely absent:
            the revenue model for the vendor marketplace (commission structure,
            listing fees, the untrackable-vendor surcharge rates — these are in the
            concepts but not in a single coherent revenue document).
Priority    HIGH — a solo founder needs a single clear revenue document
Blocked by  Nothing. A financial model document alongside the Bible.
Notes       This may warrant a standalone financial model file rather than a
            concept doc — something between a concept and a Bible volume.
```

### Subscription Tier Framework
```
Status      UNSPECCED
Sources     [V7] §20.3 · [Z] §17
Gap         V7 has a four-tier table (Free / Basic / Pro / Enterprise) but it's
            generic. Neither V4 nor any concept doc specifies ZUKA's actual
            subscription tiers: what each tier includes, the UGX price points for
            Uganda, what the upgrade trigger is (what pain makes someone upgrade),
            which features are free forever (free event listing — anti-drain rule),
            which are premium (advanced analytics, AI copilot, multi-event
            management, white-label), and the "subscription improves capability,
            never merit" rule operationalised per tier.
Priority    HIGH — determines revenue from launch day
Blocked by  Nothing. This is a product decision, not a technical dependency.
Notes       The "subscription never buys opportunity" principle (V7 Principle 7,
            also in V4 Vol 23) must be the hard constraint. Draft tiers then run
            through zuka-reality-checker and zuka-legal.
```

### Competitive Landscape
```
Status      UNSPECCED
Sources     [Z] §38
Gap         ZUKA.md §38 has a full competitive map: global competitors (Eventbrite,
            Fever, HotSpot, Dice), local East Africa competitors, and ZUKA's
            defensible advantages per competitor. Completely absent from all
            generated documents. This matters for positioning, for pitch decks,
            for zuka-growth's work, and for identifying which advantages to
            protect architecturally.
Priority    MEDIUM — needed for pitch/investment context and growth strategy
Blocked by  Nothing. A research + positioning document.
Notes       Competitive landscapes go stale; this needs a "last verified" date
            and a refresh cadence. May belong in docs/research/ rather than
            docs/concepts/.
```

### Multi-City & Internationalisation — Operational Model
```
Status      THIN
Sources     [Z] §35 · [V4] Vol 24 Ch 3
Gap         Vol 24 has the city onboarding model at a high level. Missing: the
            technical architecture for multi-city (how events in Nairobi are
            isolated from Kampala in discovery, how cross-city browsing works,
            how city-specific analytics are scoped), the language support roadmap
            (English → +Luganda → +Swahili → +French → +Arabic) with the actual
            implementation model (string externalisation, right-to-left layout for
            Arabic, locale-specific date/currency formatting), and currency handling
            (how Kenyan Shillings work alongside UGX, FX margin model).
Priority    LOW — post-Kampala-launch
Blocked by  A live, working Kampala product. Never spec multi-city before single-
            city is proven.
```

### White-Label / API Products
```
Status      THIN
Sources     [Z] §17 Stage 4 · [V4] Vol 23 Ch 7
Gap         Mentioned as a Stage 4 revenue source. No specification: what exactly
            gets white-labelled (the ticketing flow? the scanner? the full event
            product?), the API product (what endpoints, what rate limits, what
            pricing, what the developer experience looks like), who the target
            buyer is, and the regulatory/liability implications of licensing the
            platform to third parties.
Priority    LOW — Stage 4, far future
Blocked by  Everything else. Spec only when approaching Stage 4 triggers.
```

### Innovations Registry
```
Status      UNSPECCED
Sources     [Z] §39
Gap         ZUKA.md §39 notes a catalog of ZUKA's innovations (the things that
            are genuinely new, patent-adjacent, or defensibly differentiated). The
            document exists in ZUKA.md but was never generated or synthesised into
            V4 or any concept file. This registry matters for investor conversations,
            for identifying what to protect, and for keeping the product honest
            about what's actually novel vs what's standard.
Priority    MEDIUM — important for IP awareness and pitch
Blocked by  Nothing. Can be generated by synthesising ZUKA.md §39 + the concept
            docs to date.
Notes       Items to include: the Smart Bucket QR inversion, the Go Live one-tap
            live activation, the Poster self-scan acquisition engine, the vendor
            scan-to-activate, context fusion intent engine, audio streaming
            conversion funnel, gift ticket as acquisition, the closed-system
            ticket provenance model.
```

---

## Section 4 — V7 Gathering Intelligence (Gathering Types)

### Gathering Constellations
```
Status      THIN
Sources     [V7] §14 · [V4] Vol 13 (brief mention)
Gap         V7 §14 defines "gathering constellations" — a gathering rarely exists
            alone; a wedding spans bridal shower → bachelor party → ceremony →
            reception → after-party → anniversary. A church spans Sunday service →
            youth → choir → Bible study → outreach. The constellation model lets
            ZUKA treat these as a connected series, not isolated events, enabling:
            shared Memory threads, relationship mapping across the series, tradition
            detection, and a single "recurring gathering" subscription for the
            organiser. Currently only noted in the Dormant Vision Register.
Priority    MEDIUM — high value for church/community/wedding markets; dormant until
            those gathering types are proven on the platform.
Blocked by  The core gathering model (Phase 1). GIE dormant until DRL 2+.
```

### Gathering Capability Packs — Church, Wedding, Funeral, School
```
Status      THIN
Sources     [V7] §14 · [V4] Vol 13 · Scanner Pro concept
Gap         V7 §14 specifies per-type capability packs in detail:
            Church Pack: recurring service, attendance, giving, prayer requests,
              volunteer scheduling, community archive, children check-in,
              announcements.
            Funeral Pack: condolence book, directions, family contacts, memorial
              archive, private donations, photo archive, livestream, quiet
              notifications.
            Wedding Pack: gift verification, table allocation, RSVP tracking.
            School/University Pack: never fully specced anywhere.
            The Scanner Pro concept mentions Layer-2 gathering capability packs
            but doesn't define any. Nothing has the actual per-type feature lists
            in a governed document.
Priority    MEDIUM — high value for faith/ceremony markets; ZUKA's differentiation
            from generic event apps
Blocked by  Core platform live. These are gathering-type templates, not core infra.
Notes       The Funeral Pack's "quiet notifications" rule is a non-trivial design
            constraint that touches the Notification Constitution (Vol 16 Ch 8)
            — grief-sensitive timing, no upbeat framing, condolence-only content.
            This needs careful design.
```

---

## Section 5 — Safety & Operations Features

### SafeRoute — Full Design
```
Status      THIN
Sources     [Z] §32 · [V4] Vol 20 (Zuka Ride connector brief mention)
Gap         ZUKA.md §32 is a complete SafeRoute spec: Share My Route (real-time
            location sharing with a trusted contact during a ride home), Arrive
            Safe (automatic check-in when a destination is reached), Driver Report
            (in-app incident reporting with driver details preserved), Emergency
            Alert (SOS to designated contacts + nearby ZUKA users), and
            integration with the Zuka Ride connector (SafeBoda). The Privacy
            implications of real-time location sharing are significant (DPPA).
            Currently only mentioned in passing in Vol 20.
Priority    MEDIUM — a trust/safety differentiator, especially for solo attendees
Blocked by  Zuka Ride connector (SafeBoda) live. DPPA review for location sharing.
```

---

## Section 6 — Graduated Items (moved to full concept or Bible volume)

> Items promoted from UNSPECCED/THIN to a full concept doc or Bible volume.
> Kept here as an audit trail.

```
DATE        ITEM                              GRADUATED TO
────────────────────────────────────────────────────────────────────────────
2026-07-09  Secondary ticket resale /         CONCEPT_Secondary_Ticket_Marketplace.md
            ownership-transfer marketplace     (Proposed). Resale was a latent gap:
            (organizer resale policy, paid      Vol 14 has the transfer engine but no
            listings, waitlist, anti-scalp)     resale policy / market / waitlist.
                                                Reality-checked (TRIM): transfer engine
                                                is spec-only/unbuilt → free-transfer slice
                                                is post-launch + demand-gated, NOT MVP;
                                                paid path dormant (ADR-029 + zuka-legal).
```

---

## Maintenance Instructions

```
ADDING A GAP:
  Copy a section template, fill in Status/Sources/Gap/Priority/Blocked by/Notes.
  Place it in the most relevant section. Add a [date] tag in Notes if discovered
  during a session.

GRADUATING A GAP:
  Change Status to GRADUATED. Add a "Graduated to: [filename] [date]" line.
  Add a row to Section 6. The original entry stays.

PARKING A GAP:
  Change Status to DORMANT. Add "Reason: [why dormant]" and "Trigger: [what
  would revive it]." Dormant items get reviewed in the annual Dormant Register
  review (Vol 24).

BLOCKING A GAP:
  Change Status to BLOCKED. Add "Blocked by: [what]." Review when the blocker
  resolves.
```

---

> **Unspecced & Thin Features Register — Living Document**
> The honest inventory of what's known but not yet designed. Updated continuously.
> Nothing here is built until promoted to a concept doc and ratified by the
> reality-checker. The goal is that this file eventually becomes empty — every
> gap either designed or deliberately parked.