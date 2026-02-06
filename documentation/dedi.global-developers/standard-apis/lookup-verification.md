# Lookup Verification APIs

DeDi.global provides cryptographic verification capabilities that allow you to verify the authenticity and integrity of namespace, registry, and record lookup responses. These APIs enable independent verification of data integrity using the same cryptographic proofs that are anchored on the CORD blockchain.

**Authentication**: 🔒 All Lookup Verification APIs require authentication via API key.

## What is Lookup Verification?

Lookup verification is a cryptographic process that allows you to independently verify that the data returned from DeDi.global lookup operations (namespaces, registries, and records) has not been tampered with and originates from the authentic source. This is achieved by recomputing the cryptographic digest of the lookup response data and comparing it against the blockchain-anchored proof.

### Why Verification Matters

**Data Integrity Assurance:**
- Verify that lookup responses haven't been modified in transit
- Ensure data authenticity from the original creator
- Detect any tampering or corruption of data

**Trust Without Intermediaries:**
- Independent verification without relying solely on DeDi.global's API
- Cryptographic proof validation using the same algorithms as the blockchain
- End-to-end data integrity verification

**Compliance and Audit:**
- Maintain audit trails of verification attempts
- Meet regulatory requirements for data integrity
- Support legal and compliance frameworks

## How Verification Works

The verification process follows the same cryptographic approach used when data is initially anchored on-chain:

### Verification Process Flow

1. **Obtain Lookup Response**: Perform a standard namespace, registry, or record lookup
2. **Extract Verification Fields**: Extract specific fields from the response data 
3. **Build Verification Object**: Construct an object with the exact fields used for original digest calculation
4. **Canonical Serialization**: Apply the same serialization used during creation
5. **String Conversion**: Convert to string using `JSON.stringify()`
6. **Hash Calculation**: Calculate digest using **BLAKE2 H256** algorithm
7. **Compare Digests**: Compare calculated digest with the digest in the proof section
8. **Log Verification Result**: Store verification attempt and result for audit purposes

### Fields Used for Digest Calculation

Each entity type uses specific fields for digest calculation, exactly matching those used during creation:

**Namespace Verification Fields:**
- `namespace_id` - Unique namespace identifier
- `description` - Namespace description
- `genesis` - Creation timestamp (ISO string format)
- `created_by` - Creator's DID identifier
- `version` - Version string
- `version_count` - Numeric version counter

**Registry Verification Fields:**
- `namespace_id` - Parent namespace identifier
- `registry_name` - Registry name within namespace
- `description` - Registry description
- `schema` - JSON schema object
- `tag` - Schema tag identifier
- `version` - Version string
- `version_count` - Numeric version counter
- `state` - Registry state (live, suspended, etc.)
- `genesis` - Creation timestamp (ISO string format)
- `created_by` - Creator's DID identifier
- `meta` - Metadata object
- `ttl` - Time-to-live value

**Record Verification Fields:**
- `namespace_id` - Parent namespace identifier
- `registry_id` - Parent registry identifier
- `registry_name` - Registry name
- `record_name` - Record name within registry
- `description` - Record description
- `details` - Record data content
- `version` - Version string
- `version_count` - Numeric version counter
- `state` - Record state (live, draft, etc.)
- `genesis` - Creation timestamp (ISO string format)
- `created_by` - Creator's DID identifier
- `meta` - Metadata object
- `ttl` - Time-to-live value

## Namespace Verification APIs

### Verify Namespace Lookup

Cryptographically verify the integrity of a namespace lookup response.

**Endpoint:** `POST /dedi/verify-namespace-lookup`

**Request Body:**
```json
{
  "namespace_lookup_response": {
    "message": "string",
    "data": {
      "namespace_id": "string",
      "description": "string",
      "genesis": "string (ISO timestamp)",
      "created_by": "string",
      "version": "string",
      "version_count": "number",
      "proof": {
        "type": "string",
        "namespace_did": "string",
        "creator_did": "string",
        "digest": "string (hex)",
        "network_genesis": "string (hex)"
      }
    }
  }
}
```

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/verify-namespace-lookup', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'Authorization': 'Bearer your-api-key'
  },
  body: JSON.stringify({
    namespace_lookup_response: namespaceResponse
  })
});

const verificationResult = await response.json();
```

**Success Response (200):**
```json
{
  "message": "Verification successful"
}
```

**Failed Verification (400):**
```json
{
  "message": "Verification failed"
}
```

### Get Namespace Verification Logs

Retrieve audit trail of namespace verification activities.

**Endpoint:** `GET /dedi/{namespace}/get-namespace-verification-logs`

**Parameters:**
- `namespace` (path, required): Namespace ID or domain to get logs for

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/acme-university/get-namespace-verification-logs', {
  method: 'GET',
  headers: {
    'Authorization': 'Bearer your-api-key'
  }
});

const logs = await response.json();
```

