# ZUKA Design Constitution

> The standing design brief for every ZUKA design session. Pair this document
> with ONE filled `ZUKA_DESIGN_BRIEF_TEMPLATE.md` and design that artifact.
> Self-contained by design: it works pasted into a chat with no repo access.
>
> Authority: subordinate to SOUL.md and ZukaBibleV4 (Vol 25 Brand & Identity).
> The living spec of record is `docs/design/ZUKA_DESIGN_LIVING_DOC.md` — this
> file feeds it and never overrides it.
>
> Last synced: 2026-07-18 against Vol 25 Ch1/Ch3/Ch4/Ch5, `lib/core/theme/`,
> `lib/core/motion.dart`, docs/flutter/ZUKA_FLUTTER_DESIGN_TOKENS.md §5, and
> the living doc's Motion law. If tokens change there, re-sync this sheet.

---

## 1 · Identity

ZUKA is **Human Participation Infrastructure** — it begins as an entertainment
discovery and ticketing product for Kampala and grows into community, trust,
and opportunity coordination across Africa. It is not "an events app." It is
the infrastructure that makes a city's live energy visible and joinable.

```
Tagline   The city just came alive.
Essence   The city, alive.
Context   Kampala, Uganda · solo founder (Ying) · Africa-first, mobile-only.
```

Brand personality — five traits, each with its guardrail:

```
ALIVE         Energetic, present-tense, immediate.     Not manic or hype-y.
LOCAL         Of Kampala, in the language of Kampala.  Not parochial or excluding.
WARM          Welcoming, human, belonging-first.       Not saccharine or fake.
DIRECT        Plain, specific, honest.                 Not blunt or cold.
TRUSTWORTHY   Reliable, safe, transparent.             Not corporate or stiff.
```

## 2 · Non-negotiables (design red lines)

1. **Trust is a state label, never a number.** "Trusted Organiser", "New
   Member" — never "Trust: 84", never progress bars toward trust.
2. **No dark patterns.** No manufactured urgency, fake scarcity, hype emojis,
   countdown pressure, or engagement traps. Honest availability always.
3. **The three sacred proofs are UX budgets no design may regress:**
   - **60-second Go Live** — an organiser publishes a gathering in ≤ 60s.
   - **Zero-login discovery** — the feed, detail, and map need no account.
   - **3-tap ticket** — discovery → ticket in ≤ 3 taps.
   A design that adds a tap, a login wall, or a required field to these paths
   is wrong by definition.
4. **Locked vocabulary.** *Gathering* not "event" in semantic copy (UI may
   label the category "Events"); *Person* not "user"; Memory ≠ Media.
5. **Money is UGX, integer minor units.** Render as `UGX 20,000` — never
   decimals, never `$`, never abbreviations like "20k" in money contexts.
6. **Vendor money is vendor-direct.** Never write copy implying ZUKA holds,
   routes, or pays out vendor order money. The ZUKA Wallet is frozen — no
   wallet UI anywhere.
7. **Coral means LIVE.** See the colour law below — this is enforced like law.

Voice principles:

```
PRESENT-TENSE & IMMEDIATE   "47 events live in Kampala" — not "Discover events."
SPECIFIC OVER CLEVER        "8 spots in Kabalagala, 2km away" — not "Lots nearby!"
LOCAL-FIRST                 Real neighbourhoods, real venues, Kampala cadence.
WARM, NOT HYPED             "Sounds good?" — not "🔥 INSANE VIBES TONIGHT 🔥".
HONEST                      Never imply live-when-pre-launch or scale we lack.
SHORT                       Built for a phone. One idea per line.
```

We say / we never say:

```
WE SAY                              WE NEVER SAY
─────────────────────────────────────────────────────────────────────────
"live now"                          "trending" / "viral"
"the city came alive"               "unlock amazing experiences"
"47 events live in Kampala"         "join thousands of users"
"Sounds good? 3 spots left."        "🔥 Don't miss out! Selling fast!! 🔥"
"Gathering" (semantic copy)         "event" used to mean something disposable
"a quiet night in Ntinda"           a blank empty state
"New Member" / "Trusted Organiser"  "Trust score: 84"
"free entry"                        "FREE!!!" with manufactured excitement
honest availability                 "live now" when it's pre-launch
```

## 3 · Africa-first constraints

- **Reference device:** 2GB-RAM Android, 360×800 logical px, on 3G. Every
  mockup is designed at 360×800 first; larger screens are the adaptation.
- **Body text never below 16px** — an accessibility floor, not a style choice.
- **Contrast:** WCAG AA minimum (4.5:1 body text) — sunlight-readable.
- **Thumb reach:** primary actions live in the bottom half of the screen.
- **No cellular autoplay.** Video/heavy media loads on tap, with its data
  cost shown ("12 MB"). Wi-Fi may autoplay muted.
