<details>
<summary>Relevant source files</summary>

The following files were used as context for generating this wiki page:

- [README.md](https://github.com/aanickode/access-control-service/blob/main/README.md)
- [docs/one-pager.md](https://github.com/aanickode/access-control-service/blob/main/docs/one-pager.md)
</details>

# Introduction

The Access Control Service is an internal Role-Based Access Control (RBAC) microservice that provides centralized permission enforcement for internal tools, APIs, and services within the organization. It manages user-role assignments, role-permission mappings, and enforces access controls at runtime, ensuring consistent and auditable permission enforcement across various systems.

The service eliminates the need for hardcoded permission logic in individual applications by decoupling role logic from application code. It follows a flat RBAC model without hierarchies or scopes, and utilizes declarative role-to-permission mappings defined in a JSON configuration file.

## Architecture Overview

The Access Control Service follows a middleware-based architecture for permission enforcement. The high-level flow is as follows:

```mermaid
graph TD
    Request[Request /api/*] -->|x-user-email| authMiddleware[Auth Middleware]
    authMiddleware -->|user email| resolveRole[Resolve Role]
    resolveRole -->|role| loadPermissions[Load Permissions]
    loadPermissions -->|permissions[]| enforceAccess[Enforce Access]
    enforceAccess -->|allow/deny| Response[Response]
```

1. Incoming requests to the `/api/*` routes include the `x-user-email` HTTP header for user identification.
2. The authentication middleware resolves the user's role from the `db.users` map.
3. The user's role permissions are loaded from the `config/roles.json` configuration file.
4. Routes are annotated with required permissions, and access is granted or denied based on the user's role permissions.

Sources: [docs/one-pager.md:17-23](https://github.com/aanickode/access-control-service/blob/main/docs/one-pager.md#L17-L23)

## Service Setup and Usage

The Access Control Service is a Node.js application that can be set up by following these steps:

1. Copy the `.env.example` file to `.env` and configure any necessary environment variables.
2. Install the required dependencies using `npm install`.
3. Start the service with `npm run start`.

Sources: [docs/one-pager.md:27-31](https://github.com/aanickode/access-control-service/blob/main/docs/one-pager.md#L27-L31)

The service provides a command-line interface (CLI) for managing user-role assignments. For example, to assign the `engineer` role to the user `alice@company.com`, run:

```bash
node cli/manage.js assign-role alice@company.com engineer
```

Sources: [docs/one-pager.md:34-36](https://github.com/aanickode/access-control-service/blob/main/docs/one-pager.md#L34-L36)

## API Overview

The Access Control Service exposes a RESTful API for managing users, roles, and permissions. The following table summarizes the available endpoints and their required permissions:

| Method | Endpoint         | Description                   | Permission         |
|--------|------------------|-------------------------------|--------------------|
| GET    | /api/users       | List all users and roles      | `view_users`       |
| POST   | /api/roles       | Create a new role             | `create_role`      |
| GET    | /api/permissions | View all role definitions     | `view_permissions` |
| POST   | /api/tokens      | Assign user to a role         | *None (bootstrap)* |

All API requests must include the `x-user-email` HTTP header for user identification.

Sources: [docs/one-pager.md:39-48](https://github.com/aanickode/access-control-service/blob/main/docs/one-pager.md#L39-L48)

## Deployment Considerations

The Access Control Service is designed to be stateless, with no persistent database. The configuration is stored in memory, making it suitable for internal-only usage behind an API gateway. For persistent configuration storage, the service can be integrated with an external configuration store like etcd or Consul.

Sources: [docs/one-pager.md:51-53](https://github.com/aanickode/access-control-service/blob/main/docs/one-pager.md#L51-L53)

## Related Documentation

- [`docs/permissions.md`](docs/permissions.md): Detailed documentation on role definitions and structure.
- [`docs/api.md`](docs/api.md): Complete API contract and endpoint specifications.

Sources: [docs/one-pager.md:56-58](https://github.com/aanickode/access-control-service/blob/main/docs/one-pager.md#L56-L58)