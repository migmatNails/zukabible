# ZUKA Readiness Constitution — DRL · BRL · CRL

**Version:** 2.0  
**Status:** Active — Constitutional Layer  
**Supersedes:** `ZUKA_DRL_CRL_Constitution.md` v1.0 (which conflated build-maturity and autonomy under one "CRL" label)  
**Authority:** Book XII Chapters 4 & 5; Volume XIV governing inequality; Unified Dormant Feature Register  
**Governs:** All capability and feature activation decisions across the platform  

---

## Constitutional Preamble

Every capability in ZUKA is governed by three independent readiness questions. They are independent because they fail independently, and conflating them hides risk.

1. **DRL — Data Readiness Ladder:** Is the *data* mature enough to support this capability honestly? Assessed **per entity and per operation**, never as a global average.
2. **BRL — Build Readiness Ladder:** Is the capability *built, tested, and operationally proven*?
3. **CRL — Capability Readiness Ladder:** Is the capability *permitted to act on participants*, and to what autonomy depth? (observing → coordinating)

A capability may have rich data (DRL5) and be fully built (BRL Constitutional) yet still be permitted only to *observe* (CRL-0). A capability may be permitted deep autonomy in principle yet be blocked because the data for one entity in its scope is sparse. The three axes are orthogonal and all three bind.

### The Governing Inequality

This is the master rule of the entire platform. It comes from Volume XIV and governs everything:

```
manifestation_depth ≤ min(DRL_permits, BRL_built, CRL_earned)
```

A capability may only manifest to the depth permitted by the **minimum** of its three readiness axes. The weakest axis is always the binding constraint. There is no averaging, no compensating a weak axis with a strong one.

**Why minimum and not average:** A capability with perfect data and perfect code but unearned autonomy (CRL-0) must still only observe. A capability permitted to coordinate, built to production grade, but operating on DRL1 data for some entity in its scope must still hold back — because acting confidently on immature data is exactly how trust is destroyed. The minimum rule makes the system fail safe.

---

## Part I — DRL · Data Readiness Ladder

The DRL measures the maturity of the data a capability depends on. **DRL is assessed per entity and per operation** — never as a single platform-wide number.

### DRL Stages

| Stage | Name | Definition |
|-------|------|-----------|
| DRL0 | Schema | The schema exists. Fields defined, typed, constrained. No meaningful data written. |
| DRL1 | Collection | Data is actively being written. Volume low. Quality unvalidated. |
| DRL2 | Patterns | Sufficient volume to observe repeating patterns. Quality spot-checked. |
| DRL3 | Confidence | Quality validated. Patterns stable and statistically significant. Trusted for product decisions. |
| DRL4 | Automation | Pipeline automated, monitored, self-healing. Quality enforced systematically. |
| DRL5 | Intelligence | Mature enough to train models and power autonomous decisions exposed to users. |

### Per-Entity, Per-Operation Assessment (the critical property)

This is what makes ZUKA's DRL smarter than a naive maturity score.

**Per-entity:** The same data table holds entities at wildly different maturity. A venue that has hosted 200 events has DRL3+ attendance data. A venue registered yesterday has DRL0. The Trust Engine, the Historians, and every matching engine must read the DRL *of the specific entity they are operating on*, not the population average.

**Per-operation (the minimum-across-scope rule):** When an operation touches multiple entities, its **effective DRL is the minimum DRL across all entities in scope.**

```
effective_DRL(operation) = min( DRL(entity) for entity in operation.scope )
```

A sponsorship match between a mature community (DRL3) and a brand-new participant (DRL0) operates at **effective DRL0** — and is therefore blocked from any autonomy depth requiring DRL above 0, no matter how rich the community's data is. The sparse side governs.

This rule is why the Dormant Feature Register repeatedly specifies *"per-entity DRL 2+"*, *"minimum across all entities in scope"*, and *"per-operation effective DRL ≥ 2"*. It is not optional precision — it is the core safety property of the data axis.

### DRL Assessment Dimensions

Each entity's DRL for a given capability is the **minimum** across five dimensions:

| Dimension | Question |
|-----------|----------|
| Volume | Enough data for patterns to be meaningful? |
| Quality | Accuracy validated? Null rates acceptable? Edge cases handled? |
| Freshness | Current? Refresh cadence adequate? |
| Coverage | Represents the full population, or a biased subset? |
| Stability | Schema stable? Free of frequent breaking changes? |

