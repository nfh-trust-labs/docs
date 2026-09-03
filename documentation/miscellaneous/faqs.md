# FAQs

#### General

**Q: What is DeDi?**&#x20;

DeDi (Decentralized Directory) is an open protocol under Linux Foundation Decentralized Trust that defines a universal way to publish, discover, and verify public information. DeDi.global is a hosted implementation of this protocol, operated by Networks for Humanity Foundation.

**Q: Is DeDi free?**&#x20;

Yes. DeDi.global is free to publish on and free to query. There are no licensing fees, contracts, or exit clauses.

**Q: Is my data public?**&#x20;

Yes — DeDi is designed for public registries. Lookup and Query APIs are publicly accessible without authentication. If your data is sensitive or private, DeDi is not the right tool.

**Q: How is DeDi different from a blockchain?**&#x20;

DeDi is not a blockchain. It is a directory protocol with a RESTful API. However, every entry's cryptographic proof is anchored on the CORD blockchain to provide tamper-evidence and auditability. You interact with DeDi via standard HTTPS APIs, not blockchain transactions.

**Q: Do I need to run a server to adopt DeDi?**&#x20;

No. You adopt DeDi by publishing signed DeDi files — one file per directory, plus a signed manifest at `/.well-known/dedi.index.json` declaring your signing key — on a domain you control. Operating a server is a separate, optional role. See [Publishing DeDi files](../resources/publishing-dedi-files/).

**Q: Can I run my own DeDi server?**&#x20;

Yes. DeDi is built on the open Decentralized Directory Protocol (DDP). Anyone can implement the protocol independently. DeDi.global is one implementation — not the only one. Running a server makes sense if you want to index and serve many publishers' directories, or you have sovereignty requirements; it is not needed to publish your own.

***

#### Publishing on your own domain

**Q: What do I have to publish?**&#x20;

Two signed artifacts: one DeDi file per directory (a self-contained JSON document holding the registry and its records), and a manifest at `/.well-known/dedi.index.json` that declares your current signing key(s) and lists your files. Both are ordinary static JSON — any web server, object store, or source repository that serves files over HTTPS will do.

**Q: Does DeDi.global ever see my signing key?**&#x20;

No. Publishers sign locally, and only the **public** key is ever published — it is embedded in your files and declared in your manifest. No DeDi server receives, stores, or logs private signing material. This is a hard invariant of the protocol.

**Q: How do I rotate or revoke a signing key?**&#x20;

By editing your own manifest. Add the new key to `keys` to rotate, and remove a key to revoke it: files signed by a removed key immediately fail verification everywhere, including in cached and relayed copies, because presence in `keys` *is* validity. No ticket, no operator, no third party.

**Q: How does anyone find my files?**&#x20;

Two ways: a server that reaches your domain fetches `/.well-known/dedi.index.json` and learns everything you publish; and your domain can be added to the public discovery list (`domains.txt`) in the [protocol repository](https://github.com/LF-Decentralized-Trust-labs/decentralized-directory-protocol), which servers poll. Being on the list confers discovery, never trust — verification always runs against your own signed well-known.

**Q: Does DeDi.global index files published on my own domain today?**&#x20;

Yes. Add your domain to the discovery list and DeDi.global's crawler fetches, verifies, and indexes your files, serving them through `/dedi/lookup` and `/dedi/query` with your original signature. Your files are also independently verifiable by anyone without DeDi.global. See [dedi.global and origin-published files](../resources/publishing-dedi-files/dedi.global-and-origin-published-files.md).

**Q: If I publish on my own domain, do I still get on-chain anchoring?**&#x20;

Not by itself. Origin-published files carry your own signature, which gives integrity and authenticity offline. CORD anchoring is something DeDi.global adds to content published through it.

**Q: I already publish on DeDi.global. Do I need to change anything?**&#x20;

No. The publishing model is additive: the information model, the APIs, and everything you have published stay exactly as they are.

***

#### Account & Setup

**Q: How do I create an account?**&#x20;

Register at [publish.dedi.global](https://publish.dedi.global/) with your email and a password (6+ characters with at least one special character). You'll receive a magic link to verify your email.

**Q: How do I get an API key?**&#x20;

After registration and email verification, log in and use the "Get API Key" option in your profile, or call `GET /dedi/get-api-key` while authenticated. Include the key as a Bearer token in all API requests.

***

#### Domain Verification

**Q: Is domain verification mandatory?**&#x20;

No, but it is strongly recommended. Verification links your namespace to your domain, making API paths human-readable (e.g., `/dedi/your-domain.com/registry/record` instead of a hash) and establishing organizational provenance.

**Q: How long does domain verification take?**&#x20;

The domain whitelisting request is typically processed within \~5 minutes. After adding the DNS TXT record, DNS propagation can take up to 24–48 hours depending on your DNS provider.

**Q: Can I use DeDi without domain verification?**&#x20;

Yes. You can create namespaces, registries, and records without verification. Your namespace will use a system-generated ID instead of your domain name.

***

#### Data & Schemas

**Q: What credential formats does DeDi support?**&#x20;

DeDi is format-agnostic. It supports the co-existence of multiple data standards and schemas, including VC JSON-LD, mDocs/mDL, and custom JSON Schema (Draft 7). You define the schema when creating a registry.

**Q: Can I define custom schemas?**&#x20;

Yes. You can provide any valid JSON Schema (Draft 7) when creating a registry. DeDi also offers pre-built schema templates (Membership, Public Key, Revocation, Beckn One).

**Q: Can I bulk upload data via API?**

&#x20;Yes. DeDi provides a `POST /dedi/bulk-upload` endpoint that accepts CSV files via multipart/form-data. You can also bulk upload through the UI. See the [Bulk Upload guide](https://claude.ai/dedi.global-developers/guides/bulk-upload) for details.

**Q: What happens if schemas don't match during upload?**&#x20;

The upload will fail. Schema consistency is required — your CSV column headers must match the registry's schema property names, and values must conform to the defined types.

***

#### Verification & Trust

**Q: How do I verify that a DeDi entry hasn't been tampered with?**&#x20;

Every DeDi.global entry includes a cryptographic proof (BLAKE2 H256 digest) anchored on the CORD blockchain. You can verify by recalculating the digest from the entry's fields and comparing it to the proof. You can also verify directly on-chain via [apps.cord.network](https://apps.cord.network/).

For a directory published as DeDi files on a publisher's own domain, verification is the protocol's five-step check instead: shape, signature over the JCS-canonicalized document, the signing key's presence in the publisher's `/.well-known/dedi.index.json`, freshness, and registry state. See [Verification and lifecycle](../resources/publishing-dedi-files/verification-and-lifecycle.md).

**Q: Can I subscribe to changes in a registry?**&#x20;

Yes. The Watch feature lets you subscribe to changes on namespaces, registries, specific records, or schema tags. You receive notifications when entries are created, updated, or their state changes.

***

#### Migration & Portability

**Q: How do I migrate from another registry to DeDi?**&#x20;

Export your existing data as CSV, map your columns to a DeDi schema, and use the bulk upload feature. If you have a custom data format, define a matching JSON Schema when creating your registry.

**Q: Can I export my data from DeDi?**&#x20;

Yes. Your data is exportable as standard JSON  — fully portable.

***

#### Support

For additional questions, [contact support](https://docs.google.com/forms/d/e/1FAIpQLSesBZMkgmnKY3SDzGQPDF8E-OoAo75O5n4LVrdoDKFtz_Y3FA/viewform).&#x20;