**Success Response (200):**
```json
{
  "message": "Verification logs fetched successfully",
  "data": {
    "verification_count": 5,
    "logs": [
      {
        "id": "vt_62...",
        "lookup_type": "namespace",
        "verified_id": "76EU...",
        "verified_by": "admin@acme.edu",
        "result": "success",
        "created_at": "2024-01-15T14:30:00Z",
        "request": {
          "message": "Resource retrieved successfully",
          "data": { "..." }
        }
      },
      ...
    ]
  }
}
```

## Registry Verification APIs

### Verify Registry Lookup

Cryptographically verify the integrity of a registry lookup response.

**Endpoint:** `POST /dedi/verify-registry-lookup`

**Request Body:**
```json
{
  "registry_lookup_response": {
    "message": "string",
    "data": {
      "namespace_id": "string",
      "registry_id": "string",
      "registry_name": "string",
      "description": "string",
      "schema": "object (JSON schema)",
      "tag": "string",
      "version": "string",
      "version_count": "number",
      "state": "string",
      "genesis": "string (ISO timestamp)",
      "created_by": "string",
      "meta": "object",
      "ttl": "number",
      "proof": {
        "type": "string",
        "namespace_did": "string",
        "registry_identifier": "string",
        "creator_did": "string",
        "digest": "string (hex)",
        "network_genesis": "string (hex)"
      }
    }
  }
}
```

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/verify-registry-lookup', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'Authorization': 'Bearer your-api-key'
  },
  body: JSON.stringify({
    registry_lookup_response: registryResponse
  })
});

const verificationResult = await response.json();
```

**Success Response (200):**
```json
{
  "message": "Verification successful"
}
```

### Get Registry Verification Logs

Retrieve audit trail of registry verification activities.

**Endpoint:** `GET /dedi/{namespace}/{registry_name}/get-registry-verification-logs`

**Parameters:**
- `namespace` (path, required): Namespace ID or domain
- `registry_name` (path, required): Registry name

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/acme-university/student-records/get-registry-verification-logs', {
  method: 'GET',
  headers: {
    'Authorization': 'Bearer your-api-key'
  }
});

const logs = await response.json();
```

**Success Response (200):**
```json
{
  "message": "Verification logs fetched successfully",
  "data": {
    "verification_count": 3,
    "logs": [
      {
        "id": "vt_62...",
        "lookup_type": "registry",
        "verified_id": "76EU...",
        "verified_by": "registrar@acme.edu",
        "result": "success",
        "created_at": "2024-01-15T15:45:00Z",
        "request": {
          "message": "Resource retrieved successfully",
          "data": { "..." }
        }
      },
      ...
    ]
  }
}
```

## Record Verification APIs

### Verify Record Lookup

Cryptographically verify the integrity of a record lookup response.

**Endpoint:** `POST /dedi/verify-record-lookup`

**Request Body:**
```json
{
  "record_lookup_response": {
    "message": "string",
    "data": {
      "namespace_id": "string",
      "registry_id": "string",
      "registry_name": "string",
      "record_id": "string",
      "record_name": "string",
      "description": "string",
      "details": "object (record data)",
      "version": "string",
      "version_count": "number",
      "state": "string",
      "genesis": "string (ISO timestamp)",
      "created_by": "string",
      "meta": "object",
      "ttl": "number",
      "proof": {
        "type": "string",
        "namespace_did": "string",
        "registry_identifier": "string",
        "record_identifier": "string",
        "creator_did": "string",
        "digest": "string (hex)",
        "network_genesis": "string (hex)"
      }
    }
  }
}
```

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/verify-record-lookup', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'Authorization': 'Bearer your-api-key'
  },
  body: JSON.stringify({
    record_lookup_response: recordResponse
  })
});

const verificationResult = await response.json();
```

**Success Response (200):**
```json
{
  "message": "Verification successful"
}
```

### Get Record Verification Logs

Retrieve audit trail of record verification activities.

**Endpoint:** `GET /dedi/{namespace}/{registry_name}/{record_name}/get-record-verification-logs`

**Parameters:**
- `namespace` (path, required): Namespace ID or domain
- `registry_name` (path, required): Registry name
- `record_name` (path, required): Record name

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/acme-university/student-records/john-doe-2024/get-record-verification-logs', {
  method: 'GET',
  headers: {
    'Authorization': 'Bearer your-api-key'
  }
});

const logs = await response.json();
```

