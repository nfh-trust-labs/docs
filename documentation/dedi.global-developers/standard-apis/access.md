# Access APIs

The Access APIs provide comprehensive data retrieval capabilities for the DeDi.global platform. These read-only endpoints allow you to lookup, query, and explore namespaces, registries, and records with powerful filtering and version management features.

🔒 **Authentication Required**: All Access APIs require authentication using your API key in the Authorization header as a Bearer token.

## Overview

Access APIs are organized into three main categories:

- **Lookup APIs** - Retrieve detailed information about specific resources
- **Query APIs** - Search and filter collections of resources with pagination
- **Version APIs** - Manage and retrieve historical versions of resources

All access APIs require authentication and support both current and historical data queries.

## Lookup APIs

Lookup APIs provide detailed information about specific resources using their identifiers.

### Namespace Lookup

Retrieve detailed information about a specific namespace.

**Endpoint:** `GET /dedi/lookup/{namespace}`

**Parameters:**
- `namespace` (path, required): Namespace ID or verified domain
- `version_id` (query, optional): Specific version to retrieve
- `as_on` (query, optional): Historical lookup date (YYYY-MM-DD format)

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/lookup/my-namespace', {
  method: 'GET',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  }
});

const data = await response.json();
```

**Success Response (200):**
```typescript
{
  "message": "Namespace details retrieved successfully",
  "data": {
    "domain": "example.com",
    "name": "my-namespace",
    "namespace_id": "did:cord:3yVazTQMc...",
    "digest": "0x123abc...",
    "description": "Production namespace for example.com services",
    "created_by": "did:cord:3xMxFkzY...",
    "genesis": "0x456def...",
    "created_at": "2024-01-01T00:00:00Z",
    "updated_at": "2024-01-01T00:00:00Z",
    "version": "0x789ghi...",
    "version_count": 3,
    "is_verified": true,
    "meta": {
      "category": "production",
      "region": "global"
    },
    "registry_count": 15,
    "ttl": 86400,
    "proof": {
      "type": "DediNamespaceProof2026",
      "namespace_did": "did:cord:3yVazTQMc...",
      "creator_did": "did:cord:3xMxFkzY...",
      "digest": "0x123abc...",
      "network_genesis": "0x101112..."
    }
  }
}
```

**Error Responses:**
- `400` - Invalid namespace parameter or date format
- `404` - Namespace not found or version not found
- `500` - Internal server error

### Registry Lookup

Retrieve detailed information about a specific registry.

**Endpoint:** `GET /dedi/lookup/{namespace}/{registry_name}`

**Parameters:**
- `namespace` (path, required): Namespace ID or domain
- `registry_name` (path, required): Registry name
- `version_id` (query, optional): Specific version to retrieve
- `as_on` (query, optional): Historical lookup date (YYYY-MM-DD format)

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/lookup/my-namespace/user-registry', {
  method: 'GET',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  }
});

const data = await response.json();
```

**Success Response (200):**
```typescript
{
  "message": "Resource retrieved successfully",
  "data": {
    "namespace": "my-namespace",
    "namespace_id": "did:cord:3yVazTQMc...",
    "registry_id": "did:cord:3zAbcDef...",
    "registry_name": "user-registry",
    "digest": "0x456abc...",
    "description": "User management registry for authentication",
    "created_by": "did:cord:3xMxFkzY...",
    "tag": "membership",
    "schema": {
      "type": "object",
      "properties": {
        "email": { "type": "string", "format": "email" },
        "role": { "type": "string", "enum": ["admin", "user"] },
        "active": { "type": "boolean" }
      },
      "required": ["email", "role"]
    },
    "genesis": "0x789def...",
    "created_at": "2024-01-01T00:00:00Z",
    "updated_at": "2024-01-01T00:00:00Z",
    "state": "live",
    "meta": {
      "department": "engineering",
      "priority": "high"
    },
    "record_count": 150,
    "version_count": 2,
    "version": "0x012ghi...",
    "query_allowed": true,
    "ttl": 3600,
    "proof": {
      "type": "DediRegistryProof2026",
      "namespace_did": "did:cord:3yVazTQMc...",
      "registry_identifier": "did:cord:3zAbcDef...",
      "creator_did": "did:cord:3xMxFkzY...",
      "digest": "0x456abc...",
      "network_genesis": "0x101112..."
    }
  }
}
```

