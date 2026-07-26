# ZUKA Unified Architecture Bible
# BOOK IV — MEMORY CIVILIZATION

> **Constitutional Authority:** Level 1 — Domain Constitution
> **Source:** Canonical Volume V (Memory Architecture)
>              synthesised with Zuka-Bible 2, Book I Part B (Memory Doctrine)
>              and Zuka-Bible 2, Book IV (Memory Civilization)
> **Status:** Substantive — full Memory architecture specification

---

## Constitutional Premise

Memory is preserved meaning derived from participation.

From Zuka-Bible 2, Book I Part B (Chapter 9.1).

From Volume V's opening thesis: gatherings are civilisation's atoms. The Memory
Architecture is how those atoms accumulate meaning across time. Unlike media files,
which can be recovered, Memory — the meaning attached to an experience — once lost
is gone forever.

*"Media is not memory."* (ADR-009)

---

## Memory ≠ Media (ADR-009; Volume V, Ch.4)

The most important architectural separation in the entire Memory domain:

```
MEMORY                              MEDIA
─────────────────────────────────── ────────────────────────────────────
Holds MEANING                       Holds EVIDENCE (bytes)
Has significance, emotion_tags,     Has file_url, checksum, mime_type,
  life_chapter, importance_level      storage_tier, captured_at
Always hot storage                  Tiered storage (hot/warm/cold)
Irreplaceable once context lost     Recoverable from backup
Never deleted (Volume VII TTB)      Cold tier may be archived
A Memory may exist without Media    Media may exist without a Memory
(a remembered moment undocumented)  (a photo without meaning attached)
```

These are two permanently separate entity layers. They reference each other via
`memory.media_refs[]` → `media.media_id`. They never merge into one record.
Any schema migration that combines them violates ADR-009 and must be rejected.

---

## Memory Value Curve (Volume V)

Memory capital appreciates over time, not deprecates:

```
Day 1:   A photo of a gathering
Year 1:  A documented memory with context and tagged friends
Year 5:  A named Memory within a Family Vault, linked to a Tradition
Year 10: Part of a multi-generational Story; referenced in a Community Archive
Year 25: A historical record; referenced by the Tradition Historian;
         part of the Community's Civilization capital
```

This is the primary strategic moat argument for the Memory domain:
raw volume is less important than temporal depth. A platform with
5-year-old memories is categorically more valuable than a platform
with the same number of memories all from the last 90 days.

---

## The Four-Layer Memory Curation Model (Volume V, Ch.7)

```
LAYER 1 — CAPTURE (Media entity created)
  A photo is uploaded. A video is recorded. The bytes exist.
  No Memory entity yet. No meaning assigned.
  Action: Create Media entity; generate AttendanceSeedRecord (Volume XXI, Ch.9)

LAYER 2 — MINIMAL CURATION (Memory entity created)
  A title and date are added. The moment is named.
  Action: Create Memory entity with significance or title populated.
  This is the minimum threshold for a Memory to count in DRL assessments.

LAYER 3 — CONTEXTUAL ENRICHMENT (meaning deepened)
  Emotion tags are added. Life chapter identified. Importance level set.
  Action: Update Memory with emotion_tags, life_chapter_id, importance_level.
  CRITICAL: emotion_tags are irreplaceable if missed (Volume V, Ch.28).
  They must be collectable from day one of the platform.

LAYER 4 — RELATIONSHIP INTEGRATION (Memory becomes social)
  Other Person entities are tagged. Community linkage established.
  Memory linked to a Tradition or Story.
  Action: tagged_person_ids populated; community_id linked.
  This is when a Memory becomes Relationship Capital, not just Memory Capital.
```

---

## Memory Types (Volume V, Ch.8)

```
ATTENDANCE     — being present at a Gathering
CONTRIBUTION   — creating something for a Community (Archive entry)
MILESTONE      — life events (graduation, birth, marriage, first job)
TRADITION      — recurring Gathering elevated to cultural pattern
RELATIONSHIP   — a Memory defined primarily by who was there
ACHIEVEMENT    — a recognised accomplishment
PLACE          — a memory of a specific location or space
TRANSITION     — a change in life chapter (moving city, career change)
```

---

