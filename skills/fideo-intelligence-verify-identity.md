---
name: Verify an identity and read the risk score
description: >-
  Run Fideo Verify on a set of identifiers to get a real-time 0-1 fraud risk
  score, a session identifier, and an array of identity/risk checks — for
  onboarding, account origination, and account-takeover prevention.
api: Fideo Verify & Signals API
base_url: https://api.fideo.ai
operations:
- POST /verify
generated: '2026-07-19'
method: generated
source: https://docs.fideo.ai/docs/verify
---

# Verify an identity with Fideo

Use this skill to score an identity in real time with Fideo Verify.

## Authenticate

Every request needs a bearer API key:

```
Authorization: Bearer {API_KEY}
Content-Type: application/json
Accept: application/json
```

Keys are created in the Fideo platform or issued by Fideo support. Use a
separate key per environment and never embed a key in client-side code.

## Call POST /verify

Send any combination of identifiers in the shared multi-field request body. You
must supply at least one of: `email`, `phone`, `ssn`, a social handle/ID,
`ip_address`, or a combined name + postal address. Optional fields include
`first_name`/`middle_name`/`last_name`, `date_of_birth` (YYYY-MM-DD),
`address_line1`/`address_line2`, `city`, `state_code`, `country_code`
(ISO 3166-1 alpha-2), `organization`, `title`, and a caller-supplied `record_id`.

```
curl -X POST https://api.fideo.ai/verify \
  -H 'Authorization: Bearer {API_KEY}' \
  -H 'Content-Type: application/json' \
  -d '{ "email": "...", "phone": "...", "first_name": "...", "last_name": "..." }'
```

Pin the response contract by appending `?v=20260609` (the current dated version)
if you need a stable schema.

## Read the response

- `risk` — a score from 0.0 (least risky) to 1.0 (most risky). Steer your flow
  with it to optimize friction and cut top-of-funnel cost.
- `sessionId` — a Fideo-generated identifier. Re-supply it to stitch a user's
  workflow into a single billable session instead of opening a new one.
- `checks[]` — check objects sorted by risk (HIGH, MED, LOW, NONE), each with
  `id`, `state`, `name`, `description`, `risk`, and `checkPackage`.

## Handle status codes

- `200` existing session, `201` new session — both are success.
- `401` no account / invalid key, `403` product not enabled for this key.
- `410` the user requested data deletion (only hashed tokens retained).
- `429` rate limit or trial quota exceeded — back off or upgrade.
- `500`/`503` Fideo system or temporary error — retry `503` with backoff.

There is no idempotency-key header; use `sessionId` to group a workflow and
`record_id` to correlate to your own records.
