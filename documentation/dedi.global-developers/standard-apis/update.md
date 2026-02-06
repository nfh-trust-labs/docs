# Update Management APIs

Update Management APIs enable you to modify and evolve your DeDi.global entities after creation while maintaining data integrity and version history. These APIs support controlled updates with immutable versioning, ensuring complete audit trails and rollback capabilities.

**Authentication**: 🔒 All Update Management APIs require authentication via API key.

## Why Updates Matter in DeDi.global

### Version Control and Immutability

DeDi.global implements immutable versioning for all updates. When you update any entity, the system:

1. **Preserves History**: Original versions remain unchanged and accessible
2. **Creates New Versions**: Updates generate new version entries with incremented version numbers
3. **Maintains Relationships**: Parent-child relationships and dependencies are preserved
4. **Enables Rollback**: Historical versions can be accessed using version-specific queries

### Real-World Update Scenarios

Consider an educational institution "EduTech University" managing student data:

#### **Namespace Updates**
- **Quarterly Rebranding**: Update namespace description and metadata when the university rebrands
- **Policy Changes**: Modify TTL values when data retention policies change
- **Contact Updates**: Update metadata with new administrative contact information

#### **Registry Updates**  
- **Schema Evolution**: Change from basic student registry to comprehensive academic records
- **Compliance Requirements**: Update metadata to include compliance information
- **Department Restructuring**: Modify descriptions when academic departments reorganize

#### **Record Updates**
- **Student Progress**: Update student records as they complete courses and achieve milestones
- **Contact Information**: Modify student contact details when they move or change phone numbers
- **Academic Status**: Update enrollment status, GPA, and graduation information

---

## Namespace Update APIs

Namespace updates enable you to modify properties including name, description, metadata, and TTL values while maintaining version history and data integrity.

### Update Namespace

Modify an existing namespace with new information while preserving the original version through immutable versioning.

**Endpoint:** `POST /dedi/{namespace}/update-namespace`

**Parameters:**
- `namespace` (path, required): Namespace ID or verified domain

**Request Body:**
```typescript
{
  name?: string;        // Optional: New namespace display name
  description?: string; // Optional: Updated description  
  meta?: object;       // Optional: Updated metadata object
  ttl?: number;        // Optional: New TTL in seconds
}
```

**Prerequisites:**
- Requesting user must be a namespace delegate or owner
- Either `name` or `description` must be provided
- Metadata must follow valid JSON structure

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/edutech-university/update-namespace', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    name: "EduTech University System",
    description: "Comprehensive academic data management for EduTech University - Updated for 2024 academic year",
    meta: {
      department: "IT Administration", 
      contact: "admin@edutech.edu",
      updated_reason: "Annual policy review"
    },
    ttl: 31536000 // 1 year in seconds
  })
});

const data = await response.json();
```

**Success Response (200):**
```typescript
{
  "message": "namespace updated",
  "data": {
    "digest": "0x1a2b3c4d5e6f..."
  }
}
```

**Error Responses:**
- `400` - Missing required fields, empty name, or invalid metadata
- `403` - Insufficient privileges to update namespace
- `404` - Namespace not found
- `500` - Internal server error

**Updatable Fields:**

| Field | Updatable | Notes |
|-------|-----------|-------|
| ✅ `name` | Yes | Display name can be changed |
| ✅ `description` | Yes | Full description updates allowed |
| ✅ `meta` | Yes | Metadata object can be modified |
| ✅ `ttl` | Yes | TTL can be adjusted |
| ❌ `namespace_id` | No | Immutable identifier |
| ❌ `created_by` | No | Original creator preserved |
| ❌ `genesis` | No | Creation timestamp preserved |
| ❌ `delegates` | No | Use delegation APIs instead |
| ❌ `domain` | No | Use domain verification APIs |

**Important Notes:**
- **Validation Required**: Either `name` or `description` must be provided
- **Empty Names Rejected**: Name cannot be empty if provided
- **Metadata Validation**: Meta objects must follow valid JSON structure
- **Authorization Required**: Only namespace delegates can perform updates
- **Version Increment**: Each update creates a new version

## Registry Update APIs

Registry updates allow you to modify properties including description, metadata, schema tags, and TTL values while maintaining schema integrity and state validation.

### Update Registry

Modify an existing registry while maintaining schema integrity and ensuring proper state validation.

**Endpoint:** `POST /dedi/{namespace}/{registry_name}/update-registry`

**Parameters:**
- `namespace` (path, required): Namespace ID or verified domain
- `registry_name` (path, required): Registry name to update

**Request Body:**
```typescript
{
  description?: string; // Optional: Updated registry description
  meta?: object;       // Optional: Updated metadata object
  tag?: string;        // Optional: New schema tag reference
  ttl?: number;        // Optional: New TTL in seconds
}
```

**Prerequisites:**
- Requesting user must be a registry delegate or namespace delegate
- Registry must be in LIVE state
- New schema tags must exist in the bootstrap registry

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/edutech-university/student-records/update-registry', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    description: "Comprehensive student academic records including transcripts, achievements, and enrollment status - Updated for 2024 standards",
    meta: {
      data_classification: "confidential",
      retention_policy: "7_years_post_graduation",
      last_schema_review: "2024-01-15",
      department_owner: "registrar"
    },
    tag: "academic-record-v2",
    ttl: 31536000
  })
});

const data = await response.json();
```

