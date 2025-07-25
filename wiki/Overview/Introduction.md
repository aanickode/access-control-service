<details>
<summary>Relevant source files</summary>

The following files were used as context for generating this wiki page:

- [README.md](https://github.com/aanickode/access-control-service/blob/main/README.md)
- [docs/one-pager.md](https://github.com/aanickode/access-control-service/blob/main/docs/one-pager.md)
</details>

# Introduction

The Access Control Service is an internal Role-Based Access Control (RBAC) microservice that provides centralized permission enforcement for internal tools, APIs, and services within the organization. Its primary purpose is to manage user-role assignments, role-permission mappings, and enforce access controls at runtime, ensuring consistent and auditable permission enforcement across various systems.

By centralizing access control decisions, this service eliminates the need for hardcoded permission logic within individual applications, promoting a decoupled and maintainable approach to authorization. It serves as a single source of truth for managing roles, permissions, and their associations, simplifying the overall access control management process.

## Features

The Access Control Service offers the following key features:

- **Flat RBAC Model**: It implements a flat Role-Based Access Control model without hierarchies or scopes, providing a straightforward and easy-to-understand approach to permission management.
- **Declarative Role-to-Permission Mappings**: Role-permission mappings are defined declaratively using a JSON configuration file, allowing for easy maintenance and updates.
- **Middleware-based Permission Enforcement**: The service provides a middleware component that can be integrated into various applications, enabling runtime permission enforcement based on the user's assigned role.
- **CLI Tools**: Command-line interface (CLI) tools are available for bootstrapping the service and assigning roles to users.
- **REST API**: A RESTful API is provided for managing roles, users, and permissions, enabling integration with other systems and automation.

## Architecture Overview

The Access Control Service follows a simple architecture, as illustrated by the following diagram:

```mermaid
graph TD
    subgraph Access Control Service
        API[/api/* Endpoints] -->|x-user-email| AuthMiddleware[Auth Middleware]
        AuthMiddleware -->|role| RoleResolver[Role Resolver]
        RoleResolver -->|permissions[]| PermissionEnforcer[Permission Enforcer]
        PermissionEnforcer -->|allow/deny| API
    end
    Client[Client Application] -->|Request| API
```

The key components of the architecture are:

1. **Client Application**: The application or service that requires access control enforcement sends requests to the Access Control Service's API endpoints.
2. **API Endpoints (`/api/*`)**: The service exposes a set of RESTful API endpoints for managing users, roles, and permissions.
3. **Authentication Middleware**: This middleware component extracts the user's identity from the `x-user-email` HTTP header included in the request.
4. **Role Resolver**: Based on the user's identity, the Role Resolver retrieves the user's assigned role from the `db.users` mapping.
5. **Permission Enforcer**: The Permission Enforcer loads the permissions associated with the user's role from the `config/roles.json` configuration file and determines whether the requested operation should be allowed or denied based on the required permissions.

Sources: [docs/one-pager.md:19-23](https://github.com/aanickode/access-control-service/blob/main/docs/one-pager.md#L19-L23)

## Role and Permission Management

The Access Control Service provides a declarative approach to managing roles and their associated permissions through a JSON configuration file (`config/roles.json`). This file defines the mapping between roles and the permissions granted to each role.

```json
{
  "admin": ["view_users", "create_role", "view_permissions"],
  "engineer": ["view_permissions"],
  "guest": []
}
```

In the example above, the `admin` role has permissions to view users, create new roles, and view role definitions, while the `engineer` role can only view role definitions, and the `guest` role has no permissions assigned.

Sources: [docs/one-pager.md:8-9](https://github.com/aanickode/access-control-service/blob/main/docs/one-pager.md#L8-L9)

## User Management

The Access Control Service maintains a mapping of users to their assigned roles in the `db.users` data structure. This mapping is likely an in-memory data store or configuration file, as the service is designed to be stateless.

Users can be assigned roles using the provided CLI tool, as shown in the following example:

```bash
node cli/manage.js assign-role alice@company.com engineer
```

This command assigns the `engineer` role to the user with the email `alice@company.com`.

Sources: [docs/one-pager.md:14-15](https://github.com/aanickode/access-control-service/blob/main/docs/one-pager.md#L14-L15)

## API Overview

The Access Control Service exposes a RESTful API for managing users, roles, and permissions. The following table summarizes the available API endpoints and their corresponding permissions:

| Method | Endpoint         | Description                   | Permission         |
|--------|------------------|-------------------------------|--------------------|
| GET    | /api/users       | List all users and roles      | `view_users`       |
| POST   | /api/roles       | Create a new role             | `create_role`      |
| GET    | /api/permissions | View all role definitions     | `view_permissions` |
| POST   | /api/tokens      | Assign user to a role         | *None (bootstrap)* |

All API requests must include the `x-user-email` header with the user's email address for authentication purposes.

Sources: [docs/one-pager.md:25-34](https://github.com/aanickode/access-control-service/blob/main/docs/one-pager.md#L25-L34)

## Deployment and Integration

The Access Control Service is designed to be stateless, making it suitable for internal usage behind an API gateway. However, for persistent storage of configuration and user-role mappings, the service can be integrated with an external configuration store like etcd or Consul.

To integrate the Access Control Service with other applications, the provided middleware component can be incorporated into the application's request pipeline. This middleware will handle the authentication and authorization process, ensuring that only authorized requests are processed.

Sources: [docs/one-pager.md:37-40](https://github.com/aanickode/access-control-service/blob/main/docs/one-pager.md#L37-L40)

## Conclusion

The Access Control Service provides a centralized and consistent approach to managing access control and permissions within the organization. By decoupling permission logic from individual applications and providing a declarative role-permission mapping, it simplifies the management and enforcement of access controls across various internal tools, APIs, and services. The service's middleware-based architecture, RESTful API, and CLI tools facilitate seamless integration and management of roles, permissions, and user assignments.