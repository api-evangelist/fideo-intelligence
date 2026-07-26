---
name: Enrich an identity with Fideo Signals
description: >-
  Retrieve modular identity intelligence signals (person, email, location,
  professional, IP, phone, social, economic) for a set of identifiers to enrich
  fraud models, risk scoring, investigations, and link analysis.
api: Fideo Verify & Signals API
base_url: https://api.fideo.ai
operations:
- POST /signals
generated: '2026-07-19'
method: generated
source: https://docs.fideo.ai/docs/signals
---

# Enrich an identity with Fideo Signals

Use this skill to pull identity intelligence signals to feed your own risk
models rather than a single decision score.

## Authenticate

```
Authorization: Bearer {API_KEY}
Content-Type: application/json
```

## Call POST /signals

Signals shares the same multi-field request body as Verify — pass any
combination of identifiers (`email`, `phone`, `ssn`, name + address,
`ip_address`, profile handles). Select the response version with the `v` query
parameter, e.g. `POST /signals?v=20240424`.

```
curl -X POST 'https://api.fideo.ai/signals?v=20260609' \
  -H 'Authorization: Bearer {API_KEY}' \
  -H 'Content-Type: application/json' \
  -d '{ "email": "...", "phone": "...", "ip_address": "..." }'
```

## Work with the signal categories

The response is organized into signal families you can map into your model:

- Person, Email, Location, Professional, IP Address, Phone, Social, Economic.

For most identifiers you receive up to 5 additional linked identifiers; for
hashed emails up to 20. Optional country filtering uses ISO 3166-1 alpha-2
codes. Beta Signal Patterns (Time Series, Historical Summary, Patterns Within
Signals) add temporal context.

## Handle status codes

Same envelope as Verify: `401` invalid key, `403` product not enabled, `429`
rate/quota exceeded, `500`/`503` server-side — retry `503` with backoff. Errors
are signaled by HTTP status code; there is no problem+json body.
