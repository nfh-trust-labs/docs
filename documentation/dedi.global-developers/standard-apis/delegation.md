# Delegation Management APIs

Delegation is a fundamental collaboration feature that enables organizations to share control and management responsibilities across teams and departments. By distributing access permissions, organizations can maintain security while enabling efficient, scalable data management workflows.

**Authentication**: 🔒 All Delegation Management APIs require authentication via API key.

## Why Delegation Matters in DeDi.global

### Organizational Collaboration

Modern organizations rarely operate with a single person controlling all data infrastructure. Different departments, teams, and roles need varying levels of access to different data sets. DeDi.global's delegation system mirrors real-world organizational structures, enabling distributed yet controlled access management.

### Real-World Scenarios

Consider a technology company "InnovateTech" that creates a namespace `innovatetech-corp` for their organization:

#### **Namespace-Level Delegation**
The company's IT administrators and senior management need broad control across the entire namespace:
- **CEO**: Full namespace access for strategic oversight
- **CTO**: Technical control across all registries  
- **Data Protection Officer**: Compliance monitoring across departments
- **IT Administrator**: Infrastructure management and user access

#### **Registry-Level Delegation**
Different departments manage their own specific data registries:
- **HR Department**: Controls `employee-profiles` and `benefits-registry`
- **Engineering Team**: Manages `product-specifications` and `development-milestones`
- **Sales Team**: Oversees `customer-contacts` and `sales-pipeline`
- **Marketing Team**: Handles `campaign-data` and `lead-generation`

### Benefits of Delegation

1. **🎯 Principle of Least Privilege**: Users get exactly the access they need, nothing more
2. **⚡ Operational Efficiency**: Teams can work independently without bottlenecks
3. **🛡️ Enhanced Security**: Distributed control reduces single points of failure
4. **📈 Scalability**: Organizations can grow without centralized management overhead
5. **🔄 Flexible Workflows**: Easy to adapt permissions as teams and projects evolve

### Delegation Hierarchy

DeDi.global implements a two-tier delegation model:

```
Namespace Level (Broad Control)
├── Create/manage any registry within the namespace
├── Create/manage records within any registry
├── Add/remove namespace delegates  
├── View all registries and records
└── Control namespace-wide settings

Registry Level (Specific Control)  
├── Create/edit/delete records within the registry
├── Add/remove registry delegates
├── Modify registry settings and metadata
└── Export registry data
```

## Namespace Delegation APIs

Namespace delegation grants administrative access to an entire namespace, allowing delegates to manage all registries within it.

### Add Namespace Delegate

Grant a user administrative access to an entire namespace and all its registries.

**Endpoint:** `POST /dedi/{namespace}/add-namespace-delegate`

**Parameters:**
- `namespace` (path, required): Namespace ID or verified domain

**Request Body:**
```typescript
{
  email: string; // Email address of the user to add as delegate
}
```

**Prerequisites:**
- Requesting user must be the namespace owner or an existing namespace delegate
- Target user must have a registered DeDi.global account
- Target user cannot already be a namespace delegate

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/innovatetech-corp/add-namespace-delegate', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    email: 'cto@innovatetech.com'
  })
});

const data = await response.json();
```

**Success Response (200):**
```typescript
{
  "message": "Delegate added successfully"
}
```

**Error Responses:**
- `400` - Missing email, user not found, or user already a delegate
- `403` - Insufficient privileges to add delegates
- `404` - Namespace not found
- `500` - Internal server error

**Delegate Permissions:**
Once added, namespace delegates can:
- Create, update, and manage any registry within the namespace
- Add or remove other namespace delegates
- View all records across all registries
- Export data from any registry
- Manage namespace settings and metadata

### Remove Namespace Delegate

Remove a user's administrative access from a namespace.

**Endpoint:** `POST /dedi/{namespace}/remove-namespace-delegate`

**Parameters:**
- `namespace` (path, required): Namespace ID or verified domain

**Request Body:**
```typescript
{
  email: string; // Email address of the delegate to remove
}
```

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/innovatetech-corp/remove-namespace-delegate', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    email: 'former-cto@innovatetech.com'
  })
});

const data = await response.json();
```

**Success Response (200):**
```typescript
{
  "message": "Delegate removed successfully",
  "current_delegates": [
    "did:web:did.cord.network:3xOwner...",
    "did:web:did.cord.network:3xRemaining..."
  ]
}
```

**Error Responses:**
- `400` - Missing email or user not found
- `403` - Insufficient privileges to remove delegates
- `404` - Namespace not found or user not a delegate
- `500` - Internal server error

**Important Notes:**
- Namespace owners cannot remove themselves
- Removing a delegate immediately revokes all their namespace-level permissions
- Registry-level delegations remain unaffected

### Get Namespace Delegates

Retrieve a list of all users who have administrative access to a namespace.

**Endpoint:** `GET /dedi/{namespace}/get-delegates-by-namespace`

**Parameters:**
- `namespace` (path, required): Namespace ID or verified domain

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/innovatetech-corp/get-delegates-by-namespace', {
  method: 'GET',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  }
});

