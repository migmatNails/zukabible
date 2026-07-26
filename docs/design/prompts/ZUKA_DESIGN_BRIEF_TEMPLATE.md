# ZUKA Design Brief — Template

> Copy this file, fill every slot, and pair it with
> `ZUKA_DESIGN_CONSTITUTION.md` to run ONE design session for ONE artifact.
> Pick the artifact from `ZUKA_ARTIFACT_CATALOGUE.md` (its row pre-fills
> class, prior art, proof tie, and flags).
>
> Two binding rules:
> 1. **No invented data.** DATA AVAILABLE lists only fields the backend
>    provides today; mockups may not show anything else.
> 2. **Prior art is binding.** If a living-doc § exists, this session evolves
>    it and marks superseded parts — it never writes a competing spec.
>    In a fresh chat with no repo access, paste the prior-art § below.

```
ARTIFACT NAME     ______________________________________________
ARTIFACT CLASS    page | card | clip-video | clip-social | clip-listrow |
                  receipt | form | template-message | template-poster |
                  template-scaffold | template-document
PURPOSE           one sentence: what a Person accomplishes with it
ENTRY POINTS      where it appears / what navigates here
DATA AVAILABLE    exact fields the backend provides today (name : type : example)
STATES REQUIRED   default · loading · empty · offline · error · success
                  + artifact-specific: ______________________________
SACRED-PROOF TIE  none | 60s-go-live | zero-login-discovery | 3-tap-ticket
                  budget statement: e.g. "this screen is tap 2 of 3"
MONEY/TRUST FLAG  no | yes → UGX integer minor units · vendor-direct language ·
                  trust only as state labels · surface never animates (motion:
                  instant)
SHARE SURFACE     none | WhatsApp | IG story | print/PDF — target aspect + format
PRIOR ART         living-doc § ___ (paste the section text if no repo access)
DELIVERABLES      ___ HTML mockup(s) — which variants — + living-doc spec section
OPEN QUESTIONS    what must be resolved (or asked) before rendering
```

## Session flow

1. Read the Constitution, then this filled brief. Ask the OPEN QUESTIONS
   before designing if they block layout or copy.
2. Where the expressive layer is involved, produce 2–3 named variants with
   one-line rationale each.
3. Render the HTML mockup(s) per the Constitution's output contract →
   `docs/design/mockups/<artifact-slug>/`.
4. Write the living-doc spec section (status line first).
5. Run the Constitution §7 quality bar. Present for founder review.
