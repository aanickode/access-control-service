<details>
<summary>Relevant source files</summary>

The following files were used as context for generating this wiki page:

- [src/index.js](https://github.com/aanickode/access-control-service/blob/main/src/index.js)
- [src/routes.js](https://github.com/aanickode/access-control-service/blob/main/src/routes.js)
- [src/authMiddleware.js](https://github.com/aanickode/access-control-service/blob/main/src/authMiddleware.js) (assumed to exist based on import in routes.js)
- [src/db.js](https://github.com/aanickode/access-control-service/blob/main/src/db.js) (assumed to exist based on import in routes.js)
</details>

# Architecture Overview

The Access Control Service is a Node.js application built with Express.js that provides a RESTful API for managing user roles, permissions, and access tokens. It serves as a centralized access control system for other applications or services within the project.

## Application Setup

The application entry point is `src/index.js`, which sets up the Express server, configures middleware, and defines the API routes. The server listens on the specified `PORT` environment variable or defaults to port 8080.

```mermaid
graph TD
    A[index.js] -->|imports| B[express]
    A -->|imports| C[dotenv]
    A -->|imports| D[routes.js]
    A -->|configures| E[Express app]
    E -->|uses| F[JSON middleware]
    E -->|uses| G[API routes]
    G -->|from| D
    E -->|listens on| H[PORT]
```

Sources: [src/index.js](https://github.com/aanickode/access-control-service/blob/main/src/index.js)

## API Routes

The API routes are defined in `src/routes.js`, which imports the necessary dependencies and middleware functions. The routes handle various operations related to users, roles, permissions, and access tokens.

```mermaid
graph TD
    A[routes.js] -->|imports| B[express]
    A -->|imports| C[authMiddleware.js]
    A -->|imports| D[db.js]
    A -->|creates| E[Express Router]
    E -->|GET /users| F[Get users]
    F -->|uses| C
    E -->|POST /roles| G[Create role]
    G -->|uses| C
    E -->|GET /permissions| H[Get permissions]
    H -->|uses| C
    E -->|POST /tokens| I[Create token]
```

Sources: [src/routes.js](https://github.com/aanickode/access-control-service/blob/main/src/routes.js)

### GET /users

This route retrieves a list of all registered users and their associated roles. It requires the `view_users` permission, which is checked by the `checkPermission` middleware imported from `authMiddleware.js`.

```mermaid
sequenceDiagram
    participant Client
    participant Server
    Client->>Server: GET /api/users
    Server->>Server: checkPermission('view_users')
    alt has permission
        Server-->>Client: 200 OK [{ email, role }]
    else no permission
        Server-->>Client: 403 Forbidden
    end
```

Sources: [src/routes.js:5-8](https://github.com/aanickode/access-control-service/blob/main/src/routes.js#L5-L8)

### POST /roles

This route allows creating a new role with a specified name and a list of associated permissions. It requires the `create_role` permission, which is checked by the `checkPermission` middleware.

```mermaid
sequenceDiagram
    participant Client
    participant Server
    Client->>Server: POST /api/roles { name, permissions }
    Server->>Server: checkPermission('create_role')
    alt has permission
        Server->>Server: Store role in db.roles
        Server-->>Client: 201 Created { role, permissions }
    else no permission
        Server-->>Client: 403 Forbidden
    else invalid request
        Server-->>Client: 400 Bad Request
    end
```

Sources: [src/routes.js:10-16](https://github.com/aanickode/access-control-service/blob/main/src/routes.js#L10-L16)

### GET /permissions

This route retrieves a list of all defined roles and their associated permissions. It requires the `view_permissions` permission, which is checked by the `checkPermission` middleware.

```mermaid
sequenceDiagram
    participant Client
    participant Server
    Client->>Server: GET /api/permissions
    Server->>Server: checkPermission('view_permissions')
    alt has permission
        Server-->>Client: 200 OK { roles }
    else no permission
        Server-->>Client: 403 Forbidden
    end
```

Sources: [src/routes.js:18-21](https://github.com/aanickode/access-control-service/blob/main/src/routes.js#L18-L21)

### POST /tokens

This route allows creating an access token for a user by associating the user with a specific role. It does not require any permission checks.

```mermaid
sequenceDiagram
    participant Client
    participant Server
    Client->>Server: POST /api/tokens { user, role }
    alt valid request
        Server->>Server: Store user-role mapping in db.users
        Server-->>Client: 201 Created { user, role }
    else invalid request
        Server-->>Client: 400 Bad Request
    end
```

Sources: [src/routes.js:23-30](https://github.com/aanickode/access-control-service/blob/main/src/routes.js#L23-L30)

## Data Storage

The application uses an in-memory data store (`db.js`) to store user-role mappings and role-permission mappings. This is likely a temporary solution for development purposes and should be replaced with a persistent database in a production environment.

| Key | Value |
| --- | --- |
| `db.users` | Object mapping user identifiers (e.g., email) to roles |
| `db.roles` | Object mapping role names to lists of associated permissions |

Sources: [src/routes.js](https://github.com/aanickode/access-control-service/blob/main/src/routes.js) (references to `db.users` and `db.roles`)

## Authentication and Authorization

The `authMiddleware.js` module (assumed to exist based on the import in `routes.js`) likely contains the implementation of the `checkPermission` middleware function used for authorization checks. This middleware verifies if the authenticated user has the required permission(s) to access a specific route.

```mermaid
sequenceDiagram
    participant Middleware
    participant Database
    Middleware->>Database: Get user's role
    Database-->>Middleware: User's role
    Middleware->>Database: Get permissions for role
    Database-->>Middleware: Role's permissions
    alt has required permission
        Middleware-->>Next: Allow access
    else no permission
        Middleware-->>Client: 403 Forbidden
    end
```

Sources: [src/routes.js](https://github.com/aanickode/access-control-service/blob/main/src/routes.js) (references to `checkPermission` middleware)

## Summary

The Access Control Service provides a centralized API for managing user roles, permissions, and access tokens. It allows creating and retrieving roles with associated permissions, as well as creating access tokens for users by associating them with specific roles. The service enforces authorization checks based on the user's role and required permissions for certain routes. The current implementation uses an in-memory data store, but a persistent database solution should be considered for production environments.