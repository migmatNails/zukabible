# ZUKA Architecture Bible
# Volume XVIII — Legal & Governance Architecture

> **Series:** ZUKA Architecture Bible
> **Volume:** XVIII of N
> **Status:** Canonical — establishes constitutional frameworks and procedures;
>              explicitly NOT a substitute for legal counsel in any market
> **Depends on:** The full Bible to date (Volumes I-XX), particularly Volume VI
>                  (community governance disputes), Volume XIII (individual-level
>                  Appeals mechanism), Volume XII (data protection references),
>                  Volume XX (DRL Activation Contract governance)
> **Required by:** Every future volume or capability touching: community disputes,
>                  cross-market data compliance, Knowledge Federation approval,
>                  the Connector Framework, and institutional-scale succession

---

# Preface: What This Volume Can and Cannot Do

This volume has been deferred across thirteen prior volumes for a reason that deserves to be stated plainly in its opening: architecture can specify a framework, but it cannot substitute for legal advice. This volume does the former rigorously and explicitly refuses to do the latter.

**What this volume DOES specify:**
- The constitutional framework for institutional-scale disputes that Volume XIII's individual-conclusion Appeals mechanism was never built to resolve
- ZUKA's governance role definition — where the platform preserves and freezes vs. where it may have affirmative obligations
- The process architecture for how Founder Review Queue items move to decisions
- The Privacy Impact Assessment procedure
- The Legal Addenda Framework — the structure in which market-specific legal specifications attach to this volume's constitutional base without requiring a new Bible volume per market

**What this volume DOES NOT do:**
- Give legal advice for any specific market
- Make determinations about what any specific law requires
- Replace qualified legal counsel in Uganda, Kenya, Tanzania, or any other market
- Resolve Knowledge Federation or platform-identity questions (those remain in the Founder Review Queue)

Every section touching jurisdiction-specific law carries this explicit note: *"This section identifies the applicable law. The specific compliance obligations require qualified legal counsel and are documented in the relevant Legal Addendum (Part V), not specified here."*

---

# Part I — The Governance Constitution

---

## Chapter 1 — Three Principles

```
PRINCIPLE 1 — ZUKA IS INFRASTRUCTURE, NOT ARBITER
  ZUKA's role in any dispute between community members,
  between community leadership factions, or between a
  community and an individual is to PRESERVE and FREEZE,
  not to judge and decide.

  This extends Volume VI, Chapter 28's partial answer
  ("ZUKA explicitly does NOT position itself as an
  arbiter") into a general constitutional rule applying
  to ALL dispute types.

PRINCIPLE 2 — DATA IS SACRED, EVEN IN DISPUTES
  No dispute resolution process may result in the unilateral
  deletion, editing, or suppression of data from any party's
  record without the append-only correction mechanism
  established in Volume XIII, Chapter 9.

  This is Volume VII's Temporal Truth Principle applied
  specifically to the adversarial case — the moment the
  temptation to "clean up" a record is highest.

PRINCIPLE 3 — GOVERNANCE AUTHORITY IS EARNED AND LAYERED
  Per Volume VI, Part XIV's four-layer governance model
  and Volume XIV's CRL framework: governance authority is
  earned through readiness principles. No external party
  — not a regulator, not a legal process, not a well-
  resourced actor — gains governance authority over a
  community's data through pressure alone. Authority over
  data requires ZUKA's own consent architecture and, where
  applicable, a valid legal process through an appropriate
  jurisdiction.
```

---

## Chapter 2 — The Preserve-and-Freeze Protocol

