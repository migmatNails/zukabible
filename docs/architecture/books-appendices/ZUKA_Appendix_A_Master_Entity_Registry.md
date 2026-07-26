# ZUKA Unified Architecture Bible
# APPENDIX A — MASTER ENTITY REGISTRY

> **Authority:** Constitutional Level 1; derived from Volume II and all domain volumes
> **Purpose:** Single reference for every named entity type in the ZUKA system,
>              with its owning domain, primary volume, key fields, and DRL note
> **Update rule:** Append-only. New entities added below; no entity removed.
>              Cross-reference: ZUKA_Book_II_Canonical_Domain_Model.md for
>              full TypeScript schemas

---

## Tier 0 — Constitutionally Protected Entities
*(Zuka-Bible 2, Book I Part A, Ch.4.4 — cannot be removed from the platform model)*

| Entity | Domain | Volume | Key Fields | Notes |
|--------|--------|--------|-----------|-------|
| Person | Identity | III | person_id, display_name, phone_number, status, trust_profile, legacy_settings, embedding | Canonical human entity. NOT "User". NOT "Participant". ADR-010 |
| Relationship | Cross-domain | II | relationship_id, from_entity_id, to_entity_id, type, weight, trust_score, lifecycle | First-class entity, NOT a join table. ADR-001 |
| Memory | Memory | V | memory_id, significance, emotion_tags, importance_level, life_chapter_id, tradition_id, media_refs | Preserved meaning, NOT raw media. ADR-009 |
| Trust | Trust | III/XIII | TrustProfile, TrustEdge, TrustDimensions (6), TrustTimeline | Cannot be purchased. ADR-002 |
| Community | Community | VI | community_id, community_family, founding_members, lifecycle_stage, trust_profile | Eight community families. ADR-012 |
| Opportunity | Opportunity | II/XV | opportunity_id, opportunity_type, parties, provenance_record_id | Requires ProvenanceRecord. ADR-007 |

---

## Tier 1 — Human Entities

| Entity | Domain | Volume | Key Fields | Notes |
|--------|--------|--------|-----------|-------|
| Child | Identity | III | child_id, guardian_ids | Distinct consent rules from Person. Volume IX, Ch.4 |
| Family | Community | VI | family_id, family_name, founding_members, family_vault_id | Treated as Community type. Volume VI, Ch.4 |
| Persona | Identity | III | persona_id, person_id, persona_type, visibility, persona_trust_score | Multi-persona architecture. Volume III, Ch.9 |
| Verification | Identity | III | verification_id, person_id, tier, verified_at, confidence | Earned, never purchased. Smile Identity connector (Volume XIX, Ch.10) |
| Badge | Identity | III | badge_id, person_id, badge_type, provenance_record_id | Always carries ProvenanceRecord. Revocation is human (ADR-014) |
| LifeChapter | Identity | III | chapter_id, person_id, name, started_at, ended_at | Named life period for Memory and Relationship context |
| MemorialRecord | Identity | III | memorial_id, person_id, memorialised_at | Created on Memorialisation; Memory Vault persists |
| CreatorStats | Identity | III | persona_id, specialisations, bookings_completed, delivery_rate | Attached to creator-type Persona |

---

## Tier 2 — Place Entities

| Entity | Domain | Volume | Key Fields | Notes |
|--------|--------|--------|-----------|-------|
| Venue | Gathering | II/IV | venue_id, name, venue_type, district_id, lat, lng, capacity | Physical or virtual |
| District | Gathering | II | district_id, name, city_id, polygon | GeoJSON polygon |
| City | Gathering | II | city_id, name, country_id | City Twin (Volume IX, Ch.11) — aggregate only |
| Country | Identity/Legal | XIV/XVIII | country_id, iso_3166_code, name | Country readiness domain. Legal Addendum boundary |

---

## Tier 3 — Gathering Entities

| Entity | Domain | Volume | Key Fields | Notes |
|--------|--------|--------|-----------|-------|
| Gathering | Gathering | IV | gathering_id, title, gathering_type, status, host_persona_id, tradition_id | NOT "Event". ADR-011. 40+ types |
| GatheringTier | Opportunity | XXI | tier_id, gathering_id, price, capacity, tickets_sold | Ticket pricing and capacity |
| Tradition | Gathering | IV/VII | tradition_id, name, community_id, status, occurrence_count | Discovered, not created. Volume VII, Ch.15 |
| Participation | Gathering | IV | participation_id, person_id, gathering_id, role, presence_level | 9-level Presence Ladder (Volume IV) |
| CheckInRecord | Gathering/Opportunity | XXI | check_in_id, ticket_id, check_in_method, synced_at, offline_session_id | Supports offline. Smart Bucket method is primary |

---

## Tier 4 — Memory Entities

| Entity | Domain | Volume | Key Fields | Notes |
|--------|--------|--------|-----------|-------|
| Media | Memory | V | media_id, media_type, storage_url, storage_tier, checksum | Evidence layer, NOT meaning. ADR-009 |
| Story | Memory | V | story_id, creator_id, title, memory_ids | Curated narrative linking Memories |
| Album | Memory | V | album_id, creator_id, memory_ids | Grouping without narrative |
| AttendanceSeedRecord | Memory | XXI | seed_id, ticket_id, gathering_id, status | Auto-created on CheckIn; becomes Memory if person accepts |
| MemorySuccessionRecord | Memory | V | succession_id, memory_id, applied_at | Documents legacy processing |

---

## Tier 5 — Community Entities

