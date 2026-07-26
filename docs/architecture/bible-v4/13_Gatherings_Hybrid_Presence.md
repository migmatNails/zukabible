# ZUKA BIBLE V4 — VOLUME 13
# Gatherings, Events & Hybrid Presence

> **Part IV — Experience & Commerce** · **Authority Level 3**
> Synthesises: Canonical Volume IV, Volume XXII (Digital Gathering), ZUKA.md §5-7,19
> ADR-011: The canonical community-scale event is a Gathering, not an Event

---

## Chapter 1 — Event vs Gathering (resolved)

```
EVENT      The product-level term shown to users ("47 events live in Kampala").
           A Tier-1 operational entity: the logistical listing.
GATHERING  The architectural term carrying capital-generation semantics.
           What an Event becomes when it generates memories, relationships,
           and trust.

At MVP, the UI says "Events" (per ZUKA.md). Architecturally every Event is a
Gathering. They are the same record viewed through two lenses: logistics vs
civilisation. (Volume 03 Dictionary.)
```

---

## Chapter 2 — The Gathering Medium Model (Volume XXII)

```
A gathering is a gathering regardless of medium.
Physical + Digital + Hybrid share ONE canonical model.

GATHERING TYPES (by medium):
  Physical    In-person only
  Digital     Online only
  Hybrid      In-person + remote streaming simultaneously
  Persistent  Ongoing (a community space that's always "open")
  Ephemeral   Spontaneous, short-lived (a Go Live session)
```

---

## Chapter 3 — The Six-State Presence Model

```
Physical Presence      Bodily at the venue
Digital Presence       Connected remotely
Active Presence        Participating (streaming, chatting, contributing)
Passive Presence       Watching/listening only
Contribution Presence  Creating content (Vibe Drops, photos)
Memory Presence        Engaging after the fact (viewing the Live Wall later)
```

All six feed the Attendance Ledger and the Gathering Historian.

---

## Chapter 4 — Gathering Categories (ZUKA.md, 40+ types)

```
Primary (one required): Nightclub · Bar · Live Music · Concert · Comedy ·
  Sports Screening · Movie Night · Cultural · Wedding · Corporate · Party ·
  Festival · Religious · Market · Food & Drink · Exhibition · Theatre ·
  Wellness · Conference · Art Show

Vibe tags (up to 3, optional): Lit · Chill · Family-friendly · Date Night ·
  Afrobeats · Amapiano · Jazz · Gospel · Hip-Hop · EDM · Live Band ·
  Free Entry · Outdoor · Rooftop · Members Only · 18+ · All Ages
```

---

## Chapter 5 — The Gathering Lifecycle

```
DRAFT → PUBLISHED → ACTIVE (within 24h) → LIVE (Go Live / check-in open) →
ENDED → CLOSED (post-event report generated)
Parallel: CANCELLED (any point before ENDED)
```

---

## Chapter 6 — Go Live (the informal-economy feature)

The most important feature for the informal entertainment economy, and MVP Proof 1:

```
Any registered venue/organiser taps "Go Live" once. Instantly:
  Pushes notification to followers within 5km
  Bumps venue to top of Live Now feed
  Activates audio streaming toggle (if connected)
  Drops a pulsing live pin on the heat map
  Enables the crowd meter

No advance scheduling. A bar unexpectedly full on a Monday can go live and fill
further within 20 minutes. No existing platform has built this.

CONSTITUTIONAL PROOF 1: tap "Go Live" → pin visible on another device ≤ 60s on 3G.
```

---

## Chapter 7 — The Crowd Meter & City Pulse

```
Crowd levels: Quiet → Picking Up → Packed → At Capacity
Users within 5km get notified when a followed venue hits "Packed."

City Pulse Strip (home screen top):
  "47 events live in Kampala  [activity bars over last 6 hours]"
  Communicates the city's energy before a single event name is read.

The Heat Map: one-tap toggle from feed. Active venues glow amber;
packed venues pulse coral; quiet zones are dark.
```

---

## Chapter 8 — The Audio & Video Streaming System (ZUKA.md §7)

ZUKA's most significant technical differentiator. Four modes (Phase 2, post-MVP):

```
MODE 1 — LIVE BOARD-OUT AUDIO
  Venue connects Zuka's audio bridge to their sound system output.
  Clean board-quality audio streamed to remote users (<3s latency, WebRTC).
  Conversion: listen >2 min → "Sounds good? 3 tickets left at UGX 15,000."
  The audio stream is a purchase funnel.

MODE 2 — SHAZAM-STYLE TRACK ID
  Audio fingerprinting (ACRCloud/AudD) identifies the playing track,
  queues it on the user's Spotify/Apple Music.

MODE 3 — SYNC SCREENING
  Host-controlled synchronised video playback for distributed audiences
  (movie nights, sports watch parties). WebSocket sync, <200ms.

MODE 4 — ATMOSPHERE AUDIO
  Zero-hardware ambient streaming via a venue phone in a good acoustic spot.
  Accessible to every venue regardless of setup.
```

---

## Chapter 9 — The Check-In System & Vibe Drops

```
Check-in (via Smart Bucket QR or manual):
  Confirms attendance → feeds crowd data + heat map
  Unlocks Vibe Drop (15-sec anonymous clip → event's Live Wall)
  Awards Pulse Points (Volume 14)
  Enables real-time vibe rating
  Logs to private attendance history
  Creates an AttendanceSeedRecord (→ Memory prompt, Volume 10)

The Live Wall: a rolling mosaic of Vibe Drops. Social proof engine that converts
remote viewers into attendees.
```

---

## Chapter 10 — Tradition Detection

```
A Tradition = Repeated Gathering + Time + Shared Participation + Memory.
The Tradition Agent (Volume 18) detects the pattern after recurring gatherings;
the community confirms and names it (never auto-named).
Lifecycle: Seeded → Active → At-Risk → Lapsed → Revived.
```

---

## Chapter 11 — Festival Mode (ZUKA.md §31)

```
Multi-day, multi-stage events. Features:
  Festival Map (stages, food, toilets, first aid, exits)
  Schedule Builder with clash detection
  Stage-specific check-in and Go Live
  Multi-day passes with per-day entry tracking
  Artist Paging ("Fireboy DML on stage in 15 minutes")
  Lost & Found
  Emergency Broadcast (organiser → all checked-in users instantly)
```

---

## Chapter 12 — Gathering Domain Events

```
GatheringCreated · GatheringUpdated · GatheringPublished · GatheringWentLive ·
GatheringEnded · GatheringCancelled · RSVPSubmitted · CheckInRecorded ·
TraditionDetected · TraditionStrengthened · TraditionLapsed · TraditionRevived ·
AttendanceSeedRecord

Venue aggregate (a venue is where gatherings happen — registered 2026-07-18,
emitted by 20260717120002/3 create/deactivate-venue RPCs):
VenueCreated · VenueDeactivated

Gathering poster media (registered 2026-07-20, emitted by 20260718120001
attach/clear-gathering-poster RPC — the poster is Media, ADR-009):
GatheringPosterAttached · GatheringPosterCleared
```

---

> **Volume 13 — Gatherings, Events & Hybrid Presence**
> A gathering is a gathering regardless of medium.
