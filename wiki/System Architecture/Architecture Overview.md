<details>
<summary>Relevant source files</summary>

The following files were used as context for generating this wiki page:

- [src/index.js](https://github.com/aanickode/access-control-service/blob/main/src/index.js)
- [src/routes.js](https://github.com/aanickode/access-control-service/blob/main/src/routes.js)
- [src/authMiddleware.js](https://github.com/aanickode/access-control-service/blob/main/src/authMiddleware.js)
- [src/db.js](https://github.com/aanickode/access-control-service/blob/main/src/db.js)
- [package.json](https://github.com/aanickode/access-control-service/blob/main/package.json)
</details>

# Architecture Overview

## Introduction

This document provides an overview of the architecture and components of the Access Control Service, a Node.js application built with Express.js. The service is responsible for managing user roles, permissions, and authentication tokens within an application or system. It exposes a set of RESTful API endpoints for creating roles, assigning roles to users, and retrieving user and permission information.

Sources: [src/index.js](), [src/routes.js](), [package.json]()

## Application Structure

The Access Control Service follows a typical Express.js application structure, with the main entry point defined in `src/index.js`. This file sets up the Express application, configures middleware, and mounts the API routes.

```mermaid
graph TD
    A[index.js] -->|imports| B[express]
    A -->|imports| C[dotenv]
    A -->|imports| D[routes.js]
    A -->|uses| E[express.json()]
    A -->|mounts| D
    A -->|listens on port| F[8080]
```

Sources: [src/index.js]()

## API Routes

The API routes are defined in `src/routes.js`, which imports the necessary dependencies and sets up the route handlers. The following routes are available:

```mermaid
graph TD
    A[/api/users] -->|GET| B[Get all users and roles]
    C[/api/roles] -->|POST| D[Create a new role]
    E[/api/permissions] -->|GET| F[Get all roles and permissions]
    G[/api/tokens] -->|POST| H[Create a token for a user]
    B -->|checkPermission| I[view_users]
    D -->|checkPermission| J[create_role]
    F -->|checkPermission| K[view_permissions]
```

Sources: [src/routes.js]()

### GET /api/users

This route retrieves a list of all users and their assigned roles. It requires the `view_users` permission, which is checked by the `checkPermission` middleware.

```javascript
router.get('/users', checkPermission('view_users'), (req, res) => {
  res.json(Object.entries(db.users).map(([email, role]) => ({ email, role })));
});
```

Sources: [src/routes.js:5-8]()

### POST /api/roles

This route creates a new role with the specified name and permissions. It requires the `create_role` permission, which is checked by the `checkPermission` middleware.

```javascript
router.post('/roles', checkPermission('create_role'), (req, res) => {
  const { name, permissions } = req.body;
  if (!name || !Array.isArray(permissions)) {
    return res.status(400).json({ error: 'Invalid role definition' });
  }
  db.roles[name] = permissions;
  res.status(201).json({ role: name, permissions });
});
```

Sources: [src/routes.js:10-18]()

### GET /api/permissions

This route retrieves a list of all roles and their associated permissions. It requires the `view_permissions` permission, which is checked by the `checkPermission` middleware.

```javascript
router.get('/permissions', checkPermission('view_permissions'), (req, res) => {
  res.json(db.roles);
});
```

Sources: [src/routes.js:20-23]()

### POST /api/tokens

This route creates a new authentication token for a user by assigning a role to the user. It does not require any specific permission.

```javascript
router.post('/tokens', (req, res) => {
  const { user, role } = req.body;
  if (!user || !role) {
    return res.status(400).json({ error: 'Missing user or role' });
  }
  db.users[user] = role;
  res.status(201).json({ user, role });
});
```

Sources: [src/routes.js:25-33]()

## Authentication Middleware

The `checkPermission` middleware is used to enforce role-based access control for certain routes. It checks if the authenticated user has the required permission based on their assigned role.

```javascript
import db from './db.js';

export const checkPermission = (requiredPermission) => {
  return (req, res, next) => {
    const userRole = db.users[req.user];
    if (!userRole) {
      return res.status(403).json({ error: 'Unauthorized' });
    }
    const rolePermissions = db.roles[userRole] || [];
    if (rolePermissions.includes(requiredPermission)) {
      next();
    } else {
      res.status(403).json({ error: 'Forbidden' });
    }
  };
};
```

Sources: [src/authMiddleware.js]()

## Data Storage

The Access Control Service uses an in-memory data store (`src/db.js`) to store user roles, permissions, and authentication tokens. In a production environment, this data would typically be stored in a persistent database.

```javascript
const db = {
  users: {},
  roles: {},
};

export default db;
```

Sources: [src/db.js]()

## Conclusion

The Access Control Service provides a simple and extensible architecture for managing user roles, permissions, and authentication tokens within an application or system. It follows a RESTful API design and leverages Express.js for routing and middleware. The service could be further enhanced by integrating with a persistent database, implementing authentication mechanisms, and adding additional features as needed.