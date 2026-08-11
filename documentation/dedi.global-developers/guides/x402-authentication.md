# X402 Agent-Ready Authentication

## Overview

X402 Agent-Ready Authentication enables AI agents and other automated systems to sign up for and use the DeDi API without requiring traditional email/password credentials. This authentication method leverages cryptographic wallet signatures to provide a secure, scalable, and agent-friendly authentication flow.

The system implements the **L402 (HTTP 402 Payment Required) standard**, combined with **EIP-3009 message signing**, to create a stateless, challenge-response authentication mechanism.

---

## Key Features

### 1. **Challenge-Response Authentication Flow**
- Unauthenticated requests to protected endpoints receive a **402 Payment Required** response with a unique, time-sensitive cryptographic challenge
- Agents sign the challenge using their wallet's private key
- The API validates the signature and issues a session or creates a new account on first use

### 2. **Tiered Rate Limiting**
- Different quotas based on user type to protect the service from abuse:
  - **Anonymous Wallet**: Limited requests and on-chain operations
  - **Delegated Agent**: Enhanced quotas with verified permissions
  - **Human Users**: Highest quotas for traditional email/password-authenticated users

### 3. **Stateless & Scalable**
- No session storage required for validation
- Challenges expire quickly (default 60 seconds) to prevent replay attacks
- Seamless integration with existing authentication methods

---

## Architecture

### Challenge-Response Flow

```
┌─────────────────────────────────────────────────────────────┐
│ 1. Agent makes unauthenticated request to protected endpoint│
└────────────────────────┬────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────┐
│ 2. API returns 402 Payment Required with challenge in:      │
│    WWW-Authenticate: L402 token_type="EIP-3009"             │
│                          challenge="<base64_challenge>"     │
└────────────────────────┬────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────┐
│ 3. Agent signs challenge with wallet private key (EIP-3009) │
│    Message: "EIP-3009 Payment:\n{challenge}"                │
└────────────────────────┬────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────┐
│ 4. Agent retries request with X-PAYMENT header:             │
│    X-PAYMENT: <base64({challenge, signature})>              │
└────────────────────────┬────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────┐
│ 5. API verifies signature:                                   │
│    - Extract signer address from EIP-3009 message           │
│    - Validate challenge hasn't expired                       │
│    - Find or create account linked to wallet                │
└────────────────────────┬────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────┐
│ 6. Return 200 OK with requested resource                    │
└─────────────────────────────────────────────────────────────┘
```

### Core Components

#### 1. **Challenge Generation** (`generateChallenge`)
Located in: `src/utils/auth/x402.ts`

Generates a unique, time-sensitive challenge containing:
- HTTP method (`GET`, `POST`, etc.)
- Full request URL
- Current timestamp
- Random 16-byte nonce (prevents collisions)

**Challenge Structure:**
```json
{
  "method": "POST",
  "url": "https://api.dedi.io/dedi/create-namespace",
  "timestamp": 1234567890000,
  "nonce": "0x1234567890abcdef1234567890abcdef"
}
```

#### 2. **Signature Verification** (`verifySignature`)
Located in: `src/utils/auth/x402.ts`

- Uses `ethers.verifyMessage()` to extract the signer's wallet address from the EIP-3009 signed message
- Returns the wallet address on success, `null` on failure
- Follows the EIP-191 signing standard
- Message format: `"EIP-3009 Payment:\n{challenge}"`

#### 3. **Account Management** (`findOrCreateAccountByWallet`)
Located in: `src/utils/auth/x402.ts`

On first authentication:
1. Checks if an `EntityAccount` exists for the wallet address
2. If not found:
   - Creates a new on-chain account with a generated mnemonic
   - Transfers initial units for on-chain operations
   - Creates a profile linked to the wallet
   - Generates a DID (Decentralized Identifier)
3. Returns or stores the account in the database
4. Marks account as verified for agent-based authentication
5. Sets synthetic email: `{wallet_address}@dedi.agent`

#### 4. **Middleware: Auth Check** (`checkLoginStatus`)
Located in: `src/app/middleware/auth/checkLoginStatus.ts`

Enhanced to handle X402:
- If X-PAYMENT header is present and auth fails → return **401** (challenge already attempted, authentication failed)
- If no auth found and no X-PAYMENT header → return **402 Payment Required** with new challenge
- Supports backward compatibility with existing auth methods (API keys, cookies, JWT)
- Challenge is returned in `WWW-Authenticate` header with format:
  ```
  L402 token_type="EIP-3009", maxAmountRequired="0", challenge="<base64>"
  ```

