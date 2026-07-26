# ZUKA — Concept Document
# In-Event Vendor Marketplace ("Live Vendors")

> **Type:** Feature Concept & Design Brief · **Status:** Proposed (pre-build)
> **Authority:** Subordinate to ZukaBibleV4. This document explores a feature;
> it does not amend the Bible. If built, the canonical entities it introduces
> graduate into Vol 04/14 via the normal ADR process — but NOT before.
> **Scope discipline:** read Chapter 8 (MVP cut lines) before building anything.
> **Working name:** Live Vendors (final naming → zuka-growth, per Brand Vol 25)

---

## 1. The Idea in One Paragraph

Inside a live event, the people selling things — snacks, drinks, merch, face-
painting — become discoverable to attendees through ZUKA, but **only** while the
event is live, **only** to people who are actually there, and **only** because
the vendor proved they showed up by having their vendor ticket scanned at the
entrance. The entrance scan is the activation key. When the event ends, the
listing evaporates. A vendor at Event A is invisible at Event B, even if it's the
same company — because activation is tied to the specific ticket scanned, not to
the vendor's identity.

This turns the same scan-to-enter mechanic that already exists (Vol 14) into a
second capability: **scan-to-go-live as a vendor.**

---

## 2. Why This Fits ZUKA (and isn't scope creep)

This is not a generic food-delivery bolt-on. It is a direct expression of ZUKA's
core thesis, which is why it belongs in the MVP conversation rather than the
dormant register:

```
- It is PARTICIPATION infrastructure: a vendor participates in a gathering, and
  that participation is made visible and useful. (Vol 01 participation chain.)
- It is REALITY-FIRST: the listing exists because of an observed real-world fact
  (the vendor was scanned in), not a claim. (Reality Doctrine, Vol 01.)
- It is the LIVE SYSTEM as brand: "what's alive right now, near you" extends from
  events to the vendors inside them. (Brand–Product Bridge, Vol 25 Ch 7.)
- It generates real ECONOMIC value for local Kampala micro-vendors — the kind of
  opportunity ZUKA exists to coordinate. (Vol 19, but at human scale, today.)
```

The honest counter-point (kept visible on purpose): a full ordering +
payments + live-status + reviews system is a *lot* for an MVP. Chapter 8 draws
the cut lines so this ships in a disciplined sequence rather than all at once.

---

## 3. The Activation Model (the heart of the feature)

This is the part that must be exactly right. Everything keys off **proven
presence at the event** — and there are TWO routes to prove it, because not every
event has tickets. The deep insight: the entrance scan was never really about
*ticketing*; it was always about *proving someone is really here*. Both routes
below converge on the same scanned credential.

### Route A — Ticketed events (paid or free-with-tickets)
```
A VENDOR-class TICKET for Event X is scanned at the entrance.
The vendor acquired this ticket the same way any ticket is acquired.
(Paid events: the vendor ticket carries a HIGHER commission — see Ch 9.)
```

### Route B — Free / un-ticketed events (the gap this closes)
Many events need vendors but issue no tickets — a free rooftop set, a community
festival, a free product launch. These had no activation path. The new route:
```
1. Vendor obtains PERMISSION to vend at Event X from that event's management,
   OFF the Zuka platform. (Zuka does not adjudicate who may vend at someone
   else's event — that's the organiser's business and liability, not Zuka's.)
2. Vendor pays Zuka DIRECTLY for a VENDOR LISTING for Event X (+ any on-platform
   services). This is the free-event route's primary revenue (Ch 9).
3. Zuka issues a VENDOR PASS for Event X — a scannable credential, functionally
   identical to a vendor ticket but with no attendee-ticket semantics.
4. The pass is scanned at the entrance → presence proven → listing activates.

PERMISSION IS ATTESTED, NOT VERIFIED: Zuka can't see an off-platform permission.
The vendor attests they have it; the event ORGANISER can see and remove any
vendor at their event from the organiser console. This keeps the organiser in
control without making Zuka the permission police.
```

### The unified rule (both routes converge here)
```
A vendor listing for Event X is ACTIVE if and only if:
  (a) a vendor CREDENTIAL for Event X — a vendor ticket (Route A) OR a vendor
      pass (Route B) — has been scanned at the entrance, AND
  (b) Event X is currently live (between doors-open and event-end), AND
  (c) the vendor has not manually paused themselves (see live status), AND
  (d) the vendor has no unsettled platform fee blocking activation (Ch 9).

Discovery of that listing is available if and only if:
  (e) the viewer is a proven attendee present at this event.
      Ticketed events: their own ticket was scanned.
      Free events: their own presence is established at entry (a free check-in /
      a tap-in at the gate — the same scanner, an attendee "free pass").
```

> Note for free events: attendees also need a lightweight "presence" check-in so
> rule (e) holds — otherwise anyone outside could browse the vendors. The
> simplest MVP form is a free tap-in at the gate (the scanner already there),
> issuing a no-cost attendance token. This is a small addition the backend must
> account for, flagged in Ch 10.

### Why activation is tied to the scanned ticket, not the vendor

This single decision solves the multi-team problem cleanly:

```
"Kampala Bites" runs three teams tonight:
  Team 1 → vendor ticket for Event A (Nyege afterparty)
  Team 2 → vendor ticket for Event B (corporate gala)
  Team 3 → vendor ticket for Event C (rooftop set)

Each team's listing activates ONLY when ITS ticket is scanned at ITS event.
Team 1 scanned in at A → Kampala Bites is discoverable at A, by A's attendees.
Team 2 hasn't arrived at B yet → no Kampala Bites listing at B.
Same brand, three independent activations, zero cross-event leakage.
```

The ticket is the event-scoped key. The vendor *identity* (Kampala Bites, with
its logo and menu) is reusable across events; the *activation* is per-ticket,
per-event, per-scan. This is the elegant core — it reuses the entrance scan you
already have and needs no new "which events is this vendor at" tracking.

### Lifecycle of a listing

