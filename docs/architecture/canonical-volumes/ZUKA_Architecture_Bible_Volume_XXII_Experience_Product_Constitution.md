# ZUKA Architecture Bible
# Volume XXII — Experience & Product Constitution

> **Series:** ZUKA Architecture Bible
> **Volume:** XXII of N
> **Status:** Canonical — the constitutional rules governing ZUKA's user-facing
>              surface; the product layer's equivalent of Volume I's vision
> **Depends on:** Volume I (Vision), Volume III (Identity), Volume IV (Gatherings),
>                  Volume V (Memory), Volume XIV (CRL/Invisible Sophistication),
>                  Volume XXI (Ticketing)
> **Required by:** Every design decision, every notification, every UI surface
>                  ZUKA builds — this volume is the constitution for the product layer

---

# Preface

This Bible has specified thirteen domains, seven graphs, six Twin types, five Agents, three governance layers, and a complete anti-fraud architecture. It has specified almost nothing about what the person who picks up their phone actually experiences.

This volume corrects that. It is not a design system, a wireframe, or a feature specification — it is the **constitutional rules** that govern what ZUKA's product surface is permitted to do and prohibited from doing. These rules are as binding as the data architecture rules in Volume VII or the consent architecture in Volume IX, and they are derived from exactly the same source: ZUKA's founding commitments, stated in Volume I, applied to the experience layer.

---

# Part I — The Three Constitutional Proofs

---

## Chapter 1 — The MVP Proofs Are Not Aspirations

These three statements from ZUKA.md are not aspirational marketing copy. They are **verifiable, testable criteria** that define whether ZUKA has succeeded at its most basic product promise. This volume makes them testable by specifying what "counts" for each.

```
PROOF 1 — A VENUE CAN GO LIVE AND BE DISCOVERABLE
           WITHIN 60 SECONDS

  Starts: organiser taps "Go Live"
  Ends:   the venue's pin is visible on a different
          device's discovery feed in the same city
  Passes when: elapsed time ≤ 60 seconds, measured
               from tap to pin visible, under normal
               network conditions (3G or better)

  What "discoverable" means:
    - The gathering appears in the Live Now section
      of the discovery feed (ZUKA.md §5)
    - The location pin is rendered on the heat map
    - A search for the venue's name or type returns it

  Blocking conditions that would fail this proof:
    - Requiring organiser profile completion before
      Go Live is accessible
    - Requiring venue verification before the first
      Go Live (verification can happen in parallel)
    - Any multi-step form between "Go Live" tap and
      the gathering being published

PROOF 2 — A PERSON CAN FIND SOMETHING HAPPENING NEAR
           THEM RIGHT NOW WITH ZERO FRICTION

  Starts: person opens the ZUKA app for the first time
  Ends:   they can see at least one live or soon event
          relevant to their location
  Passes when: time from app open to first relevant
               event visible = 0 additional actions
               (no signup required to browse)

  What "zero friction" means:
    - The discovery feed loads WITHOUT login or signup
    - Location is requested ONCE, not per-session
    - No paywall, no "create an account to see more"
      modal interrupts browsing
    - Filter and search work without an account

  Blocking conditions that would fail this proof:
    - Login wall before any discovery content is shown
    - Mandatory onboarding flow before feed is visible
    - Age gate or location gate that hides content
      without explanation

PROOF 3 — A TICKET CAN BE BOUGHT IN UNDER 3 TAPS

  Starts: person is looking at a gathering's detail view
  Ends:   ticket is confirmed and in their possession
  Passes when: tap count from detail view to confirmed
               ticket ≤ 3, measured on a returning user
               with a saved payment method

  The three taps:
    1. Tap "Buy Ticket" (or specific tier)
    2. Tap "Pay with [saved method]" (no re-entry)
    3. Tap "Confirm" → TicketPurchased fires

  For first-time users (no saved payment):
    The 3-tap target applies to returning users.
    First purchase may have additional steps for
    payment method setup — but ONLY once.

  Blocking conditions that would fail this proof:
    - Requiring re-entry of payment details on every
      purchase (saved payment method is mandatory UX)
    - Any interstitial ("are you sure?") between
      Confirm and TicketPurchased
    - OTP verification on every purchase (OTP is for
      account creation, not every transaction)
```

---

# Part II — The Invisible Sophistication Principle

---

## Chapter 2 — Adoption as Constitutional Law

Volume XIV's source material introduced "Invisible Sophistication" but it was not adopted into the Bible at that point. This volume adopts it in full, as a constitutional principle governing every product and engineering decision.

