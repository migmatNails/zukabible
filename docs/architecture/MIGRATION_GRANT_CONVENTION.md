# Migration Grant Convention — the definitive function-EXECUTE lock

> Ratified S050 (2026-07-17). Governs every migration that creates or exposes a
> `public` function. Subordinate to SOUL.md and ZukaBibleV4; this is engineering
> reference, not law. See memory `project_supabase_auto_grant` for the full trap
> history (corrections 1–3).

## The trap

Supabase's **baseline default privileges** `GRANT EXECUTE` on every newly created
`public` function to **`anon`, `authenticated`, AND `service_role`** — as
**explicit per-role grants**, applied at `CREATE FUNCTION` time, *before* any
migration runs. These are **not** the vanilla Postgres `PUBLIC =X` grant.

Because of that, **neither single-form REVOKE is sufficient**:

| Form written | What it strips | What LEAKS |
|---|---|---|
| `REVOKE … FROM PUBLIC` | only the `PUBLIC` path | explicit `anon`, `authenticated`, `service_role` all survive |
| `REVOKE … FROM anon, authenticated` | those two roles | the `PUBLIC` path **and** `service_role` survive |

Both forms shipped to `main` and both leaked live — the running dev DB masked it
(s014-class grant drift), so it surfaced only on a **clean-apply** sweep (fresh
container → all migrations via `psql` in order → all suites). The local DB is
**never** the grant-truth oracle; CI / a clean `supabase db reset` is.

Superseded single/partial-form comments still in the tree (do **not** copy them):
`20260716120000:91-96` (`_tier_is_open`), `20260715100000:16`
(`seed_trust_profile`), `20260714100002:299` (the notify_sms drain). All three
were closed by `20260717113000_revoke_default_privilege_role_grants.sql`.

## The definitive lock (use this, always)

Name **all four grantees** in the REVOKE — path-independent, one statement strips
the `PUBLIC` grant *and* every explicit per-role grant regardless of the
create-time posture — then `GRANT` back **only** the client roles the function's
own test asserts:

```sql
-- <fn> is <owner-only | authenticated-only | …>. <one line: why>.
REVOKE EXECUTE ON FUNCTION public.<fn>(<argtypes>)
    FROM PUBLIC, anon, authenticated, service_role;
-- GRANT back ONLY what the test asserts (omit entirely for owner-only fns):
GRANT  EXECUTE ON FUNCTION public.<fn>(<argtypes>) TO authenticated;   -- e.g.
```

Rules:

- **SECURITY DEFINER / owner-invoked functions keep EXECUTE by ownership** — the
  owner (`postgres`) is unaffected by these REVOKEs, so internal engine, cron, and
  `register_person`-style callers keep working. Grant a client role back **only**
  when a client legitimately calls the function directly.
- **Owner-only** (forges rows, emits ledger events, drains a queue): REVOKE the
  four roles and grant nothing back — e.g. `seed_trust_profile`,
  `drain_notification_dispatch`.
- **Intentional anon surface** (zero-login discovery, offline scanner) is a
  deliberate GRANT, not a leak — `discovery_feed`, `discovery_feed_nearby`,
  `gathering_detail`, `check_in_ticket`, `download_scanner_manifest`,
  `sync_offline_scans`, `claim_free_ticket`, `register_person`, etc. Never lock
  these down to "match the pattern".
- **Verify the EFFECTIVE ACL, never the REVOKE text.** Assert with
  `has_function_privilege('<role>', 'public.<fn>(<argtypes>)', 'EXECUTE')` in a
  committed suite (the teeth) — a one-time "all locked" sweep rots (RNDS001 found
  `seed_trust_profile` still anon-reachable long after a sweep declared it locked).
  A DO-block sanity check inside the migration is defence-in-depth, not a substitute.

## Known latent backlog (same pattern, not yet locked)

The S050 `pg_proc` sweep found three **trigger** functions still carrying
baseline `anon/authenticated/service_role` EXECUTE grants:
`domain_events_append_only()`, `commission_debt_ledger_append_only()`,
`set_updated_at()`. Direct invocation of a trigger function errors
(`can only be called as triggers`), so there is no data-path leak — but they
violate the explicit-ACL posture. They are **not** locked here because no suite
forbids them yet; a future session should add the teeth **then** apply the
four-role REVOKE (test-first, no intent-guessing). Recorded as a backlog candidate.
