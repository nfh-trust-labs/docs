# Publish APIs

The Publish APIs enable you to create and manage the core components of your data infrastructure on DeDi.global. These APIs allow you to organize data hierarchically through namespaces, define structured schemas via registries, and store individual records with full lifecycle management.

**Authentication**: 🔒 All Publish APIs require authentication via API key.

## Core Concepts

Before diving into the APIs, it's essential to understand DeDi.global's hierarchical structure:

- **Namespace**: A top-level container that organizes related registries
- **Registry**: Defines the structure and schema for a specific type of data
- **Record**: Individual data entries conforming to a registry's schema

## Namespace Management

Namespaces provide isolation and organization for your data. They act as containers for related registries and can be associated with verified domains for enhanced trust.

### Create Namespace

Create a new namespace to organize your registries and establish data boundaries.

**Endpoint**: `POST /dedi/create-namespace`

**Request Body**:
```typescript
{
  name: string;        // Unique namespace name
  description: string; // Purpose and description of the namespace
  meta?: object;       // Optional metadata for additional context
  version_count?: number; // Optional version number for tracking
}
```

**Example Request**:
```typescript
const response = await fetch('https://api.dedi.global/dedi/create-namespace', {
  method: 'POST',
  headers: {
    'Authorization': `Bearer ${your_api_key}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    name: 'employee-directory',
    description: 'Corporate employee information and credentials',
    meta: {
      department: 'Human Resources',
      company: 'Acme Corporation'
    }
  })
});

const namespace = await response.json();
```

**Response**:
```typescript
{
  message: string;        // Success confirmation message
  data: {
    namespace_id: string; // Unique identifier for the created namespace
  }
}
```

**Error Scenarios**:
- `400` - Invalid request body
- `401` - Missing or invalid API key
- `409` - Namespace name already exists
- `429` - Rate limit exceeded

**Use Cases**:
- Organizing data by department, project, or domain
- Creating isolated environments for different applications
- Setting up customer-specific data spaces

## Registry Management

Registries define the structure and validation rules for the data you want to store. They act as templates that ensure data consistency and enable powerful querying capabilities.

### Create Registry

Define a new registry within a namespace to store structured records.

**Endpoint**: `POST /dedi/{namespace}/create-registry`

**Path Parameters**:
- `namespace` - Namespace ID or verified domain name

**Request Body**:
```typescript
{
  registry_name: string;  // Unique registry name within the namespace
  description: string;    // Description of what this registry stores
  schema?: object;        // Custom JSON Schema defining record structure
  tag?: 'membership' | 'public_key' | 'revoke' | 'beckn_subscriber' | 'beckn_subscriber_reference'; // Pre-built schema tag
  meta?: object;          // Optional metadata
}
```

Provide either `schema` or `tag`. Do not send both in the same request.

**Example Request**:
```typescript
const registryData = {
  registry_name: 'employee-profiles',
  description: 'Employee profile information including roles and contact details',
  schema: {
    type: 'object',
    properties: {
      employee_id: { type: 'string', pattern: '^EMP[0-9]{6}$' },
      full_name: { type: 'string', minLength: 1 },
      email: { type: 'string', format: 'email' },
      department: { type: 'string', enum: ['Engineering', 'HR', 'Sales'] },
      hire_date: { type: 'string', format: 'date' },
      active: { type: 'boolean', default: true }
    },
    required: ['employee_id', 'full_name', 'email', 'department']
  },
  meta: { 
    version: '1.0',
    data_classification: 'internal' 
  }
};