```
PRINCIPLE — INVISIBLE SOPHISTICATION

  The more sophisticated ZUKA becomes internally, the
  simpler it must become externally.

  OPERATIONALISED AS FOUR RULES:

  RULE 1 — 3-5 TAPS MAXIMUM FOR ANY CORE ACTION
    Core actions: attend (RSVP), buy (ticket), go live,
    share, create a memory.
    If a core action requires more than 5 taps from
    any entry point, it is a product defect, not a
    feature.

  RULE 2 — MOST ACTIONS COMPLETE IN UNDER 30 SECONDS
    This includes all MVP core actions. If an action
    (excluding payment processing wait time, which is
    external) takes more than 30 seconds on a 3G
    connection, it is a performance defect.

  RULE 3 — AI IS INVISIBLE
    No feature in ZUKA's consumer surface announces
    itself as "AI-powered," references its DRL level,
    or asks the person to interact with intelligence
    infrastructure directly. AI capabilities surface
    as natural product outcomes:
      - "You might like this" (not "Our AI detected...")
      - "People you know are going" (not "Our matching
        algorithm identified...")
      - A gathering is suggested (not "DRL 3 Opportunity
        Intelligence recommends...")
    The intelligence layer (Volumes IX-X) is entirely
    invisible to the person using the product.

  RULE 4 — PROGRESSIVE DISCLOSURE
    The product surfaces only what the person needs
    for the immediate action. Deeper information, settings,
    and capabilities are revealed progressively as the
    person asks for them.
    The inverse — showing all available information at
    once — is prohibited. ZUKA's architecture has enormous
    depth; the product hides that depth unless the person
    specifically seeks it.
```

---

# Part III — The Notification Constitution

---

## Chapter 3 — What ZUKA May Notify

ZUKA is permitted to send notifications in exactly six categories, and no others:

```
PERMITTED NOTIFICATION CATEGORIES:

  CATEGORY 1 — TRANSACTIONAL (always permitted, non-configurable)
    Ticket purchase confirmations
    Ticket transfer notifications
    Gift ticket received/accepted/declined
    Payment processed / payout sent
    Check-in confirmation
    These notifications confirm an action the person
    took or that directly affects their property (ticket,
    money). They cannot be disabled.

  CATEGORY 2 — SAFETY-CRITICAL (always permitted, non-configurable)
    Account security: new device login, OTP messages
    Trust alerts: account under review (Vol XIII Part V)
    Legal and data requests affecting the person
    These cannot be disabled — they protect the person.

  CATEGORY 3 — GATHERING REMINDERS (configurable: on/off)
    Event starting in 1 hour / starting now
    "Doors open" reminder
    Post-event memory creation prompt (Ch.9, Vol XXI)
    Default: ON. Person may disable per notification
    settings.

  CATEGORY 4 — SOCIAL (configurable: granular)
    Someone tagged the person in a Memory
    A friend is going to a gathering near them
    A Community the person belongs to announced an event
    Default: ON for communities; OFF for broad social
    (follows Volume III Part VI's consent-forward approach —
    social notifications default to a narrower scope)

  CATEGORY 5 — DISCOVERY (configurable: on/off/frequency)
    "Something is happening near you right now"
    City Pulse notification (live count crosses a threshold)
    These are the only notifications that ZUKA proactively
    surfaces without a specific person-triggered event.
    Default: ON, max 2 per day.
    Frequency configurable: immediate / daily digest / off.

  CATEGORY 6 — ANNIVERSARY (configurable: on/off/sensitivity)
    "On this day X years ago" — Volume V's Anniversary Engine
    outputs, governed by Volume V Ch.21's mandatory
    sensitivity rules.
    Default: ON.
    Sensitivity configurable: all memories / joyful only /
    off.
    The sensitivity rules from Volume V Ch.21 apply
    unconditionally regardless of this setting — grief-tagged
    memories never receive celebratory framing even if
    "all memories" is selected.

PROHIBITED NOTIFICATIONS (never permitted regardless of
user settings or business reason):

  - Engagement pressure: "You haven't opened ZUKA in 3 days"
  - Scarcity manipulation: "Only 2 tickets left!" as a push
    notification (may appear in-product; never as a push)
  - Social pressure: "Your friend just bought a ticket" sent
    without that friend's explicit share action
  - Re-engagement following mental-health-sensitive signals:
    if a person's Memory data contains recent grief-tagged
    entries and they have been inactive, ZUKA does not
    send a "come back" notification
```

---

## Chapter 4 — Notification Timing Rules

