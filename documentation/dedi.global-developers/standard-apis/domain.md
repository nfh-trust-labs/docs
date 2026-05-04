# Domain Verification APIs

Domain verification is a critical trust mechanism that enhances the credibility and authenticity of namespaces on DeDi.global. By proving ownership of a domain, organizations can establish verified namespaces that users can trust.

**Authentication**: 🔒 All Domain Verification APIs require authentication via API key.

## Why Domain Verification Matters

### Trust Through Domain Ownership

Domains are the fundamental source of identity on the internet. They represent a unique, globally recognized identifier that only the legitimate owner can control. The internet's trust infrastructure is built around domain ownership - when you visit `microsoft.com`, you trust that Microsoft Corporation owns and controls that domain.

DeDi.global leverages this established trust mechanism to bring the same level of confidence to decentralized data. By allowing namespace owners to attach verified domains to their namespaces, we create a bridge between traditional web trust and decentralized identity.

### The Verification Advantage

Consider a scenario where multiple organizations create namespaces with similar names like "TechCorp", "TechCorp-Official", or "TechCorp-Inc". Without domain verification, users have no reliable way to distinguish between legitimate and potentially fraudulent namespaces.

However, when the legitimate TechCorp organization verifies their namespace using their domain `techcorp.com`, they gain:

- **🎯 Authentic Identity**: Clear proof of legitimacy through domain ownership
- **⭐ Enhanced Trust**: Verified status increases user confidence and adoption
- **🛡️ Brand Protection**: Differentiation from potentially misleading similar namespaces
- **📈 Better Discovery**: Verified namespaces receive priority in search and recommendations

### Real-World Example

Imagine three namespaces exist:
- `global-bank` (unverified)
- `globalbank-official` (unverified) 
- `global-bank-verified` (verified with `globalbank.com`)

Users interacting with financial data would naturally trust the domain-verified namespace, knowing that only the real Global Bank can control the `globalbank.com` domain.

## How Domain Verification Works

The verification process follows a secure, industry-standard DNS-based validation:

### 1. **Namespace Creation**
First, create your namespace using the [Create Namespace API](./publish.md#create-namespace):

```typescript
const namespaceResponse = await fetch('https://api.dedi.global/dedi/create-namespace', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    name: 'techcorp-data',
    description: 'TechCorp official data namespace',
    meta: {
      company: 'TechCorp Inc.',
      industry: 'Technology'
    }
  })
});
```

### 2. **Choose a Verification Method**

DeDi.global supports three verification flows:

- `self_dns` - prove ownership by publishing the TXT value in DNS
- `self_http` - prove ownership by serving the TXT value from `https://<domain>/.well-known/dedi-verification.txt`
- `request_other_namespace` - request an already self-verified namespace to attest your domain

For the self-managed flows (`self_dns` and `self_http`), the domain must still be present in the DeDi.global global registry.

### 3. **Domain Whitelisting / Global Registry Check**
Before generating self-managed verification records, your domain must be available in the DeDi.global global registry:

> **📞 Contact Required**: To get your domain whitelisted for verification, please reach out to the DeDi.global team through our support channels. This security measure prevents abuse and ensures domain legitimacy.

### 4. **Verification Record Generation**
Generate a unique verification string using the [Generate DNS TXT API](#generate-dns-txt-record):

```typescript
const txtResponse = await fetch('https://api.dedi.global/dedi/generate-dns-txt/your-namespace-id/techcorp.com?verification_method=self_dns', {
  method: 'GET',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  }
});
```

### 5. **Publish the Proof**
For `self_dns`, add the generated TXT record to your domain's DNS settings through your domain provider (GoDaddy, Cloudflare, Route 53, etc.):

```
Record Type: TXT
Host: @ (or your domain)
Value: [generated verification string] <-- make sure you copy the entire DNS TXT(i.e. dedi-verification=abcdefg1234567)
TTL: 300 (or minimum allowed)
```

For `self_http`, expose the same TXT value at:

```text
https://<your-domain>/.well-known/dedi-verification.txt
```

For `request_other_namespace`, the generate step creates a pending request for the target namespace delegates.

### 6. **Verification**
Once DNS propagation is complete (usually 5-30 minutes), verify your domain using the [Verify Domain API](#verify-domain-ownership):

```typescript
const verifyResponse = await fetch('https://api.dedi.global/dedi/verify-domain', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    namespace_id: 'your-namespace-id'
  })
});
```

## Domain Verification APIs

### Generate DNS TXT Record

Generate a unique DNS TXT record for domain verification.

**Endpoint:** `GET /dedi/generate-dns-txt/{namespace_id}/{domain}`

**Parameters:**
- `namespace_id` (path, required): Target namespace identifier
- `domain` (path, required): Domain to verify
- `verification_method` (query or body, required): One of `self_dns`, `self_http`, or `request_other_namespace`
- `target_namespace` (query or body, required for `request_other_namespace`): Namespace ID or verified domain of the attesting namespace

**Validation Rules:**
- Domain must contain a dot (valid format)
- Namespace must exist
- Domain cannot already be actively verified by another namespace
- `self_dns` and `self_http` require the domain to be present in the DeDi.global global registry
- `request_other_namespace` requires a self-verified target namespace

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/generate-dns-txt/did:cord:3yVazTQMc.../techcorp.com?verification_method=self_dns', {
  method: 'GET',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  }
});

