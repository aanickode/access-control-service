<details>
<summary>Relevant source files</summary>

The following files were used as context for generating this wiki page:

- [README.md](https://github.com/aanickode/access-control-service/blob/main/README.md)
- [docs/one-pager.md](https://github.com/aanickode/access-control-service/blob/main/docs/one-pager.md)
</details>

# Introduction

The Access Control Service is an internal Role-Based Access Control (RBAC) microservice that provides centralized permission enforcement for internal tools, APIs, and services within the organization. It manages user-role assignments, role-permission mappings, and enforces access controls at runtime, ensuring consistent and auditable permission enforcement across various systems.

The service eliminates the need for hardcoded permission logic within individual applications by decoupling role logic from application code. This approach promotes code reusability, maintainability, and consistency in access control decisions.

## Purpose and Overview

The primary purpose of the Access Control Service is to centralize access control decisions and ensure consistent, auditable permission enforcement across internal systems. It achieves this by providing a declarative role-to-permission mapping configuration and a middleware-based permission enforcement mechanism.

The service follows a flat RBAC model, where users are assigned roles, and roles are mapped to specific permissions. When a user attempts to access a protected resource or perform a privileged action, the service checks the user's assigned role and the required permissions for that action. Access is granted or denied based on the role-permission mapping.

Sources: [docs/one-pager.md:3-6](), [docs/one-pager.md:11-13]()

## Architecture and Data Flow

The Access Control Service follows a straightforward architecture and data flow:

```mermaid
flowchart TD
    Client([Client])
    API[/api/* Endpoint]
    AuthMiddleware[authMiddleware]
    RoleResolver[Role Resolver]
    PermissionLoader[Permission Loader]
    PermissionEnforcer[Permission Enforcer]
    Config[config/roles.json]
    UserDB[db.users]

    Client -- Request --> API
    API -- Request --> AuthMiddleware
    AuthMiddleware -- Resolve Role --> RoleResolver
    RoleResolver -- Get Role --> UserDB
    AuthMiddleware -- Load Permissions --> PermissionLoader
    PermissionLoader -- Get Permissions --> Config
    AuthMiddleware -- Check Permissions --> PermissionEnforcer
    PermissionEnforcer -- Allow/Deny --> AuthMiddleware
    AuthMiddleware -- Response --> Client
```

1. The client sends a request to an API endpoint (`/api/*`).
2. The `authMiddleware` intercepts the request and resolves the user's role based on the `x-user-email` HTTP header.
3. The user's role is retrieved from the `db.users` map.
4. The `authMiddleware` loads the required permissions for the requested endpoint from the `config/roles.json` configuration file.
5. The `PermissionEnforcer` checks if the user's role has the required permissions.
6. Based on the permission check, access is granted or denied, and the response is sent back to the client.

Sources: [docs/one-pager.md:18-22]()

## Role and Permission Management

The Access Control Service provides a set of tools and APIs for managing roles, permissions, and user-role assignments.

### CLI Tools

The service includes a command-line interface (CLI) tool for bootstrapping and assigning roles to users. For example, to assign the "engineer" role to the user "alice@company.com", you can run:

```bash
node cli/manage.js assign-role alice@company.com engineer
```

Sources: [docs/one-pager.md:26-28]()

### REST API

The service exposes a REST API for managing users, roles, and permissions. Here are some key endpoints:

| Method | Endpoint         | Description                   | Permission         |
|--------|------------------|-------------------------------|--------------------|
| GET    | /api/users       | List all users and roles      | `view_users`       |
| POST   | /api/roles       | Create a new role             | `create_role`      |
| GET    | /api/permissions | View all role definitions     | `view_permissions` |
| POST   | /api/tokens      | Assign user to a role         | *None (bootstrap)* |

All API requests must include the `x-user-email` HTTP header to identify the user.

Sources: [docs/one-pager.md:31-38]()

## Role and Permission Configuration

The role-to-permission mappings are defined in a JSON configuration file (`config/roles.json`). This file declaratively specifies the permissions associated with each role. The structure and format of this configuration file are detailed in the `docs/permissions.md` documentation.

Sources: [docs/one-pager.md:22](), [docs/one-pager.md:43]()

## Deployment and Persistence

The Access Control Service is designed to be stateless, with no persistent database. The configuration and user-role mappings are stored in memory, making it suitable for internal-only usage behind an API gateway.

For persistent storage and configuration management, the service can be integrated with an external configuration store, such as etcd or Consul.

Sources: [docs/one-pager.md:40-42]()

## Conclusion

The Access Control Service provides a centralized and consistent approach to managing access control and permission enforcement across internal systems. By decoupling role logic from application code and providing a declarative role-permission mapping configuration, the service promotes code reusability, maintainability, and auditable access control decisions.