```typescript
PreserveAndFreezeRecord {
  protocol_id:              UUID
  triggered_at:                Timestamptz
  triggered_by:                   TriggerSource
  // 'internal_dispute_flag' | 'legal_process_received' |
  // 'governance_violation_detected' | 'founder_decision'

  scope: {
    affected_community_id:        UUID
    affected_person_ids:             UUID[]
    affected_asset_types:               FrozenAssetType[]
    // 'governance_authority'  — no new binding decisions
    // 'archive_modification'  — no new writes; reads always
    //                           permitted
    // 'financial_authority'   — no new Treasury disbursements
    // 'membership_changes'    — no new admissions/expulsions;
    //                           existing members retain access
    //
    // NEVER frozen:
    //   Member read access to own data
    //   Existing member ability to attend gatherings
    //   Memory creation
    //   Individual data portability (Vol XII Ch.11)
  }

  operations_continuing_during_freeze: {
    member_read_access:              true  // always
    gathering_attendance:                true  // always
    memory_creation:                     true  // always
    day_to_day_operational_decisions:
      'leadership_on_record_at_freeze_time'
    // Last known legitimate leadership continues day-to-day
    // operational decisions; freeze blocks NEW governance
    // changes, not existing governance execution
  }

  resolution_path: {
    method:                          ResolutionMethod
    // 'community_self_resolution' — parties reach agreement;
    //   ZUKA unfreezes on receipt of signed resolution
    // 'external_mediation' — mediator's confirmed resolution
    // 'legal_process' — binding court or regulatory order;
    //   ZUKA complies using append-only correction (Vol XIII)
    // 'time_based_default' — no resolution after maximum
    //   freeze period; see Chapter 6

    maximum_freeze_period_days:          Integer
    resolution_confirmed_at:                Timestamptz
    resolution_by:                             UUID
    // or 'external_mediator' | 'court_order' | 'time_default'
  }

  legal_hold_flag:                          Boolean
  // TRUE when a valid legal process (subpoena, court order,
  // regulatory demand) has been received and verified.
  // When true:
  //   - Maximum freeze period suspended until legal process
  //     concludes or is withdrawn
  //   - Relevant Legal Addendum (Part V) activated
  //   - No data may be deleted from any affected record
  //     regardless of any other platform policy
}
```

---

# Part II — Dispute Type Registry

---

## Chapter 3 — The Six Institutional Dispute Types

```
TYPE 1 — COMMUNITY FRAGMENTATION (Vol VI Ch.27)
  Two or more factions each claiming to represent the
  original community, with no internal consensus.

  Freeze scope: governance, archive, financial, membership
  Maximum period: 90 days
  Default resolution: Apply the community's own configured
    CommunitySuccessionPolicy (Vol VI Ch.25). If none
    configured: Archive → Archivist; financial assets →
    frozen indefinitely pending legal process.
  ZUKA's role: PRESERVE AND FREEZE only.

TYPE 2 — LEADERSHIP CONTEST (Vol VI Ch.28)
  Contested succession — two or more parties claim
  legitimate leadership authority unresolvable by the
  community's own governance model.

  Freeze scope: contested governance only; archive NOT
    frozen (Archivist continues); financial if contested
  Maximum period: 60 days
  Default resolution: Apply the community's governance
    model decision_model (Vol VI Ch.16) as configured:
    elder_consensus → Elder-role holders decide;
    founder_authority → Founder-role holder decides;
    membership_election → ZUKA surfaces election to all
    active members.

TYPE 3 — ARCHIVE OWNERSHIP DISPUTE (Vol VI Ch.30)
  Disagreement over Community Archive content visibility,
  accuracy, or control — independent of other disputes.

  Freeze scope: archive_modification only. All other
    operations continue normally.
  Maximum period: 30 days
  Default resolution: Archivist role holder's most
    recent documented decision is authoritative. If no
    Archivist designated: Archive remains frozen
    indefinitely until resolution or legal process.

TYPE 4 — FINANCIAL ASSET DISPUTE
  Contested disbursement, fundraising allocation, or
  financial authority in a leadership transition.

  Freeze scope: financial_authority only
  Maximum period: 90 days
  Default resolution: Per applicable market law (see
    Legal Addenda, Part V). ZUKA holds frozen assets
    until a legal authority with appropriate jurisdiction
    provides direction. ZUKA never redistributes disputed
    financial assets using its own authority alone.

TYPE 5 — POLITICAL CAPTURE (Vol VI Ch.32)
  Community governance captured by a faction using
  ZUKA's own governance tooling to entrench capture.
  Named in Volume VI as the "least-resolved risk."

  Detection signals (observable from platform data):
    - governance_change_policy violated: changes made
      without required notice_period or ratification
    - Membership suppression: ≥ 20% of previously active
      members removed/suspended in 30 days without
      visible community-wide process
    - Financial anomaly: disbursements inconsistent with
      prior financial_transparency_policy post-change
    - Appeal volume: ≥ 5 appeals (Vol XIII) citing the
      same governance decision within 30 days

  When ≥ 2 signals fire simultaneously: flag for
    HUMAN internal review. Detection is automated;
    investigation and response are always human-initiated.

  Freeze scope (if review confirms capture):
    governance, financial, membership
  Maximum period: NO DEFAULT — indefinite.

  Rationale for no default: The other five types can
  be resolved by applying a configured governance rule
  in the parties' absence. Political capture is the case
  where ZUKA's governance tooling may ITSELF have been
  used to configure a rule that favours the captured
  party — meaning applying the configured rule as a
  default would reinforce the capture. ZUKA holds and
  waits for external resolution.

TYPE 6 — CROSS-COMMUNITY DISPUTE
  A dispute between two distinct Community entities over
  shared assets, federation relationships (Vol VI Part
  XIX), or joint event organiser relationships.

  Freeze scope: ONLY shared or contested assets.
    Each community's own independent operations continue.
  Maximum period: 60 days
  Default resolution: Contested shared assets revert to
    the last uncontested state per event history (Vol VII).
    No unilateral asset transfer to either community.
```

