# DeDi in Verifiable Credential Workflows

## DeDi in Verifiable Credential Workflows

DeDi serves as a **directory and trust layer** for verifiable credential (VC) ecosystems — complementing DIDs and DID Documents by providing discoverable, auditable registries of issuers, keys, schemas, and credential status.

> For specific registry implementations, see [Representative Use Cases](https://claude.ai/resources/representative-use-cases).

### The Verifier's Problem

When a verifier receives a credential, they need answers to five questions:

1. Is this DID valid and current?
2. Which public key should verify the signature?
3. Was this key valid when the credential was issued?
4. Has this credential been revoked?
5. Is this issuer trusted in my trust network?

DID Documents answer questions 1–2 for a single identifier. **DeDi addresses all five across entire ecosystems.**

### What DeDi Provides

#### 1. Unified Key Discovery

Public keys are scattered across email headers, key servers, DNS records, and proprietary directories. DeDi lets organizations publish keys to a claimed namespace. Verifiers query one standardized API regardless of the underlying DID method.

#### 2. Auditable Key History

DID Documents show current state only. In DeDi, every key change is a versioned record with cryptographic provenance anchored on-chain. Verifiers can retrieve the key that was valid at any point in time, and subscribe to alerts when keys change.

#### 3. Revocation and Status Lists

Issuers publish status lists (revoked credentials, suspended issuers) as standardized, machine-readable registries on DeDi. Updates propagate immediately and are tamper-evident via on-chain anchoring.

#### 4. Trusted Issuer Directories

Authoritative bodies (regulators, accreditors, industry associations) publish directories of recognized issuers. Verifiers configure which namespaces they trust, then automatically accept issuers listed under those namespaces.

#### 5. VC Schema Registry

Issuers publish VC schemas as registry records with full version history and provenance tracking. Verifiers retrieve the exact schema version referenced by a credential.

### How It Works

**Issuer Setup:**

1. Register a namespace on DeDi
2. Publish registries for public keys, VC schemas, and status lists
3. Each publish operation is signed and anchored on-chain

**Credential Issuance:** The issuer signs the VC with their current key. The VC contains the issuer DID and optionally references DeDi for schema and status list.

**Verification Flow:**

```
Verifier receives VC with issuer DID
        ↓
Query DeDi:
  • Is issuer in a trusted namespace?
  • What key was valid at issuance time?
  • Is credential on any status list?
  • What schema version applies?
        ↓
Resolve DID Document, cross-check key with DeDi
        ↓
Verify signature → Accept or Reject
```

DeDi complements DID Documents: **DID Document** = cryptographic material for one identity; **DeDi** = discovery, trust context, and status across many identities.

### Summary

DeDi doesn't replace DIDs — it makes them **usable at scale** by answering the questions that DID Documents alone cannot. All answers are machine-readable, API-accessible, and cryptographically anchored.