### DRL Rules

- DRL is assessed per entity, per capability. Never a single global figure.
- An operation's effective DRL is the minimum across all entities in scope.
- DRL advances only on documented evidence (ADR entry). It is **downgraded immediately** if data quality degrades, suspending any capability whose floor is no longer met.

---

## Part II — BRL · Build Readiness Ladder

The BRL measures whether the capability is built, tested, and operationally proven. *(This was called "CRL" in v1.0 of this document. It is renamed to BRL to free the CRL label for its correct meaning — autonomy. The stages are unchanged.)*

### BRL Stages

| Stage | Name | Gate criteria |
|-------|------|--------------|
| BRL0 | Defined | Constitutional definition exists. Purpose, scope, constraints documented and founder-reviewed. |
| BRL1 | Designed | Full technical design: schema, domain events, API contracts, UI flows, edge cases. No open design questions. |
| BRL2 | Prototype | Working prototype in dev. Core happy-path flows function end-to-end. Not production-safe. |
| BRL3 | Operational Pilot | Live for a controlled subset. Real data flows. Monitoring active. Rollback plan tested. |
| BRL4 | Controlled Production | Live in production with guardrails: feature flags, rate limits, capacity constraints, human monitoring. Runbook written. |
| BRL5 | Constitutional | Full scale without active guardrails. 30+ days at BRL4, no P1 incidents. Monitoring automated, runbooks tested. |

### BRL Rules

- Begins at BRL0 when first documented. No stage skipped.
- BRL4 → BRL5 requires a mandatory 30-day clean waiting period. No exceptions.
- A P1 incident at BRL5 reverts the capability to BRL4 pending post-mortem.
- Blocks to advancement: open P1 incident, unresolved constitutional contradiction, missing rollback plan (blocks BRL3+), missing monitoring (blocks BRL3+), missing runbook (blocks BRL4+).

---

## Part III — CRL · Capability Readiness Ladder

The CRL measures permitted autonomy depth — how far the capability may reach into a participant's experience. Full specification in **`ZUKA_CRL_Constitution.md`**. Summary:

| CRL | Verb | May surface? | May act? | Human in loop? |
|-----|------|-------------|----------|----------------|
| CRL-0 | Observing | No | No | n/a |
| CRL-1 | Learning | No (adapts internal models only) | No | n/a |
| CRL-2a | Advising (pull) | On request only | No | Human asks |
| CRL-2b | Advising (proactive) | Volunteered suggestions | No | Human decides |
| CRL-3 | Assisting | Yes | Bounded, confirmed | Human confirms each action |
| CRL-4 | Coordinating | Yes | Autonomous, bounded | Human sets bounds only |

CRL is the axis most often binding for intelligent capabilities, because data and code mature faster than the right to act on people should. The constitutional ceiling is COORDINATING — there is no autonomous-unbounded stage. Anything beyond requires constitutional amendment and founder ratification, not a CRL advancement.

Advancement requires a passed **Trust Impact Assessment** at the appropriate tier (see CRL Constitution Part IV). Anti-fraud capabilities are constitutionally pinned at CRL-0 Observing — they flag to human review queues and never autonomously punish.

---

## Part IV — The Three-Axis Activation Gate

To activate a capability at a given depth, all three axes must independently permit it. The binding axis is the minimum.

### Worked Examples

**Trust Engine, computing scores for payout decisions (MVP):**
- DRL: needs DRL3 (confident data) for the entities being scored → currently DRL0, **binding**
- BRL: BRL1 (designed)
- CRL: needs CRL-0 only (it observes and computes; payout *eligibility* is a rule-check, not autonomous action against the user)
- `manifestation_depth ≤ min(DRL0, BRL1, CRL0)` → **blocked on DRL; build proceeds, activation waits for data**

**A Historian surfacing memories proactively:**
- DRL: per-participant; needs DRL2+ for that participant's memory stream
- BRL: must be built (BRL2+)
- CRL: proactive surfacing is CRL-2b → requires sustained time at CRL-2a first, plus a Standard Trust Impact Assessment
- Binding axis is almost always CRL here — the Historian will have data and code long before it earns the right to volunteer memories. **Correct: it observes for a year before it advises.**

