<details>
<summary>Relevant source files</summary>

The following files were used as context for generating this wiki page:

- [src/routes.js](https://github.com/aanickode/access-control-service/blob/main/src/routes.js)
- [src/models.js](https://github.com/aanickode/access-control-service/blob/main/src/models.js)
</details>

# Extending and Customizing

## Introduction

The "Extending and Customizing" functionality within this project refers to the ability to define and manage roles and permissions for access control. This feature allows administrators to create new roles with specific sets of permissions, assign users to these roles, and control access to various resources or actions based on the assigned permissions.

The project appears to be an access control service or module that handles user authentication, role management, and permission checking. It provides a set of API endpoints for managing users, roles, and permissions, as well as generating access tokens for authenticated users.

## Role Management

The project allows creating and managing roles, each with a unique name and a set of associated permissions. Roles are stored in the `db.roles` object, where the role name is the key, and the value is an array of permission strings.

### Creating a New Role

To create a new role, a POST request is made to the `/roles` endpoint with the role definition in the request body. The request must include a `name` property (string) and a `permissions` property (array of strings).

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

Sources: [src/routes.js:10-17]()

### Viewing Roles and Permissions

The `/permissions` endpoint allows retrieving a list of all defined roles and their associated permissions. This endpoint is protected by the `view_permissions` permission.

```javascript
router.get('/permissions', checkPermission('view_permissions'), (req, res) => {
  res.json(db.roles);
});
```

Sources: [src/routes.js:18-21]()

## User Management

The project maintains a mapping of users to their assigned roles in the `db.users` object, where the key is the user's email, and the value is the name of the assigned role.

### Assigning a Role to a User

To assign a role to a user, a POST request is made to the `/tokens` endpoint with the user's email and the role name in the request body.

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

Sources: [src/routes.js:22-29]()

### Viewing Users and Their Roles

The `/users` endpoint allows retrieving a list of all registered users and their assigned roles. This endpoint is protected by the `view_users` permission.

```javascript
router.get('/users', checkPermission('view_users'), (req, res) => {
  res.json(Object.entries(db.users).map(([email, role]) => ({ email, role })));
});
```

Sources: [src/routes.js:4-7]()

## Data Models

The project defines two data models: `User` and `Role`.

### User Model

The `User` model represents a user in the system and has the following properties:

| Property | Type     | Description |
|----------|----------|-------------|
| email    | `string` | The user's email address |
| role     | `string` | The name of the role assigned to the user |

```javascript
export const User = {
  email: 'string',
  role: 'string'
};
```

Sources: [src/models.js:1-4]()

### Role Model

The `Role` model represents a role in the system and has the following properties:

| Property    | Type       | Description |
|-------------|------------|-------------|
| name        | `string`   | The name of the role |
| permissions | `string[]` | An array of permission strings associated with the role |

```javascript
export const Role = {
  name: 'string',
  permissions: ['string']
};
```

Sources: [src/models.js:5-8]()

## Permission Checking

The project includes an `authMiddleware.js` file (not provided in the source files) that likely contains a `checkPermission` middleware function. This middleware is used to protect certain routes by checking if the authenticated user has the required permission(s) based on their assigned role.

```javascript
import { checkPermission } from './authMiddleware.js';

router.get('/users', checkPermission('view_users'), (req, res) => {
  // ...
});

router.get('/permissions', checkPermission('view_permissions'), (req, res) => {
  // ...
});

router.post('/roles', checkPermission('create_role'), (req, res) => {
  // ...
});
```

Sources: [src/routes.js:3,5,13,19]()

## Conclusion

The "Extending and Customizing" functionality in this project revolves around the management of roles and permissions for access control. It allows administrators to create new roles with specific sets of permissions, assign users to these roles, and control access to various resources or actions based on the assigned permissions. The project provides a set of API endpoints for managing users, roles, and permissions, as well as generating access tokens for authenticated users.