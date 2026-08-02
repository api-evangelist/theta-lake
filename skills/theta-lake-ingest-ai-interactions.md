---
name: Ingest AI interactions into Theta Lake
description: Authenticate, find the target integration, and upload AI (aiComms) interactions for archiving, compliance analysis, and supervision.
api: openapi/theta-lake-openapi-original.yml
operations:
- 'POST /token'
- 'GET /integrations'
- 'GET /ingestion/quota'
- 'POST /ingestion/integration/{id}/ai_interactions'
- 'GET /ingestion/integration/{id}/state'
---

# Ingest AI interactions into Theta Lake

Use this skill to push AI prompt/response (aiComms) content into Theta Lake for archiving and proactive compliance.

## Auth
1. `POST /token` with `grant_type=client_credentials`, `client_id`, `client_secret` (x-www-form-urlencoded). Receive a JWT `access_token` (expires_in 3600s). Requires permission: `none`.
2. Send `Authorization: Bearer <access_token>` on every subsequent call.

## Steps
1. `GET /integrations` to locate the integration you will ingest into; note its `id`. Requires `integrations:read`.
2. `GET /ingestion/quota` to confirm remaining ingestion capacity. Requires `ingestion:read`.
3. `POST /ingestion/integration/{id}/ai_interactions` with the AI interaction payload (prompt/response content, participants, timestamps). Requires `ingestion:upload`. Rate-limit tier: heavy.
4. `GET /ingestion/integration/{id}/state` to confirm the ingestion was accepted and processed. Requires `ingestion:read`.

## Rules
- No idempotency key is supported — do not blindly retry a `POST .../ai_interactions` on a network error; check `GET .../state` first to avoid duplicate records.
- Every response carries a `request_id`; log it for support correlation.
- On `429 TooManyRequests`, back off (heavy tier); on `402 Payment Required` the license does not cover this endpoint.
- Error envelope: `{status_code, status_string, request_id, message}` (see errors/theta-lake-problem-types.yml).
