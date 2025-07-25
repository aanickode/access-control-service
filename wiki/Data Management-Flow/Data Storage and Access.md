<details>
<summary>Relevant source files</summary>

The following files were used as context for generating this wiki page:

- [src/db.js](https://github.com/aanickode/access-control-service/blob/main/src/db.js)
- [src/models.js](https://github.com/aanickode/access-control-service/blob/main/src/models.js)
</details>

# Data Storage and Access

## Introduction

The "Data Storage and Access" component within this project is responsible for managing user data and role-based access control. It defines the data models for users and roles, as well as providing a simple in-memory data store for storing and retrieving this information.

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
2. `roles`: An object that maps role names to their corresponding `Role` objects, which define the permissions associated with each role.

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

Sources: [src/db.js:1-10]()

## Access Control Flow

The access control flow within this project likely involves the following steps:

```mermaid
graph TD
    A[User Request] -->|1. Authenticate| B(Check User Role)
    B -->|2. Retrieve Role| C{Role Permissions}
    C -->|3. Authorize| D[Grant/Deny Access]
```

1. When a user makes a request, their identity is authenticated (e.g., via email and password).
2. The user's role is retrieved from the `users` object in the `db` data store.
3. The permissions associated with the user's role are retrieved from the `roles` object in the `db` data store.
4. Based on the requested operation and the user's role permissions, access is either granted or denied.

Sources: [src/db.js](), [src/models.js]()

## Limitations and Future Enhancements

The current implementation of the "Data Storage and Access" component is relatively simple and has the following limitations:

- The data store is in-memory, which means data is not persisted across application restarts.
- User authentication and authorization mechanisms are not implemented, as the focus is on the data models and access control logic.
- The role-based access control system is basic and may not scale well for complex permission hierarchies or dynamic permission assignments.

Potential future enhancements could include:

- Integrating with a persistent database (e.g., SQL or NoSQL) for storing user and role data.
- Implementing user authentication and session management mechanisms.
- Enhancing the role-based access control system to support more advanced features, such as role hierarchies, dynamic permission assignment, and fine-grained access control.
- Introducing additional data models or relationships to support more complex access control scenarios.

Sources: [src/db.js](), [src/models.js]()