<details>
<summary>Relevant source files</summary>

The following files were used as context for generating this wiki page:

- [config/roles.json](https://github.com/aanickode/access-control-service/blob/main/config/roles.json)
- [src/models.js](https://github.com/aanickode/access-control-service/blob/main/src/models.js)
</details>

# Role Management

## Introduction

The Role Management feature within this project provides a way to define and manage user roles and their associated permissions. It allows for the creation of roles with specific sets of permissions, and the assignment of these roles to individual users. This feature is crucial for implementing access control and ensuring that users have the appropriate level of access to various parts of the system based on their roles.

Sources: [config/roles.json](), [src/models.js]()

## Role Definition

Roles are defined in a JSON configuration file (`config/roles.json`). Each role is represented as a key-value pair, where the key is the role name, and the value is an array of permission strings.

```json
{
  "admin": ["view_users", "create_role", "view_permissions"],
  "engineer": ["view_users", "view_permissions"],
  "analyst": ["view_users"]
}
```

In the example above, three roles are defined: `admin`, `engineer`, and `analyst`. The `admin` role has permissions to view users, create roles, and view permissions. The `engineer` role can view users and permissions, while the `analyst` role can only view users.

Sources: [config/roles.json]()

## Data Models

The project defines two data models related to role management: `User` and `Role`.

### User Model

```javascript
export const User = {
  email: 'string',
  role: 'string'
};
```

The `User` model represents a user in the system. It has two properties:

- `email`: A string representing the user's email address.
- `role`: A string representing the user's assigned role.

Sources: [src/models.js:1-4]()

### Role Model

```javascript
export const Role = {
  name: 'string',
  permissions: ['string']
};
```

The `Role` model represents a role in the system. It has two properties:

- `name`: A string representing the name of the role.
- `permissions`: An array of strings representing the permissions associated with the role.

Sources: [src/models.js:6-9]()

## Role Assignment

To assign a role to a user, the `role` property of the `User` model should be set to the desired role name defined in the `config/roles.json` file.

```javascript
const user = {
  email: 'john@example.com',
  role: 'admin'
};
```

In this example, the user with the email `john@example.com` is assigned the `admin` role, granting them the permissions defined for that role in the `config/roles.json` file.

Sources: [src/models.js:1-4](), [config/roles.json]()

## Permission Checking

To check if a user has a specific permission, the user's role and its associated permissions can be looked up in the `config/roles.json` file. If the requested permission is present in the array of permissions for the user's role, the user is granted access; otherwise, access is denied.

```javascript
const userRole = getUserRole(user);
const permissions = getRolePermissions(userRole);

if (permissions.includes('view_users')) {
  // User has permission to view users
} else {
  // User does not have permission to view users
}
```

In this example, the `getUserRole` and `getRolePermissions` functions (not shown) would retrieve the user's role and its associated permissions from the data models and configuration file, respectively. The code then checks if the `'view_users'` permission is included in the user's role permissions.

Sources: [config/roles.json](), [src/models.js]()

## Conclusion

The Role Management feature in this project provides a flexible and extensible way to define roles, assign roles to users, and check user permissions based on their roles. By separating the role definitions from the application code and using a configuration file, it becomes easier to manage and update roles and permissions without modifying the codebase directly.