---

# Part III — The Arbitration Role Framework

---

## Chapter 4 — When ZUKA Has Affirmative Obligations

```
CATEGORY A — VALID LEGAL PROCESS
  A valid legal process (court order, subpoena, regulatory
  demand) from a jurisdiction with appropriate authority
  creates a legal obligation to respond. This is compliance,
  not arbitration.

  Protocol:
    1. Verify the process is genuine and jurisdictionally
       valid for ZUKA's operations or data in that market
    2. Activate legal_hold_flag on PreserveAndFreezeRecord
    3. Engage qualified legal counsel for the relevant
       market (per Legal Addenda, Part V)
    4. Comply using append-only corrections only (Vol XIII)
    5. Document all actions in the PreserveAndFreezeRecord
       with full DomainEvent trail

  ZUKA never proactively shares data beyond the scope
  of the legal process, even to assist one party.

CATEGORY B — CLEAR HARM TO A PROTECTED CLASS
  Where platform data clearly shows a protected class being
  harmed per applicable law — not contested, but objectively
  verifiable (e.g., documented exclusion pattern on legally
  protected grounds) — ZUKA may have an affirmative legal
  obligation.

  Trigger: Legal counsel review confirms the obligation.
  NEVER self-triggered by automated systems.

CATEGORY C — CHILD SAFETY
  Where data or behaviour implicates the safety of a Child
  entity (Vol II §3.1; Vol III Ch.11), applicable child
  protection law overrides the default preserve-and-freeze
  posture. ZUKA has an affirmative protective obligation.

  This is the ONLY category permitting immediate affirmative
  action without a served legal process — child protection
  law in most jurisdictions imposes proactive platform
  obligations, not merely reactive ones.

  Child safety responses are immediate operational
  responses, not subject to the 30-120 day freeze periods
  of other dispute types.

EVERYTHING ELSE: preserve-and-freeze, never arbitrate.
```

---

## Chapter 5 — The Legal Review Queue Process

The Founder Review Queue (Dormant Register) currently holds four items. This chapter specifies how they move from "held" to "decided."

