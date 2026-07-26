# ZUKA — Flutter Design Tokens

**Source of truth:** `docs/architecture/bible-v4/25_brand_identity_system · MD`,
Chapter 4 (The Colour System) and Chapter 5 (Typography). **Every value below
is ported, not invented** — if a value here ever disagrees with Vol 25, Vol
25 wins and this file is stale (fix it, don't trust this copy blindly).
**Status:** Living reference, not an ADR — colours/type are Vol 25's
jurisdiction (`zuka-designer` / brand governance), not a frozen engineering
contract. This file exists purely so a Flutter session pastes correct Dart
instead of re-transcribing hex codes from a markdown table by hand.

---

## 1. Colour — "Colour as Information" (LOCKED brand law, Vol 25 Ch 4)

**The hard rule, restated because it is load-bearing:** Live Coral means
**LIVE RIGHT NOW** and nothing else. It is never decorative, never used for
a "featured" badge, never used because something looks good in coral. A
payment success state must never wear Coral — Coral means live, not
"good news" (Vol 25 Ch 4, line 199). This rule is enforced in the design
living doc (`ZUKA_DESIGN_LIVING_DOC.md` §1 UX Rule: "Colour-as-information is
literal here — a coral card means live, nothing else") — the Flutter layer's
job is to never make it possible to violate this by accident, which is
exactly why these are named semantic tokens, not raw `Color(0xFF...)`
literals scattered through widget code.

```dart
// lib/core/theme/zuka_colors.dart
import 'package:flutter/material.dart';

/// Ported verbatim from Vol 25 Ch 4. Do not add new brand colours here
/// without a Vol 25 amendment — this file mirrors the Bible, it doesn't
/// extend it.
class ZukaColors {
  ZukaColors._();

  // Brand — status/energy colours (Vol 25 Ch4 lines 171-178)
  /// LIVE RIGHT NOW. Reserved. Never decorative, never "featured", never a
  /// success/confirmation colour. See the hard rule above.
  static const liveCoral = Color(0xFFFF4D4D);
  /// Starting soon · primary action · active/energy-rising.
  static const emberGold = Color(0xFFF5A623);
  /// Tonight · scheduled · calm-but-coming.
  static const kampalaForest = Color(0xFF1A3A2A);
  /// Primary background — "the city at 2am."
  static const midnight = Color(0xFF0E0C0A);

  // Neutral ramp — text/surfaces/borders on the Midnight base (Ch4 187-190)
  static const ink0 = Color(0xFF0E0C0A); // base background (== midnight)
  static const ink1 = Color(0xFF1A1815); // raised surface / cards
  static const ink2 = Color(0xFF2A2724); // borders / dividers
  static const ink3 = Color(0xFF6B6863); // secondary text
  static const ink4 = Color(0xFFA8A49E); // tertiary / disabled
  static const ink5 = Color(0xFFF2EFEA); // primary text on dark

  // System colours — deliberately distinct from the brand colours they
  // could be confused with (Ch4 193-196). Do not substitute one for the
  // other even when they look similar.
  /// Confirmations (e.g. "ticket secured") — NOT Kampala Forest.
  static const success = Color(0xFF3A9D5C);
  /// Cautions — NOT Ember Gold (Gold means "energy", not "caution").
  static const warning = Color(0xFFE08A2B);
  /// Failures — NOT Live Coral (Coral means "live", not "error").
  static const error = Color(0xFFD64545);
  /// Neutral system messages.
  static const info = Color(0xFF4A90B8);
}
```

**Accessibility (Vol 25 Ch 4 lines 202-208, non-negotiable):** all text/
background pairs must meet WCAG AA (4.5:1 body, 3:1 large) on Midnight.
Coral-on-Midnight and Gold-on-Midnight specifically need contrast
verification per Vol 25 — do not assume the raw brand hex passes at every
text size; darken/adjust for text use per that check, not by eye.

## 2. Typography — The Type Trio + Scale (Vol 25 Ch 5)

```text
Space Grotesk   Display · wordmark · headlines · the City Pulse count.
                Never for long reading (Vol 25 Ch5 rule).
Inter           Body · UI · labels · everything functional. The workhorse.
Space Mono      Data · timestamps · counts · ticket codes. Used sparingly,
                for precision. Never for prose.
```

**The scale** (Vol 25 Ch 5, "sized for a 2GB Android at arm's length" —
body text must never go below 16px, this is an accessibility floor, not a
style preference):

| Role | Font | Size/Line | Weight | Use |
|---|---|---:|---:|---|
| Display | Space Grotesk | 32/38 | 700 | City Pulse hero, splash |
| H1 | Space Grotesk | 26/32 | 600 | screen titles |
| H2 | Space Grotesk | 21/28 | 600 | section heads |
| H3 | Inter | 18/24 | 600 | card titles |
| Body | Inter | 16/24 | 400 | primary reading (min 16 — never smaller) |
| Body-strong | Inter | 16/24 | 600 | emphasis |
| Caption | Inter | 14/20 | 400/500 | metadata, helper text |
| Micro | Inter | 12/16 | 500 | legal, timestamps (use rarely) |
| Data | Space Mono | 14/20 | 400 | counts, codes, times |

```dart
// lib/core/theme/zuka_typography.dart
import 'package:flutter/material.dart';

/// Ported verbatim from Vol 25 Ch 5. Sizes are logical pixels (Flutter sp).
/// Body must never render below 16 — do not create a smaller "body" variant.
class ZukaTypography {
  ZukaTypography._();

  static const _spaceGrotesk = 'SpaceGrotesk';
  static const _inter = 'Inter';
  static const _spaceMono = 'SpaceMono';

  static const display = TextStyle(
    fontFamily: _spaceGrotesk, fontSize: 32, height: 38 / 32,
    fontWeight: FontWeight.w700,
  );
  static const h1 = TextStyle(
    fontFamily: _spaceGrotesk, fontSize: 26, height: 32 / 26,
    fontWeight: FontWeight.w600,
  );
  static const h2 = TextStyle(
    fontFamily: _spaceGrotesk, fontSize: 21, height: 28 / 21,
    fontWeight: FontWeight.w600,
  );
  static const h3 = TextStyle(
    fontFamily: _inter, fontSize: 18, height: 24 / 18,
    fontWeight: FontWeight.w600,
  );
  static const body = TextStyle(
    fontFamily: _inter, fontSize: 16, height: 24 / 16,
    fontWeight: FontWeight.w400,
  );
  static const bodyStrong = TextStyle(
    fontFamily: _inter, fontSize: 16, height: 24 / 16,
    fontWeight: FontWeight.w600,
  );
  static const caption = TextStyle(
    fontFamily: _inter, fontSize: 14, height: 20 / 14,
    fontWeight: FontWeight.w400,
  );
  static const micro = TextStyle(
    fontFamily: _inter, fontSize: 12, height: 16 / 12,
    fontWeight: FontWeight.w500,
  );
  static const data = TextStyle(
    fontFamily: _spaceMono, fontSize: 14, height: 20 / 14,
    fontWeight: FontWeight.w400,
  );
}
```

Font files (Space Grotesk, Inter, Space Mono — all open-source/Google Fonts)
still need sourcing and registering in `pubspec.yaml`'s `fonts:` section;
that's a session-1 task, not a design decision.

## 3. Spacing — RATIFIED (Vol 25 Ch5, founder-approved 2026-07-18 via A-00)

Vol 25 originally locked colour and type but no spacing scale. This 8px grid
was PROPOSED here, submitted for ratification on the A-00 foundation sheet
(design prompt system Phase 0), and founder-approved 2026-07-18 — now recorded
in Vol 25 Ch5 ("The Spacing Scale"). A standard Flutter/Material convention,
chosen for being "boring and well-supported":

```dart
// lib/core/theme/zuka_spacing.dart
class ZukaSpacing {
  ZukaSpacing._();
  static const xs = 4.0;
  static const sm = 8.0;
  static const md = 16.0;
  static const lg = 24.0;
  static const xl = 32.0;
  static const xxl = 48.0;
}
```

Graduated: the one-line amendment lives in Vol 25 Ch5 ("The Spacing Scale",
2026-07-18). Ratification trail: A-00 foundation sheet →
`ZUKA_DESIGN_LIVING_DOC.md` Design Foundations (A-00 section).

## 4. Status → colour mapping (bridges the living doc's cards to tokens)

`ZUKA_DESIGN_LIVING_DOC.md` §1 Colour Logic, translated to the tokens above —
this table is the one place "which token for which state" is decided, so no
widget re-derives it:

| Gathering/ticket state | Token |
|---|---|
| Live | `ZukaColors.liveCoral` (may pulse — see living doc) |
| Starting soon | `ZukaColors.emberGold` |
| Tonight / scheduled | `ZukaColors.kampalaForest` |
| Free entry | `ZukaColors.ink4` (neutral) |
| Signal (unconfirmed, `CONCEPT_Signal_Gatherings.md`) | `ZukaColors.ink3` (muted/ghosted — never a brand colour, never claims live/soon/tonight) |
| Ticket secured / payment success | `ZukaColors.success` (never `liveCoral`) |
| Payment/validation failure | `ZukaColors.error` (never `liveCoral`) |

## 5. Motion — "Motion as Information" (founder-approved 2026-07-16)

The motion analogue of the colour law. Coral is reserved so that when it
blooms, it means something; motion is reserved the same way. The app is calm,
instant infrastructure by default — **motion is reserved for liveness**.
Spec source: `ZUKA_DESIGN_LIVING_DOC.md` Design Foundations → "Motion as
Information"; this section is the engineering port (like §1–§2 above).

### The three tiers

| Tier | Surfaces | Character |
|---|---|---|
| **T0 — Structure** | navigation, tabs, chrome, layout | Silent or near-instant. Nothing springs, nothing bounces. |
| **T1 — Feedback** | tap acknowledgment, content swaps, sheets, skeleton→content, route pushes | Quick (120–280ms), standard cubic curves. Confirms; never entertains. |
| **T2 — Life** | LIVE pulse dot, go-live coral bloom, ticket/QR reveal | The only expressive motion — longer, characterful. The city came alive *there*. |

### Tokens

```dart
// lib/core/motion.dart (ZukaMotion)
class ZukaMotion {
  ZukaMotion._();

  // Durations — collapse to Duration.zero under reduce-motion (use the
  // d() helper, never the raw constants, for anything that animates).
  static const instant  = Duration.zero;                    // money/trust surfaces
  static const quick    = Duration(milliseconds: 120);      // touch feedback
  static const standard = Duration(milliseconds: 200);      // in-screen swaps
  static const route    = Duration(milliseconds: 280);      // page transitions
  static const routeBack = Duration(milliseconds: 220);     // reverse/pop
  static const bloom    = Duration(milliseconds: 700);      // T2 ONLY
  static const settle   = Duration(milliseconds: 400);      // T2 ONLY — ticket/QR reveal

  // Curves
  static const enter    = Curves.easeOutCubic;   // entrances
  static const exit     = Curves.easeInCubic;    // exits
  static const move     = Curves.easeInOutCubic; // on-screen moves
  static const bloomCurve = Curves.easeOutBack;  // T2 bloom ONLY — nowhere else
}
```

### Hard rules (never-list — enforce in review, like the coral rule)

- **Opacity + transform only.** Never animate layout properties on lists; no
  blur animation; no continuous large-area loops. (2GB-RAM frame budget +
  battery discipline — the doctrine already stated in `status_pill.dart`.)
- **Money and trust surfaces are motion-silent.** Prices never count up,
  trust states never shimmer, `PriceText` never animates. `instant` exists
  as a token precisely to make this rule visible at the call-site.
- **Motion never gates a sacred proof.** Transitions are interruptible, taps
  register mid-transition, the 3-tap path never waits for an animation to
  finish. The 60s Go-Live loop-close timing is unchanged by any animation.
- **`easeOutBack` (overshoot) and `bloom` are T2-only.** A T0/T1 surface
  using either is a violation, same class as decorative coral.
- **Every animation respects reduce-motion.** `zukaReduceMotion` is wired to
  the OS setting (`MediaQuery.disableAnimations`); loops render a static
  frame (existing pattern), one-shots render their end state, transition
  durations collapse to zero. Tests/goldens set the flag directly.
- **No stagger cascades on lists, ever** (3G reflow + battery). List content
  arrives via the skeleton cross-fade only.

No animation packages (flutter_animate etc.) — framework-native only, so no
ADR is required. Adding one later IS a stack change → ADR first.
