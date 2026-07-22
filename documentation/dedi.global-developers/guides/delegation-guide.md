# Delegation Guide

Delegation is a powerful feature that allows you to grant management permissions to other registered DeDi users.

### Prerequisites

Before setting up delegation, ensure that:

* You have a registered DeDi account with administrative access to the registry
* The delegate (person you want to grant access to) has a registered DeDi Global account
* You have the delegate's registered email address

### Current Limitations

* Delegation permissions cannot be partially restricted (it's all or nothing)
* The delegate must have a registered DeDi Global account

### Setting Up Delegation

You can set up delegation through either the UI or the API. Delegation can be set up at both the **namespace** level and the **registry** level.

#### Namespace Delegation

Namespace delegates gain administrative access to the entire namespace, including all registries and records within it.

##### Using the UI

1. Navigate to your namespace dashboard
2. Click on the three dots at the top right corner
3. Click "Delegate"
4. Enter the delegate's email address
5. Confirm the addition

##### Using the API

**Add a namespace delegate:**

```http
POST /dedi/{namespace}/add-namespace-delegate
```

**Request Body**

```json
{
  "email": "delegate@example.com"
}
```

**List namespace delegates:**

```http
GET /dedi/{namespace}/get-delegates-by-namespace
```

**Remove a namespace delegate:**

```http
POST /dedi/{namespace}/remove-namespace-delegate
```

**Request Body**

```json
{
  "email": "delegate@example.com"
}
```

#### Registry Delegation

Registry delegates gain management access to a specific registry within a namespace.

##### Using the UI

1. Navigate to your registry dashboard
2. Click on the three dots at the top right corner
3. Click "Delegate"
4. Enter the delegate's email address
5. Confirm the addition

##### Using the API

**Add a registry delegate:**

```http
POST /dedi/{namespace}/{registry_name}/add-delegate
```

**Request Body**

```json
{
  "email": "delegate@example.com"
}
```

**List registry delegates:**

```http
GET /dedi/{namespace}/{registry_name}/get-delegates-by-registry
```

**Remove a registry delegate:**

```http
POST /dedi/{namespace}/{registry_name}/remove-delegate
```

**Request Body**

```json
{
  "email": "delegate@example.com"
}
```

**Response**

A successful delegation will return a 200 OK response.