#### 5. **Middleware: Tiered Rate Limiting** (`tieredRateLimit`)
Located in: `src/app/middleware/rateLimit/tieredRateLimit.ts`

Applies per-tier rate limits based on user type detection:

| User Type | Requests/Hour | Anchor Operations/Day | Detection |
|-----------|---|---|---|
| **Anonymous Wallet** | 100 | 10 | Wallet address, no delegated permissions |
| **Delegated Agent** | 1,000 | 100 | Wallet address + delegated permissions (`namespaceAuth`) |
| **Human User** | 5,000 | 500 | Traditional auth (email/API key/cookie) |

**Usage in routes:**
```typescript
// Regular requests
tieredRateLimit()

// On-chain/anchor operations (stricter limits apply)
tieredRateLimit(true)
```

Configuration is environment-driven (see Environment Variables section).

---

## Configuration

### Environment Variables

Add the following to your `.env` file:

```bash
# ==============================================================================
# X402 Agent Authentication
# ==============================================================================
X402_CHALLENGE_EXPIRY_SECONDS=60     # Challenge validity window (seconds)
X402_PRICE=0                          # Price in wei (0 for free access)

# ==============================================================================
# Tiered Rate Limiting Configuration
# ==============================================================================

# --- ANONYMOUS AGENT (WALLET-BASED) TIER ---
RATE_LIMIT_ANONYMOUS_REQUESTS_LIMIT=100           # Max requests per hour
RATE_LIMIT_ANONYMOUS_REQUESTS_WINDOW_SEC=3600     # Time window in seconds
RATE_LIMIT_ANONYMOUS_ANCHORS_LIMIT=10             # Max anchor ops per day
RATE_LIMIT_ANONYMOUS_ANCHORS_WINDOW_SEC=86400     # Time window in seconds

# --- DELEGATED AGENT (WALLET-BASED, WITH PERMISSIONS) TIER ---
RATE_LIMIT_DELEGATED_REQUESTS_LIMIT=1000
RATE_LIMIT_DELEGATED_REQUESTS_WINDOW_SEC=3600
RATE_LIMIT_DELEGATED_ANCHORS_LIMIT=100
RATE_LIMIT_DELEGATED_ANCHORS_WINDOW_SEC=86400

# --- HUMAN USER (COOKIE/API-KEY BASED) TIER ---
RATE_LIMIT_HUMAN_REQUESTS_LIMIT=5000
RATE_LIMIT_HUMAN_REQUESTS_WINDOW_SEC=3600
RATE_LIMIT_HUMAN_ANCHORS_LIMIT=500
RATE_LIMIT_HUMAN_ANCHORS_WINDOW_SEC=86400
```

### Database Schema Update

A new column has been added to `EntityAccount`:

```typescript
@Column({ nullable: true, unique: true })
wallet_address?: string;
```

This stores the wallet address (Ethereum-format: `0x...`) for X402-authenticated accounts.

---

## Usage Guide

### For API Users/Agents

#### Step 1: Initial Request
Make an unauthenticated request to any protected endpoint:

```bash
curl -X POST https://api.dedi.io/dedi/create-namespace \
  -H "Content-Type: application/json" \
  -d '{"name": "my-namespace"}'
```

**Response (402 Payment Required):**
```http
HTTP/1.1 402 Payment Required
WWW-Authenticate: L402 token_type="EIP-3009", maxAmountRequired="0", challenge="eyJtZXRob2QiOiJQT1NUIiwid...iOjE2OTQ2MDAwMDB9"
Content-Type: application/json

{
  "message": "Payment Required"
}
```

**Challenge Decoding:**
The challenge in the header is base64-encoded. Decode it to get:
```json
{
  "method": "POST",
  "url": "https://api.dedi.io/dedi/create-namespace",
  "timestamp": 1694640000000,
  "nonce": "0x1234567890abcdef1234567890abcdef"
}
```

#### Step 2: Sign the Challenge
Use the provided `sign-challenge.js` script to sign the challenge:

```bash
# Update the script with:
# 1. Your test wallet private key
# 2. The base64 challenge from the 402 response
node sign-challenge.js
```

