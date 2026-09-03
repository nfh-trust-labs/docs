---
description: How dedi.global handles files published on your own domain
---

# dedi.global and origin-published files

In protocol terms, **dedi.global is a DeDi server**: it discovers, verifies, indexes, and serves directories, and exposes `/dedi/lookup` and `/dedi/query` across many publishers. It is one implementation of the protocol, not the protocol itself, and it is never an authority over the data it serves.

## Available today

* **Hosted publishing on dedi.global.** Create a namespace, verify your domain, define a schema, and publish registries and records through the dashboard or the APIs. Every namespace, registry, and record is anchored on CORD and served with a platform proof. See the [Quickstart](../../dedi.global-developers/quickstart/).
* **Origin-hosted publishing, independently of dedi.global.** The publishing model is part of the protocol and needs nothing from us: sign your DeDi files, serve a `/.well-known/dedi.index.json`, and any conformant verifier — a relying party's own code included — can verify them end to end. The [specification](https://github.com/LF-Decentralized-Trust-labs/decentralized-directory-protocol/blob/main/docs/publishing-dedi-files.md) and [examples](https://github.com/LF-Decentralized-Trust-labs/decentralized-directory-protocol/tree/main/examples) are everything a publisher needs.

* **Crawling of origin-published files.** dedi.global runs as a crawling DeDi server: once your domain is on the [discovery list](discovery.md), your files are fetched, verified, indexed, and served through `/dedi/lookup` and `/dedi/query` alongside natively published content — carrying your original signature.

{% hint style="success" %}
**dedi.global crawls origin-published files.** Publish on your own domain, add the domain to the discovery list, and your registries become queryable through dedi.global's APIs with no account and no server of your own.
{% endhint %}

## How the crawler behaves

* **Crawl and ingest.** Polls the LFDT root `domains.txt` (following list references, with bounded recursion), fetches each publisher's `/.well-known/dedi.index.json` over TLS with conditional GET, and processes both referenced and inline `files[]` entries.
* **Full verification at ingest.** All five steps. Shape, signature, or key-membership failures are hard: never indexed, never served. Expired or inactive content is soft: indexed and served with a flag, never presented as current.
* **Relayed proofs, not platform proofs.** Responses for crawled records carry the **publisher's original signature and key**, unaltered — never a dedi.global proof over someone else's data. The crawler holds no signing material of its own for this content: there is nothing to steal and nothing to forge with.
* **One address space.** Crawled records are served under the same `{namespace}/{registry_name}/{record_name}` triple through the existing `/dedi/lookup` and `/dedi/query`, alongside natively published content. For crawled publishers the namespace is the domain, and a file whose `namespace` differs from its manifest's `domain` is rejected — so no publisher can declare another's namespace.
* **Re-verification on change.** When a publisher's `keys` change, every indexed file of that publisher is re-verified; files signed by a removed key stop being served as authentic immediately.

## In development

* **Publisher diagnostics.** A way to answer "why is my domain not indexed," backed by recorded per-file failure reasons.
* **Export for hosted publishers.** Generating a signed DeDi file per registry and a signed manifest per namespace for publishers who publish natively on dedi.global — so hosted content becomes crawlable by *any* DeDi server, not only ours, and a publisher can later move the same files to its own domain without re-onboarding its consumers.

## Which path should you choose?

Publishing your own files and publishing on dedi.global are not competing choices — they are the same information model with different hosting. See [How to Use DeDi](../the-vision-for-decentralised-directory-dedi/how-to-use-dedi.md) for the paths side by side.

* Prefer **your own origin** when the data must live on your domain, when you already run a website or a repo that can serve static JSON, or when you want zero dependency on any operator.
* Prefer **dedi.global** when you would rather not manage signing, hosting, and re-issue cadence yourself, or when you want what a server adds — cross-directory search, version history, on-chain anchoring, subscriptions.

Either way the protocol is the same, the data is portable, and a relying party's verification comes out the same.
