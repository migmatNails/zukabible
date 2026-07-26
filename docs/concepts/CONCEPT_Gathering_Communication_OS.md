# CONCEPT — Gathering Communication OS
### Official Announcements · Community Bulletins · Lost & Found

```
Status        Proposed (Fable-5 authored, 2026-07-10, from founder brain-dump)
Owner         This doc owns: gathering_announcement, gathering_bulletin,
              lost_item_report, found_item_report, item_match, item_handover
References    Vol 13 Ch 11 (Festival Mode: Lost & Found, Emergency Broadcast,
              Artist Paging) · Vol 12 (Community OS) · Vol 15 (Discovery/
              Attention) · CONCEPT_Unspecced_Features_Register (Festival Mode
              THIN entry) · CONCEPT_Scanner_Pro_Capability_Model (Smart Bucket
              intake) · CONCEPT_In_Event_Vendor_Marketplace (attendance gating,
              Ch 3) · CONCEPT_Venue_Knowledge_Graph (Parked — venue-persistent
              extension) · RS024-F1 (the shared-table read-surface lesson)
Reality-check NOT MVP. Nothing here ships before the three sacred proofs are
              live in Kampala. Phase A is the first post-launch candidate.
Gates         zuka-designer (channel UX) · zuka-security (contact non-exposure,
              claim fraud) · zuka-legal (DPPA: photos, contact brokering,
              found-property law) · zuka-reality-checker (phase cut lines)
```

---

## 1 · The founder's premise (verbatim intent, translated to canon)

A Person reporting lost keys is not making an *announcement* — it is a
**community bulletin**. Mixing attendee posts into the organiser's official
channel makes the official channel noisy and untrustworthy. So ZUKA separates
two systems that share a delivery surface:

```
OFFICIAL ANNOUNCEMENTS   organiser · staff · security · permitted vendors
COMMUNITY BULLETINS      checked-in attendees and other participants
```

And Lost & Found graduates from "write a post about it" to a **first-class,
structured workflow**: report → intake → match → verify → hand over — with
contact details never exposed until both parties consent, and staff always in
the loop for the physical handover.