| Entity | Domain | Volume | Key Fields | Notes |
|--------|--------|--------|-----------|-------|
| CommunityMembership | Community | VI | membership_id, person_id, community_id, role, status | Roles: member, elder, founder, archivist, treasurer |
| CommunityTrustProfile | Community/Trust | VI/XIII | community_id, six trust dimensions, composite_score | Volume VI, Part XI |
| CommunityArchive | Memory/Community | V/VI | archive_id, community_id, archivist_id | Governed by Archivist role |
| CommunityKnowledgeEntry | Community | XVI | entry_id, community_id, knowledge_type, contributed_by | Volume VIII Ch.17 concept; first schema in Volume XVI, Ch.3. Dormant |
| SuccessionRecord | Community | VI | succession_id, from_role_holder, to_role_holder, governance_decision | Decoupled succession per Volume VI, Part XXII |
| FederationRelationship | Community | VI | federation_id, parent_community_id, child_community_id | Volume VI, Part XIX |

---

## Tier 6 — Trust & Reputation Entities

| Entity | Domain | Volume | Key Fields | Notes |
|--------|--------|--------|-----------|-------|
| TrustProfile | Trust | III/XIII | person_id, six TrustDimensions, composite_score | Multi-dimensional; never collapsed to one number |
| TrustEdge | Trust | VIII | edge_id, from_entity_id, to_entity_id, trust_weight | Graph edge in Trust Graph |
| TrustTimelinePoint | Trust | VII | point_id, person_id, dimension, value, observed_at | Append-only. ADR-015 |
| ReputationProfile | Identity | III | person_id, character_tags, avg_organiser_rating | Perceived value; distinct from trust |
| FraudSignal | Trust | XIII | signal_id, signal_type, implicated_entity_ids, confidence | 4 types: device_clustering, graph_topology, velocity, content_pattern |
| AppealRecord | Trust | XIII | appeal_id, contested_conclusion_id, appeal_grounds, review_outcome | 4-step process. Append-only correction only |
| PreserveAndFreezeRecord | Governance | XVIII | protocol_id, scope, legal_hold_flag, resolution_path | Community dispute containment |

---

## Tier 7 — Commerce Entities

| Entity | Domain | Volume | Key Fields | Notes |
|--------|--------|--------|-----------|-------|
| Ticket | Opportunity | XXI | ticket_id, ticket_ref, status, smart_bucket_token, paid_amount, provenance | Full lifecycle state machine |
| GiftTicketRecord | Opportunity | XXI | gift_id, ticket_id, gifter_id, recipient_phone, gift_state, expires_at | 48-hour non-extendable expiry |
| PaymentIntent | Payments | XIX | intent_id, amount, currency, flw_ref | Via Flutterwave connector (Volume XIX, Ch.6) |
| PayoutRecord | Payments | XIX | payout_id, recipient_id, amount, transfer_code | Via Flutterwave. Immutable after completion |
| WalletLedger | Payments | — | ledger_id, entity_id, amount, transaction_type | Append-only financial ledger |

---

## Tier 8 — Intelligence Entities

| Entity | Domain | Volume | Key Fields | Notes |
|--------|--------|--------|-----------|-------|
| Twin (all 6 types) | Cross-domain | IX | twin_id, twin_type, represented_entity_id, current_drl, current_crl, consent_record, synthesis | DRL 0 at this writing. 6 types: Personal, Family, Community, Gathering, Opportunity, City |
| TwinConsentRecord | Identity/Intelligence | IX | twin_id, consenting_entity_id, consent_scope, revocable (always true) | Always revocable immediately |
| TwinLegacyBinding | Identity/Intelligence | IX | twin_id, posthumous_access, default: immediate_on_death | Default is most protective |
| AgentAuthorisation | Intelligence | X | agent_type, inherited_consent_basis, operation_scope | Per-operation, not standing |
| ProvenanceRecord | Cross-domain | VIII | conclusion_id, conclusion_type, evidence_events, evidence_summary, confidence | Mandatory on ALL intelligence outputs. ADR-007 |
| DataReadinessScore | Cross-domain | XX | domain, five dimensions (Volume, Quality, Freshness, Coverage, Stability), composite_score | Five dimensions all must independently clear |
| CapabilityReadiness | Intelligence | XIV | entity_id, readiness_domain, current_drl, current_crl | CRL governing inequality: depth ≤ min(DRL_permits, CRL_earned) |

---

## Tier 9 — Connector & Governance Entities

| Entity | Domain | Volume | Key Fields | Notes |
|--------|--------|--------|-----------|-------|
| ConnectorContract | Infrastructure | XIX | connector_id, connector_name, version, capabilities[], privacy_classification, approval | All external integrations route through this |
| CapabilityDeclaration | Infrastructure | XIX | capability_name, input_schema, output_schema, error_handling, idempotent | Atomic capability unit within a connector |
| LegalAddendum | Governance | XVIII | addendum_id, market, applicable_laws, legal_counsel, sections 1-7 | Per-market legal compliance; requires qualified counsel |
| ADRRecord | Governance | ADR Registry | adr_id, decision, zone, source_volume, amendment_threshold | Append-only. 20 ADRs at this writing |

---

## Entity Count Summary

```
Tier 0 — Constitutionally Protected:    6 entities
Tier 1 — Human:                         8 entities
Tier 2 — Place:                          4 entities
Tier 3 — Gathering:                       5 entities
Tier 4 — Memory:                          5 entities
Tier 5 — Community:                        6 entities
Tier 6 — Trust & Reputation:               6 entities
Tier 7 — Commerce:                          5 entities
Tier 8 — Intelligence:                       7 entities
Tier 9 — Connector & Governance:              4 entities
─────────────────────────────────────────────────────
Total named entity types:                 56 entities
```

---

*Source: Volume II (Entity Taxonomy); all domain volumes II-XXII*
*Zuka-Bible 2, Appendix A (original stub — now substantive)*
*Authority: Level 1 — Domain Constitution*
