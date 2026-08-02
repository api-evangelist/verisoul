---
name: Manage account lists for the rules engine
description: >-
  Create and maintain Verisoul account lists (e.g. allow/deny lists) and add or
  remove accounts, to drive the automated rules engine.
api: openapi/verisoul-list-openapi.json
operations: [listLists, getList, createList, deleteList, addAccountToList, removeAccountFromList]
generated: '2026-07-21'
method: generated
---

# Manage account lists for the rules engine

Lists are named groups of accounts that Verisoul's rules engine can act on
(e.g. force-allow trusted users, block known-bad accounts).

## Auth & environment
- `x-api-key` header. Base URL `https://api.prod.verisoul.ai`.

## Steps
1. **List all lists** — `GET /list` (`listLists`).
2. **Create a list** — `POST /list/{list_name}` (`createList`).
3. **Add an account** — `POST /list/{list_name}/account/{account_id}`
   (`addAccountToList`).
4. **Remove an account** — `DELETE /list/{list_name}/account/{account_id}`
   (`removeAccountFromList`).
5. **Inspect** — `GET /list/{list_name}` (`getList`) for the list and its
   accounts; **delete** — `DELETE /list/{list_name}` (`deleteList`).

## Notes
- Writes are keyed by `list_name` / `account_id`, so retries are safe (no
  idempotency-key header).
- Errors are `{message, error, statusCode}`; `429` on rate-limit (300/min/key).