**Success Response (200):**
```json
{
  "message": "Verification logs fetched successfully",
  "data": {
    "verification_count": 7,
    "logs": [
      {
        "id": "vt_62...",
        "lookup_type": "record",
        "verified_id": "76EU...",
        "verified_by": "verifier@acme.edu",
        "result": "success",
        "created_at": "2024-01-15T16:20:00Z",
        "request": {
          "message": "Resource retrieved successfully",
          "data": { "..." }
        }
      },
      ...
    ]
  }
}
```

## Practical Usage Examples

### Verifying Critical Data Before Processing

Instead of implementing digest calculation yourself, use DeDi.global's verification APIs to ensure data integrity:

#### Academic Record Verification Workflow

```typescript
// Step 1: Perform a standard record lookup
async function getStudentRecord(namespace: string, registry: string, recordName: string) {
  const lookupResponse = await fetch(`https://api.dedi.global/dedi/lookup/${namespace}/${registry}/${recordName}`, {
    headers: { 'Authorization': 'Bearer your-api-key' }
  });
  
  return await lookupResponse.json();
}

// Step 2: Verify the lookup response before trusting the data
async function verifyAndProcessRecord(namespace: string, registry: string, recordName: string) {
  try {
    // Get the record data
    const recordLookup = await getStudentRecord(namespace, registry, recordName);
    
    // Verify the data integrity using DeDi.global's verification API
    const verificationResponse = await fetch('https://api.dedi.global/dedi/verify-record-lookup', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'Authorization': 'Bearer your-api-key'
      },
      body: JSON.stringify({
        record_lookup_response: recordLookup
      })
    });
    
    const verificationResult = await verificationResponse.json();
    
    if (verificationResponse.ok) {
      console.log('✓ Record verified successfully');
      // Process the verified data
      processStudentRecord(recordLookup.data);
    } else {
      console.error('✗ Verification failed:', verificationResult.message);
      // Handle verification failure
      handleVerificationFailure(recordLookup);
    }
    
  } catch (error) {
    console.error('Error in verification workflow:', error);
  }
}

function processStudentRecord(recordData: any) {
  // Now you can safely process the verified data
  console.log('Processing verified student record:', recordData.record_name);
  // Your business logic here...
}

function handleVerificationFailure(recordData: any) {
  // Log the failure and take appropriate action
  console.warn('Data integrity check failed for record:', recordData.data?.record_name);
  // Maybe retry, notify administrators, or reject the data
}
```

#### Registry Schema Validation Workflow

```typescript
// Verify registry before creating records
async function safeRegistryOperations(namespace: string, registryName: string) {
  try {
    // Look up the registry
    const registryResponse = await fetch(`https://api.dedi.global/dedi/lookup/${namespace}/${registryName}`, {
      headers: { 'Authorization': 'Bearer your-api-key' }
    });
    const registryLookup = await registryResponse.json();
    
    // Verify registry integrity
    const verificationResponse = await fetch('https://api.dedi.global/dedi/verify-registry-lookup', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'Authorization': 'Bearer your-api-key'
      },
      body: JSON.stringify({
        registry_lookup_response: registryLookup
      })
    });
    
    if (verificationResponse.ok) {
      console.log('✓ Registry schema verified');
      return registryLookup.data.schema; // Safe to use the schema
    } else {
      throw new Error('Registry verification failed');
    }
    
  } catch (error) {
    console.error('Registry verification error:', error);
    return null;
  }
}
```

### Audit Trail and Compliance Monitoring

```typescript
// Monitor verification activities for compliance
async function auditVerificationActivity(namespace: string) {
  try {
    // Get namespace verification logs
    const logsResponse = await fetch(`https://api.dedi.global/dedi/${namespace}/get-namespace-verification-logs`, {
      headers: { 'Authorization': 'Bearer your-api-key' }
    });
    
    const logs = await logsResponse.json();
    
    console.log(`Found ${logs.data.verification_count} verification attempts`);
    
    // Analyze verification patterns
    const failedVerifications = logs.data.logs.filter(log => log.result === 'failed');
    
    if (failedVerifications.length > 0) {
      console.warn(`⚠️  ${failedVerifications.length} failed verification attempts detected`);
      // Alert security team or take corrective action
    }
    
    return logs.data;
    
  } catch (error) {
    console.error('Audit retrieval error:', error);
  }
}
```

<!-- ### Batch Verification for Data Migration

```python
import asyncio
import aiohttp

