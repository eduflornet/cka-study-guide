# Cluster Roles

Real difficulties can be overcome; it is only the imaginary ones that are unconquerable.

– Theodore N. Vail

1. For the first few questions of this lab, you would have to inspect the existing ClusterRoles and ClusterRoleBindings that have been created in this cluster.

Run the command: 

```bash
kubectl get clusterroles --no-headers | wc -l
74

kubectl get clusterroles -o json | jq '.items | length'
74
```

2. How many ClusterRoleBindings exist on the cluster?

```bash
kubectl get clusterrolebindings --no-headers | wc -l
59
```

3. What namespace is the cluster-admin clusterrole part of?

**ClusterRole** is a non-namespaced resource. You can check via the kubectl api-resources --namespaced=false command. So the correct answer would be Cluster Roles are cluster wide and not part of any namespace.

Este comando lista todos los recursos que no dependen de un namespace, como nodes, persistentvolumes, clusterroles, etc.

✅ Por eso, la afirmación “Cluster Roles are cluster wide and not part of any namespace” es correcta: los ClusterRole permiten definir permisos que se aplican en todo el clúster, y no están limitados a un espacio de nombres.

4. What user/groups are the cluster-admin role bound to?
The ClusterRoleBinding for the role is with the same name.

Run the command: kubectl describe clusterrolebinding cluster-admin





