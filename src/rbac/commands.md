# RBAC Commands

This document contains a collection of essential commands related to Role-Based Access Control (RBAC) in Kubernetes. These commands are fundamental for effectively managing roles and permissions.

## Common RBAC Commands

### 1. Create a Role
To create a role within a specific namespace, use the following command:
```bash
kubectl create role <role-name> --verb=<verb> --resource=<resource> --namespace=<namespace>
```
Replace `<role-name>`, `<verb>`, `<resource>`, and `<namespace>` with the appropriate values.

### 2. Create a ClusterRole
To create a cluster-wide role, use:
```bash
kubectl create clusterrole <clusterrole-name> --verb=<verb> --resource=<resource>
```
This command allows you to define permissions that apply to all namespaces.

### 3. Bind a Role to a User
To bind a role to a user within a namespace, use:
```bash
kubectl create rolebinding <rolebinding-name> --role=<role-name> --user=<username> --namespace=<namespace>
```
This command associates a specific role with a user in the designated namespace.

### 4. Bind a ClusterRole to a User
To bind a cluster role to a user, use:
```bash
kubectl create clusterrolebinding <clusterrolebinding-name> --clusterrole=<clusterrole-name> --user=<username>
```
This command grants cluster-wide permissions to the specified user.

### 5. View Roles
To list all roles in a specific namespace, use:
```bash
kubectl get roles --namespace=<namespace>
```

### 6. View ClusterRoles
To list all cluster roles, use:
```bash
kubectl get clusterroles
```

### 7. Delete a Role
To delete a specific role, use:
```bash
kubectl delete role <role-name> --namespace=<namespace>
```

### 8. Delete a ClusterRole
To delete a cluster role, use:
```bash
kubectl delete clusterrole <clusterrole-name>
```

### 9. View Role Bindings
To list all role bindings in a specific namespace, use:
```bash
kubectl get rolebindings --namespace=<namespace>
```

### 10. View ClusterRole Bindings

To list all cluster role bindings, use:

```bash
kubectl get clusterrolebindings
```

## Additional Resources
For more detailed information about RBAC commands and their usage, refer to the official Kubernetes documentation on RBAC.