```
TIMING RULES:

  RULE 1 — NO ENGAGEMENT-OPTIMISED TIMING
    Notification delivery times are never determined by
    ML-predicted "highest open rate" hour. This would mean
    timing notifications to the person's most psychologically
    susceptible moment — a dark pattern this Bible explicitly
    rejects (Vol V Ch.21's "no engagement-optimisation logic"
    for Anniversary notifications, extended here to all
    notification types).

  RULE 2 — RESPECT QUIET HOURS
    No Category 3, 4, 5, or 6 notifications are sent
    between 10:00 PM and 7:00 AM local time.
    Categories 1 and 2 (transactional and safety) may
    be sent at any time — the person needs to know about
    their ticket or their account regardless of the hour.

  RULE 3 — BATCHING OVER FLOODING
    If multiple Category 4 or 5 notifications would be
    sent within a 30-minute window, they are batched
    into one notification summary, not sent individually.

  RULE 4 — GRIEF-SENSITIVE TIMING
    Category 6 (Anniversary) notifications for memories
    carrying grief or sadness as primary emotion are sent
    only in the afternoon (2:00 PM - 5:00 PM local time),
    never as a morning notification. Per Volume V Ch.21,
    joyful morning timing is reserved for joyful content.
```

---

# Part IV — The Anti-Dark-Pattern Constitution

---

## Chapter 5 — Prohibited UX Patterns

These patterns are explicitly prohibited across all surfaces ZUKA controls. They are not prohibited because they are illegal — many are not — but because they contradict the founding commitments of Volume I and the consent architecture of Volume III.

```
PROHIBITED PATTERNS:

  P1 — FABRICATED SOCIAL PROOF
    "127 people are viewing this event right now" where
    the number is estimated, approximate, or server-computed
    rather than reflecting actual simultaneously-viewing
    users. Real-time crowd signals (heat map, crowd meter)
    are permitted because they reflect genuine attendance
    data. Fabricated urgency is not.

  P2 — INVOLUNTARY SOCIAL SHARING
    No action the person takes on their behalf (RSVP, check-in,
    Memory creation) is shared to their network without an
    explicit, per-action share choice. Default is private.
    Volume III Part VI's visibility model governs — the
    product must make that model legible to the person,
    not invisible.

  P3 — ALGORITHMIC MANIPULATION OF EMOTIONAL STATE
    Volume V Ch.9's principle ("emotion should be modelled,
    not manipulated, understood") extended to every surface:
    ZUKA does not use emotional data (grief tags, importance
    levels, Memory Capital signals) to manufacture urgency,
    target distress-adjacent advertising, or time content
    to capitalise on emotional vulnerability.

  P4 — CONSENT DARK PATTERNS
    Pre-ticked data sharing boxes are prohibited.
    Consent flows that use confusing double-negatives
    ("Uncheck to not share your data") are prohibited.
    Consent that expires silently and renews automatically
    without notice is prohibited.
    Volume IX's TwinConsentRecord (revocable, explicit,
    never bundled) is the model for all consent flows.

  P5 — ARTIFICIAL SCARCITY / COUNTDOWN TIMERS
    Countdown timers on ticket prices or availability are
    permitted ONLY if they reflect genuine external constraints
    (the actual sale close time, the actual remaining capacity).
    Countdown timers manufactured purely for conversion pressure
    — "Offer ends in 4:32" on a price that renews automatically
    — are prohibited.

  P6 — DELETION OBSTACLES
    Account deletion and data export (Vol XII Ch.11) must
    be reachable within 3 taps from the settings menu.
    No "are you sure? your memories will be lost" guilt-
    trip interstitials are permitted — the platform must
    inform the person what happens to their data on deletion
    (consistent with the LegacySettings model, Vol III Part X)
    but must not weaponise attachment to prevent deletion.

  P7 — ADDICTION-PATTERN DESIGN
    Infinite scroll is prohibited for the Memory Vault and
    Community Archive (these are archival surfaces, not feeds).
    Variable-ratio reward schedules (unpredictable reward
    timing to maximise engagement) are prohibited on any
    ZUKA surface — the platform's value compounds through
    genuine use, not through engineered compulsion.

  P8 — PRICING OBFUSCATION
    The full ticket price — including all platform fees and
    processing charges — must be displayed before the
    person is asked to commit to purchase. "Fees added at
    checkout" patterns that reveal the full price only at
    the confirmation step are prohibited.
    Per the three-tap proof (Chapter 1): if showing the
    full price requires an additional tap, that tap is
    worth it and is not counted against the 3-tap limit.
```

---

# Part V — The Accessibility Baseline

---

## Chapter 6 — African Market Context Requirements

ZUKA launches in Kampala and expands across East Africa. The accessibility baseline must reflect the actual device and network environment of this market, not a Silicon Valley design standard.