async def verify_batch_records(namespace: str, registry: str, record_names: list):
    """Verify multiple records efficiently before data migration"""
    
    async with aiohttp.ClientSession() as session:
        verification_results = []
        
        for record_name in record_names:
            try:
                # Get record
                async with session.get(
                    f'https://api.dedi.global/dedi/lookup/{namespace}/{registry}/{record_name}',
                    headers={'Authorization': 'Bearer your-api-key'}
                ) as response:
                    lookup_data = await response.json()
                
                # Verify record
                async with session.post(
                    'https://api.dedi.global/dedi/verify-record-lookup',
                    headers={
                        'Content-Type': 'application/json',
                        'Authorization': 'Bearer your-api-key'
                    },
                    json={'record_lookup_response': lookup_data}
                ) as verify_response:
                    
                    verification_results.append({
                        'record_name': record_name,
                        'verified': verify_response.status == 200,
                        'data': lookup_data['data'] if verify_response.status == 200 else None
                    })
                    
            except Exception as e:
                verification_results.append({
                    'record_name': record_name,
                    'verified': False,
                    'error': str(e)
                })
        
        # Process results
        verified_records = [r for r in verification_results if r['verified']]
        failed_records = [r for r in verification_results if not r['verified']]
        
        print(f"✓ Verified: {len(verified_records)} records")
        print(f"✗ Failed: {len(failed_records)} records")
        
        return verified_records, failed_records

# Usage
async def main():
    verified, failed = await verify_batch_records(
        'acme-university', 
        'student-records', 
        ['john-doe-2024', 'jane-smith-2024', 'bob-wilson-2024']
    )
    
    # Proceed with migration only for verified records
    for record in verified:
        print(f"Safe to migrate: {record['record_name']}")
``` -->

### Real-Time Verification Middleware

```typescript
// Express.js middleware for automatic verification
function verificationMiddleware(entityType: 'namespace' | 'registry' | 'record') {
  return async (req: any, res: any, next: any) => {
    const lookupResponse = res.locals.lookupData; // Assume lookup data is stored here
    
    if (!lookupResponse) {
      return next();
    }
    
    try {
      const verifyEndpoint = `https://api.dedi.global/dedi/verify-${entityType}-lookup`;
      const requestBody = { [`${entityType}_lookup_response`]: lookupResponse };
      
      const verificationResponse = await fetch(verifyEndpoint, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
          'Authorization': 'Bearer your-api-key'
        },
        body: JSON.stringify(requestBody)
      });
      
      if (verificationResponse.ok) {
        res.locals.verified = true;
        console.log(`✓ ${entityType} verification passed`);
      } else {
        res.locals.verified = false;
        console.warn(`✗ ${entityType} verification failed`);
      }
      
    } catch (error) {
      console.error('Verification middleware error:', error);
      res.locals.verified = false;
    }
    
    next();
  };
}

// Usage in routes
app.get('/api/student/:recordName', 
  lookupRecord,              // First get the data
  verificationMiddleware('record'), // Then verify it
  (req, res) => {
    if (res.locals.verified) {
      res.json(res.locals.lookupData);
    } else {
      res.status(400).json({ error: 'Data verification failed' });
    }
  }
);
```

These examples show how to integrate DeDi.global's verification APIs into your application workflow without needing to implement the complex cryptographic verification logic yourself. The APIs handle all the digest calculation, serialization, and comparison internally, providing you with a simple pass/fail result that you can act upon.

## Error Handling

### Common Error Responses

**Missing Lookup Response (400):**
```json
{
  "message": "Invalid input: namespace_lookup_response is missing"
}
```

**Authentication Required (401):**
```json
{
  "message": "Authentication failed"
}
```

**User Not Found (404):**
```json
{
  "message": "User not found"
}
```

**Verification Failed (400):**
```json
{
  "message": "Verification failed"
}
```

## Best Practices

### Security Guidelines

1. **Always Verify Responses**: Verify critical lookup responses before trusting the data
2. **Implement Local Verification**: Use client-side verification for additional security
3. **Log Verification Results**: Maintain audit trails of all verification attempts
4. **Handle Verification Failures**: Implement proper error handling for failed verifications
5. **Regular Verification**: Periodically re-verify critical data

### Performance Optimization

1. **Cache Verification Results**: Cache verification results for frequently accessed data
2. **Batch Verification**: Verify multiple responses in batch operations when possible
3. **Selective Verification**: Verify only critical data paths based on risk assessment
4. **Background Verification**: Perform verification asynchronously when immediate results aren't required

### Integration Patterns

1. **Middleware Integration**: Implement verification as middleware in your application
2. **Webhook Verification**: Verify webhook payloads before processing
3. **API Gateway Integration**: Add verification layers at API gateway level
4. **Event-Driven Verification**: Trigger verification based on specific events or conditions

The Lookup Verification APIs provide robust cryptographic verification capabilities, ensuring that data retrieved from DeDi.global maintains its integrity and authenticity throughout your application's data flow.
