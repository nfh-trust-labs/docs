# Postman Collection

The DeDi.global Postman collection provides a comprehensive set of pre-configured API requests for testing, development, and integration with the DeDi.global platform.

&#x20;<a href="../../../postman-collection.json" class="button primary">Download postman collection</a>

### Quick Setup

1. **Import:** _Open Postman_ → Click _Import_ → _Paste the collection URL or upload the JSON file_
2. **Configure Environment:** The collection includes variables for Base URL, API Key, and Namespace IDs
3. **Set Authentication:** In the collection's Authorization tab, select Bearer Token and enter your API key. All requests inherit this automatically.

### Collection Contents

1. **Core Operations:** Authentication, Namespaces, Registries, Records (CRUD)
2. **Advanced Features:** Domain Verification, Bulk Operations (CSV import/export), Search & Query, Webhooks, Delegation & Access Control
3. **Administrative:** Lifecycle and recovery operations, Version Control

### Features

Pre-configured requests with proper headers, environment variables, test scripts, inline documentation, and error handling examples.

### Usage Tips

1. Start with Authentication to get your API key
2. Use environment variables for base URLs and common IDs
3. Follow the workflow: Namespace → Registry → Record
4. Use automated response tests to catch issues

### Related Resources

* [OpenAPI Specification](openapi-specification.md): Complete API schema
* [API Reference Documentation](../standard-apis/): Detailed endpoint documentation
* [Quickstart Guide](../quickstart/): Step-by-step integration guide

### Support

If you encounter issues with the Postman collection:

1. Check the [FAQ section](../../miscellaneous/faqs.md)
2. Review the [API Reference](../standard-apis/) for endpoint details
3. Contact support through our official channels