> **Important**: A `200` response indicates successful data retrieval, not that the registry is active. Always check the `state` field to verify the registry's current status before using it.

**Error Responses:**
- `400` - Missing namespace or registry name
- `404` - Namespace or registry not found
- `500` - Internal server error

### Record Lookup

Retrieve detailed information about a specific record.

**Endpoint:** `GET /dedi/lookup/{namespace}/{registry_name}/{record_name}`

**Parameters:**
- `namespace` (path, required): Namespace ID or domain
- `registry_name` (path, required): Registry name
- `record_name` (path, required): Record name
- `version_id` (query, optional): Specific version to retrieve
- `as_on` (query, optional): Historical lookup date (YYYY-MM-DD format)

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/lookup/my-namespace/user-registry/john-doe', {
  method: 'GET',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  }
});

const data = await response.json();
```

**Success Response (200):**
```typescript
{
  "message": "Resource retrieved successfully",
  "data": {
    "namespace": "my-namespace",
    "namespace_id": "did:cord:3yVazTQMc...",
    "registry_id": "did:cord:3zAbcDef...",
    "registry_name": "user-registry",
    "registry_state": "live",
    "record_id": "did:cord:3aRecDef...",
    "record_name": "john-doe",
    "description": "User record for John Doe",
    "digest": "0x789abc...",
    "schema": {
      "type": "object",
      "properties": {
        "email": { "type": "string", "format": "email" },
        "role": { "type": "string", "enum": ["admin", "user"] }
      }
    },
    "version_count": 3,
    "version": "0x345ghi...",
    "details": {
      "email": "john.doe@example.com",
      "role": "admin",
      "active": true,
      "last_login": "2024-01-01T10:30:00Z"
    },
    "meta": {
      "department": "engineering",
      "access_level": "high"
    },
    "genesis": "2024-01-01T00:00:00.000Z",
    "created_at": "2024-01-01T00:00:00Z",
    "updated_at": "2024-01-01T00:00:00Z",
    "created_by": "did:cord:3xMxFkzY...",
    "state": "live",
    "ttl": 86400,
    "proof": {
      "type": "DediRecordProof2026",
      "namespace_did": "did:cord:3yVazTQMc...",
      "registry_identifier": "did:cord:3zAbcDef...",
      "record_identifier": "did:cord:3aRecDef...",
      "creator_did": "did:cord:3xMxFkzY...",
      "digest": "0x789abc...",
      "network_genesis": "0x101112..."
    }
  }
}
```

**Expired Record Response (200):**
```typescript
{
  "message": "Record has been expired",
  "data": {
    // Same structure as above but with state: "expired"
  }
}
```

> **Important**: A `200` response indicates successful data retrieval, not that the record is active. Always check the `state` field to verify the record's current status before using it.

**Error Responses:**
- `400` - Missing required parameters
- `404` - Namespace, registry, or record not found
- `500` - Internal server error

## Query APIs

Query APIs enable searching and filtering collections with pagination support.

### Query Registries by Namespace

List all registries within a namespace with filtering options.

**Endpoint:** `GET /dedi/query/{namespace}`

**Parameters:**
- `namespace` (path, required): Namespace ID or domain
- `from` (query, optional): Filter from date (YYYY-MM-DD)
- `to` (query, optional): Filter to date (YYYY-MM-DD)
- `status` (query, optional): Filter by status (`revoked`, `suspended`)
- `name` (query, optional): Search by registry name (minimum 3 characters)
- `sort` (query, optional): Sort by `date`, `status`, `name`
- `page` (query, optional): Page number for pagination
- `page_size` (query, optional): Items per page
- `as_on` (query, optional): Historical query date (YYYY-MM-DD)

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/query/my-namespace?sort=date&page=1&page_size=10', {
  method: 'GET',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  }
});

const data = await response.json();
```

