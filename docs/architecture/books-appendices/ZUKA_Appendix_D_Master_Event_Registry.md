# ZUKA Unified Architecture Bible
# APPENDIX D — MASTER DOMAIN EVENT REGISTRY

> **Authority:** Constitutional Level 1; Zuka-Bible 2, Book II Ch.4 (Event Theory)
> **Source:** Volume II, Ch.9 (base registry) extended through Volume XXI
> **Status:** Canonical and complete at this writing. Append-only.
> **Rule:** Every new DomainEvent type must be added here before any code
>            may emit it. An event type not in this registry is not canonical.

---

## Event Theory (Zuka-Bible 2, Book II, Ch.4)

A DomainEvent is a recorded occurrence affecting one or more entities.

Constitutional properties of every DomainEvent:
```
IMMUTABLE    — never updated after writing; Volume VII's Temporal Truth Principle
AUDITABLE    — every event is traceable to an actor and a timestamp
TIMESTAMPED  — observed_at and recorded_at both present (Volume VII, Four Clocks)
TRACEABLE    — can be followed through to the ProvenanceRecord of any conclusion
               derived from it
APPEND-ONLY  — ADR-015: the Event Store table has no UPDATE or DELETE operations
```

---

## IDENTITY DOMAIN EVENTS

```
PersonCreated
  Payload: person_id, display_name, phone_number_hash, created_at
  Side effects: Identity Graph node created; Welcome notification queued

PersonVerified
  Payload: person_id, verification_tier, verified_at, confidence
  Side effects: Verification entity created; TrustGranted event queued

PersonUpdated
  Payload: person_id, updated_fields[], updated_at
  Side effects: Entity updated; no trust impact unless trust-relevant fields

PersonMemorialised
  Payload: person_id, memorialised_at, memorialised_by, memory_policy_applied
  Side effects: MemorialRecord created; LegacySettings applied;
                any active Personal Twin → TwinLegacyBinding applied
                (default: immediate decommission per Volume IX, Ch.16)

PersonDeleted
  Payload: person_id, deletion_requested_at, deletion_scheduled_at
  Side effects: SOFT delete only; 30-day cooling period before data removal;
                Memory Vault transferred to Legacy Steward per LegacySettings

PersonaMergeRequested
  Payload: person_id, source_persona_id, target_persona_id
  Side effects: Queued for human review; never auto-merged (ADR-018)

PhoneVerificationInitiated
  Payload: person_id, phone_e164_hash, otp_delivery_method
  Side effects: Africa's Talking SMS OTP sent (Volume XIX, Ch.8)

PhoneVerificationCompleted
  Payload: person_id, verified_at
  Side effects: PersonVerified queued; VerificationTier advances to phone_otp

BadgeAwarded
  Payload: person_id, badge_type, awarded_for, provenance_record_id
  Side effects: Badge entity created; TrustGranted queued

BadgeRevoked
  Payload: person_id, badge_id, revoked_by_identity_id, reason
  Side effects: Badge status → revoked; TrustLost queued
  Rule: revoked_by_identity_id must be a human identity — ADR-014

TrustGranted
  Payload: entity_id, trust_dimension, signal_type, evidence_event_id
  Side effects: TrustTimelinePoint created; TrustProfile recomputed

TrustLost
  Payload: entity_id, trust_dimension, signal_type, evidence_event_id
  Side effects: TrustTimelinePoint created; TrustProfile recomputed

TrustScoreRecomputed
  Payload: entity_id, new_composite_score, recomputed_by, method_version
  Side effects: ReputationProfile may update; relevant Twins re-synthesis triggered

VerificationApproved
  Payload: person_id, tier, smile_identity_ref, confidence
  Side effects: Verification entity created; TrustGranted event queued

FounderReviewItemDecided
  Payload: item_name, decision (ADOPT/DEFER/REJECT), decided_by, rationale
  Side effects: Decision Log entry appended; Bible updated per item
```

---

## GATHERING DOMAIN EVENTS

