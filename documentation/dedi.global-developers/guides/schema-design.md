# Schema design

Each registry in DeDi defines a schema — the structure of the records it contains.

When creating a registry you should provide a schema. There are two main options:

* Use a pre-defined schema tag. Each schema tag points to a JSON Schema stored in the system. To list available tags use the lookup API:

```http
GET /dedi/lookup/dedi.global/Schemas
```

* Provide a custom JSON Schema (JSON Schema Draft 7 is supported).

#### Example: minimal JSON Schema (Draft 7)

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "title": "Example record",
  "type": "object",
  "properties": {
    "id": { "type": "string" },
    "name": { "type": "string" },
  },
  "required": ["id", "name"]
}
```

* If you are doing a bulk upload, the importer will use the first row of the CSV to map columns to schema properties — see `bulk-upload.md` in this folder for full details.

#### See also

* Lookup API: `../standard-apis/1. lookup.md`
* Bulk upload guide: `3. bulk-upload.md`
