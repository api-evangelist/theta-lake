---
name: Place a legal hold and open a legal matter in Theta Lake
description: Authenticate, open a legal matter, assign custodians, and create a legal hold to preserve in-scope communications for eDiscovery.
api: openapi/theta-lake-openapi-original.yml
operations:
- 'POST /token'
- 'POST /legal_matters'
- 'POST /legal_matters/{id}/custodians'
- 'POST /legal_hold'
- 'GET /legal_hold/{id}/logs'
---

# Place a legal hold and open a legal matter

Use this skill to preserve communications for litigation/eDiscovery.

## Auth
1. `POST /token` (client-credentials) -> JWT; send `Authorization: Bearer <jwt>`.

## Steps
1. `POST /legal_matters` to open the matter (name, description). Requires `legal_matter:create`.
2. `POST /legal_matters/{id}/custodians` to add the custodians (identities) in scope. Requires `legal_matter:update`.
3. `POST /legal_hold` to create the hold that preserves in-scope content and rules. Requires `legal_hold:create`.
4. `GET /legal_hold/{id}/logs` to audit hold activity over time. Requires `legal_hold:read`.

## Rules
- Legal hold and legal matter are distinct resources; the matter defines scope/custodians, the hold enforces preservation.
- No idempotency key — check for an existing matter/hold before re-creating on a retry.
- Error envelope: `{status_code, status_string, request_id, message}`; a `403` means the token lacks the required `legal_*` permission.
