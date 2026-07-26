# ZUKA — Concept Document
# The Live Streaming System ("hear the city before you go")

> **Type:** Feature Concept & Design Brief · **Tier:** 2 (Phase 2 system; Mode 4
> is the near-term spike) · **Status:** Proposed (pre-build)
> **Authority:** Subordinate to ZukaBibleV4. Expands Vol 13 Ch 8 (The Audio &
> Video Streaming System) and Vol 20 (transport stack). This document explores
> and specs; it does not amend the Bible. **ADR-032 was ACCEPTED by the
> founder 2026-07-16** (Mode-4 segment-push architecture + client deps
> authorized). Nothing here ships until the remaining blocking gate clears:
> qualified-counsel clearance of §5 (DPPA incl. cross-border transfer, music
> copyright/UPRS, UCC licensing).
> **Provenance:** Founder streaming-architecture brainstorm ("the pass-through
> tower"), 2026-07-16, translated to ZUKA's locked stack and Kampala reality.
> Un-THINs the "Audio & Video Streaming — Full Feature Spec" entry in
> `CONCEPT_Unspecced_Features_Register.md`.

---

## 1. The idea in one paragraph

A Gathering that has gone live should be **audible from the City Pulse feed**: a
listener anywhere in Kampala taps "Listen in" on a Live Now card and hears the
room — no login, no hardware on the venue side beyond the organiser's own phone.
The stream is not a broadcast product; it is a **ticket-purchase funnel** (Vol 13
Ch 8: "listen >2 min → 'Sounds good? 3 tickets left at UGX 15,000'"). The
architecture beneath it is deliberately boring: the venue phone slices audio into
small self-contained segments, pushes them to storage ZUKA already runs, and a
CDN ZUKA already fronts with does all fan-out. No streaming server exists anywhere
in the system. That property — **origin does the thinking, static HTTP does the
scaling** — is the through-line of every mode below.

## 2. The pass-through-tower translation

The source brainstorm described a Node.js "pass-through tower": one process
holding the live stream, throttle-slicing it, and fanning chunks out to every
listener socket — with relay tiers or a CDN once one process can't hold the
sockets. Every insight in it survives; almost every *component* changes, because
ZUKA's stack is locked (CLAUDE §3) and its Migration Ladder forbids
scale-stage infrastructure at MVP.

| Pass-through-tower brainstorm | ZUKA-native equivalent |
|---|---|
| Long-running Node slicer process ("the tower") | **The venue phone is the tower.** The Flutter organiser app records and slices; no server process exists. Node/Express is outside the locked stack. |
| Relay tiers + Redis pub/sub for fan-out | **Rejected** — Redis and relay fleets are scale-stage Migration-Ladder targets (`ZUKA_Infrastructure_Technology_Guide.md`), adopted only on an observed metric trigger. |
| CDN chunk delivery (HLS/CMAF) — "what the industry converged on" | **Adopted as the only delivery path.** HLS is static files over HTTP: segments live in Supabase Storage, Cloudflare (already the locked CDN) does fan-out for free, to any listener count. |
| Rolling burst-buffer so joins start instantly | The **HLS playlist window** (~10 × 6 s segments): a joining client sees the last ~60 s, starts at the live edge, buffers backwards. Same trick, zero custom code. |
| Slice on frame boundaries, not byte counts | **ADTS/AAC segments** — the elementary audio stream is self-synchronizing; any join point decodes cleanly. This is why v1 is audio-only and container-free. |
| Fragmented MP4 for video + free A/V sync | Deferred to **Mode 3** (sync screening). When video arrives it is fMP4/CMAF, where shared presentation timestamps give A/V sync for free. |
| Broadcast clock + timed cues (ID3 / WebSocket) | Segment `seq × duration` **is** the stream clock. Cues (polls, announcements, "last 10 tickets") publish over **Supabase Realtime** carrying a target stream-timestamp; the client fires the cue when playback crosses it, so late joiners stay in sync. HLS timed ID3 is the in-band upgrade if Realtime cue drift is ever observed. |
| Reconnect = re-pipe; client auto-retry with backoff | Stronger here: delivery is **stateless HTTP**, so there is no session to lose. Client handles `stalled`/`error` with short exponential backoff against the playlist URL. Degrade ladder when 3G can't sustain audio: drop to the **Live Wall + Crowd Meter** (text/imagery over Realtime) — the gathering still *feels* live. |

Two Kampala inversions the source brainstorm didn't have to make:

- **Audio-first, not video-first.** 2 GB-RAM Android on 3G. v1 is mono 48 kbps
  AAC-LC ≈ **~21 MB/hour** — and that number is shown to the listener before
  they tap (data honesty is an Africa-first UX requirement, not a nicety).
- **The uplink is the scarce resource, not the downlink.** The venue phone
  uploads ~36 KB every 6 s — deliberately sized to survive congested venue 3G.

## 3. The four modes, specced

Phase order: **4 → 1 → 3 → 2.** Mode 4 proves the funnel with zero hardware;
everything else waits for its evidence.

### Mode 4 — Atmosphere Audio (the spike; full architecture in §4)
- **What:** zero-hardware ambient streaming from a venue phone in a good
  acoustic spot (Vol 13: "accessible to every venue regardless of setup").
- **Latency budget: ≤ 30 s.** Atmosphere is vibe, not conversation; a listener
  cannot tell 25-seconds-ago from now. This budget is what makes the
  serverless segment-push design possible at all.
- **Transport:** segment-push HLS (ADR-032). Listener needs only an
  HLS-capable audio player.

### Mode 1 — Live Board-Out Audio
- **What:** board-quality audio from the venue's mixer. **Latency budget: < 3 s**
  (Vol 13) — a hard real-time requirement segment-push cannot meet.
- **Transport:** true WebRTC. The documented upgrade path is **Cloudflare
  Stream Live / Cloudflare Calls** (WHIP ingest from the venue app →
  LL-HLS/WebRTC out of the CDN ZUKA already uses) — a managed replacement for
  the relay tier, adopted via its own future ADR **only after Mode 4 proves
  listen→ticket conversion** (cost is per-delivered-minute; see ADR-032
  alternatives for the math).
- **Setup flow (the register's missing piece):** organiser plugs a 3.5 mm or
  USB-C audio interface from the mixer's aux/monitor out into the venue phone;
  the app detects the wired source and switches the ingest from mic to line-in.
  The *streaming pipeline is identical to Mode 4* — board-out at Mode-4 latency
  is available on day one as "Mode 1-lite"; the <3 s budget is what requires
  the WebRTC upgrade, not the cabling.

### Mode 3 — Sync Screening
- **What:** host-controlled synchronised video playback for distributed
  audiences (movie nights, watch parties). **Sync budget: < 200 ms** between
  participants (Vol 13).
- **Transport:** this is *not* live capture — the content is a file. Video
  ships as **fMP4/CMAF chunks** (Storage + CDN, same delivery spine as Mode 4);
  synchronisation is a **Supabase Realtime broadcast channel** carrying the
  host's clock (`play/pause/seek @ media-timestamp`, drift-corrected by
  participants adjusting playback rate, never seeking). A/V sync inside the
  stream is free (shared PTS in fMP4); only inter-*device* sync is ZUKA's job.
- Licensing posture for screened content is a `zuka-legal` question flagged
  now, unresolved (§6).

### Mode 2 — Shazam-Style Track ID
- **What:** identify the playing track, queue it on the listener's
  Spotify/Apple Music.
- **Not streaming at all:** the attendee's phone samples ~10 s locally and
  calls a fingerprint API. **ACRCloud/AudD is a new vendor ⇒ its own ADR**,
  plus DPPA §5 applies to the sample. Phased last; nothing in Modes 4/1/3
  depends on it.

### The funnel (common to every audible mode)
The player surface is a conversion surface, always:

```
Listener state            Overlay
listening 0–2 min         Gathering name · venue · Crowd Meter state ("Packed")
listening > 2 min         "Sounds good? Tickets from UGX 15,000" → 3-tap ticket
tickets scarce (cue)      "3 tickets left" (pushed as a Realtime cue, §2)
stream ends / gathering   "Tonight's over — follow the venue for the next one"
```

**Metrics that decide the system's future** (instrumented from the spike's
first night): listen sessions per live Gathering · concurrent listeners (peak) ·
median listen duration · **listen→ticket conversion** · listen→follow
conversion. Mode 1's ADR spends money against these numbers or not at all.

