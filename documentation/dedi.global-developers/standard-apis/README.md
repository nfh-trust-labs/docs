# API Reference

## Base URL

All API requests should be made to:

```
https://api.dedi.global
```

## Authentication

DeDi.global supports two authentication methods:

- API key authentication for production integrations
- Cookie authentication for browser-based flows

Most write APIs require authentication. For the current authentication endpoints and flows, refer to [Authentication APIs](./authentication.md).

### Using Your API Key

Include your API key in authenticated requests as a bearer token:

```typescript
const response = await fetch('https://api.dedi.global/endpoint', {
  method: 'GET',
  headers: {
    'Authorization': `Bearer ${your_api_key}`,
    'Content-Type': 'application/json'
  }
});
```

**Authentication Status**: Each API endpoint in this documentation is clearly marked with:

* 🔒 **Authentication Required** - Must include API key
* 🌐 **Public Access** - No authentication needed

## API Sections Overview

### [Authentication APIs](./authentication.md)

Manage registration, login, email verification, API key generation, session inspection, token refresh, logout, and password reset flows.

* **Register/Login Flow**: Start account creation or sign-in with magic-link verification
* **Session Management**: Verify email, inspect the current user, refresh credentials, and logout
* **API Key Issuance**: Generate bearer tokens for server-side integrations
* **Password Recovery**: Reset passwords through authenticated and magic-link flows

### [Publish APIs](./publish.md)

Create and manage the core components of your data infrastructure on DeDi.global.

* **Namespace Management**: Create isolated environments to organize your data
* **Registry Creation**: Define structured schemas for data collection
* **Record Management**: Store draft records and publish them in batches
* **Bulk Operations**: Handle large-scale data uploads via CSV files
* **CSV Tooling**: Download schema-derived sample CSVs and failed-entry reports

### [Access APIs](./access.md)

Query and retrieve data from the DeDi.global network with powerful search capabilities.

* **Lookup Operations**: Direct access to specific entities using identifiers
* **Query Interface**: Advanced filtering and pagination across namespaces and registries
* **Version History**: Access historical data and track changes over time
* **Schema Discovery**: Find registries by standardized schema tags

### [Domain Verification](./domain.md)

Establish domain ownership to build trust and enhance namespace credibility.

* **DNS TXT Generation**: Create unique verification records for domains
* **Domain Ownership Verification**: Validate control through DNS validation
* **Trust Enhancement**: Increase user confidence through verified domain status
* **Verification Monitoring**: Track and confirm verification progress

### [Delegation Management](./delegation.md)

Enable collaborative access and team-based workflows through controlled permission sharing.

* **Namespace Delegation**: Grant administrative access to entire namespaces for broad control
* **Registry Delegation**: Provide specific registry management permissions for focused collaboration
* **Access Control**: Manage team permissions with principle of least privilege
* **Organizational Workflows**: Support complex organizational structures and project-based access patterns

### [Update Management](./update.md)

Modify existing data while maintaining integrity and audit trails.

* **Record Updates**: Edit records with version tracking
* **Registry Modifications**: Update description and metadata
* **Namespace Changes**: Modify namespace properties and settings

### [State Management](./state-management.md)

Control the lifecycle and availability of your data.

* **Publication Control**: Manage record visibility and accessibility
* **Archival Operations**: Archive outdated or obsolete data
* **Status Tracking**: Monitor the current state of all entities

### [Advanced Search](./advanced-search.md)

Powerful search capabilities across the entire namespaces.

* **Cross-Registry Search**: Query multiple registries simultaneously
* **Filtering Options**: Apply complex filters to narrow results
* **Aggregation Queries**: Perform analytical operations on data sets

### [Subscription](./subscription.md)

Stay informed about changes to data you care about.

* **Webhook Integration**: Receive real-time notifications of changes
* **Event Filtering**: Subscribe to specific types of updates
* **Subscription Management**: Control your notification preferences

### [Lookup Verification](./lookup-verification.md)

Advanced verification and data integrity operations.

* **Cryptographic Verification**: Validate data authenticity and integrity
* **Trust Chain Analysis**: Understand the verification path of data
* **Compliance Checking**: Ensure data meets required standards

## Error Handling

All APIs follow consistent error response patterns:

```typescript
{
  "error": {
    "code": "ERROR_CODE",
    "message": "Human-readable error description",
    "details": {
      // Additional context when available
    }
  }
}
```

Common HTTP status codes:

* `200` - Success
* `400` - Bad Request (invalid parameters)
* `401` - Unauthorized (missing or invalid API key)
* `403` - Forbidden (insufficient permissions)
* `404` - Not Found (resource doesn't exist)
* `429` - Rate Limit Exceeded
* `500` - Internal Server Error

## Getting Started

1. [Obtain your API key](./#authentication) from the DeDi.global platform
2. Review the [Authentication APIs](./authentication.md) flow and generate credentials
3. Start with [Publish APIs](./publish.md) to create your first namespace and registry
4. Use [Access APIs](./access.md) to query and retrieve your data