```
STEP 1 — LEGAL BRIEF PREPARATION
  Before any item can be decided, a Legal Brief is prepared
  identifying: every applicable law in every active market,
  specific compliance obligations and risks per law, and
  implementation choices ZUKA must make to comply.
  Prepared by or reviewed by qualified legal counsel in
  each applicable market.
  The Legal Brief is NOT the decision — it is the
  information required to make an informed decision.

STEP 2 — ARCHITECTURAL IMPACT ASSESSMENT
  The Architecture Bible team prepares an assessment covering:
    - Which volumes would be affected if the item is adopted
    - Which Dormant Features would be unlocked or blocked
    - Which DRL Activation Contracts need revisiting
    - What new Feature Seeds would be required

  Quick assessment per queued item:

  KNOWLEDGE FEDERATION (Vol VIII Ch.24):
    Volumes affected: VIII, XI, XII
    Dormant Features unlocked: Memory Federation, full
    Connector Framework spec
    New Feature Seeds required: per-platform connector
    contracts (Google Photos API, Meta Graph API, etc.)
    DRL prerequisite: all Knowledge Federation features
    remain DRL 0 until decision is made AND DRL thresholds
    (Vol XX Ch.7) independently cleared

  PLATFORM-IDENTITY REFRAME (Vol XVII Ch.5):
    Volumes affected: I, XVII, README
    Dormant Features: none (brand decision only)
    Architecture verdict: already closed as architectural
    question; only brand/marketing materials need updating

  FULL DOCKING / CONNECTOR FRAMEWORK (Vol XI Ch.10):
    Volumes affected: XI, XIX (still-unwritten)
    New Feature Seeds: all external connector types beyond
    the SafeBoda/payment minimum
    DRL prerequisite: Infrastructure Stage 2 trigger
    (Vol XI Ch.5) must have fired

  LEGAL & GOVERNANCE per-market addenda (this volume):
    Volumes affected: this volume's Appendix B slots
    Architecture: no new volumes required

STEP 3 — FOUNDER DECISION
  With Legal Brief and Architectural Impact Assessment
  in hand, the founder makes an explicit documented decision:
    ADOPT  — feature/reframe adopted; Bible updated
    DEFER  — item remains with updated "next review date"
             (not open-ended — a date, not "eventually")
    REJECT — item permanently removed with reason documented
             in this volume's Appendix A (Decision Log)

STEP 4 — BIBLE UPDATE
  On ADOPT decisions:
    Architectural Impact Assessment is executed
    DomainEvent FounderReviewItemDecided is filed with
    decision, rationale, and assessor identity
    Appendix A (Decision Log) records permanently

  The Decision Log ensures decisions are traceable per
  Vol VIII Ch.20's Knowledge Provenance principle —
  applied to the governance process itself.
```

---

## Chapter 6 — Default Resolution Timelines

```
DISPUTE TYPE             MAX FREEZE   DEFAULT RESOLUTION
Community Fragmentation  90 days      Apply configured SuccessionPolicy
Leadership Contest       60 days      Apply governance decision_model
Archive Ownership        30 days      Archivist's last documented decision
Financial Asset          90 days      Per applicable market law (Legal Addenda)
Political Capture        NO DEFAULT   External resolution only
Cross-Community          60 days      Revert to last uncontested state

NON-DEFAULT EXTENSIONS permitted only when:
  - legal_hold_flag is active (legal process received)
  - Founder explicitly authorises with documented reason
  - All disputing parties jointly request in writing

No extension is ever automatic. Each requires a new decision
and a new DomainEvent in the PreserveAndFreezeRecord.
```

---

# Part IV — Data Protection Compliance Framework

---

## Chapter 7 — Three-Layer Compliance Architecture

