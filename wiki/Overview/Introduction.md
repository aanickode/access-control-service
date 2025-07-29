<details>
<summary>Relevant source files</summary>

The following files were used as context for generating this wiki page:

- [README.md](https://github.com/aanickode/access-control-service/blob/main/README.md)
- [docs/one-pager.md](https://github.com/aanickode/access-control-service/blob/main/docs/one-pager.md)
</details>

# Introduction

The Access Control Service is an internal Role-Based Access Control (RBAC) microservice that provides centralized permission enforcement for internal tools, APIs, and services within the organization. Its primary purpose is to manage user-role assignments, role-permission mappings, and enforce access controls at runtime, eliminating the need for hardcoded permission logic across various internal systems.

By centralizing access control decisions, this service ensures consistent and auditable permission enforcement while decoupling role logic from application code. It follows a flat RBAC model without hierarchies or scopes, allowing for declarative role-to-permission mappings defined in a JSON configuration file.

## Architecture Overview

The Access Control Service follows a straightforward architecture, as illustrated by the following flow diagram:

```mermaid
graph TD
    Request[Request] -->|/api/*| authMiddleware[Auth Middleware]
    authMiddleware -->|x-user-email| resolveRole[Resolve Role]
    resolveRole -->|db.users| loadPermissions[Load Permissions]
    loadPermissions -->|config/roles.json| checkPermissions[Check Permissions]
    checkPermissions -->|permissions[]| allowDeny[Allow/Deny]
```

1. Incoming requests to the `/api/*` endpoints are intercepted by an authentication middleware.
2. The middleware resolves the user's role based on the `x-user-email` HTTP header and a mapping in the `db.users` data structure.
3. The user's role permissions are loaded from the `config/roles.json` configuration file.
4. The requested route is checked against the user's permissions.
5. Access is either allowed or denied based on the permission evaluation.

Sources: [docs/one-pager.md:18-22]()

## Role and Permission Management

The Access Control Service provides a set of tools and APIs for managing roles, permissions, and user-role assignments.

### CLI Tools

A command-line interface (CLI) tool is available for bootstrapping and assigning roles to users. For example:

```bash
node cli/manage.js assign-role alice@company.com engineer
```

This command assigns the `engineer` role to the user with the email `alice@company.com`.

Sources: [docs/one-pager.md:28-30]()

### REST API

The service exposes a REST API for various role and user management operations, such as listing users and roles, creating new roles, viewing role definitions, and assigning users to roles. The API requires an `x-user-email` header for authentication.

Here's an overview of the available API endpoints and their required permissions:

| Method | Endpoint         | Description                   | Permission         |
|--------|------------------|-------------------------------|--------------------|
| GET    | /api/users       | List all users and roles      | `view_users`       |
| POST   | /api/roles       | Create a new role             | `create_role`      |
| GET    | /api/permissions | View all role definitions     | `view_permissions` |
| POST   | /api/tokens      | Assign user to a role         | *None (bootstrap)* |

Sources: [docs/one-pager.md:33-41]()

## Deployment and Integration

The Access Control Service is designed to be stateless, with no persistent database. Instead, the configuration is stored in memory, making it suitable for internal-only usage behind an API gateway.

For persistent configuration storage, the service can be integrated with an external configuration store like etcd or Consul.

Sources: [docs/one-pager.md:45-48]()

## Related Documentation

- [`docs/permissions.md`](docs/permissions.md): Detailed documentation on role definitions and structure.
- [`docs/api.md`](docs/api.md): Complete API contract and specification.

Sources: [docs/one-pager.md:51-53]()