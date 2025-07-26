<details>
<summary>Relevant source files</summary>

The following files were used as context for generating this wiki page:

- [src/db.js](https://github.com/aanickode/access-control-service/blob/main/src/db.js)
- [src/models.js](https://github.com/aanickode/access-control-service/blob/main/src/models.js)
</details>

# Data Storage and Access

## Introduction

The "Data Storage and Access" component within this project is responsible for managing user data and role-based access control. It provides a centralized data store for user information, including their email addresses, roles, and associated permissions. This component serves as the foundation for implementing authentication and authorization mechanisms throughout the application.

## Data Models

### User Model

The `User` model represents a user entity within the system. It consists of the following fields:

```javascript
export const User = {
  email: 'string',
  role: 'string'
};
```

- `email` (string): The email address of the user, which serves as a unique identifier.
- `role` (string): The role assigned to the user, which determines their access permissions.

Sources: [src/models.js:1-4]()

### Role Model

The `Role` model defines the structure of roles within the system and their associated permissions.

```javascript
export const Role = {
  name: 'string',
  permissions: ['string']
};
```

- `name` (string): The name of the role.
- `permissions` (array of strings): An array of permission strings granted to users with this role.

Sources: [src/models.js:6-9]()

## Data Storage

The project uses an in-memory data store implemented in the `db.js` file. This data store holds the user and role data for the application.

```javascript
const db = {
  users: {
    'admin@internal.company': 'admin',
    'analyst@internal.company': 'analyst',
  },
  roles: roles
};
```

- `users` (object): An object that maps user email addresses to their respective roles.
- `roles` (object): An object containing the defined roles and their associated permissions, imported from a JSON configuration file (`roles.json`).

Sources: [src/db.js:3-9]()

### Role Configuration

The roles and their associated permissions are defined in a JSON configuration file (`roles.json`). This file is imported and used to populate the `roles` object in the `db` data store.

```javascript
import roles from '../config/roles.json' assert { type: 'json' };
```

Sources: [src/db.js:1]()

## Data Access

The `db` object serves as the central data store for user and role information. It can be imported and used throughout the application to retrieve user data, check roles, and verify permissions.

```javascript
import db from './db.js';

// Access user data
const userRole = db.users['admin@internal.company']; // 'admin'

// Access role data
const adminPermissions = db.roles.admin.permissions;
```

By importing the `db` object, other components of the application can access and utilize the stored user and role data as needed.

Sources: [src/db.js:10](), [src/models.js]()

## Sequence Diagram: User Authentication and Authorization

The following sequence diagram illustrates a potential flow for user authentication and authorization using the provided data models and storage:

```mermaid
sequenceDiagram
    participant Client
    participant AuthService
    participant DataStore

    Client->>AuthService: Login(email, password)
    AuthService->>DataStore: Retrieve user data
    DataStore-->>AuthService: User data
    Note right of AuthService: Verify credentials

    opt Successful authentication
        AuthService-->>Client: Authentication successful
        Client->>AuthService: Request resource
        AuthService->>DataStore: Retrieve user role
        DataStore-->>AuthService: User role
        AuthService->>DataStore: Retrieve role permissions
        DataStore-->>AuthService: Role permissions
        Note right of AuthService: Check permissions
        opt Authorized
            AuthService-->>Client: Provide resource
        else Not authorized
            AuthService-->>Client: Access denied
        end
    else Authentication failed
        AuthService-->>Client: Authentication failed
    end
```

1. The client initiates the login process by sending the user's email and password to the `AuthService`.
2. The `AuthService` retrieves the user data from the `DataStore` and verifies the provided credentials.
3. If authentication is successful, the `AuthService` notifies the client.
4. The client then requests a resource from the `AuthService`.
5. The `AuthService` retrieves the user's role from the `DataStore`.
6. The `AuthService` retrieves the permissions associated with the user's role from the `DataStore`.
7. The `AuthService` checks if the user has the required permissions for the requested resource.
8. If the user is authorized, the `AuthService` provides the requested resource to the client.
9. If the user is not authorized, the `AuthService` denies access to the resource.

Sources: [src/db.js](), [src/models.js]()

## Conclusion

The "Data Storage and Access" component in this project provides a centralized data store for user and role information, enabling user authentication and role-based access control. It defines data models for users and roles, stores user and role data in an in-memory data store, and allows other components to access and utilize this data as needed. By integrating this component with authentication and authorization mechanisms, the application can ensure secure access to resources based on user roles and permissions.