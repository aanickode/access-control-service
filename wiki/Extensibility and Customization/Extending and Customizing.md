<details>
<summary>Relevant source files</summary>

The following files were used as context for generating this wiki page:

- [src/authMiddleware.js](https://github.com/aanickode/access-control-service/blob/main/src/authMiddleware.js)
- [src/models.js](https://github.com/aanickode/access-control-service/blob/main/src/models.js)
</details>

# Extending and Customizing

## Introduction

The "Extending and Customizing" functionality within this project revolves around the access control and permission management system. It allows defining roles with specific permissions, assigning roles to users, and enforcing access control based on the user's role and the required permissions for a given operation or resource.

This system can be extended by adding new roles, modifying existing roles' permissions, or introducing new permission types as needed by the application's requirements. Customization can involve adjusting the permission checking logic or integrating with external authentication and authorization providers.

## Role and Permission Management

The project defines two core data models: `User` and `Role`. The `User` model represents an individual user with properties like `email` and `role`. The `Role` model defines a named role with an associated list of `permissions`.

```javascript
export const User = {
  email: 'string',
  role: 'string'
};

export const Role = {
  name: 'string',
  permissions: ['string']
};
```

Sources: [src/models.js](https://github.com/aanickode/access-control-service/blob/main/src/models.js)

To extend or customize the role and permission system, developers can modify the `Role` model to include additional properties or constraints as needed. For example, they could introduce a hierarchical role structure or add metadata to roles for better organization and management.

## Access Control Middleware

The `authMiddleware.js` file exports a `checkPermission` function, which is a middleware function for Express.js applications. This middleware checks if the requesting user has the required permission to access a particular resource or perform a specific operation.

```javascript
export function checkPermission(requiredPermission) {
  return function (req, res, next) {
    const userEmail = req.headers['x-user-email'];
    if (!userEmail || !db.users[userEmail]) {
      return res.status(401).json({ error: 'Unauthorized: no user context' });
    }

    const role = db.users[userEmail];
    const permissions = db.roles[role] || [];

    if (!permissions.includes(requiredPermission)) {
      return res.status(403).json({ error: 'Forbidden: insufficient permissions' });
    }

    next();
  };
}
```

Sources: [src/authMiddleware.js](https://github.com/aanickode/access-control-service/blob/main/src/authMiddleware.js)

The `checkPermission` function takes a `requiredPermission` string as an argument and returns a middleware function. This middleware function checks if the user associated with the request (identified by the `x-user-email` header) has the required permission based on their assigned role.

To extend or customize the access control logic, developers can modify the `checkPermission` function or create additional middleware functions. For example, they could introduce more complex permission checks, integrate with external authentication and authorization providers, or implement role-based access control (RBAC) hierarchies.

## Permission Checking Flow

The permission checking flow within the access control system can be represented using the following sequence diagram:

```mermaid
sequenceDiagram
    participant Client
    participant Express
    participant AuthMiddleware
    participant Database

    Client->>Express: Request with x-user-email header
    Express->>AuthMiddleware: checkPermission(requiredPermission)
    AuthMiddleware->>Database: Retrieve user role and permissions
    Database-->>AuthMiddleware: User role and permissions
    alt User has required permission
        AuthMiddleware-->>Express: next()
        Express-->>Client: Successful response
    else User lacks required permission
        AuthMiddleware-->>Express: 403 Forbidden
        Express-->>Client: 403 Forbidden error
    end
```

Sources: [src/authMiddleware.js](https://github.com/aanickode/access-control-service/blob/main/src/authMiddleware.js)

1. The client sends a request to the Express.js application, including the `x-user-email` header to identify the user.
2. The Express.js application invokes the `checkPermission` middleware, passing the required permission for the requested operation or resource.
3. The `checkPermission` middleware retrieves the user's role and associated permissions from the database based on the provided `x-user-email` header.
4. If the user's role has the required permission, the middleware calls `next()` to allow the request to proceed.
5. If the user's role lacks the required permission, the middleware responds with a 403 Forbidden error.

To extend or customize the permission checking flow, developers can modify the middleware logic, introduce additional checks or validations, or integrate with external authorization services.

## Conclusion

The "Extending and Customizing" functionality within this project revolves around the access control and permission management system. It allows defining roles with specific permissions, assigning roles to users, and enforcing access control based on the user's role and the required permissions for a given operation or resource.

Developers can extend or customize this system by modifying the `User` and `Role` models, adjusting the `checkPermission` middleware function, or introducing additional middleware functions to handle more complex access control scenarios. Additionally, they can integrate with external authentication and authorization providers or implement role-based access control (RBAC) hierarchies as needed by the application's requirements.