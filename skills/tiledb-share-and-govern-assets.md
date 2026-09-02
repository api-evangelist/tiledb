---
name: tiledb-share-and-govern-assets
description: Register, group, share and un-share TileDB Cloud assets, and mint or revoke the scoped REST API tokens that gate them — with the reversal path for each write stated up front.
api: TileDB Storage Platform API (v1)
base_url: https://api.tiledb.com/v1
operations:
  - registerArray
  - deregisterArray
  - deleteArray
  - createGroup
  - registerGroup
  - changeGroupContents
  - getGroupContents
  - listOwnedGroups
  - listSharedGroups
  - shareArray
  - getArraySharingPolicies
  - shareGroup
  - getGroupSharingPolicies
  - cancelShareArrayByInvite
  - cancelShareGroupByInvite
  - requestToken
  - getTokenScopes
  - revokeToken
generated: '2026-08-30'
method: generated
source: openapi/tiledb-cloud-v1-openapi.yaml
---

# Share and govern TileDB Cloud assets

Every operationId below is verified present in the TileDB-published v1 contract. This skill writes.
Read the reversal note on each step before you run it.

## Before you start

- `X-TILEDB-REST-API-KEY: <token>`, base URL `https://api.tiledb.com/v1`.
- **No idempotency keys exist in this API.** A retried write is a second write. Read the current state
  back (`getArraySharingPolicies`, `getGroupContents`) instead of blind-retrying.

## Registering and grouping

1. **Register an array into TileDB Cloud.** `registerArray`
   (`POST /arrays/{namespace}/{array}/register`) makes an array in object storage visible as a
   TileDB Cloud asset.
   *Reversal:* `deregisterArray` (`DELETE /arrays/{namespace}/{array}/deregister`) removes it from
   TileDB Cloud and leaves the underlying data alone. No time window is stated.

2. **Do not confuse deregister with delete.** `deleteArray` (`DELETE /arrays/{namespace}/{array}`)
   deletes the array. **There is no restore operation.** If your intent is "take it out of the
   catalog", use `deregisterArray`.

3. **Create a group.** `createGroup` (`POST /groups/{namespace}/create`), then add members with
   `registerGroup` (`POST /groups/{namespace}/{array}/register`) or `changeGroupContents`
   (`POST /groups/{group_namespace}/{group_name}/contents`). Read the result back with
   `getGroupContents`.
   *Reversal:* `changeGroupContents` also removes members. `deleteGroup` is not reversible.

## Sharing

4. **Read the current policy before changing it.** `getArraySharingPolicies`
   (`GET /arrays/{namespace}/{array}/share`) and `getGroupSharingPolicies`
   (`GET /groups/{group_namespace}/{group_name}/share`) return who has what.

5. **Grant access.** `shareArray` (`PATCH /arrays/{namespace}/{array}/share`) takes an `ArraySharing`
   body: a target `namespace`, its `namespace_type` (user or organization), and an `actions` list drawn
   from the `ArrayActions` enum — `read`, `write`, `edit`, `read_array_logs`, `read_array_info`,
   `read_array_schema`. `shareGroup` is the group equivalent and carries both `group_actions`
   (`read`, `write`, `edit`) and `array_actions` for the group's subarrays.
   *Reversal:* there is **no** distinct unshare operation. Sharing is a PATCH of the actions list, so
   revocation means rewriting that list — and TileDB does not document what an empty list does.
   Verify with step 4 after every change rather than assuming.

6. **Cancel a pending invitation.** Shares issued to someone who has not accepted are invitations, and
   those genuinely are cancellable: `cancelShareArrayByInvite`, `cancelShareGroupByInvite`,
   `cancelJoinOrganization`, `cancelSharePayment`. This is the cleanest reversal in the API.

## Tokens

7. **Mint a scoped token.** `requestToken` (`POST /token`) takes a `TokenRequest` with an optional
   `name`, an optional `expires` timestamp and an optional `scope` array.
   - **If you omit `expires`, the token expires in 30 minutes.**
   - **If you omit `scope`, the token gets ALL permissions (`*`).** Always set a scope.
   - Valid scopes come from the `TokenScope` enum: `user:read`, `user:read-write`, `user:admin`,
     `array:read`, `array:read-write`, `array:admin`, `organization:read`, `organization:read-write`,
     `organization:admin`, `group:read`, `group:read-write`, `group:admin`, plus `password_reset`,
     `confirm_email` and `*`. `getTokenScopes` (`GET /tokens/scopes`) lists them live.

8. **Revoke it.** `revokeToken` (`DELETE /tokens/{token}`). This is the one reversal in the API with a
   provider-stated bound, and the bound is the expiry rather than an undo window.

## Rules

- **Least privilege by default.** Never mint a `*` token for an agent. Pick the narrowest
  `TokenScope` that covers the flow, and set an explicit `expires`.
- **Irreversible operations — confirm with a human first:** `deleteArray`, `deleteGroup`, `deleteUser`,
  `deleteOrganization`, `deleteUDFInfo`, `deleteRegisteredTaskGraph`, `deleteAWSAccessCredentials`,
  `deleteSSODomain`, `vacuumArray`, `consolidateArray`. The last two destroy the array's time-travel
  history, which is the only way an earlier state could otherwise have been recovered.
- **Credentials are assets too.** `addAWSAccessCredentials` / `updateAWSAccessCredentials` /
  `deleteAWSAccessCredentials` manage the cloud credentials TileDB uses to reach a customer's storage.
  Treat any operation on `/credentials/{namespace}/aws` as privileged.
