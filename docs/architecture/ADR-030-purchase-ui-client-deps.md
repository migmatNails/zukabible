# ADR-030 — Purchase-UI client dependencies (url_launcher + a pure-Dart QR painter)

```
Status:   SUPERSEDED by ADR-034 (2026-07-20, founder-disposed). This ADR was
                     never itself disposed; ADR-034 absorbed both candidates and
                     re-evaluated them with current data. Outcomes:
                       · `url_launcher` — survived unchanged, ACCEPTED (ADR-034 T1).
                       · `qr_flutter`  — recommendation AGED OUT (no publish in
                         ~3 years). ADR-034 reopened the QR choice on maintenance
                         grounds; it is Tier 2 and still undecided.
                     Retained for history. Do not dispose or cite this ADR for
                     new work — cite ADR-034.
Date:     2026-07-11
Session:  S029 (purchase-ui-backend-prereqs) · deliverable 4
Deciders: Kiyingi James (founder)
Context:  FDES-01 §7.2 (external-checkout anti-phishing posture) · §8 (offline QR)
Stack rule: any new dependency requires a formal ADR (SOUL red line 7 / CLAUDE §3)
```

## Context

FDES-01 designed the attendee purchase + ticket surfaces. Two of those screens
cannot be built with the current dependency set:

1. **Opening the hosted Flutterwave checkout.** §7.2 rules the payment step must
   hand off to an **external browser**, never an embedded webview — an embedded
   webview trains users to enter MoMo credentials inside our app chrome, which is
   exactly the phishing surface we must not normalise. Flutter cannot open an
   external URL without a platform channel or a plugin.

2. **Rendering the ticket QR offline.** §8 renders the ticket's `qr_token` as a
   scannable QR at the gate — where 3G is unreliable and often absent. The QR
   must render from local data with **zero network** (no hosted QR image
   service). Flutter has no built-in QR painter.

Both are one-time client-capability additions with no backend change. S029 ships
the backend (`gathering_detail`, `my_tickets`, ZK410) the build pair consumes;
this ADR unblocks that pair's client work.

## Decision drivers

- **Anti-phishing posture is non-negotiable** (§7.2): the checkout opens in the
  device's real browser, address bar visible.
- **Africa-first**: 2GB-RAM Android on 3G. APK size, cold-start cost, and
  **offline** behaviour all matter. A QR painter that hits the network is
  disqualified outright.
- **Supply-chain trust** (SOUL red line 7 + supabase supply-chain guidance):
  prefer first-party / widely-audited packages; pin versions; commit the lockfile.
- **No native bloat**: prefer pure-Dart where a plugin is not strictly required.

## Candidates

### (1) External URL launch

| Candidate | Notes | Africa-first |
|---|---|---|
| **`url_launcher`** (recommended) | Flutter-team first-party (flutter/packages). `LaunchMode.externalApplication` forces the system browser — precisely §7.2's posture. Tiny, ubiquitous, long-maintained. | Negligible APK impact; no runtime network of its own; battle-tested on low-end Android. |
| Raw platform channel / `android_intent_plus` | Works, but re-implements what url_launcher already wraps and adds our own native code to maintain. | Similar size; more of our own code to audit. |

**Recommendation: `url_launcher`** — first-party supply-chain trust, exactly the
external-browser semantics §7.2 requires, minimal surface.

### (2) Offline QR painter

| Candidate | Notes | Africa-first |
|---|---|---|
| **`qr_flutter`** (recommended) | Renders a QR to a `CustomPainter` — pure-Dart, no platform channel, no network. Depends only on `qr` (pure-Dart encoder). Most-used Flutter QR widget. | Cheap to paint on 2GB; fully offline; no native binary added. |
| `pretty_qr_code` / others | Comparable capability; smaller ecosystems, more styling than we need. | Similar; less battle-tested. |
| A hosted QR image URL | Rejected: violates the offline-at-the-gate requirement. | Fails 3G/offline — disqualified. |

**Recommendation: `qr_flutter`** — pure-Dart, offline, `CustomPainter`-based
(low memory), painting the `qr_token` locally with no network.

## Decision (PROPOSED — pending founder)

Adopt **`url_launcher`** (external-browser checkout) and **`qr_flutter`**
(offline ticket QR). Pin exact versions and commit `pubspec.lock`. No other
dependency rides in on this ADR.

## Consequences

- The purchase build pair can open Flutterwave checkout externally and render the
  ticket QR offline — both FDES-01 blockers cleared.
- Two dependencies enter the locked stack; each must be version-pinned and the
  lockfile committed (supply-chain discipline). Future major-version bumps are
  routine maintenance, not new ADRs.
- If the founder prefers to defer the QR painter (e.g. paint QR by hand via a
  minimal in-repo encoder), url_launcher can still be accepted independently —
  the checkout path is the more urgent of the two.
- **Not in scope:** any Flutter screen code (the build pair), embedded-webview
  fallback (explicitly rejected), and deep-link return handling (`zuka://payment-return`
  is already speced in living-doc §9; it needs no new dependency).

## Status note

This ADR is **PROPOSED**. Per SOUL red line 7 / CLAUDE §3, no dependency is added
to `pubspec.yaml` until the founder marks it ACCEPTED. S029 adds nothing to
`pubspec.yaml`; it only records this decision for disposal.
