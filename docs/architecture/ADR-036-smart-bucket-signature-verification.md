# ADR-036 — Smart Bucket device-signature verification: asymmetric Ed25519, in-Postgres (pgsodium) preferred over an Edge-Function shim

```
Status:   ACCEPTED  2026-07-23 (Option C — pgsodium-native Ed25519). BOTH gates
                     cleared: founder acceptance + zuka-security RE-REVIEW = PASS
                     (the agent revised its own Task-5 Option-A ruling; it
                     live-verified pgsodium `crypto_sign_verify_detached` against
                     the RFC 8032 Ed25519 vector — good→t, tampered→f — and that
                     the key-mgmt surface is already permission-fenced). Conditions
                     1–3 unchanged, 4 re-cast, 5 strengthened, +6/+7 below.
                     ONE blocking BUILD gate remains (not an acceptance gate):
                     the Phase-B session's first check must confirm
                     `CREATE EXTENSION pgsodium` + the verify primitive on
                     ZUKA's HOSTED Supabase; on failure, fall back to Option A.
Date:     2026-07-23 (drafted)
Deciders: Kiyingi James (founder) — pending
Context:  Smart Bucket SB-1 Phase B (design spec
          docs/superpowers/specs/2026-07-23-smart-bucket-credential-design.md §7.1) ·
          Vol 14 Ch 5–7 (Smart Bucket entry mechanic) · ADR-015 (append-only
          Event Store) · ADR-016 (Supabase-first modular monolith) ·
          ADR-023 (frozen offline manifest hashing)
Stack rule: adopting a Postgres EXTENSION (pgsodium) or an Edge-Function compute
          surface is a stack/dependency change requiring a formal ADR
          (SOUL red line 7 / CLAUDE §3)
Security co-gate: zuka-security LEADS smart_bucket_qr (guard). The 2026-07-23
          Task-5 ruling chose Option A over B without Option C in view; this ADR
          MUST route back through zuka-security before acceptance.
```

## Context

The Smart Bucket presents a device-bound, rotating QR — `ZUKA:BKT:v1:[bucket_ref]:[nonce]:[signature]` — that the online matching engine must verify before admitting (design §3.1–§3.3). The signature binds the credential to a **non-exportable device keypair** (Android Keystore / iOS Secure Enclave) so a screenshot replayed on another device fails. The server verifies with the device's registered **public** key; the private key never leaves the enclave.

The blocker: **`pgcrypto` (the only crypto extension installed) exposes `digest`/`hmac` but NO asymmetric verification** — no Ed25519, no ECDSA. So a device-signed QR cannot be verified in plain plpgsql *as currently provisioned*. The 2026-07-23 `zuka-security` Task-5 ruling resolved this as a binary — an Edge-Function shim (asymmetric) vs SQL-native HMAC (symmetric) — and chose the Edge Function. During ADR drafting, a **third option surfaced**: `pgsodium` (libsodium) is *available* on the stack (`pg_available_extensions`: pgsodium 3.1.8, not yet installed), and libsodium's `crypto_sign_verify_detached` **is Ed25519**. That makes native in-Postgres asymmetric verification possible — removing the Edge Function's two costs while keeping its security property. This ADR records the full three-option decision.

## Decision drivers

- **Crown-Jewel blast radius is the deciding axis.** Every check-in seeds an `AttendanceSeedRecord` into the **append-only, non-correctable** Participation Ledger (ADR-015), which feeds the Trust Graph — "hardest to rebuild." Under assume-breach the database is the layer that falls, so a forged signature must remain impossible **even with full DB read**. → The server must store **public keys only** (asymmetric). This rejects symmetric HMAC outright.
- **Locked stack / ADR-016.** Prefer the modular monolith: no new compute surface, no Node/Deno service, unless a genuine capability gap forces it. A Postgres extension is more in-stack than an Edge Function.
- **Testability is a security property here.** The verification path guards a Crown-Jewel write; it must be provable by the repo's suites. Deno Edge Functions are **unrunnable in this sandbox and untested in CI** ([[project_deno_absent_headless_sandbox]], [[project_ci_never_green_s014_drift]]) — the one component local suites cannot prove.
- **Africa-first, offline-first.** Verification is **online-only** by design (§3.2); the offline door degrades to `bucket_id` membership in the signed manifest hash-set regardless of which option is chosen. No option changes the offline lane.
- **Latency ≤ 400 ms + oracle discipline** (design §3.3): a single Ed25519 verify is microseconds; a bad signature must be latency-identical to not-found (one bit, no probing oracle).

## The three options

