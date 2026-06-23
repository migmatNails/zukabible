# ZUKA Trust Engine Specification

**Version:** 1.0  
**Status:** Active — MVP Phase 1 (BRL1 · DRL0 · CRL-0)  
**Authority:** Book VII (Trust and Opportunity Systems), Book XII (Governance), `ZUKA_Readiness_Constitution.md`  
**Readiness Gate:** BRL4 · DRL3 · CRL-0 required for controlled production (the Trust Engine *observes and computes*; it does not act autonomously on participants)  
**Companion schemas:** `ZUKA_Canonical_Schema_Pack.md` → `trust_scores`, `trust_signals`  
**Companion events:** `ZUKA_Event_Catalog.md` → `TRUST.TrustScore.recomputed`  

---

## Constitutional Position

Trust is not a feature. Trust is the operating condition that makes everything else possible.

From the Constitutional Glossary: *"Trust: Confidence earned through participation."*

The Trust Engine translates raw participation signals into a computed trust state per participant. That state governs what capabilities are unlocked, what weight a participant's actions carry in platform decisions, and what level of platform resources (visibility, payouts, scanner access) they can access.

**Three constitutional constraints govern the Trust Engine permanently:**

1. **Trust is internal.** The numeric score is never exposed to participants or organisers. Only human-readable tier labels are surfaced where necessary, and only where they add participant value (e.g. "Verified Venue" badge).

2. **Trust is earned through participation, not purchased.** There is no mechanism by which a participant can pay to increase their trust score. Verification fees (for document review) cover operational cost, not score manipulation.

3. **Trust scarcity is preserved.** The score model is calibrated so that the highest tiers require sustained positive participation over time — not a single action. This prevents score inflation and maintains the signal value of high-trust status.

---

## Architecture Overview

```
Participation event occurs
        ↓
Domain event emitted (e.g. TICKETING.Ticket.checked_in)
        ↓
Trust Engine Edge Function subscribes via pg_notify
        ↓
Signal classifier: determine signal_type and weight
        ↓
Insert trust_signals row (append-only)
        ↓
Recompute trust_scores for participant
        ↓
If tier changed: emit TRUST.TrustScore.recomputed
        ↓
Downstream effects (feed weight, capability access, notifications)
```

The Trust Engine is a **pure function with side effects**. Given the same set of trust signals, it always produces the same score. The score is deterministic and auditable — any external observer with access to the signal log can reproduce any score at any point in time.

---

## Part I — Signal Catalogue

Every trust signal has a **type**, a **base weight** (positive or negative), a **decay profile**, and a **cap** (maximum cumulative contribution from this signal type to prevent gaming).

### Positive Signals

| Signal Type | Base Weight | Decay | Cap | Notes |
|-------------|------------|-------|-----|-------|
| `phone_verified` | +80 | None | +80 | One-time, on registration |
| `identity_verified` (standard tier) | +120 | None | +120 | One-time, on verification approval |
| `identity_verified` (pro tier) | +60 | None | +60 | Incremental above standard |
| `check_in_recorded` | +8 | Half-life 90 days | +200 lifetime | Each verified check-in. GPS proximity required. |
| `event_completed` | +25 | Half-life 180 days | +300 lifetime | Organiser: event ran without cancellation |
| `ticket_sold` | +3 | Half-life 90 days | +150 lifetime | Organiser: each ticket sold and not refunded |
| `payout_received` | +15 | Half-life 180 days | +200 lifetime | Organiser: successful payout processed |
| `review_received_positive` | +10 | Half-life 180 days | +100 lifetime | Venue/organiser: verified review ≥ 4 stars |
| `dispute_resolved_for` | +20 | None | +60 lifetime | Participant won a dispute resolution |
| `referral_converted` | +5 | Half-life 90 days | +50 lifetime | Referred participant made first purchase |

### Negative Signals

| Signal Type | Base Weight | Recovery | Notes |
|-------------|------------|---------|-------|
| `event_cancelled` | -40 | Recoverable via subsequent `event_completed` | Organiser cancelled a published paid event |
| `review_received_negative` | -15 | Recoverable | Verified review ≤ 2 stars |
| `dispute_raised_against` | -10 | Recoverable if dispute resolved for participant | Dispute opened against this participant |
| `dispute_resolved_against` | -35 | Recoverable slowly | Platform ruled against this participant |
| `fraud_flag` | -150 | Requires manual review to remove | Automated or human-flagged fraud signal |
| `chargeback_filed` | -80 | Manual review required | Payment processor chargeback initiated |