```
GatheringCreated
  Payload: gathering_id, title, gathering_type, host_persona_id,
           primary_community_id, venue_id, starts_at, ends_at
  Side effects: Gathering node added to Gathering Graph; feed updated

GatheringUpdated
  Payload: gathering_id, updated_fields[], updated_at
  Side effects: Entity updated; attendees notified if material change

GatheringPublished
  Payload: gathering_id, published_at
  Side effects: Visible in discovery feed; ticket sales may open

GatheringWentLive
  Payload: gathering_id, went_live_at, organiser_location_lat, organiser_location_lng
  Side effects: Live pin visible on City heat map within 60 seconds
                (Constitutional Proof 1 — Volume XXII, Ch.1)

GatheringEnded
  Payload: gathering_id, ended_at, actual_attendance_count
  Side effects: Tradition detection sweep triggered (Volume X, Ch.8);
                AttendanceSeedRecord prompts queued for all attendees

GatheringCancelled
  Payload: gathering_id, cancelled_at, cancellation_reason
  Side effects: TicketRefunded events queued for all ISSUED tickets;
                Attendees notified

RSVPSubmitted
  Payload: gathering_id, person_id, response ('yes'|'maybe'|'no'), submitted_at
  Side effects: Participation entity created; Gathering capacity updated

CheckInRecorded
  Payload: gathering_id, ticket_id, person_id, check_in_method,
           checked_in_at, gate_id, offline_session_id
  Side effects: Ticket status → CHECKED_IN; AttendanceSeedRecord created;
                CrowdLevel signal updated; TrustGranted if first event type

TraditionDetected
  Payload: tradition_id, community_id, gathering_ids[], detected_at, confidence
  Side effects: Tradition entity created in SEEDED status;
                Organiser/community prompted to name it (never auto-named)

TraditionStrengthened
  Payload: tradition_id, latest_gathering_id, occurrence_count, strength_delta
  Side effects: Tradition status may advance; Tradition Graph edge updated

TraditionLapsed
  Payload: tradition_id, lapsed_at, last_occurrence_at
  Side effects: Status → LAPSED; Community notified; AT_RISK threshold missed

TraditionRevived
  Payload: tradition_id, revival_gathering_id, revived_at
  Side effects: Status → ACTIVE; TraditionStrengthened queued
```

---

## MEMORY DOMAIN EVENTS

```
MemoryCreated
  Payload: memory_id, person_id, gathering_id, title, significance,
           importance_level, emotion_tags, visibility
  Side effects: Memory entity persisted; Memory Graph node created;
                Relationship Graph edges updated for tagged_person_ids

MemoryEnriched
  Payload: memory_id, enrichment_type, updated_fields[]
  Side effects: Entity updated; Memory Graph semantic signals updated

MemoryShared
  Payload: memory_id, new_visibility, tagged_person_ids_added
  Side effects: Notifications to newly tagged persons; Memory Graph edges added

MemoryLinkedToTradition
  Payload: memory_id, tradition_id, linked_at
  Side effects: Tradition Graph strengthened; tradition_id populated on Memory

MemoryAddedToVault
  Payload: memory_id, vault_id, vault_type ('family'|'community'), added_by
  Side effects: Vault record updated; Archive Archivist notified if community

AttendanceSeedCreated
  Payload: seed_id, ticket_id, gathering_id, person_id, created_at
  Side effects: Post-gathering Memory creation prompt queued
                (sent once, 30 mins after GatheringEnded; one follow-up at 24h)

PhotoUploaded
  Payload: media_id, uploader_id, gathering_id, captured_at, file_size_bytes
  Side effects: Media entity created (Layer 1 capture); no Memory entity yet

StoryCreated
  Payload: story_id, creator_id, title, memory_ids[], visibility
  Side effects: Story entity persisted; Memory Capital signal updated

AlbumCreated
  Payload: album_id, creator_id, memory_ids[], title
  Side effects: Album entity persisted
```

---

## COMMUNITY DOMAIN EVENTS

```
CommunityCreated
  Payload: community_id, name, community_family, community_type,
           founding_members[], founding_date, governance_model
  Side effects: Community entity created; founding_members permanently recorded
                (irreplaceable if missed per Volume VI, Ch.26)

CommunityMemberAdded
  Payload: community_id, person_id, role, joined_at, added_by
  Side effects: CommunityMembership created; CommunityTrustProfile recomputed

CommunityMemberLeft
  Payload: community_id, person_id, reason, left_at
  Side effects: CommunityMembership status → inactive;
                their Memories in Community Archive preserved (never deleted)

CommunityMemberRoleChanged
  Payload: community_id, person_id, old_role, new_role, changed_by, approved_by
  Side effects: CommunityMembership updated; if governance-level: succession recorded

CommunityGatheringCreated
  Payload: community_id, gathering_id, organiser_persona_id
  Side effects: Community Graph edge community→gathering created

CommunityTraditionEstablished
  Payload: community_id, tradition_id, established_at, established_by
  Side effects: Tradition confirmed by community; status → ACTIVE

CommunitySuccessionCompleted
  Payload: community_id, role, from_person_id, to_person_id, succession_type
  Side effects: SuccessionRecord created; governance records updated

CommunityEndorsed
  Payload: community_id, endorsed_by_person_id, endorsement_type, context
  Side effects: TrustEdge updated; CommunityTrustProfile recomputed
  Note: New event type introduced in Volume XIII, Ch.5 — not in original Volume II registry

CommunityDisputeTriggered
  Payload: community_id, dispute_type, triggered_by, triggered_at
  Side effects: PreserveAndFreezeRecord created; relevant governance layers frozen
```

