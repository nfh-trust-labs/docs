# State Management APIs

DeDi.global provides comprehensive state management capabilities for both registries and records, allowing you to control the lifecycle and availability of your data through well-defined state transitions. All state changes are versioned and tracked on the blockchain, ensuring complete auditability.

**Authentication**: 🔒 All State Management APIs require authentication via API key.

## Registry State Management

### Suspend Registry

Temporarily suspends a registry, making it unavailable for queries and record operations while preserving all data. You can still lookup the registry. This is a **reversible** operation that can be undone using the reinstate endpoint.

**Endpoint:** `POST /dedi/{namespace}/{registry_name}/suspend-registry`

**Parameters:**
- `namespace` (path, required): Namespace ID or verified domain
- `registry_name` (path, required): Registry name to suspend

**Request Body:**
No request body required.

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/acme-corp/products/suspend-registry', {
  method: 'POST',
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
  "message": "Registry has been suspended"
}
```

**Error Responses:**
- `400` - Invalid input parameters
- `403` - Insufficient privileges to perform this action
- `404` - Namespace or registry not found
- `500` - Internal server error

**State Transition Rules:**
- ✅ `LIVE` → `SUSPENDED` (Allowed)
- ❌ `SUSPENDED` → `SUSPENDED` (Registry already suspended)
- ❌ `REVOKED` → `SUSPENDED` (Revoked registries cannot be suspended)
- ❌ `EXPIRED` → `SUSPENDED` (Expired registries cannot be suspended)

### Reinstate Registry

Reactivates a suspended registry, restoring it to live status and making it available for all operations.

**Endpoint:** `POST /dedi/{namespace}/{registry_name}/reinstate-registry`

**Parameters:**
- `namespace` (path, required): Namespace ID or verified domain
- `registry_name` (path, required): Registry name to reinstate

**Request Body:**
No request body required.

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/acme-corp/products/reinstate-registry', {
  method: 'POST',
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
  "message": "Registry has been reinstated"
}
```

**Error Responses:**
- `400` - Invalid input parameters
- `403` - Insufficient privileges to perform this action
- `404` - Namespace or registry not found
- `500` - Internal server error

**State Transition Rules:**
- ✅ `SUSPENDED` → `LIVE` (Allowed)
- ❌ `LIVE` → `LIVE` (Registry already active)
- ❌ `REVOKED` → `LIVE` (Revoked registries cannot be reinstated)
- ❌ `EXPIRED` → `LIVE` (Expired registries cannot be reinstated)

### Revoke Registry

Permanently revokes a registry, making it completely unavailable for all operations. You can still lookup the registry. This is an **irreversible** operation that cannot be undone.

**Endpoint:** `POST /dedi/{namespace}/{registry_name}/revoke-registry`

**Parameters:**
- `namespace` (path, required): Namespace ID or verified domain
- `registry_name` (path, required): Registry name to revoke

**Request Body:**
No request body required.

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/acme-corp/products/revoke-registry', {
  method: 'POST',
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
  "message": "Registry has been revoked"
}
```

**Error Responses:**
- `400` - Invalid input parameters
- `403` - Insufficient privileges to perform this action
- `404` - Namespace or registry not found
- `500` - Internal server error

**State Transition Rules:**
- ✅ `LIVE` → `REVOKED` (Allowed)
- ❌ `SUSPENDED` → `REVOKED` (Suspended registries cannot be revoked)
- ❌ `REVOKED` → `REVOKED` (Registry already revoked)
- ❌ `EXPIRED` → `REVOKED` (Expired registries cannot be revoked)

## Record State Management

### Suspend Record

Temporarily suspends a record, making it unavailable for queries while preserving all data. You can still lookup the record. This is a **reversible** operation.

**Endpoint:** `POST /dedi/{namespace}/{registry_name}/{record_name}/suspend-record`

**Parameters:**
- `namespace` (path, required): Namespace ID or verified domain
- `registry_name` (path, required): Registry name containing the record
- `record_name` (path, required): Record name to suspend

**Request Body:**
No request body required.

**Prerequisites:**
- Registry must be in `LIVE` state

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/acme-corp/products/laptop-001/suspend-record', {
  method: 'POST',
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
  "message": "Record has been suspended"
}
```

