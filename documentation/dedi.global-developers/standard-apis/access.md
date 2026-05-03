# Access APIs

Access APIs provide lookup, query, version-history, and export endpoints for namespaces, registries, and records.

These APIs are primarily used to read published data, inspect historical versions, and export record sets for downstream use.

## Authentication

The current Postman collection marks most Access APIs as public.

- No authentication required:
  - Lookup endpoints
  - Query endpoints
  - Version list endpoints
  - Registry-by-tag lookup
- Authentication required:
  - CSV export endpoint

## Access API Categories

The current collection groups access endpoints into four practical categories:

- **Lookup**: Fetch a specific namespace, registry, or record
- **Query**: Search through registries or records using filters
- **Version**: List the known versions of a resource
- **Export**: Download record data as CSV

## Lookup APIs

## Namespace Lookup

Public namespace lookup by namespace ID or domain.

**Endpoint:** `GET /dedi/lookup/{namespace}`

**Path Parameters:**
- `namespace` (required): Namespace ID or verified domain

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/lookup/acme', {
  method: 'GET',
  headers: {
    'Accept': 'application/json'
  }
});

const data = await response.json();
```

**Success Response Requirements In Collection:**
- `message`
- `data.namespace_id`
- `data.proof.digest`

**Illustrative Success Shape:**
```json
{
  "message": "Namespace details retrieved successfully",
  "data": {
    "namespace_id": "namespace-id",
    "name": "Acme Namespace",
    "description": "Namespace for production data",
    "proof": {
      "digest": "proof-digest"
    }
  }
}
```

**Allowed Status Codes:**
- `200` - Namespace returned
- `400` - Invalid lookup input
- `404` - Namespace not found
- `500` - Internal server error

## Registry Lookup

Public registry lookup.

**Endpoint:** `GET /dedi/lookup/{namespace}/{registry_name}`

**Path Parameters:**
- `namespace` (required): Namespace ID or verified domain
- `registry_name` (required): Registry name

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/lookup/acme/employees', {
  method: 'GET',
  headers: {
    'Accept': 'application/json'
  }
});

const data = await response.json();
```

**Success Response Requirements In Collection:**
- `message`
- `data.registry_id`
- `data.proof.digest`

**Illustrative Success Shape:**
```json
{
  "message": "Resource retrieved successfully",
  "data": {
    "registry_id": "registry-id",
    "registry_name": "employees",
    "description": "Employee registry",
    "schema": {},
    "tag": "membership",
    "proof": {
      "digest": "proof-digest"
    }
  }
}
```

**Allowed Status Codes:**
- `200` - Registry returned
- `400` - Invalid lookup input
- `404` - Namespace or registry not found
- `500` - Internal server error

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

**Optional Query Behavior:**
- Use `version_id` when you already know the exact version you want
- Use `as_on` when you want the record view as of a specific point in time

**Example Request:**
```http
GET /dedi/lookup/acme/products/item-001?version_id=v1&as_on=2026-01-01T00:00:00Z
```

**JavaScript Example:**
```typescript
const response = await fetch(
  'https://api.dedi.global/dedi/lookup/acme/products/item-001?version_id=v1&as_on=2026-01-01T00:00:00Z',
  {
    method: 'GET',
    headers: {
      'Accept': 'application/json'
    }
  }
);

const data = await response.json();
```

**Success Response Requirements In Collection:**
- `message`
- `data.record_id`
- `data.proof.digest`

**Illustrative Success Shape:**
```json
{
  "message": "Resource retrieved successfully",
  "data": {
    "record_id": "record-id",
    "record_name": "item-001",
    "description": "Product record",
    "details": {
      "name": "Sample Entity"
    },
    "proof": {
      "digest": "proof-digest"
    }
  }
}
```

**Allowed Status Codes:**
- `200` - Record returned
- `400` - Invalid lookup input
- `404` - Namespace, registry, or record not found
- `500` - Internal server error

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

**Example Request:**
```http
GET /dedi/query/acme?status=active&name=employee&sort=date&page=1&page_size=20
```

**Success Response Requirements In Collection:**
- `message`
- `data.registries`

**Allowed Status Codes:**
- `200` - Registries returned
- `400` - Invalid query input
- `404` - Namespace not found
- `500` - Internal server error

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

**Example Request:**
```http
GET /dedi/query/acme/employees?state=live&name=john&sort=name&page=1&page_size=10
```

**Success Response Requirements In Collection:**
- `message`
- `data.records`

**Allowed Status Codes:**
- `200` - Records returned
- `400` - Invalid query input
- `404` - Namespace or registry not found
- `500` - Internal server error

## Query Registries By Tag

Lists verified registries associated with a schema tag.

**Endpoint:** `GET /dedi/{registry_tag}/get-registry-by-tag`

**Path Parameters:**
- `registry_tag` (required): Schema tag to query

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/membership/get-registry-by-tag', {
  method: 'GET',
  headers: {
    'Accept': 'application/json'
  }
});

const data = await response.json();
```

**Success Response Requirements In Collection:**
- `message`
- `data.registries`

**Allowed Status Codes:**
- `200` - Registries returned
- `400` - Invalid tag input
- `500` - Internal server error

## Version APIs

## Namespace Version List

Public namespace version list.

**Endpoint:** `GET /dedi/versions/{namespace}`

**Path Parameters:**
- `namespace` (required): Namespace ID or verified domain

**Success Response Requirements In Collection:**
- `message`
- `data.versions`

**Allowed Status Codes:**
- `200` - Versions returned
- `400` - Invalid input
- `404` - Namespace not found
- `500` - Internal server error

## Registry Version List

Public registry version list.

**Endpoint:** `GET /dedi/versions/{namespace}/{registry_name}`

**Path Parameters:**
- `namespace` (required): Namespace ID or verified domain
- `registry_name` (required): Registry name

**Success Response Requirements In Collection:**
- `message`
- `data.versions`

**Allowed Status Codes:**
- `200` - Versions returned
- `400` - Invalid input
- `404` - Namespace or registry not found
- `500` - Internal server error

## Record Version List

Public record version list.

**Endpoint:** `GET /dedi/versions/{namespace}/{registry_name}/{record_name}`

**Path Parameters:**
- `namespace` (required): Namespace ID or verified domain
- `registry_name` (required): Registry name
- `record_name` (required): Record name

**Success Response Requirements In Collection:**
- `message`
- `data.versions`

**Allowed Status Codes:**
- `200` - Versions returned
- `400` - Invalid input
- `404` - Namespace, registry, or record not found
- `500` - Internal server error

## Export APIs

## Export Records As CSV

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

**Example Request:**
```typescript
const response = await fetch(
  'https://api.dedi.global/dedi/acme/employees/export-as-csv?state=live',
  {
    method: 'GET',
    headers: {
      'Authorization': 'Bearer YOUR_API_KEY',
      'Accept': 'text/csv'
    }
  }
);
```

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
