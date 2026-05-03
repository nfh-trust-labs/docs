# Advanced Search APIs

Advanced Search provides a namespace-scoped search endpoint for records using standard filters and dynamic record-detail query parameters.

## Authentication

The current Postman collection marks Advanced Search as public.

## Search Records In Namespace

Search across records within a namespace using the built-in filters shown in the collection and additional detail-field query parameters supported by the backend.

**Endpoint:** `GET /dedi/search/{namespace}`

**Path Parameters:**
- `namespace` (required): Namespace ID or verified domain

**Documented Query Parameters In Collection:**
- `registry_name` (optional): Filter by registry
- `record_name` (optional): Filter by record name
- `email` (optional): Example dynamic detail-field filter shown in the collection

## How The Search Endpoint Is Intended To Be Used

The Postman collection shows a few representative query parameters, but the endpoint description is broader: it supports dynamic detail-field query params.

That means you can use:

- Built-in search keys shown directly in the collection
- Additional keys that correspond to fields inside record details

## Example Requests

Search by registry and record name:

```http
GET /dedi/search/acme?registry_name=employees&record_name=john-doe
```

Search with the example dynamic detail field shown in the collection:

```http
GET /dedi/search/acme?email=john@example.com
```

Combined search:

```http
GET /dedi/search/acme?registry_name=employees&record_name=john-doe&email=john@example.com
```

JavaScript example:

```typescript
const response = await fetch(
  'https://api.dedi.global/dedi/search/acme?registry_name=employees&record_name=john-doe&email=john@example.com',
  {
    method: 'GET',
    headers: {
      'Accept': 'application/json'
    }
  }
);

const data = await response.json();
```

## Response Shape

The collection expects a successful response to include:

- `message`
- `data`

Example response:

```json
{
  "message": "Search results",
  "data": [
    {
      "record_id": "record-id",
      "record_name": "john-doe",
      "registry_name": "employees",
      "namespace_id": "acme",
      "details": {
        "email": "john@example.com"
      }
    }
  ]
}
```

## Allowed Status Codes

- `200` - Search results returned
- `400` - Invalid search input
- `404` - Namespace not found
- `500` - Internal server error

## Notes

- The collection does not mark this endpoint as authenticated.
- Search is namespace-scoped through the `{namespace}` path parameter.
- The request shown in Postman is illustrative rather than exhaustive, so the safest collection-aligned guidance is to document the provided query keys and note that additional detail-field params are supported.
