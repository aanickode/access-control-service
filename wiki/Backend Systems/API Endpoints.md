<details>
<summary>Relevant source files</summary>

The following files were used as context for generating this wiki page:

- [src/routes.js](https://github.com/aanickode/access-control-service/blob/main/src/routes.js)
- [docs/api.md](https://github.com/aanickode/access-control-service/blob/main/docs/api.md)
</details>

# API Endpoints

## Introduction

The API Endpoints in this project provide a set of RESTful routes for managing user roles, permissions, and access tokens within an access control system. These endpoints allow authorized clients to retrieve user information, create new roles with associated permissions, view available permissions, and generate access tokens for specific users and roles.

Sources: [src/routes.js](), [docs/api.md]()

## User Management

### GET /users

This endpoint retrieves a list of all registered users and their associated roles.

#### Requirements

- The client must have the `view_users` permission.

#### Response

- Status Code: 200 OK
- Body: An array of user objects, each containing the `email` and `role` properties.

```json
[
  { "email": "user1@example.com", "role": "admin" },
  { "email": "user2@example.com", "role": "editor" }
]
```

#### Flow

```mermaid
graph TD
    Client-->|GET /users|Router
    Router-->|checkPermission('view_users')|AuthMiddleware
    AuthMiddleware-->|Authorized|Router
    Router-->|res.json()|Client
```

Sources: [src/routes.js:6-9]()

## Role Management

### POST /roles

This endpoint creates a new role with a specified set of permissions.

#### Requirements

- The client must have the `create_role` permission.
- The request body must contain a `name` property (string) and a `permissions` property (array of strings).

#### Request Body

```json
{
  "name": "editor",
  "permissions": ["edit_content", "publish_content"]
}
```

#### Response

- Status Code: 201 Created
- Body: An object containing the `role` (name) and `permissions` array.

```json
{
  "role": "editor",
  "permissions": ["edit_content", "publish_content"]
}
```

#### Error Handling

- If the request body is invalid (missing `name` or `permissions` array), a 400 Bad Request response is returned with an error message.

```json
{
  "error": "Invalid role definition"
}
```

#### Flow

```mermaid
graph TD
    Client-->|POST /roles|Router
    Router-->|checkPermission('create_role')|AuthMiddleware
    AuthMiddleware-->|Authorized|Router
    Router-->|validateBody|Router
    Router-->|db.roles[name] = permissions|Database
    Router-->|res.status(201).json()|Client
    Router-->|res.status(400).json()|Client
```

Sources: [src/routes.js:11-19]()

## Permission Viewing

### GET /permissions

This endpoint retrieves a list of all available roles and their associated permissions.

#### Requirements

- The client must have the `view_permissions` permission.

#### Response

- Status Code: 200 OK
- Body: An object where the keys are role names, and the values are arrays of permissions.

```json
{
  "admin": ["view_users", "create_role", "view_permissions"],
  "editor": ["edit_content", "publish_content"]
}
```

#### Flow

```mermaid
graph TD
    Client-->|GET /permissions|Router
    Router-->|checkPermission('view_permissions')|AuthMiddleware
    AuthMiddleware-->|Authorized|Router
    Router-->|res.json(db.roles)|Client
```

Sources: [src/routes.js:21-24]()

## Token Generation

### POST /tokens

This endpoint generates an access token for a specific user and role.

#### Requirements

- The request body must contain a `user` property (string) and a `role` property (string).

#### Request Body

```json
{
  "user": "user1@example.com",
  "role": "editor"
}
```

#### Response

- Status Code: 201 Created
- Body: An object containing the `user` and `role` properties.

```json
{
  "user": "user1@example.com",
  "role": "editor"
}
```

#### Error Handling

- If the request body is missing the `user` or `role` property, a 400 Bad Request response is returned with an error message.

```json
{
  "error": "Missing user or role"
}
```

#### Flow

```mermaid
graph TD
    Client-->|POST /tokens|Router
    Router-->|validateBody|Router
    Router-->|db.users[user] = role|Database
    Router-->|res.status(201).json()|Client
    Router-->|res.status(400).json()|Client
```

Sources: [src/routes.js:26-35]()

## Data Model

The project uses an in-memory data store (`db.js`) to store user roles, permissions, and access tokens. The data model consists of two main objects:

| Object   | Description                                                  |
|----------|--------------------------------------------------------------|
| `db.users` | An object where keys are user emails, and values are role names. |
| `db.roles` | An object where keys are role names, and values are arrays of permissions. |

Sources: [src/routes.js]()

## Conclusion

The API Endpoints in this project provide a comprehensive set of routes for managing user roles, permissions, and access tokens within an access control system. The endpoints follow RESTful principles and implement authorization checks using middleware functions. The data model is simple and stored in memory, but in a production environment, it would likely be replaced with a more robust and persistent data storage solution.

Sources: [src/routes.js](), [docs/api.md]()