const data = await response.json();
```

**Success Response (200):**
```typescript
{
  "message": "Delegates fetched successfully",
  "owner": "founder@innovatetech.com",
  "delegates": [
    "cto@innovatetech.com",
    "dpo@innovatetech.com"
  ]
}
```

**Error Responses:**
- `400` - Namespace ID required
- `403` - Insufficient privileges to view delegates
- `404` - Namespace not found
- `500` - Internal server error

**Use Cases:**
- Audit namespace access permissions
- Display administrative team members
- Verify delegate assignments for compliance
- Monitor access control changes

## Registry Delegation APIs

Registry delegation grants specific access to individual registries, allowing focused collaboration without broad namespace control.

### Add Registry Delegate

Grant a user management access to a specific registry within a namespace.

**Endpoint:** `POST /dedi/{namespace}/{registry_name}/add-delegate`

**Parameters:**
- `namespace` (path, required): Namespace ID or verified domain
- `registry_name` (path, required): Registry name to grant access to

**Request Body:**
```typescript
{
  email: string; // Email address of the user to add as delegate
}
```

**Prerequisites:**
- Requesting user must be a namespace delegate or registry owner
- Target user must have a registered DeDi.global account
- Registry must exist and be accessible

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/innovatetech-corp/employee-profiles/add-delegate', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    email: 'hr-manager@innovatetech.com'
  })
});

const data = await response.json();
```

**Success Response (200):**
```typescript
{
  "message": "Delegate added successfully"
}
```

**Error Responses:**
- `400` - Missing email, user not found, or user already a delegate
- `403` - Insufficient privileges to add delegates
- `404` - Namespace or registry not found
- `500` - Internal server error

**Delegate Permissions:**
Registry delegates can:
- Create, edit, and delete records within the specific registry
- Add or remove other registry delegates (for the same registry)
- Modify registry metadata and settings

### Remove Registry Delegate

Remove a user's access from a specific registry.

**Endpoint:** `POST /dedi/{namespace}/{registry_name}/remove-registry-delegate`

**Parameters:**
- `namespace` (path, required): Namespace ID or verified domain  
- `registry_name` (path, required): Registry name to remove access from

**Request Body:**
```typescript
{
  email: string; // Email address of the delegate to remove
}
```

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/innovatetech-corp/employee-profiles/remove-registry-delegate', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    email: 'former-hr@innovatetech.com'
  })
});

const data = await response.json();
```

**Success Response (200):**
```typescript
{
  "message": "Delegate removed successfully",
  "current_delegates": [
    "did:web:did.cord.network:3xOwner...",
    "did:web:did.cord.network:3xRemaining..."
  ]
}
```

**Error Responses:**
- `400` - Missing email or user not found
- `403` - Insufficient privileges to remove delegates
- `404` - Namespace, registry not found, or user not a delegate
- `500` - Internal server error

**Important Notes:**
- Removing a registry delegate only affects access to that specific registry
- User may still have namespace-level access if they are a namespace delegate
- Registry owners cannot remove themselves

### Get Registry Delegates

Retrieve a list of all users who have access to a specific registry.

**Endpoint:** `GET /dedi/{namespace}/{registry_name}/get-delegates-by-registry`

**Parameters:**
- `namespace` (path, required): Namespace ID or verified domain
- `registry_name` (path, required): Registry name to check delegates for

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/innovatetech-corp/employee-profiles/get-delegates-by-registry', {
  method: 'GET',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  }
});

const data = await response.json();
```

**Success Response (200):**
```typescript
{
  "message": "Delegates fetched successfully",
  "owner": "founder@innovatetech.com",
  "delegates": [
    "hr-manager@innovatetech.com",
    "hr-assistant@innovatetech.com"
  ]
}
```

**Error Responses:**
- `400` - Missing required parameters
- `403` - Insufficient privileges to view delegates
- `404` - Namespace or registry not found
- `500` - Internal server error

**Response Details:**
- **`owner`**: Email address of the registry owner
- **`delegates`**: Array of delegate email addresses with registry access

**Use Cases:**
- Audit registry access permissions
- Display current team members with registry access
- Verify delegate assignments for specific registries
- Monitor access control for sensitive data registries

## Best Practices

### Delegation Strategy

1. **Start with Registry-Level**: Begin with specific registry access and escalate to namespace level only when needed
2. **Regular Audits**: Periodically review delegate lists to ensure appropriate access
3. **Documentation**: Maintain records of why each delegation was granted and when it should be reviewed
4. **Principle of Least Privilege**: Grant minimal necessary permissions for each role

### Security Considerations

1. **Onboarding/Offboarding**: Promptly add new users and remove those which are no longer needed
2. **Role Changes**: Update delegations when users change roles or departments
3. **Temporary Access**: Consider time-limited approaches for contractors or temporary projects
4. **Monitor Activity**: Track delegate actions for compliance and security monitoring

### Organizational Workflows

#### **Department-Based Structure:**
```
InnovateTech Namespace
├── HR Department (Registry Delegates)
│   ├── employee-profiles
│   └── benefits-data
├── Engineering (Registry Delegates)
│   ├── product-specs
│   └── development-milestones
└── IT Administrators (Namespace Delegates)
    └── Full access to all registries
```

#### **Project-Based Structure:**
```
ProjectAlpha Namespace  
├── Core Team (Namespace Delegates)
│   └── Full project access
├── External Consultants (Registry Delegates)
│   ├── technical-docs (read-only)
│   └── deliverables (write access)
└── Stakeholders (Registry Delegates)
    └── reports (read-only)
```

### Common Use Cases

1. **Multi-Department Organizations**: Each department manages their own registries while IT has namespace control
2. **Client Projects**: Project teams get registry access while account managers have namespace oversight
3. **Vendor Collaboration**: External partners get limited registry access for specific deliverables
4. **Compliance Teams**: Auditors and compliance officers get read-only namespace access for monitoring
5. **Development Workflows**: Developers get registry access while DevOps has namespace control for infrastructure

Delegation in DeDi.global enables organizations to maintain security while fostering collaboration, ensuring that the right people have the right access to the right data at the right time.
