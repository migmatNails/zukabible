# ZUKA — APPENDIX B: MASTER DOMAIN REGISTRY

> Source: Volume XI, Ch.3 | Zuka-Bible 2, Book II, Ch.7

Eight canonical domains. No domain leakage. No cross-domain direct access.

| Domain | Primary Entities | Primary Events | Owning Volume | Notes |
|--------|-----------------|---------------|---------------|-------|
| Identity | Person, Child, Persona, Verification, Badge | PersonCreated, PersonVerified, BadgeAwarded | III | Root of all graphs |
| Community | Community, CommunityMembership, Family | CommunityCreated, CommunityMemberAdded | VI | Eight families |
| Gathering | Gathering, GatheringTier, Tradition | GatheringCreated, GatheringWentLive, CheckInRecorded | IV | NOT "Event" — ADR-011 |
| Memory | Memory, Media, Story, Album | MemoryCreated, PhotoUploaded | V | Memory ≠ Media — ADR-009 |
| Trust | TrustProfile, TrustEdge, TrustTimeline, FraudSignal | TrustGranted, TrustScoreRecomputed | XIII | ADR-002 |
| Opportunity | Opportunity, Ticket, Booking | OpportunityEmerged, TicketPurchased | XV/XXI | Requires ProvenanceRecord |
| Payments | PaymentIntent, PayoutRecord, WalletLedger | PaymentIntentCreated, PaymentIntentVerified | XIX | Flutterwave connector |
| Notifications | NotificationRecord, DeviceToken | — | XXII | Six categories only |

**Cross-cutting (not a domain — views over all domains):**
Knowledge Graphs (Volume VIII) | Twins (Volume IX) | Agents (Volume X) | Relationship (Volume II)
