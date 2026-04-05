# Bulk Upload

DeDi Global supports CSV-based bulk uploads for efficient data import into your registries.

### Via User Interface

1. Go to your namespace (or create one) and select **Bulk Upload**
2. Upload a CSV file:
   * First row: Column headers (must match your schema properties)
   * Remaining rows: Records to import
3. For existing registries: navigate to your registry, select Bulk Upload, and ensure your CSV columns match the registry schema.

### Via API

```
POST /dedi/bulk-upload
Content-Type: multipart/form-data
```

| Field       | Value                                     |
| ----------- | ----------------------------------------- |
| `namespace` | Your namespace ID or verified domain name |
| `file`      | CSV file                                  |

**Example:**

```bash
curl -X POST https://api.dedi.global/dedi/bulk-upload \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -F "namespace=your-domain.com" \
  -F "file=@records.csv"
```

> **Tip:** After domain verification, you can use your domain name (e.g., `your-domain.com`) instead of the raw namespace ID.

### CSV Format

#### Sample CSV — Public Key Registry

```csv
keyId,algorithm,publicKeyPem,purpose,validFrom
key-001,Ed25519,MCowBQYDK2VwAyEA...,signing,2025-01-01T00:00:00Z
key-002,ECDSA-P256,MFkwEwYHKoZIzj0C...,encryption,2025-03-15T00:00:00Z
key-003,Ed25519,MCowBQYDK2VwAyEA...,authentication,2025-06-01T00:00:00Z
```

#### Sample CSV — Membership Registry

```csv
memberId,name,role,status,joinedDate
M001,Acme Corp,full_member,active,2024-01-15
M002,Beta Industries,associate,active,2024-03-20
M003,Gamma Ltd,full_member,suspended,2024-06-01
```

#### Requirements

* First row must contain column headers
* Headers must match your schema property names exactly
* Values must conform to the property types defined in your schema
* Schema consistency is required — mismatched schemas will fail the upload