## 4. Mode-4 spike architecture (buildable next, gated on ADR-032)

### Organiser side
- Go Live completes → the organiser's live-Gathering screen offers an
  **"Open the mic"** toggle. It is **never inside the Go Live path itself**:
  Constitutional Proof 1 (tap → pin ≤ 60 s on 3G, measured 16 s/8.1 s
  on-device) must not acquire a new step. Vol 13 Ch 6 already specs this
  ordering ("Activates audio streaming toggle") — this concept keeps it.
- Toggle on → the app records the mic in **6-second, mono, 48 kbps AAC-LC
  segments (~36 KB each)** and uploads each to Supabase Storage at
  `live-audio/{gathering_guid}/{seq}.aac` (GUID per canon:
  `ZKA_GATHERING_<UUID>`), then advances the playlist (below).
- Screen-off/backgrounded recording, battery drain, and an interrupted-upload
  retry queue are the three client engineering risks; the build session proves
  all three on the reference 2 GB device before the toggle ships.
- Toggle off, Gathering end, or 3 consecutive missed segment deadlines →
  stream marked ended; listeners get the end-state overlay (§3 funnel table).

### Delivery
- A rolling **`playlist.m3u8`** (window ≈ 10 segments ≈ 60 s) is regenerated on
  each segment arrival — served via Storage/Edge Function with a **short edge
  TTL (~2 s)** at Cloudflare, the same edge-cache posture ADR-021 set for the
  discovery feed (`DESIGN_Discovery_Feed.md` §cache-purge).