- **Offline and SMS-fallback are designed states, not error states.** Every
  artifact renders something useful and honest with no connection.
- **Image weight budgets (PROPOSED):** hero/poster ≤ 80 KB, card thumb ≤ 25 KB,
  icon/avatar ≤ 8 KB. Flag the first real violation instead of silently
  shipping it.
- **Responsive adaptation rules (ratified 2026-07-18):** design at 360×800,
  then adapt both directions — never stretch:
  - **320px floor** (small/older Androids): everything still works — no
    horizontal scroll, body stays ≥ 16px, buttons stay full-width, the QR
    stays scannable (min 200px square). Trim decoration, never content.
  - **360–430 (the mass market):** the reference design; fluid widths
    (`max-width:100%`), spacing tokens absorb the difference.
  - **≥ 480 (large phones, landscape):** content column caps at ~480px and
    centres — a phone app's line lengths, never a stretched card.
  - **≥ 600 (tablets, rare here):** stay one-column for flows (forms, ticket,
    checkout — money surfaces never go multi-column); feed/list surfaces MAY
    go two-column card grid; scaffolds' thumb-zone law becomes
    bottom-of-column.
  - Mockups must be built with fluid units + these caps (not fixed 360px
    frames), and every artifact is checked at 320 and ≥ 600 before
    presenting.

## 4 · Token sheet (portable brand law)

Ported from Vol 25 Ch4/Ch5 via `lib/core/theme/`. **No new brand colours
without a Vol 25 amendment.**

### Colour — "Colour as Information" (LOCKED)

| Token | Hex | Means | Hard rule |
|---|---|---|---|
| liveCoral | `#FF4D4D` | LIVE right now | **Reserved.** Never decorative, never "featured", never success/confirmation. |
| emberGold | `#F5A623` | Starting soon · primary action · energy rising | Not a caution colour. |
| kampalaForest | `#1A3A2A` | Tonight · scheduled · calm-but-coming | Not a success colour. |
| midnight | `#0E0C0A` | Primary background — "the city at 2am" | — |
| ink0–ink5 | `#0E0C0A` `#1A1815` `#2A2724` `#6B6863` `#A8A49E` `#F2EFEA` | base bg · raised surface/cards · borders · secondary text · tertiary/disabled · primary text | — |
| success | `#3A9D5C` | confirmations ("ticket secured") | NOT kampalaForest. |
| warning | `#E08A2B` | cautions | NOT emberGold. |
| error | `#D64545` | failures | NOT liveCoral. |
| info | `#4A90B8` | neutral system messages | — |

### Typography — the Type Trio (LOCKED roles)

Space Grotesk = display/headlines/the Pulse count · Inter = body/UI workhorse ·
Space Mono = data/timestamps/codes, sparingly.
HTML mockup fallback stacks: `'Space Grotesk', 'Arial Narrow', sans-serif` ·
`Inter, system-ui, sans-serif` · `'Space Mono', monospace`.

| Style | Family | Size/Line | Weight |
|---|---|---|---|
| display | Space Grotesk | 32/38 | 700 |
| h1 | Space Grotesk | 26/32 | 600 |
| h2 | Space Grotesk | 21/28 | 600 |
| h3 | Inter | 18/24 | 600 |
| body | Inter | 16/24 | 400 |
| bodyStrong | Inter | 16/24 | 600 |
| caption | Inter | 14/20 | 400 |
| micro | Inter | 12/16 | 500 |
| data | Space Mono | 14/20 | 400 |

### Spacing (LOCKED — Vol 25 Ch5, ratified 2026-07-18 via A-00)

`xs 4 · sm 8 · md 16 · lg 24 · xl 32 · xxl 48` (8px grid). Founder-ratified
on the A-00 foundation sheet; image weight budgets (§3) remain PROPOSED.

### Motion — "Motion as Information" (approved 2026-07-16)

Three tiers: **T0 structure is silent** (navigation, tabs, chrome: silent or
near-instant) · **T1 feedback** (quick 120ms / standard 200ms / route 280ms /
routeBack 220ms, easeOut/easeIn/easeInOut cubic — route pushes like Feed →
Gathering Detail are T1) · **T2 life** (bloom 700ms easeOutBack) is the ONLY
expressive motion, reserved for liveness (the LIVE pulse, the Pulse count).
Never-list: money/trust surfaces never animate (instant); bloom on a non-life
surface is the same violation class as decorative coral; motion never gates a
sacred proof (transitions interruptible; the 3-tap path never waits); no
stagger cascades on lists; opacity+transform only; every animation collapses
under reduce-motion.

### Depth & Material — "Depth as Light" (ratified 2026-07-24, A-21 round-trip)

Depth comes from **light, value, and geometry — never bitmap texture or blur.**
Blur (`BackdropFilter`) and image textures look premium in a mockup then jank / blow
the byte budget on a 2GB Android. Every depth effect below is a GPU-cheap gradient,
clip, or type — crisp at any DPR and expressible in a JSON spec + Flutter painters.

