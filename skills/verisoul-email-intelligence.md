---
name: Submit an email for intelligence and process the webhook
description: >-
  Submit an email address to Verisoul Email Intelligence for asynchronous risk,
  deliverability, breach, and identity analysis, then verify and process the
  signed webhook result.
api: openapi/verisoul-email-openapi.json
operations: [submitEmail, submitEmailBatch]
generated: '2026-07-21'
method: generated
---

# Submit an email for intelligence and process the webhook

Email Intelligence is asynchronous: you submit, and the result arrives on your
webhook endpoint as an `email.intelligence.completed` event.

## Auth & environment
- `x-api-key` header, backend only. Base URL `https://api.prod.verisoul.ai`.
- Configure your webhook endpoint and shared secret in the dashboard first.

## Steps
1. **Submit** — `POST /email` (`submitEmail`) with `{ "email": "...",
   "identity_intelligence": true|false, "claims": { ... } }`. Set
   `identity_intelligence: true` to include online-presence and claim matching.
   For many at once, use the batch endpoint (`submitEmailBatch`).
2. The response returns a `request_id` and `webhook_configured`. Correlate the
   later webhook to this `request_id`.
3. **Receive the webhook** — Verisoul POSTs the result to your endpoint.
   **Verify the `x-signature` HMAC-SHA256 header before trusting the body**
   (see conventions/verisoul-conventions.yml and the AsyncAPI at
   asyncapi/verisoul-email-asyncapi.yml). Reject if the timestamp is older than
   5 minutes or the signature does not match (constant-time compare over the
   raw body).
4. **Act on `data.result.decision`** — one of `high_trust`, `trusted`,
   `moderate`, `risky`, `high_risk` (driven by `risk_score` -1..1). Use
   `deliverability.send_recommendation` (`send` / `send_with_caution` /
   `do_not_send`) for email sends.

## Notes
- On failure the webhook has `status:"error"` and an `error` object
  (`{email, code, message}`); `data` is null.
- Always verify against the exact raw request bytes — never a re-serialized body.
