---
name: Run a Face Match liveness verification
description: >-
  Create a Verisoul Face Match session for liveness/selfie verification and
  account deduplication, and read the result.
api: openapi/verisoul-facematch-openapi.json
operations: [createFaceMatchSession, enrollFaceMatch, verifyFace, verifyIdentityFaceMatch, redirectToFaceMatch]
generated: '2026-07-21'
method: generated
---

# Run a Face Match liveness verification

Face Match confirms a live human and deduplicates users by biometric match
(prevents one person opening many accounts). The capture UI is hosted by
Verisoul.

## Auth & environment
- `x-api-key` header. Base URL `https://api.prod.verisoul.ai`
  (`https://api.sandbox.verisoul.ai` for testing).

## Steps
1. **Create a session** — `GET /liveness/session` (`createFaceMatchSession`).
   Returns `session_id`. Sandbox supports `?simulate=<case>`.
2. **Navigate the user** to `https://app.verisoul.ai?session_id={session_id}`
   (or `GET /public/liveness-redirect`, `redirectToFaceMatch`) for the selfie/
   liveness capture; Verisoul redirects back with the outcome.
3. (Optional) **Enroll** to an account — `POST /liveness/enroll`
   (`enrollFaceMatch`).
4. **Verify** — `POST /liveness/verify-face` (`verifyFace`) for the liveness /
   dedup result, or `POST /liveness/verify-identity`
   (`verifyIdentityFaceMatch`) to verify against a known identity.

## Notes
- Errors are `{message, error, statusCode}`; simulate failure cases return
  `400`. Risk flags are enumerated in the docs.
- Rate limit 300 req/min per key (`429` on exceed).
