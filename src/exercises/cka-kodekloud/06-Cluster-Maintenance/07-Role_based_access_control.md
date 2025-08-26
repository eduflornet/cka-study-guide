# Role Based Access Controls

The things always happens that you really believe in; and the belief in a thing makes it happen.

– Frank Lloyd Wright

1. Inspect the environment and identify the authorization modes configured on the cluster.

Check the kube-apiserver settings.

**Node,RBAC**

Use the command kubectl describe pod kube-apiserver-controlplane -n kube-system and look for --authorization-mode.

```bash
k -n kube-system describe pod kube-apiserver-controlplane | grep -i "authorization-mode"
      --authorization-mode=Node,RBAC
``` 

2. How many roles exist in the default namespace?

**Cero**

```bash 
k -n default get all
NAME                       READY   STATUS    RESTARTS   AGE
pod/red-755c7b5c4f-6rxq5   1/1     Running   0          7m25s
pod/red-755c7b5c4f-nw8ws   1/1     Running   0          7m25s

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   172.20.0.1   <none>        443/TCP   13m

NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/red   2/2     2            2           7m25s

NAME                             DESIRED   CURRENT   READY   AGE
replicaset.apps/red-755c7b5c4f   2         2         2       7m25s
```

3. How many roles exist in all namespaces together?

**There are 12**

```bash
k get roles -A
NAMESPACE     NAME                                             CREATED AT
blue          developer                                        2025-08-26T09:57:43Z
kube-public   kubeadm:bootstrap-signer-clusterinfo             2025-08-26T09:51:46Z
kube-public   system:controller:bootstrap-signer               2025-08-26T09:51:45Z
kube-system   extension-apiserver-authentication-reader        2025-08-26T09:51:45Z
kube-system   kube-proxy                                       2025-08-26T09:51:47Z
kube-system   kubeadm:kubelet-config                           2025-08-26T09:51:46Z
kube-system   kubeadm:nodes-kubeadm-config                     2025-08-26T09:51:46Z
kube-system   system::leader-locking-kube-controller-manager   2025-08-26T09:51:45Z
kube-system   system::leader-locking-kube-scheduler            2025-08-26T09:51:45Z
kube-system   system:controller:bootstrap-signer               2025-08-26T09:51:45Z
kube-system   system:controller:cloud-provider                 2025-08-26T09:51:45Z
kube-system   system:controller:token-cleaner                  2025-08-26T09:51:45Z

```

4. What are the resources the kube-proxy role in the kube-system namespace is given access to?

**configmaps**

```bash
k -n kube-system describe role kube-proxy 
Name:         kube-proxy
Labels:       <none>
Annotations:  <none>
PolicyRule:
  Resources   Non-Resource URLs  Resource Names  Verbs
  ---------   -----------------  --------------  -----
  configmaps  []                 [kube-proxy]    [get]
```

5. What actions can the kube-proxy role perform on configmaps?

**get**

6. Which of the following statements are true?

puede obtener los detalles de configmaps unicamente como objeto kube-proxy

7. Which account is the kube-proxy role assigned to?

**system:bootstrappers:kubeadm:default-node-token**


Run the command: ``` kubectl describe rolebinding kube-proxy -n kube-system ```

```bash
k -n kube-system describe rolebinding kube-proxy
Name:         kube-proxy
Labels:       <none>
Annotations:  <none>
Role:
  Kind:  Role
  Name:  kube-proxy
Subjects:
  Kind   Name                                             Namespace
  ----   ----                                             ---------
  Group  system:bootstrappers:kubeadm:default-node-token 
```

8. A user dev-user is created. User's details have been added to the kubeconfig file. Inspect the permissions granted to the user. Check if the user can list pods in the default namespace.

Use the --as dev-user option with kubectl to run commands as the dev-user.

**dev-user** does not have permissions to list pods

Run the command: kubectl get pods --as dev-user

```bash
k get pods --as dev-user
Error from server (Forbidden): pods is forbidden: User "dev-user" cannot list resource "pods" in API group "" in the namespace "default"
```

9. Create the necessary roles and role bindings required for the dev-user to create, list and delete pods in the default namespace.

Use the given spec:

- Role: developer

- Role Resources: pods

- Role Actions: list

- Role Actions: create

- Role Actions: delete

- RoleBinding: dev-user-binding

- RoleBinding: Bound to dev-user

Use the command kubectl create to create a role developer and rolebinding dev-user-binding in the default namespace.

To create a Role:- 

```bash
k -n default create role developer --verb=list,create,delete --resource=pods
```

To create a RoleBinding:- 

```bash
k -n default create rolebinding dev-user-binding --role=developer --user=dev-user
```

**Solution manifest file** to create a role and rolebinding in the default namespace:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  namespace: default
  name: developer
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["list", "create","delete"]

---
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: dev-user-binding
subjects:
- kind: User
  name: dev-user
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: developer
  apiGroup: rbac.authorization.k8s.io
```

10. A set of new roles and role-bindings are created in the blue namespace for the dev-user. However, the dev-user is unable to get details of the dark-blue-app pod in the blue namespace. Investigate and fix the issue.

We have created the required roles and rolebindings, but something seems to be wrong

New roles and role bindings are created in the blue namespace.
Check out the resourceNames configured on the role.

Run the command: kubectl edit role developer -n blue and correct the resourceNames field. You don't have to delete the role.

```yaml
# role-developer.yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  creationTimestamp: "2025-08-26T09:57:43Z"
  name: developer
  namespace: blue
  resourceVersion: "915"
  uid: a7e3a78b-579c-4b15-a5c4-47f88f2ac870
rules:
- apiGroups:
  - ""
  resourceNames:
  - dark-blue-app
  resources:
  - pods
  verbs:
  - get
  - watch
  - create
  - delete
```

```bash
k -n blue apply -f role-developer.yaml 

Warning: resource roles/developer is missing the kubectl.kubernetes.io/last-applied-configuration annotation which is required by kubectl apply. kubectl apply should only be used on resources created declaratively by either kubectl create --save-config or kubectl apply. The missing annotation will be patched automatically.
role.rbac.authorization.k8s.io/developer configured
```

11. Add a new rule in the existing role developer to grant the dev-user permissions to create deployments in the blue namespace.

Remember to add api group "apps".

permissions added to create deployments?

Use the command ``` kubectl edit ``` to add a new rule for user **dev-user** to grant permissions to create deployments in the **blue** namespace.

Edit the **developer** role in the **blue** namespace to add a new rule under the rules section.

Append the below rule to the end of the file

``` kubectl edit role developer -n blue ```

```yaml
- apiGroups:
  - apps
  resources:
  - deployments
  verbs:
  - create
```

So it looks like this:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: developer
  namespace: blue
rules:
- apiGroups:
  - apps
  resourceNames:
  - dark-blue-app
  resources:
  - pods
  verbs:
  - get
  - watch
  - create
  - delete
- apiGroups:
  - apps
  resources:
  - deployments
  verbs:
  - create
```
