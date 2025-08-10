# Personal Notes on RBAC

## Key Concepts
- **Role-Based Access Control (RBAC)**: A method of regulating access to computer or network resources based on the roles of individual users within an enterprise.
- **Roles**: A set of permissions that can be assigned to users or groups. Roles define what actions can be performed on resources.
- **RoleBindings**: Associates a role with a user or a set of users, granting them the permissions defined in the role.
- **ClusterRoles**: Similar to roles, but they apply across the entire cluster rather than within a specific namespace.
- **ClusterRoleBindings**: Binds a ClusterRole to users or groups at the cluster level.

## Definitions
- **Subject**: An entity that can be granted access to resources (e.g., users, groups, or service accounts).
- **Resource**: An object in the Kubernetes API (e.g., pods, services, deployments) that can be accessed or manipulated.
- **Verb**: An action that can be performed on a resource (e.g., get, list, create, delete).

## Important Information
- RBAC is enabled by default in Kubernetes clusters starting from version 1.6.
- The `kubectl` command-line tool is commonly used to manage RBAC configurations.
- Always follow the principle of least privilege when assigning roles to users.

## Additional Notes
- Review the official Kubernetes documentation for the most up-to-date information on RBAC.
- Consider using tools like `kubectl auth can-i` to test permissions for specific users or service accounts.