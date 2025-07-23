<details>
<summary>Relevant source files</summary>

The following files were used as context for generating this wiki page:

- [README.md](https://github.com/aanickode/access-control-service/blob/main/README.md)
- [docs/one-pager.md](https://github.com/aanickode/access-control-service/blob/main/docs/one-pager.md)
</details>

# Introduction

The Access Control Service is an internal Role-Based Access Control (RBAC) microservice that provides centralized permission enforcement for internal tools, APIs, and services within the organization. It manages user-role assignments, role-permission mappings, and enforces access controls at runtime, ensuring consistent and auditable permission enforcement across various systems.

The service follows a flat RBAC model without hierarchies or scopes, allowing for declarative role-to-permission mappings defined in a JSON configuration file. It provides middleware-based permission enforcement, CLI tools for bootstrapping and role assignment, and a REST API for role and user management.

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

Sources: [docs/one-pager.md:10-12]()

The key steps in the architecture are:

1. Incoming requests to the `/api/*` routes are intercepted by the authentication middleware.
2. The middleware extracts the user's identity from the `x-user-email` HTTP header.
3. The user's role is resolved by looking up the `db.users` map.
4. The permissions associated with the user's role are loaded from the `config/roles.json` configuration file.
5. The requested route is checked against the user's permissions, and access is either allowed or denied based on the result.

Sources: [docs/one-pager.md:10-12]()

## Role and Permission Management

The Access Control Service provides a CLI tool and a REST API for managing roles and user-role assignments.

### CLI Tool

The CLI tool, `cli/manage.js`, allows administrators to assign roles to users. For example:

```bash
node cli/manage.js assign-role alice@company.com engineer
```

This command assigns the `engineer` role to the user with the email `alice@company.com`.

Sources: [docs/one-pager.md:19-21]()

### REST API

The service exposes a REST API for managing users, roles, and permissions. The following table summarizes the available API endpoints:

| Method | Endpoint         | Description                   | Required Permission |
|--------|------------------|-------------------------------|----------------------|
| GET    | /api/users       | List all users and roles      | `view_users`        |
| POST   | /api/roles       | Create a new role             | `create_role`       |
| GET    | /api/permissions | View all role definitions     | `view_permissions`  |
| POST   | /api/tokens      | Assign user to a role         | *None (bootstrap)*  |

Sources: [docs/one-pager.md:24-30]()

All API requests must include the `x-user-email` header to identify the authenticated user.

Sources: [docs/one-pager.md:32-33]()

## Deployment and Configuration

The Access Control Service is designed to be stateless, with no persistent database. Instead, the configuration is loaded into memory at runtime. This makes it suitable for internal-only usage behind an API gateway.

For persistent configuration storage, the service can be integrated with an external configuration store like etcd or Consul.

Sources: [docs/one-pager.md:35-38]()

To set up and run the service locally, follow these steps:

```bash
cp .env.example .env
npm install
npm run start
```

Sources: [docs/one-pager.md:16-18]()

## Related Documentation

- [`docs/permissions.md`](docs/permissions.md): Detailed documentation on role definitions and structure.
- [`docs/api.md`](docs/api.md): Complete API contract and endpoint specifications.

Sources: [docs/one-pager.md:40-42]()