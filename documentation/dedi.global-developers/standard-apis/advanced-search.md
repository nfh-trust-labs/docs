# Advanced Search APIs

DeDi.global provides powerful search capabilities across records within a namespace. The search functionality allows you to query records using various criteria including registry filtering, field-specific searches, and nested JSON field exploration.

**Authentication**: 🔒 Search API requires authentication via API key.

## Core Search Concepts

- **Namespace-scoped search**: Search is always performed within a specific namespace
- **Global search limitation**: Cross-namespace search is not supported for security and performance reasons  
- **Latest records only**: Search returns only the latest version of each record
- **Flexible field matching**: Support for both direct field searches and nested JSON field queries
- **Partial matching**: All text searches use case-insensitive partial matching

## Search Records in Namespace

Search for records across an entire namespace with flexible filtering capabilities.

**Endpoint:** `GET /dedi/search/{namespace}`

### Parameters

**Path Parameters:**
- `namespace` (required) - Namespace ID or domain to search within

**Query Parameters (all optional):**
- `registry_name` - Filter by registry name (partial match)
- `record_name` - Search by record name (partial match)  
- `from` - Filter records created/updated after this date (ISO 8601 format)
- `to` - Filter records created/updated before this date (ISO 8601 format)
- Any field from record details - Search by specific field values

### Field Search Capabilities

The search API supports searching within the JSON `details` field of records:

**Direct Field Search:**
- `email=john@example.com` - Searches `record.details.email`
- `publicKey=abc123` - Searches `record.details.publicKey`
- `name=john` - Searches `record.details.name`

**Nested Field Search (Dot Notation):**
- `profile.name=john` - Searches `record.details.profile.name`
- `contact.email=john@example.com` - Searches `record.details.contact.email`
- `verification.status=verified` - Searches `record.details.verification.status`
- `metadata.tags.category=finance` - Searches `record.details.metadata.tags.category`

## Example Record Schema and Search Demonstrations

To demonstrate the search capabilities, let's use a comprehensive employee profile record that showcases all the different search patterns supported by the API.

### Sample Employee Profile Record

Consider this employee profile record stored in the `my-company` namespace:

```json
{
  "record_name": "john-doe-profile",
  "registry_name": "user-profiles",
  "description": "John Doe's employee profile",
  "details": {
    "email": "john.doe@company.com",
    "name": "John Doe",
    "publicKey": "0x1234567890abcdef...",
    "profile": {
      "firstName": "John",
      "lastName": "Doe", 
      "title": "Senior Developer",
      "department": "Engineering"
    },
    "contact": {
      "email": "john.doe@company.com",
      "phone": "+1-555-0123",
      "address": {
        "street": "123 Tech Street",
        "city": "San Francisco",
        "country": "USA"
      }
    },
    "verification": {
      "status": "verified",
      "method": "email",
      "verifiedAt": "2024-01-15T10:30:00Z"
    },
    "metadata": {
      "tags": {
        "category": "employee",
        "level": "senior", 
        "team": "backend"
      },
      "permissions": ["read", "write", "admin"]
    }
  }
}
```

Now let's see how you can search for this record and similar records using different search patterns:

## Search Examples Using the Sample Record

### 1. Basic Registry Filtering
Search all employee profiles in the user-profiles registry:

```http
GET /dedi/search/my-company?registry_name=user-profiles
```
*This would return John Doe's profile and all other user profiles*

### 2. Record Name Search  
Find records with specific names (partial match):

```http
GET /dedi/search/my-company?record_name=john-doe
```
*This would search across all registries under 'my-company' namespace and return John Doe's profile record*

### 3. Direct Field Search
Find all employees with a specific email:

```http
GET /dedi/search/my-company?email=john.doe@company.com
```
*This searches within `record.details.email` and returns John Doe's profile*

### 4. Nested Field Searches

**Search by profile first name:**
```http
GET /dedi/search/my-company?profile.firstName=John
```
*This searches within `record.details.profile.firstName`*

**Search by profile department:**
```http
GET /dedi/search/my-company?profile.department=Engineering
```
*This searches within `record.details.profile.department` and returns all Engineering employees*

**Search by contact city:**
```http
GET /dedi/search/my-company?contact.address.city=San%20Francisco
```
*This searches within `record.details.contact.address.city`. Note: Spaces in URL parameters must be URL-encoded as `%20`*

**Search by verification status:**
```http
GET /dedi/search/my-company?verification.status=verified
```
*This searches within `record.details.verification.status`*

**Search by metadata tags:**
```http
GET /dedi/search/my-company?metadata.tags.category=employee
```
*This searches within `record.details.metadata.tags.category`. Note: This is not DeDi's metadata field*

**Search by team:**
```http
GET /dedi/search/my-company?metadata.tags.team=backend
```
*This searches within `record.details.metadata.tags.team`*

### 5. Combined Search Criteria
Combine multiple search parameters for precise filtering:

```http
GET /dedi/search/my-company?registry_name=user-profiles&profile.department=Engineering&metadata.tags.level=senior
```
*This returns all senior-level employees in the Engineering department*

### 6. Time-based Filtering
Search records within date ranges:

