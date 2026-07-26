# ZUKA — Concept Document
# Context Fusion & Intent Engine ("One QR, Infinite Experiences")

> **Type:** Feature Concept · **Tier:** 1 (the jewel of the V7 mining)
> **Status:** Proposed (pre-build) · **Authority:** Subordinate to ZukaBibleV4.
> Reconciles V7's Context OS · CFE · Intent Engine · ORS · COF · AXE into ONE
> coherent engine, mapped to V4. Feeds V4 via ADR; does not amend it.
> **Reconcile to:** Discovery (Vol 15), Experience (Vol 16), the Poster
> Acquisition Engine + Vendor Marketplace concepts. Terms per V4 Dictionary (Vol 03).

---

## 1. The Idea in One Paragraph

A single ZUKA QR, scanned by different people at different moments, should open
different experiences — because the platform fuses *who you are, when it is, where
you are, what phase the gathering is in, and what you've just done* into an
understanding of **what you're most likely trying to do right now**, and shows you
that. The QR carries almost nothing; the intelligence is server-side. The same
stage poster shows an organiser "crowd exit + vendor settlement" at closing time,
and an attendee "where's my ride" at the same moment. One physical anchor,
infinite contextual experiences.

---

## 2. Why This Is the Jewel

V7 contains ~22 operating systems, but this cluster is the one genuinely novel,
genuinely buildable, genuinely *ZUKA* idea — and it answers a question the
existing concepts already ask:

```
- The Poster Acquisition Engine asks: "what should a poster scan DO?"
- The Vendor Marketplace asks: "what happens when a vendor credential is scanned?"
- The Scanner asks: "what should this scan mean in this context?"

Context Fusion + Intent is the ENGINE that answers all three. It's the connective
intelligence beneath the scan-driven features we've already designed.
```

It also expresses V4's constitution beautifully: Invisible Sophistication (Vol 16
— deep inside, simple outside), Reality-First (the engine observes reality and
recommends, never dictates), and the live-system-as-brand (Vol 25).

---

## 3. Reconciliation: What's New vs What V4 Already Has

```
NEW (additive — the real contribution):
  - Context Fusion Engine (CFE): fuse many signals into one operational picture
  - Intent Engine: infer what the person is trying to DO (not just their role)
  - Operational Relevance Score (ORS): rank candidate operations continuously
  - "One QR, infinite experiences": the QR is a generic anchor; context decides

ALREADY IN V4 (reconcile, don't duplicate):
  - Ranking/relevance → Discovery & Attention Economy (Vol 15) already ranks
    things. CFE/ORS EXTENDS this from "rank events for discovery" to "rank
    operations for the current moment." Same family, new application.
  - Adaptive experience → Experience Constitution (Vol 16) already mandates
    progressive disclosure + adaptive depth. V7's AXE is the mechanism for it.
    Fold AXE into Vol 16's adaptive-UX principle, don't make a rival system.
  - Personalisation gating → DRL/CRL (Vol 05/07). The engine MUST obey it:
    no intent inference beyond what the data maturity permits.
```

So this concept proposes **one new engine** (CFE + Intent + ORS) feeding **two
existing V4 systems** (Discovery's ranking, Experience's adaptive UX), not a
parallel stack.

---

## 4. The Core Mechanism (reconciled & simplified)

V7 describes a long pipeline. Reconciled to V4 and stripped to its essence:

```
SIGNALS (what the platform observes)
  identity · role · time · gathering phase · location · recent activity ·
  permissions · pending operations · preferences
        ↓
CONTEXT FUSION (combine into one picture, with confidence + recency weighting)
        ↓
INTENT (what is this person most likely trying to do right now?)
        ↓
OPERATIONAL RELEVANCE SCORE (rank candidate operations for this moment)
        ↓
THE FEED (show the top few; hide the rest)
        ↓
ADAPTIVE UX (the surface gently reshapes — Vol 16's progressive disclosure)
```

### The one-QR principle (reconciled with the Poster + Smart Bucket concepts)
```
The QR/poster is a STABLE PHYSICAL ANCHOR carrying almost nothing (an ID + a
signature). It does NOT encode the experience. The server, given the scan +
the fused context, decides what to show. This is already the posture of:
  - the Poster Acquisition Engine (a poster opens different things for user vs
    non-user, entrance vs bar zone)
  - the Smart Bucket scan (Vol 14: the scanner asks "does this satisfy my
    context?", the bucket answers minimally)
Context Fusion generalises that pattern to EVERY scan.
```

---

## 5. The Worked Example (V7's best illustration, reconciled)

Same stage poster, four moments — this is the feature's "wow":

```
SCANNER       MOMENT            FUSED INTENT        TOP OPERATIONS SHOWN
─────────────────────────────────────────────────────────────────────────────
Organiser     1h before start   Pre-event prep      sound check · staff check-in ·
                                                     gate readiness
Organiser     mid-gathering      Live operations     attendance · queue status ·
                                                     vendor activity
Organiser     30m before end     Closing/wrap-up      final announce · crowd exit ·
                                                     vendor settlement
Attendee      same closing       Departing            find my ride · rate the night ·
                                                     save my memory

One physical poster. The intelligence is entirely server-side. Nobody printed
four posters; the context engine produced four experiences.
```

