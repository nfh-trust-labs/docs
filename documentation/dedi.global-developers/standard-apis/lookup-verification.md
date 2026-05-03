# Lookup Verification

Lookup Verification APIs verify lookup response objects and retrieve verification logs. These endpoints are authenticated in the current Postman collection.

## Authentication

Use either of the supported authentication methods described in [Authentication APIs](authentication.md):

- API key authentication using `Authorization: Bearer <api_key>`
- Auth cookie authentication for browser-based sessions

## Why Use Lookup Verification

Lookup APIs return proof-bearing objects. Verification endpoints let you submit those lookup responses back to DeDi.global so the platform can validate them and store an audit log of the verification attempt.

Typical use cases:

- Verify a namespace response before trusting ownership or verification status
- Verify a registry lookup before consuming its schema
- Verify a record lookup before using the returned business data
- Keep an audit trail of who verified which response and when

## Verify Namespace Lookup

Verifies a namespace lookup response object and stores a verification log.

**Endpoint:** `POST /dedi/verify-namespace-lookup`

**Request Body:**
```json
{
  "namespace_lookup_response": {
    "message": "Namespace details retrieved successfully",
    "data": {
      "name": "Sample Namespace",
      "namespace_id": "namespace-id",
      "digest": "paste-proof-digest",
      "description": "Sample namespace description",
      "created_by": "did:cord:creator",
      "genesis": "2026-01-01T00:00:00.000Z",
      "created_at": "2026-01-01T00:00:00.000Z",
      "updated_at": "2026-01-01T00:00:00.000Z",
      "state": "live",
      "version": "1",
      "version_count": 1,
      "is_verified": false,
      "meta": {},
      "registry_count": 0,
      "ttl": 3600,
      "proof": {
        "type": "DediNamespaceProof2026",
        "namespace_did": "namespace-id",
        "creator_did": "did:cord:creator",
        "digest": "paste-proof-digest",
        "network_genesis": "paste-network-genesis"
      }
    }
  }
}
```

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/verify-namespace-lookup', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json',
    'Accept': 'application/json'
  },
  body: JSON.stringify({
    namespace_lookup_response: namespaceLookupResponse
  })
});

const data = await response.json();
```

**Allowed Status Codes:**
- `200` - Verification completed
- `400` - Invalid verification payload
- `401` - Authentication failed
- `404` - Related resource or user not found
- `500` - Internal server error

## Verify Registry Lookup

Verifies a registry lookup response object and stores a verification log.

**Endpoint:** `POST /dedi/verify-registry-lookup`

**Request Body:**
```json
{
  "registry_lookup_response": {
    "message": "Resource retrieved successfully",
    "data": {
      "namespace_id": "namespace-id",
      "registry_id": "registry-id",
      "registry_name": "registry-name",
      "description": "Sample registry description",
      "tag": "custom",
      "schema": {},
      "created_by": "did:cord:creator",
      "genesis": "2026-01-01T00:00:00.000Z",
      "created_at": "2026-01-01T00:00:00.000Z",
      "updated_at": "2026-01-01T00:00:00.000Z",
      "state": "live",
      "meta": {},
      "record_count": 0,
      "version_count": 1,
      "version": "1",
      "query_allowed": true,
      "ttl": 3600,
      "digest": "paste-proof-digest",
      "proof": {
        "type": "DediRegistryProof2026",
        "namespace_did": "namespace-id",
        "registry_identifier": "registry-id",
        "creator_did": "did:cord:creator",
        "digest": "paste-proof-digest",
        "network_genesis": null
      }
    }
  }
}
```

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/verify-registry-lookup', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json',
    'Accept': 'application/json'
  },
  body: JSON.stringify({
    registry_lookup_response: registryLookupResponse
  })
});

const data = await response.json();
```

**Allowed Status Codes:**
- `200` - Verification completed
- `400` - Invalid verification payload
- `401` - Authentication failed
- `404` - Related resource or user not found
- `500` - Internal server error

## Verify Record Lookup

Verifies a record lookup response object and stores a verification log.

**Endpoint:** `POST /dedi/verify-record-lookup`

**Request Body:**
```json
{
  "record_lookup_response": {
    "message": "Resource retrieved successfully",
    "data": {
      "namespace": "Sample Namespace",
      "namespace_id": "namespace-id",
      "registry_id": "registry-id",
      "registry_name": "registry-name",
      "record_id": "record-id",
      "record_name": "record-name",
      "description": "Sample record description",
      "digest": "paste-proof-digest",
      "schema": {},
      "version_count": 1,
      "version": "1",
      "details": {},
      "meta": {},
      "genesis": "2026-01-01T00:00:00.000Z",
      "created_at": "2026-01-01T00:00:00.000Z",
      "updated_at": "2026-01-01T00:00:00.000Z",
      "created_by": "did:cord:creator",
      "state": "live",
      "ttl": 3600,
      "proof": {
        "type": "DediRecordProof2026",
        "namespace_did": "namespace-id",
        "registry_identifier": "registry-id",
        "record_identifier": "record-id",
        "creator_did": "did:cord:creator",
        "digest": "paste-proof-digest",
        "network_genesis": "paste-network-genesis"
      }
    }
  }
}
```

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/verify-record-lookup', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json',
    'Accept': 'application/json'
  },
  body: JSON.stringify({
    record_lookup_response: recordLookupResponse
  })
});

const data = await response.json();
```

**Allowed Status Codes:**
- `200` - Verification completed
- `400` - Invalid verification payload
- `401` - Authentication failed
- `404` - Related resource or user not found
- `500` - Internal server error

## Get Namespace Verification Logs

Returns namespace lookup verification logs.

**Endpoint:** `GET /dedi/{namespace}/get-namespace-verification-logs`

**Path Parameters:**
- `namespace` (required): Namespace ID or verified domain

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/acme/get-namespace-verification-logs', {
  method: 'GET',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Accept': 'application/json'
  }
});

const data = await response.json();
```

**Success Response Requirements In Collection:**
- `message`
- `data.logs`

**Allowed Status Codes:**
- `200` - Logs returned
- `400` - Invalid input
- `401` - Authentication failed
- `500` - Internal server error

## Get Registry Verification Logs

Returns registry lookup verification logs.

**Endpoint:** `GET /dedi/{namespace}/{registry_name}/get-registry-verification-logs`

**Path Parameters:**
- `namespace` (required): Namespace ID or verified domain
- `registry_name` (required): Registry name

**Success Response Requirements In Collection:**
- `message`
- `data.logs`

**Allowed Status Codes:**
- `200` - Logs returned
- `400` - Invalid input
- `401` - Authentication failed
- `404` - Namespace or registry not found
- `500` - Internal server error

## Get Record Verification Logs

Returns record lookup verification logs.

**Endpoint:** `GET /dedi/{namespace}/{registry_name}/{record_name}/get-record-verification-logs`

**Path Parameters:**
- `namespace` (required): Namespace ID or verified domain
- `registry_name` (required): Registry name
- `record_name` (required): Record name

**Success Response Requirements In Collection:**
- `message`
- `data.logs`

**Allowed Status Codes:**
- `200` - Logs returned
- `400` - Invalid input
- `401` - Authentication failed
- `404` - Namespace, registry, or record not found
- `500` - Internal server error

## Notes

- Verification APIs operate on prior lookup response payloads rather than raw identifiers alone.
- The collection treats successful verification as a normal `200` response with a `message` field.
- Each verification request also stores a verification log that can be retrieved through the corresponding log endpoint.