```
DORMANT      vendor ticket issued for Event X; not yet scanned. No listing.
ACTIVATING   vendor ticket scanned at entrance → VendorPresence event fires.
LIVE         listing visible to present attendees; vendor can serve/order.
PAUSED       vendor toggled "back in 10 min" / "sold out". Visible, not orderable.
CLOSED       event ends (or vendor taps "done for the night"). Listing evaporates.

The listing NEVER outlives the event. Post-event, it is gone from discovery;
only its records (sales, reviews) persist for reporting.
```

---

## 3B. The Poster Self-Scan (free-event activation without a staffed gate)

### The problem
Free-event organisers may not bother staffing an entrance scanner — but without a
presence signal, vendor listings can't activate and no analytics exist. We must
not depend on the organiser's diligence for the feature to work.

### The idea (and its bonus)
ZUKA generates a **printable QR poster** for the event. The organiser prints
copies and places them at gates and around the venue. Attendees **scan the poster
with their phone camera**:
```
- Has ZUKA installed → app opens, registers them as present at this event,
  activates all the downstream presence logic (discovery, the attendee side).
- No ZUKA → routed to Play Store / App Store to install → free marketing, and
  the gift-loop dynamic (Vol 14) applied to free events. Every poster is a
  Zuka billboard.
```
This inverts the scan: the attendee scans the venue, not the gate staff scanning
the attendee. It removes the organiser-diligence dependency AND turns the venue
into an acquisition surface.

### Scope (your decision)
```
Poster self-scan is a FREE-EVENT activation route only.
Paid events continue to use STAFFED entrance scanning (the Smart Bucket flow,
Vol 14) — their tickets are the high-integrity presence proof and the analytics
they support are trustworthy.
```

### The honest catch: a printed code proves LESS than a staffed scan
A staffed gate proves "this person was physically admitted." A wall poster proves
only "this device scanned an image of a code" — and an image can be photographed
and shared. A person at home could scan a photo of the poster and be counted
"present." This barely matters for vendor discovery and marketing; it matters a
lot for paid analytics ("you had 1,200 attendees") and for review eligibility.

Two designs are presented; **the founder decides which to adopt.**

#### DESIGN 1 — Two-tier presence (honest separation)
```
LIGHTWEIGHT presence  (poster self-scan):
  Good enough for: vendor discovery · the live experience · free marketing ·
  rough attendance ("people here now"). Labelled as an ESTIMATE, never sold as
  a verified headcount.
HIGH-INTEGRITY presence  (staffed scan, paid events):
  Required for: trustworthy paid analytics · review eligibility · anything a
  third party (sponsor, the organiser's own reporting) relies on as fact.

The two signals are stored distinctly and never conflated. A free event simply
has no high-integrity tier — and that's stated plainly in its analytics.
```
Strength: honest, simple, no false precision. Weakness: free events can't offer
paid-grade analytics or reviews. (Acceptable — those can be the carrot to run a
paid/ticketed event or add staffed scanning.)

#### DESIGN 2 — One tier, mitigated (poster-scan hardened)
```
Make the poster scan good-enough-for-most via anti-abuse controls:
  ROTATING CODE     the poster QR shows a code that rotates (a small e-ink or a
                    periodically reprinted/displayed code; or a static poster code
                    + a server-side rotating nonce the app fetches on scan).
  GPS FENCE         on scan, the app checks the device is within the venue
                    geofence (PostGIS, already in stack). Out-of-fence scan →
                    not counted as present (or counted as "remote/marketing").
  VELOCITY CAP      N scans of the same code from improbable spread → flagged
                    (the Trust OS velocity substrate, Vol 11) — discounts the
                    abusive ones.
  ONE-PER-DEVICE    a device counts once per event; re-scans don't inflate.
```
Strength: a single, simpler attendee story. Weakness: the GPS fence is the real
guarantee and GPS is spoofable / flaky indoors; mitigations reduce abuse but
don't equal a staffed gate. Risk of *false* confidence in the numbers.

```
RECOMMENDATION (zuka-reality-checker lens): start with DESIGN 1. It's honest,
needs no rotating-code hardware, and never sells a number it can't stand behind.
Borrow Design 2's GPS fence as a cheap enhancement (mark out-of-fence scans as
"remote/marketing," not present). Don't build rotating codes at MVP — that's
hardware/complexity the free-event tier doesn't justify yet.
```

### The poster also needs an end time → see 3C.

---

## 3C. Event Duration (a required primitive, not just a vendor concern)

The "live only while the event is live" logic — vendor presence, Go Live, crowd
meter, the poster's validity — all need an authoritative end. Today that's
under-specified. Requirement:
```
EVENT DURATION IS REQUIRED AT EVENT CREATION:
  scheduled_start + scheduled_end (the organiser must provide both).
  The poster QR is valid from start until (scheduled_end + grace).

DYNAMIC DISABLING uses it:
  vendor listings, poster scans, live features auto-close at the boundary.

BUT events run over — so the boundary is soft, not a guillotine:
  scheduled_end + GRACE WINDOW (e.g. 2h)  → features wind down
  organiser "We're done" override          → close now
  organiser "Extend"                        → push the boundary
  A vendor mid-sale at the boundary isn't cut off — the grace window + an
  in-flight-order rule (Ch 10) protect them.
```
This is a small, clean addition with broad benefit — it firms up the temporal
edge the whole live-system relies on, not just vendors.

---

## 3D. Reusable Vendor Assets (don't destroy what's reusable)

Event-scoped activation must not throw away durable, reusable resources when the
event ends. The entity split already does this (Ch 7): the EPHEMERAL part
(VendorPresence) dies at event end; the DURABLE part (VendorProfile) persists.

```
DURABLE (survives every event, lives on VendorProfile — the vendor's library):
  - Vendor identity (name, logo, MoMo number)
  - MENU TEMPLATES — named, reusable, versioned:
      "Rooftop Menu", "Festival Menu", "Day Stall Menu"
    A vendor builds a menu once and IMPORTS it into any event's listing.
  - Default location text, default hours pattern
  - Accumulated reputation (rating travels across events, Ch 5d)

EPHEMERAL (event-scoped, dies at event close):
  - The VendorPresence (this event's activation + live status)
  - This event's orders, this event's review anchors
  - Any per-event menu tweak (overrides the template for THIS event only)

THE FLOW:
  Create menu once → save as a template on the profile → import into Event A →
  tweak prices for A if needed → event ends → template UNCHANGED in the library
  → import into Event B next week in two taps.
```
Menu versioning means price changes over time are tracked (Temporal Truth, Vol
05) and a vendor can roll back to a prior menu. Nothing reusable is ever lost at
event close — only the live activation evaporates.

