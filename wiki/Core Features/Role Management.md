<details>
<summary>Relevant source files</summary>

The following files were used as context for generating this wiki page:

- [config/roles.json](https://github.com/aanickode/access-control-service/blob/main/config/roles.json)
- [src/models.js](https://github.com/aanickode/access-control-service/blob/main/src/models.js)
- [src/routes.js](https://github.com/aanickode/access-control-service/blob/main/src/routes.js)

</details>

# Role Management

## Introduction

The Role Management system is a crucial component of the access control service, responsible for managing user roles and their associated permissions. It defines the roles available within the application and the specific actions or resources each role is authorized to access. This system ensures proper access control and segregation of duties, allowing for secure and controlled access to various features and functionalities.

Sources: [src/routes.js](), [config/roles.json]()

## Role Definition

Roles are defined in a JSON configuration file (`config/roles.json`). Each role is represented as a key-value pair, where the key is the role name, and the value is an array of permission strings.

```json
{
  "admin": ["view_users", "create_role", "view_permissions"],
  "engineer": ["view_users", "view_permissions"],
  "analyst": ["view_users"]
}
```

This configuration defines three roles: `admin`, `engineer`, and `analyst`. The `admin` role has permissions to view users, create roles, and view permissions. The `engineer` role can view users and permissions, while the `analyst` role can only view users.

Sources: [config/roles.json]()

## Data Models

The application uses two main data models: `User` and `Role`.

### User Model

```javascript
export const User = {
  email: 'string',
  role: 'string'
};
```

The `User` model represents a user in the system, with properties for `email` and `role`. The `role` property corresponds to one of the defined roles in the `config/roles.json` file.

Sources: [src/models.js:1-4]()

### Role Model

```javascript
export const Role = {
  name: 'string',
  permissions: ['string']
};
```

The `Role` model defines the structure of a role, consisting of a `name` (string) and an array of `permissions` (strings).

Sources: [src/models.js:6-9]()

## Role Management API

The application exposes several API endpoints for managing roles and permissions.

### Get Users

```mermaid
graph TD
    A[Client] -->|GET /users| B[checkPermission('view_users')]
    B -->|Authorized| C[Get Users from DB]
    C -->|User Data| D[Response with User List]
    D -->|JSON| A
```

The `/users` endpoint retrieves a list of all users in the system, including their email addresses and assigned roles. This endpoint requires the `view_users` permission.

Sources: [src/routes.js:8-11]()

### Create Role

```mermaid
graph TD
    A[Client] -->|POST /roles| B[checkPermission('create_role')]
    B -->|Authorized| C[Validate Request Body]
    C -->|Valid| D[Create Role in DB]
    D -->|Role Created| E[Response with Role Details]
    E -->|JSON| A
    C -->|Invalid| F[Bad Request Response]
    F -->|JSON| A
```

The `/roles` endpoint allows creating a new role by providing a `name` and an array of `permissions`. This endpoint requires the `create_role` permission. If the request body is invalid, a 400 Bad Request response is returned.

Sources: [src/routes.js:13-21]()

### Get Permissions

```mermaid
graph TD
    A[Client] -->|GET /permissions| B[checkPermission('view_permissions')]
    B -->|Authorized| C[Get Roles from DB]
    C -->|Role Data| D[Response with Roles and Permissions]
    D -->|JSON| A
```

The `/permissions` endpoint retrieves a list of all roles and their associated permissions. This endpoint requires the `view_permissions` permission.

Sources: [src/routes.js:23-26]()

### Create Token

```mermaid
sequenceDiagram
    participant Client
    participant Server
    participant Database

    Client->>Server: POST /tokens
    Note right of Server: No permission check required
    Server->Server: Validate request body
    opt Valid request
        Server->>Database: Store user and role
        Database-->>Server: User and role stored
        Server-->>Client: 201 Created (user, role)
    else Invalid request
        Server-->>Client: 400 Bad Request
    end
```

The `/tokens` endpoint is used to create a new user and assign them a role. It requires a `user` (email) and a `role` in the request body. This endpoint does not require any specific permission. If the request body is invalid, a 400 Bad Request response is returned.

Sources: [src/routes.js:28-37]()

## Permission Middleware

The application uses a middleware function `checkPermission` to enforce role-based access control on certain routes.

```javascript
import db from './db.js';

export const checkPermission = (permission) => {
  return (req, res, next) => {
    const userRole = db.users[req.user];
    const rolePermissions = db.roles[userRole] || [];
    if (rolePermissions.includes(permission)) {
      next();
    } else {
      res.status(403).json({ error: 'Forbidden' });
    }
  };
};
```

The `checkPermission` function takes a `permission` string as an argument and returns a middleware function. This middleware function checks if the authenticated user's role has the required permission. If the user has the permission, the request is allowed to proceed; otherwise, a 403 Forbidden response is returned.

Sources: [src/routes.js:3](), [src/authMiddleware.js]()

## Role and Permission Management

| Feature | Description |
| --- | --- |
| Role Definition | Roles and their associated permissions are defined in a JSON configuration file (`config/roles.json`). |
| User Management | Users are assigned roles, which determine their access permissions. |
| Role Creation | The `/roles` endpoint allows creating new roles with specified permissions, requiring the `create_role` permission. |
| User Listing | The `/users` endpoint retrieves a list of all users and their assigned roles, requiring the `view_users` permission. |
| Permission Listing | The `/permissions` endpoint retrieves a list of all roles and their associated permissions, requiring the `view_permissions` permission. |
| Token Creation | The `/tokens` endpoint creates a new user and assigns them a role, without requiring any specific permission. |
| Role-Based Access Control | The `checkPermission` middleware enforces role-based access control on routes that require specific permissions. |

Sources: [src/routes.js](), [config/roles.json]()

## Conclusion

The Role Management system is a critical component of the access control service, enabling secure and controlled access to various features and functionalities based on user roles and permissions. It provides a flexible and extensible approach to managing access control, allowing for easy addition or modification of roles and permissions as needed. By leveraging this system, the application can ensure proper segregation of duties and maintain a robust security posture.