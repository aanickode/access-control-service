<details>
<summary>Relevant source files</summary>

The following files were used as context for generating this wiki page:

- [src/db.js](https://github.com/aanickode/access-control-service/blob/main/src/db.js)
- [src/models.js](https://github.com/aanickode/access-control-service/blob/main/src/models.js)
</details>

# Data Storage and Access

## Introduction

The "Data Storage and Access" component of this project is responsible for managing user data and role-based access control. It defines the data models for users and roles, and provides a simple in-memory data store for storing and retrieving this information.

Sources: [src/db.js](), [src/models.js]()

## Data Models

### User Model

The `User` model represents a user entity within the system. It has the following properties:

```javascript
export const User = {
  email: 'string',
  role: 'string'
};
```

- `email` (string): The email address of the user, which serves as a unique identifier.
- `role` (string): The name of the role assigned to the user, which determines their access permissions.

Sources: [src/models.js:1-4]()

### Role Model

The `Role` model defines the access permissions associated with a particular role. It has the following properties:

```javascript
export const Role = {
  name: 'string',
  permissions: ['string']
};
```

- `name` (string): The name of the role, which is used to associate users with a set of permissions.
- `permissions` (array of strings): A list of permission strings that define the access rights granted to users with this role.

Sources: [src/models.js:6-9]()

## Data Storage

The project uses an in-memory data store implemented as a JavaScript object called `db`. This data store contains two main properties:

1. `users`: An object that maps user email addresses to their respective roles.
2. `roles`: An object that defines the available roles and their associated permissions.

```javascript
const db = {
  users: {
    'admin@internal.company': 'admin',
    'analyst@internal.company': 'analyst',
  },
  roles: roles
};
```

The `roles` object is imported from a JSON configuration file (`roles.json`) located in the `config` directory.

Sources: [src/db.js:1-9]()

## Data Access

The `db` object serves as the central data store for user and role information. It can be imported and used throughout the application to retrieve user data and role permissions.

For example, to get the role of a user with a specific email address, you can access the `users` object:

```javascript
const userRole = db.users['admin@internal.company']; // 'admin'
```

To get the permissions associated with a particular role, you can access the `roles` object:

```javascript
const adminPermissions = db.roles.admin.permissions; // ['create:*', 'read:*', 'update:*', 'delete:*']
```

Sources: [src/db.js:3-6](), [src/db.js:8]()

## Limitations and Future Improvements

The current implementation of the data storage and access component is very basic and lacks several features that would be expected in a production-ready system:

- **In-Memory Storage**: The data is stored in an in-memory JavaScript object, which means it will be lost when the application restarts. A persistent storage solution, such as a database, should be implemented.
- **Static Data**: The user and role data is hardcoded in the application code and configuration files. A mechanism for dynamically managing users and roles should be provided.
- **Security Concerns**: User passwords are stored in plaintext, which is a major security risk. Proper password hashing and salting techniques should be employed.
- **Lack of User Management**: There is no functionality for creating, updating, or deleting users or roles. User management features should be added.
- **Limited Role-Based Access Control**: The current implementation only associates users with a single role. A more flexible and granular permission system may be required.

Sources: [src/db.js](), [src/models.js]()

In summary, while the current implementation provides a basic foundation for data storage and access, it should be considered a starting point and would require significant enhancements to meet the requirements of a production-ready application.