# Features of DeDi.global

### **Cryptographic Trust**

<table data-view="cards"><thead><tr><th></th><th></th></tr></thead><tbody><tr><td><h4><mark style="color:$primary;background-color:orange;"><strong>On-chain anchoring</strong></mark></h4></td><td><em>Every namespace, registry, and record is anchored on the CORD blockchain (BLAKE2 H256). Any change creates an immutable, auditable trail that can be verified directly on-chain.</em></td></tr><tr><td><h4><mark style="color:$primary;background-color:orange;"><strong>Provenance</strong></mark></h4></td><td><em>Each entry carries a verifiable chain of authorship: who published it, when, and under which namespace. DID Documents are auto-generated for every profile.</em></td></tr><tr><td><h4><mark style="color:$primary;background-color:orange;"><strong>Tamper evidence</strong></mark></h4></td><td><em>Any modification — even by the publisher — generates a new version with its own proof. Historical states remain verifiable through the API and on-chain.</em></td></tr></tbody></table>

### Lifecycle management

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><h4><mark style="color:$primary;background-color:orange;"><strong>Versioning</strong></mark> </h4></td><td><em>Every change creates a new version. Full history is accessible via the Versions API. Historical lookups via the as_on date parameter retrieve the state at any point in time.</em></td><td></td></tr><tr><td><h4><mark style="color:$primary;background-color:orange;"><strong>State management</strong></mark></h4></td><td><em>Namespaces remain live, registries move between live and inactive, and records move from draft to live. Deleted entities remain recoverable during the restore window before permanent archival.</em> </td><td><a href="../../dedi.global-developers/guides/state-management.md">state-management.md</a></td></tr><tr><td><h4><mark style="color:$primary;background-color:orange;"><strong>Watch &#x26; Subscribe</strong></mark></h4></td><td><em>Real-time notifications when registries, records, or tags change. Subscribe to a namespace/ registry/ registry schema for alerts on new or modified records.</em> </td><td><a href="../../dedi.global-developers/standard-apis/subscription.md">subscription.md</a></td></tr><tr><td><h4><mark style="color:$primary;background-color:orange;">Delegation</mark></h4></td><td><em>Grant management permissions to other DeDi users at namespace or registry level. Delegates can publish and manage records on behalf of the owner.</em></td><td><a href="../../dedi.global-developers/standard-apis/delegation.md">delegation.md</a></td></tr><tr><td><h4><mark style="color:$primary;background-color:orange;">Schema design</mark></h4></td><td><em>Configurable schemas define entry structure and types. Schemas are versioned and machine-readable, enabling automated validation.</em></td><td></td></tr></tbody></table>

## Zero Lock-in by Design

Every design decision ensures adopters bear zero switching cost and zero platform risk:

<table data-header-hidden><thead><tr><th width="374"></th><th></th></tr></thead><tbody><tr><td><strong>Open protocol</strong></td><td>DDP is an open spec under LFDT. Anyone can implement it. DeDi.global is one implementation — not the only one. Records are interoperable across all nodes.</td></tr><tr><td><strong>Full data portability</strong></td><td>Standard JSON with W3C-aligned proofs. Export your namespace and host it elsewhere at any time.</td></tr><tr><td><strong>No fees, no contracts</strong></td><td>Free to publish, free to query. No licensing. No exit clause. Stop anytime.</td></tr></tbody></table>



<br>
