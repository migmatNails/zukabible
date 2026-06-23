# ZUKA CRL Constitution — The Capability Readiness Ladder

**Version:** 1.0  
**Status:** Active — Constitutional Layer  
**Authority:** Book XII (Governance), Volume XIV CRL governing inequality, Unified Dormant Feature Register  
**Governs:** The permitted *autonomy depth* of every capability that acts on participant data or experience  
**Companion:** `ZUKA_Readiness_Constitution.md` (the unified DRL/BRL/CRL system)  

---

## Constitutional Preamble

A capability can be perfectly built and still be dangerous to unleash. The question "is it built?" (BRL) is different from the question "is the data ready?" (DRL), and both are different from the question this document governs:

**How much is this capability permitted to act on a human being?**

The Capability Readiness Ladder (CRL) answers that question. It is a ladder of *autonomy* — the depth to which a capability is permitted to reach into a participant's experience, reputation, relationships, or decisions. A capability climbs from passively watching, through learning, to offering advice, to taking assistive action, to coordinating outcomes across participants.

CRL is the constitutional brake on AI overreach. The Historians, the Twins, the Agents, the Copilots — every intelligent system in the ZUKA architecture — is governed first and foremost by CRL. A system may *observe* for a year before it is permitted to *advise*. It may *advise* for a year before it is permitted to *assist*. This is not slowness for its own sake. It is the deliberate accumulation of evidence that the capability acts well before it is permitted to act consequentially.

**The governing inequality (Volume XIV):**

```
manifestation_depth ≤ min(DRL_permits, BRL_built, CRL_earned)
```

A capability may only manifest to the depth permitted by the *minimum* of its three readiness axes. CRL is the axis most often binding, because data and code mature faster than the right to act on people should.

---

## The Five CRL Stages

CRL is a permission gradient. Each stage grants strictly more autonomy than the one below it. No stage may be skipped. The stages are verbs because they describe what the capability is permitted to *do*.

### CRL-0 · OBSERVING

**Permission:** The capability may read data and record its own internal state. It may compute, infer, and store conclusions. It may **not** surface anything to any participant, may **not** influence any ranking or decision, and may **not** act.

**What this looks like:**
- A Historian reads the memory stream and builds an internal model of a participant's attendance patterns. Nothing is shown. Nothing is suggested.
- A Trust Agent computes topology features across the relationship graph and stores anomaly candidates in an internal queue. No flag is raised to any human yet.
- A Twin synthesises a participant's taste profile and holds it internally, producing no recommendations.

**Why it exists:** Observation is how a capability earns the evidence required to advance. An observing capability is auditable — its internal conclusions can be compared against ground truth before it is ever permitted to act on them. This is the stage where we discover that the capability is wrong 40% of the time *before* it tells anyone anything.

**Gate to advance (OBSERVING → LEARNING):**
- The capability has observed for a documented minimum period (capability-specific, never less than 30 days).
- Its internal conclusions have been validated against ground truth at an accuracy bar set per capability.
- DRL for the entities in scope is at or above the capability's stated DRL floor.

---

### CRL-1 · LEARNING

**Permission:** Everything in OBSERVING, plus the capability may **adapt its own internal models** based on feedback signals (corrections, outcomes, participant behaviour). It may refine. It still may **not** surface anything to participants or influence any decision.

**What this looks like:**
- The Semantic Enrichment Engine adjusts its pattern-recognition weights as more Tradition Engine seed data arrives.
- A Community Copilot refines its model of what a given community values, learning from which suggestions *would have* been acted on (counterfactually logged, never shown).
- The Trust Engine's decay model recalibrates as real signal-age data accumulates.

**Why it exists separately from OBSERVING:** A capability that merely observes is static. A learning capability changes itself. That introduces a new risk class — model drift, feedback loops, overfitting to early data. LEARNING is the stage where we confirm the capability improves rather than degrades as it adapts, still without any participant exposure.