---

## 3E. Vendor Analytics & Documents (a monetization surface, with a fairness floor)

Vendors will want data and documents — and this is a clean B2B SaaS revenue
surface (data, not money movement → no NPS Act exposure, Ch 9). The governing
principle keeps it the right side of the anti-drain rules:
```
FREE (a vendor's own basic data is theirs — charging for it would be extraction):
  - Tonight's tally: orders, items sold, their own MoMo takings (their data)
  - Current live status, their own menu, their own rating

PAID (genuine value-ADD — enrichment, not their raw data):
  - Cross-event trends: "your skewers sell 2x faster at rooftop events"
  - Downloadable, formatted DOCUMENTS: sales summaries, receipts, and
    EFRIS-/tax-ready records (real admin value for a Ugandan micro-business)
  - Benchmarking: "you're in the top 20% of drink vendors at this venue type"
    (anonymised/aggregated per Vol 21 — never another vendor's raw data)
  - Attendee-demand insight: what people searched for but no vendor offered
  - Retention/repeat-customer patterns across events

PRICING: micro, vendor-scale (a few thousand UGX, or bundled into a higher
listing tier). It compounds with the listing fee — the analytics make the
listing more valuable, the listing generates the analytics.
```
This is the same "compounding before extraction" logic as the order fee: the
free basics hook the vendor; the paid enrichment is bought because it
demonstrably helps them sell more or do their admin faster.

---

## 3F. Organiser Vendor Setup (the event's vendor plan)

The organiser configures the vendor side of an event the way they configure
attendee ticket tiers (Vol 14) — same shape, applied to vendors and other
in-event service providers.

```
REQUIRED VENDOR COUNT + CATEGORY MIX:
  The organiser declares how many vendors the event needs, by category:
    "8 vendors: 4 Food · 2 Drinks · 1 Merch · 1 Other(service)"
  This drives: capacity per category (no over-selling vendor spots), the
  event's vendor-readiness ("6 of 8 filled"), and the suggestion of how many
  vendor passes/tickets to make available.

VENDOR CATEGORIES — a PLATFORM-PROVIDED CHECKLIST the organiser picks from:
  Food · Drinks · Merch · Crafts · Services (face-paint, photo) ·
  Experiences · Hawkers · Performers · Other. The organiser selects from this
  list (not free-text) so every category carries the platform's trackability
  level and fees (3H). Categories drive pricing, the listing filter attendees
  see, and analytics. New categories are added to the checklist by the platform,
  not invented per event.

CATEGORY-BASED VENDOR-TICKET PRICING (like attendee tiers):
  Each vendor category has its OWN vendor-ticket/pass price, and pricing can
  reflect placement value:
    "Drinks spot near the stage — UGX 150,000"
    "Food court pitch — UGX 100,000"
    "Back-corner merch table — UGX 40,000"
  Paid events: this vendor ticket carries a HIGHER commission than attendee
  tickets (Ch 9). Free events: this is the listing fee (Route B, Ch 3).
  OTHER SERVICE PROVIDERS (a DJ-for-hire, a photo booth, a kids' corner) use the
  same category/pricing model — "vendor" is shorthand for any in-event provider.

REUSABLE VENDOR T&Cs (durable, like menus — 3D):
  The organiser writes their vendor terms ONCE ("you bring your own power; you
  clear your own waste; pitch fee non-refundable; Zuka commission applies"),
  saves them as a reusable VendorTermsTemplate on their organiser profile, and
  attaches the template to any event.
  A vendor ACCEPTS the T&Cs when acquiring the vendor credential; acceptance is
  recorded PER EVENT with the terms version + timestamp (Temporal Truth, Vol 05)
  — so there is always a record of who accepted what, when. Terms are versioned;
  updating the template never silently changes what a past vendor agreed to.
```

---

## 3G. Vendor Company / Team Model & the Commission Lock

This chapter closes two escape hatches and reshapes the entity model: debt must
trace to the COMPANY, not a disposable team, and an unpaid company is locked
everywhere — but only in the vendor domain.

### The two-level hierarchy (replaces the flat "VendorProfile")
```
VendorCompany   the durable business entity (e.g. "Kampala Bites").
                OWNS: the billing relationship, the accumulated commission-debt
                ledger, reputation, T&C acceptances, reusable menus (3D).
VendorTeam      a unit of the company that works a specific event. Carries the
                event credential (ticket/pass) that gets scanned. A company may
                run MANY teams at MANY events simultaneously.
```
Independent activation, shared accountability: each team still activates
per-event (the credential is still the event-scoped key, Ch 3), but billing and
the lock are evaluated at the COMPANY level.

### The commission lock (HARD, company-level — your decision)
```
TRIGGER (hard lock):
  ANY unsettled platform fee owed by the company → the company is LOCKED.
  No threshold, no per-event grace before locking. One unpaid fee locks the
  whole company.

SCOPE — what the lock restricts (recommendation, reasoned in §below):
  RESTRICTED (vendor-domain only):
    - No vendor listing activates for ANY of the company's teams, at ANY event.
      The listing does not appear in the attendee feed.
    - The company cannot ACQUIRE NEW vendor credentials (tickets/passes) for
      future events until settled. (Closes the "just buy another and treat the
      lock as noise" hatch.)
  NOT RESTRICTED (the Person/attendee relationship is untouched):
    - Anyone from the company may buy/use ANY ATTENDEE ticket, attend any event,
      and order from other vendors as a normal customer.
    - Browse, discover, full attendee-side participation — all open.

THE FLAG (told at the gate, not discovered by silence):
  The moment a locked company's team credential is scanned at an event:
    → presence does NOT activate as a vendor
    → the team's phone shows: "Listing locked. <Company> has an unsettled
       platform fee of UGX X from <event>. Settle to go live."
  No silent failure — they learn at the scan, with the exact amount and source.

INSTANT UNLOCK (what makes a hard lock FAIR):
  The moment the company settles, EVERY team unlocks immediately. No appeal
  queue, no manual review for a plain payment. The key to the lock is always in
  the company's own pocket — it is a gate they hold the key to, not a punishment.

MID-EVENT EDGE CASE (don't yank a live listing):
  If a company crosses into "locked" while one of its teams is ALREADY live and
  selling, that active presence FINISHES its event (don't pull a listing out
  from under attendees mid-order). The lock blocks the NEXT activation, at the
  next scan — consistent with the event-duration grace logic (3C).
```

