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

## Related references

- API reference: [State Management APIs](../standard-apis/state-management.md)
- Publish and draft workflows: [Publish APIs](../standard-apis/publish.md)
- Versioned updates: [Update APIs](../standard-apis/update.md)
