<details>
<summary>Relevant source files</summary>

The following files were used as context for generating this wiki page:

- [README.md](https://github.com/aanickode/access-control-service/blob/main/README.md)
- [docs/one-pager.md](https://github.com/aanickode/access-control-service/blob/main/docs/one-pager.md)
</details>

# Introduction

The Access Control Service is an internal Role-Based Access Control (RBAC) microservice that provides centralized permission enforcement for internal tools, APIs, and services within the organization. It manages user-role assignments, role-permission mappings, and enforces access controls at runtime, ensuring consistent and auditable permission enforcement across various systems.

The service aims to eliminate hardcoded permission logic across internal systems by decoupling role logic from application code, promoting code reusability and maintainability. It follows a flat RBAC model without hierarchies or scopes, allowing for declarative role-to-permission mappings defined in a JSON configuration file.

## Features

- Flat RBAC model (no scopes or hierarchies)
- Declarative role-to-permission mappings (via JSON config)
- Middleware-based permission enforcement
- CLI tools for bootstrapping and role assignment
- REST API for role and user management

Sources: [docs/one-pager.md](https://github.com/aanickode/access-control-service/blob/main/docs/one-pager.md)

## Architecture Overview

The Access Control Service follows a straightforward architecture, where incoming requests to the `/api/*` endpoints are intercepted by an `authMiddleware` component. This middleware resolves the user's role based on the provided identity (`x-user-email` HTTP header) and retrieves the associated permissions from the `config/roles.json` configuration file. The requested route is then checked against the user's permissions, and access is either allowed or denied accordingly.

```mermaid
graph TD
    subgraph Access Control Service
        Request(Incoming Request) -->|/api/*| authMiddleware[Auth Middleware]
        authMiddleware -->|x-user-email| userRole[Resolve User Role]
        userRole -->|role| permissionsConfig[Load Permissions Config]
        permissionsConfig -->|permissions[]| routeCheck[Check Route Permissions]
        routeCheck -->|allow/deny| Response(Response)
    end
```

Sources: [docs/one-pager.md](https://github.com/aanickode/access-control-service/blob/main/docs/one-pager.md)

## Setup and Usage

The Access Control Service can be set up by following these steps:

1. Copy the `.env.example` file to `.env` and configure any necessary environment variables.
2. Install the required dependencies using `npm install`.
3. Start the service with `npm run start`.

The service also provides a command-line interface (CLI) for assigning roles to users. For example, to assign the `engineer` role to the user `alice@company.com`, run:

```bash
node cli/manage.js assign-role alice@company.com engineer
```

Sources: [docs/one-pager.md](https://github.com/aanickode/access-control-service/blob/main/docs/one-pager.md)

## API Overview

The Access Control Service exposes a RESTful API for managing users, roles, and permissions. The following table summarizes the available endpoints and their required permissions:

| Method | Endpoint         | Description                   | Permission         |
|--------|------------------|-------------------------------|--------------------|
| GET    | /api/users       | List all users and roles      | `view_users`       |
| POST   | /api/roles       | Create a new role             | `create_role`      |
| GET    | /api/permissions | View all role definitions     | `view_permissions` |
| POST   | /api/tokens      | Assign user to a role         | *None (bootstrap)* |

All API requests must include the `x-user-email` header to identify the authenticated user.

Sources: [docs/one-pager.md](https://github.com/aanickode/access-control-service/blob/main/docs/one-pager.md)

## Deployment Considerations

The Access Control Service is designed to be stateless, with no persistent database. Instead, the configuration is loaded into memory during runtime. This makes it suitable for internal-only usage behind an API gateway.

For persistent configuration storage, the service can be integrated with an external configuration store like etcd or Consul.

Sources: [docs/one-pager.md](https://github.com/aanickode/access-control-service/blob/main/docs/one-pager.md)

## Related Documentation

- [`docs/permissions.md`](docs/permissions.md): Role definitions and structure
- [`docs/api.md`](docs/api.md): Complete API contract

Sources: [docs/one-pager.md](https://github.com/aanickode/access-control-service/blob/main/docs/one-pager.md)

In summary, the Access Control Service provides a centralized and consistent approach to managing permissions and enforcing access controls across internal systems. By decoupling role logic from application code and offering a declarative configuration model, it promotes code reusability, maintainability, and auditable permission enforcement.