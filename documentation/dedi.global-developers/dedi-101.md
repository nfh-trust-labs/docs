# DeDi 101

The **DeDi Protocol** defines a universal, open standard for discovering and verifying public information across the internet. It specifies a machine-readable, unified API that enables interoperability and trust in querying and verifying public directories.

**DeDi Global** is a hosted implementation of the DeDi (Decentralized Directories) Protocol.

Before proceeding, review the core protocol specifications available [here](https://github.com/LF-Decentralized-Trust-labs/decentralized-directory-protocol) to gain a foundational understanding of the DeDi architecture and design principles.

Anyone implementing the DeDi Protocol or integrating with DeDi Global must be familiar with its **three-tier architecture** and **API conventions**, which together enable secure and verifiable data discoverability.

The **DeDi architecture** is organized into three hierarchical layers:

#### 1. Namespace (Root Level)

Represents the root domain or trust anchor.\
Each namespace corresponds to a domain name and defines the boundary of trust for the registries it manages.

Establishing the root of trust is critical; without it, discoverable data lacks contextual integrity.\
The most common form of identity today is a domain name, which serves as the namespace identifier.\
Consequently, all DeDi-compliant APIs begin with the namespace identifier.

#### 2. Registry (Intermediate Level)

A registry represents a logical grouping of records within a namespace.\
It defines the schema or structure of the data it contains.

#### 3. Record (Leaf Level)

Individual entries or data points within a registry.

These three entities — **namespace**, **registry**, and **record** — are tightly coupled.\
DeDi assumes that a consumer knows which domain (namespace) and registry they intend to query.

For example, a person’s SSN could appear in multiple registries depending on context; therefore, querying without specifying the correct namespace and registry may yield meaningless results.
