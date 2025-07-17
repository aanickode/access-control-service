<details>
<summary>Relevant source files</summary>

The following files were used as context for generating this wiki page:

- [src/routes.js](https://github.com/aanickode/access-control-service/blob/main/src/routes.js)
- [docs/api.md](https://github.com/aanickode/access-control-service/blob/main/docs/api.md)
</details>

# API Endpoints

## Introduction

The API Endpoints module provides a set of routes and handlers for managing user roles, permissions, and authentication tokens within the access control service. It allows authorized users to view and create roles, view permissions, and generate authentication tokens for users with specific roles.

Sources: [src/routes.js](), [docs/api.md]()

## Route Handlers

### GET /users

This endpoint retrieves a list of all users and their associated roles.

#### Flow

```mermaid
graph TD
    A[Client] -->|GET /users| B[Express Router]
    B --> C{checkPermission<br>'view_users'}
    C -->|Permitted| D[Retrieve users<br>from database]
    D --> E[Send user list<br>as JSON response]
    C -->|Not Permitted| F[Send 403<br>Forbidden response]
```

To access this endpoint, the user must have the `'view_users'` permission.

Sources: [src/routes.js:6-8]()

### POST /roles

This endpoint allows creating a new role with a set of permissions.

#### Request Body

| Field       | Type     | Description                    |
|-------------|----------|--------------------------------|
| `name`      | `string` | The name of the new role       |
| `permissions` | `array`  | An array of permission strings |

#### Flow

```mermaid
graph TD
    A[Client] -->|POST /roles| B[Express Router]
    B --> C{checkPermission<br>'create_role'}
    C -->|Permitted| D{Validate<br>request body}
    D -->|Valid| E[Store role<br>in database]
    E --> F[Send 201 Created<br>with role details]
    D -->|Invalid| G[Send 400 Bad Request<br>with error message]
    C -->|Not Permitted| H[Send 403<br>Forbidden response]
```

To create a new role, the user must have the `'create_role'` permission. The request body must contain a `name` field (string) and a `permissions` field (array of strings).

Sources: [src/routes.js:10-16]()

### GET /permissions

This endpoint retrieves a list of all roles and their associated permissions.

#### Flow

```mermaid
sequenceDiagram
    participant Client
    participant Router
    participant AuthMiddleware
    participant Database
    
    Client->>Router: GET /permissions
    Router->>AuthMiddleware: checkPermission('view_permissions')
    alt Permitted
        AuthMiddleware-->>Router: Next()
        Router->>Database: Retrieve roles
        Database-->>Router: Roles data
        Router-->>Client: 200 OK with roles
    else Not Permitted
        AuthMiddleware-->>Router: 403 Forbidden
        Router-->>Client: 403 Forbidden
    end
```

To access this endpoint, the user must have the `'view_permissions'` permission.

Sources: [src/routes.js:18-20]()

### POST /tokens

This endpoint generates an authentication token for a user with a specific role.

#### Request Body

| Field  | Type     | Description                      |
|--------|----------|----------------------------------|
| `user` | `string` | The email or username of the user|
| `role` | `string` | The role to assign to the user   |

#### Flow

```mermaid
graph TD
    A[Client] -->|POST /tokens| B[Express Router]
    B --> C{Validate<br>request body}
    C -->|Valid| D[Store user-role<br>mapping in database]
    D --> E[Send 201 Created<br>with user and role]
    C -->|Invalid| F[Send 400 Bad Request<br>with error message]
```

The request body must contain a `user` field (string) and a `role` field (string). The user-role mapping is stored in the database, and a 201 Created response is sent with the user and role details.

Sources: [src/routes.js:22-28]()

## Authentication Middleware

The `checkPermission` middleware function is used to enforce role-based access control for certain routes. It checks if the user has the required permission based on their assigned role.

```mermaid
classDiagram
    class AuthMiddleware {
        +checkPermission(requiredPermission: string)
    }
    class Database {
        -users: Map~string, string~
        -roles: Map~string, string[]~
    }
    AuthMiddleware ..> Database : uses
```

The `checkPermission` function retrieves the user's role from the database, looks up the associated permissions for that role, and checks if the required permission is present. If the user has the necessary permission, the request is allowed to proceed; otherwise, a 403 Forbidden response is sent.

Sources: [src/routes.js:6, 12, 19](), [src/authMiddleware.js]()

## Data Storage

The application uses an in-memory data store (`db.js`) to store user-role mappings and role-permission mappings.

```mermaid
erDiagram
    USERS ||--o{ ROLES : has
    ROLES ||--|{ PERMISSIONS : has
```

- `users` is a map of user email/username to their assigned role.
- `roles` is a map of role name to an array of associated permissions.

Sources: [src/routes.js:3](), [src/db.js]()

## Conclusion

The API Endpoints module provides a set of routes and handlers for managing user roles, permissions, and authentication tokens within the access control service. It allows authorized users to view and create roles, view permissions, and generate authentication tokens for users with specific roles. The module also includes an authentication middleware for enforcing role-based access control and an in-memory data store for storing user-role and role-permission mappings.

Sources: [src/routes.js](), [docs/api.md]()