**Success Response (200):**
```typescript
{
  "message": "registry updated",
  "data": {
    "digest": "0x2b3c4d5e6f7a..."
  }
}
```

**Error Responses:**
- `400` - Missing required parameters or invalid metadata
- `403` - Insufficient privileges to update registry
- `404` - Namespace or registry not found, or invalid state
- `500` - Internal server error

**Updatable Fields:**

| Field | Updatable | Notes |
|-------|-----------|-------|
| ✅ `description` | Yes | Full description updates allowed |
| ✅ `meta` | Yes | Metadata object can be modified |
| ✅ `tag` | Yes | Schema tag can be changed (validates against bootstrap registry) |
| ✅ `ttl` | Yes | TTL can be adjusted |
| ❌ `registry_name` | No | Immutable identifier |
| ❌ `namespace_id` | No | Parent namespace cannot be changed |
| ❌ `registry_id` | No | Unique registry ID preserved |
| ❌ `schema` | No | Schema structure is immutable (use tag for schema changes) |
| ❌ `created_by` | No | Original creator preserved |
| ❌ `delegates` | No | Use delegation APIs instead |
| ❌ `state` | No | Use state management APIs instead |

**State Validation:**
Registry updates are only allowed for registries in specific states:
- ✅ **LIVE**: Full updates permitted
- ❌ **REVOKED**: Updates not allowed
- ❌ **SUSPENDED**: Updates not allowed  
- ❌ **EXPIRED**: Updates not allowed

**Important Notes:**
- **Tag Validation**: New tags must exist in the bootstrap schema registry
- **Authorization Required**: Only registry delegates or namespace delegates can perform updates
- **State Checks**: Registry must be in LIVE state for updates
- **Webhook Notifications**: Updates trigger webhooks to registered watchers

## Record Update APIs

Record updates enable you to modify record data, metadata, and validity information with comprehensive schema validation and state management.

### Update Record

Modify existing record data while enforcing schema compliance and maintaining proper state validation.

**Endpoint:** `POST /dedi/{namespace}/{registry_name}/{record_name}/update-record`

**Parameters:**
- `namespace` (path, required): Namespace ID or verified domain
- `registry_name` (path, required): Registry name containing the record
- `record_name` (path, required): Record name to update

**Request Body:**
```typescript
{
  details: object;      // Required: Updated record data (must match registry schema)
  description?: string; // Optional: Updated record description
  meta?: object;       // Optional: Updated metadata object
  valid_till?: string; // Optional: New expiration date (ISO string)
  ttl?: number;        // Optional: New TTL in seconds
}
```

**Prerequisites:**
- Requesting user must be a registry delegate or namespace delegate
- Record must be in DRAFT or LIVE state
- Parent registry must be in LIVE state
- Details must conform to registry schema

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/edutech-university/student-records/john-doe-2024/update-record', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    details: {
      student_id: "EDU2024001",
      full_name: "John Michael Doe",
      email: "john.doe@student.edutech.edu", 
      phone: "+1-555-0123",
      enrollment_status: "active",
      academic_level: "undergraduate",
      major: "Computer Science",
      minor: "Mathematics",
      gpa: 3.85,
      credits_completed: 90,
      expected_graduation: "2025-05-15",
      address: {
        street: "123 University Ave",
        city: "College Town", 
        state: "CA",
        zip: "90210"
      },
      emergency_contact: {
        name: "Jane Doe",
        relationship: "mother",
        phone: "+1-555-0124"
      },
      last_updated: "2024-02-05T10:30:00Z"
    },
    description: "Updated student record with current academic progress and contact information",
    meta: {
      updated_by: "registrar_system",
      update_reason: "semester_grade_posting",
      previous_gpa: 3.72,
      grade_posting_date: "2024-02-01"
    },
    valid_till: "2029-05-15T00:00:00Z",
    ttl: 126144000
  })
});

