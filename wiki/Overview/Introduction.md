<details>
<summary>Relevant source files</summary>

The following files were used as context for generating this wiki page:

- [README.md](https://github.com/aanickode/access-control-service/blob/main/README.md)
- [docs/one-pager.md](https://github.com/aanickode/access-control-service/blob/main/docs/one-pager.md)
</details>

# Introduction

The Access Control Service is an internal Role-Based Access Control (RBAC) microservice that provides centralized permission enforcement for internal tools, APIs, and services within a company or organization. Its primary purpose is to eliminate hardcoded permission logic across various internal systems by centralizing access control decisions, ensuring consistent and auditable permission enforcement, and decoupling role logic from application code.

Sources: [docs/one-pager.md:3-5](), [docs/one-pager.md:9-11]()

## Features

The Access Control Service offers the following key features:

- **Flat RBAC Model**: It implements a flat Role-Based Access Control model without support for scopes or hierarchies.
- **Declarative Role-to-Permission Mappings**: Role-to-permission mappings are defined declaratively in a JSON configuration file.
- **Middleware-based Permission Enforcement**: Permission enforcement is implemented as middleware, allowing seamless integration with other services and APIs.
- **CLI Tools**: Command-line interface (CLI) tools are provided for bootstrapping and assigning roles to users.
- **REST API**: A RESTful API is available for managing roles and users.

Sources: [docs/one-pager.md:13-18]()

## Architecture Overview

The Access Control Service follows a simple architecture for handling requests and enforcing permissions:

```mermaid
graph TD
    Request --> API[/api/*]
    API --> AuthMiddleware[authMiddleware]
    AuthMiddleware --> UserRole[role]
    UserRole --> PermissionsArray[permissions[]]
    PermissionsArray --> AllowDeny[allow/deny]
```

1. Requests are made to the `/api/*` endpoints.
2. The `authMiddleware` component is responsible for authenticating the user based on the `x-user-email` HTTP header.
3. The user's role is resolved by looking up the user in the `db.users` map.
4. The permissions associated with the user's role are loaded from the `config/roles.json` configuration file.
5. The requested route is checked against the user's permissions, and access is either allowed or denied.

Sources: [docs/one-pager.md:22-27]()

## Setup and CLI Usage

To set up the Access Control Service, follow these steps:

```bash
cp .env.example .env
npm install
npm run start
```

The CLI tool `manage.js` can be used to assign roles to users:

```bash
node cli/manage.js assign-role alice@company.com engineer
```

Sources: [docs/one-pager.md:30-34](), [docs/one-pager.md:37-38]()

## API Overview

The Access Control Service provides a RESTful API for managing users, roles, and permissions. The following table summarizes the available endpoints and their required permissions:

| Method | Endpoint         | Description                   | Permission         |
|--------|------------------|-------------------------------|--------------------|
| GET    | /api/users       | List all users and roles      | `view_users`       |
| POST   | /api/roles       | Create a new role             | `create_role`      |
| GET    | /api/permissions | View all role definitions     | `view_permissions` |
| POST   | /api/tokens      | Assign user to a role         | *None (bootstrap)* |

All API requests must include the `x-user-email` HTTP header to identify the user.

Sources: [docs/one-pager.md:41-48]()

## Deployment Considerations

The Access Control Service is designed to be stateless, with no persistent database. Instead, the configuration is stored in memory. This makes it suitable for internal-only usage behind an API gateway. However, for persistent configuration storage, the service can be integrated with an external configuration store like etcd or Consul.

Sources: [docs/one-pager.md:51-53]()

## Related Documentation

- [`docs/permissions.md`](docs/permissions.md): Provides details on the role definitions and structure used by the Access Control Service.
- [`docs/api.md`](docs/api.md): Describes the complete API contract for the Access Control Service.

Sources: [docs/one-pager.md:56-57]()