- Segments are **immutable** → `Cache-Control: immutable`, CDN caches them
  indefinitely for their short life; every listener beyond the first is served
  entirely from the edge. Fan-out cost ≈ 0; listener ceiling ≈ CDN's, not ours.

### Listener side
- City Pulse **Live Now** card gains a "Listen in" affordance (speaker glyph +
  **"~21 MB/hr"** data label). **Zero-login listening** — same posture as
  zero-login discovery; the soft-wall triggers on *write* actions (buying),
  never on listening.
- Player: burst-buffers the playlist window, plays at live edge, reconnects on
  stall with backoff, reuses the same player element so the UI never visibly
  dies. Degrade ladder per §2.
- UX detail for `zuka-designer` (design pass owed before build): the player is
  a persistent mini-bar over the feed, not a full screen — listening while
  browsing *other* gatherings is the discovery-flywheel case.

### Lifecycle & data posture
- Segments are **ephemeral transport bytes, not Media** (ADR-009: Media is
  bytes *held as evidence* — checksummed, tiered, referenced by Memories).
  Live segments create **no Media row and no Memory row**; a Vibe Drop someone
  captures *is* Media. Any future "save this set" feature would be a new,
  consent-bearing flow graduating segments into real Media entities — out of
  scope here.
- **pg_cron** deletes segments and playlists shortly after stream end, and the
  build must pair that with a **Cloudflare cache purge** — immutable edge
  copies would otherwise outlive origin deletion until TTL expiry. Retention is
  measured in minutes. Posture wording matters: this is **"short-lived and
  minimized," not "no persistence"** — bytes do transit third-party Storage and
  CDN edges for their life (§5).
