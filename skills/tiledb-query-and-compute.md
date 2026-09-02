---
name: tiledb-query-and-compute
description: Run a read against a TileDB Cloud array, a serverless SQL query, or a user-defined function — sizing the work first and handling the fact that no dry-run mode exists.
api: TileDB Storage Platform API (v1)
base_url: https://api.tiledb.com/v1
operations:
  - getEstResultSizes
  - getArrayMaxBufferSizes
  - submitQueryJson
  - finalizeQuery
  - runSQL
  - submitGenericUDF
  - submitUDF
  - registerUDFInfo
  - getUDFInfo
  - getTiledbStats
generated: '2026-08-30'
method: generated
source: openapi/tiledb-cloud-v1-openapi.yaml
---

# Query and compute on TileDB Cloud

Every operationId below is verified present in the TileDB-published v1 contract.

## Before you start

- `X-TILEDB-REST-API-KEY: <token>`, base URL `https://api.tiledb.com/v1`.
- Compute is metered. TileDB sells vCPUs and, on individual accounts, runs against a credit balance.
  An oversized query costs real money and there is no dry-run parameter anywhere in the contract.

## Steps

1. **Size the read first.** This is the substitute for a dry run.
   - `getEstResultSizes` (`POST /arrays/{namespace}/{array}/query/est_result_sizes`) estimates how much
     data a subarray will return.
   - `getArrayMaxBufferSizes` (`GET /arrays/{namespace}/{array}/max_buffer_sizes`) gives the buffer
     sizes a client must allocate.
   Run one of these before any query whose subarray you did not compute from
   `getArrayNonEmptyDomainJson`.

2. **Submit the query.** `submitQueryJson`
   (`POST /arrays/{namespace}/{array}/query/submit_query_json`) takes and returns JSON. The sibling
   `submitQuery` speaks `application/capnp` and is what the native clients use for throughput — only
   reach for it if you can decode Cap'n Proto.

3. **Handle incomplete reads.** TileDB returns partial results when a read exceeds the buffer budget.
   Re-submit with the returned continuation state until the query reports complete, and call
   `finalizeQuery` (`POST /arrays/{namespace}/{array}/query/finalize`) where the contract requires it
   for the write path. A single response is not proof you have the whole result.

4. **Or query with SQL.** `runSQL` (`POST /sql/{namespace}`) runs serverless SQL against tables and
   arrays in a namespace. Use this instead of hand-building subarrays when the shape is tabular.

5. **Or run code next to the data.** `submitGenericUDF` (`POST /udfs/generic/{namespace}`) executes a
   user-defined function without binding it to one array; `submitUDF`
   (`POST /arrays/{namespace}/{array}/udf/submit`) runs one against a specific array;
   `submitMultiArrayUDF` spans several. Register a reusable UDF first with `registerUDFInfo`
   (`POST /udf/{namespace}/{name}`) and read it back with `getUDFInfo`.

6. **Check what it cost.** `getTiledbStats` (`GET /.stats`) returns platform statistics for the caller.

## Rules

- **There is no dry-run.** Nothing in the contract validates a query without executing it. Steps 1 is
  the only pre-flight available; use it.
- **No idempotency keys.** A retried `submitGenericUDF` or `runSQL` runs a second time and bills a
  second time. Track your own request identity before retrying anything that computes.
- **Escalate a 402.** `getNotebookServerStatus` is the one operation declaring `402 Payment Required`;
  a 402 anywhere means credit or billing, not throttling.
- **Record the `request_id`.** Every error carries one and it is the only handle TileDB support has.