**Error Responses:**
- `400` - Invalid input parameters
- `403` - Insufficient privileges to perform this action
- `404` - Namespace, registry, or record not found
- `500` - Internal server error

**State Transition Rules:**
- ✅ `LIVE` → `SUSPENDED` (Allowed)
- ✅ `DRAFT` → `SUSPENDED` (Allowed)
- ❌ `SUSPENDED` → `SUSPENDED` (Record already suspended)
- ❌ `REVOKED` → `SUSPENDED` (Revoked records cannot be suspended)
- ❌ `EXPIRED` → `SUSPENDED` (Expired records cannot be suspended)

### Reinstate Record

Reactivates a suspended record, restoring it to live status and making it available for queries. You can still lookup the record.

**Endpoint:** `POST /dedi/{namespace}/{registry_name}/{record_name}/reinstate-record`

**Parameters:**
- `namespace` (path, required): Namespace ID or verified domain
- `registry_name` (path, required): Registry name containing the record
- `record_name` (path, required): Record name to reinstate

**Request Body:**
No request body required.

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/acme-corp/products/laptop-001/reinstate-record', {
  method: 'POST',
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
  "message": "Record has been reinstated"
}
```

**Error Responses:**
- `400` - Invalid input parameters
- `403` - Insufficient privileges to perform this action
- `404` - Namespace, registry, or record not found
- `500` - Internal server error

**State Transition Rules:**
- ✅ `SUSPENDED` → `LIVE` (Allowed)
- ❌ `LIVE` → `LIVE` (Record already active)
- ❌ `DRAFT` → `LIVE` (Use publish endpoint instead)
- ❌ `REVOKED` → `LIVE` (Revoked records cannot be reinstated)
- ❌ `EXPIRED` → `LIVE` (Expired records cannot be reinstated)

### Revoke Record

Permanently revokes a record, making it completely unavailable for all operations. You can still lookup the record. This is an **irreversible** operation.

**Endpoint:** `POST /dedi/{namespace}/{registry_name}/{record_name}/revoke-record`

**Parameters:**
- `namespace` (path, required): Namespace ID or verified domain
- `registry_name` (path, required): Registry name containing the record
- `record_name` (path, required): Record name to revoke

**Request Body:**
No request body required.

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/acme-corp/products/laptop-001/revoke-record', {
  method: 'POST',
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
  "message": "Record has been revoked"
}
```

**Error Responses:**
- `400` - Invalid input parameters
- `403` - Insufficient privileges to perform this action
- `404` - Namespace, registry, or record not found
- `500` - Internal server error

**State Transition Rules:**
- ✅ `LIVE` → `REVOKED` (Allowed)
- ✅ `DRAFT` → `REVOKED` (Allowed)
- ✅ `SUSPENDED` → `REVOKED` (Allowed)
- ❌ `REVOKED` → `REVOKED` (Record already revoked)
- ❌ `EXPIRED` → `REVOKED` (Expired records cannot be revoked)

## State Transition Matrix

### Registry States

| Current State | Suspend | Reinstate | Revoke |
|---------------|---------|-----------|---------|
| **LIVE**      | ✅ → SUSPENDED | ❌ Already Active | ✅ → REVOKED |
| **SUSPENDED** | ❌ Already Suspended | ✅ → LIVE | ❌ Not Allowed |
| **REVOKED**   | ❌ Not Allowed | ❌ Not Allowed | ❌ Already Revoked |
| **EXPIRED**   | ❌ Not Allowed | ❌ Not Allowed | ❌ Not Allowed |

### Record States

| Current State | Suspend | Reinstate | Revoke |
|---------------|---------|-----------|---------|
| **LIVE**      | ✅ → SUSPENDED | ❌ Already Active | ✅ → REVOKED |
| **DRAFT**     | ✅ → SUSPENDED | ❌ Use Publish | ✅ → REVOKED |
| **SUSPENDED** | ❌ Already Suspended | ✅ → LIVE | ✅ → REVOKED |
| **REVOKED**   | ❌ Not Allowed | ❌ Not Allowed | ❌ Already Revoked |
| **EXPIRED**   | ❌ Not Allowed | ❌ Not Allowed | ❌ Not Allowed |

**Note:** Record state management is dependent on the parent registry state. Records can only be managed when the parent registry is in `LIVE` state.

## Reversibility Guide

