# Bulk Upload

DeDi Global supports CSV-based bulk uploads for efficient data import into your registries.

### Process Overview

#### Via User Interface

1. Go to your namespace (or create one) and select **Bulk Upload**
2. Upload a CSV file with the following structure:
   * First row: Schema definition (used to infer properties)
   * Remaining rows: Records to import
3. For existing registries:
   * Navigate to your registry and select **Bulk Upload**
   * Ensure your CSV schema matches the existing registry schema

#### Via API

You can programmatically upload bulk data using our HTTP API endpoint.

**Endpoint:**

```http
POST /dedi/bulk-upload
```

**Request Format:**

```http
Content-Type: multipart/form-data

namespace: "76EU7J4fmT4Dq8BCSGEsGWHjJQWK2DBg4pv7phd2ghxMMKFrWKWzWa"
file: <CSV file>
```

### CSV File Requirements

* First row must contain column headers
* Headers must match your schema property names
* Values must conform to the property types defined in your schema
