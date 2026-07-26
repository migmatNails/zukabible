# ADR-034 — The client dependency pile (supersedes ADR-030)

```
Status:   ACCEPTED (Tier 1) — founder disposed 2026-07-20: "accept T1 and run F024".
          Tier 1 is ACCEPTED and lands in pubspec under this ADR.
          Tier 2 remains APPROVED-IN-PRINCIPLE (not in pubspec; the QR-painter
                 choice is still an open founder pick — see Tier 2).
          Tier 3 remains REFUSED until a session needs it.
Date:     2026-07-20
Session:  drafted at founder request after F022/F024/F025 all stalled on this gate
Deciders: Kiyingi James (founder)
Supersedes: ADR-030 (Purchase-UI client deps) — PROPOSED, never disposed; its two
            candidates are re-evaluated here with current data (one has aged badly)
Context:  _BACKLOG `adr-030-client-dependency-pile` · LIVING_DOC §7.7/§8.7/§10.11/§12.8
Stack rule: any new dependency requires a formal ADR (SOUL red line 7 / CLAUDE §3)
```

## Context — why this ADR exists now

`pubspec.yaml` is constitutionally bounded: no dependency lands without an ADR.
Several *already-designed* surfaces have been built up to the point where the only
thing missing is a client capability Flutter doesn't ship. The gate is now the
binding constraint on the F-track, not the engineering:

| Blocked surface | Needs | Evidence |
|---|---|---|
| Gathering poster upload (A-08) | image pick + on-device compression | **F024 promoted and spec'd, deliberately held unrun** — its §2 requires founder go-ahead on exactly these |
| External checkout (§7.2) | open the system browser | ADR-030 drafted 2026-07-11, never disposed |
| Ticket QR at the gate (§8) | offline QR *render* | ADR-030, same |
| Gate scanning (§10) | camera + QR *decode* | designed, unbuilt |
| Receipts / share-images (A-05/06/14/18/19) | PDF render, share sheet | designed, unbuilt, **not blocking anything today** |

**F022** deferred its `SettleReveal` widget for want of a QR painter. **F024** cannot
run. **F025** was cancelled for unrelated reasons but its Wi-Fi gating would have
needed `connectivity_plus`. Three consecutive F sessions have hit this wall.

## Decision drivers

1. **Licence is a red line, not a preference.** SOUL red line 7 — a GPL/AGPL
   package infects a proprietary product. ADR-033 already rejected
   `flutter_map_tile_caching` on exactly these grounds. **Every candidate below is
   licence-checked; anything copyleft is disqualified outright, regardless of
   merit.**
2. **Africa-first: APK bytes are a real cost.** 2GB-RAM Android on 3G. A 10MB APK
   increase is a meaningful download barrier in Kampala. Where a package offers a
   smaller variant, the smaller variant is the recommendation.
3. **Supply-chain trust.** Prefer first-party (`flutter.dev`) and verified
   publishers; prefer pure-Dart over native; pin versions; commit `pubspec.lock`.
4. **ADR-027 discipline applies to this ADR itself.** Approving the *whole pile*
   because it is convenient to decide at once would be adopting dependencies ahead
   of need — the same error the founder rejected in F022/F023 (a token reserved
   ahead of its widget). **This ADR therefore tiers the decision and deliberately
   declines to approve the speculative tail.**

## Candidate evaluation (verified on pub.dev, 2026-07-20)

| Package | Licence | Version | Publisher | Health | Native / size |
|---|---|---|---|---|---|
| `image_picker` | Apache-2.0 / BSD-3 | 1.2.3 | **flutter.dev (first-party)** | published 19 days ago | native, small |
| `flutter_image_compress` | **MIT** | 2.5.0 | fluttercandies (verified) | published 3 days ago | native (Kotlin), small |
| `url_launcher` | BSD-3 | — | **flutter.dev (first-party)** | actively maintained | negligible |
| `qr_flutter` | BSD-3 | 4.1.0 | theyakka.com (verified) | ⚠️ **last published ~3 years ago** | pure Dart |
| `pretty_qr_code` | **MIT** | 3.6.0 | ⚠️ unverified uploader | published 5 months ago | pure Dart |
| `mobile_scanner` | BSD-3 | 7.4.0 | steenbakker.dev (verified) | published **hours** ago; 1.1M downloads | native; **+3–10MB bundled MLKit, ~600KB unbundled** |
| `share_plus` | BSD-3 | 13.2.1 | fluttercommunity (verified) | published 5 days ago; Flutter Favorite | native; toolchain floor (see risks) |

**No candidate here is copyleft.** The licence red line is satisfied by all of
them; the differentiators are maintenance, size, and need.

## Decision (PROPOSED — pending founder)

### Tier 1 — ADOPT NOW (unblocks work already queued)

- **`image_picker`** (^1.2.3) — first-party, the obvious choice, no real
  alternative worth arguing.