**Scholarship Engine, automated distribution path:**
- DRL: DRL4+ (register requirement)
- BRL: BRL4+ (controlled production)
- CRL: CRL-4 Coordinating + sustained dispute-free record
- The strictest gate in the platform. The committee-review path (CRL-2 advising, human decides) is buildable at MVP as schema-only; the autonomous path waits for all three axes at their highest practical levels.

### Activation Permission Matrix

| Activation context | BRL | DRL (effective, min-across-scope) | CRL |
|-------------------|-----|-----------------------------------|-----|
| Bible documentation | BRL0 | DRL0 | CRL-0 |
| Technical design | BRL1 | DRL0 | CRL-0 |
| Internal prototype | BRL2 | DRL0 | CRL-0 |
| Internal pilot | BRL3 | DRL1 | CRL-0/1 |
| Closed beta | BRL3 | DRL2 | per Trust Impact Assessment |
| Controlled production | BRL4 | DRL3 | per Trust Impact Assessment |
| Full / constitutional | BRL5 | DRL4 | up to CRL-3 |
| Autonomous coordination | BRL5 | DRL4+ | CRL-4 + dispute-free record |

---

## Part V — Current Capability Register

**Key:** ✅ met · ⏳ in progress · 🔒 blocked · 💤 dormant (intentional)

### Phase 1 MVP — non-autonomous capabilities (CRL-0, rule-based)

These are deterministic, rule-based features. They do not "act on" participants with judgement — they execute defined rules. They sit at CRL-0 and are gated by DRL and BRL only.

| Capability | BRL | DRL target | CRL | Status |
|-----------|-----|-----------|-----|--------|
| Participant registration (phone + OTP) | BRL1 | DRL3 | CRL-0 | ⏳ building |
| Google / Apple OAuth | BRL1 | DRL3 | CRL-0 | ⏳ |
| Venue creation & management | BRL1 | DRL3 | CRL-0 | ⏳ |
| Gathering creation & publish | BRL1 | DRL3 | CRL-0 | ⏳ |
| Go Live (instant activation) | BRL1 | DRL3 | CRL-0 | ⏳ |
| Home feed (Live/Soon/Tonight) | BRL1 | DRL3 | CRL-0 | ⏳ |
| Search & discovery (rule-ranked) | BRL1 | DRL3 | CRL-0 | ⏳ |
| Ticket purchase (Mobile Money / card) | BRL1 | DRL3 | CRL-0 | ⏳ |
| Smart Bucket QR | BRL1 | DRL3 | CRL-0 | ⏳ |
| Zuka Scanner (native tickets) | BRL1 | DRL3 | CRL-0 | ⏳ |
| Check-in (GPS proximity) | BRL1 | DRL3 | CRL-0 | ⏳ |
| Push notifications | BRL1 | DRL3 | CRL-0 | ⏳ |
| Venue verification (manual) | BRL1 | DRL3 | CRL-0 | ⏳ |
| Organiser dashboard (basic counts) | BRL1 | DRL3 | CRL-0 | ⏳ |
| Payout system | BRL1 | DRL3 | CRL-0 | ⏳ |
| Follow system | BRL1 | DRL3 | CRL-0 | ⏳ |
| Pulse Points (earn only) | BRL1 | DRL3 | CRL-0 | ⏳ |
| Attendance memories (created) | BRL1 | DRL3 | CRL-0 | ⏳ |
| Trust Engine (score computation) | BRL1 | DRL3 | CRL-0 | ⏳ observes & computes only |

### Phase 2 — feature capabilities

| Capability | BRL | DRL target | CRL | Status |
|-----------|-----|-----------|-----|--------|
| Audio streaming (board-out / ambient) | BRL0 | DRL2 | CRL-0 | 🔒 design pending |
| Gift ticketing | BRL1 | DRL2 | CRL-0 | ⏳ |
| Scanner Pro (external, staff) | BRL1 | DRL2 | CRL-0 | ⏳ |
| Pulse Points redemption (burn) | BRL0 | DRL2 | CRL-0 | 🔒 |
| Heat map | BRL1 | DRL2 | CRL-0 | ⏳ needs DRL2 |
| Co-attendance notifications | BRL1 | DRL2 | CRL-0 | ⏳ needs DRL2 |
| Event Wallet | BRL1 | DRL3 | CRL-0 | ⏳ regulatory review |
| City Pulse Score | BRL0 | DRL2 | CRL-2a | 🔒 needs DRL2 |

