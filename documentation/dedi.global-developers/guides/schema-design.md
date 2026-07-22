# Schema design

Each registry in DeDi defines a schema — the structure of the records it contains.

When creating a registry, you provide a schema. There are three options:

#### **Option 1: Use a pre-defined schema tag.**&#x20;

Each tag points to a JSON Schema stored in the system. To list available tags:

```
GET /dedi/lookup/dedi.global/Schemas
```

Available templates include:

* `membership` - Identity and membership data schemas
* `public_key` - Cryptographic public key schemas
* `revoke` - Revocation and status tracking schemas
* `beckn_subscriber` - Beckn protocol subscriber information
* `beckn_subscriber_reference` - Beckn protocol subscriber reference data
* `public_rule_set` - Public rule set schemas
* `public_data_set` - Public data set schemas

#### **Option 2: Build a custom schema using the Visual Schema Builder.**&#x20;

The DeDi.global publish UI includes a built-in schema builder with two modes:

##### Form Builder

A visual form-based editor where you define fields without writing JSON:

- **Field name** — the property key (alphanumeric, no spaces)
- **Type** — `string`, `number`, `integer`, `boolean`, `file`, `object`, or `array`
- **Required** — whether the field must be present in every record
- **Nullable** — whether the field accepts `null` values
- **Description** — optional help text for the field
- **Default value** — optional default value

Type-specific options:
- **String**: format (`email`, `date`, `date-time`, `uri`, `uuid`, `data-url`), pattern (regex), min/maxLength, enum values
- **Number/Integer**: min/max value, multipleOf
- **Array**: item type (with nested field definitions), min/max items, unique items
- **Object**: nested fields (recursive)
- **Root strict mode**: reject unlisted fields at the schema root

A live JSON Schema preview updates as you build. Click **Use Schema** to apply it.

##### Paste JSON

Direct input for JSON Schema Draft-7 documents:

- Paste your schema into the editor
- Live syntax validation with valid/invalid indicators
- Schema preview panel updates in real-time
- Click **Use Schema** to apply the parsed schema

##### Publish Schema

Both modes include a **Publish Schema** button that opens a GitHub issue to request making your schema public. Once approved, your schema becomes a discoverable tag available to all DeDi.global users.

#### **Option 3: Provide a raw JSON Schema.**&#x20;

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

{% hint style="info" %}
For registries using the beckn\_subscriber schema, the subscriber identity is now tied to the namespace’s verified domain to prevent participants from claiming another network participant’s identity. Before creating a record, verify the namespace using a DNS TXT record, a well-known file, or an approved attestation request. For direct domain verification, records may also be created while verification is pending using the requested domain, with a pending-verification warning displayed. During record creation, the verified domain is automatically locked as the subscriber ID, and the user may optionally enter a subdomain. For attested namespaces, record creation is enabled only after the attestation is approved, and the subscriber ID is constructed using the current attester and attested-domain details. Values that do not match the verified domain or one of its valid subdomains are rejected. The Record ID is displayed as Key ID for Beckn subscriber records, and existing records show a warning when their subscriber ID no longer matches the namespace’s current verification.
{% endhint %}

### See Also

* [Lookup API ](../standard-apis/access.md)— Query available schema tags
* [Bulk Upload guide](bulk-upload.md) — CSV import with schema mapping
