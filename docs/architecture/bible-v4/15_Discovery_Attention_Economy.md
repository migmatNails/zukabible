# ZUKA BIBLE V4 — VOLUME 15
# Discovery, Ranking & The Attention Economy

> **Part IV — Experience & Commerce** · **Authority Level 3** · **Crown Jewel**
> Synthesises: BLACK Edition XXXV + ZUKA.md §20 ranking + fresh research
>   (fairness-of-exposure: Singh & Joachims; minimum-exposure constraints;
>    manifold re-ranking SIGIR'26; optimal traffic allocation)
> Status: This domain was ENTIRELY ABSENT from prior V4. Named as the #1 gap
>   by the Architecture Audit (Finding 10).

---

## Chapter 1 — Why Attention Is Constitutional

Attention is the scarcest resource in the ZUKA ecosystem. Ticket sales, community
growth, trust formation, and opportunities all begin with visibility. The question
"what deserves attention?" is not a tuning parameter — it is a governance decision that
shapes the entire ecosystem's health.

```
Bad attention systems create manipulation.
Good attention systems create opportunity.
```

This volume IS the "Attention Constitution" the Reconciliation Audit named as missing.

---

## Chapter 2 — The Attention Constitution

```
MERIT BEFORE MONEY
  Ranking prioritises relevance, quality, trust, and participation BEFORE
  advertising spend. Paid promotion may amplify visibility; it must never
  replace merit. (Mirrors the ZUKA.md boost cap: money increases visibility
  but cannot buy the top result outright.)

DIVERSITY OF EXPOSURE
  Avoid winner-take-all ecosystems. Provide discovery opportunities to smaller
  organisers. (Grounded in fairness-of-exposure research: a small group of top
  performers otherwise captures disproportionate visibility, starving the
  long tail.)

ANTI-MONOPOLY EXPOSURE
  No organiser permanently dominates attention. Enforced via minimum-exposure
  constraints and exposure-fairness re-ranking.

EXPLAINABLE DISCOVERY
  Every recommendation has explainable signals. (ADR-007 applies to ranking.)
```

---

## Chapter 3 — The Discovery Engine

```
INPUTS (six signal families):
  Identity Signals · Relationship Signals · Memory Signals ·
  Trust Signals · Community Signals · Context Signals (time, location, device)

OUTPUTS:
  Event Recommendations · Venue Recommendations ·
  Community Recommendations · Opportunity Recommendations
```

---

## Chapter 4 — The Six Ranking Dimensions

```
1. RELEVANCE       Category/vibe match to the person's real taste
2. TRUST           Organiser and venue trust (Volume 11)
3. QUALITY         Review ratings, verification, historical performance
4. FRESHNESS       How soon / how live (live-now beats next-week)
5. PARTICIPATION   Real attendance signals (not views or likes)
6. LOCALITY        Proximity and neighbourhood relevance
```

---

## Chapter 5 — The Composite Ranking Model (from ZUKA.md §20, generalised)

```
score = relevance   × 0.30
      + proximity   × 0.25
      + recency     × 0.20
      + social      × 0.15   (friend attendance, follows)
      + quality     × 0.10   (verification, reviews)
      + boost_modifier       (capped — see Chapter 8)

The boost cap is the anti-drain principle: a paid boost can raise an item at
most 2 positions and can NEVER displace the organic top 3 by more than that.
Money amplifies; it does not purchase the summit.
```

---

## Chapter 6 — Per-Type Ranking Models

```
EVENT RANKING:     ticket velocity · attendance history · organiser trust ·
                   venue quality · community interest
VENUE RANKING:     reliability · attendance · reviews · historical performance
COMMUNITY RANKING: participation · retention · trust · continuity
```

---

## Chapter 7 — The Attention Allocation Model (fairness-of-exposure, research-grounded)

This is the heart of the volume, and the part with no precedent in prior layers. It
operationalises the fairness-of-exposure literature:

```
RESERVE a minimum share of visibility for:
  Emerging Organisers · New Communities · New Venues

MECHANISM (minimum-exposure constraints):
  The ranker imposes a floor — comparable new-but-promising items receive a
  guaranteed minimum exposure share, even when established items would otherwise
  capture all visibility. This mirrors "minimum exposure or share constraints"
  and fair re-ranking that promotes long-tail items while preserving overall
  relevance.

WHY: Without this, the rich-get-richer dynamic (high CTR → more exposure → more
  CTR) permanently starves new organisers, and the ecosystem stagnates. A
  marketplace where a small group captures traffic at low cost while the
  majority is invisible is an unhealthy marketplace. The floor prevents
  stagnation without sacrificing relevance for the user.

TENSION ACKNOWLEDGED: there is a real accuracy-vs-fairness trade-off. Equally
  treating all groups can reduce short-term relevance. ZUKA accepts a bounded,
  explainable relevance cost in exchange for long-term ecosystem health — a
  deliberate, governed choice, reviewed by the Architecture Council.
```

---

## Chapter 8 — Sponsored Content Constitution

```
Sponsored content must be:
  CLEARLY LABELED          Never disguised as organic
  SEPARATED               From merit ranking (a distinct, capped slot)
  LIMITED IN INFLUENCE     Ad load capped at 1 promoted per 10 organic
                          (the ZUKA.md anti-drain rule)
```

---

## Chapter 9 — Attention Abuse Protection

```
Protect against:
  Click Farms · Engagement Farms · Review Manipulation · Ranking Manipulation

Detection shares the Trust OS anti-fraud substrate (Volume 11): velocity
anomalies, graph topology anomalies, content pattern matching. Ranking signals
derived from suspected-fraudulent engagement are discounted before ranking.
```

---

## Chapter 10 — Empty State Design (ZUKA.md §20)

```
ZUKA never shows a blank empty state. If nothing matches:
  Show nearest matching results with the most restrictive filter relaxed
  Show what's happening in the nearest active neighbourhood
  "Nothing live in Ntinda right now. Kabalagala is 2km away and has 8 spots."
```

---

## Chapter 11 — Discovery DRL / CRL

```
DRL: 0 Rules · 1 Signals · 2 Ranking · 3 Personalization ·
     4 Intelligence · 5 Adaptive Discovery
CRL: Identity · Community · City · Country · Platform

At MVP, discovery operates at DRL 1-2 (rules + basic ranking). Personalisation
(DRL 3) activates only after a person has 3-5 real check-ins — taste is learned
from participation, never assumed.
```

---

## Chapter 12 — The Personalised Pulse (ZUKA.md §5)

```
After 3-5 check-ins, the feed learns from REAL behaviour:
  Which categories the person actually attends (not just views)
  Which neighbourhoods they travel to
  Which price points they transact at
  Which vibe tags match their history
  Which day/time patterns they follow

Irrelevant categories recede; relevant ones amplify. The app becomes
increasingly irreplaceable as it learns — but only from participation, never
from declared preference alone. (Reality Doctrine.)
```

---

## Chapter 13 — Founder Rules

```
1. Attention is a public resource.
2. Discovery must remain fair.
3. Quality should outperform manipulation.
4. Diversity creates healthier ecosystems.
5. Visibility should create opportunity.
```

---

> **Volume 15 — Discovery, Ranking & The Attention Economy** · Crown Jewel
> The health of the ecosystem depends on how attention is allocated.
