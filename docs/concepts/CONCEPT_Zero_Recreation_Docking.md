# ZUKA — Concept Document
# Zero-Recreation Docking (Evidence-Based Onboarding)

> **Type:** Feature Concept · **Tier:** 2 (buildable, post-MVP)
> **Status:** Proposed (pre-build) · **Authority:** Subordinate to ZukaBibleV4.
> Reconciles V7's Docking OS to Supabase-first + DPPA reality + V4 Trust (Vol 11).
> Genuinely additive — V4 has no direct equivalent. Feeds V4 via ADR.

---

## 1. The Idea in One Paragraph

When an organiser, venue, or vendor joins ZUKA, don't make them re-type what the
internet already knows about them. With their permission, ZUKA imports verifiable
*evidence* from where their business already lives (their website, Google Business
profile, Facebook page) to pre-fill their profile and give them a head start —
while keeping the crucial distinction that ZUKA imports **evidence, not trust**.
Trust is still earned through real participation on ZUKA; the import just removes
the blank-form friction of starting from zero.

---

## 2. Why This Is Genuinely New (and worth keeping)

V4 has no onboarding-evidence concept — this is the most additive idea in V7.
It directly serves a real ZUKA goal: getting 30 Kampala venues onboarded for the
pre-launch (Vol 24 city-onboarding). A venue owner who can join by saying "here's
my Facebook page" instead of filling six screens is far likelier to actually
onboard. It's an acquisition-friction killer.

```
It also expresses V4 principles well:
  - Reality-First (Vol 01): import real-world evidence of who they are.
  - Invisible Sophistication (Vol 16): they say "here's my page"; ZUKA does the work.
  - Trust is earned (Vol 11): evidence ≠ trust. Imported evidence is a FOUNDATION,
    not a trust score. This distinction is the concept's spine.
```

---

## 3. The Spine: Import Evidence, NOT Trust (reconciled to Vol 11)

This is the single most important reconciliation. V7 states it; V4's Trust OS
makes it enforceable.

```
EVIDENCE (imported, external):
  has a SOURCE (which platform), a CONFIDENCE (how reliable), a FRESHNESS (how
  recent). "This Facebook page has existed 6 years" is evidence.

TRUST (computed, ZUKA-internal — Vol 11):
  six dimensions, earned through observed participation, decaying, explainable.
  NEVER imported. A venue with great external evidence still starts at ZUKA's
  earned-trust baseline and must participate to build real trust.

THE RULE: imported evidence can pre-fill a PROFILE and inform a starting
VERIFICATION level (Vol 08), but it does NOT grant ZUKA trust standing. You don't
buy or import your way to "Trusted Organiser" — you earn it by running real
gatherings well. (Vol 11 ADR-002: trust cannot be purchased — or imported.)
```

---

## 4. The Zero-Recreation Principle (the friction pyramid)

```
Prefer, in order (least human effort first):
  1. Reuse existing ZUKA data
  2. Reuse permitted docked-platform data (their website/FB/Google)
  3. Infer from context
  4. Extract from an uploaded flyer/poster (AI)
  5. Ask the user (only what's still missing)
  6. Manual entry (last resort)

Never ask a human to type what's already reusable or inferable with confidence.
```

---

## 5. Reconciliation to the Locked Stack & DPPA (where V7 must bend)

V7 lists aggressive connectors (Facebook, Google, WhatsApp). V4's stack and
Uganda's law discipline this hard:

```
SUPABASE-FIRST: imports run through Edge Functions calling permitted public APIs;
  structured evidence stored in Postgres with source/confidence/freshness fields.
  No new backend stack. A connector is a ConnectorContract (ADR-013), each needing
  an ADR — not a free-for-all of SDKs.

DPPA 2019 (the hard gate): importing data ABOUT a business that may include
  personal data (an owner's name, a pastor's identity) is processing personal
  data. Requires:
    - explicit, informed CONSENT before any import ("ZUKA will read your public
      Facebook page to set up your profile — OK?")
    - clear purpose limitation (onboarding only)
    - data-subject rights (correct/delete the imported data)
    - source + freshness recorded; stale evidence expires
  zuka-legal MUST rule before any connector ships. This is non-negotiable and is
  why this concept is POST-MVP, not Phase 1.

CONSENT-FIRST, NOT SCRAPE-FIRST: ZUKA imports only what the owner authorises,
  from sources they confirm are theirs. No silent scraping. (Also a trust-with-
  the-user issue, not just legal.)
```

---

## 6. New Concepts This Introduces (for eventual ADR)

```
ENTITIES (proposed):
  DigitalEvidenceProfile  (entity_id, sources[], imported_fields[], consent_ref).
                          The structured import result.
  EvidenceSource          (kind[website|google|facebook|flyer], confidence,
                          retrieved_at, refresh_after, consent_id). Per source.
  EvidenceItem            (source_id, field, value, confidence). A single
                          imported fact, with provenance.
  ImportConsent           (entity_id, source, scope, granted_at, revocable).
                          The DPPA consent record — mandatory before import.

EVENTS (append-only, ADR-015):
  ImportConsentGranted · EvidenceImported · EvidenceRefreshed · EvidenceExpired ·
  EvidenceCorrectedByOwner

REUSED: ConnectorContract (Vol 20, ADR-013), Verification levels (Vol 08), Trust
OS dimensions (Vol 11 — which evidence INFORMS but never sets), RLS, provenance.
```

---

## 7. MVP Cut Lines

```
NOT PHASE-1 MVP. The 30-venue pre-launch can onboard with a SHORT manual form;
evidence-import is an enhancement, and it's gated on a DPPA consent review. So:

PHASE A (post-MVP, after legal sign-off) — ONE connector, lowest risk:
  Website URL or flyer-upload import (least personal-data-heavy; owner-supplied).
  Pre-fills name, location, category, hours. Consent-gated.

PHASE B — Google Business / Facebook page import (richer, more personal data →
  more DPPA care). One connector at a time, each an ADR + legal review.

PHASE C — the inference/AI extraction depth (flyer → full profile), refresh
  cycles, multi-source confidence merging.

DORMANT: the full V7 connector suite, "progressive trust migration" beyond
  evidence (trust is earned, not migrated — Vol 11 caps this), reputation import.
```

---

## 8. Open Questions

```
- zuka-legal (priority, blocking): DPPA consent model for importing business +
  personal data from third-party platforms. THE gate for this whole concept.
- Which single connector is the lowest-risk first? (Likely owner-supplied website
  or flyer upload — they hand it over, less third-party-data concern.)
- Confidence→verification mapping: how much does imported evidence move the
  Verification level (Vol 08) — and the firm rule that it NEVER moves Trust (Vol 11).
- Refresh/expiry: how stale before imported evidence is re-confirmed or dropped?
```

---

> **Zero-Recreation Docking — Concept (Tier 2, post-MVP)**
> Don't retype what the internet already knows. Import evidence, never trust.
> Consent-first, Supabase-first, DPPA-gated. The friction-killer for the
> 30-venue pre-launch — once the legal gate is cleared.