**Success Response (200):**
```typescript
{
  "message": "Resource retrieved successfully",
  "data": {
    "namespace_id": "did:cord:3yVazTQMc...",
    "namespace_name": "my-namespace",
    "domain": "my-namespace.dedi.global",
    "created_by": "did:cord:3xMxFkzY...",
    "created_at": "2024-01-01T00:00:00Z",
    "updated_at": "2024-01-01T00:00:00Z",
    "total_registries": 25,
    "page_number": 1,
    "page_size": 10,
    "registries": [
      {
        "registry_id": "did:cord:3zAbcDef...",
        "registry_name": "user-registry",
        "description": "Registry for user profiles",
        "digest": "0x456def...",
        "tag": "users",
        "schema": {
          "type": "object",
          "properties": {
            "email": { "type": "string", "format": "email" }
          }
        },
        "genesis": "2024-01-01T00:00:00.000Z",
        "created_at": "2024-01-01T00:00:00Z",
        "updated_at": "2024-01-01T00:00:00Z",
        "created_by": "did:cord:3xMxFkzY...",
        "state": "live",
        "meta": {
          "category": "identity",
          "version": "v1.0"
        },
        "record_count": 150,
        "version_count": 3,
        "version": "0x789abc...",
        "query_allowed": true,
        "ttl": 86400
      }
    ]
  }
}
```

**Error Responses:**
- `404` - Namespace not found
- `500` - Internal server error

### Query Records by Registry

List all records within a registry with filtering options.

**Endpoint:** `GET /dedi/query/{namespace}/{registry_name}`

**Parameters:**
- `namespace` (path, required): Namespace ID or domain
- `registry_name` (path, required): Registry name
- `from` (query, optional): Filter from date (YYYY-MM-DD)
- `to` (query, optional): Filter to date (YYYY-MM-DD)
- `state` (query, optional): Filter by state (`revoked`, `suspended`, `expired`)
- `name` (query, optional): Search by record name (minimum 3 characters)
- `sort` (query, optional): Sort by `date`, `state`, `name`
- `page` (query, optional): Page number for pagination
- `page_size` (query, optional): Items per page
- `as_on` (query, optional): Historical query date (YYYY-MM-DD)

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/query/my-namespace/user-registry?state=live&sort=name', {
  method: 'GET',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  }
});

const data = await response.json();
```

**Success Response (200):**
```typescript
{
  "message": "Resource retrieved successfully",
  "data": {
    "namespace_id": "did:cord:3yVazTQMc...",
    "namespace_name": "my-namespace",
    "registry_name": "user-registry",
    "registry_id": "did:cord:3zAbcDef...",
    "schema": {
      "type": "object",
      "properties": {
        "email": { "type": "string", "format": "email" },
        "role": { "type": "string", "enum": ["admin", "user"] }
      }
    },
    "meta": {
      "category": "identity",
      "access_level": "public"
    },
    "created_by": "did:cord:3xMxFkzY...",
    "created_at": "2024-01-01T00:00:00Z",
    "updated_at": "2024-01-01T00:00:00Z",
    "total_records": 150,
    "total_pages": 15,
    "page_number": 1,
    "page_size": 10,
    "records": [
      {
        "record_id": "did:cord:3aRecDef...",
        "record_name": "john-doe",
        "description": "User record for John Doe",
        "digest": "0x789abc...",
        "details": {
          "email": "john.doe@example.com",
          "role": "admin",
          "active": true
        },
        "genesis": "2024-01-01T00:00:00.000Z",
        "created_at": "2024-01-01T00:00:00Z",
        "updated_at": "2024-01-01T00:00:00Z",
        "created_by": "did:cord:3xMxFkzY...",
        "state": "live",
        "version_count": 2,
        "version": "0x345ghi...",
        "ttl": 86400,
        "meta": {
          "department": "engineering",
          "access_level": "high"
        }
      }
    ]
  }
}
```

**Error Responses:**
- `404` - Registry or namespace not found
- `500` - Internal server error

### Query Registries by Schema Tag

Find registries by their schema tag across the platform.

**Endpoint:** `GET /dedi/{tag}/get-registry-by-tag`

**Parameters:**
- `tag` (path, required): Schema tag to filter by (e.g., `membership`, `public_key`, `revoke`)

> **Note:** The tag cannot be `custom` as this endpoint is specifically for standardized schemas.

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/membership/get-registry-by-tag', {
  method: 'GET',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  }
});

const data = await response.json();
```

