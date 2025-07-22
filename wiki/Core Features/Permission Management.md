<details>
<summary>Relevant source files</summary>

The following files were used as context for generating this wiki page:

- [config/roles.json](https://github.com/aanickode/access-control-service/blob/main/config/roles.json)
- [docs/permissions.md](https://github.com/aanickode/access-control-service/blob/main/docs/permissions.md)
</details>

# Permission Management

## Introduction

The Permission Management system is a crucial component of the access-control-service, responsible for enforcing role-based access control (RBAC) across the application. It defines a set of roles, each associated with specific permissions, and ensures that users can only access resources and functionalities based on their assigned roles. This system plays a vital role in maintaining data security and controlling access to sensitive operations within the application.

## Role Definition

The roles and their associated permissions are defined in the `config/roles.json` file. This file serves as the central configuration for the RBAC system, allowing administrators to easily manage and update roles and permissions.

```json
{
  "admin": ["view_users", "create_role", "view_permissions"],
  "engineer": ["view_users", "view_permissions"],
  "analyst": ["view_users"]
}
```

Each key in the JSON object represents a role, and the corresponding value is an array of permissions granted to that role.

Sources: [config/roles.json](https://github.com/aanickode/access-control-service/blob/main/config/roles.json)

## Default Roles

The system comes preconfigured with three default roles:

### admin

- Permissions: `view_users`, `create_role`, `view_permissions`
- Description: Full system access, intended for platform and DevOps teams.

### engineer

- Permissions: `view_users`, `view_permissions`
- Description: Read-only access to users and permissions, used for observability and debugging.

### analyst

- Permissions: `view_users`
- Description: Basic read-only access, intended for data/reporting use cases.

Sources: [docs/permissions.md](https://github.com/aanickode/access-control-service/blob/main/docs/permissions.md)

## Permission Enforcement

The access-control-service enforces permissions on a per-route basis. Each route defines the permission required to access it, and these permissions are checked at runtime against the user's assigned role.

```mermaid
sequenceDiagram
    participant Client
    participant AccessControlService
    participant UserDB

    Client->>AccessControlService: Request with x-user-email header
    AccessControlService->>UserDB: Check user role
    UserDB-->>AccessControlService: User role
    AccessControlService->>AccessControlService: Check if role has required permission
    alt Role has permission
        AccessControlService-->>Client: Allowed
    else Role does not have permission
        AccessControlService-->>Client: Denied
    end
```

For a request to be considered valid, it must:

1. Include the `x-user-email` header
2. Match a known user in the in-memory `db.users` map
3. Have a role that includes the required permission for the requested route

Sources: [docs/permissions.md](https://github.com/aanickode/access-control-service/blob/main/docs/permissions.md)

## Adding a New Role

To add a new role to the system, follow these steps:

1. Edit the `config/roles.json` file to define the new role and its associated permissions:

```json
{
  "support": ["view_users"]
}
```

2. Assign the new role to a user using the provided CLI tool:

```bash
node cli/manage.js assign-role support@company.com support
```

3. Ensure that consuming services request the appropriate permissions when making requests to the access-control-service.

Sources: [docs/permissions.md](https://github.com/aanickode/access-control-service/blob/main/docs/permissions.md)

## Limitations and Future Enhancements

### Current Limitations

- All permission checks are flat; no wildcarding or nesting is supported.
- All user-role mappings are stored in-memory, which may not be suitable for large-scale deployments.
- Changes to the `roles.json` configuration file require a service restart to take effect.

### Planned Enhancements

The following enhancements are planned for the Permission Management system:

- Scoped permissions (e.g., `project:view:marketing`) to provide more granular access control.
- Integration with Single Sign-On (SSO) group claims to simplify user-role mapping and management.
- Audit logging for role changes and access attempts to improve security and auditing capabilities.

Sources: [docs/permissions.md](https://github.com/aanickode/access-control-service/blob/main/docs/permissions.md)

## Conclusion

The Permission Management system is a critical component of the access-control-service, responsible for enforcing role-based access control and ensuring data security. By defining roles and associated permissions, and enforcing these permissions at runtime, the system ensures that users can only access resources and functionalities based on their assigned roles. While the current implementation has some limitations, the planned enhancements will further improve the system's scalability, granularity, and security.