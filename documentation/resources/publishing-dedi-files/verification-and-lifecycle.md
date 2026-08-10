---
description: Five verification steps, one network fetch — and how signing keys, freshness, and retirement work
---

# Verification and lifecycle

## Signing

Signing is **mandatory**, and both artifacts are signed the same way.

* **Canonicalization.** The signing input is the whole JSON document **with the `proof` block removed**, canonicalized with **JCS (RFC 8785)**, so re-serialization — pretty-printing, key reordering — never breaks the signature. `proof.canonicalization` must be `"JCS"`.
* **Signature.** `proof.jws` is a **detached JWS (RFC 7515)** over the canonicalized bytes, with the algorithm matching the key (for example `EdDSA` for Ed25519). `proof.verification_method` must name the signing key's `kid`.

{% hint style="success" %}
Publishers sign locally. No DeDi server — dedi.global included — ever receives, stores, or logs private signing material. That is a hard invariant of the protocol.
{% endhint %}

## The five verification steps

A verifier — a DeDi server or a relying party — must perform these in order:

{% stepper %}
{% step %}
### Shape

Check the file against its JSON Schema.
{% endstep %}

{% step %}
### Integrity — offline

Canonicalize (JCS) the document minus `proof` and verify `proof.jws` against the file's **embedded** `publisher.key`. Reject on failure.
{% endstep %}

{% step %}
### Authenticity — one cacheable fetch

Fetch `https://{publisher.domain}/.well-known/dedi.index.json`, verify *its* signature against a key it itself lists, and confirm the file's `publisher.key` is present in the manifest's `keys`.

If it is not present, the file is **integrity-valid but not authenticated** — a verifier must not treat it as an authentic statement by that domain.
{% endstep %}

{% step %}
### Freshness

`now ≤ next_update`, for both the file and the manifest. Past it, re-fetch before relying on the content.
{% endstep %}

{% step %}
### Registry state

`registry.state == "live"`. An `inactive` registry is not authoritative.
{% endstep %}
{% endstepper %}

Steps 1 and 2 are location-independent — they work on a copy from any source, offline. Only step 3 touches the network, and its result (the current `keys`) is cacheable for the manifest's `next_update`.

## The signing lifecycle lives in the manifest

* **Rotate** — add the new entry to `keys`, and drop the old one when the changeover is done.
* **Revoke** — remove the entry from `keys`. Files signed by it fail verification everywhere immediately, because "embedded key ∈ `keys`" no longer holds.

There is no separate revocation registry for signing material: presence in `keys` **is** validity. A publisher manages the whole lifecycle by editing its own well-known — no ticket, no operator, no third party.

Unlike deleting a hosted file, removal from `keys` also reaches **cached and relayed copies**, at their next authenticity check.

## Freshness, retirement, and negative lists

A signed file is a **snapshot**: the signature proves *who* and *unmodified-since*, never *still-current*. Three mechanisms carry currency:

* **`next_update`** — every file and manifest carries it. Past it, treat the copy as stale and re-fetch. A publisher whose data is stable still re-issues on a cadence, so "silence" is distinguishable from "unreachable."
* **`updated_at` vs `next_update`** — `updated_at` advances only when content actually changes; `next_update` advances on every re-issue, including a refresh that changes nothing. A revocation list re-published hourly therefore shows a moving `next_update` and a static `updated_at` — which is the signal that its content is unchanged.
* **Registry `state`** — `live` or `inactive`. `inactive` retires a whole directory, and a cached copy still carries that signal, which "removed from the manifest" alone would not reach.

**Removing an entry.** Records carry no per-record state; polarity lives at the registry:

* a **positive directory** (keys, memberships) — presence means valid; remove the entry and freshness propagates it;
* a **negative list** (revocations) — presence means revoked; the record's existence *is* the fact.

To hard-revoke a member of a positive directory, use the pattern PKI has always used: remove it from the positive registry **and** add it to a companion negative registry. No per-record lifecycle field is needed.

## Security considerations

* **Authority is the origin's well-known — an accepted trade-off.** Because the manifest is served from the publisher's own domain, a compromise of the **web host itself**, where an attacker swaps both the well-known `keys` and the files, cannot be caught by the protocol alone. This is the limitation `did:web` and the general TLS/web-PKI model carry. Mitigate with external **monitors** watching a publisher's manifest for unexpected changes to `keys`. In exchange, the trust model needs no central registry.
* **Stolen signing material.** A thief can sign files until the publisher removes the entry from its well-known. Revocation is then immediate and global.
* **Rollback and replay on negative lists.** An old but validly-signed revocation file could hide a newer revocation; `next_update` (stale copies rejected) and monitors watching for a regressing `updated_at` counter this.
* **Canonicalization ambiguity.** Signing raw JSON bytes is unsafe across tools, which is why JCS is mandatory.
