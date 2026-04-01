# Domain Verification - The bedrock of trust in DeDi

### Why Verify a Domain?

Domain verification helps establish trust and provides several benefits:

* Makes your namespace identifiers human-readable
* Establishes clear provenance of data
* Associates your namespace with a verified domain name
* Improves the readability and trustworthiness of API interactions

### Example

#### Before Domain Verification

```http
GET dedi/76EU7hbHV3aEnjjMZKTrPqbudeYrymjYR761h6v6egEe6JMjnkU6HT/employees/Alice
```

#### After Domain Verification

```http
GET dedi/dhiway.com/employees/Alice
```

The verified domain makes the URL human-readable and helps establish clear provenance of the data.

### Verification Process

#### Prerequisites

* A registered domain name that you control
* Access to your domain's DNS settings
* A namespace ID of the namespace you created

#### Steps

1. **Domain Whitelisting**
   * Contact our support team to whitelist your domain
2.  **Generate DNS Record**

    * Use our API/UI to generate a DNS TXT record

    ```http
     GET dedi/generate-dns-txt/{{namespace_id}}/{{domain}}
    ```
3. **DNS Configuration**
   * Add the provided TXT record to your domain's DNS settings
   * Wait for DNS propagation (typically 24-48 hours)
4.  **Verify Domain**

    * Use either the UI or API to complete verification

    API Endpoint:

    ```http
    POST /dedi/verify-domain
    ```

    Request Body:

    ```json
    {
      "namespace_id": "string"
    }
    ```

### What's Next?

After verification, you can:

* Use your domain name instead of namespace ID in all API calls
* Establish stronger trust with your data consumers
* Create a more professional and polished API experience
