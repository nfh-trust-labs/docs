# Schema design

Each registry in DeDi defines a schema — the structure of the records it contains.

When creating a registry, you provide a schema. There are two options:

#### **Option 1: Use a pre-defined schema tag.**&#x20;

Each tag points to a JSON Schema stored in the system. To list available tags:

```
GET /dedi/lookup/dedi.global/Schemas
```

Available templates include: Membership, Public Key, Revocation, Beckn.

#### **Option 2: Provide a custom JSON Schema.**&#x20;

JSON Schema Draft 7 is supported.

#### Example: Minimal Custom Schema

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "title": "Example record",
  "type": "object",
  "properties": {
    "id": { "type": "string" },
    "name": { "type": "string" }
  },
  "required": ["id", "name"]
}
```

#### Example: Public Key Registry Schema

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "title": "Public Key Record",
  "type": "object",
  "properties": {
    "keyId": { "type": "string", "description": "Unique key identifier" },
    "algorithm": { "type": "string", "enum": ["Ed25519", "ECDSA-P256", "RSA-2048"] },
    "publicKeyPem": { "type": "string", "description": "PEM-encoded public key" },
    "purpose": { "type": "string", "enum": ["signing", "encryption", "authentication"] },
    "validFrom": { "type": "string", "format": "date-time" },
    "validUntil": { "type": "string", "format": "date-time" }
  },
  "required": ["keyId", "algorithm", "publicKeyPem", "purpose", "validFrom"]
}
```

### Bulk Upload and Schemas

If you are doing a bulk upload, the importer uses the first row of the CSV to map columns to schema properties. See the [Bulk Upload guide](bulk-upload.md) for details.

### See Also

* [Lookup API ](../standard-apis/access.md)— Query available schema tags
* [Bulk Upload guide](bulk-upload.md) — CSV import with schema mapping
