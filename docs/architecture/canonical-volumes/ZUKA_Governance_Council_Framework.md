# ZUKA Governance Council Framework

> **Authority:** Zuka-Bible 2, Book I Part D (Chapter 19 — Governance Doctrine) +
>                  Book I Part C (Chapter 12 — Authority Model) +
>                  Chapter 11 (Constitutional Hierarchy)
> **Operationalised by:** Volume XI, Ch.13 (Founder Rule); Volume XII, Ch.12 (Founder
>                  Rules Reconciled); Volume XVIII, Ch.5 (Legal Review Queue Process);
>                  Volume XX, Ch.10 (DRL Activation Contract)
> **Status:** Constitutional — the council names are immutable (Zuka-Bible 2 Book I
>              Part C, Ch.13.2); the remit and process of each council are Stable Zone
>              and may evolve through governance

---

## Constitutional Context

Zuka-Bible 2, Book I Part D (Chapter 19) named four governance bodies without
specifying their remits:

```
- Architecture Council
- Governance Council
- Security Council
- Trust Council
```

The Authority Matrix file added a fifth:

```
- Evolution Council
```

This document operationalises all five: what each council governs, what decisions
require it, who constitutes it in a solo-founder phase (the answer is honest about
what "council" means for a single founder with AI-assisted architecture), and what
the decision authority matrix looks like across the constitutional hierarchy
(Zuka-Bible 2, Book I Part C, Ch.11.2).

---

## The Constitutional Hierarchy (from Zuka-Bible 2, Part C, Ch.11.2)

```
Level 0  —  Foundational Constitution
             (Book I Parts A-E; highest authority)
Level 1  —  Domain Constitutions
             (Volumes II-VI, VIII — entity models, domain rules)
Level 2  —  Governance & Intelligence
             (Volumes IX-XIV — Twin/Agent governance, DRL/CRL framework)
Level 3  —  Ecosystem Expansion
             (Volumes XV-XIX — Opportunity, Community OS, Docking)
Level 4  —  Survival & Protection
             (Volume XVIII — Legal & Governance; Volume XIII — Trust)
Level 5  —  Experience Layer
             (Volume XXII — Product Constitution)
Level 6  —  Platform Kernel
             (Volume XI — Infrastructure; Volume XX — Data Readiness)
Level 7  —  Implementation
             (Engineering decisions within approved patterns)

CONFLICT RULE (Zuka-Bible 2, Book I Part C, Ch.11.3):
When conflict exists, higher constitutional authority prevails.
```

---

## Innovation Zones (from Zuka-Bible 2, Book I Part C, Ch.13)

Every decision sits in one of four zones that determine which council reviews it:

```
IMMUTABLE   — Cannot change without constitutional amendment
              Examples: Vision, Core Entities, Founder Red Lines, ADRs 001-008
              Threshold: Founder decision + constitutional amendment process

STABLE      — May evolve through governance council review
              Examples: Trust policies, community policies, domain ADRs 009-014
              Threshold: Relevant council review + founder approval

ADAPTIVE    — Changes through normal operational governance
              Examples: Discovery algorithms, pricing mechanics, operational ADRs
              Threshold: Architecture Council review

EXPERIMENTAL — High innovation space, highest rate of change
              Examples: AI Twins, Agents, Predictive Systems, ADRs 019-020
              Threshold: Architecture Council review; outcomes feed future ADRs
```

---

## Five Councils — Full Specification

---

### Council 1 — Architecture Council

**Constitutional source:** Zuka-Bible 2, Book I Part D, Ch.19 (named);
Chapter 21 (ADR governance assigned here)

**Remit:**
The Architecture Council is the primary governing body for every structural decision
about ZUKA's platform. It owns the canonical volumes, the ADR registry, and the
DRL Activation Contract process.

**Decisions it governs:**

```
MUST review (all zones):
  - Any new ADR proposal (all zones)
  - Any DRL Activation Contract (Volume XX, Ch.10) — the council
    is the reviewing body for Sections A-F of the contract before
    founder sign-off is triggered
  - Any new ConnectorContract (Volume XIX, Ch.4, Step 4 Technical Review)
  - Any new canonical volume or material volume amendment
  - Any Stage transition decision (Volume XI, Ch.5 triggers)
  - Any proposal to move a capability between innovation zones

DEFERS to other councils:
  - Trust-specific decisions → Trust Council
  - Security-specific decisions → Security Council
  - Legal/market-specific decisions → Governance Council
  - Constitutional evolution → Evolution Council
```

**Membership (solo-founder phase):**
The founder constitutes the Architecture Council, supported by this Bible as
the primary reference document. As the team grows, the first engineering hire
joins the Architecture Council. The AI architect (Claude, in this session's
context) serves as the architecture review function — raising conflicts,
naming precedents from the canonical volumes, and drafting impact analyses —
but the founder holds all decision authority. This is not a limitation to be
apologised for; it is the correct model for a pre-product company. The council
structure creates the habit and the documented process before the team arrives.

