# ZUKA — API REGISTRY

> Source: Volume XIX (ConnectorContract architecture); Volume XI (domain boundaries);
>          Zuka-Bible 2 API Registry (stub — now substantive)
> Status: MVP-scope only. Post-MVP API surface grows with each DRL graduation.

---

## API Principles

All ZUKA APIs honour the same constitutional constraints as every other layer:
- Person-centric (not user-centric) — all endpoints use person_id, not user_id
- Gathering-centric (not event-centric) — all endpoints use gathering_id
- Temporal envelopes on all entity responses (Volume VII, Ch.3 fields)
- ProvenanceRecord included on all intelligence outputs (ADR-007)
- No endpoint surfaces a capability the underlying DRL hasn't earned (ADR-008)

---

## MVP API Domains

### Identity
```
POST /persons                    — create Person (phone OTP initiated)
POST /persons/verify-otp         — complete phone verification
GET  /persons/:id                — get Person (visibility-filtered)
PATCH /persons/:id               — update Person
GET  /persons/:id/personas       — list Personas
POST /persons/:id/personas       — create new Persona
```

### Gathering
```
POST /gatherings                 — create Gathering (host Persona required)
PATCH /gatherings/:id/go-live   — trigger Go Live (≤60s to discovery)
GET  /gatherings                 — discovery feed (no auth required — Proof 2)
GET  /gatherings/:id             — gathering detail
POST /gatherings/:id/rsvp       — RSVP (auth required)
```

### Ticketing
```
POST /gatherings/:id/tickets/purchase  — initiate purchase (Flutterwave intent)
POST /tickets/verify-payment           — confirm payment (webhook + polling)
GET  /tickets/:id/smart-bucket-token   — generate 30-second Smart Bucket JWT
POST /tickets/:id/check-in             — Scanner Pro check-in
POST /tickets/:id/gift                 — gift a ticket to a phone number
```

### Memory
```
POST /memories                   — create Memory (auth required)
PATCH /memories/:id              — enrich with emotion_tags, life_chapter
GET  /memories/:id               — retrieve (visibility-filtered)
POST /media/upload               — upload Media entity (separate from Memory)
```

### Community
```
POST /communities                — create Community
POST /communities/:id/join       — join as member
GET  /communities/:id            — Community profile
POST /communities/:id/gatherings — create community Gathering
```

### Connectors (internal — not user-facing)
```
SafeBoda:        POST /connectors/safeboda/ride-quote
Flutterwave:     POST /connectors/flutterwave/payment-intent
                 POST /connectors/flutterwave/verify-payment
                 POST /connectors/flutterwave/payout
Africa's Talking: POST /connectors/at/send-otp
                  POST /connectors/at/send-notification
```

---

## API Versioning

APIs are versioned at the URL path level: `/v1/gatherings`, `/v2/gatherings`.
Connector APIs are versioned independently of platform API versions (Volume XIX, Ch.3).
No endpoint is deprecated without a minimum notice period per the relevant
ConnectorContract.deprecation_policy.notice_period_days.
