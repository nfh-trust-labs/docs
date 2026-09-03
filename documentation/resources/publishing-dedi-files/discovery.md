---
description: How DeDi servers find published directories — and why the list confers no trust
---

# Discovery

Two complementary mechanisms let a DeDi server find a publisher:

1. **Discovery list** — the publisher's domain appears on a public list; servers poll the list and crawl the domains on it. This does not depend on the files being linked from anywhere.
2. **Crawl** — a server reaching any domain fetches `/.well-known/dedi.index.json`, verifies it, and learns every DeDi file the publisher offers plus the signing key to expect.

The manifest anchors both mechanisms, and its well-known path is what makes an unknown domain probeable. Neither mechanism consults a file's name or directory: the manifest lists absolute URLs and a server follows them. The naming conventions help glob-match a corpus already in hand — a repository, a mirror, a server's ingest filter — and nothing more. The web cannot be enumerated by file extension; it can only be followed by link.

## The discovery list

The "registry" of publishers is a public list, and nothing more. One entry per line; lines beginning with `#` are comments. An entry is either a **publisher domain** or the **URL of another list**:

```
domains.txt
    # head: 2026-07-10T00:00:00Z
    example.org
    univ.edu
    gleif.org
    https://lists.example.net/domains.txt
```

* **List references.** An entry that is a URL points to another `domains.txt`, read the same way. This lets the list be partitioned by region, sector, or operator instead of growing without bound in one file. A poller must bound recursion and ignore entries it has already seen, so referenced lists cannot loop. A referenced list carries no more authority than the list that named it: none.
* **Root list.** The root `domains.txt` lives at the root of the [DeDi protocol repository](https://github.com/LF-Decentralized-Trust-labs/decentralized-directory-protocol) under Linux Foundation Decentralized Trust. It is the conventional starting point for a crawl; any party may still maintain and poll its own.
* **Optional head.** A list may carry a first comment line, `# head: <value>`, updated whenever the entries change, so pollers can tell a new version exists without re-reading everything. It is unsigned and carries no authority. A git-hosted list already provides this through its commit id, and may omit the head.

## The list is not a trust anchor

The list makes no assertion about any entry. Any party may add any domain, and doing so confers no authority, because trust derives from each domain's own signed well-known. Adding `example.org` to the list does not let a third party speak for example.org: a server that crawls the entry fetches example.org's own manifest and obtains its genuine key.

The list therefore requires no proof of control, no registration record, and no pinning. **It is a discovery mechanism, and lies outside the verification path entirely.**

## Getting listed

Add your domain to the root `domains.txt` in the protocol repository by pull request. Adding a domain is the publisher's own opt-in to being crawled; it grants nothing and asserts nothing beyond "there may be DeDi files here."

{% hint style="info" %}
**Status.** dedi.global's crawler consumes the root list and indexes the domains on it. List additions are reviewed as pull requests. See [dedi.global and origin-published files](dedi.global-and-origin-published-files.md).
{% endhint %}