---

## TRUST DOMAIN EVENTS

```
TrustEdgeCreated
  Payload: from_entity_id, to_entity_id, relationship_context, initial_weight
  Side effects: Trust Graph edge created; Trust Agent computation triggered

TrustEdgeUpdated
  Payload: edge_id, new_weight, decay_applied, updated_at
  Side effects: TrustTimelinePoint created; TrustProfile recomputed

DisputeFiled
  Payload: dispute_id, filing_person_id, disputed_entity_id, dispute_type
  Side effects: Dispute entity created; trust signals for involved parties flagged

DisputeResolved
  Payload: dispute_id, resolution, resolved_by, resolved_at
  Side effects: TrustGranted or TrustLost depending on resolution;
                AppealRecord closed if this resolves an appeal

ReviewSubmitted
  Payload: review_id, reviewer_id, subject_entity_id, rating, gathering_id
  Side effects: ReputationProfile updated; TrustDimension reliability affected
  Prerequisite: gathering check-in record must exist for this reviewer_id
                (fake review prevention — Volume XIII, Ch.7)

FraudSignalDetected
  Payload: signal_id, signal_type, implicated_entity_ids[], confidence
  Side effects: FraudSignal entity created; human review queue populated
                No automated action taken — ADR-014

AppealUpheldCorrection
  Payload: appeal_id, conclusion_id, correction_type, new_event_id
  Side effects: New DomainEvent appended (never edits original); downstream
                Twins/Agents will pick up correction on next synthesis cycle

PrivacyImpactAssessmentCompleted
  Payload: pia_id, capability_name, risk_level, assessed_by, mitigations[]
  Side effects: PIA record persisted; referenced in DRL Activation Contract
```

---

## OPPORTUNITY DOMAIN EVENTS

```
OpportunityEmerged
  Payload: opportunity_id, opportunity_type, parties[], community_id,
           evidence_event_ids[], confidence, provenance_record_id
  Side effects: Opportunity entity created in EMERGING status;
                Trust Agent output verified as DRL 2+ first
  Generator: Opportunity Agent Mode 1 (Volume X, Ch.7) — first
             specification of what actually generates this event

OpportunityProposed
  Payload: opportunity_id, proposing_party_id, recipient_party_id, terms
  Side effects: Opportunity status → PROPOSED; both parties notified

OpportunityAccepted
  Payload: opportunity_id, accepted_by, accepted_at
  Side effects: Opportunity status → ACTIVE; Relationship edge strengthened

OpportunityCompleted
  Payload: opportunity_id, completed_at, outcome_notes
  Side effects: Economic Trust signals fired for all parties;
                Opportunity Capital updated

OpportunityDeclined
  Payload: opportunity_id, declined_by, reason
  Side effects: Status → DECLINED; no trust impact (declining is legitimate)

SponsorshipProposed
  Payload: opportunity_id, sponsor_id, target_entity_id, terms, provenance_record_id
  Side effects: Opportunity entity created; ProvenanceRecord mandatory per
                Volume VIII, Ch.22's unconditional stricter provenance bar

JobPosted
  Payload: opportunity_id, posting_entity_id, requirements[], compensation
  Side effects: Opportunity entity created; Opportunity Graph updated

BookingConfirmed
  Payload: opportunity_id, creator_persona_id, organiser_persona_id, terms
  Side effects: Booking entity (Opportunity sub-type) created; Economic Trust signals

ScholarshipDistributed
  Payload: opportunity_id, issuing_community_id, recipient_person_id, amount
  Side effects: WalletLedger entry; Community Capital signal
```

---

## TICKETING & COMMERCE EVENTS

