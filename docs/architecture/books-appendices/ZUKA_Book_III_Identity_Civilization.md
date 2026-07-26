# ZUKA Unified Architecture Bible
# BOOK III — IDENTITY CIVILIZATION

> **Constitutional Authority:** Level 1 — Domain Constitution
> **Source:** Canonical Volume III (Identity Architecture)
>              synthesised with Zuka-Bible 2, Book I (Identity Doctrine)
>              and Zuka-Bible 2, Book III (Identity Civilization)
> **Status:** Substantive — full Identity Stack specification

---

## Constitutional Premise

Identity is the foundation of all participation.

Without a reliable, honest Identity, no Trust can be earned, no Memory attributed,
no Community governed, no Opportunity matched, no Legacy preserved.

From Zuka-Bible 2, Book I Part E (Chapter 24):
*"Identity captures participation."*

---

## The Five-Layer Identity Stack (Volume III)

```
LAYER 1 — VERIFICATION
  What can be proven: phone number, government ID, biometric
  Components: PhoneVerification, Verification entity (§3.8)
  Tiers: basic_phone → phone_otp → id_document → biometric
  Rule: Verification is earned, never purchased (ADR-002 applied to identity)

LAYER 2 — PERSONA
  How a person presents in different contexts
  Components: Persona entity — personal, professional, creator, organiser
  Rule: A Person may have multiple Personas; each has its own trust score,
  visibility level, and consent scope

LAYER 3 — REPUTATION
  How a person is perceived by others
  Components: ReputationProfile, character_tags, tags
  Rule: Reputation must never be gamed or purchased, only earned (Volume VI, Ch.14)
  Distinction: Trust measures earned reliability; Reputation captures perceived value

LAYER 4 — TRUST
  Confidence earned through observed participation
  Components: TrustProfile, TrustDimensions (six — see below)
  Rule: Trust cannot be purchased (ADR-002); cannot be self-declared;
  computed exclusively from observed DomainEvents

LAYER 5 — LEGACY
  What persists after active participation ends
  Components: LegacySettings, LegacyVault, MemorialRecord
  Rule: Default posture is preservation, not deletion (Volume III, Part X)
```

---

## The Six Trust Dimensions (Volume III, Ch.15)

```
reliability        — does the person do what they commit to?
                     (RSVP kept, ticket honoured, booking completed)

authenticity       — are they who they say they are?
                     (Verification tier, real check-ins, no Sybil signals)

professionalism    — do they meet the standards of their role?
                     (Creator stats, organiser delivery_rate)

safety             — do others feel safe in their presence?
                     (Safety-related reviews, no dispute flags)

consistency        — is their behaviour stable over time?
                     (Trust Timeline — Volume VII, Ch.17)

community_standing — how do communities regard them?
                     (CommunityTrustProfile integration — Volume VI, Part XI)
```

These six dimensions are never collapsed into a single score for external display.
"Trust" is always contextual and multi-dimensional (ADR-003 applied to identity:
no simplification that would make a single number supersede the richer reality).

---

## The Trust Pyramid (Volume XIII, Part II)

The Pyramid describes WHEN each level of trust becomes assessable — a sequencing
model layered over, not replacing, the six-dimension model above:

```
Level 1 — IDENTITY TRUST
  When: immediately after phone verification
  Evidence: Verification tier, authentic profile signals

Level 2 — BEHAVIORAL TRUST
  When: after first gatherings attended and RSVPs kept
  Evidence: reliability + consistency dimensions

Level 3 — COMMUNITY TRUST
  When: after sustained community membership and contribution
  Evidence: community_standing dimension + CommunityTrustProfile

Level 4 — ECONOMIC TRUST
  When: after real financial commitments (ticket purchases, payouts received)
  Evidence: professionalism + financial_completion_rate

Level 5 — STEWARDSHIP TRUST
  When: after taking responsibility for others (Archivist, Family Vault
  steward, Legacy Steward, Community succession role)
  Evidence: steward-type roles with demonstrated responsibility
  Note: "Stewardship Trust" is an addition from Volume XIII, Part II —
  the first formal naming of a real but previously-unnamed concept
```

---

## Privacy Architecture (Volume III, Part VI)