```
TARGET DEVICE BASELINE:
  Processor: equivalent to Snapdragon 450 (entry-level 2019)
  RAM: 2GB
  Storage: 16GB (ZUKA app target: ≤ 50MB installed)
  Screen: 5.0" to 6.5", 720p to 1080p
  Android version: 8.0+ (API 26+)
  Battery: 3000mAh (assume 60-70% charge typical)

TARGET NETWORK BASELINE:
  Primary: 4G LTE at variable signal (1-10 Mbps)
  Degraded: 3G (0.3-1 Mbps)
  Worst case: 2G EDGE or intermittent connectivity
  All core flows must complete on 3G.
  All core flows must DEGRADE GRACEFULLY on 2G
    (show cached content, queue actions for later sync,
    never crash or show a blank screen).

MINIMUM ACCESSIBILITY REQUIREMENTS:
  WCAG AA contrast ratios on all text and interactive
    elements — many entry-level displays have lower
    colour accuracy; AA contrast is the minimum.
  Touch targets: minimum 44x44dp for all interactive
    elements — many target users are using devices
    while in motion or in variable light conditions.
  Text size: never below 14sp for body text; all text
    must be legible without user magnification.
  Offline mode: every screen the user has visited
    must render from cache when offline. "No connection"
    blank screens are a product defect.
  SMS fallback: every critical notification sent via
    FCM push (Vol XIX Ch.7) must also be sendable via
    Africa's Talking SMS as a fallback when push
    delivery fails.

DATA EFFICIENCY REQUIREMENTS:
  Images: served via Cloudflare CDN (Vol XIX Ch.8)
    with automatic format conversion (WebP where
    supported) and responsive sizing — a thumbnail
    in a list view must not download at full resolution.
  Video: never auto-plays over mobile data.
    Auto-play is permitted on Wi-Fi only, and must
    be configurable.
  Bundle size: the Flutter app must render the
    discovery feed within 3 seconds on a 3G connection.
    Core data (gatherings near the user) must be
    ≤ 50KB of JSON per page of results.

LANGUAGE:
  English is the primary language of the application
    at MVP. Luganda and Swahili are V1.1 priorities
    for East African market expansion.
  All user-visible text must be translatable (no
    hardcoded strings in UI code).
```

---

# Part VI — The Product Governance Rules

---

## Chapter 7 — What Requires Founder Sign-Off

Per the Founder Rule (Volume XI, Chapter 13) applied to the product layer:

```
PRODUCT DECISIONS REQUIRING FOUNDER SIGN-OFF:

  Any change to the Three Constitutional Proofs (Chapter 1)
    — relaxing the 3-tap, 60-second, or zero-friction
    criteria requires a documented founder decision,
    not an engineering or product manager decision

  Any new notification category beyond the six defined
    in Chapter 3 — new notification types are product
    decisions with real user impact; they require the
    same founder-level governance as capability activations

  Any exception to the Anti-Dark-Pattern Constitution
    (Chapter 5) — there are no exceptions to the listed
    prohibitions without a documented founder decision
    and a documented reason. "It will increase conversion"
    is not a sufficient reason; the prohibitions exist
    precisely because some dark patterns do increase
    conversion.

  Monetisation model changes
    — Any new revenue mechanism (beyond ticketing
    commission, venue badges, boosts as specified in
    ZUKA.md's revenue model) requires founder sign-off
    before product build begins. Revenue mechanisms
    that require dark-pattern UX to generate revenue
    are doubly prohibited — by this chapter and by
    the Anti-Dark-Pattern Constitution.

  Consent architecture changes
    — Any change to the consent flows, privacy settings,
    or data export mechanisms (Vol XII Ch.11) requires
    founder sign-off and a Privacy Impact Assessment
    (Vol XVIII Ch.10) before implementation.
```

---

## Chapter 8 — What the Product Team Owns Without Sign-Off

```
PRODUCT TEAM AUTHORITY (no founder sign-off required):

  Visual design decisions within the brand system
    (colours, typography, spacing, illustration style)
    — as long as they maintain WCAG AA contrast (Ch.6)
    and don't introduce new notification categories
    or consent patterns

  Copy and tone within the notification constitution
    (Chapter 3) — the product team owns the specific
    wording of any permitted notification; the
    prohibition list (Chapter 3's prohibited patterns)
    constrains what can be said, not how it is said

  Feature sequencing within already-approved capabilities
    — once a capability has cleared its DRL Activation
    Contract (Vol XX Ch.10), the product team decides
    how and when to surface it; founder sign-off was
    given at the DRL gate, not per-surface

  A/B tests that do not test against constitutional rules
    — testing two different on-boarding screen designs
    is within product team authority; testing "full-friction
    login wall" vs. "no login wall" (which would test
    against Constitutional Proof 2) is not
```