```
TicketPurchased
  Payload: ticket_id, gathering_id, tier_id, owner_person_id, paid_amount,
           currency_code, payment_intent_id, smart_bucket_token
  Side effects: Ticket entity created (ISSUED); GatheringTier.tickets_sold++
  Prerequisite: PaymentVerified must fire first (Volume XXI, Ch.10 Rule 2)

TicketCheckedIn
  Payload: ticket_id, gathering_id, gate_id, check_in_method, checked_in_at
  Side effects: Ticket status → CHECKED_IN; AttendanceSeedRecord created;
                GatheringTier.tickets_checked_in++
  Methods: 'smart_bucket' | 'manual_qr' | 'name_list' | 'supervisor_override'

TicketTransferInitiated
  Payload: ticket_id, from_person_id, to_person_id, transfer_reason
  Side effects: Ticket status → TRANSFERRING; escrow activated

TicketTransferCompleted
  Payload: ticket_id, new_owner_person_id
  Side effects: Ticket owner_person_id updated; provenance chain appended

TicketGiftPending
  Payload: ticket_id, gifter_person_id, recipient_phone_e164, expiry_at
  Side effects: Ticket status → GIFT_PENDING; is_gift = true;
                SMS notification to recipient (Africa's Talking)

TicketGiftAccepted
  Payload: ticket_id, accepting_person_id, accepted_at
  Side effects: Ticket gift_state → ACCEPTED; TicketTransferCompleted queued

TicketGiftDeclined
  Payload: ticket_id, declined_at
  Side effects: Ticket → RETURNED to gifter; gift_state → DECLINED

TicketGiftExpired
  Payload: ticket_id, expired_at
  Side effects: Ticket → RETURNED to gifter; gift_state → EXPIRED
  Rule: 48-hour expiry is non-negotiable. No override. Volume XXI, Ch.10 Rule 5

TicketRefunded
  Payload: ticket_id, refund_amount, reason, flw_refund_reference
  Side effects: Ticket status → REFUNDED; Flutterwave refund initiated
  Prerequisite: GatheringCancelled must exist for this gathering_id,
                OR organiser with appropriate trust level must have authorised

TicketRevoked
  Payload: ticket_id, fraud_signal_id, revoked_by_identity_id, reason
  Side effects: Ticket status → REVOKED
  Rule: revoked_by_identity_id must be a human — ADR-014. Never automated.

PaymentIntentCreated
  Payload: payment_intent_id, amount, currency, tx_ref, flw_ref
  Side effects: PaymentIntent entity created; user redirected to Flutterwave

PaymentIntentVerified
  Payload: payment_intent_id, verified_at, amount_charged, payment_method
  Side effects: TicketPurchased may now fire

PayoutInitiated
  Payload: payout_id, recipient_id, amount, account_type, flw_transfer_code
  Side effects: WalletLedger entry; organiser notified

TierAmended
  Aggregate: GatheringTier (aggregate_id = tier_id)
  Payload: tier_id, gathering_id, amended_by (person_id),
           changes { <field>: { before, after } } for each of the amended
           fields tier_name / price_ugx / capacity (a NULL param is unchanged
           and absent from changes)
  Side effects: gathering_tier.tier_name/price_ugx/capacity updated in place
  Emitted by: amend_tier RPC (S059). Organiser-only, post-go-live tier edit.
  Rule: reject-only guards — capacity may never drop below tickets_sold and a
        price change may not cross the free/paid (price_ugx = 0) boundary; NO
        ticket is ever auto-revoked (ADR-014). Appended, never an UPDATE of
        GatheringTierCreated (ADR-015). No raw PII in payload.
```

---

## INTELLIGENCE DOMAIN EVENTS

```
DataReadinessLevelChanged
  Payload: domain, previous_drl, new_drl, assessed_at, assessor_id,
           five_dimension_scores{Volume, Quality, Freshness, Coverage, Stability}
  Side effects: Dormant Feature Register graduation conditions re-evaluated;
                relevant Twins and Agents may unlock new output permissions
  Rule: never fires automatically — human assessor sign-off required (Volume XX, Ch.8)

FeatureSeedActivated
  Payload: capability_name, dormant_register_entry_id, activated_at,
           drl_activation_contract_id, activated_by_identity_id
  Side effects: Capability moves from Dormant to active; Register entry removed;
                relevant volume(s) updated

TwinSynthesisCompleted
  Payload: twin_id, twin_type, synthesis_method_version, new_embedding_version,
           provenance_record_id
  Side effects: Twin's synthesis state updated; downstream consumers notified

LegalAddendumUpdated
  Payload: addendum_id, market, version, updated_at, updated_by, change_summary
  Side effects: Any DRL Activation Contract relying on this addendum is flagged
                for re-review per Volume XIX, Ch.4

FounderReviewItemDecided
  Payload: item_name, decision, decided_by, rationale, bible_volumes_affected[]
  Side effects: Decision Log appended; affected volumes updated; Dormant Register
                updated if applicable
```

---

## Event Registry Statistics

```
Identity Domain:      13 event types
Gathering Domain:     10 event types
Memory Domain:         9 event types
Community Domain:      9 event types
Trust Domain:          9 event types
Opportunity Domain:    9 event types
Ticketing & Commerce: 14 event types
Intelligence Domain:   5 event types
────────────────────────────────────
Total registered:     78 event types
```

---

*Source: Volume II, Ch.9 (base registry); Volumes IV, V, VI, VII, X, XIII, XVIII, XIX,
XX, XXI (extensions); Zuka-Bible 2, Book II Ch.4 (Event Theory)*
*Authority: Level 1 — Domain Constitution*
