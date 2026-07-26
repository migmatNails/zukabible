# ZUKA — Concept Documents

Feature ideas, brainstorms, and design briefs live here — NOT in the Bible.
This keeps ZukaBibleV4 stable and authoritative while ideas are explored freely.

## The flow
```
Concept doc (here)   explore · brainstorm · design · phase · cut-line
        │
        ▼  (only if ratified for build, via the reality-checker + an ADR)
ADR                  graduate the locked canonical entities
        │
        ▼
Bible (Vol 04/14/…)  only the governed result enters the Bible
```

## Conventions
- One file per concept: `CONCEPT_<Name>.md`
- Each concept states its status (Proposed · Ratified · Building · Shipped · Parked),
  reuses existing architecture where it can, lists the NEW entities it would add,
  and draws explicit MVP cut lines / phases.
- A concept does NOT amend the Bible. It proposes; the ADR process disposes.
- Run a concept through `zuka-reality-checker` before committing to build.
- SHARED ENTITIES: when two concepts use the same entity (e.g. `EventPoster`,
  `AttendancePresence` shared by the Poster Acquisition Engine and the Vendor
  Marketplace), it is defined in ONE owning concept and referenced by the other —
  never redefined. The owning doc is noted in each concept's header.

## Index

### Event-experience concepts
| Concept | Status | Summary |
|---------|--------|---------|
| In-Event Vendor Marketplace | Proposed | Attendance-gated, event-scoped vendor listings + ordering, activated by the entrance scan |
| Gathering Communication OS | Proposed | Official Announcements (organiser/staff/security, EMERGENCY priority class) vs Community Bulletins (checked-in attendees) as **separate trust domains** (the RS024-F1 lesson applied at design time), plus first-class Lost & Found (structured report → deterministic DRL-safe match suggestion → double-opt-in contact → staff handover; Smart Bucket intake). Closes the Vol 13 Ch 11 / Unspecced-Register Festival-Mode THIN gap. **NOT MVP** — Phase A (announcements) is the first post-launch candidate. Owns `gathering_announcement`, `gathering_bulletin`, `lost_item_report`, `found_item_report`, `item_match`, `item_handover`. Build directive: `docs/fable-bible/04_COMMUNICATION_OS_DIRECTIVE.md` (2026-07-10, Fable-5). |
| Table Delivery | Deferred | Order-to-seat fulfilment — blocked on Phase-2 ordering + a table/seat-location concept (see vendor marketplace Ch 11) |
| Poster Acquisition Engine | Proposed | Printable QR poster: user check-in + non-user read-only web preview + attributed installs + organiser credit/status. Owns the shared poster/presence mechanic. |
| Signal Gatherings | Proposed (feed seam MVP; engine Tier 2) | Auto-sourced provisional gatherings from opt-in/official feeds — floored beneath real Gatherings, confidence-as-state + decay, claim/corroborate graduation. Owns the general provenance model. `zuka-legal`-gated. From the 2026-07-04 feed brainstorm. |
| Gift Ticket Growth Loop | Ratified (already Bible-canonical, Vol 14 Ch 9 — build-scoping only) | The pre-launch growth priority (2026-07-05 compendium brainstorm, Thread D): scopes the acquisition-mechanics depth (landing page, deep-link survival, group/table gifting, monetisation question) around an already-specified gift-ticket state machine + already-registered events. No new entities. Shares the non-user-preview pattern with Poster Acquisition Engine. |
| Venue Knowledge Graph | Proposed — **Parked** | Canonical multi-source venue graph + owner claiming, reframed from a "Place OS / Volume XXXVI" proposal. Two layers: a live Discovery Layer (Google/OSM never persisted) + an owned Participation Graph (steward + first-party data only). Extends `venue` (Vol 04 Ch2); **references Signal Gatherings** for the provenance/claim-graduation model. Editor vs Steward-Commerce claim split; dedup gated on a labelled corpus; intelligence DRL-gated. From the 2026-07-05 nine-agent brainstorm. |