```
LAYER 1 — CONSTITUTIONAL FLOOR (this Bible, universal)
  Privacy protections applying everywhere, regardless of
  local law, as ZUKA's own constitutional commitments:
    - Vol III Part VI's visibility model: all markets
    - Vol IX's TwinConsentRecord + revocation: all markets
    - Vol VIII Ch.24's Knowledge Federation prohibition
      without explicit review: all markets
    - Vol XII Ch.11's personal data export right: all markets
    - Vol XIII Ch.9's append-only correction: all markets
  When a market's law permits LESS protection than Layer 1,
  Layer 1 still applies — ZUKA never reduces its floor
  to match a permissive market minimum.

LAYER 2 — MARKET-SPECIFIC FLOOR (per Legal Addenda)
  Jurisdiction-specific obligations in addition to Layer 1
  where they are stricter. Specified per market in the
  Legal Addendum for that market (Part V).

LAYER 3 — PRODUCT-SPECIFIC CHOICES
  Where law permits discretion, product and engineering
  teams make specific choices documented in the relevant
  volume or specification, not here.
```

---

## Chapter 8 — Uganda: The Baseline Market

Uganda's Data Protection and Privacy Act 2019 (DPPA 2019) is the foundational market-specific law for ZUKA.

*This section identifies the applicable law. The specific compliance obligations, procedures, and risk mitigations for the DPPA 2019 require qualified legal counsel and are documented in Legal Addendum 001 (Appendix B). That addendum is pending engagement of qualified legal counsel in Uganda.*

What this volume specifies about Uganda: the DPPA 2019 is the baseline against which ZUKA's Layer 1 constitutional commitments (Chapter 7) were originally designed. Where Layer 1 commitments directly satisfy DPPA 2019 requirements, this is confirmed in Legal Addendum 001. Where DPPA 2019 requires additional obligations beyond Layer 1, those are specified in the addendum.

---

# Part V — The Legal Addenda Framework

---

## Chapter 9 — How Legal Addenda Attach to This Volume

```
PER-MARKET LEGAL ADDENDUM TEMPLATE:

  Market:              [e.g., Kenya]
  Applicable law(s):       [e.g., Kenya Data Protection Act 2019]
  Legal counsel:               [name / firm]
  Addendum prepared:               [date]
  Addendum reviewed:                   [date]

  SECTION 1 — LAYER 2 OBLIGATIONS
    Obligations the market's law imposes beyond Layer 1

  SECTION 2 — CONSENT ARCHITECTURE ADJUSTMENTS
    Any adjustments to Vol IX's TwinConsentRecord or
    Vol III's privacy_settings required by local law

  SECTION 3 — DATA RETENTION REQUIREMENTS
    Market-specific retention periods per data category

  SECTION 4 — DATA LOCALISATION REQUIREMENTS
    Whether applicable law requires data to be stored
    within the country's borders; infrastructure implications
    (Vol XI Ch.8's storage layer choices)

  SECTION 5 — REGULATORY CONTACTS
    Applicable data protection authority; ZUKA's registered
    contact or representative in the market

  SECTION 6 — INCIDENT NOTIFICATION REQUIREMENTS
    Timeframes and procedures for breach notification per
    applicable law

  SECTION 7 — CROSS-BORDER DATA TRANSFER RULES
    Restrictions on transferring data from this market
    to other markets or data centers

ADDENDUM REGISTRY (to be populated as markets expand):

  Addendum 001   Uganda / DPPA 2019   [pending qualified
                                       legal counsel engagement]
  Addendum 002   Kenya / KDPA 2019    [pending market entry]
  ...

The addendum registry is sparse at this writing by design —
an architecture document that fills in compliance details
without legal counsel would be worse than one that
honestly names the gap.
```

---

# Part VI — Privacy Impact Assessment Procedure

---

## Chapter 10 — When a PIA Is Required

A Privacy Impact Assessment is required for any capability that:
- Processes a data category not previously used by the platform
- Introduces a new processing PURPOSE for already-collected data
- Involves Knowledge Federation from an external system (if ever approved)
- Is classified as high-risk processing under any applicable market's law