const data = await response.json();
```

**Success Response (200):**
```typescript
{
  "message": "Record updated", 
  "data": {
    "digest": "0x3c4d5e6f7a8b..."
  }
}
```

**Error Responses:**
- `400` - Missing details, schema validation failed, or invalid state
- `403` - Insufficient privileges to update record
- `404` - Namespace, registry, or record not found
- `500` - Internal server error

**Updatable Fields:**

| Field | Updatable | Notes |
|-------|-----------|-------|
| ✅ `details` | Yes | Must conform to registry schema |
| ✅ `description` | Yes | Record description can be updated |
| ✅ `meta` | Yes | Metadata object can be modified |
| ✅ `valid_till` | Yes | Expiration date can be adjusted |
| ✅ `ttl` | Yes | TTL can be modified |
| ❌ `record_name` | No | Immutable identifier |
| ❌ `record_id` | No | Unique record ID preserved |
| ❌ `namespace_id` | No | Parent namespace cannot be changed |
| ❌ `registry_name` | No | Parent registry cannot be changed |
| ❌ `created_by` | No | Original creator preserved |
| ❌ `genesis` | No | Creation timestamp preserved |
| ❌ `state` | No | Use state management APIs instead |

**Update Behavior by Record State:**

#### Draft Records
For records in **DRAFT** state:
- **In-Place Updates**: Changes are applied directly to the existing record
- **No Version Increment**: Version number remains the same
- **No Blockchain Transaction**: Updates are local database operations

#### Live Records  
For records in **LIVE** state:
- **Version Creation**: New version entry is created
- **Blockchain Update**: Changes are recorded on the CORD blockchain
- **Immutable History**: Previous version becomes historical
- **Strict Schema**: Full schema validation is enforced

**State Validation:**
Record updates are only allowed for specific states:
- ✅ **DRAFT**: Direct updates allowed
- ✅ **LIVE**: Versioned updates allowed
- ❌ **EXPIRED**: Updates not allowed
- ❌ **REVOKED**: Updates not allowed
- ❌ **SUSPENDED**: Updates not allowed

**Important Notes:**
- **Required Field**: `details` must be provided
- **Schema Compliance**: Details must match the registry's JSON schema exactly
- **Registry State Check**: Parent registry must be in LIVE state
- **Authorization Required**: Only registry delegates or namespace delegates can perform updates
- **Webhook Notifications**: Updates trigger webhooks to record and registry watchers

## Common Use Cases

### Version Management Strategy

1. **Document Changes**: Use metadata to track update reasons and change details
2. **Incremental Updates**: Make small, focused changes rather than large overwrites
3. **Test Schema Changes**: Validate new tags in development before production updates
4. **Monitor Versions**: Keep track of version counts to understand update frequency

### Data Integrity Guidelines

1. **Schema Validation**: Always validate data against current registry schemas
2. **State Awareness**: Check entity states before attempting updates
3. **Backup Strategy**: Leverage immutable versioning for rollback capabilities
4. **Consistency Checks**: Ensure related records remain consistent across updates

### Organizational Workflows

#### **Educational Institution Example:**
```
EduTech University Namespace
├── Student Records Registry
│   ├── Update GPAs (Semester-end)
│   ├── Update contact information (As needed)
│   └── Update enrollment status (Registration periods)
├── Course Catalog Registry
│   ├── Update descriptions (Annual review)
│   ├── Update prerequisites (Curriculum changes)
│   └── Update credit hours (Academic policy changes)
└── Faculty Directory Registry
    ├── Update office information (As needed)
    ├── Update course assignments (Semester planning)
    └── Update research interests (Annual updates)
```

## Best Practices

### Update Strategy

1. **Document Changes**: Use metadata to track update reasons and change details  
2. **Incremental Updates**: Make small, focused changes rather than large overwrites
3. **Test Schema Changes**: Validate new tags in development before production updates
4. **Monitor Versions**: Keep track of version counts to understand update frequency

### Security Considerations

1. **Authorization Verification**: Ensure users have appropriate delegate permissions
2. **State Awareness**: Check entity states before attempting updates  
3. **Input Validation**: Validate all input data to prevent injection attacks
4. **Audit Trails**: Use metadata to maintain clear audit trails for compliance

### Performance Optimization

1. **Batch Related Updates**: Group related changes into single update operations
2. **TTL Management**: Set appropriate TTL values to balance caching and freshness
3. **Metadata Efficiency**: Keep metadata objects focused and reasonably sized
4. **Update Frequency**: Avoid excessive updates that could impact system performance

### Common Update Workflows

1. **Student Progress Tracking**: Regular GPA and credit updates with semester milestones
2. **Contact Information Management**: Address and phone number updates as users relocate
3. **Schema Evolution**: Gradual migration to new schema versions for enhanced data validation
4. **Compliance Updates**: Metadata changes to reflect new regulatory requirements
5. **Seasonal Updates**: Bulk description and policy updates during annual review periods

Update Management APIs in DeDi.global enable organizations to maintain evolving data structures while preserving data integrity, audit trails, and compliance requirements. The immutable versioning system ensures that all changes are tracked and historical data remains accessible for compliance and rollback purposes.
