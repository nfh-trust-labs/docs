# How are DeDi.global entries cryptographically secured?

DeDi.global entries are cryptographically secured through proof anchoring on the CORD blockchain. Proofs are anchored at **every level** of the hierarchy—namespaces, registries, and records each have their own cryptographic proof.

### How Proofs Work

Every entry in DeDi is secured through a consistent process:

1. The entry data is converted into an object
2. Canonically serialized
3. Stringified using `JSON.stringify()`
4. Hashed using **BLAKE2 H256**

The resulting digest, along with metadata about the creator and network, forms the proof that is anchored on-chain.

### Proof Structures

#### Namespace Proof

```json
{
  "type": "DediNamespaceProof2025",
  "namespace_did": "did:dedi:namespace:acme-org",
  "creator_did": "did:web:acme.org#admin",
  "digest": "0x8f3c91a2b7d4e918c0d55eab3c1a9e4e21e9f8c3d7c6b1a2f4c9d88e1a7b3f22",
  "network_genesis": "0x93abf42c1d77e8f0a928f31fd29d77bd73cd91eb99f15e41f6d39e087ac3b22c"
}
```

**Fields used for digest calculation:** `namespace_id`, `description`, `genesis`, `created_by`, `version`, `version_count`

#### Registry Proof

```json
{
  "type": "DeDiRegistryProof2025",
  "namespace_did": "did:dedi:ns:acme.global",
  "registry_identifier": "registry:acme.membership",
  "creator_did": "did:web:acme.org#admin",
  "digest": "0x91ef3ab2c77dd9f01892bf31cd29b8ae7f5dd321ee019ec45bd119ab4cf72d11",
  "network_genesis": "0x93abf42c1d77e8f0a928f31fd29d77bd73cd91eb99f15e41f6d39e087ac3b22c"
}
```

**Fields used for digest calculation:** `namespace_id`, `registry_name`, `description`, `schema`, `tag`, `version`, `version_count`, `state`, `genesis`, `created_by`, `meta`, `ttl`

#### Record Proof

```json
{
  "type": "DediRecordProof2025",
  "namespace_did": "did:dedi:ns:acme.global",
  "registry_identifier": "registry:acme.membership",
  "record_identifier": "record:acme.membership.member123",
  "creator_did": "did:web:acme.org#issuer1",
  "digest": "0xa17cd92be81f7c0da923ff10efa92b9b37cd10aa8e1c4ff2342dd19cbf9023af",
  "network_genesis": "0x93abf42c1d77e8f0a928f31fd29d77bd73cd91eb99f15e41f6d39e087ac3b22c"
}
```

**Fields used for digest calculation:** `namespace_id`, `registry_id`, `registry_name`, `record_name`, `description`, `details`, `version`, `version_count`, `state`, `genesis`, `created_by`, `meta`, `ttl`

### How Verification Works

To verify any DeDi entry (namespace, registry, or record):

1. **Lookup** — Query the entry via DeDi API
2. **Extract fields** — Take the fields required for digest calculation from the response
3. **Build object** — Convert extracted fields into an object
4. **Serialize** — Apply canonical serialization
5. **Stringify** — Convert to string using `JSON.stringify()`
6. **Hash** — Calculate digest using BLAKE2 H256
7. **Compare** — Verify that your calculated digest matches the `digest` in the proof

#### On-Chain Verification

For additional assurance, verify directly against the CORD blockchain:

1. Go to [apps.cord.network](https://apps.cord.network/) (CORD explorer)
2. Perform a state query using the entry's identifier (`namespace_id`, `registry_id`, or `record_id`)
3. The returned digest should match what you calculated

This confirms the proof was genuinely anchored on-chain and hasn't been tampered with.

### DID Documents

For every `profile_id`, a DID document is automatically generated:

```
did:web:did.cord.network:{id}
```

Access any DID document at: `https://did.cord.network/{id}/did.json`

### Summary

| Level         | Proof Type               | Key Identifiers                                                            |
| ------------- | ------------------------ | -------------------------------------------------------------------------- |
| **Namespace** | `DediNamespaceProof2025` | `namespace_did`, `creator_did`                                             |
| **Registry**  | `DeDiRegistryProof2025`  | `namespace_did`, `registry_identifier`, `creator_did`                      |
| **Record**    | `DediRecordProof2025`    | `namespace_did`, `registry_identifier`, `record_identifier`, `creator_did` |

All proofs include a `digest` (BLAKE2 H256 hash of entry data) and `network_genesis` (CORD chain identifier), and can be independently verified both through the DeDi API and directly on-chain.