### Why this restriction scope (anti-drain / fairness reasoning)
```
- ANTI-DRAIN (Vol 23) is about not strangling value ZUKA creates; it does NOT
  forbid withholding a privilege whose fee is unpaid. The vendor genuinely owes
  for sales ZUKA drove. Not extending more vendor credit until settled is fair,
  not extractive.
- PROPORTIONALITY (Vol 11 enforcement ladder): restrict ONLY what the debt
  relates to. The debt is vendor activity → restrict vendor activity (listing +
  new vendor credentials). Nothing else.
- A VENDOR IS ALSO A PERSON (Vol 08): the Person's right to participate as an
  attendee is not forfeit because the Company owes a vendor fee. Touching their
  attendee life would punish an unrelated relationship. Correct scoping, not
  leniency.
- BLOCKING NEW VENDOR CREDENTIALS is the one addition that gives the lock teeth:
  without it a locked company keeps buying vendor tickets, gets flagged at each
  gate, and never settles. With it, "you can't line up your next gig until you
  pay" — the pressure that actually collects the fee, still strictly in-domain.
```

---

## 3H. The Trackability Spectrum & How Untrackable Vendors Are Charged

The order fee (Ch 9) only works when sales flow through the app. But some vendors
never create a `VendorOrder` — a face-painter, a roaming hawker, a tip-based
performer. The platform can't see their sales, so it can't take a percentage.
This chapter resolves how they're charged, fairly.

### The category checklist carries a trackability flag
The organiser builds the event's vendor plan from a platform-provided
**category checklist** (3F). Each category carries a platform-assigned
trackability level — the organiser just picks categories; the model knows which
are trackable.

```
THE SPECTRUM (per category, platform-assigned):

FULLY-TRACKABLE     discrete orders flow through the app (snacks, drinks, merch).
                    Charge: lower upfront fee + tiered fee on tracked orders (Ch 9).

PARTIALLY-TRACKABLE vendor can't take payment in-app but OPTS IN to a simple
  (self-reported)   in-app volume signal — a tap-counter ("served +1") or an
                    end-of-night self-reported tally. Charge: a MIDDLE flat fee —
                    lower than untrackable, as a reward for sharing a signal.

UNTRACKABLE         no order, no signal (face-painting, hawkers, tip performers).
                    Charge: a HIGHER flat upfront fee, per category (below). No
                    per-sale commission — there's nothing the platform can see.
```

### How untrackable vendors are charged (your decisions)
```
THE PRINCIPLE: if the platform can't take a cut of sales it can't see, it charges
MORE UPFRONT instead. The higher upfront fee is the SUBSTITUTE for the order
commission — not a penalty, just the only mechanism that fits how they sell.

UNTRACKABLE vendor pays:  category vendor-ticket/listing fee
                          + a PLATFORM-SET, PER-CATEGORY untrackable surcharge
                          (paid directly to Zuka, over the ticket price)
                          + NOTHING on sales (can't see them)

- "Slightly higher commission" = the premium lives in the UPFRONT surcharge,
  since there is no per-sale cut to raise. It stands in for the commission they
  will never pay per-sale, and it is kept SLIGHT (anti-drain, Vol 23) — not
  extractive.
- DYNAMIC PER CATEGORY: the surcharge is set PER untrackable category, reflecting
  that category's realistic earning potential at an event. A face-painter
  (low-value, slow throughput) pays less than a high-value untrackable service
  (e.g. a henna station). Platform-set, fixed per category — the organiser just
  picks the category; the fee is what it is (your decision).
```

### Who sets the fee (your decision)
```
The PLATFORM sets a FIXED per-category untrackable surcharge. The organiser does
not adjust it — they pick the category, and the fee is determined. This keeps
pricing consistent across events and prevents an organiser from waiving Zuka's
fee to attract vendors. (Zuka's revenue, Zuka's price.)
```

### The self-report tier — honest about what it is
```
Self-reported volume is UNVERIFIABLE — a vendor could under-report. The model
does NOT pretend otherwise, and is designed so lying barely pays:

- The platform does NOT take a percentage of self-reported sales (that WOULD
  reward under-reporting). It charges a FLAT middle fee — a fixed step between
  trackable and untrackable — in exchange for ANY volume signal.
- Because the discount is a fixed step, not a cut of what they report, the
  incentive to lie about the number mostly evaporates. They're paying for the
  TIER, not per reported sale.
- The signal's value to Zuka is rough demand data (which vendor types draw
  crowds, at which events) — useful for the suggestion engine and the organiser's
  vendor-mix advice, explicitly understood as vendor-asserted, not audited.

This turns "untrackable" from a fixed penalty into a CHOICE: pay the higher flat
fee and stay invisible, or share a signal and pay the middle fee. Vendor agency,
not platform punishment.
```

### The anti-gaming guardrail (the trap to avoid)
```
DON'T price the untrackable surcharge so high it pushes a vendor to register as a
cheaper category and do their real thing anyway. If "face-painting" is punishing,
a face-painter just books the cheapest slot and paints. So:
- Per-category fees track REALISTIC earnings, not aspirational platform revenue.
- The untrackable premium stays genuinely SLIGHT.
- The organiser sees the declared category vs what the vendor actually does
  (they're at the event) and can flag a mismatch — a light human check, not an
  enforcement system.
```

---

## 4. How It Reuses Existing Architecture

Almost nothing here is net-new plumbing. It rides on locked systems:

