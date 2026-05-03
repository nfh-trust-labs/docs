# Access APIs

Access APIs provide lookup, query, version-history, and export endpoints for namespaces, registries, and records.

## Authentication

The current Postman collection marks most Access APIs as public.

- No authentication required:
  - Lookup endpoints
  - Query endpoints
  - Version list endpoints
  - Registry-by-tag lookup
- Authentication required:
  - CSV export endpoint

## Lookup APIs

## Namespace Lookup

Public namespace lookup by namespace ID or domain.

**Endpoint:** `GET /dedi/lookup/{namespace}`

**Path Parameters:**
- `namespace` (required): Namespace ID or verified domain

**Allowed Status Codes:**
- `200` - Namespace returned
- `400` - Invalid lookup input
- `404` - Namespace not found
- `500` - Internal server error

**Success Response Requirements in Collection:**
- `message`
- `data.namespace_id`
- `data.proof.digest`

## Registry Lookup

Public registry lookup.

**Endpoint:** `GET /dedi/lookup/{namespace}/{registry_name}`

**Path Parameters:**
- `namespace` (required): Namespace ID or verified domain
- `registry_name` (required): Registry name

**Allowed Status Codes:**
- `200` - Registry returned
- `400` - Invalid lookup input
- `404` - Namespace or registry not found
- `500` - Internal server error

**Success Response Requirements in Collection:**
- `message`
- `data.registry_id`
- `data.proof.digest`

## Record Lookup

Public record lookup with optional historical query parameters.

**Endpoint:** `GET /dedi/lookup/{namespace}/{registry_name}/{record_name}`

**Path Parameters:**
- `namespace` (required): Namespace ID or verified domain
- `registry_name` (required): Registry name
- `record_name` (required): Record name

**Query Parameters:**
- `version_id` (optional): Specific record version to return
- `as_on` (optional): Historical lookup timestamp/filter value

**Example Request:**
```http
GET /dedi/lookup/acme/products/item-001?version_id=v1&as_on=2026-01-01T00:00:00Z
```

**Allowed Status Codes:**
- `200` - Record returned
- `400` - Invalid lookup input
- `404` - Namespace, registry, or record not found
- `500` - Internal server error

**Success Response Requirements in Collection:**
- `message`
- `data.record_id`
- `data.proof.digest`

## Query APIs

## Namespace Query (Registries)

Public registry query within a namespace.

**Endpoint:** `GET /dedi/query/{namespace}`

**Path Parameters:**
- `namespace` (required): Namespace ID or verified domain

**Query Parameters:**
- `from` (optional)
- `to` (optional)
- `status` (optional): Collection default is `active`
- `name` (optional): Search string
- `sort` (optional): Collection default is `date`
- `page` (optional)
- `page_size` (optional)
- `as_on` (optional)

**Allowed Status Codes:**
- `200` - Registries returned
- `400` - Invalid query input
- `404` - Namespace not found
- `500` - Internal server error

**Success Response Requirements in Collection:**
- `message`
- `data.registries`

## Registry Query (Records)

Public record query within a registry.

**Endpoint:** `GET /dedi/query/{namespace}/{registry_name}`

**Path Parameters:**
- `namespace` (required): Namespace ID or verified domain
- `registry_name` (required): Registry name

**Query Parameters:**
- `from` (optional)
- `to` (optional)
- `state` (optional): Collection default is `live`
- `name` (optional): Search string
- `sort` (optional): Collection default is `name`
- `page` (optional)
- `page_size` (optional)
- `as_on` (optional)

**Allowed Status Codes:**
- `200` - Records returned
- `400` - Invalid query input
- `404` - Namespace or registry not found
- `500` - Internal server error

**Success Response Requirements in Collection:**
- `message`
- `data.records`

## Query Registries by Tag

Lists verified registries associated with a schema tag.

**Endpoint:** `GET /dedi/{registry_tag}/get-registry-by-tag`

**Path Parameters:**
- `registry_tag` (required): Schema tag to query

**Allowed Status Codes:**
- `200` - Registries returned
- `400` - Invalid tag input
- `500` - Internal server error

**Success Response Requirements in Collection:**
- `message`
- `data.registries`

## Version APIs

## Namespace Version List

Public namespace version list.

**Endpoint:** `GET /dedi/versions/{namespace}`

**Path Parameters:**
- `namespace` (required): Namespace ID or verified domain

**Allowed Status Codes:**
- `200` - Versions returned
- `400` - Invalid input
- `404` - Namespace not found
- `500` - Internal server error

**Success Response Requirements in Collection:**
- `message`
- `data.versions`

## Registry Version List

Public registry version list.

**Endpoint:** `GET /dedi/versions/{namespace}/{registry_name}`

**Path Parameters:**
- `namespace` (required): Namespace ID or verified domain
- `registry_name` (required): Registry name

**Allowed Status Codes:**
- `200` - Versions returned
- `400` - Invalid input
- `404` - Namespace or registry not found
- `500` - Internal server error

**Success Response Requirements in Collection:**
- `message`
- `data.versions`

## Record Version List

Public record version list.

**Endpoint:** `GET /dedi/versions/{namespace}/{registry_name}/{record_name}`

**Path Parameters:**
- `namespace` (required): Namespace ID or verified domain
- `registry_name` (required): Registry name
- `record_name` (required): Record name

**Allowed Status Codes:**
- `200` - Versions returned
- `400` - Invalid input
- `404` - Namespace, registry, or record not found
- `500` - Internal server error

**Success Response Requirements in Collection:**
- `message`
- `data.versions`

## Export APIs

## Export Records as CSV

Exports registry records as CSV. The collection explicitly requires authentication for this endpoint.

**Endpoint:** `GET /dedi/{namespace}/{registry_name}/export-as-csv`

**Authentication:** Required

**Path Parameters:**
- `namespace` (required): Namespace ID or verified domain
- `registry_name` (required): Registry name

**Query Parameters:**
- `from` (optional)
- `to` (optional)
- `state` (optional): Collection default is `live`
- `name` (optional): Search string
- `as_on` (optional)

**Headers:**
- `Accept: text/csv`

**Allowed Status Codes:**
- `200` - CSV returned
- `400` - Invalid export input
- `401` - Authentication failed
- `404` - Namespace or registry not found
- `500` - Internal server error

## Notes

- `access.md` in the current collection is not an auth-only surface; most endpoints are public lookup and query endpoints.
- Successful lookup responses are expected to include proof data such as `data.proof.digest` for downstream verification workflows.
- The separate `Lookup Verification` page documents how those lookup responses are verified and logged.
