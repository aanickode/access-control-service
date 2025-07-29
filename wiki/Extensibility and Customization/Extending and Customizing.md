<details>
<summary>Relevant source files</summary>

The following files were used as context for generating this wiki page:

- [src/models.js](https://github.com/aanickode/access-control-service/blob/main/src/models.js)
- [src/routes.js](https://github.com/aanickode/access-control-service/blob/main/src/routes.js)
</details>

# Extending and Customizing

## Introduction

This wiki page covers the process of extending and customizing the access control system within the project. The access control system manages user roles, permissions, and authentication tokens. It provides a set of API endpoints for creating roles, assigning roles to users, viewing users and their roles, and retrieving permissions associated with roles.

Sources: [src/routes.js]()

## Role Management

The system allows creating and managing roles, each with a set of associated permissions. Roles are stored in the `db.roles` object, where the key represents the role name, and the value is an array of permission strings.

### Creating a Role

To create a new role, send a `POST` request to the `/roles` endpoint with the role name and an array of permissions in the request body.

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

Sources: [src/routes.js:8-16]()

### Viewing Permissions

To retrieve a list of all roles and their associated permissions, send a `GET` request to the `/permissions` endpoint.

```javascript
router.get('/permissions', checkPermission('view_permissions'), (req, res) => {
  res.json(db.roles);
});
```

Sources: [src/routes.js:18-21]()

## User Management

The system stores user information, including their email and assigned role, in the `db.users` object.

### Assigning a Role to a User

To assign a role to a user, send a `POST` request to the `/tokens` endpoint with the user's email and the role name in the request body.

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

Sources: [src/routes.js:23-30]()

### Viewing Users and Their Roles

To retrieve a list of all users and their assigned roles, send a `GET` request to the `/users` endpoint. This endpoint is protected by the `view_users` permission.

```javascript
router.get('/users', checkPermission('view_users'), (req, res) => {
  res.json(Object.entries(db.users).map(([email, role]) => ({ email, role })));
});
```

Sources: [src/routes.js:4-7]()

## Data Models

The system uses two main data models: `User` and `Role`.

### User Model

```javascript
export const User = {
  email: 'string',
  role: 'string'
};
```

The `User` model represents a user in the system, with the following properties:

| Property | Type     | Description                  |
|----------|----------|------------------------------|
| `email`  | `string` | The user's email address     |
| `role`   | `string` | The name of the user's role  |

Sources: [src/models.js:1-4]()

### Role Model

```javascript
export const Role = {
  name: 'string',
  permissions: ['string']
};
```

The `Role` model represents a role in the system, with the following properties:

| Property      | Type       | Description                                |
|----------------|------------|-------------------------------------------|
| `name`         | `string`   | The name of the role                      |
| `permissions`  | `string[]` | An array of permission strings for the role |

Sources: [src/models.js:6-9]()

## Permission Checks

The system uses a middleware function `checkPermission` to verify if a user has the required permission before accessing certain routes. This middleware is imported from the `./authMiddleware.js` file (not provided in the given source files).

```javascript
import { checkPermission } from './authMiddleware.js';
```

The `checkPermission` middleware is applied to the following routes:

- `GET /users`: Requires the `view_users` permission.
- `POST /roles`: Requires the `create_role` permission.
- `GET /permissions`: Requires the `view_permissions` permission.

Sources: [src/routes.js:3,5,9,19]()

## Conclusion

The access control system provides a flexible way to manage user roles, permissions, and authentication tokens. It allows creating and managing roles with associated permissions, assigning roles to users, and retrieving user and permission information through API endpoints. The system uses middleware to enforce permission checks for certain routes, ensuring proper access control.