### Signal Weight Conventions

- Weights are integer points on a 0–1000 scale.
- Negative signals can drive the score below zero at the raw level, but the **stored score is clamped to 0** at the floor.
- No single positive signal may exceed 200 points in total lifetime contribution (except identity verification, which is foundational and non-repeating).
- No single negative signal may exceed -200 points without triggering a manual review flag.

---

## Part II — Score Computation

### Component Scores

The overall trust score (0–1000) is computed from four weighted component scores:

```
score = (
  identity_score      × 0.30
  + participation_score × 0.35
  + organiser_score     × 0.25
  + financial_score     × 0.10
)
```

**identity_score (0–200)**

Derived from verification tier and phone verification status.

| Condition | Points |
|-----------|--------|
| Phone verified | +80 |
| Standard verified (document reviewed) | +120 |
| Pro verified (business registration reviewed) | +60 additional |

Maximum: 260 raw → normalised to 200 ceiling.

**participation_score (0–200)**

Derived from check-in history, referral conversions, and positive community engagement.

| Signal source | Points |
|--------------|--------|
| `check_in_recorded` signals (with decay) | Up to +200 |
| `referral_converted` signals | Up to +50 |
| `review_left_verified` (participant leaving a review) | Up to +30 |

Decay model: signals older than 90 days contribute at 50% weight. Signals older than 180 days contribute at 25%. Signals older than 365 days contribute at 0% (expired from participation score, but remain in the signal log permanently for audit).

This decay is intentional: a participant who was active 18 months ago but has not attended anything recently should not retain a high participation score. Trust requires active participation, not historic participation.

**organiser_score (0–200)**

Only computed for participants who have hosted at least one gathering. Zero for non-organisers.

| Signal source | Points |
|--------------|--------|
| `event_completed` signals (with decay) | Up to +150 |
| `ticket_sold` signals | Up to +50 |
| `payout_received` signals | Up to +100 |
| Penalty: `event_cancelled` signals | Down to -120 |

Cancellation ratio: if a participant's cancellation rate exceeds 30% of their total events in the past 90 days, a **Cancellation Penalty Multiplier** of 0.5x is applied to their entire organiser_score. This compounds with the base negative signal.

**financial_score (0–200)**

Derived from payment and payout history. Signals include successful payments (no chargebacks), successful payouts, and absence of fraud flags.

| Signal source | Points |
|--------------|--------|
| No chargebacks in lifetime | +80 baseline |
| Each `payout_received` signal (with decay) | Up to +80 |
| `chargeback_filed` signal | -80 per occurrence |
| `fraud_flag` signal | -150; triggers manual hold |

---

## Part III — Trust Tiers

The computed score maps to a human-readable tier. Tiers are used internally for capability access decisions and externally (sparingly) for participant-facing labels.

| Tier | Score Range | Label (internal) | Participant-facing display | Meaning |
|------|------------|-----------------|--------------------------|---------|
| `new` | 0–99 | New | None shown | Just joined; no participation history |
| `emerging` | 100–299 | Emerging | None shown | Early participation; growing history |
| `established` | 300–499 | Established | None shown | Consistent participation; reliable history |
| `trusted` | 500–749 | Trusted | None shown | Strong history; no negative signals |
| `verified_trusted` | 750–1000 | Verified Trusted | "Verified" badge (organisers/venues only) | Identity verified + strong participation record |

### Tier Transitions

Tier transitions are non-instantaneous in the downward direction. A downgrade requires the score to remain below the threshold for **72 consecutive hours** before the tier label changes. This prevents flapping (score oscillates around a threshold causing repeated tier changes).

Upgrades are immediate — a score that crosses an upgrade threshold activates the new tier within the next score computation cycle (maximum 5 minutes).

---

## Part IV — Capability Gates by Trust Tier

The Trust Engine is the runtime enforcement mechanism for capability access decisions. The following table defines which capabilities are gated by trust tier. This is in addition to — not a replacement for — the DRL/CRL gate. Both must be satisfied.

### Participant Capabilities