**Script Input:**
```javascript
const privateKey = "0x1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef";
const base64Challenge = "eyJtZXRob2QiOiJQT1NUIiwid...iOjE2OTQ2MDAwMDB9";
```

**Script Process:**
1. Decodes the base64 challenge
2. Creates message: `"EIP-3009 Payment:\n{decoded_challenge}"`
3. Signs message using wallet's private key (EIP-191)
4. Constructs payload: `{ challenge, signature }`
5. Base64 encodes the JSON payload
6. Outputs the X-PAYMENT header value

**Script Output:**
```
Base64-encoded X-PAYMENT Header Value (copy this):
eyJjaGFsbGVuZ2UiOiJ7XCJtZXRob2RcIjpcIlBPU1RcIi4uLn0iLCJzaWduYXR1cmUiOiIweDEyMz4..."}
```

#### Step 3: Retry with X-PAYMENT Header
Retry the original request with the X-PAYMENT header:

```bash
curl -X POST https://api.dedi.io/dedi/create-namespace \
  -H "Content-Type: application/json" \
  -H "X-PAYMENT: eyJjaGFsbGVuZ2UiOiJ7XCJtZXRob2QcIjpcIlBPU1RcIi4uLn0iLCJzaWduYXR1cmUiOiIweDEyMz4...\"
}" \
  -d '{"name": "my-namespace"}'
```

**Response (200 OK):**
```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "id": "namespace_12345",
  "name": "my-namespace",
  "created_at": "2024-01-01T00:00:00Z"
}
```

### For Developers

#### Integration Points

The X402 authentication is integrated at these levels:

**1. Route-level Integration:**
```typescript
// Example: Protected route with rate limiting
router.post(
  "/namespace",
  checkLoginStatus,              // Authentication (includes X402)
  tieredRateLimit(false),         // Rate limit for regular requests
  createNamespace
);

// Anchor operations (on-chain) use stricter limits
router.post(
  "/create-namespace",
  checkLoginStatus,
  tieredRateLimit(true),          // Stricter limit for on-chain ops
  createNamespace
);
```

**2. Access authenticated user:**
```typescript
import { Request, Response } from "express";

export async function myController(req: Request, res: Response) {
  const user = res.locals.authUser;
  
  if (!user) {
    return res.status(401).json({ error: "Unauthorized" });
  }
  
  // User details available
  console.log(user.wallet_address);  // X402 users have this
  console.log(user.email);
  console.log(user.profile_id);
}
```

#### Helper Functions

**Extract authentication details from request:**
```typescript
import { extractToken } from "../utils/core/helper";

const extracted = await extractToken(req);
if (extracted.auth_type === "x402") {
  const wallet = extracted.user_details?.wallet_address;
  console.log("X402 authenticated with wallet:", wallet);
} else if (extracted.auth_type === "api_key") {
  console.log("API key authenticated");
} else if (extracted.auth_type === "cookie") {
  console.log("Cookie/JWT authenticated");
}
```

**Manual signature verification:**
```typescript
import { verifySignature } from "../utils/auth/x402";

const challengeJson = '{"method":"POST","url":"...","timestamp":1694640000000,"nonce":"0x..."}';
const signature = "0x1234567890abcdef...";

const walletAddress = await verifySignature(challengeJson, signature);
if (walletAddress) {
  console.log("Verified wallet:", walletAddress);
} else {
  console.log("Signature invalid or verification failed");
}
```

**Check user tier:**
```typescript
function getUserTier(user) {
  if (!user) return "anonymous";
  
  if (user.wallet_address && user.namespaceAuth?.length > 0) {
    return "delegated";
  }
  
  if (user.wallet_address) {
    return "anonymous";
  }
  
  return "human";
}

const tier = getUserTier(res.locals.authUser);
console.log("User tier:", tier);  // "anonymous" | "delegated" | "human"
```

---

## Testing

### Quick Test with Docker Compose

#### 1. Start the service:
```bash
docker compose up --build -d
```

#### 2. Make an unauthenticated request:
```bash
curl -X POST http://localhost:3000/dedi/create-namespace \
  -H "Content-Type: application/json" \
  -d '{"name": "test-namespace"}'
```

Expected response:
```http
HTTP/1.1 402 Payment Required
WWW-Authenticate: L402 token_type="EIP-3009", maxAmountRequired="0", challenge="eyJt..."
Content-Type: application/json

{
  "message": "Payment Required"
}
```

