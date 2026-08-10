---
description: The two signed artifacts a publisher produces
---

# The DeDi file and the manifest

## The DeDi file

A DeDi file carries **exactly one registry** (directory) and its records. A namespace with *N* registries publishes *N* files, indexed together by the manifest. Field names reuse the DeDi API's, so a server can project a file into a `/dedi/lookup` response without translation.

```json
{
  "dedi_version": "0.1",
  "type": "dedi-file",
  "source_url": "https://example.org/dedi/dedi.public-keys.json",
  "next_update": "2026-07-15T10:00:00Z",

  "publisher": {
    "domain": "example.org",
    "key": {
      "kid": "key-1", "kty": "OKP", "crv": "Ed25519",
      "x": "11qYAYKxCrfVS_7TyWQHOg7hcvPapiMlrwIaaPcHURo"
    }
  },

  "namespace": "example.org",
  "registry": {
    "name": "public-keys",
    "schema": "https://raw.githubusercontent.com/LF-Decentralized-Trust-labs/decentralized-directory-protocol/main/schemas/public_key.json",
    "state": "live",
    "updated_at": "2026-07-08T10:00:00Z"
  },

  "records": [
    {
      "record_name": "auth-service",
      "details": {
        "public_key_id": "example.org:auth",
        "publicKey": "MCowBQYDK2VwAyEAGb9ECWmEzf6FQbrBZ9w7lshQhqowtrbLDFw4rXAxZk",
        "keyType": "ed25519",
        "keyFormat": "base64",
        "entity": { "name": "Auth Service", "url": "https://example.org/auth" }
      }
    }
  ],

  "proof": {
    "verification_method": "key-1",
    "canonicalization": "JCS",
    "jws": "eyJhbGciOiJFZERTQSIsImI2NCI6ZmFsc2UsImNyaXQiOlsiYjY0Il19..<detached-signature>"
  }
}
```

### Field notes

* **`type`** is required. Filename and path are conventions only, so a file may arrive with neither; `type` is the one self-identification that always travels with the content.
* **`source_url`** is the link a relocated copy keeps back to origin, for re-fetching a fresher version.
* **`publisher.key` is public only.** Publishers sign locally. No server ever receives private key material.
* **`records` are pure data** — `{ record_name, details }`. Lifecycle lives at the *registry* level (`state`) and through *negative registries*, not per record.
* **`record_name` is required and must be unique within its registry.** A file with duplicate record names is invalid and must be rejected. Names are case-sensitive, carry no character-set restriction, and are percent-encoded when they appear in a URL path.
* **Addressing.** Every record is addressed by the triple `{namespace}/{registry_name}/{record_name}`. A DeDi server must expose an ingested record at exactly that triple, whatever file, host, or manifest it was crawled from.
* **`schema`** is a URL or an inline JSON Schema object, never anchored to a central host.
* **One file = one registry.** Sharding a very large registry across files is a deferred extension.

### Layout and hosting conventions

Nothing in verification or discovery depends on where a file sits or what it is called — the manifest carries absolute URLs. The following are **conventions, not conformance requirements**; a publisher whose host serves opaque URLs is still fully conformant.

* **Filename — `dedi.<registry-name>.json`** (recommended). The `.json` suffix is what makes the file serve as `application/json`; the `dedi.` prefix makes a publisher's set matchable by one glob in a repo, a mirror, or a server's ingest filter.
* **Path — a `/dedi/` directory** (recommended), which keeps the files administrable and gives one place to scope the headers below.

```
https://example.org/.well-known/dedi.index.json   ← manifest — fixed path, normative (RFC 8615)
https://example.org/dedi/dedi.public-keys.json    ← files — recommended convention
https://example.org/dedi/dedi.revocations.json
```

* **CORS** — serve `Access-Control-Allow-Origin: *`. Without it a browser-based relying party cannot read the file at all, and no property of the signature substitutes for the header. The data is public and no credentials are involved.
* **Cache-Control** — do not advertise a `max-age` extending beyond the file's `next_update`. Freshness is declared in-band; an HTTP cache outliving that bound would serve copies the protocol has already declared stale.

