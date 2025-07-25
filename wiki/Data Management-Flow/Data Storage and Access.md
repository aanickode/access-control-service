<details>
<summary>Relevant source files</summary>

The following files were used as context for generating this wiki page:

- [src/db.js](https://github.com/aanickode/access-control-service/blob/main/src/db.js)
- [src/models.js](https://github.com/aanickode/access-control-service/blob/main/src/models.js)
</details>

# Data Storage and Access

## Introduction

This section covers the data storage and access mechanisms within the project. It provides an overview of how user data and role-based access control (RBAC) information are stored and retrieved. The project appears to use a simple in-memory data structure to store this information, though it could potentially be extended to integrate with a more robust database system.

Sources: [src/db.js](), [src/models.js]()

## Data Models

The project defines two main data models: `User` and `Role`. These models are defined as JavaScript objects with specific properties.

### User Model

The `User` model represents a user in the system and has the following properties:

```javascript
export const User = {
  email: 'string',
  role: 'string'
};
```

- `email` (string): The email address of the user, which serves as the unique identifier.
- `role` (string): The name of the role assigned to the user, which determines their access permissions.

Sources: [src/models.js:1-4]()

### Role Model

The `Role` model defines the roles and associated permissions within the system:

```javascript
export const Role = {
  name: 'string',
  permissions: ['string']
};
```

- `name` (string): The name of the role.
- `permissions` (array of strings): A list of permissions granted to users with this role.

Sources: [src/models.js:6-9]()

## Data Storage

The project uses an in-memory data structure to store user and role information. This data structure is defined in the `src/db.js` file.

```javascript
const db = {
  users: {
    'admin@internal.company': 'admin',
    'analyst@internal.company': 'analyst',
  },
  roles: roles
};
```

- `users` (object): An object that maps user email addresses to their assigned role names.
- `roles` (object): An object containing the role definitions, imported from a JSON configuration file (`roles.json`).

Sources: [src/db.js:3-9]()

The `roles.json` file (not shown) likely contains the actual role definitions, with each role specifying its name and associated permissions.

## Data Access

The `db` object serves as the central data access point for retrieving user and role information. Since the data is stored in-memory, there are no explicit functions or APIs for querying or modifying the data. However, the project could potentially extend the `db` object to include methods for interacting with the data, such as:

- Retrieving a user by email
- Retrieving a role by name
- Checking if a user has a specific permission
- Adding or modifying users and roles

These methods would likely access and manipulate the `db.users` and `db.roles` objects directly.

Sources: [src/db.js]()

## Potential Enhancements

While the current implementation uses a simple in-memory data structure, the project could be enhanced to integrate with a more robust and persistent data storage solution, such as a relational database or a NoSQL database. This would involve:

1. Defining database schemas or models based on the existing `User` and `Role` data models.
2. Implementing data access layers or repositories to interact with the database.
3. Updating the existing code to use the new data access layers instead of directly accessing the in-memory `db` object.

Additionally, the project could benefit from implementing proper authentication and authorization mechanisms, as well as adding support for user management operations (e.g., creating, updating, and deleting users and roles).

Sources: [src/models.js]()

## Conclusion

The "Data Storage and Access" section covers how user and role-based access control information is stored and retrieved within the project. While the current implementation uses a simple in-memory data structure, the project could be extended to integrate with a more robust and persistent data storage solution, as well as implement additional features like authentication, authorization, and user management.