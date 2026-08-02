---
name: Run an ID Check document verification
description: >-
  Create a Verisoul ID Check session, send the user through the hosted document
  + face verification flow, and read the verification result.
api: openapi/verisoul-idcheck-openapi.json
operations: [createIDCheckSession, enrollIDCheck, verifyID, redirectToIDCheck]
generated: '2026-07-21'
method: generated
---

# Run an ID Check document verification

ID Check verifies a government ID document and matches the holder's face. The
capture UI is fully hosted by Verisoul (BIPA/biometric consent handled by
Verisoul).

## Auth & environment
- `x-api-key` header. Base URL `https://api.prod.verisoul.ai`
  (`https://api.sandbox.verisoul.ai` for testing).

## Steps
1. **Create a session** — `GET /liveness/session` (`createIDCheckSession`).
   Returns a `session_id` (and `request_id`). In sandbox, add
   `?simulate=<case>` (e.g. `success`, `liveness_check_failed`) to bypass real
   capture — see sandbox/verisoul-sandbox.yml.
2. **Navigate the user** to the hosted flow at
   `https://app.verisoul.ai?session_id={session_id}` (or via
   `GET /public/liveness-redirect`, `redirectToIDCheck`). The user captures
   their ID and selfie; Verisoul redirects back with success/error.
3. (Optional) **Enroll** the session to an account — `POST /liveness/enroll`
   (`enrollIDCheck`) with `session_id` + `account_id`.
4. **Read the result** — `POST /liveness/verify-id` (`verifyID`) to get the
   decision, matched fields, and risk flags.

## Notes
- Errors are `{message, error, statusCode}`; non-success simulate cases return
  `400`. See errors/verisoul-problem-types.yml for the case list.
- Supported document types: see docs (verifications/id-check supported-documents).
