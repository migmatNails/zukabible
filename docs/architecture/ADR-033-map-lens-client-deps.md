# ADR-033 — Map-lens client dependencies (flutter_map + Mapbox raster tiles + own disk cache + geolocator)

```
Status:   ACCEPTED 2026-07-18 (founder confirmed the F013 session gate —
                     `zuka confirm F013`, merged to main at a8c69a2 — after
                     being explicitly flagged that confirming constituted
                     accepting this dependency bundle. Mechanism per the
                     Status note below: the orchestrator's (ADR-031)
                     session-confirmation gate is where the founder disposes.)
Date:     2026-07-17
Session:  F013 (map-lens-flutter-map-pins) · slice 2 of the map-lens build order
Deciders: Kiyingi James (founder)
Context:  docs/superpowers/specs/2026-07-16-mvp-map-lens-design.md §2 Decision #1,
          §3.1 (rendering & dependencies) · Design Living Doc §2 (Nearby/Map — the
          lens law) · CLAUDE.md §5 (Africa-first reality, the three MVP proofs)
Stack rule: any new dependency requires a formal ADR (SOUL red line 7 / CLAUDE §3 /
          ADR-016 dependency bound)
```

## Context

The map lens (design spec slice 2) makes the existing `Map` toggle in `LensControl`
a real spatial view of the *same* in-memory Pulse feed. It cannot be built with the
current ADR-028 dependency set (`flutter_riverpod` + `go_router` + `supabase_flutter`
+ `sentry_flutter` only). Four capabilities are missing:

1. **A map canvas that renders on a 2GB-RAM Android on 3G without OOM/jank.**
2. **Map tiles** — the raster imagery the canvas draws.
3. **Offline tile behaviour** — a Kampala nightlife app must not blank when the
   network drops mid-session, and repeated panning must not re-download the city.
4. **Optional device location** — a "Use my location" affordance that asks the OS
   permission only on explicit tap (zero-permission by default, DPPA-clean).