**Gate to advance (LEARNING → ADVISING):**
- Demonstrated model stability: the capability's conclusions converge rather than oscillate as it learns.
- A passed **Trust Impact Assessment** (see Part IV) — required for any capability crossing into participant-facing territory.
- For AI capabilities touching personal/family/community data: relevant consent records confirmed (TwinConsentRecord, community governance consent).
- DRL floor met, typically DRL2+ (patterns observable).

---

### CRL-2 · ADVISING

**Permission:** Everything in LEARNING, plus the capability may **surface conclusions to participants as suggestions, passively and on a pull basis**. It offers. It does not act, does not push, and does not decide. The participant must come to it or explicitly invite it. Every output is clearly a suggestion the participant may ignore.

**Two sub-modes:**
- **Pull-only / passive (CRL-2a):** The capability responds only when a participant explicitly asks. A Community Knowledge Layer that answers a question when queried. A Memory Time Machine that responds to a natural-language search. Nothing is volunteered.
- **Proactive surfacing (CRL-2b):** The capability may volunteer a suggestion into a participant's surface (a card, a gentle prompt) but only suggestions, never actions. "You might enjoy this event." "This tradition is at risk — want to revive it?"

The register distinguishes these explicitly: *"CRL 'advising' for passive/pull-only surfacing; CRL 'assisting' for proactive"* in some contexts, and elsewhere proactive surfacing is itself the higher bar within advising. **Rule: pull-only (2a) is permitted at the ADVISING floor; proactive surfacing (2b) requires the capability to have spent a documented period stable at 2a first.**

**What this looks like:**
- Opportunity, Sponsorship, Mentorship, Creator, Scholarship engines surfacing *candidate matches* for human review — never auto-executing them.
- A Personal Historian answering "what did I do last December?" when asked.
- The Anniversary Engine — but only after its sensitivity infrastructure is built and tested, and even then surfacing gently.

**Why it exists:** This is the first stage where the capability touches a human. The risk is no longer internal. A bad suggestion wastes attention, erodes trust, or — for sensitive domains like anniversaries or memory — causes real emotional harm. ADVISING is bounded so that the worst case is an ignorable suggestion, never an unwanted action.

**Gate to advance (ADVISING → ASSISTING):**
- Documented period of stable advising with measured suggestion-acceptance and an absence of trust-damaging incidents.
- DRL3+ for capabilities whose advice depends on confident data (most matching engines).
- A working **ProvenanceRecord per output** for any capability whose advice will become action (matching engines must be able to explain *why* this match, per Vol VIII Ch.22).
- Renewed Trust Impact Assessment at the assisting threshold.

---

### CRL-3 · ASSISTING

**Permission:** Everything in ADVISING, plus the capability may **take bounded actions on the participant's behalf, with the participant in the loop**. It acts — but each action is either pre-authorised within tight bounds or confirmed by the participant at the moment of action. It assists a human who remains the decision-maker.

**What this looks like:**
- A Copilot drafting a community announcement the organiser reviews and sends — the Copilot acts (drafts, schedules) but the human confirms.
- An Opportunity Agent that, once a human has approved a match, executes the introduction/handoff workflow.
- Proactive surfacing that has earned the right to also take a small confirming action ("Add to your schedule?" → done on tap).

**Why it exists:** Assisting removes friction by acting, but keeps the human as the final gate. The risk is that the capability acts *correctly but unwantedly*, or that the bounds of its pre-authorisation are wrong. ASSISTING is where we prove the capability's actions are reliably within bounds before removing the human from the loop.