## The Memory Vault Architecture (Volume V, Ch.16)

```
Personal Memory Vault
  Every Person entity has one. Contains all personal Memories.
  Governed by the person's own LegacySettings (Volume III, Part X).
  Default visibility: private.

Family Memory Vault
  Every Family entity has one (Volume II §3.1).
  Governed by family governance consensus (Volume VI, Ch.16's
    strategic_layer model — not a single family member's decision).
  Contains Memories contributed by family members to the shared archive.
  Persists after individual family members are Memorialised.
  The Family Twin (Volume IX, Ch.7) draws from this vault.

Community Archive
  Every Community entity has one (Volume V, Ch.17).
  Governed by the Archivist role (Volume VI, Ch.8).
  Contains community-owned Memories: gatherings, traditions, milestones.
  Operationally distinct from individual members' personal vaults.
  The principle: a community's memory is not the sum of its members'
  individual memories (Volume VI, Ch.9).
  Persists indefinitely — communities outlive individuals (Volume VI, Ch.24).
```

---

## Anniversary Engine (Volume V, Ch.21) — Dormant

The Anniversary Engine surfaces "on this day" Memory prompts. Its activation
requires:
- Memory domain at DRL 2+ (≥ 2,000 curated Memory entities; emotion_tags on ≥ 60%)
- At least 12 months of Memory history for the Person being prompted
- The six mandatory sensitivity rules implemented and tested:

```
SENSITIVITY RULES (all six must be active before any Anniversary notification fires):
  1. grief_tagged memories receive no celebratory framing
  2. Anniversary prompts respect quiet hours (10PM-7AM local time)
  3. grief/sadness primary-emotion memories: afternoon delivery only (2PM-5PM)
  4. The person's notification settings (Category 6 configurable per Volume XXII)
  5. Recent bereavement signal: suppress all Anniversary prompts for 30 days
  6. Dismissed seeds (Volume XXI, Ch.9): never re-prompted after explicit dismiss
```

The Anniversary Engine is listed in the Dormant Feature Register pending both
DRL threshold and these six sensitivity rules being confirmed built and tested.

---

## Memory Domain Events (Volume II, Ch.9 extended)

```
MemoryCreated          — Memory entity written (Layer 2 minimum curation)
MemoryEnriched         — emotion_tags, life_chapter, or importance added (Layer 3)
MemoryShared           — visibility changed; tagged persons notified
MemoryLinkedToTradition — tradition_id populated
MemoryAddedToVault     — placed in Family or Community vault
MemorySuccessionApplied — legacy_settings processed after Memorialisation
MemoryAccessGranted    — steward-level access given to a Legacy Steward
AlbumCreated           — grouping of Memory entities
StoryCreated           — narrative linking multiple Memories
PhotoUploaded          — Media entity created (Layer 1 capture)
AttendanceSeedCreated  — automatic seed after TicketCheckedIn (Volume XXI, Ch.9)
```

---

## Memory Domain ADR Cross-References

```
ADR-003  Reality precedes AI — observed memories outweigh inferred ones
ADR-006  Legacy remains cross-domain — Memory domain is one of three legacy scopes
ADR-007  Explainability mandatory — ProvenanceRecord on all Memory Twin outputs
ADR-009  Memory and Media are permanently separate entities (primary ADR for this domain)
ADR-018  Detect aggressively, correct conservatively — Memory quality checks
         never auto-correct; missing emotion_tags flagged but not assumed
```

---

## DRL Thresholds for Memory Domain (Volume XX, Ch.7)

```
DRL 0 → 1: Memory entity schema active WITH emotion_tags field collecting
DRL 1 → 2: ≥ 500 Memory entities with title/significance; emotion_tags on ≥ 30%
DRL 2 → 3: ≥ 2,000 Memory entities; emotion_tags ≥ 60%; ≥ 200 with tradition_id
DRL 3 → 4: Memory Historian active; Anniversary Engine sensitivity rules complete
DRL 4 → 5: Personal Twin (Memory scope) at sustained high engagement; 24+ months history
```

---

*Source: Volume V (Memory Architecture); Zuka-Bible 2, Books I Part B and IV*
*Authority: Level 1 — Domain Constitution*
*Next: Book V — Relationship Civilization*
