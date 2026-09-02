---
name: tiledb-discover-and-inspect-arrays
description: Find the TileDB Cloud arrays a caller can reach, then inspect one — schema, non-empty domain, metadata and recent activity — before reading any data.
api: TileDB Storage Platform API (v1)
base_url: https://api.tiledb.com/v1
operations:
  - getUser
  - getArraysInNamespace
  - listAssets
  - listPublicAssets
  - getArray
  - loadArraySchema
  - getArrayNonEmptyDomainJson
  - getArrayMetaDataJson
  - getArraySampleData
  - arrayActivityLog
generated: '2026-08-30'
method: generated
source: openapi/tiledb-cloud-v1-openapi.yaml
---

# Discover and inspect TileDB Cloud arrays

Every operationId below is verified present in the TileDB-published v1 contract. Do not invent others.

## Before you start

- Send `X-TILEDB-REST-API-KEY: <token>` on every request. Base URL `https://api.tiledb.com/v1`.
- Assets are addressed as `{namespace}/{array}`, where the namespace is a user or an organization.
  If you do not know the namespace, start with `getUser`.
- Errors come back as `application/json` `{code, message, request_id}`. Keep the `request_id`.

## Steps

1. **Establish who you are.** `getUser` (`GET /user`) returns the authenticated user, including the
   namespaces available to them. This is also the cheapest way to confirm your token works — an
   invalid token returns 401 with the standard error envelope.

2. **List what is reachable.** Pick the narrowest listing for the job:
   - `getArraysInNamespace` (`GET /arrays/{namespace}`) — arrays in one namespace.
   - `listAssets` (`GET /assets/{namespace}`) — every asset type in a namespace (arrays, notebooks,
     UDFs, ML models, files, dashboards, registered task graphs).
   - `listPublicAssets` (`GET /public_assets`) — publicly shared assets, no namespace needed.
   Listings paginate with `page` and `per_page` and return `pagination_metadata`. Walk pages until
   `page` reaches `total_pages`; do not assume one response is the whole set.

3. **Read the array record.** `getArray` (`GET /arrays/{namespace}/{array}`) returns the array's info
   and schema summary.

4. **Load the full schema.** `loadArraySchema` (`POST /arrays/{namespace}/{array}/schema`) returns
   dimensions, attributes, datatypes, tile layout and filters. Note this is a POST despite being a
   read — the contract models it as a load, not a GET.

5. **Bound the data before touching it.** `getArrayNonEmptyDomainJson`
   (`GET /arrays/{namespace}/{array}/non_empty_domain_json`) tells you the coordinate range that
   actually holds data. Query outside it and you get nothing back at cost.

6. **Read the metadata.** `getArrayMetaDataJson` (`GET /arrays/{namespace}/{array}/metadata_json`) is
   the JSON form. The sibling `getArrayMetadata` returns Cap'n Proto — use the `*Json` operation
   unless you have a Cap'n Proto decoder.

7. **Sample, don't scan.** `getArraySampleData` (`GET /arrays/{namespace}/{array}/sample`) returns a
   sample of cells. Use it to sanity-check shape and types before issuing a real query.

8. **Check recent activity if the data looks wrong.** `arrayActivityLog`
   (`GET /arrays/{namespace}/{array}/activity`) shows who wrote to the array and when.

## Rules

- **Prefer `*Json` operations.** `getArrayMetaDataJson`, `getArrayNonEmptyDomainJson` and
  `submitQueryJson` have Cap'n Proto siblings that return `application/capnp`. Requesting the wrong
  one gets you a binary body you cannot parse.
- **No rate limits are published.** TileDB declares no 429, no `Retry-After` and no `RateLimit-*`
  headers. Back off on your own schedule; do not assume you will be told to slow down.
- **This skill is read-only.** Nothing here mutates an array. If a step returns 403 or 404, the array
  is not shared with your namespace — check `getArraySharingPolicies` rather than retrying.
