# API Reference

Welcome to the DeDi.global API Reference. This comprehensive guide covers all APIs available for integrating with the DeDi.global decentralized directory system. DeDi.global provides a robust infrastructure for creating, managing, and querying structured data registries with built-in verification and cryptographic security.

## Base URL

All API requests should be made to:
```
https://api.dedi.global
```

## Authentication

Before using any DeDi.global APIs, you need to obtain an API key for authentication. Most APIs require authentication except for public lookup and query operations.

### Getting Your API Key

1. **Navigate to the DeDi.global Platform**  
   Go to [https://publish.dedi.global](https://publish.dedi.global)

2. **Register or Login**
   - **First-time users**: Click "Register" and provide:
     - Name
     - Email address
     - Password (minimum 6 characters with special characters)
   - **Existing users**: Click "Login" with your credentials

3. **Email Verification**  
   Check your email for a magic link and click it to verify your account (Multi-Factor Authentication)

4. **Generate API Key**
   - Once logged in, click the profile button in the top-right corner
   - Select "Get API Key" 
   - Copy the generated API key for use in your applications

> **Note**: You can attach screenshots of the registration flow, login interface, and API key generation process to help users navigate the platform more easily. Place images in an `assets/` folder and reference them using relative paths.

### Using Your API Key

Include your API key in all authenticated requests as a Bearer token in the Authorization header:

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
- 🔒 **Authentication Required** - Must include API key
- 🌐 **Public Access** - No authentication needed

## API Sections Overview

### [Publish APIs](documentation/dedi.global-developers/standard-apis/publish.md)
Create and manage the core components of your data infrastructure on DeDi.global.

- **Namespace Management**: Create isolated environments to organize your data
- **Registry Creation**: Define structured schemas for data collection
- **Record Management**: Store, draft, and publish individual data records
- **Bulk Operations**: Handle large-scale data uploads via CSV files
- **Data Export**: Extract registry data for analysis and backup

### [Access APIs](documentation/dedi.global-developers/standard-apis/access.md)
Query and retrieve data from the DeDi.global network with powerful search capabilities.

- **Lookup Operations**: Direct access to specific entities using identifiers
- **Query Interface**: Advanced filtering and pagination across namespaces and registries
- **Version History**: Access historical data and track changes over time
- **Schema Discovery**: Find registries by standardized schema tags

### [Domain Verification](documentation/dedi.global-developers/standard-apis/domain.md)
Establish domain ownership to build trust and enhance namespace credibility.

- **DNS TXT Generation**: Create unique verification records for domains
- **Domain Ownership Verification**: Validate control through DNS validation
- **Trust Enhancement**: Increase user confidence through verified domain status
- **Verification Monitoring**: Track and confirm verification progress

### [Delegation Management](documentation/dedi.global-developers/standard-apis/delegation.md)
Enable collaborative access and team-based workflows through controlled permission sharing.

- **Namespace Delegation**: Grant administrative access to entire namespaces for broad control
- **Registry Delegation**: Provide specific registry management permissions for focused collaboration
- **Access Control**: Manage team permissions with principle of least privilege
- **Organizational Workflows**: Support complex organizational structures and project-based access patterns

### [Update Management](documentation/dedi.global-developers/standard-apis/update.md)
Modify existing data while maintaining integrity and audit trails.

- **Record Updates**: Edit records with version tracking
- **Registry Modifications**: Update description and metadata
- **Namespace Changes**: Modify namespace properties and settings

### [State Management](documentation/dedi.global-developers/standard-apis/state-management.md)
Control the lifecycle and availability of your data.

- **Publication Control**: Manage record visibility and accessibility
- **Archival Operations**: Archive outdated or obsolete data
- **Status Tracking**: Monitor the current state of all entities

### [Advanced Search](documentation/dedi.global-developers/standard-apis/advanced-search.md)
Powerful search capabilities across the entire namespaces.

- **Cross-Registry Search**: Query multiple registries simultaneously
- **Filtering Options**: Apply complex filters to narrow results
- **Aggregation Queries**: Perform analytical operations on data sets

### [Subscription](documentation/dedi.global-developers/standard-apis/subscription.md)
Stay informed about changes to data you care about.

- **Webhook Integration**: Receive real-time notifications of changes
- **Event Filtering**: Subscribe to specific types of updates
- **Subscription Management**: Control your notification preferences

### [Lookup Verification](documentation/dedi.global-developers/standard-apis/lookup-verification.md)
Advanced verification and data integrity operations.

- **Cryptographic Verification**: Validate data authenticity and integrity
- **Trust Chain Analysis**: Understand the verification path of data
- **Compliance Checking**: Ensure data meets required standards

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
- `200` - Success
- `400` - Bad Request (invalid parameters)
- `401` - Unauthorized (missing or invalid API key)
- `403` - Forbidden (insufficient permissions)
- `404` - Not Found (resource doesn't exist)
- `429` - Rate Limit Exceeded
- `500` - Internal Server Error

<!-- ## Rate Limits

API requests are subject to rate limiting:
- **Authenticated requests**: 1000 requests per hour
- **Public endpoints**: 100 requests per hour
- **Bulk operations**: Special limits apply (see individual endpoint documentation)

Rate limit information is included in response headers:
- `X-RateLimit-Limit` - Total requests allowed
- `X-RateLimit-Remaining` - Requests remaining in current window
- `X-RateLimit-Reset` - Time when rate limit resets -->

## Getting Started

1. [Obtain your API key](#authentication) from the DeDi.global platform
2. Start with [Publish APIs](documentation/dedi.global-developers/standard-apis/publish.md) to create your first namespace and registry
3. Use [Access APIs](documentation/dedi.global-developers/standard-apis/access.md) to query and retrieve your data
4. Explore [advanced features](documentation/dedi.global-developers/standard-apis/domain.md) like domain verification and webhooks
