<details>
<summary>Relevant source files</summary>

The following files were used as context for generating this wiki page:

- [README.md](https://github.com/aanickode/access-control-service/blob/main/README.md)
- [docs/one-pager.md](https://github.com/aanickode/access-control-service/blob/main/docs/one-pager.md)
</details>

# Introduction

The Access Control Service is an internal Role-Based Access Control (RBAC) microservice that provides centralized permission enforcement for internal tools, APIs, and services within the organization. It manages user-role assignments, role-permission mappings, and enforces access controls at runtime, ensuring consistent and auditable permission enforcement across various systems.

The service aims to eliminate hardcoded permission logic across internal systems by decoupling role logic from application code. It serves as a single source of truth for access control decisions, promoting code reusability and maintainability.

## Purpose and Overview

The Access Control Service is designed to centralize access control decisions and ensure consistent, auditable permission enforcement across internal systems. It decouples role logic from application code, promoting code reusability and maintainability.

The service follows a flat RBAC model without hierarchies or scopes, allowing for declarative role-to-permission mappings defined in a JSON configuration file. It provides middleware-based permission enforcement, CLI tools for bootstrapping and role assignment, and a REST API for role and user management.

Sources: [docs/one-pager.md:1-14](), [docs/one-pager.md:18-22]()

## Architecture and Data Flow

The Access Control Service follows a straightforward architecture and data flow:

```mermaid
graph TD
    Request(Request) -->|/api/*| authMiddleware[Auth Middleware]
    authMiddleware -->|x-user-email| resolveRole[Resolve Role]
    resolveRole -->|db.users| loadPermissions[Load Permissions]
    loadPermissions -->|config/roles.json| checkPermissions[Check Permissions]
    checkPermissions -->|permissions[]| allowDeny[Allow/Deny]
```

1. Incoming requests to the `/api/*` endpoints are intercepted by the `authMiddleware`.
2. The middleware extracts the user's identity from the `x-user-email` HTTP header.
3. The user's role is resolved by looking up the `db.users` mapping.
4. The permissions associated with the user's role are loaded from the `config/roles.json` configuration file.
5. The requested route is checked against the user's permissions.
6. Based on the permission evaluation, the request is either allowed or denied.

Sources: [docs/one-pager.md:26-30]()

## Role and Permission Management

The Access Control Service provides a CLI tool and a REST API for managing roles and user-role assignments.

### CLI Tool

The `cli/manage.js` script allows administrators to assign roles to users:

```bash
node cli/manage.js assign-role alice@company.com engineer
```

This command assigns the `engineer` role to the user with the email `alice@company.com`.

Sources: [docs/one-pager.md:34-36]()

### REST API

The service exposes the following API endpoints for managing users, roles, and permissions:

| Method | Endpoint         | Description                   | Permission         |
|--------|------------------|-------------------------------|--------------------|
| GET    | /api/users       | List all users and roles      | `view_users`       |
| POST   | /api/roles       | Create a new role             | `create_role`      |
| GET    | /api/permissions | View all role definitions     | `view_permissions` |
| POST   | /api/tokens      | Assign user to a role         | *None (bootstrap)* |

All API requests must include the `x-user-email` header to identify the user making the request.

Sources: [docs/one-pager.md:39-47]()

## Deployment and Persistence

The Access Control Service is designed to be stateless, with no persistent database. The role-permission mappings and user-role assignments are stored in memory, making the service suitable for internal-only usage behind an API gateway.

For persistent storage, the service can be integrated with an external configuration store like etcd or Consul.

Sources: [docs/one-pager.md:50-53]()

## Related Documentation

- [`docs/permissions.md`](docs/permissions.md): Detailed documentation on role definitions and structure.
- [`docs/api.md`](docs/api.md): Complete API contract and endpoint specifications.

Sources: [docs/one-pager.md:56-58]()