```
PRIVACY IMPACT ASSESSMENT PROCEDURE v1.0

STEP 1 — IDENTIFY THE PROCESSING ACTIVITY
  What data types (specific fields)?
  From which entities (Vol II §3.1-§3.10)?
  For what purpose (new vs. extension of existing)?
  By which system (Twin, Agent, Engine, or direct query)?

STEP 2 — LEGAL BASIS REVIEW
  For each data type and purpose:
    What visibility level (Vol III Part VI) governs it?
    Does this require explicit consent beyond the standard
    TwinConsentRecord (Vol IX Ch.3)?
    Does the relevant Legal Addendum impose additional
    consent requirements for this data type?
  OUTCOME: 'no additional consent required' OR
            'explicit consent required' (specify what)

STEP 3 — RISK IDENTIFICATION
  Using the applicable Legal Addendum's Section 1:
    Is this high-risk processing per applicable law?
    Does this create a new profiling risk?
    Could this result in significantly different treatment
    of individuals based on their data?
  OUTCOME: risk level 'low' | 'medium' | 'high'

STEP 4 — RISK MITIGATION MEASURES
  For each risk:
    Technical measure (aggregation, k-anonymity, restricted
    access)?
    Architectural measure (DRL gating — inaccurate profiling
    at low DRL is itself a privacy risk)?
    Governance measure (human review, mandatory Provenance
    Record per Vol VIII Ch.20)?

STEP 5 — LEGAL COUNSEL REVIEW (if Step 3 = 'high')
  High-risk processing requires the relevant Legal Addendum
  holder to review Steps 1-4 before the DRL Activation
  Contract (Vol XX Ch.10) is even initiated.
  This PIA review is an additional gate BEFORE the DRL gate.

STEP 6 — PIA RECORD
  File DomainEvent: PrivacyImpactAssessmentCompleted
  Append-only per Vol VII Temporal Truth Principle.
  PIA record becomes an attachment to the DRL Activation
  Contract (Vol XX Ch.10) where required.

STEP 7 — ONGOING MONITORING
  High-risk processing requires 6-month scheduled PIA
  review while the capability is active — confirming
  the risk landscape has not materially changed (new
  market entered, data volume increased significantly).
```

---

# Part VII — MVP Scope

---

## Chapter 11 — What to Build First

```
BUILD (MVP):

  ✅ PreserveAndFreezeRecord schema (Ch.2) — built now;
     at MVP scale no institutional disputes are expected
     but the schema must predate the disputes it handles

  ✅ Six Dispute Type definitions (Ch.3) — as configuration
     in the dispute-handling system, with scope and maximum
     freeze period for each type

  ✅ legal_hold_flag on PreserveAndFreezeRecord — must exist
     before ZUKA operates at any scale; a valid legal process
     could arrive at any time

  ✅ PIA Procedure document template (Ch.10) — in the
     repo's governance folder, the same rationale as the
     DRL Activation Contract form (Vol XX Ch.10)

  ✅ Legal Addendum 001 (Uganda/DPPA 2019) — initiated
     with qualified legal counsel engagement [ACTION ITEM:
     not an engineering build, a legal-business action
     that must precede significant user growth]
```

```
DO NOT BUILD YET:

  ❌ Automated political capture detection beyond signal
      logging — per Chapter 3's TYPE 5 specification,
      investigation and response are always human-initiated

  ❌ Legal addenda for expansion markets until those
      markets are actually entered
```

**The single most important MVP action in this volume is not an engineering task:** engage qualified legal counsel in Uganda to prepare Legal Addendum 001. The DPPA 2019 applies from the first user, not from some future threshold, and this architecture document is not a substitute for that engagement.

---

# Part VIII — What This Volume Explicitly Does Not Resolve

---

## Chapter 12 — The Honest Remainder

