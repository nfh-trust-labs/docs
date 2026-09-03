---
description: Four ways to put your public data on tamper-proof, verifiable infrastructure
---

# How to Use DeDi

DeDi turns any public registry — entity lists, public keys, schemas, credential status — into machine-readable, cryptographically verifiable records. All data on DeDi is public data. There is no vendor lock-in: the protocol is open, the data is portable, and you can leave at any time.

**You adopt DeDi by publishing signed DeDi files.** Where those files live is a deployment choice, not a trust decision: a verifier checks your signature against the signing key declared at your own domain, whoever serves the bytes. The options below differ in who does the hosting and the re-issuing — not in what a relying party gets.

> _For registrars who would rather not host and manage signed files themselves,_ [_dedi.global_](http://dedi.global) _is offered by the_ [_Network for Humanity Foundation_](https://networksforhumanity.org/) _as a ready to use, Universal Digital Infrastructure, in alignment with DPI (digital public infrastructure)_ [_principles_](https://docs.cdpi.dev/the-dpi-wiki/dpi-tech-architecture-principles)_. This philanthropic initiative allows registrars to effortlessly publish and manage their directories using an open, on chain infrastructure — complementing and fully aligned with the open DeDi protocol._

## Choose Your Path

<details>

<summary><mark style="color:$primary;background-color:orange;"><strong>Option A: Publish signed files on your own domain</strong></mark></summary>

> _Protocol-native — your data on your domain, no server to run_

Sign one DeDi file per directory, serve a signed `/.well-known/dedi.index.json` declaring your signing key, and host both on infrastructure you already have — your website, a source repository, or any static host. Anyone can verify your directories end to end from the files alone; DeDi servers can crawl, verify, and index them so consumers can query them through a standard API.

| Setup effort        | Hours to days. Generate a signing key, produce the files, wire signing into whatever already updates the data.                                                                        |
| ------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Infrastructure cost | Zero to minimal. Static JSON on hosting you already run.                                                                                                                            |
| Maintenance         | Yours: re-issue on a cadence so `next_update` stays fresh, and manage your signing key through your own well-known.                                                                 |
| Lock-in risk        | None — there is no operator in the path at all.                                                                                                                                     |
| What you get        | Publisher-signed, offline-verifiable directories on your own domain; self-service key rotation and revocation; no dependency on any operator.                                        |
| Note                | Files published this way are verifiable by any conformant verifier, and once your domain is on the discovery list they are [indexed and served by dedi.global](../publishing-dedi-files/dedi.global-and-origin-published-files.md) too. |

<a href="../publishing-dedi-files/" class="button primary">How to publish DeDi files</a>

</details>

<details>

<summary><mark style="color:$primary;background-color:orange;"><strong>Option B: Publish on DeDi.global + mirror on your own website</strong></mark></summary>

> _Recommended as phase 0  —  Best of both worlds_

Publish your registries on DeDi.global (free, instant, zero infrastructure) and embed the same data on your own website via DeDi’s API or widget. Your users get the convenience of your existing site AND the cryptographic guarantees of DeDi. If you ever want to stop using DeDi.global, your website continues to serve the data — you just lose the on-chain anchoring until you point to another node.

| Setup effort        | Minutes. Create a namespace, publish records via API or dashboard. Embed widget or API calls on your site.                                                  |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Infrastructure cost | Zero. DeDi.global is free. Your website is already running.                                                                                                 |
| Maintenance         | Zero on the DeDi side. NFH operates and maintains DeDi.global.                                                                                              |
| Lock-in risk        | None. Data is portable. Protocol is open. You can export and leave at any time.                                                                             |
| What you get        | Tamper-proof records, on-chain anchoring, versioning, cryptographic proofs, sub-200ms API, revocation infrastructure — all without running a single server. |

</details>

<details>

<summary><mark style="color:$primary;background-color:orange;"><strong>Option C: Publish on DeDi.global only</strong></mark></summary>



> Simplest, safe path  —  Zero infrastructure, zero maintenance

Publish your registries directly on DeDi.global. Anyone can query them via the DeDi API. You don’t need to build, host, or maintain anything. This is the fastest way to make your public data machine-readable and verifiable. Ideal for organizations that currently distribute data as PDFs, spreadsheets, or static web pages and want to modernize without any engineering effort.

| Setup effort        | Minutes. Create a namespace, publish records. Done.                                                                                    |
| ------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| Infrastructure cost | Zero.                                                                                                                                  |
| Maintenance         | Zero.                                                                                                                                  |
| Lock-in risk        | None. Same portability guarantees as Option B.                                                                                         |
| Trade-off           | Your existing website doesn’t serve the data directly - consumers use the DeDi API. You can add website integration later at any time. |

</details>

<details>

<summary><mark style="color:$primary;background-color:orange;"><strong>Option D: Operate your own DeDi server</strong></mark></summary>

> Maximum control  —  For organizations with specific infrastructure requirements

Deploy the open-source DeDi protocol on your own infrastructure. You run the server, you control the data, you manage the uptime. Records anchored on your server are interoperable with DeDi.global and any other DeDi implementation — the protocol is the same everywhere. This option is for organizations that have regulatory, sovereignty, or policy reasons to self-host, or that want to index and serve other publishers' directories at scale.

**Running a server is a separate role, not a requirement of adoption.** If your goal is only to publish your own directories, Option A gives you the same guarantees with none of the operational burden.

| Setup effort        | Weeks to Months. Requires DevOps capacity to deploy and configure the server and capacity to ensure transparent cryptographic validity                        |
| ------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Infrastructure cost | Hosting, monitoring, and maintenance costs.                                                                                                                        |
| Maintenance         | Ongoing. You are responsible for uptime, security patches, and upgrades.                                                                                           |
| Lock-in risk        | None. Same open protocol. You can migrate to DeDi.global or another server at any time.                                                                            |
| When to choose this | Only if you have a hard requirement to self-host, or you intend to serve many publishers. For most organizations, Option A, B, or C delivers the same guarantees with far less operational burden. |

</details>

## At a Glance

| Metric               | A: Your own domain               | B: DeDi.global + Your Site | C: DeDi.global Only |      D: Own Server             |
| -------------------- | :------------------------------: | :------------------------: | :-----------------: | :----------------------------: |
| Setup time           |          Hours - Days            |           Minutes          |       Minutes       |         Weeks - Months         |
| Infrastructure cost  |          Zero - Minimal          |            Zero            |         Zero        |             You pay            |
| Maintenance          |        You re-issue & sign       |            Zero            |         Zero        |          You maintain          |
| Publisher signature  |                 ✓                |              ✓             |          ✓          |                ✓               |
| Offline verification |                 ✓                |         Via the API        |     Via the API     | Depends on your implementation |
| On-chain anchoring   |          Not by itself           |              ✓             |          ✓          | Depends on your implementation |
| Revocation registry  |                 ✓                |              ✓             |          ✓          |                ✓               |
| Sub-200ms API        |        Via a DeDi server         |              ✓             |          ✓          |      Depends on your infra     |
| Data on your domain  |                 ✓                |              ✓             |          ✘          |                ✓               |
| Data portability     |               Full               |            Full            |         Full        |              Full              |

## The Bottom Line

Your data is already public. DeDi makes it trustworthy and discoverable.

If you publish public registries today — entity lists, public keys, schemas, credential status, authorization records — they are already meant to be accessible. The question is not whether to share them, but whether consumers can trust what they find.

DeDi adds cryptographic proof, tamper-evidence, and a universal way to discover and verify data you are already publishing. It costs nothing, creates no vendor dependency, and can be reversed at any time.

Choose **Option A** if the data should live on your own domain and you can sign and re-issue it there. Choose **Option B or C** if you would rather someone else carried the hosting, signing, and anchoring. Either way, you can move later: the files, the records, and the guarantees are the same.
