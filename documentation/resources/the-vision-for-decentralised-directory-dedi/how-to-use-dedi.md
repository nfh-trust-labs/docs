---
description: Three ways to put your public data on tamper-proof, verifiable infrastructure
---

# How to Use DeDi

DeDi turns any public registry — entity lists, public keys, schemas, credential status — into machine-readable, cryptographically verifiable records accessible via a single API. All data on DeDi is public data. There is no vendor lock-in: the protocol is open, the data is portable, and you can leave at any time.

## Choose Your Path

<details>

<summary><mark style="color:$primary;background-color:orange;"><strong>Option A: Publish on DeDi.global + mirror on your own website</strong></mark></summary>

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

<summary><mark style="color:$primary;background-color:orange;"><strong>Option B: Publish on DeDi.global only</strong></mark></summary>



> Simplest, safe path  —  Zero infrastructure, zero maintenance

Publish your registries directly on DeDi.global. Anyone can query them via the DeDi API. You don’t need to build, host, or maintain anything. This is the fastest way to make your public data machine-readable and verifiable. Ideal for organizations that currently distribute data as PDFs, spreadsheets, or static web pages and want to modernize without any engineering effort.

| Setup effort        | Minutes. Create a namespace, publish records. Done.                                                                                    |
| ------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| Infrastructure cost | Zero.                                                                                                                                  |
| Maintenance         | Zero.                                                                                                                                  |
| Lock-in risk        | None. Same portability guarantees as Option A.                                                                                         |
| Trade-off           | Your existing website doesn’t serve the data directly - consumers use the DeDi API. You can add website integration later at any time. |

</details>

<details>

<summary><mark style="color:$primary;background-color:orange;"><strong>Option C: Spin up your own DeDi node</strong></mark></summary>

> Maximum control  —  For organizations with specific infrastructure requirements

Deploy the open-source DeDi protocol on your own infrastructure. You run the node, you control the data, you manage the uptime. Records anchored on your node are interoperable with DeDi.global and any other DeDi node — the protocol is the same everywhere. This option is for organizations that have regulatory, sovereignty, or policy reasons to self-host.

| Setup effort        | Weeks to Months. Requires DevOps capacity to deploy and configure the node and capacity to ensure transparent cryptographic validity               |
| ------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| Infrastructure cost | Hosting, monitoring, and maintenance costs.                                                                                                        |
| Maintenance         | Ongoing. You are responsible for uptime, security patches, and upgrades.                                                                           |
| Lock-in risk        | None. Same open protocol. You can migrate to DeDi.global or another node at any time.                                                              |
| When to choose this | Only if you have a hard requirement to self-host. For most organizations, Option A or B delivers the same guarantees with zero operational burden. |

</details>

## At a Glance

| Metric               | Option A: DeDi.global + Your Site | Option B: DeDi.global Only |       Option C: Own Node       |
| -------------------- | :-------------------------------: | :------------------------: | :----------------------------: |
| Setup time           |              Minutes              |           Minutes          |         Weeks - Months         |
| Infrastructure cost  |                Zero               |            Zero            |             You pay            |
| Maintenance          |                Zero               |            Zero            |          You maintain          |
| On-chain anchoring   |                 ✓                 |              ✓             | Depends on your implementation |
| Cryptographic proofs |                 ✓                 |              ✓             | Depends on your implementation |
| Revocation registry  |                 ✓                 |              ✓             |                ✓               |
| Sub-200ms API        |                 ✓                 |              ✓             |      Depends on your infra     |
| Data on your domain  |                 ✓                 |              ✘             |                ✓               |
| Data portability     |                Full               |            Full            |              Full              |

## The Bottom Line

Your data is already public. DeDi makes it trustworthy and discoverable.

If you publish public registries today — entity lists, public keys, schemas, credential status, authorization records — they are already meant to be accessible. The question is not whether to share them, but whether consumers can trust what they find.

DeDi.global adds cryptographic proof, tamper-evidence, versioning, and a universal API to data you are already publishing. It costs nothing, requires no infrastructure, creates no vendor dependency, and can be reversed at any time.

For most organizations, Option A (DeDi.global + your website) or Option B (DeDi.global only) is the obvious starting point. You can always upgrade to a self-hosted node later if your requirements demand it.
