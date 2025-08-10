# RBAC in Kubernetes - CKA Study Guide

## Overview
Role-Based Access Control (RBAC) is a critical component of security in Kubernetes. It allows administrators to define roles and permissions for users and applications, ensuring that only authorized entities can perform specific actions within the cluster.

## Key Concepts
- **Role**: Defines a set of permissions within a namespace.
- **ClusterRole**: Similar to a Role, but applies to the entire cluster.
- **RoleBinding**: Grants the permissions defined in a Role to a user or group of users within a specific namespace.
- **ClusterRoleBinding**: Grants the permissions defined in a ClusterRole to users across the entire cluster.

## Examples
### Create a Role
To create a Role that allows reading pods in a specific namespace, you can use the following YAML configuration:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: your-namespace
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list", "watch"]
```

### Bind a Role
To bind the above Role to a user, you can create a RoleBinding:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods
  namespace: your-namespace
subjects:
- kind: User
  name: your-username
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

## Best Practices
- **Principle of Least Privilege**: Always grant the minimum permissions necessary for users to perform their tasks.
- **Regular Audits**: Periodically review roles and bindings to ensure they remain relevant and secure.
- **Use Namespaces**: Use namespaces to isolate resources and permissions between different teams or projects.

## Additional Resources
For more detailed information about RBAC, refer to the official Kubernetes documentation and the RBAC section in your study materials.