# Subscription APIs

DeDi.global provides powerful real-time subscription capabilities that allow you to monitor changes to registries and records through webhooks. This enables building reactive applications, automated workflows, and real-time synchronization systems.

**Authentication**: 🔒 All Subscription APIs require authentication via API key.

## What is a Subscription?

A subscription in DeDi.global is a mechanism that allows you to receive real-time notifications when specific events occur on registries or records. When you subscribe to a resource, DeDi will automatically send HTTP POST requests (webhooks) to your specified endpoint whenever monitored events happen.

### Subscription Types

**Registry Subscription** - Get notified for all activity within a registry:
- Registry updates (metadata, description changes)
- Registry state changes (suspend, reinstate, revoke)
- Record creation (new records added to the registry)
- Record updates (existing record modifications)  
- Record state changes (suspend, reinstate, revoke of records)

**Record Subscription** - Get notified for specific record activity:
- Record updates (content, metadata changes)
- Record state changes (suspend, reinstate, revoke)

**Registry Tag Subscription** - Monitor all registries with specific tags:
- Get notified across multiple registries that share a common tag
- Useful for monitoring entire categories of data (e.g., all "employee" registries)

## Real-World Use Cases

### 1. Employee Onboarding Automation
**Scenario:** Automate the employee onboarding process when new employee records are created.

```
Registry: "employee-profiles" in company namespace
Subscription: Registry-level subscription
Webhook endpoint: https://hr-system.company.com/webhooks/employee-updates

Workflow:
1. HR creates new employee record in "employee-profiles" registry
2. DeDi sends webhook notification to HR system
3. HR system automatically:
   - Creates email account
   - Assigns office equipment  
   - Generates access cards
   - Schedules orientation meetings
   - Sends welcome email to new employee

Benefits: Eliminates manual steps, ensures consistency, reduces onboarding time
```

### 2. Certificate Expiration Monitoring
**Scenario:** Monitor SSL certificates and automate renewal processes.

```
Registry: "ssl-certificates" in infrastructure namespace  
Subscription: Record-level subscriptions for critical certificates
Webhook endpoint: https://security-ops.company.com/webhooks/cert-updates

Workflow:
1. Certificate record gets updated with new expiration date
2. DeDi sends webhook notification to security system
3. Security system automatically:
   - Checks if certificate expires within 30 days
   - Triggers certificate renewal process
   - Updates load balancers with new certificates
   - Notifies security team of completion
   - Updates monitoring dashboards

Benefits: Prevents certificate outages, automates compliance, reduces manual monitoring
```

## Subscription Management

### Subscribe to Registry or Record

Subscribe to receive webhook notifications for registry or record changes.

**Endpoint:** `POST /dedi/subscribe`

**Request Body:**

```typescript
{
  namespace: string,
  registry_name: string,
  record_name: string,
  webhook_url: string, 
  webhook_secret: string
}
```

**Parameters:**
- `namespace` (optional) - Namespace ID or domain containing the resource (required when not passing registry_tag)
- `registry_name` (optional) - Registry name to monitor (required when not passing registry_tag)
- `record_name` (optional) - Specific record name (omit for registry-level subscription)
- `webhook_url` (required) - Your endpoint URL to receive notifications
- `webhook_secret` (required) - Secret key for webhook authentication. If your webhook has no secret, fill this with a random string
- `registry_tag` (optional) - Subscribe to all registries with this tag (omit for registry-level or record-level subscription)

**Example Request:**
```typescript
// Registry subscription
const response = await fetch('https://api.dedi.global/dedi/subscribe', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'Authorization': 'Bearer your-api-key'
  },
  body: JSON.stringify({
    namespace: 'my-company',
    registry_name: 'employee-profiles',
    webhook_url: 'https://your-app.com/webhook/dedi-updates',
    webhook_secret: 'your-webhook-secret-key'
  })
});

const data = await response.json();
```

