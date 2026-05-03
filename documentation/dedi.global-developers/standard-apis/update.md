# Update Management APIs

Update Management APIs let you modify existing namespaces, registries, and records without changing their identifiers. All endpoints in this section require authentication.

## Authentication

Use either of the supported authentication methods described in [Authentication APIs](authentication.md):

- API key authentication using `Authorization: Bearer <api_key>`
- Auth cookie authentication for browser-based sessions

## Update Namespace

Updates namespace metadata. The current collection requires at least `name` or `description`.

**Endpoint:** `POST /dedi/{namespace}/update-namespace`

**Path Parameters:**
- `namespace` (required): Namespace ID or verified domain

**Request Body:**
```json
{
  "name": "Acme Namespace",
  "description": "Updated namespace description",
  "meta": {},
  "ttl": 3600
}
```

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/acme/update-namespace', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json',
    'Accept': 'application/json'
  },
  body: JSON.stringify({
    name: 'Acme Namespace',
    description: 'Updated namespace description',
    meta: {},
    ttl: 3600
  })
});

const data = await response.json();
```

**Success Response:**
- `201 Created`

```json
{
  "message": "namespace updated"
}
```

**Allowed Status Codes:**
- `201` - Namespace updated
- `400` - Invalid request body
- `401` - Authentication failed
- `403` - Not authorized to update the namespace
- `404` - Namespace not found
- `500` - Internal server error

## Update Registry

Updates registry metadata using the registry update endpoint.

**Endpoint:** `POST /dedi/{namespace}/{registry_name}/update-registry`

**Path Parameters:**
- `namespace` (required): Namespace ID or verified domain
- `registry_name` (required): Registry to update

**Request Body:**
```json
{
  "description": "Updated registry description",
  "meta": {},
  "ttl": 3600
}
```

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/acme/products/update-registry', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json',
    'Accept': 'application/json'
  },
  body: JSON.stringify({
    description: 'Updated registry description',
    meta: {},
    ttl: 3600
  })
});

const data = await response.json();
```

**Success Response:**
- `200 OK`

```json
{
  "message": "registry updated"
}
```

**Allowed Status Codes:**
- `200` - Registry updated
- `400` - Invalid request body
- `401` - Authentication failed
- `403` - Not authorized to update the registry
- `404` - Namespace or registry not found
- `500` - Internal server error

## Update Record

Updates a draft in place or creates a new live version for a live record.

**Endpoint:** `POST /dedi/{namespace}/{registry_name}/{record_name}/update-record`

**Path Parameters:**
- `namespace` (required): Namespace ID or verified domain
- `registry_name` (required): Registry containing the record
- `record_name` (required): Record to update

**Request Body:**
```json
{
  "description": "Updated record description",
  "details": {
    "name": "Updated Entity",
    "description": "Updated sample value"
  },
  "meta": {},
  "valid_till": "2026-12-31T23:59:59Z",
  "ttl": 3600
}
```

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/acme/products/item-001/update-record', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json',
    'Accept': 'application/json'
  },
  body: JSON.stringify({
    description: 'Updated record description',
    details: {
      name: 'Updated Entity',
      description: 'Updated sample value'
    },
    meta: {},
    valid_till: '2026-12-31T23:59:59Z',
    ttl: 3600
  })
});

const data = await response.json();
```

**Success Response:**
- `200 OK`

```json
{
  "message": "Record updated"
}
```

**Allowed Status Codes:**
- `200` - Record updated
- `400` - Invalid request body or schema mismatch
- `401` - Authentication failed
- `403` - Not authorized to update the record
- `404` - Namespace, registry, or record not found
- `500` - Internal server error

## Notes

- Set `Content-Type: application/json` and `Accept: application/json` for all update endpoints.
- The Postman collection models record updates as state-aware: drafts are updated in place, while live records create a new version.
- Use the state management endpoints for deletion, restoration, and registry activation changes rather than update endpoints.