| Capability | Minimum Tier | Notes |
|-----------|-------------|-------|
| Browse events (no account) | None | Zero friction. No account required. |
| Register / sign in | None | |
| Purchase tickets | `new` | Any registered participant can buy |
| Follow venues | `new` | |
| Check in | `new` | GPS proximity verified regardless of tier |
| Leave a review | `emerging` | Must have ≥ 1 check-in at this gathering. `new` tier excluded to prevent spam reviews. |
| Refer friends (earn referral points) | `emerging` | Prevents fake account referral farming |
| Gift a ticket | `emerging` | Prevents gift-as-fraud-vector abuse at `new` tier |
| Event Wallet (top-up and spend) | `established` | Financial capability requires participation history |
| Priority entry (Pulse Points redemption) | `established` | |
| Report another participant | `emerging` | `new` tier abuse vector mitigation |

### Organiser Capabilities

| Capability | Minimum Tier | Notes |
|-----------|-------------|-------|
| List a free event | `new` | Free events are always open |
| List a paid event | `emerging` | Minimum participation history before collecting money |
| Go Live (1 per month) | `new` | Unverified: 1/month cap. See Go Live cap rules. |
| Go Live (unlimited) | `verified_trusted` | Requires Standard venue verification which correlates to `verified_trusted` tier |
| Receive payouts | `emerging` | Financial trust signal required |
| Receive payouts > UGX 1,000,000 | `established` | Large payout threshold requires stronger history |
| Access scanner (basic) | `emerging` | |
| Access Scanner Pro (staff management) | `established` | |
| Promoted event listing (paid boost) | `established` | Prevents abuse of promotional surface |
| Direct follower messaging | `verified_trusted` | Pro venue feature; reserved for highest trust tier |
| Create recurring events | `established` | Requires demonstrated reliability |
| Create Festival Mode events | `trusted` | Multi-day, high-stakes; requires strong organiser history |

### Administrative Capabilities (Internal Only)

| Capability | Minimum Tier | Notes |
|-----------|-------------|-------|
| Manual admit override (scanner) | `established` | Supervisor-level action |
| Suspend another participant's scanner session | n/a | Platform admin only; not tier-gated |
| Issue comp tickets | `established` | Organiser-level action |
| Admin ticket migration | n/a | Platform admin only |

---

## Part V — Fraud and Anomaly Detection

The Trust Engine also runs a lightweight anomaly detection pass on every score computation. The following conditions trigger an automatic flag for human review:

| Condition | Action |
|-----------|--------|
| Score drops by > 200 points in a single computation | Flag for review; suppress large payouts pending |
| `fraud_flag` signal received | Immediate score penalty; suspend capability access pending review; notify admin |
| `chargeback_filed` signal received | Score penalty; suspend payout processing pending review |
| Check-in GPS proximity verification fails 3+ times in one day | Flag; reduce check-in earn weight for 7 days |
| > 5 gift initiations in 60 minutes | Rate limit gift initiations; flag for review |
| Referral chain depth > 3 levels | Suppress referral points; flag chain for review |
| Participation score > 150 with 0 check-ins in past 90 days | Decay recalculation trigger |
| `event_cancelled` count ≥ 3 in 90 days | Trigger account review; apply Cancellation Penalty Multiplier |

### Review Queue

Flagged participants enter a review queue. During review:

- No new payouts processed (existing payout history unchanged)
- Capability access frozen at current tier (no upgrades)
- Participant is not notified of the review unless it results in a suspension

Review resolution:
- **Cleared:** Flag removed, normal scoring resumes. No score change from the review itself.
- **Confirmed fraud:** `fraud_flag` signal confirmed, score penalties applied, capabilities suspended, participant notified.
- **Inconclusive:** Flag downgraded to watch status; score resumes but anomaly monitoring continues at higher sensitivity for 30 days.

---

## Part VI — Score Computation Cycle

### Trigger Conditions

The Trust Engine recomputes a participant's score when any of the following occurs:

- A new trust_signal row is inserted for this participant
- A trust_signal's decay period boundary is crossed (daily batch job)
- A manual score refresh is requested by admin
- The participant's verification tier changes

### Computation Steps

```
1. Load all trust_signals for participant (ordered by recorded_at ASC)
2. Apply decay weights to signals older than 90 days
3. Zero out signals older than 365 days (participation score only)
4. Sum each component score (identity, participation, organiser, financial)
5. Apply Cancellation Penalty Multiplier if applicable
6. Apply composite formula: score = (identity×0.30) + (participation×0.35) + (organiser×0.25) + (financial×0.10)
7. Clamp result to 0–1000
8. Determine tier from score
9. Apply 72-hour downgrade buffer if score dropped below current tier threshold
10. If score or tier changed from previous computation: update trust_scores row
11. If tier changed: emit TRUST.TrustScore.recomputed domain event
12. Log computation in domain_events for audit
```