## The manifest

`/.well-known/dedi.index.json` is **the authority**: it declares the publisher's current signing key(s) and lists its files. It is signed, and served under the domain's TLS at the well-known path (RFC 8615).

```json
{
  "dedi_version": "0.1",
  "type": "dedi-manifest",
  "domain": "example.org",
  "name": "Example Org Trust Services",

  "keys": [
    { "kid": "key-1", "kty": "OKP", "crv": "Ed25519",
      "x": "11qYAYKxCrfVS_7TyWQHOg7hcvPapiMlrwIaaPcHURo" }
  ],

  "updated_at": "2026-07-01T09:00:00Z",
  "next_update": "2026-07-15T10:00:00Z",

  "files": [
    { "registry": "public-keys",
      "url": "https://example.org/dedi/dedi.public-keys.json",
      "digest": "sha-256:9f2c1d4e7a8b0c3d5e6f70819293a4b5c6d7e8f90a1b2c3d4e5f60718293aeba" },
    { "registry": "revocations",
      "url": "https://example.org/dedi/dedi.revocations.json",
      "digest": "sha-256:5b1a2c3d4e5f60718293a4b5c6d7e8f90a1b2c3d4e5f60718293a4b5c6d7e8f0" }
  ],

  "proof": {
    "verification_method": "key-1", "canonicalization": "JCS", "jws": "eyJ..."
  }
}
```

Registry names are unique within a namespace: a manifest must not list two `files[]` entries — referenced or inline — for the same registry name.

### Self-signing and the trust anchor

The manifest declares `key-1` and is signed by `key-1`. The apparent circularity is resolved by the transport: the manifest is served under **TLS at the domain's well-known path**, and that is what establishes the declaration as the domain's own — the same resolution `did:web` uses for `https://domain/.well-known/did.json`. The signature serves a separate purpose: keeping the manifest tamper-evident once it has been cached or relayed away from the origin.

### `files[].digest`

The whole-file digest lets the *signed* manifest vouch for each DeDi file before it is fetched, and lets a server detect change cheaply — digest moved, re-fetch. It is the only place a whole-file hash is needed; the file's own signature secures its internal integrity.

### Inline registries

An entry in `files[]` may, instead of referencing a file by URL, embed a **complete DeDi file object verbatim** — same shape, its own `proof`, its own embedded `publisher.key`. A server that extracts the entry holds a standard DeDi file and verifies it by the same five steps; the key-membership check is satisfied locally, since the surrounding manifest's `keys` are already in hand. An inline entry carries no `digest`, because the manifest's own signature covers its bytes directly. An entry is a reference **or** an inline file, never both.

Inline is intended for **small registries**: a publisher with a three-record key directory becomes fully conformant with a single signed document at one fixed path. As a registry grows, move it out to a referenced file — the well-known is fetched by every verifier for the key check, and its size is a cost they all pay.

## Schemas

A registry's `schema` is either a URL or an inline JSON Schema object, never anchored to a central host:

```json
"schema": "https://raw.githubusercontent.com/LF-Decentralized-Trust-labs/decentralized-directory-protocol/main/schemas/public_key.json"
"schema": "https://example.org/schemas/my_registry.json"
"schema": { "type": "object", "required": ["id"], "properties": { "id": { "type": "string" } } }
```

The protocol declares a small canonical set — `public_key`, `revoke`, `membership` — in the [`schemas/`](https://github.com/LF-Decentralized-Trust-labs/decentralized-directory-protocol/tree/main/schemas) directory of the protocol repo. Everyone else uses an external URL or inlines their own.

{% hint style="warning" %}
The canonical schema URLs currently point at the `main` branch, which is mutable. They will be pinned to an immutable ref (a version tag or commit SHA) before the specification is released. Treat `main`-based schema URLs as a draft placeholder.
{% endhint %}