The design brainstorm already settled the rendering approach (Decision #1); this
ADR records the dependency decision that flows from it and resolves the open ToS /
pricing / token / location-package questions the spec carried into slice 2.

## Decision drivers

- **Africa-first is the hard constraint** (CLAUDE §5): 2GB-RAM Android, 3G, offline
  and low-data are first-class. Predictable memory and bounded data usage outrank
  visual richness. A dependency that risks OOM on a low-end device is disqualified.
- **Zero-permission discovery mirrors zero-login** (MVP proof #2): the map opens
  with all feed pins and asks for nothing. Precise location is untouched until an
  explicit tap (DPPA data-minimisation).
- **Mapbox down never breaks discovery**: tile loss degrades to a blank/grey canvas
  with pins and the bottom sheet still fully functional — never an error screen.
- **Supply-chain trust** (SOUL red line 7): prefer first-party / widely-audited,
  pure-Dart where possible; pin versions; commit the lockfile. Avoid GPL-encumbered
  packages that would infect the app's licensing.
- **Dependency minimalism** (ADR-016 / ADR-028 minimum-viable-shape): add only what
  the slice needs; write the thin glue ourselves rather than pull a heavy package.

## Decision #1 — Rendering: `flutter_map` (pure Dart), not native GL

`flutter_map` renders raster tiles + widget markers in pure Dart over the standard
Flutter compositor. **`mapbox_maps_flutter` (native Mapbox GL) is rejected** —
already ruled out in the design brainstorm (Decision #1): a native GL `PlatformView`
carries real OOM/jank risk on 2GB devices, per-MAU pricing, and a heavier binary.
Static-image-only was also rejected: it under-delivers a co-primary lens.

`flutter_map` gives us pins as ordinary Flutter widgets (so `FeedItemCard` colours,
the `colourFor()` law, and reduce-motion all apply for free), predictable RAM (no
GL surface), and full control over the tile pipeline.

**Adopt:** `flutter_map` (+ `latlong2`, its `LatLng` coordinate type — pure-Dart,
tiny, ubiquitous, imported directly to build markers and drive the camera).

## Decision #2 — Tiles: Mapbox **raster** (Static Tiles API)

Consume Mapbox's server-rendered raster tiles:

```
https://api.mapbox.com/styles/v1/mapbox/dark-v11/tiles/256/{z}/{x}/{y}?access_token=<TOKEN>
```

- `dark-v11` matches the Midnight theme (Vol 25) — the map reads as ZUKA, not a
  generic Google-blue slab.
- **Zoom clamped to z11–z17** (city ↔ street). Tiles outside the band are never
  requested — bounds discovery to Kampala scales and caps data.
- Server-side rendering keeps the *client* free of a vector style engine (no extra
  RAM), which is the whole point of choosing raster over native vector GL.

## Decision #3 — Cache: our own ~50MB LRU disk cache, written in-repo

- Disk cache keyed `z_x_y.png`, ~50MB LRU cap, in the app cache directory
  (`path_provider.getTemporaryDirectory()`), read-through: disk → network → write →
  decode. Eviction is oldest-modified-first once the directory exceeds the cap.
- **Written in-repo (`lib/features/discovery/map/tile_cache.dart`).** We do **not**
  adopt `flutter_map_tile_caching`: it is **GPL-licensed** and would infect the
  app's licensing — a hard no for a proprietary product (SOUL red line 7). The
  cache is ~120 lines of `dart:io`; the Rule-of-Three does not justify a heavy
  dependency for it.
- The cache is **best-effort**: any disk failure falls through to a plain network
  fetch and never blocks a tile. Offline behaviour falls out naturally — a
  previously-viewed area renders from disk with no network.
- **Cache substrate only in this ADR.** ~~The one-time Kampala core-tile *seed*
  (z11–z14 pre-warm on first Wi-Fi) is the design's B-stage and ships as a separate
  fast-follow session (`map-lens-kampala-tile-seed`); it needs no new dependency.~~
  **UPDATE 2026-07-20 (F025): the seed is CANCELLED, not deferred** — the ToS gate
  this ADR carried to that session did not clear (see the disposed carry bullet
  below). The passive LRU cache described here is the final posture for B-stage;
  there is no fast-follow seed session to schedule.

### Mapbox ToS / pricing check (resolved here per spec §8)

- **Pricing:** Mapbox raster tiles bill per *tile request* (the "Raster Tiles API"
  free tier is generous — hundreds of thousands of requests/month). Our own disk
  cache + the z11–z17 clamp + the feed-driven (not free-pan-the-continent) usage
  keep request volume low, and the LRU cache means a returning user in a
  previously-viewed area costs **zero** requests. This is the cost-control posture.
- **Offline caching & ToS:** Mapbox's Terms permit **temporary caching to improve
  performance**, but **prohibit building a permanent/offline redistributable copy
  of the map** and cap cached-tile counts/TTL under the standard raster terms. Our
  design is ToS-aligned by construction: a **volatile ~50MB LRU** device cache
  (performance, not redistribution), no export, no sharing between devices, and a
  hard z11–z17 / Kampala-scale bound. **Action carried to the slice-3 seed session:**
  before pre-seeding z11–z14 for the whole metro, re-verify the then-current Mapbox
  raster-tile terms for the specific *offline pre-seed* volume; if the seed would
  breach the cached-tile cap, ship passive-cache-only (still respectable) or move to
  a self-hosted tile source. The **passive cache in this ADR is unambiguously within
  ToS** and does not depend on that check.
- **Token handling** — see Decision #5.

## Decision #4 — Location: adopt `geolocator`, folded into **this** ADR

The spec (§3.1) left open whether a location package folds into this ADR or needs
its own. **Decision: fold it in.** The "Use my location" chip is part of this
session's Definition of Done and cannot be honestly implemented without a location
plugin, so splitting it would ship a dead chip. `geolocator` (community-standard,
well-maintained, the de-facto Flutter geolocation plugin) provides exactly the
minimal surface needed: check/request permission, get one position.

**Posture (DPPA-clean, spec §3.3):**
- **Zero permission on open.** The map centres on Kampala with all feed pins; no
  location call is made.
- The **only** trigger is an explicit "Use my location" chip tap → OS permission
  prompt → on grant, recenter + a blue dot; on deny/deny-forever/service-off,
  nothing visible changes and the chip remains. Precise location is never read
  without that tap — data-minimisation by construction.
- `geolocator` is wrapped behind a thin in-repo `LocationService` with total
  try/catch, so a plugin/permission failure degrades to "no dot" and never crashes
  discovery.

**Independence note (mirrors ADR-030):** if the founder prefers to defer geolocator
to its own ADR, the map lens still ships — the chip renders disabled and the other
three dependencies (`flutter_map`, `latlong2`, `path_provider`) stand on their own.
The three-dep map is fully useful without device location; geolocator is the one
separable item in this bundle.

## Decision #5 — Token handling

- The Mapbox token used is a **publishable** token (client-side by design), **URL-**
  **and app-restricted** in the Mapbox console — not a secret key. A **secret**
  Mapbox key must **never** ship in the client.
- It is injected at build time via `--dart-define` (`MAPBOX_TOKEN`), consistent with
  ADR-028 §4 and the existing `Env` pattern (`SUPABASE_ANON_KEY` is handled the same
  way) — **never hardcoded, never committed**, even though it is public-by-design,
  so a real project's token can't leak via copy-paste.
- An **empty** token (unconfigured dev) simply means tiles 404 → blank/grey canvas
  with pins still functional. Discovery never breaks on a missing token.

## Decision (ACCEPTED 2026-07-18 — founder confirmed the F013 gate)

Adopt, as one map-lens bundle, pinning exact versions and committing `pubspec.lock`:

| Package | Role | Nature |
|---|---|---|
| `flutter_map` | pure-Dart raster map canvas + widget markers | pure Dart |
| `latlong2` | `LatLng` coordinate type (flutter_map's own) | pure Dart, tiny |
| `path_provider` | app cache directory for the own-built LRU tile cache | Flutter-team first-party plugin |
| `geolocator` | "Use my location" (permission + one position), Decision #4 | community-standard plugin |

No other dependency rides in on this ADR. The tile cache and `LocationService` glue
are **written in-repo**, not pulled as packages.

## Consequences

- The map lens ships: raster canvas, feed-driven pins, tap→bottom-sheet, zero-
  permission Kampala default, opt-in location, and all five states (loading / empty
  / offline / low-data / tile-failure) — with Mapbox outage never breaking discovery.
- Four dependencies enter the locked stack; each is version-pinned and the lockfile
  committed (supply-chain discipline). Future major bumps are routine maintenance,
  not new ADRs.
- Android needs `INTERNET` (already present) and, only for the opt-in chip, the
  `ACCESS_FINE_LOCATION` / `ACCESS_COARSE_LOCATION` manifest entries `geolocator`
  documents. **The manifest permission is declared but never exercised until the
  user taps the chip** — declaration ≠ prompt.
- **~~Carried to slice 3 (seed session):~~ DISPOSED 2026-07-20 by F025 — NO-GO.**
  The re-verification of Mapbox offline-caching terms for the z11–z14 metro
  pre-seed volume was carried out and **the gate did not clear; the seed is not
  built.** Decisive reason is engineering, not legal: the Raster Tiles API serves
  `Cache-Control: max-age=43200` (**12h device TTL**) and Product Terms cap device
  offline use at **30 days**, so a "one-time" pre-seed cannot durably deliver
  offline Kampala under any reading — it decays and needs re-seeding. Compounding
  risk: Product Terms prohibit *"scraping or systematically downloading Licensed
  Map Content"*, and a bbox sweep ahead of use reads as systematic downloading;
  Mapbox's sanctioned offline path (Maps SDK `OfflineManager`/`TileStore`) is
  unavailable to us because Decision #1 uses `flutter_map` + raw raster URLs.
  **The passive per-view LRU cache (Decision #3) stands unchanged and is the
  clearly-compliant posture** — populated by direct requests during real use,
  single-user consumption. Full evidence, computed tile volumes (318 tiles/~8MB
  tight core; 1,392/~34MB wider metro) and sources: `docs/sessions/archive/
  F025-2026-07-20-map-lens-kampala-tile-seed.md` (SESSION OUTCOME block).
  Side-effect: `connectivity_plus` is **no longer needed** (it was only required to
  Wi-Fi-gate the seed) — one fewer item on the `adr-030-client-dependency-pile`.
  Not counsel (CLAUDE.md §8); the free definitive step, if ever revisited, is to
  ask Mapbox support directly.
- **Not in scope:** the Kampala tile seed, venue self-service / pin-drop UI (slice
  3), pin clustering, heat-map, standing venue pins, static-image low-data tier
  (all deferred — design spec §1 / §8).

## Status note

This ADR is **ACCEPTED** (2026-07-18). The founder disposed at the orchestrator's
session-confirmation gate as this note prescribed: `zuka confirm F013`, given after
the acceptance implication was flagged twice in-session, merged the staged
`pubspec.yaml` + `pubspec.lock` and the map-lens implementation to main (`a8c69a2`).
All four packages stand, `geolocator` included (Decision #4 — the "Use my location"
chip shipped enabled, opt-in, zero-permission default).

Original acceptance terms (for the record): per SOUL red line 7 / CLAUDE §3 /
ADR-016, no dependency reaches `main` until the founder marks it ACCEPTED; F013
staged the changes on the `session/F013` worktree contingent on that acceptance.
