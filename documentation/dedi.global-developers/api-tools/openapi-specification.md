# OpenAPI Specification

The DeDi.global OpenAPI specification provides a comprehensive, machine-readable description of our entire API surface. This specification follows OpenAPI 3.0 standards and enables automated tooling, code generation, and integration workflows.

<a href="../../../openAPI.yaml" class="button primary">Download OpenAPI specification</a>

### Usage Scenarios

#### Code Generation

Generate client SDKs in multiple programming languages:

```bash
# Generate Python client
openapi-generator-cli generate -i openAPI.yaml -g python -o ./dedi-python-client

# Generate JavaScript client  
openapi-generator-cli generate -i openAPI.yaml -g javascript -o ./dedi-js-client

# Generate Java client
openapi-generator-cli generate -i openAPI.yaml -g java -o ./dedi-java-client
```

#### API Documentation

Generate interactive documentation:

```bash
# Swagger UI
swagger-ui-serve openAPI.yaml

# Redoc
redoc-cli serve openAPI.yaml
```

#### API Testing

Test with tools like Postman, Insomnia, Newman, or Dredd.

### OpenAPI 3.0 Specification

The spec covers all endpoints, reusable schemas, authentication (Bearer token & cookie), response examples, and error codes. Core sections include security definitions, path operations, and component schemas.

### Integration Tools

* **Code Generation:** OpenAPI Generator, Swagger Codegen, AutoRest
* **Documentation:** Swagger UI, Redoc, Stoplight
* **Testing & Validation:** Spectral, Prism, Dredd

### Best Practices

* Pin generator versions and review generated code
* Customize documentation with interactive examples and error scenarios
* Maintain backwards compatibility, use semantic versioning, and document changes

### Specification Contents

* **Authentication:** Login/logout, API key management, token refresh
* **Core Resources:** Namespace, Registry, and Record CRUD
* **Advanced:** Domain verification, bulk import/export, webhooks, delegation
* **Administrative:** State management, version history, analytics

### Getting Started

1. Download the specification
2. Choose your tooling
3. Generate client code or documentation
4. Validate against the spec during development

### Related Resources

* [Postman Collection](postman-collection.md): Interactive API testing
* [API Reference Documentation](../standard-apis/): Human-readable docs
* [Developer Guide](../developer-documentation.md): Integration best practices
* [Authentication Guide](../standard-apis/access.md): Security implementation

