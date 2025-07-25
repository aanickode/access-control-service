<details>
<summary>Relevant source files</summary>

The following files were used as context for generating this wiki page:

- [README.md](https://github.com/aanickode/access-control-service/blob/main/README.md)
- [docs/one-pager.md](https://github.com/aanickode/access-control-service/blob/main/docs/one-pager.md)
</details>

# Introduction

The Access Control Service is an internal Role-Based Access Control (RBAC) microservice that provides centralized permission enforcement for internal tools, APIs, and services within the organization. Its primary purpose is to eliminate hardcoded permission logic across various systems by centralizing access control decisions, ensuring consistent and auditable permission enforcement, and decoupling role logic from application code.

Sources: [docs/one-pager.md:3-5](), [docs/one-pager.md:9-11]()

## Features

The Access Control Service offers the following key features:

- **Flat RBAC Model**: It implements a flat RBAC model without hierarchies or scopes, simplifying role and permission management.
- **Declarative Role-Permission Mappings**: Role-to-permission mappings are defined declaratively in a JSON configuration file, allowing for easy maintenance and updates.
- **Middleware-based Permission Enforcement**: The service provides a middleware component for enforcing permissions at runtime, ensuring that only authorized users can access protected resources or perform specific actions.
- **CLI Tools**: Command-line interface (CLI) tools are available for bootstrapping the service and assigning roles to users.
- **REST API**: A RESTful API is provided for managing roles, users, and permissions.

Sources: [docs/one-pager.md:14-19]()

## Architecture Overview

The Access Control Service follows a simple architecture, as illustrated by the following flow diagram:

```mermaid
graph TD
    Request --> API[/api/*]
    API --> AuthMiddleware[authMiddleware]
    AuthMiddleware --> ResolveRole[role]
    ResolveRole --> LoadPermissions[permissions[]]
    LoadPermissions --> AllowDeny[allow/deny]
```

1. Incoming requests to the `/api/*` endpoints are intercepted by the `authMiddleware`.
2. The user's identity is extracted from the `x-user-email` HTTP header.
3. The user's role is resolved by looking up the `db.users` map.
4. The permissions associated with the user's role are loaded from the `config/roles.json` configuration file.
5. The requested route is checked against the user's permissions, and access is either allowed or denied.

Sources: [docs/one-pager.md:22-27]()

## User and Role Management

The Access Control Service provides a CLI tool and a REST API for managing users and roles.

### CLI Tool

The `cli/manage.js` script allows administrators to assign roles to users. For example:

```bash
node cli/manage.js assign-role alice@company.com engineer
```

This command assigns the `engineer` role to the user with the email `alice@company.com`.

Sources: [docs/one-pager.md:30-32]()

### REST API

The service exposes the following API endpoints for user and role management:

| Method | Endpoint         | Description                   | Permission         |
|--------|------------------|-------------------------------|--------------------|
| GET    | /api/users       | List all users and roles      | `view_users`       |
| POST   | /api/roles       | Create a new role             | `create_role`      |
| GET    | /api/permissions | View all role definitions     | `view_permissions` |
| POST   | /api/tokens      | Assign user to a role         | *None (bootstrap)* |

All API requests must include the `x-user-email` header with the user's email address.

Sources: [docs/one-pager.md:35-42]()

## Deployment and Configuration

The Access Control Service is designed to be stateless and suitable for internal-only usage behind an API gateway. By default, the configuration is stored in memory, but for persistence, the service can be integrated with an external configuration store like etcd or Consul.

To set up the service locally, follow these steps:

```bash
cp .env.example .env
npm install
npm run start
```

Sources: [docs/one-pager.md:45-50](), [docs/one-pager.md:14-16]()

## Related Documentation

- [`docs/permissions.md`](docs/permissions.md): Detailed documentation on role definitions and structure.
- [`docs/api.md`](docs/api.md): Complete API contract and endpoint specifications.

Sources: [docs/one-pager.md:53-54]()