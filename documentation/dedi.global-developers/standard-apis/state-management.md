# State Management APIs

DeDi.global manages resource lifecycle through explicit namespace, registry, and record states, plus dedicated deletion and recovery APIs.

## State Model

| Entity | Supported states | Notes |
| --- | --- | --- |
| Namespace | `live` | A namespace remains `live` until it is deleted. |
| Registry | `live`, `inactive` | Inactive registries remain versioned and restorable. |
| Record | `draft`, `live` | Draft records are private working versions. Published records are `live`. |

## Transition Rules

### Namespace

- New namespaces are created in the `live` state.
- A namespace does not transition to another active state.
- Namespace removal is handled through the deletion and restore APIs.

### Registry

- New registries are created in the `live` state.
- `POST /dedi/{namespace}/{registry_name}/inactivate-registry` creates a new registry version in the `inactive` state.
- `POST /dedi/{namespace}/{registry_name}/reactivate-registry` creates a new registry version in the `live` state.

### Record

- `POST /dedi/{namespace}/{registry_name}/save-record-as-draft` creates a draft record.
- `POST /dedi/{namespace}/{registry_name}/publish-records` publishes draft records to the `live` state.
- Updating a draft record edits the draft version.
- Updating a live record creates a new live version.

## Registry Activation APIs

### Inactivate Registry

**Endpoint:** `POST /dedi/{namespace}/{registry_name}/inactivate-registry`

**Request Body:** None

**Success Response (200):**

```json
{
  "message": "Registry has been inactivated"
}
```

### Reactivate Registry

**Endpoint:** `POST /dedi/{namespace}/{registry_name}/reactivate-registry`

**Request Body:** None

**Success Response (200):**

```json
{
  "message": "Registry has been reactivated"
}
```

## Deletion APIs

Deletion removes the active entity from lookup, query, version, and publish flows and places the deleted data into the recovery surface for a limited restore window.

### Delete Namespace

**Endpoint:** `DELETE /dedi/{namespace}/delete-namespace`

**Request Body:**

```json
{
  "reason": "Optional deletion reason"
}
```

**Success Response (200):**

```json
{
  "message": "Namespace deleted successfully",
  "data": {
    "namespace_id": "did:cord:...",
    "deleted_namespace_versions": 3,
    "deleted_registry_versions": 8,
    "deleted_record_versions": 42
  }
}
```

### Delete Registry

**Endpoint:** `DELETE /dedi/{namespace}/{registry_name}/delete-registry`

**Request Body:**

```json
{
  "reason": "Optional deletion reason"
}
```

**Success Response (200):**

```json
{
  "message": "Registry deleted successfully",
  "data": {
    "registry_name": "employee-profiles",
    "deleted_registry_versions": 4,
    "deleted_record_versions": 17
  }
}
```

### Delete Records

**Endpoint:** `DELETE /dedi/{namespace}/{registry_name}/delete-records`

**Request Body:**

```json
{
  "records": ["record-a", "record-b"],
  "reason": "Optional deletion reason"
}
```

**Success Response (200):**

```json
{
  "message": "Records deleted successfully",
  "data": {
    "count": 2,
    "deleted_record_versions": 5,
    "records": ["record-a", "record-b"]
  }
}
```

## Deleted Entity Listing APIs

These endpoints return the latest deleted snapshot that is still eligible for restoration.

### List Deleted Namespaces

**Endpoint:** `GET /dedi/deleted/namespaces`

**Success Response (200):**

```json
{
  "message": "Deleted namespaces fetched successfully",
  "data": [
    {
      "namespace_id": "did:cord:...",
      "name": "acme",
      "state": "live",
      "deleted_at": "2026-05-04T08:30:00.000Z",
      "deleted_by": "did:cord:profile:...",
      "delete_scope": "namespace",
      "reason": "Cleanup"
    }
  ]
}
```

### List Deleted Registries

**Endpoint:** `GET /dedi/deleted/registries`

**Success Response (200):**

```json
{
  "message": "Deleted registries fetched successfully",
  "data": [
    {
      "namespace_id": "did:cord:...",
      "registry_id": "did:cord:registry:...",
      "registry_name": "employee-profiles",
      "state": "inactive",
      "deleted_history_id": "did:cord:registry:...",
      "deleted_at": "2026-05-04T08:30:00.000Z",
      "deleted_by": "did:cord:profile:...",
      "delete_scope": "registry",
      "reason": "Cleanup"
    }
  ]
}
```

### List Deleted Records

**Endpoint:** `GET /dedi/deleted/records`

**Success Response (200):**

```json
{
  "message": "Deleted records fetched successfully",
  "data": [
    {
      "namespace_id": "did:cord:...",
      "registry_name": "employee-profiles",
      "record_id": "did:cord:record:...",
      "record_name": "john-doe",
      "state": "live",
      "deleted_history_id": "did:cord:record:...",
      "deleted_at": "2026-05-04T08:30:00.000Z",
      "deleted_by": "did:cord:profile:...",
      "delete_scope": "record",
      "reason": "Cleanup"
    }
  ]
}
```

## Restore APIs

### Restore Namespace

**Endpoint:** `POST /dedi/restore/namespace`

**Request Body:**

```json
{
  "namespace_id": "did:cord:..."
}
```

**Success Response (200):**

```json
{
  "message": "Namespace restored successfully",
  "data": {
    "namespace_id": "did:cord:...",
    "restored_namespace_versions": 3
  }
}
```

### Restore Registry

**Endpoint:** `POST /dedi/restore/registry`

**Request Body:**

```json
{
  "namespace_id": "did:cord:...",
  "registry_name": "employee-profiles",
  "deleted_history_id": "did:cord:registry:..."
}
```

**Success Response (200):**

```json
{
  "message": "Registry restored successfully",
  "data": {
    "namespace_id": "did:cord:...",
    "registry_name": "employee-profiles",
    "restored_registry_versions": 4
  }
}
```

**Important:** If multiple deleted histories exist for the same registry name, `deleted_history_id` is required.

### Restore Records

**Endpoint:** `POST /dedi/restore/records`

**Request Body:**

```json
{
  "records": [
    {
      "namespace_id": "did:cord:...",
      "registry_name": "employee-profiles",
      "record_name": "john-doe",
      "deleted_history_id": "did:cord:record:..."
    }
  ]
}
```

**Success Response (200):**

```json
{
  "message": "Records restored successfully",
  "data": {
    "restored_record_versions": 2,
    "records": [
      {
        "namespace_id": "did:cord:...",
        "registry_name": "employee-profiles",
        "record_name": "john-doe",
        "deleted_history_id": "did:cord:record:..."
      }
    ]
  }
}
```

**Important:** If multiple deleted histories exist for the same record name, `deleted_history_id` is required.

## Deleted Entity Retention Window

- Deleted namespaces, registries, and records remain restorable during the deleted-entity retention window.
- The current implementation keeps deleted entities in the recovery surface for a configurable retention period.
- The default retention period is `3` days.
- After the retention window expires, deleted entities are moved to permanent archival storage and can no longer be restored through the public APIs.

## Related APIs

- Publishing and draft-to-live flows: [Publish APIs](./publish.md)
- Versioned edits: [Update APIs](./update.md)
- Domain trust lifecycle: [Domain Verification APIs](./domain.md)
- Delegation and namespace ownership changes: [Delegation Management APIs](./delegation.md)