---

### Council 2 — Trust Council

**Constitutional source:** Zuka-Bible 2, Book I Part D, Ch.19;
Authority Matrix (Trust Council)

**Remit:**
The Trust Council governs all decisions affecting the trust architecture —
scoring models, fraud detection thresholds, appeals processes, and any product
feature that surfaces or consumes trust data. It is the specific council cited
in ADR-014 (revocation is always human) as the required reviewer.

**Decisions it governs:**

```
MUST review:
  - Any change to TrustDimension weights or composition
    (Volume III, Ch.15)
  - Any new fraud detection signal type (Volume XIII, Ch.8)
    or threshold adjustment
  - Any automated trust-adjacent action that approaches
    ADR-014's boundary (revocation is always human)
  - Any Trust Pyramid level change or addition
    (Volume XIII, Part II)
  - Any change to the Appeals mechanism (Volume XIII, Part VI)
  - Any Product feature that surfaces a trust score, badge,
    or reputation label to any person or community

CANNOT authorise independently:
  - Changes to ADR-002 (Trust cannot be purchased)
    → requires Immutable zone threshold
  - New Trust-domain DRL threshold values
    → additionally requires Architecture Council review
    (since DRL is Architecture Council's primary domain per ADR-008)
```

**Membership (solo-founder phase):**
The founder + any designated trust/safety lead (when hired).
At MVP, the Trust Council and Architecture Council are functionally
the same person — the founder — but they are kept conceptually distinct
because their review frameworks differ: the Architecture Council asks
"is this structurally correct?"; the Trust Council asks "is this safe
and fair for the people affected by it?"

---

### Council 3 — Security Council

**Constitutional source:** Zuka-Bible 2, Book I Part D, Ch.19;
Authority Matrix (Security Council)

**Remit:**
The Security Council governs data security, access control, and the platform's
response to security incidents — distinct from trust (which governs platform
behaviour toward participants) and governance (which governs legal compliance).

**Decisions it governs:**

```
MUST review:
  - Any new data access pattern that grants a system or person
    access to data they did not previously have (new read scope)
  - Any change to the authentication model (OTP, device
    fingerprinting per Volume III, Ch.7-8)
  - Any security incident response that involves data access
    or potential exposure
  - Any connector that transmits personal data externally
    (Volume XIX, Ch.4, Step 2 — Privacy Review — is a
    Security Council function alongside the Governance Council)
  - Penetration test findings and their remediation plans

DEFERS to:
  - Governance Council: for data protection legal compliance
    questions arising from security incidents
  - Trust Council: for fraud detection architecture
    (the Security Council governs external attackers and data
    breaches; the Trust Council governs internal gaming and
    manipulation by platform participants)
```

**Membership (solo-founder phase):**
The founder, supported by the designated technical security lead once hired.
Security Council reviews at MVP may be a lightweight checklist applied by
the founder before any new connector or access pattern goes live —
the formality of the process matters more than the headcount.

---

### Council 4 — Governance Council

**Constitutional source:** Zuka-Bible 2, Book I Part D, Ch.19;
Authority Matrix (Governance Council)

**Remit:**
The Governance Council governs legal compliance, market-specific Legal Addenda
(Volume XVIII, Part V), dispute resolution processes (Volume XVIII, Part II),
and the Legal Review Queue (Volume XVIII, Ch.5). It is the council that bridges
architecture and law.

**Decisions it governs:**

```
MUST review:
  - Any Legal Addendum (Volume XVIII, Appendix B) before activation
  - Any Privacy Impact Assessment (Volume XVIII, Ch.10) where
    Step 3 = 'high' risk
  - Any Founder Review Queue item before it proceeds to the
    Architectural Impact Assessment and Founder Decision stages
    (Volume XVIII, Ch.5)
  - Any cross-market data transfer connector (Volume XIX, Ch.4,
    Step 3 — Legal Addendum check)
  - Any community dispute escalated to Preserve-and-Freeze
    status (Volume XVIII, Part II) where legal_hold_flag is
    being considered

DEFERS to:
  - Architecture Council: for technical implications of legal
    compliance requirements
  - Trust Council: for trust-scoring implications of legal decisions
  - External legal counsel per market: for the actual legal content
    of any addendum or compliance determination
    (the Governance Council does not replace legal counsel —
    it is the internal body that coordinates with counsel and
    ensures architectural decisions reflect legal requirements)
```

**Membership (solo-founder phase):**
The founder + qualified legal counsel per market, engaged as needed.
The Governance Council is the one council where external membership
(legal counsel) is constitutionally required for certain decisions,
even at MVP. The founder cannot substitute their own judgment for
qualified legal advice on DPPA 2019 compliance questions (Volume XVIII,
Ch.8 is explicit on this).

