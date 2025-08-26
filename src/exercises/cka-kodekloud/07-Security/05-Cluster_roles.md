# Cluster Roles

Real difficulties can be overcome; it is only the imaginary ones that are unconquerable.

– Theodore N. Vail

Great things never came from comfort zones.

– Tony Luziaya


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

**ClusterRole is a non-namespaced resource**. You can check via the ``` kubectl api-resources --namespaced=false ``` command. So the correct answer would be Cluster Roles are cluster wide and not part of any namespace.

Este comando lista todos los recursos que no dependen de un namespace, como nodes, persistentvolumes, clusterroles, etc.

✅ Por eso, la afirmación “Cluster Roles are cluster wide and not part of any namespace” es correcta: los ClusterRole permiten definir permisos que se aplican en todo el clúster, y no están limitados a un espacio de nombres.

4. What user/groups are the cluster-admin role bound to?
The ClusterRoleBinding for the role is with the same name.

**system:masters**

Run the command: 

```bash
kubectl describe clusterrolebinding cluster-admin

Name:         cluster-admin
Labels:       kubernetes.io/bootstrapping=rbac-defaults
Annotations:  rbac.authorization.kubernetes.io/autoupdate: true
Role:
  Kind:  ClusterRole
  Name:  cluster-admin
Subjects:
  Kind   Name            Namespace
  ----   ----            ---------
  Group  system:masters  
```

5. What level of permission does the cluster-admin role grant?

Inspect the cluster-admin role's privileges.

**All permissions**

Run the command: 

```bash
kubectl describe clusterrole cluster-admin

Name:         cluster-admin
Labels:       kubernetes.io/bootstrapping=rbac-defaults
Annotations:  rbac.authorization.kubernetes.io/autoupdate: true
PolicyRule:
  Resources  Non-Resource URLs  Resource Names  Verbs
  ---------  -----------------  --------------  -----
  *.*        []                 []              [*]
             [*]                []              [*]
```

7. A new user michelle joined the team. She will be focusing on the nodes in the cluster. Create the required ClusterRoles and ClusterRoleBindings so she gets access to the nodes.

Use the command kubectl create to create a clusterrole and clusterrolebinding for user michelle to grant access to the nodes.
After that test the access using the command kubectl auth can-i list nodes --as michelle.

Solution manifest file to create a clusterrole and clusterrolebinding for michelle user:

---
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: node-admin
rules:
- apiGroups: [""]
  resources: ["nodes"]
  verbs: ["get", "watch", "list", "create", "delete"]

---
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: michelle-binding
subjects:
- kind: User
  name: michelle
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: node-admin
  apiGroup: rbac.authorization.k8s.io


After save into a file, run the command kubectl create -f <file-name>.yaml to create a resources from definition file.











