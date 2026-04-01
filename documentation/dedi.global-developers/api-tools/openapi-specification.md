# OpenAPI Specification

The DeDi.global OpenAPI specification provides a comprehensive, machine-readable description of our entire API surface. This specification follows OpenAPI 3.0 standards and enables automated tooling, code generation, and integration workflows.

## 📥 Download Specification

**Direct Link**: [Download OpenAPI Specification](https://github.com/nfh-trust-labs/docs/blob/main/openAPI.yaml)

## 🛠️ Usage Scenarios

### Code Generation
Generate client SDKs in multiple programming languages:
```bash
# Generate Python client
openapi-generator-cli generate -i openAPI.yaml -g python -o ./dedi-python-client

# Generate JavaScript client  
openapi-generator-cli generate -i openAPI.yaml -g javascript -o ./dedi-js-client

# Generate Java client
openapi-generator-cli generate -i openAPI.yaml -g java -o ./dedi-java-client
```

### API Documentation
Generate interactive documentation:
```bash
# Swagger UI
swagger-ui-serve openAPI.yaml

# Redoc
redoc-cli serve openAPI.yaml
```

### API Testing
Automated testing with tools like:
- **Postman**: Import specification directly
- **Insomnia**: Generate collections from spec
- **Newman**: Automated test execution
- **Dredd**: API contract testing

## 🏗️ Specification Structure

### OpenAPI 3.0 Features
- **Complete Endpoint Coverage**: All API endpoints documented
- **Schema Definitions**: Reusable component schemas
- **Authentication Security**: Bearer token and cookie auth
- **Response Examples**: Real-world response samples
- **Error Documentation**: Comprehensive error codes

### Core Sections

#### Info & Metadata
- API version and description
- Contact information and support links
- License and terms of service

#### Security Definitions
- Bearer token authentication
- Cookie-based authentication
- Security requirement mappings

#### Path Operations
- All HTTP methods and endpoints
- Request/response schemas
- Parameter definitions
- Status code documentation

#### Component Schemas
- Data model definitions
- Reusable parameter objects
- Response schema templates
- Error response structures

## 🔧 Integration Tools

### Popular Generators
- **OpenAPI Generator**: Multi-language client generation
- **Swagger Codegen**: Legacy code generation support
- **AutoRest**: Microsoft's code generation tool
- **OpenAPI Tools**: Community-driven generators

### Documentation Tools
- **Swagger UI**: Interactive API explorer
- **Redoc**: Beautiful API documentation
- **Stoplight**: API design and documentation platform
- **Insomnia**: API client with OpenAPI support

### Testing & Validation
- **Spectral**: OpenAPI linting and validation
- **OpenAPI Enforcer**: Schema validation
- **Prism**: Mock server generation
- **Dredd**: API contract validation

## 💡 Best Practices

### Code Generation
1. **Version Pinning**: Pin specific generator versions for consistency
2. **Custom Templates**: Customize generated code for your needs
3. **Configuration Files**: Use config files for generator options
4. **Generated Code Review**: Always review and test generated clients

### Documentation Generation
1. **Theme Customization**: Brand documentation to match your style
2. **Interactive Examples**: Enable "Try it out" functionality
3. **Response Samples**: Include realistic response examples
4. **Error Scenarios**: Document common error conditions

### API Evolution
1. **Backwards Compatibility**: Maintain compatibility across versions
2. **Deprecation Notices**: Clear deprecation paths and timelines
3. **Version Management**: Semantic versioning for API changes
4. **Change Documentation**: Detailed changelog maintenance

## 📋 Specification Contents

### Authentication Endpoints
- Password-based login/logout
- API key management
- Token refresh mechanisms

### Core Resource Management
- Namespace operations (CRUD)
- Registry management with schemas
- Record lifecycle management

### Advanced Features
- Domain verification workflows
- Bulk import/export operations
- Real-time webhook subscriptions
- Delegation and access control

### Administrative Operations
- State management (suspend/reinstate/revoke)
- Version history and rollback
- Analytics and monitoring

## 🔗 Related Resources

- [Postman Collection](postman-collection.md): Interactive API testing
- [API Reference Documentation](../standard-apis/README.md): Human-readable docs
- [Developer Guide](../developer-documentation.md): Integration best practices
- [Authentication Guide](../standard-apis/access.md): Security implementation

## 🚀 Getting Started

1. **Download the specification** from the link above
2. **Choose your tooling** based on your use case
3. **Generate client code** or documentation as needed
4. **Validate against the spec** during development
5. **Keep your spec updated** as you integrate

The OpenAPI specification is the single source of truth for our API contract and is continuously maintained to reflect the latest features and improvements.