const data = await response.json();
```

**Success Response (200):**
```typescript
{
  "message": "Generated domain's DNS TXT record",
  "txt": "dedi-verification=abcdefg1234567"
}
```

**TXT Record Already Exists (200):**
```typescript
{
  "message": "TXT is already generated",
  "data": {
    "txt": "dedi-verification=abcdefg1234567"
  }
}
```

**Delegated Verification Request Created (200):**
```typescript
{
  "message": "Verification request created",
  "data": {
    "request_id": "uuid",
    "txt": "dedi-verification=abcdefg1234567",
    "target_namespace": "did:cord:..."
  }
}
```

**Error Responses:**
- `400` - Invalid domain format, invalid verification method, missing `target_namespace`, or domain/namespace state conflict
- `404` - Namespace not found or target namespace not found
- `500` - Failed to generate verification string

### Verify Domain Ownership

Verify domain ownership by checking the TXT record in DNS.

**Endpoint:** `POST /dedi/verify-domain`

**Request Body:**
```typescript
{
  namespace_id: string; // Namespace ID to verify
  domain?: string;      // Optional when multiple self-verification records exist
}
```

**Prerequisites:**
- TXT record must be generated first using the Generate DNS TXT API
- For `self_dns`, TXT record must be added to domain DNS
- For `self_http`, the TXT value must be served from `/.well-known/dedi-verification.txt`
- DNS propagation should be complete (allow 5-30 minutes)

**Verification Process:**
1. Validates the namespace exists
2. Retrieves the generated self-verification record
3. Attempts DNS TXT verification first
4. Falls back to HTTP verification at `/.well-known/dedi-verification.txt` if DNS verification does not succeed
5. Updates namespace and domain verification status if a proof matches

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/verify-domain', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    namespace_id: 'did:web:did.cord.network:76E...'
  })
});

const data = await response.json();
```

**Success Response (200):**
```typescript
{
  "message": "Verification Successful"
}
```

**Error Responses:**
- `400` - Namespace ID missing, verification file content mismatch, or domain is already in use
- `404` - Namespace not found or no self-verification TXT has been generated
- `500` - Internal verification errors

**Important Notes:**
- DNS changes may take time to propagate (typically 5-30 minutes, up to 48 hours in rare cases)
- HTTP verification is attempted after DNS verification if DNS does not match
- Only self-managed verification records are verified through this endpoint
- Verified domains cannot be shared between multiple namespaces

