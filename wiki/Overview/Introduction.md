<details>
<summary>Relevant source files</summary>

The following files were used as context for generating this wiki page:

- [README.md](https://github.com/aanickode/access-control-service/blob/main/README.md)
- [docs/one-pager.md](https://github.com/aanickode/access-control-service/blob/main/docs/one-pager.md)
</details>

# Introduction

The Access Control Service is an internal Role-Based Access Control (RBAC) microservice that provides centralized permission enforcement for internal tools, APIs, and services within the organization. It manages user-role assignments, role-permission mappings, and enforces access controls at runtime, eliminating the need for hardcoded permission logic across various internal systems.

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
    allowDeny -->|annotated routes| Response[Response]
```

1. Incoming requests to the `/api/*` endpoints are intercepted by the authentication middleware.
2. The middleware resolves the user's role based on the `x-user-email` HTTP header and the `db.users` mapping.
3. The user's role permissions are loaded from the `config/roles.json` configuration file.
4. The requested route is checked against the user's permissions, and access is either allowed or denied.
5. The response is sent back to the client.

Sources: [docs/one-pager.md:18-22]()

## Permission Management

The Access Control Service provides a declarative approach to managing role-permission mappings through a JSON configuration file (`config/roles.json`). This file defines the available roles and their associated permissions.

```json
{
  "roles": {
    "engineer": ["create_repo", "deploy_app"],
    "manager": ["view_users", "create_role"],
    "admin": ["*"]
  }
}
```

In the example above, the `engineer` role has permissions to create repositories and deploy applications, while the `manager` role can view users and create new roles. The `admin` role has a wildcard (`*`) permission, granting access to all actions.

Sources: [docs/one-pager.md:12]()

## User Management

The Access Control Service provides a CLI tool (`cli/manage.js`) for assigning roles to users. The following command assigns the `engineer` role to the user `alice@company.com`:

```bash
node cli/manage.js assign-role alice@company.com engineer
```

Sources: [docs/one-pager.md:27-29]()

## API Overview

The Access Control Service exposes a REST API for managing users, roles, and permissions. The following table summarizes the available endpoints and their required permissions:

| Method | Endpoint         | Description                   | Permission         |
|--------|------------------|-------------------------------|--------------------|
| GET    | /api/users       | List all users and roles      | `view_users`       |
| POST   | /api/roles       | Create a new role             | `create_role`      |
| GET    | /api/permissions | View all role definitions     | `view_permissions` |
| POST   | /api/tokens      | Assign user to a role         | *None (bootstrap)* |

All API requests must include the `x-user-email` header to identify the authenticated user.

Sources: [docs/one-pager.md:32-40]()

## Deployment and Persistence

The Access Control Service is designed to be stateless, with configuration stored in memory. This makes it suitable for internal-only usage behind an API gateway. For persistent storage of configuration, the service can be integrated with an external configuration store like etcd or Consul.

Sources: [docs/one-pager.md:43-45]()

In summary, the Access Control Service provides a centralized and consistent approach to managing and enforcing permissions across internal systems, ensuring proper access control and reducing the need for hardcoded permission logic in individual applications.