### Phase 3 / AI Systems — autonomy-governed (CRL binding)

From the Unified Dormant Feature Register. CRL is the binding axis for nearly all of these.

| Capability | BRL | DRL (per-entity) | CRL current → target | Status |
|-----------|-----|------------------|---------------------|--------|
| 7 Historians | BRL0 | DRL0 | CRL-0 → CRL-2 | 💤 observing |
| 6 Twin types | BRL0 | DRL0 | CRL-0 → CRL-2 | 💤 observing; needs TwinConsentRecord |
| 5 Agents (Trust first, Opportunity last) | BRL0 | DRL0-1 | CRL-0 → CRL-2 | 💤 observing |
| Community Copilots | BRL0 | DRL3 (community) | CRL-0 → CRL-2/3 | 💤 |
| Gathering Copilots | BRL0 | DRL3 (gathering) | CRL-0 → CRL-1 | 💤 learning |
| Semantic Enrichment Engine | BRL0 | DRL2 | CRL-0 → CRL-1 | 💤 learning |
| Community Intelligence Layer | BRL0 | DRL2 (DRL3 "future leaders") | CRL-0 → CRL-1 | 💤; "future leaders" sub-question held at CRL-0 |
| Opportunity Intelligence Layer | BRL0 | DRL3 | CRL-0 → CRL-2 | 💤 needs ProvenanceRecord per match |
| Sponsorship / Mentorship / Creator engines | BRL0 | DRL3 | CRL-0 → CRL-2 | 💤 advising target |
| Scholarship Engine (committee path) | BRL1 | DRL0 (schema) | CRL-2 | ⏳ buildable at MVP, schema-only |
| Scholarship Engine (automated path) | BRL0 | DRL4 | CRL-0 → CRL-4 | 💤 strictest gate |
| Anti-Fraud (topology/content/collusion) | BRL0 | DRL0 | **CRL-0 pinned** | 💤 observes & flags to human review only |
| Trust OS (full, Trust Timeline UI) | BRL0 | DRL3 | CRL-0 → CRL-2 | 💤 |

### Constitutionally Dormant (Appendix J — no timeline)

| Capability | DRL req | BRL req | CRL ceiling |
|-----------|---------|---------|-------------|
| AI Identity Twins (full) | DRL5 | BRL5 | CRL-2/3 |
| Predictive Historians | DRL5 | BRL5 | CRL-2 |
| Opportunity OS (full) | DRL4 | BRL5 | CRL-3 |
| Federation | DRL4 | BRL5 | governance-gated, not CRL |
| Civilization Systems | DRL5 | BRL5 | governance-gated |
| Multi-city (city 2+) | DRL3 | BRL4 | n/a — Kampala self-sustaining first |

---

## Part VI — Governing Principles (Non-Negotiable)

**1. Three axes, all binding.** DRL, BRL, and CRL are independent. The minimum governs. A strong axis never compensates for a weak one.

**2. DRL is per-entity, per-operation.** Effective DRL for any operation is the minimum across all entities in scope. The sparse side always governs. No population averages.

**3. No stage skipped on any axis.** BRL0→5 in order. CRL observing→coordinating in order. DRL0→5 in order.

**4. CRL is the autonomy brake.** Most intelligent capabilities will have data and code long before they earn the right to act. They observe first. This is correct, not slow.

**5. Anti-fraud never acts autonomously.** Fraud models are pinned at CRL-0. They flag to humans. Only humans act against participants.

**6. Advancement is evidence-gated, never pressure-gated.** Not a roadmap date, not commercial pressure, not an emotional appeal. Only documented evidence against gate criteria. Graduation from the Dormant Register follows the same discipline.

**7. Dormant is alive.** A dormant capability's design is preserved. When reality requires it, the design is ready. Graduation is a documented note, never a silent deletion.

**8. The register is living.** Updated on every advancement or regression. A stale register is a governance failure.

---

*Authority: Book XII Ch.4 & 5 · Volume XIV governing inequality · Unified Dormant Feature Register*  
*Companion documents: `ZUKA_CRL_Constitution.md` · `ZUKA_Trust_Engine_Specification.md` · `ZUKA_Appendix_J_Dormant_Capability_Registry.md` · `ZUKA_ADR_Catalog.md`*
