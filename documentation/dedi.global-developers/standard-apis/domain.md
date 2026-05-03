# Domain Verification APIs

The Domain Verification APIs manage namespace verification, stored DNS verification material, and verification-request notifications.

🔒 **Authentication Required**: Every endpoint in this section requires authentication.

## Endpoints Overview

| Endpoint | Method | Purpose |
| --- | --- | --- |
| `/dedi/generate-dns-txt/{namespace}/{domain}` | `GET` | Generate or reuse verification TXT material |
| `/dedi/verify-domain` | `POST` | Complete domain verification |
| `/dedi/check-verification/{namespace}` | `GET` | Check namespace verification status |
| `/dedi/get-dns-txt/{namespace}` | `GET` | Get stored DNS TXT material |
| `/dedi/{namespace}/{domain}/remove-namespace-verification` | `POST` | Remove namespace verification |
| `/dedi/notifications/pending` | `GET` | List unread verification notifications |
| `/dedi/notifications/history` | `GET` | List read verification notifications |
| `/dedi/notifications/read-all` | `POST` | Mark all verification notifications as read |
| `/dedi/notifications/{notification_id}/read` | `POST` | Mark one verification notification as read |
| `/dedi/verification-requests/{request_id}/accept` | `POST` | Accept a verification request |
| `/dedi/verification-requests/{request_id}/reject` | `POST` | Reject a verification request |

## Verification Models

The current Postman collection documents multiple verification modes via `verification_method`:

- `self_dns`
- `self_http`
- `request_other_namespace`

When `verification_method=request_other_namespace`, the `target_namespace` query parameter is used.

## Generate DNS TXT

Generates or reuses verification TXT material for namespace domain verification.

**Endpoint**: `GET /dedi/generate-dns-txt/{namespace}/{domain}?verification_method={method}&target_namespace={target}`

**Path Parameters**:

- `namespace`: namespace identifier
- `domain`: domain being verified

**Query Parameters**:

- `verification_method`: `self_dns`, `self_http`, or `request_other_namespace`
- `target_namespace`: required only for `request_other_namespace`

**Success Response**:

- `200 OK`
- Includes `message`
- May include `txt` or `data.txt`
- May include `data.request_id`

**Error Status Codes**:

- `400`
- `401`
- `404`
- `500`

## Verify Domain

Checks whether namespace domain verification can be completed.

**Endpoint**: `POST /dedi/verify-domain`

**Request Body**:

```json
{
  "namespace_id": "<namespace>",
  "domain": "example.com"
}
```

**Success Response**:

- `200 OK`
- Includes `message`

**Error Status Codes**:

- `400`
- `401`
- `404`
- `500`

## Check Verification

Returns the verified flag and primary verified domain for the namespace.

**Endpoint**: `GET /dedi/check-verification/{namespace}`

**Success Response**:

- `200 OK`
- Includes `verified`

**Error Status Codes**:

- `400`
- `401`
- `404`
- `500`

## Get DNS TXT

Returns stored TXT material for the namespace domain entry.

**Endpoint**: `GET /dedi/get-dns-txt/{namespace}`

**Success Response**:

- `200 OK`
- Includes `namespace_id`
- Includes `domain`
- Includes `txt`

**Error Status Codes**:

- `400`
- `401`
- `404`
- `500`

## Remove Verification

Removes domain verification rows and related requests for the namespace/domain pair.

**Endpoint**: `POST /dedi/{namespace}/{domain}/remove-namespace-verification`

**Success Response**:

- `200 OK`
- Includes `message`

**Error Status Codes**:

- `400`
- `401`
- `404`
- `500`

## Notifications Pending

Returns unread verification-request notifications for the authenticated user.

**Endpoint**: `GET /dedi/notifications/pending`

**Success Response**:

- `200 OK`
- Includes `message`
- Includes `unread_count`
- Includes `data`

**Notes**:

- The Postman collection saves `data[0].id` as `notification_id`
- The Postman collection saves `data[0].request_id` as `request_id`

**Error Status Codes**:

- `400`
- `401`
- `500`

## Notifications History

Returns already-read verification-request notifications.

**Endpoint**: `GET /dedi/notifications/history`

**Success Response**:

- `200 OK`
- Includes `message`
- Includes `data`

**Error Status Codes**:

- `400`
- `401`
- `500`

## Notifications Read All

Marks all unread verification notifications as read.

**Endpoint**: `POST /dedi/notifications/read-all`

**Success Response**:

- `200 OK`
- Includes `message`
- Includes `data.affected`

**Error Status Codes**:

- `400`
- `401`
- `500`

## Notification Read

Marks a single verification notification as read.

**Endpoint**: `POST /dedi/notifications/{notification_id}/read`

**Success Response**:

- `200 OK`
- Includes `message`

**Error Status Codes**:

- `400`
- `401`
- `404`
- `500`

## Accept Verification Request

Accepts a pending namespace verification request as a delegate of the target namespace.

**Endpoint**: `POST /dedi/verification-requests/{request_id}/accept`

**Success Response**:

- `200 OK`
- Includes `message`
- May include `data.request_id`, `data.registry_id`, and `data.record_id`

**Error Status Codes**:

- `400`
- `401`
- `403`
- `404`
- `500`

## Reject Verification Request

Rejects a pending namespace verification request.

**Endpoint**: `POST /dedi/verification-requests/{request_id}/reject`

**Success Response**:

- `200 OK`
- Includes `message`

**Error Status Codes**:

- `400`
- `401`
- `403`
- `404`
- `500`

## Important Changes Reflected Here

This page has been aligned to the current Postman collection and now reflects several changes beyond basic DNS verification:

- `generate-dns-txt` now documents `verification_method` and `target_namespace`
- `verify-domain` documents both `namespace_id` and `domain` in the request body
- `remove-namespace-verification` is documented with both `namespace` and `domain` in the path
- notification endpoints are included in this section
- verification-request accept/reject endpoints are included in this section

## Practical Verification Flow

1. Generate verification material with `generate-dns-txt`
2. Publish the TXT record or follow the configured verification method
3. Complete verification using `verify-domain`
4. Check status with `check-verification`
5. Use notification and verification-request endpoints when cross-namespace approval flows are involved
