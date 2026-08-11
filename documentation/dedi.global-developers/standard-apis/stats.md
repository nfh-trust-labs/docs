# Stats API

The Stats API provides aggregate platform statistics for DeDi.global.

🌐 **Public Access**: No authentication required.

### Get Platform Statistics

Retrieve current counts of namespaces, registries, records, and users across the platform.

**Endpoint:** `GET /dedi/stats`

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/stats', {
  method: 'GET'
});

const data = await response.json();
```

**Success Response (200):**
```json
{
  "namespace_count": 150,
  "registry_count": 420,
  "record_count": 12500,
  "user_count": 300
}
```

**Use Cases:**
- Display platform activity metrics on dashboards
- Monitor platform growth over time
- Public transparency reporting
