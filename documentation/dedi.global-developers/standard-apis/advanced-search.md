# Advanced Search APIs

Advanced Search provides a namespace-scoped search endpoint for records using standard filters and dynamic record-detail query parameters.

## Authentication

The current Postman collection marks Advanced Search as public.

## Search Records in Namespace

Public search across namespace records using dynamic detail-field query params.

**Endpoint:** `GET /dedi/search/{namespace}`

**Path Parameters:**
- `namespace` (required): Namespace ID or verified domain

**Documented Query Parameters in Collection:**
- `registry_name` (optional): Filter by registry
- `record_name` (optional): Filter by record name
- `email` (optional): Example dynamic detail-field filter shown in the collection

**Dynamic Detail-Field Search:**
- The collection description explicitly supports dynamic detail-field query parameters.
- That means you can pass additional query keys that map to fields in record details, not just the sample `email` key shown in Postman.

**Example Request:**
```http
GET /dedi/search/acme?registry_name=employees&record_name=john-doe&email=john@example.com
```

**Allowed Status Codes:**
- `200` - Search results returned
- `400` - Invalid search input
- `404` - Namespace not found
- `500` - Internal server error

**Success Response Requirements in Collection:**
- `message`
- `data`

## Notes

- The collection does not mark this endpoint as authenticated.
- Search is namespace-scoped through the `{namespace}` path parameter.
- The Postman request only shows sample query parameters, so the safest collection-aligned guidance is to document those explicitly and note that additional detail-field params are supported.
