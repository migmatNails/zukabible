# ZUKA — Auth Hardening Pre-Deploy Checklist

> Origin: **F003** (organiser-identity-provisioning), closing the two auth MED
> notes raised by the F002 `zuka-security` gate. `supabase/config.toml` is the
> **local-dev** posture — deliberately permissive so self-serve sign-up (§5D) and
> the F002 dev seed work with no SMTP. This checklist is what MUST change **before
> any hosted deploy**. Do NOT enable these locally (it breaks the no-SMTP dev loop
> and the dev seed).

Self-serve organiser sign-up is a product requirement (F003), so signup stays
**open**. Hardening bounds *abuse of an open signup*, it does not close it.

## Blocking before hosted deploy

- [ ] **Email confirmation on.** `[auth.email] enable_confirmations = true`.
      Consequence for the client: `signUp` returns **no session** until the user
      confirms → the app shows the §5D "check your email" state and provisions the
      Person on the **first confirmed sign-in**. This path is **built** (F003B,
      closing RF003-F1): `AuthRepository.signUp` stashes `display_name` **and the
      DPPA consent booleans** into the auth user's metadata; on a sign-in where the
      account has no linked Person, `OrganiserSession.signIn` reads
      `AuthRepository.readPendingRegistration()` and calls the idempotent
      `register_person`. Consent is passed as a **typed arg** (the server never
      reads metadata); a legacy account with no stashed consent is an honest
      "not set up to host yet", never a default-true provision. Only the config
      differs from local.
- [ ] **Real SMTP.** Configure `[auth.email.smtp]` (e.g. SendGrid via
      `env(SENDGRID_API_KEY)`) — confirmation emails cannot send without it.
- [ ] **CAPTCHA on signup/token routes.** `[auth.captcha] enabled = true`
      (hCaptcha or Turnstile) + wire the client. First line against scripted
      account creation.
- [ ] **Cloudflare edge rate-limit** on `POST /auth/v1/signup` and
      `POST /auth/v1/token`. The built-in `[auth.rate_limit] sign_in_sign_ups`
      throttle is **per-IP only** (the F002 security MED) — an edge rule adds a
      global/again-per-route ceiling a single IP-rotating actor cannot evade.

## Already applied in `config.toml` (safe locally)

- [x] `minimum_password_length = 8` (was 6) — matches the §5D client validation.
- [x] `password_requirements = "letters_digits"` — a modest Africa-first floor
      (not symbols/mixed-case, which frustrate 2GB-Android typing) that still
      rejects trivial passwords.

## Verify after enabling (hosted)

- [ ] A fresh hosted sign-up receives a confirmation email; no session until
      confirmed; first confirmed sign-in provisions exactly one Person
      (idempotent — re-verify with `has_function_privilege` that `register_person`
      is `anon=false, authenticated=true`).
- [ ] CAPTCHA challenge fires on signup; the edge rate-limit returns 429 past the
      threshold on `/auth/v1/signup` and `/auth/v1/token`.
- [ ] The three MVP proofs still hold (zero-login discovery unaffected; Go Live
      and ticketing paths unchanged).