### Check Verification Status

Check the current verification status of a namespace's associated domain.

**Endpoint:** `GET /dedi/check-verification/{namespace_id}`

**Parameters:**
- `namespace_id` (path, required): Target namespace ID to check

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/check-verification/did:web:did.cord.network:76E...', {
  method: 'GET',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  }
});

const data = await response.json();
```

**Verified Namespace Response (200):**
```typescript
{
  "verified": true,
  "domain": "techcorp.com"
}
```

**Unverified Namespace Response (200):**
```typescript
{
  "verified": false
}
```

**Error Responses:**
- `400` - Namespace ID required
- `404` - Namespace not found
- `500` - Internal server error

**Use Cases:**
- Check if domain verification is complete before proceeding with operations
- Retrieve the verified domain name for a namespace
- Display verification status in user interfaces
- Validate namespace credibility before trusting data

### Get DNS TXT Record

Retrieve the generated DNS TXT record and verification details for a namespace.

**Endpoint:** `GET /dedi/get-dns-txt/{namespace_id}`

**Parameters:**
- `namespace_id` (path, required): Target namespace identifier

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/get-dns-txt/did:web:did.cord.network:76E...', {
  method: 'GET',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  }
});

const data = await response.json();
```

**Success Response (200):**
```typescript
{
  "namespace_id": "did:web:did.cord.network:76E...",
  "domain": "techcorp.com",
  "txt": "dedi-verification=abcdefg1234567",
  "is_verified": true
}
```

**Error Responses:**
- `400` - Namespace ID required
- `404` - Domain not found for namespace (TXT record not generated)
- `500` - Internal server error

**Use Cases:**
- Retrieve TXT record after generation for DNS configuration
- Check domain and current verification status
- Audit domain verification setup
- Get verification string for manual DNS configuration
- Troubleshoot verification issues

### Remove Namespace Verification

Remove domain verification from a namespace, reverting it to unverified status.

**Endpoint:** `POST /dedi/{namespace}/{domain}/remove-namespace-verification`

**Parameters:**
- `namespace` (path, required): Namespace ID to remove verification from
- `domain` (path, required): Domain whose verification should be removed

**Prerequisites:**
- Namespace must exist
- Domain must be associated with the namespace either through self-verification or an accepted delegated verification request

**Process:**
1. Validates the namespace exists
2. Removes matching domain-verification entries and accepted/pending delegated requests for that domain
3. Recomputes namespace verification state
4. Clears the domain from the namespace only if no active verification source remains

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/did:web:did.cord.network:76E.../techcorp.com/remove-namespace-verification', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  }
});