**Depth token vocabulary (LOCKED — compose from these, never hand-tune per surface):**

| Token | Value | Purpose |
|---|---|---|
| `elev.rest` | `0 8px 20px rgba(0,0,0,.35)` | the ONE resting card shadow — single, never stacked |
| `elev.raised` | `0 12px 28px rgba(0,0,0,.45)` | selected/pressed (picker) only |
| `sheen.top` | linear 180° `rgba(ink5,.06)` → transparent @45% | directional top light (the material) |
| `vignette` | edge `rgba(ink0,.28)` | the card sits on ground |
| `hairline` | repeating-linear 1px `rgba(ink5,.045)` / 4px | security-paper structure |
| `aura.gold` | radial `rgba(emberGold,.10)` | premium glow — VIP only |
| `ghost` | oversized glyph @ 8% token opacity | a second visual plane, zero cost |

**The bright line:** permitted — flat linear/radial-gradient sheens, hairline/security
textures, ghost letterforms, die-cut corner clips (`ClipPath`), foil wordmarks
(`ShaderMask` 3-stop gold gradient). **Forbidden — bitmap texture, photographic paper
skeuomorphism, and blur.** One light source (top), one shadow, **at most one `saveLayer`
per surface** (one `ShaderMask` *or* one soft clip, not both stacked).

**Depth is hierarchy:** classes differ by how much material they earn, not by different
tricks — ordinary (sheen+hairline) → vip (adds aura+foil+ghost+die-cut, gold the only new
hue) → table (forest ground + ghost ×N + solid gold ticks) → vendor (hatch + utility band,
*less* depth: a credential reads functional, not luxurious).

**Robustness — the `full` / `lite` fidelity ladder (mandatory):** every template declares
two render tiers. `full` = sheen+vignette+aura+foil+ghost+die-cut. `lite` = flat token
fill + 1px border + hairline + type, **no `saveLayer`, no gradients**. Drop to `lite`
automatically under reduce-motion, on a depth-parse failure, or a low-fidelity device
signal — `lite` is also the error/offline render. The template degrades, never breaks.

**Guardrails:** depth never lowers text contrast below **4.5:1** (assert in the renderer);
`RepaintBoundary` per template so a scrolling list never repaints gradients; DPR-aware
hairlines (`1/devicePixelRatio` logical width) or they shimmer; foil is a STATIC gradient,
never an animated shimmer (motion never-list + cost).

## 5 · Evolve within brand

**Law (apply, never propose against):** the colour system + coral rule, the
type trio + scale, the motion never-list, the wordmark/logo, the voice tables,
every non-negotiable in §2.

**Proposable (the expressive layer):** card anatomy, surface texture,
illustration style, expressive layouts, new motion *within* the tiers, poster
template aesthetics. Propose as **named variants** (2–3) with one-line
rationale each; the founder picks; approved proposals are logged in the living
doc and flagged for the Vol 25 amendment path. Never silently extend brand law.

## 6 · Output contract (every session)

**(a) HTML mockups** — one self-contained `.html` file per artifact variant:
no external hosts (inline CSS, system/fallback fonts, placeholder images as
inline SVG/data URIs; render the ZUKA logo as a labelled placeholder — real
assets live in `assets/brand/`); a 360×800 mobile frame; real Kampala venue and
neighbourhood names, real UGX amounts; **all states rendered on one page** —
default · loading · empty · offline · error · success, plus the brief's
artifact-specific states. Saved to `docs/design/mockups/<artifact-slug>/`.

**(b) Living-doc spec section** — written ready to append to
`docs/design/ZUKA_DESIGN_LIVING_DOC.md`, matching its conventions: a Status
Index row (PLANNED · DRAFTED · REVIEWED · BUILD-READY · BUILT), then flow,
components, states, copy, colour logic, UX rules, backend dependencies.

## 7 · Quality bar — self-check before presenting

- [ ] All required states rendered, including offline?
- [ ] No sacred proof regressed (taps counted, no login added, no new
      required field on Go Live)?
- [ ] Terminology clean (Gathering/Person, trust as state label)?
- [ ] Money as `UGX n,nnn` integer; no wallet UI; vendor-direct language?
- [ ] Coral only on LIVE; system colours not substituted for brand colours?
- [ ] Contrast AA at 360×800; body ≥ 16px; primary actions thumb-reachable?
- [ ] Responsive: works at 320 (no h-scroll) and ≥ 600 (column caps, no
      stretched cards; money flows stay one-column)?
- [ ] No invented data — every field shown exists in the brief's DATA AVAILABLE?
- [ ] No dark patterns; copy passes the We Say / We Never Say table?
- [ ] Expressive-layer choices presented as named variants, not silently locked?
