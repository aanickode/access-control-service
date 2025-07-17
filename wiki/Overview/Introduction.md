<details>
<summary>Relevant source files</summary>

The following files were used as context for generating this wiki page:

- [README.md](https://github.com/aanickode/access-control-service/blob/main/README.md)
- [docs/one-pager.md](https://github.com/aanickode/access-control-service/blob/main/docs/one-pager.md)
</details>

# Introduction

The Access Control Service is an internal Role-Based Access Control (RBAC) microservice that provides centralized permission enforcement for internal tools, APIs, and services within a company or organization. Its primary purpose is to manage user-role assignments, role-permission mappings, and enforce access controls at runtime, eliminating the need for hardcoded permission logic across various internal systems. By centralizing access control decisions, the service ensures consistent, auditable permission enforcement and decouples role logic from application code.

Sources: [docs/one-pager.md:1-5](), [docs/one-pager.md:9-11]()

## Features

The Access Control Service offers the following key features:

- **Flat RBAC Model**: It implements a flat Role-Based Access Control model without support for scopes or hierarchies.
- **Declarative Role-Permission Mappings**: Role-to-permission mappings are defined declaratively in a JSON configuration file.
- **Middleware-based Permission Enforcement**: The service provides a middleware component for enforcing permissions at runtime.
- **CLI Tools**: Command-line interface (CLI) tools are available for bootstrapping and assigning roles to users.
- **REST API**: A RESTful API is provided for managing roles and users.

Sources: [docs/one-pager.md:13-18]()

## Architecture Overview

The Access Control Service follows a simple architecture for handling permission enforcement:

```mermaid
graph TD
    A[Request] -->|/api/*| B[authMiddleware]
    B --> C[role]
    C --> D[permissions[]]
    D -->|allow/deny| E[Response]
```

1. Incoming requests to the `/api/*` routes are intercepted by the `authMiddleware`.
2. The user's identity is extracted from the `x-user-email` HTTP header.
3. The user's role is resolved by looking up the `db.users` map.
4. The permissions associated with the user's role are loaded from the `config/roles.json` configuration file.
5. The requested route is checked against the user's permissions, and the request is either allowed or denied.

Sources: [docs/one-pager.md:22-26]()

## User and Role Management

### CLI Tool

The Access Control Service provides a command-line interface (CLI) tool for managing user-role assignments. The tool can be used to assign a role to a user, as shown in the following example:

```bash
node cli/manage.js assign-role alice@company.com engineer
```

This command assigns the `engineer` role to the user with the email `alice@company.com`.

Sources: [docs/one-pager.md:30-32]()

### REST API

In addition to the CLI tool, the Access Control Service exposes a RESTful API for managing users and roles. The following table summarizes the available API endpoints and their respective permissions:

| Method | Endpoint         | Description                   | Permission         |
|--------|------------------|-------------------------------|--------------------|
| GET    | /api/users       | List all users and roles      | `view_users`       |
| POST   | /api/roles       | Create a new role             | `create_role`      |
| GET    | /api/permissions | View all role definitions     | `view_permissions` |
| POST   | /api/tokens      | Assign user to a role         | *None (bootstrap)* |

All API requests must include the `x-user-email` header to identify the user making the request.

Sources: [docs/one-pager.md:35-42]()

## Deployment and Configuration

### Setup and Running

To set up and run the Access Control Service, follow these steps:

1. Copy the example environment file: `cp .env.example .env`
2. Install dependencies: `npm install`
3. Start the service: `npm run start`

Sources: [docs/one-pager.md:27-29]()

### Deployment Notes

- The Access Control Service is designed to be stateless and does not rely on a database. Instead, the configuration is stored in memory.
- The service is suitable for internal-only usage behind an API gateway.
- For persistent configuration storage, the service can be integrated with an external configuration store like etcd or Consul.

Sources: [docs/one-pager.md:45-48]()

## Related Documentation

- [`docs/permissions.md`](docs/permissions.md): Provides detailed information about role definitions and their structure.
- [`docs/api.md`](docs/api.md): Describes the complete API contract for the Access Control Service.

Sources: [docs/one-pager.md:51-52]()

In summary, the Access Control Service is a centralized RBAC microservice that simplifies permission management and enforcement across internal systems within an organization. It offers a flat RBAC model, declarative role-permission mappings, middleware-based enforcement, CLI tools, and a REST API for user and role management.