### Infrastructure & compliance concepts
| Concept | Status | Summary |
|---------|--------|---------|
| Event-Store PII Erasure | Proposed | Reconciles ADR-015 append-only immutability with DPPA-2019 right-to-erasure: keep raw PII **out** of `domain_events` payloads (by-reference default; crypto-shred/pseudonym deferred). Surfaced by R002/S002. Candidate ADR-025. |
| Notification Pipeline (REFUND_QUEUED → buyer SMS) | Proposed | ZUKA's first async-notification seam: `pg_cron` scans the immutable event store → `pgmq` → an Edge Function consumer calls Africa's Talking, as a pure downstream consumer of `PaidTicketRefundQueued` (money fn untouched — RS032 lesson). Phone resolved **just-in-time from Flutterwave by `flw_transaction_id`, never stored** (PII-by-reference; legal transactional-SMS basis). Owns `notification_dispatch` + a `notify_sms` queue + `send-sms` Edge Function; thin reuse seam (`template_key`/event-type allowlist) for later ticket/gift notices. MVP = the refund slice → **build S033**. From the 2026-07-14 design pass (researcher+legal+designer). `CONCEPT_NotificationPipeline.md`. |

### Commercial & economic concepts
> **Payment-rails gated:** `zuka-legal` + `zuka-security` lead any money-flow
> change (NPS Act 2020, SOUL red line 4). Money is `BIGINT` minor UGX. Captured
> 2026-07-06 from an external brain-dump — the source's *"Approved for
> Implementation"* self-stamp is **void** (concepts cannot self-ratify).

| Concept | Status | Summary |
|---------|--------|---------|
| Secondary Ticket Marketplace | Proposed — reality-checked (TRIM) | Verified-ownership resale (never QR/screenshot resale). **Reality check 2026-07-09 corrected the premise:** V4's transfer engine is *specified but unbuilt* (no transfer RPC/events; `TRANSFERRING` is a bare CHECK value), so the free-transfer slice is a full subsystem — **post-launch + demand-gated, not MVP**. Paid market (seller-direct, no ZUKA-held funds), waitlist allocation, markup caps dormant behind **ADR-029**. Reviewed by reality-checker/reviewer/security/legal (2026-07-09): `zuka-legal` — **seller-direct is necessary but NOT sufficient**; market facilitation + a per-transaction fee may trigger NPS-Act obligations regardless of custody, and merchant-of-record may be unachievable for peer sellers (5 preconditions gate ADR-029, §12). `zuka-security` build-requirements in §11. Reseller detection DRL 2+. From the 2026-07-09 founder-directed brainstorm. |
| Settlement Capability | **Adjudicated 2026-07-06 → absorbed** | Triaged: the one buildable idea (payment-blind order fulfilment) moved to **Vendor Marketplace §9B** (Phase 2); wallet/split/payment-states dropped (money-posture + rule conflicts). Retained as provenance. Decision record in `docs/superpowers/specs/2026-07-06-settlement-triage-phase2-fulfilment-design.md`. |
| Commercial & Operational Dimensions | **Adjudicated 2026-07-06** | Triaged: CI/CD "Constitution Pipeline" → parked to the ADR-027 backlog; monetization model → reference-only (splits/policy-engine dormant); vendor interaction models → absorbed into Vendor Marketplace Ch 3H. Retained as provenance. |

### Living gap tracker
| Document | Purpose |
|----------|---------|
| Unspecced & Thin Features Register | Living document tracking every known feature gap — unspecced, thin, graduated, or dormant. Update this whenever a gap is found or resolved. |

### V7 reconciliation set (mined from the "Architecture Bible V7" document)
> Start with the index. Everything here is reconciled to V4 — V4 stays the
> constitution; these feed it via ADR. See the index for the V7→V4 name-clash table.

| Concept | Tier | Summary |
|---------|------|---------|
| V7 Reconciliation Index | Map | The master map: triage verdict, V7→V4 name-clash table, how the set feeds V4. **Read first.** |
| Context Fusion & Intent Engine | 1 (jewel) | One QR, infinite experiences — fuse context → infer intent → show the right operations. Rule-based first, inferential at DRL 2+. |
| Scanner Pro Capability Model | 2 | One capability-gated scanner; clean time-boxed delegation. Additive to V4's scanner (Vol 14). |
| Smart Bucket Reconciliation | 2 | Resolves the V4↔V7 name clash: V4's entry mechanic is canonical; V7's "operational container" is a future extension of the same bucket. |
| Zero-Recreation Docking | 2 (post-MVP) | Evidence-based onboarding — import evidence not trust; Supabase-first, DPPA-gated. Friction-killer for the 30-venue pre-launch. |
| V7 Dormant Vision Register | 3 (parked) | Honestly parks ~18 big V7 OSes (Resource/POE/PPN/SIOS/GIE/OIOS/Federation/full Commerce + the 18-month roadmap) with V4 homes + activation triggers. |