**Example Request:**
```typescript
// Record-specific subscription  
const response = await fetch('https://api.dedi.global/dedi/subscribe', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'Authorization': 'Bearer your-api-key'
  },
  body: JSON.stringify({
    namespace: 'my-company',
    registry_name: 'employee-profiles',
    record_name: 'john-doe-profile',
    webhook_url: 'https://your-app.com/webhook/dedi-updates',
    webhook_secret: 'your-webhook-secret-key'
  })
});

const data = await response.json();
```

#### Response Examples

**Registry Subscription Success (201):**
```json
{
  "message": "Successfully subscribed to watch",
  "data": {
    "id": "sub_12345",
    "namespace_id": "my-company",
    "registry_name": "employee-profiles",
    "record_name": null,
    "webhook_url": "https://your-app.com/webhook/dedi-updates",
    "webhook_secret": "your-webhook-secret-key", 
    "is_active": true,
    "type": "registry",
    "email": "user@company.com",
    "created_at": "2024-01-15T10:30:00Z"
  }
}
```

**Record Subscription Success (201):**
```json
{
  "message": "Successfully subscribed to watch",
  "data": {
    "id": "sub_67890", 
    "namespace_id": "my-company",
    "registry_name": "employee-profiles",
    "record_name": "john-doe-profile",
    "webhook_url": "https://your-app.com/webhook/dedi-updates",
    "webhook_secret": "your-webhook-secret-key",
    "is_active": true,
    "type": "record", 
    "email": "user@company.com",
    "created_at": "2024-01-15T10:30:00Z"
  }
}
```

### Unsubscribe from Notifications

Remove an existing subscription to stop receiving notifications.

**Endpoint:** `POST /dedi/unsubscribe`

**Request Body:**
```json
{
  "id": "sub_12345"
}
```

**Parameters:**
- `id` (required) - Subscription ID returned from the subscribe endpoint

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/unsubscribe', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'Authorization': 'Bearer your-api-key'
  },
  body: JSON.stringify({
    id: 'sub_12345'
  })
});

const data = await response.json();
```

#### Response Example

**Success (200):**
```json
{
  "message": "Successfully unsubscribed",
  "data": {
    "id": "sub_12345",
    "namespace_id": "my-company", 
    "registry_name": "employee-profiles",
    "record_name": null,
    "type": "registry"
  }
}
```

### Get Your Subscriptions

Retrieve all your active subscriptions.

**Endpoint:** `GET /dedi/subscriptions`

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/subscriptions', {
  method: 'GET',
  headers: {
    'Authorization': 'Bearer your-api-key'
  }
});

const data = await response.json();
```

#### Response Example

**Success (200):**
```json
{
  "message": "Successfully retrieved subscriptions",
  "data": [
    {
      "id": "sub_12345",
      "namespace_id": "my-company",
      "registry_name": "employee-profiles", 
      "record_name": null,
      "webhook_url": "https://your-app.com/webhook/dedi-updates",
      "is_active": true,
      "type": "registry",
      "email": "user@company.com",
      "created_at": "2024-01-15T10:30:00Z"
    },
    {
      "id": "sub_67890",
      "namespace_id": "my-company", 
      "registry_name": "ssl-certificates",
      "record_name": "prod-api-cert",
      "webhook_url": "https://ops-system.company.com/webhooks/cert-alerts",
      "is_active": true,
      "type": "record",
      "email": "user@company.com", 
      "created_at": "2024-01-20T14:45:00Z"
    }
  ]
}
```

## Webhook Payload Structure

When events occur, DeDi will send HTTP POST requests to your webhook URL with the following structure:

### Registry Event Webhook
```json
{
  "event_type": "registry.updated",
  "timestamp": "2024-01-15T10:30:00Z",
  "namespace_id": "my-company",
  "registry_name": "employee-profiles",
  "registry_id": "reg_123456",
  "event_data": {
    "previous_state": "live",
    "current_state": "live", 
    "changed_fields": ["description"],
    "updated_by": "did:dedi:user:admin123"
  },
  "webhook_signature": "sha256=abc123..."
}
```