| | **A — Edge Function (WebCrypto Ed25519)** | **B — SQL-native HMAC (symmetric)** | **C — pgsodium-native Ed25519 in Postgres** |
|---|---|---|---|
| Asymmetric (public keys only) | ✅ | ❌ (server stores device secrets) | ✅ |
| DB-leak forges a QR? | ❌ no | ⚠️ **yes — mass forgery** | ❌ no |
| Runs in-stack (ADR-016) | ⚠️ separate compute surface | ✅ | ✅ (Postgres extension) |
| Testable in repo suites / CI | ❌ Deno gap | ✅ | ✅ |
| New dependency | Edge-Function runtime | none (pgcrypto) | **pgsodium extension** |
| Verify primitive | WebCrypto `Ed25519` | `hmac()` | `crypto_sign_verify_detached()` |

## Decision (RECOMMENDED — pending zuka-security re-review + founder acceptance)

**Adopt Option C: verify the device Ed25519 signature natively in Postgres via `pgsodium`, inside a `SECURITY DEFINER` `check_in_bucket` resolver.** Reject B (symmetric — DB-leak forgery of a Crown-Jewel ledger is disqualifying). Prefer C over A because C keeps A's decisive security property (asymmetric, public-keys-only) while eliminating A's two costs — the untested-in-CI Deno gap and the extra compute surface — and keeps the entire matching engine in one place, fully suite-testable.

Concretely, Phase B would:
1. `CREATE EXTENSION pgsodium` (new locked-stack dependency — this ADR authorizes it).
2. Verify the presented signature with `crypto_sign_verify_detached(signature, canonical_message, device_public_key)` where `canonical_message = bucket_id ‖ nonce ‖ monotonic_counter`, inside `check_in_bucket` — **no Edge Function**.
3. Store only device **public** keys in the registration table (a teeth test asserts no secret column).
4. Keep the offline lane unchanged (`bucket_id` membership in the signed manifest hash-set; salted-hash per the §7.1 Low finding).

**Use `pgsodium` for the stateless verify primitive ONLY** — `crypto_sign_verify_detached` with a caller-supplied public key. Do **not** use pgsodium's server-side key-vault / key-management features (those are the parts Supabase has signalled for deprecation in favour of Vault + TLS; see risk below). The verify function is core libsodium and low-migration-risk.

**Fallback:** if the zuka-security re-review or a build-time check finds `pgsodium` unavailable on ZUKA's hosted Supabase, or its `crypto_sign_verify_detached` unusable/deprecated, **fall back to Option A** (Edge-Function shim) exactly as the Task-5 ruling specified — a thin WebCrypto verifier that delegates every state change to a `service_role`-only `check_in_bucket_verified(...)`, with the ~20 verification lines carried as an explicit CI test gap.

## Mandatory conditions (bind into the Phase-B review — as AMENDED by the zuka-security re-review, 2026-07-23)

1. **Public keys only** server-side — teeth test asserts no secret/symmetric column on the device-key table. *(unchanged)*
2. **Monotonic-counter replay window:** sign over `(bucket_id, nonce, monotonic_counter)`; reject any counter ≤ last-seen for `(person, device)`; window bounded tightly. Nonce-freshness + counter kill cross-device replay and pre-play. *(unchanged)*
3. **Device-registration RPC hygiene:** the 4-role REVOKE (`FROM PUBLIC, anon, authenticated, service_role`, proven `anon=f` — [[project_supabase_auto_grant]]), `auth.uid()` identity (never `current_user` — [[project_security_definer_current_user_trap]]), LRU public-key cap per Person (the `person_device_token` idiom). Effective ACL verified live in its suite. *(unchanged)*
4. **The sole state-changing resolver is `check_in_bucket` (RE-CAST for the in-Postgres model — there is no Edge Function).** `check_in_bucket` is `SECURITY DEFINER`, `guard`-gated, and bound to a **valid scanner session** (signed JWT/PIN scoped to event+gate+window — the contextually-blind scanner, the `check_in_ticket` idiom). Nothing reaches the ledger write without **both** a verified scanner session **and** a passing device-signature verify; session validity is enforced *inside* the function, not by role. Its client EXECUTE grant follows the 4-role REVOKE. The `pgsodium` **verify primitive itself may remain anon-callable** — it is a pure, stateless, secret-free function of its arguments, so calling it teaches nothing; the security boundary is the resolver, not the primitive.
5. **Latency-identical one-bit denies (STRENGTHENED).** `bad-signature ≡ wrong-kind ≡ not-found` in SQLSTATE **and** latency band. Because verify and check-in now live in one function, an early bad-sig exit vs a late not-found exit is a timing oracle — the deny paths must be **deliberately converged** (single code path / latency-banded / dummy work so all denies exit at the same point). The oracle pgTAP MUST probe **bad-signature** alongside wrong-kind and not-found and assert all three indistinguishable.
6. **NEW — extension pin + primitive teeth test.** `CREATE EXTENSION pgsodium` is pinned in a migration, and a committed suite asserts the verify primitive works: an **RFC 8032 Ed25519 known-good vector passes and a tampered one fails**. This is the clean-apply proof AND the early-warning that catches a Supabase upgrade removing/breaking the primitive at **CI, not at the door**.
7. **NEW — schema-qualify the call (footgun).** `check_in_bucket` must call `pgsodium.crypto_sign_verify_detached(...)` **schema-qualified**. A `SECURITY DEFINER` function with the correct hijack-safe `SET search_path = public` will **not** resolve the function unqualified — set `search_path` to only the schemas needed and qualify the call.