```
EXISTING (Vol 14)                  REUSED FOR VENDORS
─────────────────────────────────────────────────────────────────────────
Vendor = a ticket_class            add 'vendor' to the existing class enum
Entrance scan → TicketCheckedIn    same scan also fires VendorPresenceActivated
Scanner session bound to event      guarantees activation is event-scoped already
Attendee check-in (proven present)  the gate for WHO can see the listings
Smart Bucket / contextual-blind     vendor scan works exactly like attendee scan
MoMo via Flutterwave (Vol 14)       the ordering payment rail — already in stack
Realtime (Vol 13 City Pulse)        live status / queue / "sold out" push
Event ends → lifecycle close        the listing's natural expiry
```

So the entrance scanner doesn't need a new mode. It scans a vendor ticket the
same way it scans any ticket; the server simply recognises the vendor class and
fires the presence activation in addition to the normal check-in. (Recall the
Matching Engine already returns ticket type — Vol 14 Ch 7. The vendor class is
just another returned type, with a side effect.)

---

## 5. The Attendee Experience — The "Live Vendors" Listing Page

What a present attendee sees. Entry point: a "Vendors here" / "Grab something"
affordance that appears in the event's live view ONLY when they're checked in and
at least one vendor is active.

### 5a. The list view
```
Header: "Live at <Event Name>"  ·  "<n> vendors here right now"

Each vendor row (a VendorCard):
  - Vendor name + tiny logo/avatar
  - What they sell (one line: "Cold drinks · water · sodas · energy")
  - Live status pill (information-colour, per Brand Vol 25):
      OPEN (gold) · BUSY ~10 min (gold, queue shown) · SOLD OUT (neutral) ·
      PAUSED "back soon" (neutral)
  - Location at the venue: "Near the main stage, left of the bar"
  - Distance/zone if the venue is mapped; otherwise the text spot
  - ★ rating (this event's attendees only — see 5d) — phases in, not MVP-day-1
  - Tap → vendor detail
```

### 5b. The vendor detail / menu view
```
  - Name, what they are, location, live status, hours active tonight
  - Menu: items + prices in UGX
      "Still water 500ml — UGX 2,000"
      "Soda — UGX 3,000"
      "Chicken skewer — UGX 8,000  (SOLD OUT)"
  - Per-item availability (sold-out greys out, never hidden)
  - [Order] button → ordering flow (if ordering is enabled for this event)
  - Reviews from this event's attendees (phases in)
```

### 5c. The ordering flow (MoMo, 3-tap-spirit per Vol 14)
```
  Tap 1  add items → cart
  Tap 2  choose fulfilment: "Collect from vendor" or "Bring to my seat/table"
         (delivery option only if the event/vendor supports it; tables know
          their location from the GatePlan/table concept if present)
  Tap 3  pay with MoMo (saved method) → order sent to vendor

  Vendor sees the order on their side; attendee sees status:
    RECEIVED → PREPARING (prep-time estimate) → READY (collect) / ON ITS WAY
  Order tied to the attendee's checked-in identity at THIS event.
```

### 5d. Reviews (event-scoped — the distinctive constraint)
```
  Only attendees whose ticket was scanned at THIS event can review a vendor at
  THIS event. (Same integrity rule as Vol 11: only checked-in attendees review.)
  A review is tied to (vendor, event, attendee). This keeps reviews real —
  you can only rate a snack you could actually have bought, at an event you
  actually attended. No drive-by reviews, no review farming across events.
  Vendor's rating can aggregate across events over time (their reputation
  travels), but each REVIEW is anchored to a real attended event.
```

---

## 6. The Vendor Experience — "Vendor Live" Mode

What the service provider sees, on their own phone, after scanning in.

```
ACTIVATION:  vendor's ticket scanned at entrance → phone shows
             "You're live at <Event>. Attendees here can now find you."

VENDOR CONSOLE (lightweight — they're busy selling):
  - One big toggle: OPEN  ·  PAUSE ("back in __ min")  ·  SOLD OUT  ·  DONE
  - Set/confirm their spot: "Near main stage" (text MVP; map pin later)
  - Menu management: items, prices, mark item sold-out in one tap
  - Live queue/prep: incoming orders, mark PREPARING / READY
  - Tonight's tally: orders, items sold, MoMo takings (their own data only)

CONSTRAINTS:
  - The console works on a cheap Android on 3G. Big buttons, few taps.
  - If they have multiple teams at multiple events, each team's phone is bound
    to ITS event via ITS scanned ticket — the console only ever shows THIS event.
```

---

## 7. New Concepts This Introduces (for eventual ADR, NOT yet canonical)

If this graduates from concept to build, these are the entities/events it adds.
Listed here so the design is concrete; they become canonical only via ADR into
Vol 04/14 — this document does not make them so.

