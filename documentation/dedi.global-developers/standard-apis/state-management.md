# State Management APIs

State Management APIs cover soft deletion, registry activation changes, deleted-resource discovery, and restoration flows. All endpoints in this section require authentication.

## Authentication

Use either of the supported authentication methods described in [Authentication APIs](authentication.md):

- API key authentication using `Authorization: Bearer <api_key>`
- Auth cookie authentication for browser-based sessions

## Delete Namespace

Moves a namespace into deleted tables for later recovery.

**Endpoint:** `DELETE /dedi/{namespace}/delete-namespace`

**Path Parameters:**
- `namespace` (required): Namespace ID or verified domain

**Request Body:**
```json
{
  "reason": "cleanup"
}
```

**Allowed Status Codes:**
- `200` - Namespace deleted
- `400` - Invalid request body
- `401` - Authentication failed
- `403` - Not authorized to delete the namespace
- `404` - Namespace not found
- `500` - Internal server error

## Delete Registry

Moves a registry and its records into deleted tables.

**Endpoint:** `DELETE /dedi/{namespace}/{registry_name}/delete-registry`

**Path Parameters:**
- `namespace` (required): Namespace ID or verified domain
- `registry_name` (required): Registry to delete

**Request Body:**
```json
{
  "reason": "cleanup"
}
```

**Allowed Status Codes:**
- `200` - Registry deleted
- `400` - Invalid request body
- `401` - Authentication failed
- `403` - Not authorized to delete the registry
- `404` - Namespace or registry not found
- `500` - Internal server error

## Delete Records

Deletes one or more records by name.

**Endpoint:** `DELETE /dedi/{namespace}/{registry_name}/delete-records`

**Path Parameters:**
- `namespace` (required): Namespace ID or verified domain
- `registry_name` (required): Registry containing the records

**Request Body:**
```json
{
  "records": [
    "record-name"
  ],
  "reason": "cleanup"
}
```

**Allowed Status Codes:**
- `200` - Records deleted
- `400` - Invalid request body
- `401` - Authentication failed
- `403` - Not authorized to delete the records
- `404` - Namespace, registry, or records not found
- `500` - Internal server error

## Inactivate Registry

Moves a live registry into inactive state.

**Endpoint:** `POST /dedi/{namespace}/{registry_name}/inactivate-registry`

**Path Parameters:**
- `namespace` (required): Namespace ID or verified domain
- `registry_name` (required): Registry to inactivate

**Request Body:**
No request body is required.

**Allowed Status Codes:**
- `200` - Registry inactivated
- `400` - Invalid state transition or request
- `401` - Authentication failed
- `403` - Not authorized to inactivate the registry
- `404` - Namespace or registry not found
- `500` - Internal server error

## Reactivate Registry

Moves an inactive registry back to live state.

**Endpoint:** `POST /dedi/{namespace}/{registry_name}/reactivate-registry`

**Path Parameters:**
- `namespace` (required): Namespace ID or verified domain
- `registry_name` (required): Registry to reactivate

**Request Body:**
No request body is required.

**Allowed Status Codes:**
- `200` - Registry reactivated
- `400` - Invalid state transition or request
- `401` - Authentication failed
- `403` - Not authorized to reactivate the registry
- `404` - Namespace or registry not found
- `500` - Internal server error

## Get Deleted Namespaces

Lists deleted namespaces accessible to the current user.

**Endpoint:** `GET /dedi/deleted/namespaces`

**Request Body:**
No request body is required.

**Success Response:**
- `200 OK`

```json
{
  "message": "Deleted namespaces fetched successfully",
  "data": []
}
```

**Allowed Status Codes:**
- `200` - Deleted namespaces returned
- `401` - Authentication failed
- `404` - No deleted namespaces found
- `500` - Internal server error

## Get Deleted Registries

Lists deleted registries accessible to the current user.

**Endpoint:** `GET /dedi/deleted/registries`

**Request Body:**
No request body is required.

**Success Response:**
- `200 OK`

```json
{
  "message": "Deleted registries fetched successfully",
  "data": [
    {
      "deleted_history_id": "history-id"
    }
  ]
}
```

**Allowed Status Codes:**
- `200` - Deleted registries returned
- `401` - Authentication failed
- `404` - No deleted registries found
- `500` - Internal server error

**Note:** The collection stores `data[0].deleted_history_id` for later restore calls.

## Get Deleted Records

Lists deleted records accessible through current registry authorizations.

**Endpoint:** `GET /dedi/deleted/records`

**Request Body:**
No request body is required.

**Success Response:**
- `200 OK`

```json
{
  "message": "Deleted records fetched successfully",
  "data": [
    {
      "deleted_history_id": "history-id"
    }
  ]
}
```

**Allowed Status Codes:**
- `200` - Deleted records returned
- `401` - Authentication failed
- `404` - No deleted records found
- `500` - Internal server error

**Note:** The collection stores `data[0].deleted_history_id` for later restore calls.

## Restore Namespace

Restores a deleted namespace by `namespace_id`.

**Endpoint:** `POST /dedi/restore/namespace`

**Request Body:**
```json
{
  "namespace_id": "namespace-id"
}
```

**Allowed Status Codes:**
- `200` - Namespace restored
- `400` - Invalid request body
- `401` - Authentication failed
- `403` - Not authorized to restore the namespace
- `404` - Deleted namespace not found
- `409` - Restore conflict
- `500` - Internal server error

## Restore Registry

Restores a deleted registry. Provide `deleted_history_id` when multiple deleted histories exist for the same registry.

**Endpoint:** `POST /dedi/restore/registry`

**Request Body:**
```json
{
  "namespace_id": "namespace-id",
  "registry_name": "registry-name",
  "deleted_history_id": "history-id"
}
```

**Allowed Status Codes:**
- `200` - Registry restored
- `400` - Invalid request body
- `401` - Authentication failed
- `403` - Not authorized to restore the registry
- `404` - Deleted registry not found
- `409` - Restore conflict
- `500` - Internal server error

## Restore Records

Restores deleted records. Provide `deleted_history_id` when multiple deleted histories exist for the same record name.

**Endpoint:** `POST /dedi/restore/records`

**Request Body:**
```json
{
  "records": [
    {
      "namespace_id": "namespace-id",
      "registry_name": "registry-name",
      "record_name": "record-name",
      "deleted_history_id": "history-id"
    }
  ]
}
```

**Allowed Status Codes:**
- `200` - Records restored
- `400` - Invalid request body
- `401` - Authentication failed
- `403` - Not authorized to restore the records
- `404` - Deleted records not found
- `409` - Restore conflict
- `500` - Internal server error

## Notes

- The current Postman collection models deletion as a recoverable flow backed by deleted-resource tables.
- Restoration is split by resource type: namespace, registry, and records each use separate restore endpoints.
- `deleted_history_id` is optional in the collection defaults, but it is specifically called out for cases where multiple deleted histories exist for the same resource name.
- The older suspend, reinstate, and revoke flows are not part of the current collection for this section.