### Record Event Webhook  
```json
{
  "event_type": "record.created",
  "timestamp": "2024-01-15T10:30:00Z", 
  "namespace_id": "my-company",
  "registry_name": "employee-profiles",
  "record_name": "jane-smith-profile",
  "record_id": "rec_789012",
  "event_data": {
    "record_state": "live",
    "created_by": "did:dedi:user:hr123",
    "version": "v1.0.0"
  },
  "webhook_signature": "sha256=def456..."
}
```

## Event Types

### Registry Events
- `registry.updated` - Registry metadata or description changed
- `registry.state_changed` - Registry suspended, reinstated, or revoked
- `record.created` - New record added to the registry
- `record.updated` - Existing record modified  
- `record.state_changed` - Record suspended, reinstated, or revoked

### Record Events  
- `record.updated` - Record content or metadata changed
- `record.state_changed` - Record state changed (suspend/reinstate/revoke)

## Webhook Security

### Signature Verification
All webhooks include a `webhook_signature` header for verification:

```javascript
const crypto = require('crypto');

function verifyWebhook(payload, signature, secret) {
  const expectedSignature = 'sha256=' + 
    crypto.createHmac('sha256', secret)
          .update(payload)
          .digest('hex');
  
  return crypto.timingSafeEqual(
    Buffer.from(signature),
    Buffer.from(expectedSignature)
  );
}
```

### Best Practices
1. **Always verify signatures** - Ensure webhooks are from DeDi.global
2. **Use HTTPS endpoints** - Protect webhook data in transit  
3. **Implement idempotency** - Handle duplicate notifications gracefully
4. **Set timeouts** - DeDi will retry failed webhooks with exponential backoff
5. **Log events** - Maintain audit trails for debugging and compliance

## Error Handling

### Webhook Delivery Failures
- DeDi will retry failed webhooks up to 5 times
- Retry intervals: 1min, 5min, 30min, 2hrs, 24hrs
- Subscriptions are automatically disabled after persistent failures
- Check subscription status regularly using the GET endpoint

### Common Error Responses

**Invalid Webhook URL (400):**
```json
{
  "message": "Invalid input: webhook_url is required"
}
```

**Resource Not Found (404):** 
```json
{
  "message": "registry not found"
}
```

**Subscription Not Found (404):**
```json
{
  "message": "No watches found to unsubscribe"
}
```

## Integration Example

### Node.js Express Webhook Handler
```javascript
const express = require('express');
const crypto = require('crypto');

const app = express();
app.use(express.json());

app.post('/webhook/dedi-updates', (req, res) => {
  const signature = req.headers['webhook-signature'];
  const payload = JSON.stringify(req.body);
  
  // Verify webhook signature
  if (!verifyWebhook(payload, signature, process.env.WEBHOOK_SECRET)) {
    return res.status(401).send('Invalid signature');
  }
  
  const { event_type, namespace_id, registry_name, record_name } = req.body;
  
  switch(event_type) {
    case 'record.created':
      handleNewEmployee(req.body);
      break;
    case 'record.updated':
      handleEmployeeUpdate(req.body);
      break;
    case 'registry.state_changed':
      handleRegistryStateChange(req.body);
      break;
  }
  
  res.status(200).send('OK');
});
```

<!-- ### Python Flask Webhook Handler
```python
from flask import Flask, request, jsonify
import hmac
import hashlib

app = Flask(__name__)

@app.route('/webhook/dedi-updates', methods=['POST'])
def handle_webhook():
    signature = request.headers.get('webhook-signature')
    payload = request.get_data()
    
    # Verify signature
    expected_signature = 'sha256=' + hmac.new(
        WEBHOOK_SECRET.encode(),
        payload,
        hashlib.sha256
    ).hexdigest()
    
    if not hmac.compare_digest(signature, expected_signature):
        return jsonify({'error': 'Invalid signature'}), 401
    
    data = request.json
    event_type = data['event_type']
    
    if event_type == 'record.created':
        handle_new_record(data)
    elif event_type == 'record.updated':
        handle_record_update(data)
        
    return jsonify({'status': 'success'})
``` -->

The Subscription API provides a powerful foundation for building reactive, event-driven applications that stay synchronized with your DeDi.global data in real-time.