```
NOT RESOLVED BY THIS VOLUME:

  Knowledge Federation decision (Vol VIII Ch.24)
    Chapter 5 specifies the PROCESS for deciding;
    it does not make the decision. Remains in the
    Founder Review Queue.

  Intellectual Property Architecture
    Photographer IP, organiser rights, participant
    rights over memories and recordings — the commercial
    framework for who owns what when a Creator (Vol II
    §3.5) produces content at a Gathering. Vol I's
    "the city's data belongs to the city" and Vol V's
    "memories are meaning" establish the constitutional
    principle; the specific IP framework requires legal
    specification per market. Legal Addendum item.

  Employment Law and Contractor Classification
    Whether platform-facilitated Bookings create
    employment obligations (relevant to Vol XV's Creator
    Economy Engine) in specific markets. Per-market
    Legal Addenda item.

  Tax and Withholding Obligations
    As Payouts scale, withholding obligations will arise
    in most markets. Legal Addenda item.

  Competition Law
    As the Knowledge Graph moat (Vol XII Ch.1) matures,
    competition regulators may take interest. A legal risk
    to monitor, not an architecture risk to specify.
```

---

# Volume XVIII Summary

```
Governance Constitution:
  3 principles: ZUKA as infrastructure not arbiter;
  data is sacred even in disputes; governance authority
  is earned and layered

Preserve-and-Freeze Protocol:
  Full PreserveAndFreezeRecord schema — scope, operations
  continuing, resolution paths, legal_hold_flag
  Narrowest possible scope: member read access, gathering
  attendance, and memory creation NEVER frozen

Six Dispute Types:
  Community Fragmentation (90d), Leadership Contest (60d),
  Archive Ownership (30d), Financial Asset (90d), Political
  Capture (NO DEFAULT), Cross-Community (60d)
  Political Capture: no time-based default because
  applying a potentially-captured governance rule as a
  default would reinforce the capture

Three Affirmative Obligation Categories:
  Valid Legal Process, Clear Harm to Protected Class,
  Child Safety — only Child Safety permits immediate
  action without a served legal process

Legal Review Queue Process:
  4-step: Legal Brief → Architectural Impact Assessment
  → Founder Decision → Bible Update
  Decision Log (Appendix A) permanently records every
  decision with rationale — Knowledge Provenance applied
  to the governance process itself

Data Protection — Three-Layer Model:
  Constitutional Floor (universal), Market-Specific
  Floor (per Legal Addenda), Product Choices (per volume)
  Layer 1 never reduced to match a permissive market

Legal Addenda Framework:
  Per-market addendum template specified; registry
  established but sparse by design; Addendum 001
  (Uganda/DPPA 2019) pending qualified legal counsel

Privacy Impact Assessment:
  7-step procedure; high-risk processing requires
  legal counsel review BEFORE the DRL Activation
  Contract process even begins

MVP Scope:
  5 items (schema, dispute types, legal_hold_flag,
  PIA template, Addendum 001 initiation)
  Critical non-engineering action named plainly

Honest Remainder (Ch.12):
  Knowledge Federation, IP, employment law, tax,
  competition law — named as out of scope, belonging
  to counsel-prepared addenda
```

This volume establishes what architecture can establish about legal and governance questions. The remaining work belongs to lawyers and founders, not architects. This Bible has done its part.

---

# What Volume XIX Must Address

Infrastructure & Ecosystem / Docking Volume — explicitly deferred by Volume XI, Chapter 10 and named as still-owed across three subsequent volumes. Volume XIX must:

1. Complete the Connector Framework that Volume XI, Chapter 9 gave only minimum shape for — full CapabilityDeclaration taxonomy, ConnectorContract versioning, capability approval process
2. Specify the SafeBoda connector as a complete ConnectorContract instance (not an example)
3. Specify payment processor connectors (Flutterwave primary; Africa's Talking for SMS)
4. Apply the Docking Principle operationally — what tests a proposed integration must pass
5. Define the Knowledge Federation connector architecture — not the adoption decision (Founder Review Queue), but the technical architecture that would be used IF the decision is made, so that the decision is not blocked by missing architecture

---

> **ZUKA Architecture Bible**
> Volume XVIII — Legal & Governance Architecture
> Built in Kampala. Built for Africa. Built for the world.