### Activity decay & negative relevance (the suppression detail)
A completed activity should not just rank low — it should be actively *suppressed*,
then fade:
```
EXAMPLE: the organiser ordered snacks 2 hours ago, delivered & completed.
  → "Order food" is not merely low-ranked; it is SUPPRESSED as operationally
     irrelevant (the need is already met).
  → It may briefly surface a DIFFERENT, lighter follow-up ("rate Sarah Snacks
     Point?") at low weight — or nothing.
  → Its influence DECAYS over time: ~4% at 120 min, 0% by ~6 hours, after which
     it affects nothing.

So the engine models two things, not one: RELEVANCE (rank up what fits the
moment) AND DECAY/SUPPRESSION (rank down or hide what's already done). Both are
needed — without suppression, a finished task keeps cluttering the feed.
```

---

## 6. The Hard Constraints (where V7 must bend to V4)

V7's pipeline is ambitious; V4's constitution disciplines it:

```
DRL/CRL GATING (non-negotiable, Vol 05/07):
  Intent inference is a PREDICTION. At DRL 1 (collection) the engine may NOT
  infer intent from learned patterns — it may only use HARD signals (role, time,
  explicit phase). Learned/probabilistic intent requires DRL 2+. So at MVP the
  "engine" is mostly RULE-BASED (role + time + phase → sensible operations), and
  becomes genuinely inferential only as real data accrues. This is the honest
  sequencing V7 omits.

REALITY INFORMS, NEVER DICTATES (Vol 01 + V7 Principle 17 — they agree):
  The engine RECOMMENDS the top operations; the person can always switch
  experience / see everything. Never trap someone in an assumed intent. An
  "experience switcher" + "show all" is mandatory.

INVISIBLE SOPHISTICATION (Vol 16):
  No "AI-powered" labels. The person just sees the right things. The fusion is
  felt, never advertised.

NO DARK PATTERNS (Vol 16):
  Relevance ranking must not be hijacked to push promoted operations. The feed
  serves the person's intent, not ZUKA's monetisation. (Distinct from the capped,
  labelled sponsored slots in Vol 15.)

EXPLAINABILITY (ADR-007):
  Any non-obvious surfaced operation can answer "why am I seeing this?" — the
  fused signals that produced it. Especially once intent is inferred (DRL 2+).
```

---

## 7. What It Upgrades in the Existing Concepts

```
POSTER ACQUISITION ENGINE: the "what does this scan open?" logic IS context
  fusion. The zone idea (entrance/bar/stage) is a spatial signal into the CFE.
  → fold the engine in as the poster's brain.

VENDOR MARKETPLACE: a vendor credential scanned at the gate → context fusion
  knows (vendor role + entrance + event live) → activates the vendor console
  experience, not the attendee one. Same scan, role-fused result.

SCANNER PRO (next concept): the capability-gated scanner is the OPERATOR side of
  the same engine — what a staff scan is *allowed to do* is a permission signal
  in the fusion.
```

---

## 8. New Concepts This Introduces (for eventual ADR, NOT yet canonical)

```
ENTITIES (proposed):
  ContextObject      the fused snapshot at a moment (identity, spatial, temporal,
                     gathering, operational, activity, preference signals +
                     confidence/recency per signal).
  IntentScore        (context_id, intent_label, score) — ranked candidate intents.
  OperationCandidate a possible operation + its live ORS for this context.
  ExperienceVector   weighted blend of active experience modes (for adaptive UX,
                     Vol 16) — "closing 0.82, departing 0.06, …".

EVENTS (append-only, ADR-015):
  ContextResolved · IntentInferred · OperationsFeedServed ·
  ExperienceSwitched (user override)

REUSED: Discovery ranking (Vol 15), the adaptive-UX principle (Vol 16), DRL/CRL
gating (Vol 05/07), the scan/QR anchors (Vol 14 + Poster concept).
```

---

## 9. MVP Cut Lines (reality-checker to ratify)

```
PHASE 1 — RULE-BASED CONTEXT (the honest MVP)
  Fuse only HARD signals (role + time + gathering phase + explicit location).
  A rule-based "feed" shows sensible operations per role/phase. No learned
  inference (DRL 1 forbids it). This alone delivers most of the "right things at
  the right time" magic, with zero ML.

PHASE 2 — INFERRED INTENT (needs DRL 2+)
  Once real participation data exists, add probabilistic intent + activity decay
  + preference weighting. The engine gets genuinely smart.

PHASE 3 — ADAPTIVE UX BLENDING (AXE)
  The continuous experience-vector blending of Vol 16's adaptive UX. Polish, not
  prerequisite.

DORMANT: the full 12-signal fusion, momentum modelling, Level-3 immersive
  theme/typography adaptation — V7's deep end. Park until the basics prove out.
```

---

## 10. Open Questions

```
- Where does the engine run? Believed: a Supabase Edge Function computing the
  feed server-side from the ContextObject (in-stack, no drift). Confirm.
- How many operations show? V7 says "top few" — confirm the number for a 2GB
  phone on 3G (likely 5-8).
- Rule-based Phase 1: who authors the role×phase→operations rules? (Founder /
  product, encoded as data, not hardcoded.)
- DRL line: exact boundary between "hard-signal rules" (allowed at DRL 1) and
  "inferred intent" (DRL 2+) — zuka-backend + the DRL framework to pin down.
- Explainability UX: how does "why am I seeing this?" surface without clutter?
```

---

> **Context Fusion & Intent Engine — Concept Document (Tier 1, the jewel)**
> One QR, infinite experiences. The platform infers what you're trying to do and
> shows you that. Rule-based first, inferential when the data earns it. The
> intelligence is invisible; only the rightness is felt.