**Success Response (200):**
```typescript
{
  "message": "Resource retrieved successfully",
  "data": [
    {
      "registry_id": "did:cord:3zAbcDef...",
      "registry_name": "employee-registry",
      "namespace_id": "did:cord:3yVazTQMc...",
      "description": "Employee membership registry",
      "digest": "0x456def...",
      "tag": "membership",
      "schema": {
        "type": "object",
        "properties": {
          "email": { "type": "string", "format": "email" },
          "role": { "type": "string" },
          "department": { "type": "string" }
        }
      },
      "genesis": "2024-01-01T00:00:00.000Z",
      "created_at": "2024-01-01T00:00:00Z",
      "updated_at": "2024-01-01T00:00:00Z",
      "created_by": "did:cord:3xMxFkzY...",
      "state": "live",
      "meta": {
        "category": "membership",
        "version": "v2.0"
      },
      "record_count": 50,
      "version_count": 3,
      "version": "0x789abc...",
      "query_allowed": true,
      "ttl": 86400
    }
  ]
}
```

**Error Responses:**
- `400` - Invalid tag (cannot be 'custom' or missing)
- `500` - Internal server error

## Version APIs

Version APIs provide access to historical versions and evolution tracking for all resources.

### Namespace Versions

Retrieve all available versions for a namespace.

**Endpoint:** `GET /dedi/versions/{namespace}`

**Parameters:**
- `namespace` (path, required): Namespace ID or domain

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/versions/my-namespace', {
  method: 'GET',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  }
});

const data = await response.json();
```

**Success Response (200):**
```typescript
{
  "message": "Resource retrieved successfully",
  "data": {
    "created_by": "did:cord:3xMxFkzY...",
    "created_at": "2024-01-01T00:00:00Z",
    "updated_at": "2024-01-01T00:00:00Z",
    "total_versions": 5,
    "versions": [
      "0x789ghi...", // Latest
      "0x456def...",
      "0x123abc...", // Oldest
    ],
    "ttl": 86400
  }
}
```

### Registry Versions

Retrieve all available versions for a registry.

**Endpoint:** `GET /dedi/versions/{namespace}/{registry_name}`

**Parameters:**
- `namespace` (path, required): Namespace ID or domain
- `registry_name` (path, required): Registry name

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/versions/my-namespace/user-registry', {
  method: 'GET',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  }
});

const data = await response.json();
```

**Success Response (200):**
```typescript
{
  "message": "Resource retrieved successfully",
  "data": {
    "registry_name": "user-registry",
    "created_by": "did:cord:3xMxFkzY...",
    "schema": {
      "type": "object",
      "properties": {
        "email": { "type": "string", "format": "email" },
        "role": { "type": "string" }
      }
    },
    "created_at": "2024-01-01T00:00:00Z",
    "updated_at": "2024-01-01T00:00:00Z",
    "total_versions": 3,
    "versions": [
      "0x345ghi...", // Latest
      "0x012def...",
      "0x789abc..."  // Oldest
    ],
    "ttl": 3600
  }
}
```

### Record Versions

Retrieve all available versions for a record.

**Endpoint:** `GET /dedi/versions/{namespace}/{registry_name}/{record_name}`

**Parameters:**
- `namespace` (path, required): Namespace ID or domain
- `registry_name` (path, required): Registry name
- `record_name` (path, required): Record name

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/versions/my-namespace/user-registry/john-doe', {
  method: 'GET',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  }
});

