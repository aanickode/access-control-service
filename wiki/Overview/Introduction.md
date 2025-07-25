<details>
<summary>Relevant source files</summary>

The following files were used as context for generating this wiki page:

- [README.md](https://github.com/aanickode/access-control-service/blob/main/README.md)
- [docs/one-pager.md](https://github.com/aanickode/access-control-service/blob/main/docs/one-pager.md)
</details>

# Introduction

The Access Control Service is an internal Role-Based Access Control (RBAC) microservice that provides centralized permission enforcement for internal tools, APIs, and services within the organization. Its primary purpose is to manage user-role assignments, role-permission mappings, and enforce access controls at runtime, eliminating the need for hardcoded permission logic across various internal systems. By centralizing access control decisions, the service ensures consistent, auditable permission enforcement and decouples role logic from application code.

Sources: [docs/one-pager.md:1-6](), [docs/one-pager.md:10-12]()

## Features

The Access Control Service offers the following key features:

- **Flat RBAC Model:** It implements a flat Role-Based Access Control model without support for scopes or hierarchies.
- **Declarative Role-to-Permission Mappings:** Role-permission mappings are defined declaratively in a JSON configuration file.
- **Middleware-based Permission Enforcement:** The service provides middleware components for enforcing permissions at runtime.
- **CLI Tools:** Command-line interface (CLI) tools are available for bootstrapping and assigning roles to users.
- **REST API:** A RESTful API is provided for managing roles and users.

Sources: [docs/one-pager.md:15-20]()

## Architecture Overview

The Access Control Service follows a simple architecture for handling permission enforcement:

```mermaid
graph TD
    A[Request] -->|/api/*| B[authMiddleware]
    B --> C[Role]
    C --> D[Permissions[]]
    D -->|allow/deny| E[Response]
```

1. Incoming requests to the `/api/*` endpoints are intercepted by the `authMiddleware`.
2. The user's identity is extracted from the `x-user-email` HTTP header.
3. The user's role is resolved by looking up the user in the `db.users` map.
4. The permissions associated with the user's role are loaded from the `config/roles.json` configuration file.
5. The requested route is checked against the user's permissions, and access is either allowed or denied.

Sources: [docs/one-pager.md:24-28]()

## User and Role Management

### CLI Tool

The Access Control Service provides a CLI tool (`cli/manage.js`) for assigning roles to users. For example, to assign the `engineer` role to the user `alice@company.com`, the following command can be used:

```bash
node cli/manage.js assign-role alice@company.com engineer
```

Sources: [docs/one-pager.md:32-34]()

### REST API

The service also exposes a RESTful API for managing users and roles:

| Method | Endpoint         | Description                   | Required Permission |
|--------|------------------|-------------------------------|----------------------|
| GET    | `/api/users`     | List all users and roles      | `view_users`        |
| POST   | `/api/roles`     | Create a new role             | `create_role`       |
| GET    | `/api/permissions` | View all role definitions     | `view_permissions`  |
| POST   | `/api/tokens`    | Assign user to a role         | *None (bootstrap)*  |

All API requests must include the `x-user-email` HTTP header to identify the user making the request.

Sources: [docs/one-pager.md:38-44]()

## Deployment and Configuration

The Access Control Service is designed to be stateless and does not rely on a database. Instead, the configuration is stored in memory. This makes it suitable for internal-only usage behind an API gateway. However, for persistent configuration, the service can be integrated with an external configuration store such as etcd or Consul.

Sources: [docs/one-pager.md:48-50]()

## Setup and Installation

To set up and run the Access Control Service locally, follow these steps:

1. Copy the example environment file: `cp .env.example .env`
2. Install dependencies: `npm install`
3. Start the service: `npm run start`

Sources: [docs/one-pager.md:26-29]()

## Related Documentation

- [`docs/permissions.md`](docs/permissions.md): Detailed documentation on role definitions and structure.
- [`docs/api.md`](docs/api.md): Complete API contract and specification.

Sources: [docs/one-pager.md:53-55]()

In summary, the Access Control Service provides a centralized and consistent way to manage user roles and permissions within the organization. By decoupling permission logic from application code and enforcing access controls at runtime, it simplifies the development and maintenance of internal tools, APIs, and services.