const response = await fetch(`https://api.dedi.global/dedi/employee-directory/create-registry`, {
  method: 'POST',
  headers: {
    'Authorization': `Bearer ${your_api_key}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify(registryData)
});
```

**Response**:
```typescript
{
  message: string;        // Success confirmation message
  data: {
    registry_id: string;  // Unique registry identifier
  }
}
```

**Schema Tags**:
DeDi.global currently provides 5 pre-built schemas out of the box:
- `membership` - Identity and membership data schemas
- `public_key` - Cryptographic public key schemas
- `revoke` - Revocation and status tracking schemas
- `beckn_subscriber` - Beckn protocol subscriber information
- `beckn_subscriber_reference` - Beckn protocol subscriber reference data

**Note**: When using any of the pre-built schema tags (`membership`, `public_key`, `revoke`, `beckn_subscriber`, `beckn_subscriber_reference`), provide `tag` and omit `schema`. For custom registries, provide `schema` and omit `tag`.

*For detailed information about these pre-built schemas, please reach out to our support team.*

**Error Scenarios**:
- `400` - Invalid schema format or invalid `schema`/`tag` combination
- `404` - Namespace not found
- `403` - Insufficient permissions for the namespace
- `409` - Registry name already exists in the namespace

## Time to Live (TTL) Management

DeDi.global supports automatic expiration of data through Time to Live (TTL) values, which can be set at both the registry and record levels.

### TTL Behavior

- **Registry TTL**: Sets the default TTL(i.e. 600) for the registry. This can be updated as per use.
- **Record TTL**: Sets the default TTL(i.e. 600) for the records. This can be updated as per use.
- **Expiration**: Once TTL expires, the cache is cleared.
- **TTL Units**: Specified in seconds (e.g., 86400 for 24 hours, 604800 for 7 days)

### Setting TTL Values

TTL can be overwritten during updates:

```typescript
// Registry update to 24 hour TTL
{
  "description": "User session data",
  "tag": "custom",
  "ttl": 86400, // default is 600
  "meta": {}
}

// Record update to 7-day TTL
{
  "description": "User session for mobile app",
  "details": { /* session data */ },
  "valid_till": , // Expiration date in ISO format
  "ttl": 604800,
  "meta": {}
}
```

### TTL Best Practices

- **Session Data**: Use short TTL (minutes to hours) for temporary session information
- **User Profiles**: Use longer TTL (weeks to months) for stable user data
- **Audit Logs**: Consider no TTL or very long TTL for compliance requirements
- **Cache Data**: Use short TTL for frequently updated cache entries

## Record Management

Records are individual data entries that conform to a registry's schema. DeDi.global supports a draft-and-publish workflow for better data quality control.

### Save Record as Draft

Create a record in draft state for review before publishing.

**Endpoint**: `POST /dedi/{namespace}/{registry_name}/save-record-as-draft`

**Path Parameters**:
- `namespace` - Namespace ID or verified domain
- `registry_name` - Target registry name

**Query Parameters**:
- `publish` - Set to `true` to save and publish immediately (optional)

**Request Body**:
```typescript
{
  record_name: string;     // Unique record name within the registry
  description: string;     // Record description
  details: object;        // Record data matching registry schema
  meta?: object;          // Application-specific metadata
  valid_till?: string;    // Expiration date in ISO format
}
```

**Example Request**:
```typescript
const recordData = {
  record_name: 'john-doe-profile',
  description: 'Employee profile for John Doe in Engineering',
  details: {
    employee_id: 'EMP123456',
    full_name: 'John Doe',
    email: 'john.doe@acme.corp',
    department: 'Engineering',
    hire_date: '2024-01-15',
    active: true
  },
  meta: {
    created_by: 'hr-system',
    data_source: 'employee_onboarding_form'
  },
  valid_till: '2025-12-31T23:59:59Z'
};

const response = await fetch(
  `https://api.dedi.global/dedi/employee-directory/employee-profiles/save-record-as-draft`,
  {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${your_api_key}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify(recordData)
  }
);
```

**Response**:
```typescript
{
  message: "record saved as draft";
  data: {
    record_name: string;
  }
}
```

When `publish=true`, the response is:

```typescript
{
  message: "record created";
  data: {
    record_id: string;
  }
}
```

### Publish Records

Queue one or more draft records for publishing so they transition to live state and become queryable.

**Endpoint**: `POST /dedi/{namespace}/{registry_name}/publish-records`

**Path Parameters**:
- `namespace` - Namespace ID or verified domain
- `registry_name` - Registry containing the draft records

**Request Body**:
```typescript
{
  records: string[]; // One or more draft record names
}
```

**Example Request**:
```typescript
const response = await fetch(
  `https://api.dedi.global/dedi/employee-directory/employee-profiles/publish-records`,
  {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${your_api_key}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      records: ['john-doe-profile', 'jane-doe-profile']
    })
  }
);
```

**Response**:
```typescript
{
  message: string; // "Records are in publish queue"
  data: {
    count: number;
    record_ids: string[];
  }
}
```

**Important Notes**:
- Every listed record must already exist in `draft` state.
- The target registry must be in `live` state.
- Publishing is queued and returns the generated record IDs for the submitted draft records.
- Published records are then available through lookup and query APIs.

## Data Export

Export registry data for analysis, backup, or migration purposes.

### Export Records as CSV

Download all records from a registry in CSV format.

**Endpoint**: `GET /dedi/{namespace}/{registry_name}/export-as-csv`

**Path Parameters**:
- `namespace` - Namespace ID or verified domain
- `registry_name` - Registry to export

**Example Request**:
```typescript
const response = await fetch(
  `https://api.dedi.global/dedi/employee-directory/employee-profiles/export-as-csv`,
  {
    headers: {
      'Authorization': `Bearer ${your_api_key}`
    }
  }
);