This is already constitutionally seeded: Vol 13 Ch 11 names **Lost & Found**
and **Emergency Broadcast** as Festival Mode features, and the Unspecced
Features Register flags exactly this gap ("Lost & Found flow, Emergency
Broadcast … missing"). This concept is the spec that closes that THIN entry —
generalised beyond festivals to every Gathering.

## 2 · The channel model

Every LIVE Gathering exposes a small, fixed set of channels. Channels are a
**presentation taxonomy**, not six tables — but the two trust domains behind
them are **physically separate tables** (see §4, the RS024 lesson).

```
Gathering
 ├── Announcements   official; organiser/staff/security; priority classes
 │     └── EMERGENCY  a priority class of announcement, not a separate system
 │                    (Vol 13: "organiser → all checked-in users instantly")
 ├── Live Updates    official; low-priority announcement class (schedule slips,
 │                   artist paging "on stage in 15 minutes")
 ├── Marketplace     ALREADY BUILT — vendor_feed() (S023). Not this concept.
 ├── Lost & Found    structured reports + matches (this concept, Phase B)
 ├── Help            attendee → staff questions (Phase C, deferred)
 └── Community       bulletins by checked-in attendees (Phase C)
```

Rules:
- **Write authority is per-channel, never per-app.** Official channels accept
  writes only from the organiser and staff roles bound to that Gathering
  (scanner_session staff identity + organiser resolution via
  `person.auth_user_id = auth.uid()`, the existing pattern). Vendors may be
  granted a scoped announcement right (e.g. "kitchen closes 22:00") by the
  organiser — a grant, never a default.
- **Read authority:** announcements are readable by every ticket-holder and
  checked-in Person of that Gathering; whether the anon/zero-login surface sees
  a public subset is a per-announcement `visibility` flag (default: holders
  only). Bulletins and Lost & Found are **attendance-gated** (reuse the vendor
  marketplace's check-in gate — the entrance scan is the key).
- **Emergency** is an announcement with `priority = EMERGENCY`: it bypasses
  quiet settings, fans out via FCM + Realtime, and is rate-limited to
  organiser-only with an event-store audit trail. No new machinery — a class.

## 3 · Lost & Found as a first-class workflow

### 3.1 Report Lost Item (attendee)
Structured, not free text: category (keys · phone · wallet · bag · ID ·
clothing · jewellery · other) · short description · last-known area (free text
or venue zone if GatePlan zones exist) · approximate time · optional photo ·
contact preference. **Reward: cut from every phase** — a promised payment
between two Persons brokered by ZUKA touches the money red line (NPS Act
posture) and invites claim fraud; revisit only with zuka-legal + a dedicated
ADR. Money stays out of this concept entirely.

### 3.2 Found-item intake (finder / staff / Smart Bucket)
Two intake paths:
- **In-app:** any checked-in Person or staff member files a Found report
  (category + where found + optional photo). The physical item goes to the
  info desk — ZUKA coordinates information, staff hold property.
- **Smart Bucket QR:** a Lost & Found bucket at the info desk. Scanning it
  opens the found-item intake form pre-bound to the Gathering + desk location.
  This is a Scanner-Pro Layer-2 capability (references
  CONCEPT_Scanner_Pro_Capability_Model); the bucket token is a scoped intake
  credential, not an admission credential — it must never share the qr_token
  admission surface.

### 3.3 Matching — deterministic first, intelligence never before DRL 2
The constitution is explicit: **immature data ships no intelligence** (DRL 1
forbids prediction). So:
- **Phase B matching is deterministic filtering, not an engine:** candidate
  matches = same Gathering + same category + overlapping time window. That is
  a WHERE clause, not intelligence — DRL-safe.
- The system **suggests; humans confirm** (ADR-014's spirit): both parties see
  "possible match" cards with the *other side's non-identifying fields only*
  (category, colour/description, area). No name, no phone, no photo EXIF.
- **Verification** is finder-mediated: the claimant answers the finder's/staff's
  natural questions ("how many keys? what keychain?") in a masked thread or at
  the desk. ZUKA structures the exchange; a person decides. No auto-release.
- **ML/fuzzy matching, cross-event patterns, serial-claimant detection: DRL 2+
  dormant.** Register the capability; do not build it.

### 3.4 Privacy & handover (DPPA is the design, not a checkbox)
- **Contact non-exposure by default.** A match reveals contact only after
  **double opt-in** ("Would you like to connect?" from both sides), or never —
  the desk handover path needs no contact exchange at all.
- **Photos are personal data.** Stored in Supabase Storage under an
  attendance-gated policy; **never** in `domain_events` payloads (the S011
  PII-by-reference rule). Reports carry storage references.
- **Retention:** reports auto-close N days after `gathering.ends_at`
  (unclaimed → staff disposition per venue policy); erased on DPPA request via
  the ADR-025 (EventStore PII Erasure) posture — events carry report IDs, not
  descriptions of people's property.
- **Handover record:** `ItemHandedOver` names the staff member, the match, and
  the time — the audit trail if a false claim is later alleged.

### 3.5 Organiser dashboard
A read RPC aggregating live counts (lost / found / matched / unclaimed) for the
organiser console. Derived data, no new writes.

## 4 · Entities & events (NEW — nothing here exists yet)

**The RS024-F1 lesson is binding:** vendor tiers hidden inside `gathering_tier`
leaked into every attendee read surface because two trust domains shared one
table. Therefore official announcements and community content are **separate
tables**, each with its own RLS and its own read RPCs — never one `post` table
with a `kind` column.

```
gathering_announcement   gathering_id · author person_id · channel class
                         (GENERAL | LIVE_UPDATE | EMERGENCY) · body ·
                         visibility (HOLDERS | PUBLIC) · published_at
gathering_bulletin       gathering_id · author person_id (checked-in gate) ·
                         body · status (ACTIVE | REMOVED_BY_ORGANISER)
lost_item_report         gathering_id · reporter person_id · category ·
                         description · area · approx_time · photo_ref ·
                         status (OPEN | MATCHED | RECOVERED | CLOSED)
found_item_report        gathering_id · finder person_id NULLABLE (desk/bucket
                         intake) · category · found_area · photo_ref · status
item_match               lost_id · found_id · state (SUGGESTED → ACCEPTED_BOTH
                         → HANDED_OVER | REJECTED | EXPIRED)
item_handover            match_id · staff person_id · handed_over_at
```

Domain events (register in Vol 14 Ch 15 / Vol 13 Ch 12 same-commit, ADR-015):
`AnnouncementPublished · BulletinPosted · BulletinRemoved · LostItemReported ·
FoundItemReported · ItemMatchSuggested · ItemMatchAccepted · ItemHandedOver ·
ItemReportClosed`

RPC surface (all SECURITY DEFINER, REVOKE-from-anon except where noted; full
conventions in `docs/fable-bible/03_API_CONSTITUTION.md`):
`publish_announcement() · announcement_feed() (anon-readable PUBLIC subset,
capped) · post_bulletin() · bulletin_feed() · report_lost_item() ·
report_found_item() · lost_found_feed() · suggest_item_matches() ·
accept_item_match() · confirm_item_handover() · lost_found_dashboard()`

Delivery: **Realtime** on the announcement tables for in-app live updates;
**FCM** fan-out for EMERGENCY and matches (both locked-stack natives).

## 5 · Phases & cut lines

```
Phase 0 (now)        This spec only. Reality line: pre-launch, build NOTHING.
Phase A (post-launch, first real gatherings — 1 session)
                     gathering_announcement + publish/feed RPCs + Realtime +
                     EMERGENCY class + FCM fan-out. The organiser's voice.
Phase B (first venue asks for it — 2 sessions)
                     Lost & Found core: reports, deterministic suggestions,
                     double-opt-in match, desk handover, dashboard counts.
                     Smart Bucket intake IF Scanner-Pro Layer-2 exists by then.
Phase C (community demand — 1–2 sessions)
                     Community bulletins + Help channel + organiser moderation
                     (removal is human — ADR-014).
DORMANT (register, never build early)
                     Venue-persistent Lost & Found (hotels/malls/campuses —
                     blocked on CONCEPT_Venue_Knowledge_Graph, itself Parked) ·
                     ML matching + serial-claim fraud detection (DRL 2+) ·
                     rewards (money red line, legal-gated) · cross-venue
                     ecosystem network effects (the genuinely big idea — and
                     exactly why it must wait until one venue's flow is proven).
```

**Intelligent audience targeting** ("only people still inside the venue"):
requires presence/check-out data that does not exist (check-in exists; exit
tracking does not). Phase A/B audience = ticket-holders + checked-in + staff.
Presence-scoped targeting is registered dormant behind a future GatePlan/
presence concept — do not fake it with heuristics.

## 6 · Why this earns its place (and what would kill it)

Earns it: it deepens the *participation* loop (SOUL: infrastructure, not an
events app), it is organiser-retention surface (the dashboard + emergency
broadcast are things WhatsApp groups cannot do safely), and Lost & Found is a
trust-visible moment — ZUKA returning your keys is a story people tell.

Kills it: building any of it before launch (violates reality-before-vision);
merging bulletins into announcements (the founder's own separation instinct is
correct and RS024 proved the table-level version of the same mistake); shipping
matching "intelligence" at DRL 1; letting rewards smuggle money flow in.

---
*Proposed. Graduates only via reality-checker + ADR. The city just came alive —
and it can hear itself.*