```
VISIBILITY LEVELS (apply to all entities and Persona outputs):
  private         — only the person themselves
  family          — confirmed family members only
  community       — specific community membership
  trusted_network — manually curated connections
  public          — anyone on the platform

DATA USE FLAGS (per-category consent, independently toggleable):
  data_for_discovery        — appears in search and feeds
  data_for_recommendations  — used in Opportunity and matching engines
  data_for_b2b_products     — included in aggregate B2B outputs
  data_for_ai_training      — (never active without explicit opt-in)
```

The Privacy Architecture governs every data access decision across all seven
Knowledge Graphs. No Agent, Twin, or Historian may access data beyond the
visibility and consent scope granted by the owner Person entity.

---

## Multi-Persona Architecture (Volume III, Ch.9-11)

```
A Person → owns many Personas

Personal Persona:   default; governs Memory creation, family relationships
Professional Persona: governs career and business relationships
Creator Persona:    governs booking, portfolio, and creative reputation
Organiser Persona:  governs gathering hosting and community leadership

Each Persona has:
  - Independent trust score (persona_trust_score)
  - Independent visibility settings
  - Independent consent scope for Twin and Agent access
  - The owning Person's Verification tier flows through to all Personas
    (verification is personal, never persona-specific)
```

---

## Child Identity (Volume III, Ch.11)

```
A Child entity has different consent rules than a Person:
  - Cannot independently consent to a Personal Twin
  - Guardian may consent on behalf with most-restrictive scope only
  - Twin must re-confirm consent directly from the adult at majority
  - A Guardian-consented Twin not re-confirmed by the adult is
    automatically decommissioned after a defined grace period

The Child → Person transition is a constitutional lifecycle event:
  All guardian-consented data relationships must be reviewed
  The Person entity (now an adult) may affirm, modify, or revoke each one
```

---

## Legacy Architecture (Volume III, Part X)

```
LegacySettings {
  memory_policy:    MemoryPolicy
  // 'preserve_all' | 'preserve_public' | 'archive_family_only' |
  // 'delete_on_death'
  ai_twin_policy:   TwinLegacyBinding   // Volume IX, Ch.16
  // default: 'immediate_on_death' (requires explicit opt-in to extend)
  steward_id:       UUID | null         // designated Legacy Steward
  final_message:    TEXT | null         // optional recorded message
}

MemorialRecord {
  // Created when a Person entity transitions to Memorialised status
  memorial_id:      UUID
  person_id:        UUID
  memorialised_at:  Timestamptz
  memorialised_by:  UUID   // who triggered; or 'system' if automated
  public_tribute:   TEXT | null
  // The Memory Vault continues to exist; family members retain
  // read access; Memory creation by others continues
}
```

---

## Identity Domain Events (Volume II, Ch.9 registry)

```
PersonCreated               — new Person entity registered
PersonVerified              — verification tier advanced
PersonUpdated               — profile fields changed
PersonMemorialised          — transition to Memorialised status
PersonDeleted               — account deletion requested (soft-delete;
                              history preserved per Volume VII Temporal Truth)
PersonaMergeRequested       — user requested merging two Personas
PhoneVerificationInitiated  — OTP sent (Volume XIX, Ch.8 — Africa's Talking)
PhoneVerificationCompleted  — OTP confirmed
BadgeAwarded                — new Badge entity created
BadgeRevoked                — Badge revoked (always human — ADR-014)
TrustGranted                — trust-positive event recorded
TrustLost                   — trust-negative event recorded
VerificationApproved        — Smile Identity confirmed (Volume XIX, Ch.10)
```

---

## ADR Cross-References for Identity Domain

```
ADR-001  Relationships are first-class entities
ADR-002  Trust cannot be purchased (applies to all trust scoring)
ADR-003  Reality precedes AI (observed > declared)
ADR-004  Participation precedes prediction (DRL 1 FORBIDDEN: trust scoring)
ADR-007  Explainability is mandatory (every TrustProfile output has ProvenanceRecord)
ADR-009  Memory and Media are permanently separate entities
ADR-010  The canonical human entity is Person, not User
ADR-014  Revocation of any trust decision is always a human act
ADR-018  Data quality controls detect aggressively, correct conservatively
         (specifically: Person entity auto-merge is forbidden)
```

---

*Source: Volume III (Identity Architecture); Zuka-Bible 2, Book III*
*Authority: Level 1 — Domain Constitution*
*Next: Book IV — Memory Civilization*