---

# Part VII — MVP Scope

---

## Chapter 9 — What to Build First

```
BUILD (MVP):

  ✅ Verify all three Constitutional Proofs before any
     public launch — these are pass/fail criteria that
     must be measurably met, not aspirational targets
     to be evaluated later

  ✅ The six notification categories (Ch.3) as the
     complete notification taxonomy; no additional
     categories may be added without founder sign-off
     per Chapter 7

  ✅ Notification timing rules (Ch.4) — quiet hours
     and batch suppression implemented before the
     first notification is sent; the temptation to
     "add this later" must be resisted because the
     first engagement-optimised notification sent
     establishes a precedent

  ✅ Anti-dark-pattern checks (Ch.5) in the design
     review process — as a literal checklist (P1-P8)
     that every UI design passes before engineering
     begins; this costs nothing to add to process now
     and is expensive to retrofit in culture later

  ✅ Offline graceful degradation (Ch.6) — every
     screen renders from cache; tested explicitly on
     a simulated 2G connection before launch; "works
     on 3G" is necessary but not sufficient

  ✅ Full ticket price display before confirmation
     (P8, Ch.5) — non-negotiable; the commerce
     integrity rules (Vol XXI Ch.10) and this chapter
     align on transparency
```

```
DO NOT BUILD YET:

  ❌ Luganda / Swahili localisation — V1.1 priority;
     architecture must support it (no hardcoded strings)
     but the translation itself is deferred

  ❌ Category 6 Anniversary notifications — the Anniversary
     Engine (Vol V Ch.21) itself is Dormant (DRL gated);
     the notification infrastructure for it should exist
     in the notification system, but it fires no content
     until the Engine is activated

  ❌ Any ZUKA-initiated push beyond Category 5 Discovery
     (the most minimal proactive notification) in the
     first 30 days post-launch — building trust before
     pushing content is better than sending notifications
     from day one and training users to ignore them
```

---

# Volume XXII Summary

```
The Three Constitutional Proofs:
  60-second Go Live, zero-friction discovery, 3-tap
  ticket purchase — each with explicit testable criteria,
  starting conditions, ending conditions, and what would
  constitute failure
  These are NOT aspirations; they are product pass/fail
  criteria enforceable in code and measured in production

The Invisible Sophistication Principle:
  Adopted as constitutional law — 4 rules: max 3-5 taps,
  max 30 seconds, AI invisible, progressive disclosure
  The intelligence layer (Volumes IX-X) must never surface
  as jargon or infrastructure in the product

The Notification Constitution:
  6 permitted categories (Transactional, Safety-Critical,
  Gathering Reminders, Social, Discovery, Anniversary) —
  Categories 1-2 non-configurable; 3-6 user-configurable
  8 prohibited patterns with explicit rationale for each
  4 timing rules (no engagement-optimised timing, quiet
  hours, batching, grief-sensitive timing)

The Anti-Dark-Pattern Constitution:
  8 named, explicitly prohibited UX patterns (P1-P8)
  covering: fabricated social proof, involuntary sharing,
  emotional manipulation, consent dark patterns, artificial
  scarcity, deletion obstacles, addiction-pattern design,
  pricing obfuscation
  No exceptions without documented founder decision

The Accessibility Baseline:
  Defined against the actual device and network environment
  of the African market (Snapdragon 450, 2GB RAM, 3G primary,
  2G graceful degradation), not a Silicon Valley baseline
  WCAG AA contrast, 44dp touch targets, offline mode
  mandatory, 3-second feed load on 3G, ≤ 50MB app size

Product Governance:
  5 categories of decisions requiring founder sign-off
  (Three Proofs changes, new notification categories,
  dark-pattern exceptions, monetisation changes, consent
  architecture changes)
  Clear delineation of what the product team owns without
  sign-off (visual design, copy, feature sequencing within
  approved capabilities)

MVP Scope:
  5 build items (proof verification, notification categories,
  timing rules, dark-pattern checklist, offline degradation)
  3 explicit deferrals (localisation, Anniversary notifications,
  proactive push beyond Discovery in first 30 days)
```

This volume closes the consumer-facing specification gap. Every product decision ZUKA now makes has a constitutional framework to check against — not as bureaucracy, but as the product expression of the same commitments this Bible has held architecturally since Volume I.

---

> **ZUKA Architecture Bible**
> Volume XXII — Experience & Product Constitution
> Built in Kampala. Built for Africa. Built for the world.