---

### Council 5 — Evolution Council

**Constitutional source:** Zuka-Bible 2 Authority Matrix (Evolution Council);
Book I Part D, Ch.17-20 (Innovation Doctrine, Architectural Evolution Doctrine,
Constitutional Amendment Process)

**Remit:**
The Evolution Council governs the process of moving the platform between
Innovation Zones and managing the Constitutional Amendment Process itself.
It is the meta-governance body — it does not govern any specific domain, but
it governs how the governance of all domains changes over time.

**Decisions it governs:**

```
MUST review:
  - Any proposal to move an ADR from one zone to another
    (e.g., a previously Stable ADR becoming Immutable, or an
    Experimental ADR graduating to Stable after being proven)
  - Any constitutional amendment proposal (Volume XVIII,
    Ch.20's six-step amendment process is the Evolution
    Council's primary procedure)
  - The annual review of the Dormant Feature Register
    (consolidating the register, confirming graduation
    conditions, removing stale entries)
  - Any proposal to create a new canonical volume beyond
    Volume XXII (the current highest-numbered volume)
  - Any proposal to add a new Innovation Zone category

CANNOT authorise independently:
  - Constitutional amendments to Immutable zone principles
    → require Founder decision as the final authority
  - Removal of any ADR from the registry
    → ADRs are never deleted (per the registry's own
    append-only principle); they may be superseded by a
    new ADR but the original entry remains with a
    SUPERSEDED_BY annotation
```

**Membership (solo-founder phase):**
The founder, with this Architecture Bible serving as the standing reference
document that the Evolution Council consults. As the platform grows, the
Evolution Council is the body most likely to expand first — it benefits
from diverse perspectives (engineering, legal, community leaders) precisely
because its job is to govern change rather than to execute it.

---

## Decision Authority Matrix

```
DECISION TYPE                               ARCH  TRUST  SECURITY  GOV  EVOL  FOUNDER
─────────────────────────────────────────────────────────────────────────────────────
New ADR (any zone)                          REQ   OPT    OPT       OPT  OPT   REQ*
DRL Activation Contract (DRL 0-3)           REQ   OPT    OPT       OPT   —     —
DRL Activation Contract (DRL 4+)            REQ   OPT    OPT       OPT   —    REQ
New ConnectorContract (operational prereq)  REQ    —     REQ       REQ   —    REQ
New ConnectorContract (capability-ext)      REQ    —     REQ       REQ   —    REQ
New fraud signal type                       OPT   REQ    OPT        —    —    OPT
Trust score model change                    REQ   REQ     —         —    —    REQ
Legal Addendum activation                   OPT    —     OPT       REQ   —    REQ
PIA (high-risk processing)                  OPT    —     REQ       REQ   —    REQ
Preserve-and-Freeze activation              OPT   OPT    OPT       REQ   —    OPT
Constitutional Amendment (Stable zone)      REQ   OPT    OPT       OPT  REQ  REQ
Constitutional Amendment (Immutable zone)   REQ   REQ    REQ       REQ  REQ  REQ
New canonical volume                        REQ    —      —         —   REQ  REQ
Innovation Zone reclassification             —     —      —         —   REQ  REQ

REQ = Required review before decision proceeds
OPT = Should be consulted; may proceed without if time-sensitive
* = Required for Immutable zone ADRs; optional for Experimental zone
```

---

## The Reality of Solo-Founder Governance

This framework is specified for the platform ZUKA is becoming, not only for
the single-founder company ZUKA currently is. The honest statement of how this
works in practice today:

```
The founder IS all five councils simultaneously.

The value of this framework at MVP is NOT that five different groups of
people are involved in each decision. The value is that the QUESTION each
council would ask is documented and must be answered before any significant
decision proceeds — even when the same person answers all five questions.

"Does this conflict with any canonical volume?" (Architecture Council)
"Is this safe and fair for affected participants?" (Trust Council)
"Does this create a new security exposure?" (Security Council)
"Does this comply with applicable law?" (Governance Council)
"Does this require a constitutional amendment?" (Evolution Council)

A founder who can answer all five questions confidently, with documented
reasoning, is doing better governance than a company with five actual
councils that rubber-stamp decisions without genuine review.

When the first hire joins: the Architecture Council gets a second member.
When the first trust/safety hire joins: the Trust Council gets a second member.
When legal counsel is formally engaged: the Governance Council gains its
constitutionally-required external member.

The councils are real from day one. Their headcount grows as the team does.
```

---

> **ZUKA Architecture Bible**
> Governance Council Framework
> Constitutional source: Zuka-Bible 2, Book I Parts C-D
> Operational source: Volumes XI, XII, XVIII, XX
> Built in Kampala. Built for Africa. Built for the world.