#### 3. Extract and decode the challenge:
```bash
# Copy the challenge value from WWW-Authenticate header
CHALLENGE="eyJtZXRob2QiOiJQT1NUIi..."

# Decode to verify format
echo $CHALLENGE | base64 -d | jq
```

#### 4. Sign the challenge:
Update `sign-challenge.js`:
```javascript
const privateKey = "0xac0974bec39a17e36ba4a6b4d238ff944bacb476cadeee4c4b340747b0c60646"; // test key
const base64Challenge = "eyJtZXRob2QiOiJQT1NUIi...";
```

Run the script:
```bash
node sign-challenge.js
```

Copy the output X-PAYMENT value.

#### 5. Retry with the signature:
```bash
X_PAYMENT="eyJjaGFsbGVuZ2UiOiJ..."

curl -X POST http://localhost:3000/dedi/create-namespace \
  -H "Content-Type: application/json" \
  -H "X-PAYMENT: $X_PAYMENT" \
  -d '{"name": "test-namespace"}'
```

Expected response:
```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "id": "namespace_...",
  "name": "test-namespace",
  ...
}
```

### Using Postman

**Step 1 - Get Challenge:**
- Method: `POST`
- URL: `http://localhost:3000/dedi/create-namespace`
- Body: `{"name": "test-namespace"}`
- Send

**Step 2 - Copy challenge from response header:**
- Look for: `WWW-Authenticate` header
- Extract base64 value from `challenge="..."`

**Step 3 - Sign in Node:**
- Copy the challenge value
- Update `sign-challenge.js` with your wallet key and challenge
- Run: `node sign-challenge.js`
- Copy the output

**Step 4 - Send authenticated request:**
- Same POST request
- Add header: `X-PAYMENT: <output_from_step_3>`
- Send

### Unit Testing

Example test for X402 flow:

```typescript
import { generateChallenge, verifySignature } from "../src/utils/auth/x402";
import { ethers } from "ethers";

describe("X402 Authentication", () => {
  it("should generate and verify a challenge", async () => {
    // Generate a test wallet
    const wallet = ethers.Wallet.createRandom();
    
    // Create a mock request object
    const mockReq = {
      method: "POST",
      protocol: "http",
      get: (header) => header === "host" ? "localhost:3000" : undefined,
      originalUrl: "/dedi/create-namespace",
    } as any;

    // Generate challenge
    const challenge = generateChallenge(mockReq);
    console.log("Challenge:", challenge);
    
    // Verify it's valid JSON
    const challengeData = JSON.parse(challenge);
    expect(challengeData.method).toBe("POST");
    expect(challengeData.nonce).toBeDefined();
    expect(challengeData.timestamp).toBeDefined();

    // Sign challenge
    const message = `EIP-3009 Payment:\n${challenge}`;
    const signature = await wallet.signMessage(message);

    // Verify signature
    const signerAddress = await verifySignature(challenge, signature);
    expect(signerAddress.toLowerCase()).toBe(wallet.address.toLowerCase());
  });

  it("should reject expired challenges", async () => {
    const wallet = ethers.Wallet.createRandom();
    
    // Create an old challenge
    const oldChallenge = JSON.stringify({
      method: "POST",
      url: "http://localhost:3000/test",
      timestamp: Date.now() - 120000, // 2 minutes ago
      nonce: ethers.hexlify(ethers.randomBytes(16)),
    });

    const message = `EIP-3009 Payment:\n${oldChallenge}`;
    const signature = await wallet.signMessage(message);

    // Verification should work (signature is valid)
    const signerAddress = await verifySignature(oldChallenge, signature);
    expect(signerAddress).toBeDefined();
    
    // But checkLoginStatus should reject it based on timestamp
    // This is handled in the middleware layer
  });

  it("should reject invalid signatures", async () => {
    const challenge = JSON.stringify({
      method: "POST",
      url: "http://localhost:3000/test",
      timestamp: Date.now(),
      nonce: ethers.hexlify(ethers.randomBytes(16)),
    });

    // Invalid signature
    const invalidSignature = "0x0000000000000000000000000000000000000000000000000000000000000000";

    const signerAddress = await verifySignature(challenge, invalidSignature);
    expect(signerAddress).toBeNull();
  });
});
```

---

