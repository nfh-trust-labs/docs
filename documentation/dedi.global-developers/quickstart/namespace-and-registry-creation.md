# User Guide - DeDi.Global namespace & directory creation

DeDi.global is a ready-to-use SaaS implementing the open DeDi protocol, offered by the Networks for Humanity Foundation. It allows registrars to publish and manage public registries on a decentralized infrastructure.

<details>

<summary>RECAP - Core Concepts</summary>

* **Namespace** — corresponds to a domain (and its organisation). Think of it as the digital "root" under which all directories and records live. Users can own multiple namespaces, similar to owning multiple domains.
* **Directory (Registry)** — a list of records with a configurable schema, sitting under a namespace. The terms _directory_ and _registry_ are interchangeable.
* **Record** — an individual entry within a directory, structured according to the directory's schema.

</details>

### <mark style="color:$tint;">1. Account Setup</mark>

1. **Register** at [publish.dedi.global](https://publish.dedi.global/) with any email address.
2. **Verify your account** — click the authentication link sent to your email.
3. **Log in** — returning users authenticate via password + a one-time email link (MFA).
4. **Generate an API Key** _(optional)_ — for secure programmatic access via the DeDi APIs.

### <mark style="color:$tint;">2. Namespace Setup</mark>

1. **Create a Namespace** — provide a name and description via the dashboard or API.
2. **Verify the Namespace** _(optional but recommended)_ — linking a namespace to a domain gives it "verified" status and makes its contents publicly accessible.

{% hint style="danger" icon="compass" %}
#### **Domain Verification Steps**

1. **Request domain whitelisting** — submit your domain [here](https://docs.google.com/forms/d/e/1FAIpQLScAwEZh94DCIw70zrxWUJ1cud_wYfo_WEjqDpEmbxaw5ZF9aw/viewform?usp=dialog). Requests are processed within \~5 minutes if no adverse signals are detected.
2. **Get the TXT record** — a DNS TXT record is generated for your domain.
3. **Update DNS** — add the generated TXT record to your domain's DNS configuration.
4. **Click "Verify"** — confirm verification once the DNS update has propagated.
{% endhint %}

### <mark style="color:$tint;">3. Registry & Records</mark>

1. **Create a Registry** — provide a name, description, and select a schema template.
   * Available templates: **Membership**, **Public Key**, **Revocation**, **Beckn schemas**.
   * Custom schemas can be provided via JSON Schema Draft 7 (ideally published on a standard like schema.org).
2. **Add Delegates** _(optional)_ — assign delegates to help manage the registry.
3. **Add Records** — create entries in the registry per the selected schema.

### <mark style="color:$tint;">4. Ongoing Management</mark>

You can update **namespace details**, **registry details**, and **record details** at any time through the dashboard or APIs. State management options are available for both registries and records.

### <mark style="color:$tint;">5. Accessing Data</mark>

Use the **Standard APIs** to query and manage data. Refer to the [API reference](../standard-apis/) section for usage patterns and sample implementations.

📹 **Video walkthrough** — [Loom demo](https://www.loom.com/share/e0293309701348dc95719a98b957d12c?sid=04b8dc00-51de-46ac-82ce-adc1a0060409)
