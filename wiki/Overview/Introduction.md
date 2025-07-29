<details>
<summary>Relevant source files</summary>

The following files were used as context for generating this wiki page:

- [README.md](https://github.com/aanickode/access-control-service/blob/main/README.md)
- [docs/one-pager.md](https://github.com/aanickode/access-control-service/blob/main/docs/one-pager.md)
</details>

# Introduction

The Access Control Service is an internal Role-Based Access Control (RBAC) microservice that provides centralized permission enforcement for internal tools, APIs, and services within the organization. Its primary purpose is to eliminate hardcoded permission logic across various systems by centralizing access control decisions, ensuring consistent and auditable permission enforcement, and decoupling role logic from application code.

Sources: [docs/one-pager.md:3-5](), [docs/one-pager.md:10-12]()

## Features

The Access Control Service offers the following key features:

- **Flat RBAC Model**: It implements a flat RBAC model without hierarchies or scopes, simplifying role and permission management.
- **Declarative Role-Permission Mappings**: Role-to-permission mappings are defined declaratively in a JSON configuration file, allowing for easy maintenance and updates.
- **Middleware-based Permission Enforcement**: The service provides middleware components for enforcing permissions at runtime, seamlessly integrating with existing applications and APIs.
- **CLI Tools**: Command-line interface (CLI) tools are available for bootstrapping the service and assigning roles to users.
- **REST API**: A RESTful API is provided for managing roles, users, and permissions, enabling integration with other systems and automation.

Sources: [docs/one-pager.md:14-19](), [docs/one-pager.md:24-28]()

## Request Flow

The Access Control Service follows a specific request flow for permission enforcement:

```mermaid
graph TD
    A[Request] -->|/api/*| B[authMiddleware]
    B --> C[Role Resolution]
    C --> D[Load Permissions]
    D -->|permissions[]| E[Allow/Deny]
```

1. Incoming requests to the `/api/*` endpoints are intercepted by the `authMiddleware`.
2. The user's role is resolved based on the `x-user-email` HTTP header and the `db.users` mapping.
3. The permissions associated with the user's role are loaded from the `config/roles.json` configuration file.
4. The required permissions for the requested route are checked against the user's permissions.
5. Based on the permission evaluation, the request is either allowed or denied.

Sources: [docs/one-pager.md:21-23]()

## Role and Permission Management

### Role Definitions

The role-to-permission mappings are defined in a JSON configuration file (`config/roles.json`). This file specifies the available roles and their associated permissions.

```json
{
  "engineer": ["create_repo", "merge_pr"],
  "manager": ["view_users", "create_role", "view_permissions"],
  "admin": ["*"]
}
```

In the example above, the `engineer` role has permissions to create repositories and merge pull requests, the `manager` role can view users, create roles, and view permissions, and the `admin` role has unrestricted access (`*`).

Sources: [docs/one-pager.md:15-16](), [docs/one-pager.md:24-28]()

### User-Role Assignments

User-role assignments are managed through the CLI tool (`cli/manage.js`). The following command assigns the `engineer` role to the user with the email `alice@company.com`:

```bash
node cli/manage.js assign-role alice@company.com engineer
```

The user-role mappings are stored in the `db.users` data structure, which is likely an in-memory data store or cache.

Sources: [docs/one-pager.md:26-27]()

## API Overview

The Access Control Service exposes a RESTful API for managing users, roles, and permissions. The following table summarizes the available endpoints and their required permissions:

| Method | Endpoint         | Description                   | Permission         |
|--------|------------------|-------------------------------|--------------------|
| GET    | /api/users       | List all users and roles      | `view_users`       |
| POST   | /api/roles       | Create a new role             | `create_role`      |
| GET    | /api/permissions | View all role definitions     | `view_permissions` |
| POST   | /api/tokens      | Assign user to a role         | *None (bootstrap)* |

All API requests must include the `x-user-email` HTTP header to identify the user making the request.

Sources: [docs/one-pager.md:24-28]()

## Deployment and Integration

The Access Control Service is designed to be stateless, with no persistent database. The configuration and user-role mappings are stored in memory, making it suitable for internal-only usage behind an API gateway.

For persistent storage and integration with external systems, the service can be integrated with a distributed configuration store like etcd or Consul.

Sources: [docs/one-pager.md:31-33]()

## Conclusion

The Access Control Service provides a centralized and consistent approach to managing permissions and enforcing access controls across internal tools, APIs, and services. By decoupling permission logic from application code and offering a declarative role-permission mapping system, it simplifies the management of access controls and ensures auditable and consistent enforcement throughout the organization.

Sources: [docs/one-pager.md:3-5](), [docs/one-pager.md:10-12]()