**Gate to advance (ASSISTING → COORDINATING):**
- Sustained, dispute-free track record of bounded actions (the register's "sustained dispute-free track record", Vol XIV Ch.4 State 4→5).
- DRL4+ for any capability that will coordinate autonomously.
- BRL at Controlled Production or above.
- A Security and Risk assessment specific to multi-party autonomous action.
- The strictest Trust Impact Assessment tier.

---

### CRL-4 · COORDINATING

**Permission:** Everything in ASSISTING, plus the capability may **coordinate outcomes across multiple participants autonomously**, within its constitutional bounds, without a human confirming each action. This is the deepest autonomy ZUKA grants. It is reserved for capabilities that have earned it through every lower stage.

**What this looks like:**
- The Scholarship Engine's *criteria-automated distribution path* — autonomously distributing scholarship resources against defined criteria. The register holds this to the highest bar: *"DRL 4+ + CRL 'coordinating' + sustained dispute-free track record."*
- An Opportunity OS coordinating multi-party matches at ecosystem scale once trust, provenance, and track record all support it.

**Why it exists, and why it is rare:** Coordination means the system shapes outcomes for people who did not individually confirm each step. This is appropriate only for capabilities whose correctness, fairness, and bounds have been proven across every lower stage over sustained time. Most capabilities will never reach COORDINATING, and that is correct. The register lists very few candidates for it.

**No stage above COORDINATING exists.** There is deliberately no "autonomous-unbounded" stage. The constitutional ceiling on machine autonomy in ZUKA is coordination-within-bounds. Anything beyond requires a constitutional amendment and founder ratification, not a CRL advancement.

---

## Part II — CRL Quick Reference

| CRL | Verb | May surface to user? | May act? | Human in loop? | Typical DRL floor |
|-----|------|---------------------|----------|----------------|-------------------|
| CRL-0 | Observing | No | No | n/a | DRL0+ |
| CRL-1 | Learning | No | No | n/a | DRL2+ |
| CRL-2a | Advising (pull) | Only on request | No | Human asks | DRL2+ |
| CRL-2b | Advising (proactive) | Volunteered suggestions | No | Human decides | DRL2–3+ |
| CRL-3 | Assisting | Yes | Bounded, confirmed | Human confirms each | DRL3+ |
| CRL-4 | Coordinating | Yes | Autonomous, bounded | Human sets bounds only | DRL4+ |

---

## Part III — CRL Mapping for the Dormant Feature Register

The Unified Dormant Feature Register uses CRL verb-states directly. This table makes the register's shorthand precise against the ladder above.

| Register capability | Register CRL note | Precise CRL gate |
|--------------------|-------------------|------------------|
| The 7 Historians | "observing" | CRL-0; advance to CRL-2a only after sustained-presence criterion + ground-truth validation |
| The 6 Twin types | "observing" | CRL-0; TwinConsentRecord required before CRL-1; honest synthesis output required before CRL-2 |
| The 5 Agents | "observing" | CRL-0; Trust Agent earliest to reach CRL-2; Opportunity Agent last (depends on Trust Agent output) |
| Community Copilots | "assisting (proactive) / advising (passive)" | CRL-2a at Community DRL3+; CRL-2b→CRL-3 for proactive, earned separately |
| Gathering Copilots | "learning" | CRL-1; single-occurrence gatherings unlikely to reach advising before tradition-series Twins |
| Semantic Enrichment Engine | "learning" | CRL-1 at DRL2 across entity population |
| Community Intelligence Layer | "learning" | CRL-1; "future leaders" sub-question held at CRL-0 pending separate review |
| Opportunity Intelligence Layer | "advising" | CRL-2 + ProvenanceRecord per match + Trust Agent output available |
| Sponsorship / Mentorship / Creator engines | "advising" | CRL-2 floor; DRL3+ + Opportunity Agent Mode 2 active |
| Scholarship Engine (automated path) | "coordinating" | CRL-4; DRL4+ + sustained dispute-free record. Committee-review path buildable at MVP (schema only) |
| Community Knowledge Layer | "advising" | CRL-2a passive; CRL-2b/3 for proactive surfacing |
| Community Maturity Score | "assisting" | CRL-3; Community Genome at DRL2+, full 7-module Community OS active |
| Trust OS (full) | "advising" (Trust Timeline UI) | CRL-2; Trust Agent DRL3+ producing meaningful output |
| Anti-Fraud (topology / content / collusion) | "observing" | CRL-0; these run as observers feeding human review, never autonomous punishment |

**Critical constitutional note on Anti-Fraud:** Fraud-detection capabilities are held at CRL-0 OBSERVING by design. They surface candidates to *human* review queues. They do not autonomously punish, suspend, or penalise. Autonomous enforcement against a participant is a coordination-level action against that person's interests and is constitutionally prohibited without human adjudication. A fraud model may *observe* and *flag*; only a human may *act* on the flag. This is non-negotiable.

---

## Part IV — The Trust Impact Assessment

Required before any capability advances from LEARNING (CRL-1) to ADVISING (CRL-2), and re-run at each subsequent advancement.

The assessment answers one question: **If this capability acts wrongly at this autonomy depth, what is the worst-case harm to a participant, and is it recoverable?**

| Assessment tier | Triggered at | Worst-case question |
|-----------------|-------------|---------------------|
| Light | CRL-1 → CRL-2a | Worst case: an ignorable wrong suggestion on request. Recoverable? Almost always yes. |
| Standard | CRL-2a → CRL-2b | Worst case: an unwanted proactive suggestion. Could it cause emotional harm (anniversaries, memory, loss)? Is sensitivity infrastructure built? |
| Heightened | CRL-2 → CRL-3 | Worst case: an unwanted *action* taken on the participant's behalf. Is it reversible? Are the action bounds correct? |
| Strict | CRL-3 → CRL-4 | Worst case: an unwanted outcome coordinated across *multiple* participants. Fairness? Provenance? Dispute path? |

A capability fails the assessment — and does not advance — if any worst-case harm is both plausible and irrecoverable at the target autonomy depth. Sensitive domains (Anniversary Engine, Memory Time Machine, anything touching grief, family, or reputation) require the sensitivity infrastructure built and tested *before* the assessment can be passed, regardless of DRL or BRL.

---

## Part V — CRL Advancement Process

1. The capability owner documents current CRL, time-in-stage, and the evidence for the stated gate criteria.
2. The relevant DRL (per-entity, minimum across scope) and BRL are confirmed to support the target CRL.
3. The appropriate Trust Impact Assessment tier is run and passed.
4. For AI capabilities touching personal/family/community data: consent records are confirmed current.
5. An ADR is filed in `ZUKA_ADR_Catalog.md`: capability, previous CRL/DRL/BRL, new CRL, evidence, Trust Impact Assessment result, risks.
6. The Unified Dormant Feature Register is updated (graduation note, never silent deletion).

**Advancement is never granted for:** a roadmap date arriving, commercial pressure, an emotional appeal, or "it's been a while." Only documented evidence against the gate criteria advances a capability. This mirrors the graduation discipline of the Dormant Feature Register itself.

---

## Part VI — CRL Regression

A capability regresses one or more CRL stages when:

- It produces a trust-damaging incident at its current autonomy depth.
- Its supporting DRL degrades below the floor for its current CRL.
- A consent basis it relied upon is withdrawn.
- An anomaly indicates its actions are exceeding their intended bounds.

On regression: the capability immediately drops to the highest CRL stage whose gate criteria are still demonstrably met, the regression is logged in `ZUKA_Constitutional_Amendment_Registry.md`, and re-advancement follows the full process from the regressed stage. A COORDINATING capability that causes an unbounded outcome drops at minimum to ASSISTING (human back in the loop) pending post-mortem.

---

*Authority: Book XII (Governance) · Volume XIV governing inequality · Unified Dormant Feature Register*  
*Companion: `ZUKA_Readiness_Constitution.md` (unified DRL/BRL/CRL) · `ZUKA_Trust_Engine_Specification.md`*
