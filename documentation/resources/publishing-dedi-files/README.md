---
description: Adopt DeDi by publishing signed files on a domain you control — no server required
---

# Publishing DeDi files

The DeDi protocol now defines a second, server-optional way to adopt it: a publisher serves its directories as **self-contained, signed JSON files** on a domain it controls, instead of operating a DeDi API.

**Adopting DeDi does not require running a server.** A publisher signs one file per directory, plus a manifest at `/.well-known/dedi.index.json` that declares its signing key. DeDi servers — dedi.global among them — then crawl, verify, index, and serve those files, relaying the publisher's original signature, so a relying party gets the same guarantee whether it queries a server or fetches the file straight from the origin.

Authority is each publisher's own signed well-known, in the manner of `did:web`. No central registry sits in the trust path.

{% hint style="info" %}
This is **additive**. The information model is unchanged — Namespace → Registry (Directory) → Record — and so are `/dedi/lookup` and `/dedi/query`. A publisher already using dedi.global, or already serving the DeDi API itself, remains conformant and need do nothing.
{% endhint %}

## Two roles

Publishing and serving are separate roles. Only the first is required to adopt DeDi.

|                        | Publisher                             | DeDi server                            |
| ---------------------- | ------------------------------------- | -------------------------------------- |
| Produces               | DeDi files and a signed manifest      | an index and the DeDi APIs             |
| Endpoints exposed      | none — static files                   | `/dedi/lookup`, `/dedi/query`          |
| Signs data             | required                              | relays the publisher's signature       |
| Operates infrastructure| no                                    | yes                                    |

A DeDi server is optional infrastructure, operated by any party that wants to serve published files at scale. It adds what static files cannot provide on their own — cross-directory search, version history, conditional fetch, availability. It is a cache and an index, never an authority.

## The three artifacts

| Artifact                                        | Where                                    | Job                                          |
| ----------------------------------------------- | ---------------------------------------- | -------------------------------------------- |
| **DeDi file**                                   | anywhere the publisher controls          | one signed registry (directory) + its records |
| **Manifest** — `/.well-known/dedi.index.json`   | the publisher's domain, at a fixed path  | declares the current signing key(s); lists the files |
| **Discovery list** — `domains.txt`              | a public list, e.g. the LFDT protocol repo | names publisher domains so servers find them |

The trust model depends only on the first two, both of which are signed. The discovery list sits **outside** the verification path and asserts nothing about the domains it names.

## End to end

```
   PUBLISHER (example.org)
     │  produces + signs:  DeDi files  and  /.well-known/dedi.index.json (declares its keys)
     │  hosts both anywhere it controls;  puts its domain on a public discovery list
     ▼
   DEDI SERVER   ── finds publishers via the discovery list + crawl
     │  ingests each file, verifies it, indexes it, serves /dedi/lookup · /dedi/query
     ▼
   RELYING PARTY
     verify the file's own signature  →  confirm its key is in the publisher's well-known  →  trust
     (may query a server, or fetch the DeDi file straight from the publisher — same check either way)
```

## Where to host

A publisher may host its files anywhere it controls — its own web server, a source repository, a public file-sharing service — or deposit them with a DeDi server that hosts them on its behalf. The choice of host does not affect verification: a verifier checks the publisher's signature against the key declared at the publisher's well-known, whoever serves the bytes. **Hosting is a deployment concern and carries no weight in the trust model.**

## Read next

{% content-ref url="dedi-file-and-manifest.md" %}
[dedi-file-and-manifest.md](dedi-file-and-manifest.md)
{% endcontent-ref %}

{% content-ref url="verification-and-lifecycle.md" %}
[verification-and-lifecycle.md](verification-and-lifecycle.md)
{% endcontent-ref %}

{% content-ref url="discovery.md" %}
[discovery.md](discovery.md)
{% endcontent-ref %}

{% content-ref url="dedi.global-and-origin-published-files.md" %}
[dedi.global-and-origin-published-files.md](dedi.global-and-origin-published-files.md)
{% endcontent-ref %}

The normative specification is [`docs/publishing-dedi-files.md`](https://github.com/LF-Decentralized-Trust-labs/decentralized-directory-protocol/blob/main/docs/publishing-dedi-files.md) in the DeDi protocol repository, with working [examples](https://github.com/LF-Decentralized-Trust-labs/decentralized-directory-protocol/tree/main/examples) and [JSON Schemas](https://github.com/LF-Decentralized-Trust-labs/decentralized-directory-protocol/tree/main/schemas). Where these pages and the specification differ, the specification governs.