### Performance Target

Score recomputation must complete within **2 seconds** from signal insertion to updated `trust_scores` row. This is not user-facing latency (the score is not shown in real-time), but it gates capability decisions that may be queried immediately after a signal (e.g. can this organiser receive a payout right after event completion).

---

## Part VII — Readiness Position and Activation Path

### Current State (Phase 1 — Architecture and Design)

**BRL: BRL1 (Designed)** — The Trust Engine is fully designed. Schema defined in `ZUKA_Canonical_Schema_Pack.md`. Events defined in `ZUKA_Event_Catalog.md`. Signal catalogue and computation model documented here. No open design questions.

**DRL: DRL0 (Schema)** — No trust signals have been collected. Schema exists. Data does not. *Note: DRL is per-entity. Once live, a long-active organiser may reach DRL3 while a new participant sits at DRL0. Any operation spanning both is governed by the minimum — see Readiness Constitution Part I.*

**CRL: CRL-0 (Observing)** — This is the constitutionally correct and permanent position for the Trust Engine. It **observes** participation, **computes** scores, and **stores** them internally. It does not advise, suggest, or act on participants with judgement. Capability *eligibility* decisions (can this organiser receive a payout, can this participant gift a ticket) are deterministic rule-checks against the computed score — not autonomous actions by the engine. The Trust Engine therefore never needs to climb the CRL ladder; it is a CRL-0 observer by design. Any future capability that *acts* on trust outputs (e.g. an autonomous reputation-based intervention) would be a separate capability with its own CRL governance.

### Activation Path (BRL × DRL; CRL fixed at 0)

| Stage | BRL Target | DRL Target (effective) | Condition to advance |
|-------|-----------|------------------------|---------------------|
| Build and unit test | BRL2 | DRL0 | Engine computes correct scores from synthetic signals in dev |
| Internal pilot | BRL3 | DRL1 | Live signal collection on internal team accounts; monitoring active |
| Closed beta | BRL3 | DRL2 | Signal patterns observable; score distribution sensible (not all-zero, not all-max) |
| Controlled production | BRL4 | DRL3 | Score confidence validated; no P1 incidents in pilot; rollback tested |
| Full production | BRL5 | DRL4 | 30 days at BRL4 clean; automated pipeline; decay job automated |

### DRL Blocker to Watch

The **participation_score decay model** requires DRL2 (patterns observable) before it has any practical effect. At DRL1 (first data collected), the decay model is executing but cannot be validated — no signals are old enough to have decayed. The first real validation requires at least 90 days of live data. This does not block the Trust Engine from launching at BRL4/DRL3, but the participation_score sub-component should be treated as **provisional for the first 90 days** of live operation.

### Intelligence Layer Gate

The Trust Engine's computed scores must reach **DRL3** (confidence validated, score distribution stable) before any capability in the intelligence layer (personalised feed, recommendations, B2B audience data) may advance past **CRL-1 (Learning)**. Trust scores are a foundational input to those capabilities. A recommendation engine that *advises* (CRL-2) on the basis of unvalidated trust scores produces worse outcomes than no recommendations. Until trust DRL3 is reached, those downstream capabilities may observe and learn from trust outputs but may not surface anything derived from them.

---

## Part VIII — Audit and Explainability Requirements

Per `ZUKA_Appendix_I_Explainability_Registry.md`, the Trust Engine must satisfy the following explainability requirements:

**Internal audit (always):** Any administrator must be able to reconstruct the exact trust score for any participant at any point in time, given the signal log. The computation is deterministic and the log is append-only.

**Participant transparency (on request):** A participant may request a plain-language summary of their trust status: "Your account is in good standing" or "Your account has some flags that our team is reviewing." No numeric score disclosed. No signal details disclosed.

**Dispute resolution:** If a participant disputes a capability restriction they believe is trust-related, the reviewing administrator can inspect the signal log to determine the root cause. The participant is told the category of signal that triggered the restriction (e.g. "a payment dispute was filed against your account") but not the full score breakdown.

**Data retention:** Trust signals are retained permanently in the signal log (they are part of the immutable participation record). Computed scores are retained for 2 years in the `trust_scores` table. Score computation audit logs (in `domain_events`) are retained for 90 days.

---

*Authority: Book VII (Trust and Opportunity Systems) · Book XII (Governance) · `ZUKA_Readiness_Constitution.md`*  
*Next: Opportunity Engine Specification*