// Handle CSV download
const csvData = await response.text();
const blob = new Blob([csvData], { type: 'text/csv' });
```

**Response**:
- Content-Type: `text/csv`
- CSV file with all registry records

**CSV Format**:
- Headers match registry schema fields
- Nested objects flattened with dot notation
- Includes record metadata (ID, timestamps, state)
- One row per record

## Bulk Operations

Handle large-scale data operations efficiently through asynchronous job processing.

### Bulk Upload Records

Upload multiple records via CSV files with asynchronous processing.

**Endpoint**: `POST /dedi/bulk-upload`

**Request Format**: `multipart/form-data`

**Form Fields**:
- `namespace` - Target namespace ID or verified domain (required)
- `registry_name` - Target registry name (required)
- `record_name_field` - CSV column to use as `record_name` (optional)
- `file` - Single CSV file upload (required, max 100MB)

**Example Request**:
```typescript
const formData = new FormData();
formData.append('namespace', 'employee-directory');
formData.append('registry_name', 'employee-profiles');
formData.append('file', csvFile);

const response = await fetch('https://api.dedi.global/dedi/bulk-upload', {
  method: 'POST',
  headers: {
    'Authorization': `Bearer ${your_api_key}`
  },
  body: formData
});

const job = await response.json();
```

**Response**:
```typescript
{
  status: string;          // "success"
  total_rows: number;      // Parsed CSV row count reported in the response
  message: string;         // Success confirmation message
  data: {
    jobId: string;         // Unique job identifier for status tracking
    statusCheckUrl: string; // Relative URL to check job progress
  }
}
```

### Check Job Status

Monitor the progress of bulk upload operations.

**Endpoint**: `GET /dedi/bulk-upload/status/{jobId}`

**Path Parameters**:
- `jobId` - Job ID returned from bulk upload

**Example Request**:
```typescript
const response = await fetch(`https://api.dedi.global/dedi/bulk-upload/status/${job_id}`, {
  headers: {
    'Authorization': `Bearer ${your_api_key}`
  }
});

const status = await response.json();
```

**Response**:
```typescript
{
  status: string;          // "success"
  message: string;         // Success confirmation message
  data: {
    jobId: string;         // Job identifier
    createdAt: string;     // Job creation timestamp
    updatedAt: string;     // Last update timestamp
    status: 'pending' | 'processing' | 'completed' | 'failed';
    namespace: string;     // Target namespace
    registryName: string;  // Target registry
    totalEntries: number;  // Rows discovered in the CSV
    validEntries: number;  // Successfully processed rows
    failedEntries: number; // Failed rows
    error?: string;        // Error message if job failed
    result?: object;       // Final processing summary if available
    failedEntriesUrl?: string | null;
    downloadFailedEntriesCsvUrl?: string | null;
  }
}
```

**Job States**:
- `pending` - Job queued, waiting to start
- `processing` - Currently processing rows
- `completed` - Processing finished
- `failed` - Job failed with errors

**Polling Recommendation**: Check status every 5-10 seconds during processing.

### Get User Jobs

Retrieve a list of all bulk upload jobs for the authenticated user.

**Endpoint**: `GET /dedi/bulk-upload/jobs`

**Query Parameters**:
- `page` - Page number (default: 1)
- `limit` - Jobs per page (default: 10, max: 100)

**Example Request**:
```typescript
const response = await fetch('https://api.dedi.global/dedi/bulk-upload/jobs?page=1&limit=20', {
  headers: {
    'Authorization': `Bearer ${your_api_key}`
  }
});

