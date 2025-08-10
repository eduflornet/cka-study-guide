# Exercises for RBAC

This section includes exercises and practice scenarios to enhance your understanding of Role-Based Access Control (RBAC) in Kubernetes. Use these exercises to test your knowledge and apply the concepts you've learned.

## Exercise 1: Role Creation
- **Task**: Create a Role that allows a user to get, list, and watch pods in a specific namespace.
- **Instructions**:
  1. Define a Role YAML manifest.
  2. Apply the manifest using `kubectl apply -f <filename>.yaml`.
  3. Verify the Role was created successfully.

## Exercise 2: Role Binding
- **Task**: Bind the Role created in Exercise 1 to a user.
- **Instructions**:
  1. Create a RoleBinding YAML manifest that associates the Role with a user.
  2. Apply the manifest using `kubectl apply -f <filename>.yaml`.
  3. Test the permissions by attempting to list pods as the user.

## Exercise 3: ClusterRole and ClusterRoleBinding
- **Task**: Create a ClusterRole that allows a user to manage nodes in the cluster.
- **Instructions**:
  1. Define a ClusterRole YAML manifest.
  2. Create a ClusterRoleBinding to bind the ClusterRole to a user.
  3. Apply both manifests and verify the permissions.

## Exercise 4: Testing Permissions
- **Task**: Use the `kubectl auth can-i` command to test permissions.
- **Instructions**:
  1. Check if a user can create deployments in a specific namespace.
  2. Check if a user can delete services in the cluster.
  3. Document the results of your tests.

## Exercise 5: Review and Audit
- **Task**: Review the RBAC configuration in your cluster.
- **Instructions**:
  1. List all Roles and RoleBindings in a specific namespace.
  2. List all ClusterRoles and ClusterRoleBindings in the cluster.
  3. Identify any potential security issues or unnecessary permissions.

## Conclusion
Completing these exercises will help solidify your understanding of RBAC in Kubernetes and prepare you for the CKA exam. Be sure to document your findings and any challenges you encounter during these exercises.