const data = await response.json();
```

**Success Response (200):**
```typescript
{
  "message": "Namespace verification removed successfully",
  "data": {
    "domain": "techcorp.com",
    "verification_methods": ["self_dns"],
    "removed_targets": [],
    "removed_entries": 1,
    "domain_removed_from_namespace": true,
    "domain_still_verified": false
  }
}
```

**Error Responses:**
- `400` - Namespace ID or domain parameter missing
- `404` - Namespace not found or no verification data exists for the provided namespace/domain pair
- `500` - Internal server error while removing verification

**Use Cases:**
- Decommission domain verification when changing domain strategy
- Reset namespace to unverified state for re-verification with different domain
- Remove verification due to domain ownership changes
- Clean up verification when domain is no longer controlled by namespace owner
- Prepare namespace for transfer to different domain owner

**Important Notes:**
- Removing verification does not delete the namespace itself
- If multiple verification sources still exist for the same domain, the namespace may remain verified
- The namespace can be re-verified with the same or different domain later

## Verification Request APIs

Delegated domain verification uses verification requests and notification APIs.

### List Pending Verification Notifications

**Endpoint:** `GET /dedi/notifications/pending`

**Success Response (200):**

```json
{
  "message": "Unread verification notifications retrieved successfully",
  "unread_count": 2,
  "data": [
    {
      "id": "vrn_...",
      "request_id": "vr_...",
      "namespace_id": "did:cord:...",
      "target_namespace_id": "did:cord:...",
      "recipient_email": "delegate@example.com",
      "actor_email": null,
      "domain": "example.com",
      "event_type": "pending",
      "title": "Verification request pending",
      "message": "A namespace has requested domain attestation.",
      "metadata": {},
      "is_read": false,
      "read_at": null,
      "created_at": "2026-05-04T08:30:00.000Z",
      "updated_at": "2026-05-04T08:30:00.000Z"
    }
  ]
}
```

### List Notification History

**Endpoint:** `GET /dedi/notifications/history`

**Success Response (200):**

```json
{
  "message": "Verification notification history retrieved successfully",
  "data": [
    {
      "id": "vrn_...",
      "request_id": "vr_...",
      "recipient_email": "delegate@example.com",
      "domain": "example.com",
      "event_type": "accepted",
      "is_read": true,
      "read_at": "2026-05-04T08:45:00.000Z"
    }
  ]
}
```

### Mark a Notification as Read

**Endpoint:** `POST /dedi/notifications/{notification_id}/read`

**Request Body:** None

**Success Response (200):**

```json
{
  "message": "Notification marked as read",
  "data": {
    "id": "vrn_...",
    "is_read": true,
    "read_at": "2026-05-04T08:45:00.000Z"
  }
}
```

### Mark All Notifications as Read

**Endpoint:** `POST /dedi/notifications/read-all`

**Request Body:** None

**Success Response (200):**

```json
{
  "message": "All notifications marked as read",
  "data": {
    "affected": 2
  }
}
```

### Accept a Verification Request

**Endpoint:** `POST /dedi/verification-requests/{request_id}/accept`

**Request Body:** None

**Success Response (200):**

```json
{
  "message": "Verification request accepted",
  "data": {
    "request_id": "vr_...",
    "registry_id": "did:cord:registry:...",
    "record_id": "did:cord:record:..."
  }
}
```

### Reject a Verification Request

**Endpoint:** `POST /dedi/verification-requests/{request_id}/reject`

**Request Body:** None

**Success Response (200):**

```json
{
  "message": "Verification request rejected"
}
```

## Domain Re-verification

Verified domains are re-checked on a scheduled basis.

- Self-managed verification sources (`self_dns`, `self_http`) and accepted delegated verification sources (`request_other_namespace`) participate in re-verification.
- The current implementation re-verifies active sources after a configurable interval. The default interval is `1` year.
- If a re-verification attempt fails, the source enters a configurable grace period. The default grace period is `7` days.
- If the verification source is restored during the grace period, the source remains active.
- If the source still fails after the grace period, that verification source is removed.
- If no active verification source remains for the domain, the domain is removed from the namespace verification state.

## Best Practices

### DNS Configuration

1. **Use Minimal TTL**: Set TTL to 300 seconds (5 minutes) during verification for faster propagation
2. **Test Before Verification**: Use online DNS checker tools to confirm TXT record is visible
3. **Allow Propagation Time**: Wait 5-30 minutes after adding TXT record before attempting verification

### Security Considerations

1. **Domain Control**: Only add TXT records if you have legitimate control of the domain
2. **Monitor Verification Status**: Regularly check verification status to ensure it remains valid

### Troubleshooting

**Common Issues:**
- **DNS Propagation Delay**: Wait longer and try verification again
- **Incorrect TXT Record**: Ensure the entire and exact string is copied without modifications
- **Domain Not Whitelisted**: Contact DeDi.global support to whitelist your domain
- **Multiple TXT Records**: Some DNS providers require specific formatting for multiple TXT records

**Verification Failed?**
1. Check TXT record is correctly added to DNS
2. Verify domain propagation using online DNS tools
3. Ensure namespace exists and hasn't been verified with another domain
4. Contact support if domain should be whitelisted but isn't recognized