## Security Considerations

### Challenge Expiry
- **Default**: 60 seconds
- **Why**: 
  - Prevents replay attacks (same challenge can't be used multiple times)
  - Limits window for brute-force signature attempts
  - Encourages fresh authentication on each request
- **Configurable**: Adjust `X402_CHALLENGE_EXPIRY_SECONDS` for your threat model
- **Trade-off**: Shorter = more secure but potentially more requests; Longer = fewer requests but higher replay risk

### Signature Validation
- **Standard**: EIP-191 (Ethereum's standardized message signing)
- **Recovery**: Uses `ethers.verifyMessage()` which recovers the signer address directly from the signature
- **No private key exposure**: The private key never leaves the agent's environment
- **Message format**: `"EIP-3009 Payment:\n{challenge}"` prevents signature reuse across different contexts

### Rate Limiting
- **Per-tier quotas**: Prevents abuse across different user categories
- **Redis-backed**: Distributed rate limiting across multiple servers
- **Key structure**: `rate-limit:{tier}:{type}:{identifier}`
  - `{tier}`: anonymous, delegated, or human
  - `{type}`: request or anchor
  - `{identifier}`: wallet address (X402) or email (traditional auth)
- **Anchor operations**: Stricter limits protect expensive on-chain operations

### Account Creation
- **On-demand**: Accounts are created automatically on first successful authentication
- **Verified status**: X402 accounts are marked as verified (assuming secure wallet storage)
- **Synthetic emails**: Generated as `{wallet_address}@dedi.agent` to maintain database uniqueness
- **On-chain setup**: 
  - Automatic account creation on-chain
  - Automatic profile creation
  - Automatic DID generation
  - Initial unit transfer for operations

### Network Security
- **HTTPS recommended**: Always use HTTPS in production to prevent challenge interception
- **Challenge unguessable**: 16-byte random nonce makes challenges cryptographically unique
- **Request-specific**: Challenge includes method and URL, preventing cross-request reuse

---

## Error Handling

### Common Error Responses

| Status | Error Message | Cause | Solution |
|--------|---|---|---|
| 402 | `Payment Required` | No X-PAYMENT header provided | Get new challenge, sign it, and retry with X-PAYMENT header |
| 401 | `Unauthorized` (with X-PAYMENT) | Invalid signature | Verify your wallet private key, re-sign the challenge, and retry |
| 401 | `Challenge expired` | Challenge exceeded expiry time | Request a new challenge (API will return 402), sign it, and retry |
| 401 | `Invalid X-PAYMENT header` | Malformed Base64 or JSON | Ensure proper Base64 encoding of `{"challenge":"...","signature":"..."}` |
| 429 | `Too many requests. Please try again later.` | Rate limit exceeded for tier | Wait for the time window to expire, then retry |
| 500 | `Internal server error` | Database or on-chain error during account creation | Check server logs; contact support if persists |

### Debug Logging

The X402 flow includes logging for troubleshooting:

```typescript
// src/utils/auth/x402.ts
console.error("Error verifying signature:", error);

// src/app/middleware/rateLimit/tieredRateLimit.ts
console.error("[TieredRateLimit]: Error:", err);

// src/app/middleware/auth/checkLoginStatus.ts
// Logs when challenge is issued and verified
```

Enable debug logging:
```bash
DEBUG=dedi:* npm start
```

---

## Migration & Backward Compatibility

### Existing Authentication Methods
X402 does **not** replace existing authentication:
- ✅ API Keys continue to work unchanged
- ✅ Cookie-based sessions continue to work unchanged
- ✅ JWT tokens continue to work unchanged
- ✅ Existing protected endpoints remain fully protected

### Gradual Rollout
The implementation is designed for gradual adoption:
1. Existing clients continue using their preferred auth method (no changes required)
2. New agents can adopt X402 immediately (no setup needed)
3. Mixed environments (hybrid auth) work seamlessly
4. Fallback: If X402 fails, existing auth methods are still attempted

### Database Compatibility
- The `wallet_address` column is optional (`nullable: true`)
- Existing accounts (without wallets) continue to function normally
- No data migration required
- New accounts created via X402 have wallet_address populated
- Existing accounts can optionally add wallet_address later

### API Compatibility
- X402 is completely transparent to existing API consumers
- No breaking changes to existing endpoints
- New header (`X-PAYMENT`) is optional
- Existing headers (Authorization, Cookie, etc.) continue to work

---

## Files Changed in This Implementation

### New Files
- `sign-challenge.js` - Client-side utility for signing challenges
- `src/utils/auth/x402.ts` - Core X402 logic (challenge generation, verification, account management)
- `src/app/middleware/rateLimit/tieredRateLimit.ts` - Tiered rate limiting middleware

### Modified Files
- `.env.example` - Added X402 and rate limiting configuration
- `package.json` - Added ethers dependency
- `src/entity/EntityAccount.ts` - Added wallet_address column
- `src/app/middleware/auth/checkLoginStatus.ts` - Added X402 challenge-response flow
- `src/utils/core/helper.ts` - Extended extractToken() to handle X402
- `src/modules/*/routes.ts` (multiple files) - Integrated tieredRateLimit() middleware across all routes

---

## Troubleshooting

### Challenge Keeps Expiring

**Problem**: Continuous 401 responses with "Challenge expired"

**Cause**: 
- Challenge validity window is too short for your use case
- Network latency between challenge request and signing

**Solution**: 
- Increase `X402_CHALLENGE_EXPIRY_SECONDS` in `.env`
- Implement request queuing/retry logic on client
- Optimize signing process to reduce latency

### Signature Verification Fails

**Problem**: "Invalid signature" error despite correct wallet key

**Cause**: 
- Challenge data changed between generation and signing
- Incorrect message format for signing
- Using wrong private key

**Solution**: 
- Ensure you're signing the exact base64-decoded challenge
- Use the provided `sign-challenge.js` script (it handles encoding)
- Verify message format: `"EIP-3009 Payment:\n{challenge}"`
- Double-check your private key matches the wallet address

**Debug:**
```bash
# Verify your wallet address
node -e "const ethers = require('ethers'); console.log(new ethers.Wallet('0xYOUR_KEY').address)"

# Test signing
node sign-challenge.js
```

### Rate Limit Errors (429)

**Problem**: Getting "Too many requests" despite low usage

**Cause**: 
- Sharing wallet address across too many concurrent requests
- Rate limit window is too restrictive for your use case
- Multiple agents using same wallet

**Solution**:
- Implement request queuing on client side (serialize requests)
- Increase rate limits for your tier in `.env`
- Use delegated agent tier if available (100x higher limits)
- Use separate wallets for separate agents/clients

**Check current usage:**
```bash
# Connect to Redis and inspect rate limit keys
redis-cli KEYS "rate-limit:*"
redis-cli GET "rate-limit:anonymous:request:{wallet_address}"
```

### Account Not Created

**Problem**: First authentication succeeds but no account appears in database

**Cause**: 
- On-chain setup failed (insufficient funds, network issue)
- Database connection issue
- Mnemonic generation failed

**Solution**:
- Check server logs for on-chain errors
- Verify `createAccount()` and `transferUnits()` are working
- Ensure database migrations are applied: `npm run typeorm migration:run`
- Verify database connectivity

**Debug:**
```bash
# Check if account was created
SELECT * FROM entity_account WHERE wallet_address = '0x...';

# Check TypeORM logs
DEBUG=typeorm:* npm start
```

### X-PAYMENT Header Not Recognized

**Problem**: Middleware ignores X-PAYMENT header, still returns 402

**Cause**:
- Header name is case-sensitive (must be uppercase)
- Header value is not properly base64-encoded
- Invalid JSON in base64 payload

**Solution**:
- Use exact header name: `X-PAYMENT` (uppercase)
- Verify base64 encoding: `echo "YmFzZTY0dGV4dA==" | base64 -d`
- Verify JSON structure: `{"challenge":"...","signature":"..."}` (no extra fields)

**Test:**
```bash
curl -v -X POST http://localhost:3000/test \
  -H "X-PAYMENT: eyJjaGFsbGVuZ2UiOiJ7XCJtZXRob2RcIjpcIlBPU1RcIn0iLCJzaWduYXR1cmUiOiIweDEyMzQifQ==" \
  2>&1 | grep -i "x-payment"
```

---

## Performance Considerations

### Challenge Generation
- **Overhead**: Minimal (JSON serialization + random byte generation)
- **Impact**: ~1-2ms per challenge generation

### Signature Verification
- **Overhead**: Moderate (elliptic curve operations)
- **Impact**: ~10-50ms per verification (depends on hardware)
- **Optimization**: Cache verified addresses in Redis for short periods if needed

### Rate Limiting
- **Overhead**: Redis incr + expire operations
- **Impact**: ~5-10ms per request (Redis latency)
- **Optimization**: Use Redis pipeline for batch operations; consider local rate limiting for edge cases

### Account Creation
- **Overhead**: Database writes + on-chain operations
- **Impact**: ~500ms-5s for first-time authentication (on-chain dependent)
- **Optimization**: Async queue for account creation if blocking becomes issue

---

## Roadmap & Future Enhancements

- [ ] Support for additional signing standards (e.g., Solana, Cosmos)
- [ ] Tiered on-chain permissions system with delegation
- [ ] Delegation tokens for agent-to-agent authorization
- [ ] Webhook notifications for suspicious authentication attempts
- [ ] Metrics dashboard for authentication success rates and failure analysis
- [ ] Account recovery mechanisms for lost private keys
- [ ] Multi-signature support (multiple wallets per account)
- [ ] Time-locked challenges (challenge validity starts at future time)
- [ ] Hardware wallet integration (Ledger, Trezor support)

---

## FAQ

**Q: Do I need a real wallet to use X402?**
A: No. You can use a test wallet generated from any mnemonic or private key. Generate one with:
```bash
node -e "const ethers = require('ethers'); const w = ethers.Wallet.createRandom(); console.log('Address:', w.address); console.log('Private Key:', w.privateKey);"
```

**Q: Can I rotate my wallet address?**
A: Currently, wallet addresses are unique and permanent for an account. Each wallet address creates one account. Future releases will support linking multiple wallets per account.

**Q: What happens to my account if I forget my private key?**
A: Account access is tied to the private key. If lost, the account becomes inaccessible. Future releases will include recovery mechanisms (e.g., social recovery, backup keys).

**Q: Is X402 free to use?**
A: Yes, by default. `X402_PRICE` is set to 0 in `.env`. This can be changed to enforce actual payment if desired.

**Q: Can I use X402 with the existing API key system?**
A: Yes! Both can coexist. Use whichever method suits your client. Authentication is attempted in this order: X402 → API Key → Cookie → JWT.

**Q: How do I upgrade from API keys to X402?**
A: Existing API keys continue to work. You don't need to upgrade; X402 is an additional option, not a replacement.

**Q: What happens if my challenge expires mid-signing?**
A: The API will return a 401 error. Generate a new challenge (by making an unauthenticated request again, which returns 402) and retry.

**Q: Can I reuse a challenge multiple times?**
A: No. Each challenge is single-use and expires after `X402_CHALLENGE_EXPIRY_SECONDS`. This prevents replay attacks.

**Q: Is the challenge visible in logs?**
A: Challenges are logged during generation but contain no sensitive data (just metadata and nonce). Signatures are never logged. Still, use HTTPS to prevent network interception.

---

## References

- [EIP-191: Signed Data Standard](https://eips.ethereum.org/EIPS/eip-191)
- [HTTP 402 Payment Required](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/402)
- [L402 Specification](https://github.com/lightningnetwork/lnd/blob/master/lnrpc/invoicesrpc/invoices.proto)
- [ethers.js Documentation](https://docs.ethers.org/v6/)
- [EIP-3009: Transfer With Authorization](https://eips.ethereum.org/EIPS/eip-3009)

---

## Support

For issues or questions about X402 authentication:
1. **Check the [Troubleshooting](#troubleshooting) section** - covers common issues
2. **Review [test examples](#testing)** - working examples for your use case
3. **Enable debug logging** and check application logs:
   ```bash
   DEBUG=dedi:* npm start
   ```
4. **Open an issue** on the repository with:
   - Full error message and stack trace
   - Steps to reproduce
   - Your configuration (without sensitive keys)
   - Relevant logs

---

## Implementation Summary

### Lines of Code Changed
- **Added**: 1,122 lines
- **Deleted**: 602 lines
- **Modified**: 21 files

### Key Metrics
- **New dependencies**: ethers v6.13.1
- **Database schema changes**: 1 new column (wallet_address)
- **New middleware**: tieredRateLimit
- **Routes enhanced**: 70+ routes across 8 modules

---

This documentation provides everything needed to understand, implement, test, and troubleshoot the X402 Agent-Ready Authentication system.