```
ENTITIES (proposed):
  VendorCompany      the durable business entity (e.g. "Kampala Bites"): name,
                     logo, own MoMo number for direct payment, reputation. OWNS
                     billing + the commission-debt ledger + T&C acceptances +
                     reusable menus. The lock (3G) is evaluated at THIS level.
  VendorTeam         a unit of a company working a specific event; carries the
                     event credential that gets scanned. (vendor_company_id,
                     label e.g. "Rooftop crew"). Many teams per company. (3G)
  CommissionDebtLedger  (vendor_company_id, entries[]) — append-only accrual +
                     settlement of platform fees. The lock reads this. (3G, Ch 9)
  VendorCredential   the scannable proof-of-presence key for an event. Either a
                     vendor TICKET (Route A) or a vendor PASS (Route B, free
                     events). (event_id, vendor_team_id, category, kind,
                     fee_state). Category-priced (3F). Acquisition BLOCKED while
                     the company is locked (3G).
  VendorCategory     event-scoped selection from a platform CHECKLIST. Each
                     category carries a platform-assigned trackability level
                     ['fully'|'partial_self_report'|'untrackable'] and, for
                     untrackable categories, a fixed per-category untrackable
                     surcharge. Drives pricing + the attendee filter. (3F, 3H)
  VendorVolumeSignal optional self-reported volume from a partially-trackable
                     vendor (vendor_presence_id, count|tally, self_reported=true).
                     Earns the middle fee tier; vendor-asserted, not audited. (3H)
  VendorTermsTemplate reusable organiser T&Cs (organiser_id, version, body);
                     attached to events, accepted per-event. (3F)
  VendorTermsAcceptance (vendor_company_id, event_id, terms_version, accepted_at).
                     Temporal record of who accepted what version, when. (3F)
  EventPoster        the printable self-scan QR for a free event (event_id,
                     poster_code, valid_from, valid_to). Scanning it creates an
                     AttendancePresence. DEFINED IN the Poster Acquisition Engine
                     concept (its owner) — referenced here, not redefined. (3B.)
  AttendancePresence a person's proven/estimated presence at an event. SHARED —
                     owned by the Poster Acquisition Engine concept; referenced
                     here. (person_id, event_id, tier['high_integrity'|
                     'lightweight'], source['staffed_scan'|'poster_scan'],
                     in_geofence bool, created_at). Tier gates discovery vs
                     paid-analytics vs reviews.
  MenuTemplate       a reusable, versioned menu on the VendorCompany (NOT
                     event-scoped). (vendor_company_id, name, version, items[]).
                     Imported into a VendorPresence per event. Survives event end.
  VendorPresence     the event-scoped activation. (vendor_team_id, event_id,
                     credential_id, status, location_text, activated_at,
                     closed_at). Created on entrance scan, killed on close. Does
                     NOT activate if the company is locked (3G).
  VendorMenuItem     belongs to a MenuTemplate (reusable) and/or a per-event
                     override. (template_id|presence_id, name, price_ugx,
                     available bool). Menu built once, imported per event (3D).
  VendorOrder        (vendor_presence_id, buyer_attendee_id, items, total_ugx,
                     fulfilment_type='collect', status, paid_via='vendor_momo').
                     Records the order for the platform fee + review eligibility;
                     ZUKA does NOT process the payment (NPS Act, Ch 9).
  VendorPlatformFee  (vendor_company_id, event_id, tier, amount_ugx, settled).
                     The tiered success-based fee Zuka invoices the company;
                     accrues into the CommissionDebtLedger. (3G, Ch 9)
  VendorReview       (vendor_company_id, event_id, attendee_id, rating, text).
                     Event-anchored integrity; reputation accrues to the company.

EVENTS (proposed, append-only per ADR-015):
  VendorPresenceActivated   ← fired by the entrance scan (the trigger)
  VendorPresenceBlocked     ← scan of a LOCKED company's team → flag returned (3G)
  AttendancePresenceCreated ← staffed scan OR poster self-scan (carries tier)
  PosterScanned             ← poster self-scan (may route to app-store install)
  VendorStatusChanged        (open/paused/soldout/done)
  VendorOrderPlaced · VendorOrderReady · VendorOrderCollected
                             (no VendorOrderPaid — payment is vendor-direct,
                              off Zuka's rails)
  VendorPlatformFeeAccrued · VendorPlatformFeeSettled
  VendorCompanyLocked · VendorCompanyUnlocked   ← the company-level lock (3G)
  VendorTermsAccepted
  VendorVolumeSelfReported   ← partially-trackable vendor's signal (3H)
  MenuTemplateSaved · MenuTemplateImported
  VendorAnalyticsPurchased
  EventClosed                ← scheduled_end + grace, or organiser override (3C)
  VendorPresenceClosed       ← fired at event close
  VendorReviewSubmitted

REUSED (no change): TicketCheckedIn, the scanner session. (NOT the MoMo
PaymentIntent for orders — that's vendor-direct. Zuka's PaymentIntent IS used
for the vendor ticket / listing fee, which the vendor pays Zuka.)
```

### The rules the backend must enforce hardest
```
1. NO SCAN, NO LISTING. VendorPresence can only go LIVE if a
   VendorPresenceActivated event exists for (this credential, this event) — the
   ticket/pass was really scanned. Enforced server-side like ticket provenance
   (Vol 14 Ch 1). A vendor cannot go live by any path other than being scanned in.

2. LOCKED COMPANY, NO LISTING. At scan time the server checks the team's
   VendorCompany against the CommissionDebtLedger. If the company has ANY
   unsettled fee, presence does NOT activate; a VendorPresenceBlocked event fires
   and the flag is returned to the team's phone with the amount + source event
   (3G). One unpaid fee, all teams everywhere, instantly. Settling unlocks all
   immediately. An already-live presence is not yanked mid-event (3G edge case).

These two are the integrity spine of the whole feature.
```

---

## 8. MVP Cut Lines (read before building — scope discipline)

"Discovery + ordering + live status + reviews" is the full vision. Shipping all
of it at once is a lot for a solo founder. Phasing (ratified by the
zuka-reality-checker pass in Ch 11):

```
PHASE 1 — PRESENCE + DISCOVERY  (the real MVP; proves the core idea)
  Vendor scans in (Route A or B) → listing activates → present attendees see
  who's here, what they sell, where, and live status (open/paused/sold-out).
  Includes: the POSTER self-scan for free events (3B, Design 1 + GPS fence),
  REQUIRED event duration (3C), reusable MENU TEMPLATES (3D), the ORGANISER
  vendor setup — required counts, categories, category pricing, reusable T&Cs
  (3F), the COMPANY/TEAM model + hard commission LOCK (3G), and the
  TRACKABILITY SPECTRUM + per-category untrackable fees (3H).
  NO in-app payment. Attendee walks over and buys in person ("collect").
  Revenue at Phase 1: vendor ticket (paid events) / listing fee (free events).

PHASE 2 — ORDERING + COLLECT  (money enters — gated on legal)
  Add the order flow + VENDOR-DIRECT MoMo + order status + the tiered platform
  fee (Ch 9). Fulfilment is COLLECT-FROM-VENDOR only. Needs the money plan-gate
  (CLAUDE.md), zuka-backend + zuka-security review, and zuka-legal sign-off on
  the vendor-direct money flow (Ch 9).

PHASE 3 — REVIEWS + RICHER LIVE STATUS + PAID ANALYTICS
  Event-scoped reviews, queue length, prep-time estimates, and the PAID vendor
  analytics/documents surface (3E) — cross-event trends, EFRIS-/tax-ready docs,
  benchmarking. Reputation that travels across events.

DEFERRED TO ITS OWN CONCEPT — "Table Delivery"
  Delivering an order to a seat/table is NOT a Phase-2 line item. It depends on
  (a) Phase-2 ordering being live and proven, AND (b) a table/seat-location
  concept that does not yet exist (the GatePlan/TableAssignment gap). Promoted
  to its own future concept; see Ch 11 reality-check. Until then, fulfilment is
  collect-from-vendor only.

DORMANT (not now): vendor-to-vendor, multi-event vendor dashboards, vendor
  ads/promotion, delivery logistics/runners, loyalty across events.
```

