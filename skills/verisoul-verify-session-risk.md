---
name: Verify a session and get an account risk decision
description: >-
  Use the Verisoul session APIs to bind a device session to a user account and
  retrieve Verisoul's fraud/risk decision and scores for that account.
api: openapi/verisoul-session-openapi.json
operations: [authenticateSession, getSession, getAccount, getLinkedAccounts]
generated: '2026-07-21'
method: generated
---

# Verify a session and get an account risk decision

Verisoul scores real-vs-fake users. The frontend SDK (Web/iOS/Android/RN/Flutter)
collects device signals under a `session_id`; your backend then turns that
session into a risk decision.

## Auth & environment
- Send the secret key in the `x-api-key` header on every call. Backend only —
  never expose it client-side.
- Base URL: `https://api.prod.verisoul.ai` (or `https://api.sandbox.verisoul.ai`
  for testing). Keys are environment-scoped.

## Steps
1. On the client, initialize the Verisoul SDK with your public Project ID and
   obtain a `session_id`. Send it to your backend.
2. **Authenticate the session** — `POST /session/authenticate`
   (`authenticateSession`) with the `session_id` and your `account_id`. This
   binds the session to the account and returns the account decision and scores.
   Use `POST /session/unauthenticated` for pre-login/anonymous checks.
3. (Optional) **Re-read the session** — `GET /session/{session_id}`
   (`getSession`) to inspect device/network/bot signals.
4. **Read the account** — `GET /account/{account_id}` (`getAccount`) for the
   current decision, or `GET /account/{account_id}/accounts-linked`
   (`getLinkedAccounts`) to surface multi-accounting.

## Conventions
- Every response carries a `request_id` (UUID) — log it for support.
- Errors are flat JSON: `{message, error, statusCode}` (see
  errors/verisoul-problem-types.yml). Handle `400` (bad id/body) and `429`
  (rate limit: 300 req/min per key — back off).
- No idempotency-key header; account/session writes are keyed by id, so retries
  converge.
