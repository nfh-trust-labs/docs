# Delegation Guide

Delegation is a powerful feature that allows you to grant management permissions to other registered DeDi users.

### Prerequisites

Before setting up delegation, ensure that:

* You have a registered DeDi account with administrative access to the registry
* The delegate (person you want to grant access to) has a registered DeDi Global account
* You have the delegate's registered email address

### Current Limitations

* Delegation is currently supported only at the **registry** level
* Delegation permissions cannot be partially restricted (it's all or nothing)

### Setting Up Delegation

You can set up delegation through either the UI or the API.

#### Using the UI

1. Navigate to your registry dashboard
2. Click on the three dots at the top right corner.
3. Click "Delegate"
4. Enter the delegate's email address
5. Confirm the addition

#### Using the API

To add a delegate via API, use the following endpoint:

```http
POST /dedi/{{namespace}}/{{registry_name}}/add-delegate
```

**Request Body**

```json
{
  "email": "delegate@example.com"
}
```

**Response**

A successful delegation will return a 200 OK response.
