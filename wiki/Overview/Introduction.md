<details>
<summary>Relevant source files</summary>

The following files were used as context for generating this wiki page:

- [README.md](https://github.com/aanickode/access-control-service/blob/main/README.md)
- [docs/one-pager.md](https://github.com/aanickode/access-control-service/blob/main/docs/one-pager.md)
</details>

# Introduction

The Access Control Service is an internal Role-Based Access Control (RBAC) microservice that provides centralized permission enforcement for internal tools, APIs, and services within the organization. Its primary purpose is to manage user-role assignments, role-permission mappings, and enforce access controls at runtime, eliminating the need for hardcoded permission logic across various internal systems. By centralizing access control decisions, the service ensures consistent, auditable permission enforcement and decouples role logic from application code.

Sources: [docs/one-pager.md:1-5](), [docs/one-pager.md:10-12]()

## Features

The Access Control Service offers the following key features:

- **Flat RBAC Model**: It implements a flat Role-Based Access Control model without support for scopes or hierarchies.
- **Declarative Role-Permission Mappings**: Role-to-permission mappings are defined declaratively in a JSON configuration file.
- **Middleware-based Permission Enforcement**: The service provides middleware components for enforcing permissions at runtime.
- **CLI Tools**: Command-line interface (CLI) tools are available for bootstrapping and assigning roles to users.
- **REST API**: A RESTful API is provided for managing roles and users.

Sources: [docs/one-pager.md:15-20]()

## Architecture Overview

The Access Control Service follows a simple architecture for handling requests and enforcing permissions:

```mermaid
graph TD
    A[Request] -->|/api/*| B[authMiddleware]
    B --> C[Role Resolution]
    C --> D[Load Permissions]
    D -->|permissions[]| E[Allow/Deny]
```

1. Requests to the `/api/*` routes are intercepted by the `authMiddleware`.
2. The user's identity is obtained from the `x-user-email` HTTP header.
3. The user's role is resolved by looking up the `db.users` map.
4. The permissions associated with the user's role are loaded from the `config/roles.json` configuration file.
5. Based on the required permissions annotated on the routes, access is either allowed or denied.

Sources: [docs/one-pager.md:24-28]()

## User and Role Management

### CLI Tool

The Access Control Service provides a command-line interface (CLI) tool for managing user-role assignments. The tool can be used to assign roles to users, as shown in the following example:

```bash
node cli/manage.js assign-role alice@company.com engineer
```

This command assigns the `engineer` role to the user with the email `alice@company.com`.

Sources: [docs/one-pager.md:32-34]()

### REST API

The service also exposes a RESTful API for managing users and roles. The following table summarizes the available API endpoints and their required permissions:

| Method | Endpoint         | Description                   | Permission         |
|--------|------------------|-------------------------------|--------------------|
| GET    | /api/users       | List all users and roles      | `view_users`       |
| POST   | /api/roles       | Create a new role             | `create_role`      |
| GET    | /api/permissions | View all role definitions     | `view_permissions` |
| POST   | /api/tokens      | Assign user to a role         | *None (bootstrap)* |

All API requests must include the `x-user-email` header to provide the user's identity.

Sources: [docs/one-pager.md:38-44](), [docs/one-pager.md:47-48]()

## Deployment and Configuration

The Access Control Service is designed to be stateless and suitable for internal-only usage behind an API gateway. By default, the configuration is stored in memory, but for persistence, the service can be integrated with an external configuration store like etcd or Consul.

To set up the service, follow these steps:

```bash
cp .env.example .env
npm install
npm run start
```

This will copy the example environment file, install the required dependencies, and start the service.

Sources: [docs/one-pager.md:52-56](), [docs/one-pager.md:26]()

## Related Documentation

For more detailed information about the Access Control Service, refer to the following documentation:

- [`docs/permissions.md`](docs/permissions.md): Describes the structure and definitions of roles and permissions.
- [`docs/api.md`](docs/api.md): Provides a complete contract for the RESTful API.

Sources: [docs/one-pager.md:59-61]()