# ADR-032 — Atmosphere Audio (Mode 4) via segment-push HLS on Storage + CDN

```
Status:   ACCEPTED  (by the founder, 2026-07-16; drafted the same day with
                     CONCEPT_Live_Streaming_System.md. Acceptance authorizes
                     the architecture and dependencies below — the legal
                     co-gate must still clear before any build session)
Date:     2026-07-16 (drafted + accepted)
Deciders: Kiyingi James (founder)
Context:  Vol 13 Ch 8 Mode 4 (atmosphere audio, "most MVP-achievable") ·
          Vol 20 (HLS = the locked low-bandwidth audio transport) ·
          docs/concepts/CONCEPT_Live_Streaming_System.md §4 (the full spec)
Stack rule: any new dependency or infrastructure capability requires a formal
          ADR (SOUL red line 7 / CLAUDE §3)
Blocking co-gate: qualified-counsel clearance of concept doc §5 — DPPA posture
          (incl. cross-border transfer s.19), music copyright / UPRS licensing
          (communication to the public), and the UCC-licensing question.
          ACCEPTED alone does not unblock a build session.
```

## Context

Vol 13 Ch 8 specs a four-mode streaming system (Phase 2) whose cheapest mode —
atmosphere audio from a venue phone — is now schedulable: its blockers (Go Live,
ticketing) are built, and the concept doc turns the founder's "pass-through
tower" architecture brainstorm into a design that fits the locked stack. What it
cannot fit for free is: (a) two new Flutter client capabilities (mic capture,
HLS playback), and (b) first use of Supabase Storage + a Cloudflare caching
policy for live segments. Both require this ADR.

## Decision drivers

- **Locked stack, no drift:** no Node relay process, no Redis pub/sub, no new
  streaming vendor. Delivery must ride Supabase + Cloudflare, which ZUKA
  already runs.
- **Prove the funnel before buying latency:** Vol 13 frames streams as ticket
  funnels. Spend engineering, not per-minute vendor fees, until
  listen→ticket conversion is measured.
- **Africa-first:** 2 GB-RAM Android on 3G on both ends. Venue uplink budget
  ~36 KB / 6 s; listener downlink ~21 MB/hr, disclosed in the UI.
- **Latency budget is the design's permission slip:** atmosphere audio
  tolerates ≤ 30 s. Only that tolerance makes a serverless segment-push
  design viable. It is NOT a path to Mode 1's <3 s.
- **Supply-chain trust** (ADR-030 precedent): first-party/widely-audited
  packages, exact version pins, lockfile committed.

## Decision (ACCEPTED 2026-07-16)

Build Mode 4 as **segment-push HLS with no streaming server**:

1. The organiser app records mic audio in **6 s mono 48 kbps AAC-LC (ADTS)
   segments**, uploaded to a new Supabase Storage bucket **`live-audio`** at
   `live-audio/{gathering_guid}/{seq}.aac`, with a rolling ~10-segment
   `playlist.m3u8`.
2. **Cloudflare caching policy:** segments `Cache-Control: immutable`;
   playlist edge TTL ~2 s (ADR-021 edge-cache posture).
3. **Short-lived by structure:** pg_cron deletes segments/playlists minutes
   after stream end, paired with a **Cloudflare cache purge** (immutable edge
   copies would otherwise outlive origin deletion). Segments are transport
   bytes, **not Media entities** (ADR-009) — no Media/Memory rows are ever
   created by streaming. (The not-Media classification is internal ontology,
   not a legal shield — concept doc §5.2.)
4. **Two client dependencies** enter the locked stack (exact package chosen at
   build time from these candidates, same disposal style as ADR-030):

| Capability | Candidates | Notes / Africa-first |
|---|---|---|
| Mic capture → AAC segments | **`record`** (recommended) · `flutter_sound` | `record` is the leaner, widely-used capture plugin; AAC-LC encoding via platform encoder (no Dart-side transcode on a 2 GB device). `flutter_sound` is heavier but carries more codec control if `record`'s segment rotation proves awkward. |
| HLS audio playback | **`just_audio`** (recommended) · `video_player` (audio-only use) | `just_audio` wraps ExoPlayer, plays HLS natively, is the de-facto Flutter audio player, and supports the background/mini-bar pattern the concept specs. `video_player` also plays HLS but is the wrong shape for a background audio surface. |

No other dependency rides in on this ADR.

## Alternatives considered

- **Cloudflare Stream Live / Calls (managed WHIP → LL-HLS)** — ~5–10 s latency
  and near-zero fan-out engineering, but a new paid product at
  ~$1 / 1,000 delivered minutes: a single 100-listener × 60-min gathering ≈
  **$6 per gathering** before conversion is proven, plus storage minutes. **Deferred,
  not rejected:** it is the documented Mode-1 (<3 s board-out) upgrade path,
  to be adopted via its own ADR when Mode-4 conversion data justifies the
  per-minute cost model (concept doc §3/§6).
- **WebRTC SFU (LiveKit/Janus/etc.)** — best latency, but a new self-run
  service + vendor: exactly the scale-stage infrastructure the Migration
  Ladder forbids without an observed trigger. Rejected for the spike.
- **Supabase Realtime as the audio transport** — Realtime is a
  message/broadcast channel, not a media pipeline; pushing ~36 KB binary
  frames through it inverts its design and bypasses the CDN. Rejected;
  Realtime's role is the *cue* channel (polls, scarcity nudges), not bytes.

## Consequences

- Mode 4 becomes buildable with **zero new vendors and ~zero marginal
  delivery cost**; listener fan-out is bounded by the CDN, not by ZUKA.
- **Latency ceiling ~20–30 s** is accepted and permanent for this
  architecture; any product need below it re-opens the Stream Live ADR.
- Two client dependencies to pin + lockfile-commit; the `live-audio` bucket
  and its pg_cron cleanup job become BRL surface the build session must ship
  with tests (upload retry, cleanup actually deletes, playlist rolls).
- The Go Live path gains **no** new steps (Constitutional Proof 1 untouched);
  "Open the mic" is strictly post-live.
- **Funnel instrumentation is part of the build, not optional:** listen
  sessions, peak concurrency, median duration, listen→ticket conversion —
  these numbers are what any Mode-1 spend will be judged against.
- Blocking co-gate stands: qualified-counsel clearance of concept §5 (DPPA
  incl. s.19 cross-border, music copyright/UPRS, UCC question) before any
  build session is minted. `zuka-legal`'s 2026-07-16 pre-review is already
  folded into §5; it surfaces, it does not clear.

## Status note

This ADR is **ACCEPTED** (founder, 2026-07-16). The segment-push architecture,
the `live-audio` bucket, and the two client dependencies are authorized —
versions pinned and lockfile committed when the build session adds them. The
**legal co-gate remains open and blocking**: no build session is minted until
qualified-counsel clearance of concept doc §5 (DPPA incl. s.19 cross-border,
music copyright/UPRS, UCC licensing).