- **`flutter_image_compress`** (^2.5.0) — MIT, actively maintained, native
  compression (materially faster than Dart-only on a 2GB device, which matters
  when A-08's whole promise is "2.1 MB → 74 KB").
- **`url_launcher`** — first-party; `LaunchMode.externalApplication` is precisely
  the §7.2 anti-phishing posture (system browser, address bar visible). Carried
  unchanged from ADR-030; it was never controversial.

**Rationale:** these three unblock F024 (poster upload) and the checkout path.
Both surfaces are designed, specified, and waiting.

### Tier 2 — ADOPT WHEN THE CONSUMING SESSION IS SCHEDULED (approved in principle, not yet in pubspec)

- **QR render (§8).** ⚠️ **ADR-030's recommendation has aged out.** `qr_flutter`
  has not been published in ~3 years. It is pure-Dart and probably still works,
  but adopting a 3-years-stale package into a constitutionally-bounded pubspec is
  the kind of supply-chain debt this ADR exists to prevent.
  **Recommendation: `pretty_qr_code`** (MIT, 3.6.0, 5 months old, pure Dart,
  `CustomPainter`, offline) — with the caveat that its publisher is *unverified*,
  which is a real if lesser supply-chain concern. **Founder choice between:**
  (a) `pretty_qr_code` — current but unverified publisher;
  (b) `qr_flutter` — verified publisher but 3 years stale;
  (c) an in-repo painter over the pure-Dart `qr` encoder both wrap — most work,
      least external risk, and genuinely viable (the encoder does the hard part).
  My lean is **(c) then (a)**: a QR painter is not much code, and ADR-033 already
  set the precedent of writing the ~120-line thing in-repo rather than taking a
  dependency.
- **QR decode / gate scanning (§10) — `mobile_scanner`** (^7.4.0). Excellent
  health. **Adopt the UNBUNDLED MLKit variant (~600KB via Play Services), not the
  bundled one (+3–10MB)** — on Africa-first grounds this is not a close call.
  Caveat to accept knowingly: unbundled requires Play Services present, so the
  scanner degrades on devices without it. The gate scanner is an *organiser*
  device, not an attendee device, which makes that trade acceptable.

**Why Tier 2 is not Tier 1:** no session is queued for these. Adopting them today
would be reserving capability ahead of need. When the QR/scanner session is
scheduled, this ADR is the standing approval — it needs only a version pin, not a
new ADR.

### Tier 3 — NOT APPROVED (deliberately)

- **PDF/receipt render, share-image** (A-05/06/14/18/19; `pdf`, `printing`,
  `share_plus`). Nothing is blocked on these. No session is queued. They are the
  speculative tail of the pile.
- `share_plus` additionally imposes a **toolchain floor** (Java 17, Kotlin 2.2.0,
  AGP ≥8.12.1, Gradle ≥8.13) — a build-environment change on a WSL2 box that
  already has a documented Gradle memory ceiling. That is a real cost to take on
  for an unbuilt feature.
- **These are refused for now on ADR-027 §2a grounds** and should be decided by
  the session that actually needs them.

## Consequences

- **Accepting Tier 1 unblocks F024 immediately** and clears the checkout path.
- Each adopted package is version-pinned and `pubspec.lock` is committed. Major
  bumps are routine maintenance, not new ADRs.
- **ADR-030 becomes SUPERSEDED** on acceptance. Its `url_launcher` call survives
  intact; its `qr_flutter` call is explicitly revised on maintenance grounds — a
  concrete example of why an undisposed ADR decays.
- `connectivity_plus` is **not** in this ADR: F025's cancellation removed its only
  consumer. If a future session needs connectivity-type detection it argues for it
  then.
- **Nothing here authorises a screen.** This ADR grants capability, not scope; the
  consuming sessions still pass their own gates.

## Risks accepted

- `flutter_image_compress` requires Kotlin ≥1.5.21 and HEIC support is Android
  API-28+ with a working hardware encoder — **wrap in try/catch and fall back to
  JPEG** (the package's own guidance; make it a DoD line in the poster session).
- `image_picker` on Android: under memory pressure the system may kill
  MainActivity — **`retrieveLostData()` at startup is mandatory**, not optional,
  on a 2GB target. Also requires `launchMode: singleTask` (not `singleInstance`).
- `mobile_scanner` unbundled depends on Play Services availability (above).

## What this ADR does NOT do

- Does not add anything to `pubspec.yaml`. Per SOUL red line 7 / CLAUDE §3, that
  happens only after the founder marks this ACCEPTED — and, per the ADR-033/F013
  precedent, the acceptance may be given at a session-confirmation gate.
- Does not approve Tier 3.
- Does not settle the Tier 2 QR-painter choice — that is an explicit founder pick
  between three named options.

## Status note

**DISPOSED 2026-07-20 — founder accepted Tier 1** ("accept T1 and run F024").

- **Tier 1 ACCEPTED and landed**: `image_picker`, `flutter_image_compress`,
  `url_launcher` entered `pubspec.yaml` under this ADR, with `pubspec.lock`
  committed (supply-chain discipline). ADR-030 is now formally **SUPERSEDED**.
- **Tier 2 unchanged — APPROVED IN PRINCIPLE, nothing in pubspec.** The
  QR-painter choice (`pretty_qr_code` / `qr_flutter` / in-repo painter) was NOT
  made at this disposal and remains open; the session that needs it makes the
  pick under this ADR without requiring a new one. `mobile_scanner` likewise
  stands pre-approved **in its unbundled-MLKit form only**.
  - **`mobile_scanner ^7.4.0` PINNED 2026-07-21 at the F028 (scanner Phase 2)
    gate — founder-ratified, unbundled-MLKit form, `pubspec.lock` committed.** It
    is the §10 gate scanner's camera + QR decode. The unbundled-MLKit build config
    and the runtime camera permission are DoD lines verified at the device gate
    (the headless box can't build the APK). The QR-*render* painter choice stays
    open — no consumer yet.
- **Tier 3 unchanged — REFUSED.** PDF/receipt/share-image are not approved. A
  future session wanting them argues the toolchain-floor cost then.

Consequence: **F024 (`gathering-poster-media-upload`) is unblocked** — its §2
dependency gate is satisfied by this acceptance and was amended to cite it, so
the build does not stop to re-ask.

---

## Addendum — Tier 2 QR-render painter PICKED (2026-07-25, T3 deep-link magnet)

The session that needed the Tier-2 QR-render painter (T3, the resource-template
deep-link magnet QR) makes the pick **under this ADR** as its Status note authorises
(no new ADR).

**PICK: `pretty_qr_code ^3.6.0`** (BSD-3, pub.dev verified; built on the `qr` package;
actively maintained — supersedes ADR-030's aged-out `qr_flutter` recommendation and the
in-repo-painter option). Founder-confirmed 2026-07-25.

- **Why not `qr_flutter`:** ~3 years stale (flagged in the Tier-2 table).
- **Why not an in-repo painter:** QR encoding (Reed-Solomon + masking) is error-prone
  for a must-be-scannable output; a maintained, tested library is the robust choice for
  a correctness-critical artifact.
- **Isolation (robustness):** the dependency is touched by exactly ONE ZUKA widget,
  `lib/features/templates/deep_link_qr.dart` (`DeepLinkQr`). Every template renders the
  magnet QR through that seam, so the painter is swappable without a codebase sweep.
  → **AMENDED 2026-07-25 — this is now TWO widgets. See the amendment below before
  acting on this bullet.**
- **`pubspec.lock` committed** at adoption (supply-chain discipline, per Tier 1's precedent).

**Scope note:** the QR encodes the **https universal link** (`https://zukaapp.com/g/<id>`
— domain confirmed 2026-07-25; `zuka.ug` was unavailable), so any camera funnels a non-app
scanner toward the app/store. The universal-link domain
association (AASA / `assetlinks.json` on `zukaapp.com` + a web store-redirect page) is an
**ops/deploy dependency**, founder-side — the QR is correct-by-construction now and fully
resolves for non-app users once the domain is wired. The `zuka://g/<id>` app scheme
(already routed) covers the app-installed case regardless.

---

## Amendment to the Tier-2 addendum — a SECOND `pretty_qr_code` call site (2026-07-25, F037)

The addendum above records isolation as an accepted property: *one* ZUKA widget touches
`pretty_qr_code`. **F037 (Smart Bucket credential screen, SB-1) makes that two**, and the
record is amended here rather than left to a docstring (RF037-2). No new dependency, no
change to the Tier-2 pick — only to the isolation claim.

**The two call sites, and why they are deliberately not one:**

| Widget | Encodes | Render settings |
|---|---|---|
| `lib/features/templates/deep_link_qr.dart` (`DeepLinkQr`) | the public https **magnet** link | smooth (rounded) modules, EC M, small on-artifact size |
| `lib/features/tickets/bucket_qr.dart` (`BucketQr`) | the Smart Bucket **credential** (`ZUKA:BKT:v1:…`, ADR-036) | **square** modules (`roundFactor: 0`), EC M, gate size, never an embedded image |

A magnet is a public funnel a stranger may photograph; a credential is not. Collapsing
them into one widget would let the two surfaces share a code path and drift into each
other — a shared default is exactly how a credential ends up rendered with a magnet's
settings, or worse, a magnet's *audience*. The credential's payload is several times
longer, so its symbol is denser and module rounding costs the most precisely on the cheap
gate cameras it is aimed at; that is why the settings differ, and why they must be allowed
to differ.

**Consequence for the swap, which is the property the original bullet was protecting:**
swapping the QR painter now means touching **two** files, not one, and the credential
widget's square-module setting must be carried across deliberately — a painter swap that
silently restores module rounding degrades scanning at the door. Both widgets carry a
docstring pointing at the other.

**Standing rule this sets:** a further `pretty_qr_code` call site requires another
amendment here. If a third appears, that is the signal to introduce a shared low-level
painter seam with the two policy widgets on top — not to keep adding rows to this table.
