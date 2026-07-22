# State management

DeDi.global uses a compact lifecycle model for namespaces, registries, and records.

## Supported states

| Entity | States |
| --- | --- |
| Namespace | `live` |
| Registry | `live`, `inactive` |
| Record | `draft`, `live` |

## How the lifecycle works

- A namespace remains `live` until it is deleted.
- A registry can move between `live` and `inactive`.
- A record starts as `draft` and becomes `live` when published.
- Updating a live record creates a new live version.
- Deletion removes the entity from active access surfaces and moves it into the recovery surface.

## Delete and restore

- Use the deletion APIs to remove namespaces, registries, or records.
- Use the deleted-entity listing APIs to discover recoverable items.
- Use the restore APIs to recover deleted data within the retention window.
- The current implementation keeps deleted entities restorable for a configurable retention period with a default of `3` days.

### Deleting via the UI

Each entity type has a specific deletion flow in the publish UI:

#### Delete Namespace

1. Click **Delete** on a namespace card
2. A confirmation dialog appears with three options:
   - **Export Namespace** — downloads a `.zip` archive containing all registries and records. Use this to create a backup before deletion.
   - **Delete Namespace** — permanently deletes the namespace, including all registries and records. This action cannot be undone.
   - **Cancel** — closes the dialog without taking any action.

> **Tip:** Always export your namespace data before deletion if you may need it in the future.

#### Delete Registry

A two-step confirmation process:

1. **First dialog** — "Are you sure?" warning with three options:
   - **Export Registry** — downloads a CSV export of the registry and its records
   - **Delete** — proceeds to the confirmation step
   - **Cancel** — closes the dialog

2. **Second dialog** — Type the registry name to confirm deletion, then click **Confirm Delete**.

> **Note:** Deleted registries and records are moved to the recovery bin and can be restored within the retention window (default: 3 days).

#### Delete Record

A two-step confirmation process:

1. **First dialog** — "Are you sure?" warning with two options:
   - **Delete** — proceeds to the confirmation step
   - **Cancel** — closes the dialog

2. **Second dialog** — Type `DELETE-RECORD` to confirm deletion, then click **Confirm Delete**.

### Deleting via the API

Use the deletion endpoints to programmatically remove entities. Deleted entities are moved to a recovery surface and can be restored within the retention window (default: 3 days).

## Restoring Deleted Entities

Deleted entities can be restored from the **Recovery Bin** in the publish UI or via the restore APIs.

### Recovery Bin (UI)

Access the Recovery Bin from the header navigation. It has three tabs:

#### Namespaces Tab
- Displays deleted namespaces as cards with name, description, namespace ID, and deletion date
- Click **Restore Namespace** on a card to restore it
- A search bar filters namespaces by name

#### Registries Tab
- Displays deleted registries as cards with registry name, namespace ID, state, and deletion date
- Click **Restore Registry** on a card to restore it
- A search bar filters registries by name

#### Records Tab
- Displays deleted records in a table with columns for namespace, registry, record name, state, and deletion date
- **Filter** by namespace ID, registry name, or state (live/draft)
- **Select Records** — enter selection mode to pick multiple records, then click **Restore Selected (N)** to bulk-restore
- Click **Restore** on individual rows to restore single records
- Paginated with 20 records per page

> **Note:** Deleted entities are automatically and permanently deleted after 3 days if not restored.

### Restore via the API

Use the restore endpoints to programmatically recover deleted entities. See the [State Management APIs](../standard-apis/state-management.md#restore-apis) for request/response details.

## Related references

- API reference: [State Management APIs](../standard-apis/state-management.md)
- Publish and draft workflows: [Publish APIs](../standard-apis/publish.md)
- Versioned updates: [Update APIs](../standard-apis/update.md)
