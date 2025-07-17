<details>
<summary>Relevant source files</summary>

The following files were used as context for generating this wiki page:

- [docs/api.md](https://github.com/aanickode/access-control-service/blob/main/docs/api.md)
- [src/routes.js](https://github.com/aanickode/access-control-service/blob/main/src/routes.js)
</details>

# API Documentation

## Introduction

The API Documentation covers the implementation details and functionality of the API endpoints provided by the access control service. This service manages user roles, permissions, and authentication tokens within the application. It allows authorized users to view and manage user roles, permissions, and generate authentication tokens.

## API Endpoints

### GET /users

This endpoint retrieves a list of all registered users and their associated roles.

#### Flow

```mermaid
flowchart TD
    Start([Start]) --> CheckPermission{Check "view_users" Permission}
    CheckPermission -->|Permitted| GetUsers[Get Users from Database]
    GetUsers --> FormatResponse[Format Response]
    FormatResponse --> SendResponse([Send Response])
    CheckPermission -->|Not Permitted| SendError([Send Error Response])
    SendError --> Stop([Stop])
    SendResponse --> Stop
```

1. The request is intercepted by the `checkPermission` middleware, which verifies if the user has the `'view_users'` permission.
2. If permitted, the user data is retrieved from the database (`db.users`).
3. The user data is formatted into an array of objects containing the user's email and role.
4. The formatted response is sent back to the client.
5. If the user is not permitted, an error response is sent.

Sources: [src/routes.js:5-8]()

### POST /roles

This endpoint allows authorized users to create a new role with a set of permissions.

#### Request Body

| Field       | Type   | Description                   |
|-------------|--------|-------------------------------|
| `name`      | string | The name of the new role      |
| `permissions` | array  | An array of permission strings |

#### Flow

```mermaid
flowchart TD
    Start([Start]) --> CheckPermission{Check "create_role" Permission}
    CheckPermission -->|Permitted| ValidateRequest[Validate Request Body]
    ValidateRequest -->|Valid| CreateRole[Create Role in Database]
    CreateRole --> SendResponse([Send Response])
    ValidateRequest -->|Invalid| SendError([Send Error Response])
    CheckPermission -->|Not Permitted| SendError
    SendError --> Stop([Stop])
    SendResponse --> Stop
```

1. The request is intercepted by the `checkPermission` middleware, which verifies if the user has the `'create_role'` permission.
2. If permitted, the request body is validated to ensure the `name` and `permissions` fields are present and correctly formatted.
3. If the request body is valid, the new role is created in the database (`db.roles`).
4. A response containing the new role's name and permissions is sent back to the client.
5. If the user is not permitted or the request body is invalid, an error response is sent.

Sources: [src/routes.js:10-16]()

### GET /permissions

This endpoint retrieves a list of all defined roles and their associated permissions.

#### Flow

```mermaid
flowchart TD
    Start([Start]) --> CheckPermission{Check "view_permissions" Permission}
    CheckPermission -->|Permitted| GetPermissions[Get Roles from Database]
    GetPermissions --> SendResponse([Send Response])
    CheckPermission -->|Not Permitted| SendError([Send Error Response])
    SendError --> Stop([Stop])
    SendResponse --> Stop
```

1. The request is intercepted by the `checkPermission` middleware, which verifies if the user has the `'view_permissions'` permission.
2. If permitted, the roles and their associated permissions are retrieved from the database (`db.roles`).
3. The roles and permissions are sent back to the client as the response.
4. If the user is not permitted, an error response is sent.

Sources: [src/routes.js:18-21]()

### POST /tokens

This endpoint allows users to generate an authentication token by providing their user identifier and role.

#### Request Body

| Field  | Type   | Description                    |
|--------|--------|--------------------------------|
| `user` | string | The user identifier (e.g., email) |
| `role` | string | The role associated with the user |

#### Flow

```mermaid
sequenceDiagram
    participant Client
    participant API
    participant Database

    Client->>API: POST /tokens
    activate API
    API->>API: Validate request body
    opt Valid request body
        API->>Database: Store user and role
        Database-->>API: Success
        API-->>Client: 201 Created
    else Invalid request body
        API-->>Client: 400 Bad Request
    end
    deactivate API
```

1. The client sends a POST request to the `/tokens` endpoint with the user identifier and role in the request body.
2. The API validates the request body to ensure the `user` and `role` fields are present.
3. If the request body is valid, the user identifier and role are stored in the database (`db.users`).
4. A success response with a status code of 201 (Created) is sent back to the client.
5. If the request body is invalid, an error response with a status code of 400 (Bad Request) is sent.

Sources: [src/routes.js:23-30]()

## Authentication Middleware

The `checkPermission` middleware is used to verify if a user has a specific permission before allowing access to certain routes or endpoints.

```javascript
import { checkPermission } from './authMiddleware.js';
```

This middleware likely checks the user's role and associated permissions against the required permission for the requested route or endpoint. If the user has the required permission, the request is allowed to proceed. Otherwise, an error response is sent.

Sources: [src/routes.js:3]()

## Data Storage

The application uses an in-memory data store (`db.js`) to store user roles, permissions, and authentication tokens.

```javascript
import db from './db.js';
```

The `db` object likely has the following structure:

```javascript
const db = {
  users: {
    // user identifier (e.g., email) as key, role as value
    'user1@example.com': 'admin',
    'user2@example.com': 'viewer',
    // ...
  },
  roles: {
    // role name as key, array of permissions as value
    'admin': ['view_users', 'create_role', 'view_permissions'],
    'viewer': ['view_users', 'view_permissions'],
    // ...
  }
};
```

Sources: [src/routes.js:4]()

## Conclusion

The API Documentation covers the implementation details and functionality of the API endpoints provided by the access control service. It includes endpoints for managing user roles, permissions, and generating authentication tokens. The service uses an in-memory data store to store user roles, permissions, and authentication tokens. The `checkPermission` middleware is used to verify if a user has the required permission before allowing access to certain routes or endpoints.