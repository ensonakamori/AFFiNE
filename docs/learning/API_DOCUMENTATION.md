# API Documentation

> **Purpose**: Complete API reference for AFFiNE's GraphQL and REST endpoints.

---

## GraphQL API

**Endpoint**: `POST /graphql`

### Queries

#### Get Workspaces

```graphql
query {
  workspaces {
    id
    name
    avatar
    createdAt
    role
    permissions {
      workspace_read
      workspace_write
      workspace_delete
    }
  }
}
```

#### Get Workspace

```graphql
query {
  workspace(id: "workspace-123") {
    id
    name
    members {
      id
      user {
        id
        name
        email
      }
      role
    }
    docs {
      id
      title
      createdAt
    }
  }
}
```

### Mutations

#### Create Workspace

```graphql
mutation {
  createWorkspace(name: "My Workspace") {
    id
    name
    createdAt
  }
}
```

#### Update Workspace

```graphql
mutation {
  updateWorkspace(input: {
    id: "workspace-123"
    name: "New Name"
    avatar: "avatar.png"
  }) {
    id
    name
    avatar
  }
}
```

#### Delete Workspace

```graphql
mutation {
  deleteWorkspace(id: "workspace-123")
}
```

---

## REST API

### Blob Operations

#### Upload Blob

```
POST /api/workspaces/:id/blobs/:key
Content-Type: multipart/form-data

Body: Binary blob data
```

**Response**:
```json
{ "success": true }
```

#### Get Blob

```
GET /api/workspaces/:id/blobs/:key
```

**Response**: Binary data

#### Delete Blob

```
DELETE /api/workspaces/:id/blobs/:key
```

---

## Authentication

### Login

```graphql
mutation {
  signIn(email: "user@example.com", password: "password") {
    id
    name
    email
  }
}
```

### Logout

```graphql
mutation {
  signOut
}
```

---

## Error Responses

```json
{
  "errors": [
    {
      "message": "Access denied",
      "extensions": {
        "code": "SPACE_ACCESS_DENIED",
        "workspaceId": "workspace-123"
      }
    }
  ]
}
```

**Common Error Codes**:
- `AUTHENTICATION_REQUIRED` - Not logged in
- `SPACE_ACCESS_DENIED` - No permission
- `WORKSPACE_NOT_FOUND` - Workspace doesn't exist
- `DOC_NOT_FOUND` - Document doesn't exist

---

*Complete API reference for AFFiNE.*