Phase 1 is genuinely MVP-sized and is the honest recommendation for first build.

---

## 9. Monetization (compounding, and the right side of the NPS Act)

### The hard regulatory constraint (verified, June 2026)
Uganda's **National Payment Systems Act 2020** regulates "aggregators" — any
party that *facilitates electronic receipt and payment for goods and services* —
and operating as one without a Bank of Uganda licence is an offence carrying
serious penalties and capital requirements (UGX 50M–500M class). **Therefore:**

```
ZUKA MUST NOT sit in the buyer→vendor money flow for orders.
If Zuka collects an attendee's payment and remits it to the vendor (even via a
Flutterwave split), Zuka is acting as an aggregator → licence required. A solo
pre-launch startup cannot carry that. This is non-negotiable until/unless Zuka
deliberately pursues that licence as a much later strategic step.
```

### The money flow (recommended, legally clean)
```
ATTENDEE → pays the VENDOR DIRECTLY (vendor's own MoMo, shown in the listing).
ZUKA     → never touches that money. Zuka charges the VENDOR a separate
           platform fee for the listing + on-platform services (a B2B software
           charge), invoiced to the vendor — not skimmed from the transaction.
```
This is the same posture the Bible already takes (ticketing goes *through*
Flutterwave; the Event Wallet is closed-loop, lighter class). Zuka stays a
software/discovery platform, not a payment system.

### The compounding revenue surfaces
```
SURFACE                     WHO PAYS   SHAPE              STATUS
─────────────────────────────────────────────────────────────────────────
Vendor ticket (paid events)  vendor    HIGHER commission  it's a business
                                       than attendee        expense → higher
                                       tickets (locked       margin is fair &
                                       ask)                  expected
Vendor listing fee           vendor    flat fee, paid      the free-event
  (free events)                        direct to Zuka       route's primary
                                                            revenue
Order platform fee           trackable TIERED & FORGIVING  success-based B2B
  (Phase 2)                   vendor   (see below)          fee on THEIR sales
Untrackable surcharge        untrack-  PLATFORM-SET, fixed  substitute for the
  (over ticket price, 3H)     able     PER CATEGORY,         order fee they
                              vendor   "slightly higher"     can't pay per-sale
Self-report middle fee        partial  flat MIDDLE step     reward for sharing
  (3H)                         vendor   (not a % of report)  a volume signal
On-platform services         vendor    freemium / add-on   analytics, featured
  (analytics, featured slot)                                slot (watch Vol 15
                                                            fairness)
```

> The trackability spectrum (3H) determines WHICH shape a vendor pays: fully-
> trackable → order fee; partial → middle flat fee; untrackable → per-category
> upfront surcharge. The platform charges through the only mechanism that fits
> how each vendor actually sells.

### The tiered forgiving order fee (your chosen shape)
Because Zuka never holds the money, the order fee is a **success-based software
fee**, calculated from the vendor's order volume tracked on-platform and billed
to the vendor:
```
Tier 0  first N orders / below a UGX volume threshold  →  FREE
        (pure upside; the vendor gets hooked, experiences Zuka making them money)
Tier 1  above the threshold                            →  small % of order volume
        (they only pay once Zuka is demonstrably driving sales)
Tier 2  high volume                                    →  slightly higher %,
        still forgiving — never approaching the anti-drain line (Vol 23)

This is "compounding before extraction" (Monetization Constitution) made literal:
the cheap/free thing (ordering) sells the profitable thing (the listing/ticket).
The vendor pays BECAUSE Zuka brought them the sales.
```

### Collection (the honest catch, and its fix)
Invoicing a vendor after the fact carries collection risk. Mitigation rides the
existing scan-gate, no payment rails needed:
```
- The small platform fee settles against the vendor's NEXT listing/ticket:
  an unsettled fee blocks the next event's activation (rule (d) in Ch 3).
- Or a small pre-paid listing deposit the fee draws down.
Either way Zuka gets paid without ever touching buyer→vendor money.
```

### Open monetization calls (founder + zuka-legal)
```
- Exact vendor-ticket commission uplift vs attendee tickets.
- Exact listing-fee price points (paid vs free events may differ).
- The tier thresholds and percentages (keep well inside anti-drain bounds).
- Featured-vendor placement vs the Vol 15 fairness-of-exposure rule — paid
  visibility must never fully starve an unpaid vendor.
```

---

## 9B. Phase-2 Order Fulfilment — the payment-blind confirmation mechanic

> Added 2026-07-06 from the Settlement brain-dump triage (full record:
> `docs/superpowers/specs/2026-07-06-settlement-triage-phase2-fulfilment-design.md`).
> This fills the gap Ch 8's "Phase 2" left open: *how* an order is confirmed when
> ZUKA is blind to the money. It **reconciles and replaces** the externally-pasted
> "Settlement Capability" proposal (`CONCEPT_Settlement_Capability.md`), which
> re-invented this in non-canonical, rule-violating form.

### The constraint everything obeys
Per Ch 9, ZUKA never sits in the buyer→vendor flow — the buyer pays the vendor's
own MoMo directly, and **ZUKA is blind to that money.** Therefore the order has
**no `PAID` state**: ZUKA cannot assert payment. It tracks *placement* and
*fulfilment* only.