- Stream liveness state (`streaming: true` on the Gathering's live status) rides
  the existing discovery snapshot; no new Realtime dependency is required for
  the spike (cues in §2/§3 are the first real Realtime use, and can land after
  audio-only v1).

### Readiness (three-axis check)
`manifestation_depth ≤ min(DRL, BRL, CRL)`:
- **DRL-safe:** no predictions, no scoring, no intelligence — capture and
  replay only.
- **BRL:** requires the Storage+CDN path (not yet built) and two new client
  dependencies (recorder, HLS player) — **that is ADR-032**, and it is the
  only gate.
- **CRL:** listening is public-by-design (the Gathering is already publicly
  live); no trust-state interaction. Trust state appears nowhere in this
  system and is never exposed (standing RLS invariant, untouched).
- Register blockers ("Go Live and ticketing flows, Phase 1") are **now built**,
  which is what makes the spike schedulable at all.

## 5. Legal flag — DPPA, copyright, communications (blocking, before build)

`zuka-legal` reviewed this section pre-build (2026-07-16); its findings are
folded in below. Everything here is an **input for qualified Ugandan counsel**
(CLAUDE §8), never final advice. Counsel clearance of this section is a
**blocking** pre-build gate, recorded in ADR-032's consequences.

### 5.1 The two largest exposures (counsel first)

- **Music copyright — Mode 4's own problem, not just Mode 3's.** The stream's
  dominant content *is* the venue's music. Streaming a DJ set or live band to
  an unlimited public audience is a **communication to the public** of
  copyrighted works and performers'/producers' neighbouring rights under the
  Copyright and Neighbouring Rights Act 2006 — typically licensed via the
  Uganda Performing Right Society (UPRS). The organiser attestation cannot
  cure this: the venue usually does not hold the communication-to-public
  right. Licensing posture (UPRS blanket licence? per-venue? ZUKA as the
  communicating party?) must be resolved by counsel before build.
- **Cross-border transfer (DPPA 2019 s.19).** Segments containing bystander
  voice are personal data, and they land in Supabase Storage and Cloudflare's
  global edge — infrastructure very likely outside Uganda. That is a
  cross-border transfer needing adequacy or a valid s.19 basis, regardless of
  how short the retention is.

### 5.2 Data-protection posture (corrected framings)

- **Organiser attestation = contractual risk-shifting, NOT consent.** DPPA
  consent must come from the data subject (the bystander), freely given,
  specific and informed. The toggle-on attestation (organiser confirms
  authority to transmit the venue's ambient sound and that attendees are
  notified) establishes a warranty/indemnity layer only; ZUKA still needs its
  own lawful basis as controller/processor — counsel to identify it.
- **Venue notice guidance:** ZUKA provides standard signage/announcement copy
  ("This gathering's atmosphere is live on ZUKA") in the organiser flow —
  notice at the point of capture, not buried in terms. Notice ≠ consent.
- **Data-subject rights gap (flagged, unresolved):** a bystander has no
  opt-out, objection, or deletion route over a live public stream.
  Minimization does not substitute for rights-handling; counsel to advise
  what mechanism (if any) the DPPA requires here.
- **Minimization by architecture:** minutes-scale retention **plus CDN edge
  purge** (§4 — "short-lived and minimized," never claimed as "no
  persistence"), no recording persisted by default, no voice analysis of any
  kind. Note honestly: (a) the ADR-009 "not Media" classification is internal
  ontology and **carries no legal weight**; (b) the dominant DPPA risk is
  *disclosure to the public*, which no-analysis does not mitigate; (c) any
  listener can re-record the stream, so minimization does not bind downstream
  copies — residual controller liability is a counsel question.
- **The acoustic reality argument** (for counsel, not a conclusion): a phone
  in a good acoustic spot at a packed gathering captures crowd noise and
  music, not intelligible individual conversation. Placement guidance should
  make that the norm, but the legal posture must not depend on it — and the
  argument is partly self-defeating: the truer it is, the more squarely the
  content is copyrighted *music* (§5.1).

### 5.3 Further questions for counsel (checklist)

- **UCC / Communications Act 2013:** does a public live audio stream reachable
  by anyone constitute a regulated communications/broadcasting or content
  service requiring UCC authorization?
- **PDPO registration:** confirm ZUKA's registration as data
  collector/controller/processor under the 2021 Regulations covers (or is
  extended to cover) live ambient-audio processing.
- **Interception exposure:** whether ambient capture/transmission of
  third-party speech engages the Regulation of Interception of Communications
  Act 2010 and/or the Computer Misuse Act 2011 (counsel to confirm the current
  live text post the Jan-2024 Constitutional Court ruling on the 2022
  amendment).
- **Special-category & children's data:** overheard speech can incidentally
  reveal religion, politics, health; minors are commonly present at
  gatherings — do heightened DPPA duties attach to incidental capture?
- Mode 2 (fingerprinting attendee-captured samples) and Mode 3 (screened
  content licensing) carry their **own** DPPA/IP questions, flagged in §6.

## 6. What this concept does NOT decide

- **When Option A (Cloudflare Stream Live / Calls) is adopted.** Trigger is
  evidence-based: Mode-4 conversion data plus a real Mode-1 latency demand —
  then its own ADR with the per-minute cost model.
- **Mode 2 vendor** (ACRCloud vs AudD) or whether Mode 2 survives
  reality-checking at all.
- **Mode 3 content licensing** posture (with `zuka-legal`).
- **Overlap with Festival Mode's Emergency Broadcast** (register: THIN) — an
  organiser→checked-in-attendees broadcast may reuse the cue channel from §2;
  reconciled when Festival Mode is specced, not pre-empted here.
- **Recording/replay of streams** — explicitly out (ADR-009 boundary, §4).
- Final naming of listener-facing surfaces ("Listen in", the mini-bar) —
  `zuka-growth` / Brand Vol 25 own the lexicon.