### Reversible Operations
These operations can be undone:

- **Suspend Registry**: Can be reversed with reinstate
- **Suspend Record**: Can be reversed with reinstate

**Example:**
```typescript
// Suspend and then reinstate a registry
const suspendResponse = await fetch('https://api.dedi.global/dedi/acme-corp/products/suspend-registry', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  }
});

const reinstateResponse = await fetch('https://api.dedi.global/dedi/acme-corp/products/reinstate-registry', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  }
});
```

### Irreversible Operations
These operations **cannot** be undone:

- **Revoke Registry**: Permanently removes registry access
- **Revoke Record**: Permanently removes record access

> ⚠️ **Warning**: Always ensure you want to permanently revoke access before calling revoke endpoints, as these changes cannot be reversed.

## Use Cases & Examples

### Temporary Maintenance

When performing maintenance on your data infrastructure:

```typescript
// 1. Suspend registry to prevent new operations
const suspendResponse = await fetch('https://api.dedi.global/dedi/acme-corp/products/suspend-registry', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  }
});

// 2. Perform maintenance tasks
// ...

// 3. Reinstate registry to resume operations
const reinstateResponse = await fetch('https://api.dedi.global/dedi/acme-corp/products/reinstate-registry', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  }
});
```

### Data Quality Issues

When discovering data quality problems in specific records:

```typescript
// 1. Suspend problematic record immediately
const suspendResponse = await fetch('https://api.dedi.global/dedi/acme-corp/products/defective-item/suspend-record', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  }
});

// 2. Fix the underlying data issue
// ...

// 3. Reinstate the corrected record
const reinstateResponse = await fetch('https://api.dedi.global/dedi/acme-corp/products/defective-item/reinstate-record', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  }
});
```

### Security Incidents

When dealing with compromised or fraudulent data:

```typescript
// Permanently revoke compromised record
const revokeResponse = await fetch('https://api.dedi.global/dedi/acme-corp/products/compromised-item/revoke-record', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  }
});
```

## Webhook Notifications

State management operations trigger webhook notifications to registered watchers:

### Registry State Changes
- `registry.suspended` - Triggered when a registry is suspended
- `registry.reinstated` - Triggered when a registry is reinstated  
- `registry.revoked` - Triggered when a registry is revoked

### Record State Changes
- `record.suspended` - Triggered when a record is suspended
- `record.reinstated` - Triggered when a record is reinstated
- `record.revoked` - Triggered when a record is revoked

**Example Webhook Payload:**

```typescript
{
  "event": "registry.suspended",
  "timestamp": "2024-12-19T10:30:00Z",
  "data": {
    "namespace": "acme-corp",
    "registry_name": "products",
    "registry_id": "reg_123456789",
    "tag": "latest",
    "previous_state": "live",
    "current_state": "suspended"
  }
}
```
## Error Handling

### Common Error Responses

**404 - Resource Not Found:**
```typescript
{
  "message": "Registry not found"
}
```

**403 - Insufficient Privileges:**
```typescript
{
  "message": "Not enough privileges to perform this action"
}
```

**400 - Invalid State Transition:**
```typescript
{
  "message": "Suspended registry cannot be revoked"
}
```

**401 - Authentication Failed:**
```typescript
{
  "message": "Authentication failed"
}
```

## Best Practices

### 1. Plan State Changes
- Always communicate planned suspensions to stakeholders
- Use suspend/reinstate for temporary issues
- Reserve revoke for permanent removal only

### 2. Monitor Dependencies
- Check downstream systems before suspending registries
- Understand the impact on dependent records
- Consider gradual rollouts for large registries

### 3. Use Webhooks
- Set up webhook monitoring for state changes
- Implement automated alerts for unexpected state transitions
- Track state change patterns for audit purposes

### 4. Document Reasons
- Maintain external logs explaining why state changes occurred
- Include correlation IDs for tracking related operations
- Document business justification for irreversible changes

<!-- ## Rate Limits

State management operations have the following rate limits:

| Operation | Rate Limit |
|-----------|------------|
| Suspend   | 100 requests per minute |
| Reinstate | 100 requests per minute |
| Revoke    | 50 requests per minute |

> Rate limits are applied per API key and are designed to prevent accidental mass state changes while allowing legitimate operational needs. -->