const data = await response.json();
```

**Success Response (200):**
```typescript
{
  "message": "Resource retrieved successfully",
  "data": {
    "created_by": "did:cord:3xMxFkzY...",
    "schema": {
      "type": "object",
      "properties": {
        "email": { "type": "string", "format": "email" },
        "role": { "type": "string" }
      }
    },
    "created_at": "2024-01-01T00:00:00Z",
    "updated_at": "2024-01-01T00:00:00Z",
    "total_versions": 4,
    "versions": [
      "0x567jkl...", // Latest
      "0x345ghi...",
      "0x123def...",
      "0x901abc..."  // Oldest
    ],
    "ttl": 86400
  }
}
```

## Error Handling

All Access APIs follow consistent error response patterns:

### Common Error Responses

**400 Bad Request:**
```typescript
{
  "message": "Invalid input: namespace is missing or empty"
}
```

**401 Unauthorized:**
```typescript
{
  "message": "Unauthorized: Invalid or missing authentication token"
}
```

**404 Not Found:**
```typescript
{
  "message": "Namespace not found"
}
```

**500 Internal Server Error:**
```typescript
{
  "message": "An unexpected error occurred"
}
```

<!-- ## Rate Limiting

Access APIs are subject to rate limiting to ensure fair usage:

- **Default limit:** 1000 requests per hour per API key
- **Burst limit:** 100 requests per minute
- **Historical queries:** Limited to 100 requests per hour per API key

Rate limit headers are included in all responses:
- `X-RateLimit-Limit`: Total requests allowed
- `X-RateLimit-Remaining`: Requests remaining in current window
- `X-RateLimit-Reset`: Unix timestamp when limit resets -->

## Best Practices

### Performance Optimization

1. **Use specific lookups** when you know exact resource identifiers
2. **Implement pagination** for large result sets
3. **Cache frequently accessed** namespace and registry data
4. **Use historical queries sparingly** as they are more resource-intensive

### Query Efficiency

1. **Apply filters** to reduce result set size
2. **Use date ranges** for time-based queries
3. **Sort consistently** to improve cache performance
4. **Implement client-side caching** for static data

### Version Management

1. **Track version counts** to detect updates
2. **Use version_id** for reproducible results
3. **Cache version lists** as they change infrequently
4. **Implement version-aware** client logic

## Examples and Use Cases

### Real-time Data Synchronization

```typescript
// Check for updates and sync data
async function syncNamespaceData(namespaceId: string) {
  const current = await lookup.namespace(namespaceId);
  const cached = getCachedVersion(namespaceId);
  
  if (current.version_count > cached.version_count) {
    // Fetch and cache updated data
    await updateCache(namespaceId, current);
    
    // Sync registries if needed
    const registries = await query.registriesByNamespace(namespaceId);
    await syncRegistries(registries.data.registries);
  }
}
```

### Advanced Search Implementation

```typescript
// Multi-criteria registry search
async function searchRegistries(criteria: SearchCriteria) {
  const results = await query.registriesByNamespace(criteria.namespace, {
    from: criteria.dateRange.start,
    to: criteria.dateRange.end,
    name: criteria.searchTerm,
    sort: 'date',
    page: criteria.page,
    page_size: 20
  });
  
  // Filter by tag if specified
  if (criteria.schemaTag) {
    const taggedRegistries = await query.registriesByTag(criteria.schemaTag);
    return filterAndMerge(results.data, taggedRegistries.data);
  }
  
  return results.data;
}
```

### Historical Analysis

```typescript
// Analyze resource evolution over time
async function analyzeResourceHistory(namespace: string, registry: string) {
  const versions = await version.registry(namespace, registry);
  const analysis = {
    totalVersions: versions.data.total_versions,
    evolutionTimeline: [],
    schemaChanges: []
  };
  
  // Fetch each version for comparison
  for (const versionId of versions.data.versions) {
    const versionData = await lookup.registry(namespace, registry, { version_id: versionId });
    analysis.evolutionTimeline.push({
      version: versionId,
      timestamp: versionData.data.updated_at,
      changes: compareSchemas(versionData.data.schema)
    });
  }
  
  return analysis;
}
```