```http
GET /dedi/search/my-company?from=2024-01-01T00:00:00Z&to=2024-12-31T23:59:59Z
```
*This returns all records created or updated in 2024*

### 7. Complex Multi-criteria Search
```http
GET /dedi/search/my-company?registry_name=user-profiles&verification.status=verified&profile.department=Engineering&contact.address.city=San%20Francisco
```
*This returns all verified Engineering employees located in San Francisco*

### 8. Partial Text Matching
All text searches use case-insensitive partial matching:

```http
GET /dedi/search/my-company?profile.firstName=joh
```
*This would still find "John" (partial match)*

```http
GET /dedi/search/my-company?contact.address.street=tech
```
*This would find "123 Tech Street" (partial match)*

## Important: URL Encoding

When search parameters contain spaces or special characters, they must be properly URL-encoded:

- **Spaces**: Use `%20` instead of spaces
- **Special characters**: Encode characters like `@`, `#`, `&`, etc.

**Examples:**
- `San Francisco` → `San%20Francisco`
- `john@company.com` → `john%40company.com`
- `C++ Developer` → `C%2B%2B%20Developer`

Most HTTP clients and browsers handle URL encoding automatically, but when constructing URLs manually, ensure proper encoding.

## Response Format

### Success Response (200)
Here's what a successful search response looks like when it returns our sample record:

```json
{
  "message": "Search results",
  "data": [
    {
      "record_id": "uuid-string",
      "record_name": "john-doe-profile", 
      "registry_name": "user-profiles",
      "namespace_id": "my-company",
      "description": "John Doe's employee profile",
      "details": {
        "email": "john.doe@company.com",
        "name": "John Doe",
        "publicKey": "0x1234567890abcdef...",
        "profile": {
          "firstName": "John",
          "lastName": "Doe",
          "title": "Senior Developer",
          "department": "Engineering"
        },
        "contact": {
          "email": "john.doe@company.com",
          "phone": "+1-555-0123",
          "address": {
            "street": "123 Tech Street",
            "city": "San Francisco",
            "country": "USA"
          }
        },
        "verification": {
          "status": "verified",
          "method": "email",
          "verifiedAt": "2024-01-15T10:30:00Z"
        },
        "metadata": {
          "tags": {
            "category": "employee",
            "level": "senior",
            "team": "backend"
          },
          "permissions": ["read", "write", "admin"]
        }
      },
      "state": "live",
      "version": "v1.2.0",
      "version_count": 3,
      "genesis": "2024-01-01T00:00:00Z",
      "created_at": "2024-01-01T00:00:00Z",
      "updated_at": "2024-01-15T10:30:00Z",
      "created_by": "did:dedi:user:creator123",
      "ttl": 86400,
      "meta": {},
      "valid_till": "2025-01-01T00:00:00Z",
      "latest": true
    }
  ]
}
```

### Error Responses

**Missing Namespace (400):**
```json
{
  "message": "namespace is missing"
}
```

**Namespace Not Found (404):**
```json
{
  "message": "namespace not found"
}
```

**Query Failed (500):**
```json
{
  "message": "Query failed",
  "error": "Database connection error"
}
```

## Search Performance Tips

1. **Use registry filtering**: Adding `registry_name` parameter significantly improves search performance
2. **Combine specific criteria**: Multiple specific filters are more efficient than broad searches
3. **Limit time ranges**: Use `from` and `to` parameters to narrow search scope
4. **Exact field matches**: More specific field values return faster results than partial matches

## Security and Access Control

- **Authentication required**: All search operations require valid authentication
- **Namespace scoped**: Search results are limited to the specified namespace
- **Permission based**: Only records accessible to the authenticated user are returned
- **No global search**: Cross-namespace searches are not permitted for security reasons

## Real-World Use Cases

**Employee Directory Search:**
- Find employees by name, email, or department: `?profile.firstName=John` or `?email=john.doe%40company.com`
- Locate team members: `?metadata.tags.team=backend` or `?profile.department=Engineering`
- Find employees by location: `?contact.address.city=San%20Francisco`

**HR Management:**
- Search for verified employees: `?verification.status=verified`
- Find employees by seniority level: `?metadata.tags.level=senior`
- Locate employees by role: `?profile.title=Developer`

**Access Control and Permissions:**
- Find users with specific permissions: `?metadata.permissions=admin`
- Search by employee category: `?metadata.tags.category=employee`
- Filter by verification method: `?verification.method=email`

**Compliance and Auditing:**
- Search records within timeframes: `?from=2024-01-01T00:00:00Z&to=2024-12-31T23:59:59Z`
- Track recent updates: `?profile.department=Engineering&from=2024-01-01T00:00:00Z`
- Find records by creator: Records include `created_by` field for audit trails

**Multi-criteria Filtering:**
- Find senior Engineering employees in specific locations
- Search verified employees with admin permissions
- Locate team members by multiple attributes simultaneously

The Advanced Search API provides a powerful and flexible way to query your namespace's records with support for complex field-based searches and nested JSON exploration, making it perfect for employee directory management, HR operations, and compliance reporting.