const jobsList = await response.json();
```

**Response**:
```typescript
{
  status: string;          // "success"
  message: string;         // Success confirmation message
  data: {
    jobs: Array<{
      jobId: string;       // Job identifier
      status: string;      // Job status
      createdAt: string;   // Job creation timestamp
      updatedAt: string;   // Last update timestamp
      namespace: string;   // Target namespace
      error?: string;      // Error message if failed
    }>;
    pagination: {
      page: number;        // Current page number
      limit: number;       // Items per page
      total: number;       // Total number of jobs
      pages: number;       // Total number of pages
    };
  }
}
```

### Download Registry CSV Template

Download a schema-derived CSV template for a registry.

**Endpoint**: `GET /dedi/{namespace}/{registry_name}/download-sample-csv`

**Path Parameters**:
- `namespace` - Namespace ID or verified domain
- `registry_name` - Registry name

**Example Request**:

```bash
curl -L \
  -H "Authorization: Bearer YOUR_API_KEY" \
  "https://api.dedi.global/dedi/employee-directory/employee-profiles/download-sample-csv" \
  -o employee-profiles_template.csv
```

**Success Response (200):**
- Content type: `text/csv`
- Content disposition: `attachment; filename={registry_name}_template.csv`

**Error Scenarios**:
- `400` - Registry schema not found or schema has no exportable fields
- `404` - Namespace not found or registry not found
- `500` - Failed to export CSV template

## CSV Format Requirements

When using bulk upload, ensure your CSV files follow these guidelines:

**File Format**:
- Valid UTF-8 encoding
- First row must contain field names matching registry schema
- Maximum file size: 100MB
- Supported extensions: `.csv`
- Upload exactly one CSV file per request

**Data Format**:
- Nested objects: Use dot notation (e.g., `address.street`, `address.city`)
- Arrays: Use comma-separated values within quotes
- Dates: ISO 8601 format recommended
- Boolean: `true`/`false` or `1`/`0`

**Example JSON Schema**:
```json
{
  "type": "object",
  "properties": {
    "employee_id": { "type": "string" },
    "personal_info": {
      "type": "object",
      "properties": {
        "full_name": { "type": "string" },
        "email": { "type": "string", "format": "email" },
        "phone": { "type": "string" }
      }
    },
    "employment": {
      "type": "object", 
      "properties": {
        "department": { "type": "string" },
        "position": { "type": "string" },
        "hire_date": { "type": "string", "format": "date" }
      }
    },
    "active": { "type": "boolean" },
    "skills": { 
      "type": "array", 
      "items": { "type": "string" }
    }
  }
}
```

**Example CSV**:
```csv
employee_id,personal_info.full_name,personal_info.email,personal_info.phone,employment.department,employment.position,employment.hire_date,active,skills
EMP123456,John Doe,john.doe@acme.corp,+1-555-0123,Engineering,Senior Developer,2024-01-15,true,"JavaScript,TypeScript,Node.js"
EMP123457,Jane Smith,jane.smith@acme.corp,+1-555-0124,Sales,Sales Manager,2024-02-01,true,"CRM,Negotiation,Analytics"
```

## Error Handling

Common error scenarios and their resolutions:

**Schema Validation Errors**:
- Ensure record data matches registry schema exactly
- Check required fields are present
- Validate data types and formats

**Permission Errors**:
- Verify you have write access to the namespace/registry
- Check if you're a delegate with appropriate permissions

**Rate Limiting**:
- Bulk operations have special rate limits
- Monitor rate limit headers in responses
- Implement exponential backoff for retries

**Troubleshooting Tips**:
- Use draft mode to test record formats before publishing
- Start with small CSV files for bulk uploads
- Monitor job status regularly for long-running operations
- Keep API keys secure and rotate them periodically