> **Blocking pre-build check (the one thing the re-review could not clear):** hosted-Supabase availability of `pgsodium` + `crypto_sign_verify_detached` was proven on the LOCAL stack only. The Phase-B session's **first action** is a clean-apply confirming `CREATE EXTENSION pgsodium` on ZUKA's hosted Supabase; on failure, fall back to Option A. This guard is correctly placed — keep it.

## Alternatives considered

- **Option B — SQL-native HMAC (symmetric).** Fully testable in plpgsql (`hmac()`), zero new dependency. **Rejected:** the server must store the device *secret* keys; one DB read lets an attacker forge a valid QR for any bucket at scale, converting a confidentiality breach into a mass **integrity** breach of the append-only Participation/Trust ledgers — the precise failure ZUKA's assume-breach posture exists to prevent. Its testability win is also moot: device-signature verification is online-only (§3.2), so B would only ever run on the online lane anyway.
- **Option A — Edge-Function Ed25519 shim.** The Task-5 ruling's choice; a thin WebCrypto verifier that delegates to a `service_role`-only SQL resolver, storing public keys only. Sound and asymmetric, but carries two avoidable costs C removes: (a) the verifier is **untested in this sandbox and CI** (the one component suites can't prove), and (b) it adds a compute surface beside Postgres (in-bounds under ADR-016 as a Supabase primitive, but still a seam). **Retained as the fallback** if pgsodium proves unavailable/unsuitable on hosted Supabase.
- **Ambient / proximity auth instead of a signature** — rejected by the design (§8c): a value-bearing action that writes the Participation Ledger requires a scanned signed credential; location is never sufficient (GPS spoofs).

## Consequences

- **If C is accepted:** `pgsodium` enters the locked stack (verify-primitive use only). The entire Smart Bucket matching engine stays in Postgres, fully suite-testable — no Deno CI gap, no Edge-Function seam. Phase B builds `check_in_bucket` with an inline Ed25519 verify. A clean-apply must confirm `CREATE EXTENSION pgsodium` succeeds on hosted Supabase (the build session's first check).
- **pgsodium deprecation risk (named honestly):** Supabase has signalled pgsodium's *key-management/vault* features for eventual deprecation in favour of Supabase Vault + TLS. This ADR uses **only** the stateless `crypto_sign_verify_detached` primitive (core libsodium), which is far lower-risk — but if a future Supabase release removes the extension entirely, the fallback is Option A (or vendoring the verify), tracked as a migration-ladder watch item.
- **If A is accepted (fallback):** an Edge-Function runtime enters the stack; the ~20-line WebCrypto verifier is an explicit, accepted CI test gap; `check_in_bucket_verified` is `service_role`-only.
- **Under either:** the offline door is unchanged; the 5 conditions above and the four §7.1 Low forward-constraints (salted-hash `bucket_id` in the manifest, constant nonce cadence, the idempotent-RPC dead-tuple note, the inherited full-column grant) bind the Phase-B review.
- **Governance:** this ADR revises a `zuka-security` ruling with new information, so it is **not** self-accepting — the security re-review of Option C is a blocking co-gate alongside founder acceptance.

## Status note

**PROPOSED.** Drafted 2026-07-23 to satisfy the Task-5 requirement for a crypto-boundary ADR, and to surface `pgsodium`-native Ed25519 (Option C), which was not in view when the ruling chose Option A. Two blocking gates before acceptance and any Phase-B build session: **(1)** `zuka-security` re-review of Option C (does native pgsodium verification satisfy the threat model as well as the Edge-Function shim, and is pgsodium available/stable on ZUKA's hosted Supabase?), and **(2)** founder acceptance. Until both clear, SB-1 Phase B remains gated.
