# Postman Collection

The DeDi.global Postman collection provides a comprehensive set of pre-configured API requests for testing, development, and integration with the DeDi.global platform.

## 📥 Download Collection

**Direct Link**: [Download Postman Collection](https://github.com/nfh-trust-labs/docs/blob/main/postman-collection.json)

## 🚀 Quick Setup

### 1. Import Collection
1. Open Postman application
2. Click **Import** in the top toolbar
3. Paste the collection URL or upload the downloaded JSON file
4. Click **Import** to add the collection to your workspace

### 2. Configure Environment
The collection includes environment variables for:
- **Base URL**: API endpoint configuration
- **API Key**: Authentication token storage
- **Namespace IDs**: Commonly used identifiers

### 3. Set Authentication
1. Navigate to collection **Authorization** tab
2. Select **Bearer Token** type
3. Enter your API key in the token field
4. Authentication will be inherited by all requests

## 📋 Collection Contents

The collection includes organized folders for:

### Core Operations
- **Authentication**: Login, logout, and token management
- **Namespaces**: Create, manage, and verify namespaces
- **Registries**: Schema management and registry operations
- **Records**: CRUD operations for record management

### Advanced Features
- **Domain Verification**: Complete verification workflow
- **Bulk Operations**: CSV import/export functionality
- **Search & Query**: Advanced search capabilities
- **Webhooks**: Subscription and notification management
- **Delegation**: Access control and permission management

### Administrative
- **State Management**: Suspend, reinstate, revoke operations
- **Version Control**: History and version management
- **Analytics**: Usage statistics and performance metrics

## ✨ Features

- **Pre-configured Requests**: All endpoints with proper headers and parameters
- **Environment Variables**: Centralized configuration management
- **Test Scripts**: Automated response validation
- **Documentation**: Inline descriptions and examples
- **Error Handling**: Common error scenarios and responses

## 💡 Usage Tips

1. **Start with Authentication**: Get your API key first
2. **Use Environment Variables**: Set base URLs and common IDs
3. **Follow the Workflow**: Namespace → Registry → Record creation flow
4. **Check Response Tests**: Automated validations help catch issues
5. **Save Responses**: Use environments to store dynamic values

## 🔗 Related Resources

- [OpenAPI Specification](openapi-specification.md): Complete API schema
- [API Reference Documentation](../standard-apis/README.md): Detailed endpoint documentation
- [Quickstart Guide](../quickstart/README.md): Step-by-step integration guide

## 📞 Support

If you encounter issues with the Postman collection:
1. Check the [FAQ section](../../miscellaneous/faqs.md)
2. Review the [API Reference](../standard-apis/README.md) for endpoint details
3. Contact support through our official channels

The Postman collection is regularly updated to reflect the latest API changes and improvements.