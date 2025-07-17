<details>
<summary>Relevant source files</summary>

The following files were used as context for generating this wiki page:

- [README.md](https://github.com/aanickode/access-control-service/blob/main/README.md)
- [docs/one-pager.md](https://github.com/aanickode/access-control-service/blob/main/docs/one-pager.md)
</details>

# Introduction

The Access Control Service is an internal Role-Based Access Control (RBAC) microservice that provides centralized permission enforcement for internal tools, APIs, and services within a company or organization. Its primary purpose is to manage user-role assignments, role-permission mappings, and enforce access controls at runtime, ensuring consistent, auditable permission enforcement across various internal systems.

By centralizing access control decisions, this service eliminates the need for hardcoded permission logic within individual applications, promoting code decoupling and maintainability. It serves as a single source of truth for managing roles, permissions, and user assignments, enabling consistent access control policies across the organization.

## Features

The Access Control Service offers the following key features:

- **Flat RBAC Model**: It implements a flat Role-Based Access Control model without hierarchies or scopes, simplifying the role and permission structure.
- **Declarative Role-Permission Mappings**: Role-to-permission mappings are defined declaratively in a JSON configuration file, allowing for easy management and updates.
- **Middleware-based Permission Enforcement**: The service provides middleware components that can be integrated into internal applications to enforce access controls at runtime based on user roles and required permissions.
- **CLI Tools**: Command-line interface (CLI) tools are available for bootstrapping the service and assigning roles to users.
- **REST API**: A RESTful API is provided for managing roles, users, and permissions, enabling integration with other systems and automation.

Sources: [docs/one-pager.md:8-18]()

## Architecture Overview

The Access Control Service follows a straightforward architecture for handling access control requests:

```mermaid
graph TD
    Request[Request] -->|/api/*| authMiddleware[Auth Middleware]
    authMiddleware -->|x-user-email| resolveRole[Resolve Role]
    resolveRole -->|db.users| loadPermissions[Load Permissions]
    loadPermissions -->|config/roles.json| checkPermissions[Check Permissions]
    checkPermissions -->|permissions[]| allowDeny[Allow/Deny]
```

1. Incoming requests to the `/api/*` endpoints are intercepted by the authentication middleware.
2. The middleware extracts the user's identity from the `x-user-email` HTTP header.
3. The user's role is resolved by looking up the user in the `db.users` mapping.
4. The permissions associated with the user's role are loaded from the `config/roles.json` configuration file.
5. The requested route is checked against the user's permissions to determine if access should be granted or denied.

Sources: [docs/one-pager.md:22-28]()

## Setup and Usage

### Setup

To set up the Access Control Service, follow these steps:

1. Copy the example environment file: `cp .env.example .env`
2. Install dependencies: `npm install`
3. Start the service: `npm run start`

Sources: [docs/one-pager.md:31-35]()

### CLI Usage

The service provides a command-line interface (CLI) tool for managing user-role assignments. For example, to assign the "engineer" role to the user "alice@company.com", run:

```bash
node cli/manage.js assign-role alice@company.com engineer
```

Sources: [docs/one-pager.md:38-40]()

## API Overview

The Access Control Service exposes a RESTful API for managing users, roles, and permissions. Here's an overview of the available endpoints:

| Method | Endpoint         | Description                   | Required Permission |
|--------|------------------|-------------------------------|----------------------|
| GET    | /api/users       | List all users and roles      | `view_users`        |
| POST   | /api/roles       | Create a new role             | `create_role`       |
| GET    | /api/permissions | View all role definitions     | `view_permissions`  |
| POST   | /api/tokens      | Assign user to a role         | *None (bootstrap)*  |

> **Note:** All API requests must include the `x-user-email` HTTP header with the user's email address.

Sources: [docs/one-pager.md:43-52]()

## Deployment Notes

The Access Control Service is designed to be stateless and does not persist data to a database. Instead, the configuration is kept in memory. This makes it suitable for internal-only usage behind an API gateway.

For persistent storage and configuration management, the service can be integrated with an external configuration store like etcd or Consul.

Sources: [docs/one-pager.md:55-58]()

## Related Documentation

- [`docs/permissions.md`](docs/permissions.md): Detailed documentation on role definitions and the structure of the `config/roles.json` file.
- [`docs/api.md`](docs/api.md): Complete API contract and endpoint specifications for the Access Control Service.

Sources: [docs/one-pager.md:61-63]()