<details>
<summary>Relevant source files</summary>

The following files were used as context for generating this wiki page:

- [src/routes.js](https://github.com/aanickode/access-control-service/blob/main/src/routes.js)
- [docs/api.md](https://github.com/aanickode/access-control-service/blob/main/docs/api.md)
- [src/authMiddleware.js](https://github.com/aanickode/access-control-service/blob/main/src/authMiddleware.js)
- [src/db.js](https://github.com/aanickode/access-control-service/blob/main/src/db.js)
- [src/app.js](https://github.com/aanickode/access-control-service/blob/main/src/app.js)

</details>

# API Documentation

## Introduction

This API documentation covers the access control service, which provides a set of endpoints for managing users, roles, and permissions within the application. The service allows authorized users to view a list of registered users, create new roles with associated permissions, view the available permissions, and generate authentication tokens for users with specific roles.

The access control service is a crucial component of the overall project, responsible for enforcing access control policies and ensuring that only authorized users can perform certain actions based on their assigned roles and permissions.

Sources: [src/routes.js](), [docs/api.md]()

## API Endpoints

### GET /users

This endpoint retrieves a list of all registered users and their associated roles.

#### Requirements

- The requesting user must have the `view_users` permission.

#### Response

- Status Code: 200 OK
- Response Body: An array of user objects, each containing the `email` and `role` properties.

```json
[
  { "email": "user1@example.com", "role": "admin" },
  { "email": "user2@example.com", "role": "editor" },
  ...
]
```

#### Flow Diagram

```mermaid
graph TD
    Client --> API[/users]
    API --> AuthMiddleware{Check Permission}
    AuthMiddleware -->|view_users| GetUsers[Get Users from DB]
    GetUsers --> API
    API --> Client
```

Sources: [src/routes.js:5-8](), [src/authMiddleware.js]()

### POST /roles

This endpoint allows authorized users to create a new role with a set of associated permissions.

#### Requirements

- The requesting user must have the `create_role` permission.
- The request body must contain a `name` property (string) and a `permissions` property (array of strings).

#### Request Body

```json
{
  "name": "editor",
  "permissions": ["edit_content", "publish_content"]
}
```

#### Response

- Status Code: 201 Created
- Response Body: An object containing the `role` (name) and `permissions` array.

```json
{
  "role": "editor",
  "permissions": ["edit_content", "publish_content"]
}
```

#### Error Responses

- Status Code: 400 Bad Request
  - If the request body is missing the `name` or `permissions` property, or if `permissions` is not an array.

#### Flow Diagram

```mermaid
graph TD
    Client --> API[/roles]
    API --> AuthMiddleware{Check Permission}
    AuthMiddleware -->|create_role| CreateRole[Create Role in DB]
    CreateRole --> API
    API --> Client
```

Sources: [src/routes.js:10-17](), [src/authMiddleware.js]()

### GET /permissions

This endpoint retrieves a list of all available roles and their associated permissions.

#### Requirements

- The requesting user must have the `view_permissions` permission.

#### Response

- Status Code: 200 OK
- Response Body: An object where the keys are role names, and the values are arrays of permissions.

```json
{
  "admin": ["view_users", "create_role", "view_permissions"],
  "editor": ["edit_content", "publish_content"],
  ...
}
```

#### Flow Diagram

```mermaid
graph TD
    Client --> API[/permissions]
    API --> AuthMiddleware{Check Permission}
    AuthMiddleware -->|view_permissions| GetPermissions[Get Roles from DB]
    GetPermissions --> API
    API --> Client
```

Sources: [src/routes.js:19-21](), [src/authMiddleware.js]()

### POST /tokens

This endpoint generates an authentication token for a user with a specific role.

#### Requirements

- The request body must contain a `user` property (string) and a `role` property (string).

#### Request Body

```json
{
  "user": "user1@example.com",
  "role": "editor"
}
```

#### Response

- Status Code: 201 Created
- Response Body: An object containing the `user` and `role` properties.

```json
{
  "user": "user1@example.com",
  "role": "editor"
}
```

#### Error Responses

- Status Code: 400 Bad Request
  - If the request body is missing the `user` or `role` property.

#### Flow Diagram

```mermaid
graph TD
    Client --> API[/tokens]
    API --> ValidateInput{Validate Input}
    ValidateInput -->|Valid| GenerateToken[Generate Token]
    GenerateToken --> API
    API --> Client
    ValidateInput -->|Invalid| API
    API -->|Error 400| Client
```

Sources: [src/routes.js:23-30]()

## Data Models

### Users

The `users` object in the database stores user email addresses as keys and their associated roles as values.

```javascript
{
  "user1@example.com": "admin",
  "user2@example.com": "editor",
  ...
}
```

Sources: [src/db.js](), [src/routes.js:7,28]()

### Roles

The `roles` object in the database stores role names as keys and their associated permissions as values (arrays of strings).

```javascript
{
  "admin": ["view_users", "create_role", "view_permissions"],
  "editor": ["edit_content", "publish_content"],
  ...
}
```

Sources: [src/db.js](), [src/routes.js:14,20]()

## Authentication and Authorization

The access control service uses a middleware function `checkPermission` to enforce authorization based on the user's role and the required permissions for each endpoint.

```javascript
import db from './db.js';

export const checkPermission = (requiredPermission) => {
  return (req, res, next) => {
    const userRole = db.users[req.user];
    const permissions = db.roles[userRole] || [];
    if (permissions.includes(requiredPermission)) {
      next();
    } else {
      res.status(403).json({ error: 'Forbidden' });
    }
  };
};
```

The `checkPermission` middleware function:

1. Retrieves the user's role from the `users` object in the database.
2. Retrieves the permissions associated with the user's role from the `roles` object.
3. Checks if the required permission is included in the user's permissions.
4. If the user has the required permission, the request is allowed to proceed to the next middleware or route handler.
5. If the user does not have the required permission, a 403 Forbidden response is sent.

Sources: [src/authMiddleware.js](), [src/routes.js:5,10,19]()

## Sequence Diagram

The following sequence diagram illustrates the flow of a request to the `/users` endpoint, including the authorization check and data retrieval from the database.

```mermaid
sequenceDiagram
    participant Client
    participant API
    participant AuthMiddleware
    participant Database

    Client->>API: GET /users
    API->>AuthMiddleware: checkPermission('view_users')
    AuthMiddleware->>Database: Get user role
    Database-->>AuthMiddleware: User role
    AuthMiddleware->>Database: Get role permissions
    Database-->>AuthMiddleware: Role permissions
    Note right of AuthMiddleware: Check if 'view_users' is in permissions
    alt Has permission
        AuthMiddleware-->>API: Next()
        API->>Database: Get users
        Database-->>API: Users data
        API-->>Client: 200 OK, Users data
    else No permission
        AuthMiddleware-->>API: 403 Forbidden
        API-->>Client: 403 Forbidden
    end
```

Sources: [src/routes.js:5-8](), [src/authMiddleware.js]()

## Conclusion

The access control service provides a set of endpoints for managing users, roles, and permissions within the application. It enforces access control policies by verifying the user's permissions before allowing certain actions. The service utilizes an in-memory database to store user and role information, and a middleware function to handle authorization checks. The API documentation covers the available endpoints, their requirements, request/response formats, and the underlying data models and authentication/authorization mechanisms.

Sources: [src/routes.js](), [src/authMiddleware.js](), [src/db.js](), [docs/api.md]()