### The `VendorOrder` lifecycle (event-sourced, ADR-015 — projection, never in-place UPDATE)
```
             (vendor accepts & starts)   (vendor)      (vendor scans buyer's order QR)
  PLACED ───────────────▶ PREPARING ───────▶ READY ───────────▶ COLLECTED
    │                                          │                 └ the single tracked event
    ├─▶ REJECTED   (vendor declines: sold out / closing)
    ├─▶ CANCELLED  (buyer, only while still PLACED)
    └─────────────────────────────────────────┴─▶ EXPIRED (READY, uncollected in window)

  Buyer-facing:  "Placed" → "Being prepared" → "Ready for pickup" → (done)
```
- The vendor's **"start preparing" tap is their own payment-risk call** — ZUKA
  never asserts payment happened.
- **Refunds/cancellations move no in-app money** (vendor↔buyer off-rail; ZUKA
  records `CANCELLED` only).
- The Ch 3G in-flight-order rule protects a queued order if the commission lock
  trips (the lock blocks the *next* activation, not the current queue).

### The confirmation mechanic — vendor scans buyer (reuses Scanner Pro)
At handover the vendor scans the buyer's order QR via a **new Scanner Pro
`FULFILMENT` capability** (alongside Entry/Vendor) → `READY → COLLECTED`. This is
**fulfilment attestation, not payment confirmation.** `COLLECTED` is the one
on-platform signal — it feeds the fee accrual *and* anchors a future review/return
(Phase 3). It's fraud-sound: the order QR is buyer-specific, and marking
`COLLECTED` only *raises* the vendor's own fee (no padding incentive).

### Offline (first-class)
| Path | Requirement |
|------|-------------|
| Order placement | **Online-only** — no signal → in-person = Phase 1 (built). |
| Preparing/ready status | Online best-effort (Realtime); tolerate staleness. |
| **Collection scan** | **MUST work offline** — reuses S013 offline manifest + rotating QR + `offline_scan_log`; `COLLECTED` syncs later. |

### Fee base & anti-gaming (both reuse Ch 9 / Ch 3H)
- **Fee base = the in-app *listed* order value** (Σ items × listed price), not real
  takings — ZUKA is blind. Forgiving tiers per Ch 9 (Tier-0 free). The fee is
  **decoupled** from the scan: the scan is for correctness/fraud/reviews; revenue
  never hangs on a perfect per-order confirmation.
- **Anti-gaming = the Ch 3H spectrum.** A vendor pushing sales off-app to dodge the
  tracked fee is classified **untrackable** and pays the per-category upfront
  surcharge instead — the escape hatch is already closed.

### New entities (extend Ch 7)
`VendorOrder` (event-sourced, lives as an order token in the Smart Bucket) ·
Scanner Pro `FULFILMENT` capability. **Money-domain — plan-gate required**
(`zuka-legal` + `zuka-security`), ships as a session, not a generic plan.

---

## 10. Open Questions (for backend / legal / design)

```
- POSTER PRESENCE TIER: founder to pick Design 1 (two-tier) vs Design 2
  (mitigated single-tier) from 3B. Recommendation: Design 1 + GPS-fence.
- Poster code: static printed code + server nonce, or rotating? (MVP: static +
  GPS fence; rotating deferred — 3B.)
- Free-event attendee presence: the poster scan IS the free-event presence
  signal (replaces the earlier "free tap-in" idea — simpler, organiser prints once).
- EVENT DURATION now required at creation — confirm the grace-window length and
  the organiser override/extend UX (3C). Affects Go Live + crowd meter too.
- Does the entrance scanner need ANY change, or is recognising the vendor
  credential and firing presence purely server-side? (Believed: server-side.)
- Vendor location: text-only at MVP; venue-map pin deferred with table-delivery.
- zuka-legal MUST confirm the vendor-direct money model before any Phase-2 build,
  and rule on DPPA obligations for order + vendor + attendance data (poster scans
  collect attendee presence → consent surface).
- EFRIS-ready vendor documents (3E): confirm what format Ugandan micro-vendors
  actually need — a zuka-legal + real-vendor question.
- In-flight order when the event closes → grace window (3C) covers it.
- Order dispute / never-collected → vendor owns it (Zuka isn't in the money).
```

---

## 11. Reality-Check Verdict — table-delivery (zuka-reality-checker pass)

```
VERDICT:  DEFER (table-delivery) · TRIM (ordering scope to collect-only at Phase 2)

WHY: "table-delivery" quietly bundles THREE unbuilt things —
  1. a table/seat-location entity (doesn't exist — the GatePlan gap),
  2. in-app ordering + payment (Phase 2, itself gated on legal),
  3. a fulfilment/runner model + Zuka's liability for wrong/late delivery
     (a support + trust surface that doesn't exist).
Bundled as one "delivery feature," these hide real cost and drag dependencies
forward.

EVIDENCE GAP: untested whether attendees even want order-to-seat, or whether
"see who's here + walk over" is 90% of the value at 10% of the build. Phase 1
answers this cheaply before ordering is built at all.

RECOMMENDATION: Ship Phase 1 (presence + discovery, collect-in-person). At
Phase 2, fulfilment is COLLECT-ONLY. Promote "deliver to my table" to its own
concept, activating only after BOTH Phase-2 ordering is proven AND the
table/seat-location concept is designed. Record it as a named future concept,
not a Phase-2 line item.
```

This verdict is why Ch 8's Phase 2 says "collect" and table-delivery is its own
deferred concept — the discipline is baked into the phasing, not bolted on.

---

## 12. Why This Lives Here, Not in the Bible

Per the founder's instruction: new feature ideas accumulate in concept documents
like this one, so the Bible stays stable and authoritative rather than churning.
This document is the design space. Only if/when the feature is ratified for build
do its canonical entities (Ch 7) graduate into the Bible via ADR — and even then,
just the locked canonical core, not this exploratory thinking.

```
Concept doc (here)     →  explore, brainstorm, design freely
ADR (if ratified)      →  graduate the canonical entities into Vol 04/14
Bible (Vol 04/14)      →  only the locked, governed result
```

---

> **In-Event Vendor Marketplace — Concept Document**
> Two routes to prove presence, one scanned key. The venue scans itself via the
> poster. Vendor-direct money, forgiving success-based fees, reusable menus that
> outlive the night. Live only while you're there — real only because you showed
> up. The city, alive — down to the snack stand.