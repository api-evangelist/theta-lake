---
name: Search and export Theta Lake records
description: Authenticate, run a unified search across archived communications, review policy hits, and export the matching records.
api: openapi/theta-lake-openapi-original.yml
operations:
- 'POST /token'
- 'POST /search/records'
- 'GET /search/records/ids'
- 'GET /records/{id}/policy_hits'
- 'POST /exports/search/{search_id}'
- 'GET /exports'
---

# Search and export Theta Lake records

Use this skill to find archived communications and export them for review or eDiscovery.

## Auth
1. `POST /token` (client-credentials) -> JWT; send `Authorization: Bearer <jwt>`.

## Steps
1. `POST /search/records` with your query (participants, date range, channel, policy). Requires `search:read`. Returns matching records; paginate with `page_token`/`page`/`limit`.
2. Optionally `GET /search/records/ids` to retrieve just the matching record IDs for bulk handling. Requires `search:read`.
3. For a record of interest, `GET /records/{id}/policy_hits` to review the compliance policy hits. Requires `records:read`.
4. `POST /exports/search/{search_id}` to export the results of a (saved) search. Requires `search:export`.
5. `GET /exports` to track export status and retrieve the output. Requires `exports:read`.

## Rules
- Pagination is cursor-based via `page_token`; iterate until the token is empty.
- Rate-limit tiers apply per operation (search/export are heavier); on `429` back off.
- Every response includes a `request_id`.
