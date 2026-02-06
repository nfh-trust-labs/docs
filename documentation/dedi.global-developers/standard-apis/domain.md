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

### 2. **Domain Whitelisting**
Before generating DNS TXT records, your domain must be whitelisted in the DeDi.global system:

> **📞 Contact Required**: To get your domain whitelisted for verification, please reach out to the DeDi.global team through our support channels. This security measure prevents abuse and ensures domain legitimacy.

### 3. **DNS TXT Generation**
Generate a unique verification string using the [Generate DNS TXT API](#generate-dns-txt-record):

```typescript
const txtResponse = await fetch('https://api.dedi.global/dedi/generate-dns-txt/your-namespace-id/techcorp.com', {
  method: 'GET',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  }
});
```

### 4. **DNS Configuration**
Add the generated TXT record to your domain's DNS settings through your domain provider (GoDaddy, Cloudflare, Route 53, etc.):

```
Record Type: TXT
Host: @ (or your domain)
Value: [generated verification string] <-- make sure you copy the entire DNS TXT(i.e. dedi-verification=abcdefg1234567)
TTL: 300 (or minimum allowed)
```

### 5. **Verification**
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
- `domain` (path, required): Domain to verify (must be whitelisted in global registry)

**Validation Rules:**
- Domain must contain a dot (valid format)
- Domain must exist in DeDi.global's whitelisted registry
- Namespace must exist and not be already verified with another domain
- Domain cannot be already verified by another namespace

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/generate-dns-txt/did:cord:3yVazTQMc.../techcorp.com', {
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

**Error Responses:**
- `400` - Invalid domain format, domain not in global registry, or namespace already verified
- `404` - Namespace not found
- `500` - Failed to generate verification string

### Verify Domain Ownership

Verify domain ownership by checking the TXT record in DNS.

**Endpoint:** `POST /dedi/verify-domain`

**Request Body:**
```typescript
{
  namespace_id: string; // Namespace ID to verify
}
```

**Prerequisites:**
- TXT record must be generated first using the Generate DNS TXT API
- TXT record must be added to domain's DNS settings
- DNS propagation should be complete (allow 5-30 minutes)

**Verification Process:**
1. Validates namespace exists and has an associated domain
2. Retrieves the generated TXT record from database
3. Performs DNS TXT record lookup on the domain
4. Compares found TXT records with generated verification string
5. Updates namespace and domain verification status if match found

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
- `400` - Namespace ID missing, domain already in use, or namespace already verified
- `404` - Namespace not found, TXT not generated, or TXT record not found in DNS
- `500` - DNS resolution errors

**Important Notes:**
- DNS changes may take time to propagate (typically 5-30 minutes, up to 48 hours in rare cases)
- Only one domain can be verified per namespace
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
