<details>
<summary>Relevant source files</summary>

The following files were used as context for generating this wiki page:

- [src/db.js](https://github.com/aanickode/access-control-service/blob/main/src/db.js)
- [src/models.js](https://github.com/aanickode/access-control-service/blob/main/src/models.js)
</details>

# Data Storage and Access

## Introduction

This section covers the data storage and access mechanisms within the project. It appears to be a simple in-memory data store implementation for managing users, their roles, and associated permissions. The data is stored in a JavaScript object, with separate properties for users and roles.

Sources: [src/db.js]()

## Data Models

The project defines two data models: `User` and `Role`. These models are represented as JavaScript objects with specific properties.

### User Model

```javascript
export const User = {
  email: 'string',
  role: 'string'
};
```

The `User` model has the following properties:

| Property | Type     | Description                      |
|----------|----------|----------------------------------|
| `email`  | `string` | The email address of the user    |
| `role`   | `string` | The name of the role assigned to the user |

Sources: [src/models.js:1-4]()

### Role Model

```javascript
export const Role = {
  name: 'string',
  permissions: ['string']
};
```

The `Role` model has the following properties:

| Property      | Type       | Description                      |
|---------------|------------|----------------------------------|
| `name`        | `string`   | The name of the role             |
| `permissions` | `string[]` | An array of permission strings associated with the role |

Sources: [src/models.js:5-8]()

## Data Storage

The project uses an in-memory data store implemented as a JavaScript object called `db`. This object has two properties: `users` and `roles`.

```javascript
const db = {
  users: {
    'admin@internal.company': 'admin',
    'analyst@internal.company': 'analyst',
  },
  roles: roles
};
```

The `users` property is an object that maps user email addresses to their respective role names. The `roles` property is an object imported from a JSON file (`roles.json`), which likely contains the definitions of roles and their associated permissions.

Sources: [src/db.js:1-9]()

## Roles Configuration

The roles and their associated permissions are defined in a separate JSON file (`roles.json`). This file is imported and assigned to the `roles` property of the `db` object.

```javascript
import roles from '../config/roles.json' assert { type: 'json' };
```

Since the contents of `roles.json` are not provided in the given source files, it's not possible to provide further details about the specific roles and permissions defined in the project.

Sources: [src/db.js:1]()

## Summary

The project implements a simple in-memory data store for managing users, their roles, and associated permissions. The data models (`User` and `Role`) define the structure of the data, while the `db` object serves as the central storage for user and role information. The roles and their permissions are defined in a separate JSON configuration file (`roles.json`).

While this implementation is straightforward, it lacks features like persistence, scalability, and security measures typically found in production-grade data storage solutions. It may be suitable for small-scale or prototyping purposes but would likely need to be replaced or extended for more robust and secure applications.