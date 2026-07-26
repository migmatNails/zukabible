# ZUKA BIBLE V4 — VOLUME 08
# Identity OS & Identity Lifecycle

> **Part III — Core Operating Systems** · **Authority Level 2** · **Crown Jewel**
> Synthesises: BLACK Edition XXVIII, Canonical Volume III (Identity Architecture)

---

## Chapter 1 — The Constitutional Premise

Identity is the foundation of ZUKA. Everything is built on top of it:

```
Identity → Participation → Memories → Trust → Communities →
Opportunities → Governance

If Identity is weak, everything above it becomes weak.
```

### The Four Identity Principles

```
CONTINUITY   Identity is not a profile. It is a continuously evolving
             representation of a person across their whole life.
OWNERSHIP    Users own their identity. The platform stewards it.
PORTABILITY  Identity is never trapped. Users retain control of their data.
GROWTH       Identity evolves through participation, not form-filling.
```

---

## Chapter 2 — The Eight-Stage Identity Lifecycle

The spine of Identity OS. A person advances through these stages by participation,
never by payment:

```
Stage 0  VISITOR        Browsing, no account (zero-friction discovery)
Stage 1  REGISTERED     Phone + OTP complete; Person entity created
Stage 2  VERIFIED       Identity verified (ID/biometric via Smile Identity)
Stage 3  PARTICIPATING  First gatherings attended, RSVPs kept
Stage 4  TRUSTED        Trust earned across dimensions (Volume 11)
Stage 5  COMMUNITY      Active community membership and contribution
Stage 6  OPPORTUNITY    Eligible for and engaged in opportunities (Volume 19)
Stage 7  LEGACY         Multi-chapter participation; legacy preservation active
```

Stages map to the Identity CRL (Seed → Growing → Established → Trusted → Legacy)
and are surfaced as human-readable states, never as stage numbers.

---

## Chapter 3 — The Five Identity Domains

```
CORE IDENTITY      Name · contact methods · account metadata
SOCIAL IDENTITY    Communities · relationships · participation
TRUST IDENTITY     Reputation · reliability · contributions
ECONOMIC IDENTITY  Payments · purchases · earnings
MEMORY IDENTITY    Experiences · events · stories
```

Each domain has its own visibility and consent scope. A Person controls each
independently.

---

## Chapter 4 — The Five-Layer Identity Stack

```
LAYER 1 — VERIFICATION   What can be proven (phone, ID, biometric)
LAYER 2 — PERSONA        How a person presents in different contexts
LAYER 3 — REPUTATION     How a person is perceived (distinct from trust)
LAYER 4 — TRUST          Confidence earned through observed participation
LAYER 5 — LEGACY         What persists after active participation ends
```

---

## Chapter 5 — Verification Levels

```
L0  Anonymous          Visitor; no verification
L1  Phone / Email      OTP-verified phone (the baseline account)
L2  Verified           Government ID document (via Smile Identity connector)
L3  Enhanced           Biometric verification
L4  Trusted Identity   L3 + sustained trust history

Verification is progressive and EARNED, never purchased.
Verification is personal — it never transfers between Personas or via
salon/organisation affiliation. (A Person's verification flows to all their
Personas; it never flows from an employer to an employee.)
```

---

## Chapter 6 — Multi-Persona Architecture

```
A Person owns many Personas:
  Personal Persona      default; Memory creation, family relationships
  Professional Persona  career and business relationships
  Creator Persona       booking, portfolio, creative reputation
  Organiser Persona     gathering hosting, community leadership

Each Persona has independent: trust score · visibility · consent scope · tags.
The owning Person's verification level flows through to all Personas.
```

---

## Chapter 7 — The Identity Graph & Timeline

```
Identity is a graph, not a record:
  Nodes: Person · Community · Gathering · Venue · Opportunity · Memory
  Edges: ATTENDED · ORGANIZED · JOINED · CREATED · TRUSTED

Identity is modeled as history (the Identity Timeline):
  What changed? When? Why?
  Nothing important is overwritten. (Temporal Truth, Volume 05)
```

---

## Chapter 8 — Child Identity

```
A Child entity has different consent rules than a Person:
  Cannot independently consent to a Personal Twin
  Guardian consents on behalf, most-restrictive-scope only
  At majority, the Child→Person transition triggers a constitutional review:
    every guardian-consented data relationship must be re-affirmed,
    modified, or revoked by the now-adult Person
  A guardian-consented Twin not re-confirmed by the adult is auto-decommissioned
```

---

## Chapter 9 — Legacy Architecture

```
LegacySettings:
  memory_policy:  preserve_all | preserve_public | archive_family_only |
                  delete_on_death
  ai_twin_policy: default 'immediate_on_death' (opt-in to extend)
  steward_id:     designated Legacy Steward (Volume 11 stewardship trust)
  final_message:  optional recorded message

Default posture is PRESERVATION, not deletion. On a Person's transition to
Memorialised status, a MemorialRecord is created; the Memory Vault persists;
family retains read access; Memory creation by others continues.
```

---

## Chapter 10 — Identity Security & Governance

```
SECURITY (Identity is Crown Jewel #1, Volume 21):
  MFA · Device Trust · Session Risk Scoring · Account Recovery Controls

GOVERNANCE:
  Appeals · Verification Reviews · Correction Rights · Audit Trails
  One account per verified phone number (OTP-enforced)
  Device fingerprinting detects multi-account abuse
```

---

## Chapter 11 — Identity Intelligence (Dormant)

```
Future systems (all DRL 0, CRL Observing at this writing):
  Identity Historian · Identity Twin · Identity Timeline · Identity Insights

Experience translation rule:
  NEVER show "CRL = 4" or "Trust = 84"
  ALWAYS show "Trusted Contributor", "Established Organiser"
```

---

## Chapter 12 — Identity Domain Events

```
PersonCreated · PersonRegistered · PersonVerified · PersonUpdated ·
PersonMemorialised · PersonDeleted · PersonaMergeRequested ·
PhoneVerificationInitiated · PhoneVerificationCompleted · BadgeAwarded ·
BadgeRevoked (human only, ADR-014) · TrustGranted · TrustLost ·
VerificationApproved · TrustProfileSeeded
```

**Emitted-event provenance (registry completeness, S041):**
- `PersonRegistered` — aggregate `Person`. A sign-up self-provisioned a linked
  `person` row via the `register_person` RPC (F003). Payload is data-minimised
  (person_id + consent flags + levels only — no display_name/email/auth id;
  DPPA erasure cannot reach the append-only store). Emitted at
  `supabase/migrations/20260710090000_fn_register_person.sql:159`.

---

## Chapter 13 — Founder Rules

```
1. Identity is a journey.
2. Participation matters more than profiles.
3. Trust must be earned.
4. History must be preserved.
5. Identity should become more valuable over time.
```

---

> **Volume 08 — Identity OS & Identity Lifecycle** · Crown Jewel
> Transform a simple account into a trusted, evolving, portable representation
> of a person's participation in life.
