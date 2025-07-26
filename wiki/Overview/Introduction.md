<details>
<summary>Relevant source files</summary>

The following files were used as context for generating this wiki page:

- [README.md](https://github.com/aanickode/access-control-service/blob/main/README.md)
- [docs/one-pager.md](https://github.com/aanickode/access-control-service/blob/main/docs/one-pager.md)
</details>

# Introduction

The Access Control Service is an internal Role-Based Access Control (RBAC) microservice that provides centralized permission enforcement for internal tools, APIs, and services within the organization. It manages user-role assignments, role-permission mappings, and enforces access controls at runtime, eliminating the need for hardcoded permission logic across various internal systems.

The service follows a flat RBAC model without hierarchies or scopes, and it utilizes declarative role-to-permission mappings defined in a JSON configuration file. It offers middleware-based permission enforcement, CLI tools for bootstrapping and role assignment, and a REST API for role and user management.

## Purpose and Overview

The primary purpose of the Access Control Service is to ensure consistent, auditable permission enforcement across internal systems while decoupling role logic from application code. By centralizing access control decisions, it simplifies the management and maintenance of permissions, reducing the risk of inconsistencies and errors.

Sources: [docs/one-pager.md:3-6](), [docs/one-pager.md:10-12]()

## Architecture and Data Flow

The service follows a straightforward architecture and data flow:

```mermaid
graph TD
    Request-->API[/api/* Endpoint]
    API-->AuthMiddleware[authMiddleware]
    AuthMiddleware-->UserRole[Resolve User Role]
    UserRole-->RolePermissions[Load Role Permissions]
    RolePermissions-->Decision{Allow/Deny}
    Decision-->Response
```

1. Requests are made to the `/api/*` endpoints.
2. The `authMiddleware` middleware component is invoked.
3. The user's role is resolved based on the `x-user-email` HTTP header and the `db.users` map.
4. The permissions associated with the user's role are loaded from the `config/roles.json` configuration file.
5. The requested route is checked against the required permissions for the user's role.
6. The request is either allowed or denied based on the permission evaluation.

Sources: [docs/one-pager.md:20-24]()

## Key Components

### Authentication Middleware

The `authMiddleware` component is responsible for extracting the user's identity from the `x-user-email` HTTP header and resolving the corresponding role from the `db.users` map. It then loads the permissions associated with the user's role from the `config/roles.json` configuration file.

Sources: [docs/one-pager.md:21-22]()

### Role-Permission Mapping

The role-permission mappings are defined in a JSON configuration file (`config/roles.json`). This file declaratively maps roles to their respective permissions, allowing for easy management and updates without modifying the application code.

Sources: [docs/one-pager.md:22]()

### Route Annotation

The service's routes are annotated with the required permissions for access. The `authMiddleware` component checks the requested route against the user's role permissions to determine whether the request should be allowed or denied.

Sources: [docs/one-pager.md:23]()

## CLI Tools

The service provides CLI tools for bootstrapping and role assignment. The `manage.js` script allows administrators to assign roles to users, as shown in the following example:

```bash
node cli/manage.js assign-role alice@company.com engineer
```

This command assigns the `engineer` role to the user with the email `alice@company.com`.

Sources: [docs/one-pager.md:27-29]()

## API Overview

The Access Control Service exposes a REST API for user and role management. The following table summarizes the available API endpoints and their corresponding permissions:

| Method | Endpoint         | Description                   | Permission         |
|--------|------------------|-------------------------------|--------------------|
| GET    | /api/users       | List all users and roles      | `view_users`       |
| POST   | /api/roles       | Create a new role             | `create_role`      |
| GET    | /api/permissions | View all role definitions     | `view_permissions` |
| POST   | /api/tokens      | Assign user to a role         | *None (bootstrap)* |

All API requests must include the `x-user-email` HTTP header with the user's email address.

Sources: [docs/one-pager.md:32-39]()

## Deployment and Persistence

The Access Control Service is designed to be stateless, with the configuration stored in memory. This makes it suitable for internal-only usage behind an API gateway. However, for persistent storage of the configuration, the service can be integrated with an external configuration store like etcd or Consul.

Sources: [docs/one-pager.md:42-44]()

## Related Documentation

- [`docs/permissions.md`](docs/permissions.md): Provides detailed information about the role definitions and structure.
- [`docs/api.md`](docs/api.md): Describes the complete API contract for the Access Control Service.

Sources: [docs/one-pager.md:47-48]()

In summary, the Access Control Service is a crucial component that centralizes permission enforcement and role management for internal systems within the organization. By providing a consistent and auditable approach to access control, it simplifies the development and maintenance of secure applications while ensuring proper separation of concerns.