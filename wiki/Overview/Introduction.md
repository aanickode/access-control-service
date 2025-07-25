<details>
<summary>Relevant source files</summary>

The following files were used as context for generating this wiki page:

- [README.md](https://github.com/aanickode/access-control-service/blob/main/README.md)
- [docs/one-pager.md](https://github.com/aanickode/access-control-service/blob/main/docs/one-pager.md)
</details>

# Introduction

The Access Control Service is an internal Role-Based Access Control (RBAC) microservice that provides centralized permission enforcement for internal tools, APIs, and services within the organization. It manages user-role assignments, role-permission mappings, and enforces access controls at runtime, ensuring consistent and auditable permission enforcement across various systems.

The service aims to eliminate the need for hardcoded permission logic across internal systems by decoupling role logic from application code. It serves as a central authority for access control decisions, promoting code reusability and maintainability.

## Purpose and Overview

The primary purpose of the Access Control Service is to centralize access control decisions and ensure consistent, auditable permission enforcement across internal systems. It achieves this by providing a flat RBAC model (without scopes or hierarchies) and declarative role-to-permission mappings defined in a JSON configuration file.

The service acts as a middleware layer, intercepting incoming requests and validating the user's permissions based on their assigned role. It resolves the user's role from an in-memory database (`db.users` map) and loads the corresponding permissions from the `config/roles.json` configuration file. Routes within the internal systems are annotated with the required permissions, and the Access Control Service enforces these permissions at runtime, allowing or denying access accordingly.

Sources: [docs/one-pager.md:1-19](), [README.md:1-3]()

## Architecture and Data Flow

The Access Control Service follows a straightforward architecture and data flow:

```mermaid
graph TD
    Request("Request /api/*") -->|x-user-email| authMiddleware("authMiddleware")
    authMiddleware -->|role| roleResolver("Role Resolver (db.users)")
    roleResolver -->|permissions[]| permissionLoader("Permission Loader (config/roles.json)")
    permissionLoader -->|allow/deny| accessDecision("Access Decision")
```

1. Incoming requests to the `/api/*` endpoints include the `x-user-email` HTTP header, which provides the user's identity.
2. The `authMiddleware` component intercepts the request and extracts the user's email from the `x-user-email` header.
3. The `roleResolver` component resolves the user's role by looking up the user's email in the `db.users` in-memory map.
4. The `permissionLoader` component loads the permissions associated with the user's role from the `config/roles.json` configuration file.
5. Based on the loaded permissions and the required permissions annotated on the requested route, the `accessDecision` component either allows or denies access to the requested resource.

Sources: [docs/one-pager.md:14-16]()

## Role and Permission Management

The Access Control Service provides a CLI tool (`cli/manage.js`) and a REST API for managing roles and user-role assignments.

### CLI Tool

The CLI tool allows administrators to assign roles to users. For example:

```bash
node cli/manage.js assign-role alice@company.com engineer
```

This command assigns the `engineer` role to the user with the email `alice@company.com`.

Sources: [docs/one-pager.md:20-22]()

### REST API

The Access Control Service exposes a REST API for managing users, roles, and permissions. The following table summarizes the available endpoints and their corresponding permissions:

| Method | Endpoint         | Description                   | Permission         |
|--------|------------------|-------------------------------|--------------------|
| GET    | /api/users       | List all users and roles      | `view_users`       |
| POST   | /api/roles       | Create a new role             | `create_role`      |
| GET    | /api/permissions | View all role definitions     | `view_permissions` |
| POST   | /api/tokens      | Assign user to a role         | *None (bootstrap)* |

All API requests must include the `x-user-email` HTTP header to provide the user's identity.

Sources: [docs/one-pager.md:25-33]()

## Deployment and Configuration

The Access Control Service is designed to be stateless, with its configuration stored in memory. This makes it suitable for internal-only usage behind an API gateway. However, for persistence and scalability, the service can be integrated with an external configuration store, such as etcd or Consul.

To set up and run the Access Control Service locally, follow these steps:

```bash
cp .env.example .env
npm install
npm run start
```

Sources: [docs/one-pager.md:36-41]()

## Related Documentation

For more detailed information about the Access Control Service, refer to the following documentation:

- [`docs/permissions.md`](docs/permissions.md): Provides information about the role definitions and structure used by the service.
- [`docs/api.md`](docs/api.md): Describes the complete API contract for the Access Control Service.

Sources: [docs/one-pager.md:44-46]()

In summary, the Access Control Service is a crucial component for managing and enforcing access controls across internal systems within the organization. It provides a centralized and consistent approach to permission management, promoting code reusability and maintainability while ensuring auditable and secure access control decisions.