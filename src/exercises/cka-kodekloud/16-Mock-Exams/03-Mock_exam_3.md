# Mock Exam 3

Whatever we believe about ourselves and our ability comes true for us.

– Susan L. Taylor

1. You are an administrator preparing your environment to deploy a Kubernetes cluster using kubeadm. Adjust the following network parameters on the system to the following values, and make sure your changes persist reboots:

- net.ipv4.ip_forward = 1
- net.bridge.bridge-nf-call-iptables = 1

Solution Steps
Step 1: Check current network parameter values

```sh
sysctl net.ipv4.ip_forward
sysctl net.bridge.bridge-nf-call-iptables
```

Step 2: Set the network parameters temporarily (immediate effect)

```sh
sudo sysctl -w net.ipv4.ip_forward=1
sudo sysctl -w net.bridge.bridge-nf-call-iptables=1
```

Step 3: Make the changes persistent across reboots

Method 1: Create a new sysctl configuration file

```sh
sudo tee /etc/sysctl.d/k8s.conf <<EOF
net.ipv4.ip_forward = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
```

Method 2: Add to the main sysctl configuration file

```sh
echo "net.ipv4.ip_forward = 1" | sudo tee -a /etc/sysctl.conf
echo "net.bridge.bridge-nf-call-iptables = 1" | sudo tee -a /etc/sysctl.conf
```

Step 4: Apply the configuration

```sh
sudo sysctl --system
```

Step 5: Verify the changes are applied

```sh
sysctl net.ipv4.ip_forward
sysctl net.bridge.bridge-nf-call-iptables
```

Expected output

net.ipv4.ip_forward = 1
net.bridge.bridge-nf-call-iptables = 1

Step 6: Test persistence by reloading sysctl configuration

```sh
sudo sysctl -p /etc/sysctl.d/k8s.conf
```

Alternative Method using modprobe (if bridge module is not loaded)

If you get an error about the bridge module not being available:

```sh
# Load the bridge module
sudo modprobe br_netfilter

# Make it persistent
echo 'br_netfilter' | sudo tee /etc/modules-load.d/k8s.conf

# Then apply the sysctl settings
sudo sysctl -w net.bridge.bridge-nf-call-iptables=1
```

Key Points:
- net.ipv4.ip_forward = 1: Enables IP forwarding, which is required for pod-to-pod communication across nodes
- net.bridge.bridge-nf-call-iptables = 1: Ensures that bridged traffic is processed by iptables rules, necessary for proper network policies and service routing
- Persistence: Using /etc/sysctl.d/k8s.conf is the recommended approach for Kubernetes-specific settings
- Module dependency: The br_netfilter kernel module must be loaded for bridge-related sysctl parameters to work
Verification Commands:

```sh
# Check if settings are applied
cat /proc/sys/net/ipv4/ip_forwardcat /proc/sys/net/bridge/bridge-nf-call-iptables 
```

```sh
# Check if configuration file exists
cat /etc/sysctl.d/k8s.conf

# Verify after reboot (optional)sudo reboot# After reboot, check again:sysctl net.ipv4.ip_forward net.bridge.
bridge-nf-call-iptables
```

2. Create a new service account with the name **pvviewer**. Grant this Service account access to list all PersistentVolumes in the cluster by creating an appropriate cluster role called **pvviewer-role** and ClusterRoleBinding called **pvviewer-role-binding**.
Next, create a pod called **pvviewer** with the image: **redis** and serviceAccount: pvviewer in the default namespace.

Solution Steps
Step 1: Create the ServiceAccount

Imperative Method:

```sh
kubectl create serviceaccount pvviewer
```

Declarative Method:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: pvviewer
  namespace: default
```

Step 2: Create the ClusterRole with PV list permissions

Create a YAML file:

```yaml
# pvviewer-role.yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: pvviewer-role
rules:
- apiGroups: [""]
  resources: ["persistentvolumes"]
  verbs: ["list"]
```

Imperative Method:

```sh
kubectl create clusterrole pvviewer-role --verb=list --resource=persistentvolumes
```

Step 3: Create the ClusterRoleBinding


Create a YAML file:

```yaml
# pvviewer-role-binding.yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: pvviewer-role-binding
subjects:
- kind: ServiceAccount
  name: pvviewer
  namespace: default
roleRef:
  kind: ClusterRole
  name: pvviewer-role
  apiGroup: rbac.authorization.k8s.io
```

Imperative Method:

```sh
kubectl create clusterrolebinding pvviewer-role-binding --clusterrole=pvviewer-role --serviceaccount=default:pvviewer
```

Step 4: Create the Pod with the ServiceAccount

Create a YAML file:

```yaml
# pvviewer-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: pvviewer
  namespace: default
spec:
  serviceAccountName: pvviewer
  containers:
  - name: pvviewer
    image: redis
```

Imperative Method:

```sh
kubectl run pvviewer --image=redis --serviceaccount=pvviewer
```

Step 5: Apply all the manifests (if using declarative method)

```sh
kubectl apply -f pvviewer-role.yaml
kubectl apply -f pvviewer-role-binding.yaml
kubectl apply -f pvviewer-pod.yaml
```

Step 6: Verification

Check ServiceAccount:

```sh
kubectl get serviceaccount pvviewer
```

Check ClusterRole:

```sh
kubectl get clusterrole pvviewer-role
kubectl describe clusterrole pvviewer-role
```

Check ClusterRoleBinding:

```sh
kubectl get clusterrolebinding pvviewer-role-binding
kubectl describe clusterrolebinding pvviewer-role-binding
```

Check Pod:

```sh
kubectl get pod pvviewer
kubectl describe pod pvviewer
```

Step 7: Test the permissions

Verify that the ServiceAccount can list PersistentVolumes:

```sh
kubectl auth can-i list persistentvolumes --as=system:serviceaccount:default:pvviewer
```

Expected output: yes

Test from within the pod:

```sh
kubectl exec -it pvviewer -- redis-cli ping
# Verify pod is running properly
```

Complete Example using Imperative Commands:

```sh
# Step 1: Create ServiceAccount
kubectl create serviceaccount pvviewer

# Step 2: Create ClusterRole
kubectl create clusterrole pvviewer-role --verb=list --resource=persistentvolumes

# Step 3: Create ClusterRoleBinding
kubectl create clusterrolebinding pvviewer-role-binding --clusterrole=pvviewer-role --serviceaccount=default:pvviewer

# Step 4: Create Pod
kubectl run pvviewer --image=redis --serviceaccount=pvviewer

# Step 5: Verify everything
kubectl get serviceaccount pvviewer
kubectl get clusterrole pvviewer-role
kubectl get clusterrolebinding pvviewer-role-binding
kubectl get pod pvviewer
```

Key Points:
- ClusterRole vs Role: Use ClusterRole because PersistentVolumes are cluster-scoped resources, not namespace-scoped
- ClusterRoleBinding: Required to bind the ClusterRole to the ServiceAccount across the entire cluster
- ServiceAccount specification: Must be set in the pod spec to use the custom ServiceAccount instead of the default one
- Permissions: The ServiceAccount gets only list permission on PersistentVolumes, following the principle of least privilege

Expected Result:
- ServiceAccount pvviewer can list all PersistentVolumes in the cluster
- Pod pvviewer runs with the custom ServiceAccount
- The pod has access to list PersistentVolumes through the ServiceAccount's permissions
- Redis container runs successfully within the pod

3. Create a StorageClass named **rancher-sc** with the following specifications:

- The provisioner should be **rancher.io/local-path**.
- The volume binding mode should be **WaitForFirstConsumer**.
- Volume expansion should be **enabled**.

Solution Steps
Step 1: Create the StorageClass YAML manifest

Create a YAML file:

```yaml
# rancher-sc.yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: rancher-sc
provisioner: rancher.io/local-path
volumeBindingMode: WaitForFirstConsumer
allowVolumeExpansion: true
```

Step 2: Apply the StorageClass manifest

```sh
kubectl apply -f rancher-sc.yaml
```

Step 3: Verify the StorageClass was created successfully

```sh
kubectl get storageclass rancher-sc
kubectl describe storageclass rancher-sc
```

Step 4: Check all StorageClasses in the cluster

```sh
kubectl get storageclass
```

Alternative: Using kubectl command line (imperative approach)
Unfortunately, there's no direct imperative command to create a StorageClass with all these specifications. You must use the YAML manifest approach.

Verification Commands
Check StorageClass details:

```sh
kubectl get sc rancher-sc -o yaml
```

Expected output should show:

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: rancher-sc
provisioner: rancher.io/local-path
volumeBindingMode: WaitForFirstConsumer
allowVolumeExpansion: true
```

Check if the StorageClass appears in the list:

```sh
kubectl get storageclass
```

Expected output format:

```sh
NAME         PROVISIONER             RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGErancher-sc   rancher.io/local-path   Delete          WaitForFirstConsumer   true                   1m
```

Key Points:
- Provisioner: rancher.io/local-path is commonly used with Rancher's local-path-provisioner for local storage
- Volume Binding Mode: WaitForFirstConsumer delays volume binding until a pod using the PVC is created
- Volume Expansion: allowVolumeExpansion: true allows resizing of volumes after creation
- No default: This StorageClass is not set as the default (no annotation for default class)

What each parameter does:
1. provisioner: rancher.io/local-path
  -Specifies which storage provisioner will handle volume creation
  -Rancher local-path provisioner creates local volumes on nodes

2. volumeBindingMode: WaitForFirstConsumer
  -Delays volume binding until a pod is scheduled
  -Ensures the volume is created on the same node where the pod will run
3. allowVolumeExpansion: true
  -Enables volume expansion after creation
  -Allows increasing the size of existing volumes

Expected Result:
The StorageClass rancher-sc will be available for use by PersistentVolumeClaims that want to use Rancher's local-path provisioner with delayed binding and expandable volumes.

4. Create a ConfigMap named **app-config** in the namespace **cm-namespace** with the following key-value pairs:

- ENV=production
- LOG_LEVEL=info

Then, modify the existing Deployment named **cm-webapp** in the same namespace to use the **app-config** ConfigMap by setting the environment variables **ENV** and **LOG_LEVEL** in the container from the ConfigMap.

Solution Steps
Step 1: Create the namespace (if it doesn't exist)

```sh
kubectl create namespace cm-namespace
```

Step 2: Create the ConfigMap

Imperative Method:

```sh
kubectl create configmap app-config --from-literal=ENV=production --from-literal=LOG_LEVEL=info -n cm-namespace
```

Declarative Method: Create a YAML file app-config.yaml:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
  namespace: cm-namespace
data:
  ENV: production
  LOG_LEVEL: info
```

Apply the ConfigMap:

```yaml
kubectl apply -f app-config.yaml
```

Step 3: Verify the ConfigMap was created

```sh
kubectl get configmap app-config -n cm-namespace
kubectl describe configmap app-config -n cm-namespace
```

Step 4: Check the existing deployment

```sh
kubectl get deployment cm-webapp -n cm-namespace
kubectl describe deployment cm-webapp -n cm-namespace
```

Step 5: Modify the deployment to use the ConfigMap

Method 1: Using kubectl edit (imperative)

```sh
kubectl edit deployment cm-webapp -n cm-namespace
```

Add the environment variables section to the container spec:

```yaml
spec:
  containers:
  - name: <container-name>
    image: <image-name>
    env:
    - name: ENV
      valueFrom:
        configMapKeyRef:
          name: app-config
          key: ENV
    - name: LOG_LEVEL
      valueFrom:
        configMapKeyRef:
          name: app-config
          key: LOG_LEVEL
```


Method 2: Using kubectl patch (imperative)

```sh
kubectl patch deployment cm-webapp -n cm-namespace -p '
{
  "spec": {
    "template": {
      "spec": {
        "containers": [
          {
            "name": "webapp",
            "env": [
              {
                "name": "ENV",
                "valueFrom": {
                  "configMapKeyRef": {
                    "name": "app-config",
                    "key": "ENV"
                  }
                }
              },
              {
                "name": "LOG_LEVEL",
                "valueFrom": {
                  "configMapKeyRef": {
                    "name": "app-config",
                    "key": "LOG_LEVEL"
                  }
                }
              }
            ]
          }
        ]
      }
    }
  }
}
'
```

Method 3: Export, modify, and apply (declarative)

```sh
# Export current deployment
kubectl get deployment cm-webapp -n cm-namespace -o yaml > cm-webapp.yaml

# Edit the file to add the env section
# Then apply the changes
kubectl apply -f cm-webapp.yaml
```

Step 6: Verify the deployment was updated

```sh
kubectl get deployment cm-webapp -n cm-namespace
kubectl describe deployment cm-webapp -n cm-namespace
```

Step 7: Check if the rollout completed successfully

```sh
kubectl rollout status deployment/cm-webapp -n cm-namespace
```

Step 8: Verify the environment variables are set in the pods

```sh
# Get the pod name
kubectl get pods -n cm-namespace -l app=cm-webapp

# Check environment variables in the pod
kubectl exec -it <pod-name> -n cm-namespace -- env | grep -E "ENV|LOG_LEVEL"
```

Step 9: Verify ConfigMap is being used

```sh
kubectl describe pod <pod-name> -n cm-namespace
```

Look for the ConfigMap reference in the environment variables section.

Complete Example with Imperative Commands:

```sh
# Step 1: Create namespace
kubectl create namespace cm-namespace

# Step 2: Create ConfigMap
kubectl create configmap app-config --from-literal=ENV=production --from-literal=LOG_LEVEL=info -n cm-namespace

# Step 3: Modify deployment (get container name first)
kubectl get deployment cm-webapp -n cm-namespace -o jsonpath='{.spec.template.spec.containers[0].name}'

# Step 4: Patch the deployment
kubectl patch deployment cm-webapp -n cm-namespace --type='json' -p='[
  {
    "op": "add",
    "path": "/spec/template/spec/containers/0/env",
    "value": [
      {
        "name": "ENV",
        "valueFrom": {
          "configMapKeyRef": {
            "name": "app-config",
            "key": "ENV"
          }
        }
      },
      {
        "name": "LOG_LEVEL",
        "valueFrom": {
          "configMapKeyRef": {
            "name": "app-config",
            "key": "LOG_LEVEL"
          }
        }
      }
    ]
  }
]'

# Step 5: Verify the changes
kubectl rollout status deployment/cm-webapp -n cm-namespace
kubectl get pods -n cm-namespace
```

Key Points:
- ConfigMap creation: Use --from-literal for key-value pairs
- Environment variables: Reference ConfigMap keys using configMapKeyRef
- Deployment update: Changes to env vars trigger a rolling update
- Verification: Always check that pods are restarted and env vars are set correctly

Expected Result:
- ConfigMap app-config is created with the specified key-value pairs
- Deployment cm-webapp is updated to use environment variables from the ConfigMap
- New pods are created with ENV=production and LOG_LEVEL=info environment variables
- The rolling update completes successfully without downtime

5. Create a PriorityClass named **low-priority** with a value of **50000**. A pod named **lp-pod** exists in the namespace **low-priority**. Modify the pod to use the priority class you created. Recreate the pod if necessary.

Solution Steps
Step 1: Create the PriorityClass

Create a YAML file :

```yaml
# low-priority.yaml
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata:
  name: low-priority
value: 50000
globalDefault: false
description: "Low priority class for less critical workloads"
```

Apply the PriorityClass:

```sh
kubectl apply -f low-priority.yaml
```

Step 2: Verify the PriorityClass was created

```sh
kubectl get priorityclass low-priority
kubectl describe priorityclass low-priority
```

Step 3: Check the existing pod

```sh
kubectl get pod lp-pod -n low-priority
kubectl describe pod lp-pod -n low-priority
```

Step 4: Export the pod configuration

```sh
kubectl get pod lp-pod -n low-priority -o yaml > lp-pod-backup.yaml
```

Step 5: Create a new pod manifest with the PriorityClass

Create a new YAML file lp-pod-updated.yaml:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: lp-pod
  namespace: low-priority
spec:
  priorityClassName: low-priority
  containers:
  - name: <container-name>  # Use the container from the existing pod
    image: <image-name>      # Use the image from the existing pod
  # Copy other spec fields from the original pod as needed
```

Step 6: Delete the existing pod

```sh
kubectl delete pod lp-pod -n low-priority
```

Step 7: Create the new pod with PriorityClass

```sh
kubectl apply -f lp-pod-updated.yaml
```

Step 8: Verify the pod is using the PriorityClass

```sh
kubectl get pod lp-pod -n low-priority
kubectl describe pod lp-pod -n low-priority | grep -i priority
```

Alternative Method: Using kubectl patch (if supported)
Note: This method won't work because priorityClassName is an immutable field in pod specs. The pod must be recreated.

Complete Example Workflow:

```sh
# Step 1: Create PriorityClass
cat <<EOF | kubectl apply -f -
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata:
  name: low-priority
value: 50000
globalDefault: false
description: "Low priority class for less critical workloads"
EOF

# Step 2: Verify PriorityClass
kubectl get priorityclass low-priority

# Step 3: Get existing pod details
kubectl get pod lp-pod -n low-priority -o yaml > original-pod.yaml

# Step 4: Extract essential information
kubectl get pod lp-pod -n low-priority -o jsonpath='{.spec.containers[0].image}'
kubectl get pod lp-pod -n low-priority -o jsonpath='{.spec.containers[0].name}'

# Step 5: Delete existing pod
kubectl delete pod lp-pod -n low-priority

# Step 6: Recreate pod with PriorityClass
kubectl run lp-pod --image=<image-from-step-4> -n low-priority --dry-run=client -o yaml > new-pod.yaml

# Edit new-pod.yaml to add:
# spec:
#   priorityClassName: low-priority

kubectl apply -f new-pod.yaml

# Step 7: Verify
kubectl describe pod lp-pod -n low-priority | grep -A 2 -B 2 Priority
```


Key Points:
- PriorityClass value: Higher values indicate higher priority (50000 is relatively high priority)
- Pod recreation required: The priorityClassName field cannot be modified on existing pods
- Immutable field: Priority class can only be set during pod creation
- Scheduling impact: Pods with higher priority values are scheduled before lower priority pods

Verification Commands:

```sh
# Check PriorityClass exists
kubectl get priorityclass

# Verify pod is using the priority class
kubectl get pod lp-pod -n low-priority -o jsonpath='{.spec.priorityClassName}'

# Check pod priority value
kubectl get pod lp-pod -n low-priority -o jsonpath='{.spec.priority}'

# Describe pod to see full priority information
kubectl describe pod lp-pod -n low-priority | grep -i priority
```

Expected Result:
- PriorityClass low-priority is created with value 50000
- Pod lp-pod is recreated in the low-priority namespace
- The new pod uses the low-priority PriorityClass
- The pod shows priority value 50000 when described
- Kubernetes scheduler will consider this pod's priority during scheduling decisions

6. We have deployed a new pod called **np-test-1** and a service called **np-test-service**. Incoming connections to this service are not working. Troubleshoot and fix it.
Create NetworkPolicy, by the name **ingress-to-nptest** that allows incoming connections to the service over port **80**.

Important: Don't delete any current objects deployed.

Solution Steps
Step 1: Check the existing pod and service

```sh
kubectl get pod np-test-1
kubectl get service np-test-service
kubectl describe pod np-test-1
kubectl describe service np-test-service
```

Step 2: Check if there are any existing NetworkPolicies

```sh
kubectl get networkpolicy
kubectl get netpol
```

Step 3: Test connectivity to the service (to confirm the issue)

```sh
# Get the service IP
kubectl get service np-test-service -o jsonpath='{.spec.clusterIP}'

# Try to connect to the service
kubectl run test-pod --image=busybox --rm -it --restart=Never -- wget -qO- http://np-test-service/
```

Step 4: Identify the pod labels and namespace

```sh
kubectl get pod np-test-1 --show-labels
kubectl get pod np-test-1 -o yaml | grep -A 5 labels
```

Step 5: Create the NetworkPolicy to allow ingress traffic

Create a YAML file:

```yaml
# ingress-to-nptest.yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: ingress-to-nptest
  namespace: default  # Adjust if the pod is in a different namespace
spec:
  podSelector:
    matchLabels:
      # Use the labels from the np-test-1 pod
      app: np-test-1  # Adjust based on actual pod labels
  policyTypes:
  - Ingress
  ingress:
  - ports:
    - protocol: TCP
      port: 80
    from: []  # Allow from all sources
```

Alternative: More specific NetworkPolicy (if you want to restrict sources)

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: ingress-to-nptest
  namespace: default
spec:
  podSelector:
    matchLabels:
      app: np-test-1  # Adjust based on actual pod labels
  policyTypes:
  - Ingress
  ingress:
  - ports:
    - protocol: TCP
      port: 80
    from:
    - podSelector: {}  # Allow from all pods in the same namespace
    - namespaceSelector: {}  # Allow from all namespaces
```

Step 6: Apply the NetworkPolicy

```sh
kubectl apply -f ingress-to-nptest.yaml
```

Step 7: Verify the NetworkPolicy was created

```sh
kubectl get networkpolicy ingress-to-nptest
kubectl describe networkpolicy ingress-to-nptest
```

Step 8: Test connectivity again

```sh
# Test the service connectivity
kubectl run test-connectivity --image=busybox --rm -it --restart=Never -- wget -qO- http://np-test-service/

# Or test with curl
kubectl run test-curl --image=curlimages/curl --rm -it --restart=Never -- curl http://np-test-service/
```

Step 9: Verify the service is working properly

```sh
# Check if the service endpoints are correct
kubectl get endpoints np-test-service

# Check service details
kubectl describe service np-test-service
```

Troubleshooting Steps if it still doesn't work:

Check pod readiness:

```sh
kubectl get pod np-test-1 -o wide
kubectl logs np-test-1
```

Check service selector:

```sh
kubectl get service np-test-service -o yaml | grep -A 5 selector
kubectl get pod np-test-1 --show-labels
```

Check if the application is listening on port 80:

```sh
kubectl exec np-test-1 -- netstat -ln | grep :80
```

Complete Example Workflow:

```sh
# Step 1: Inspect existing resources
kubectl get pod np-test-1 --show-labels
kubectl get service np-test-service
kubectl describe service np-test-service

# Step 2: Create NetworkPolicy
cat <<EOF | kubectl apply -f -
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: ingress-to-nptest
  namespace: default
spec:
  podSelector:
    matchLabels:
      run: np-test-1  # Common label when created with kubectl run
  policyTypes:
  - Ingress
  ingress:
  - ports:
    - protocol: TCP
      port: 80
EOF

# Step 3: Verify NetworkPolicy
kubectl get networkpolicy ingress-to-nptest
kubectl describe networkpolicy ingress-to-nptest

# Step 4: Test connectivity
kubectl run test-connection --image=busybox --rm -it --restart=Never -- wget -qO- http://np-test-service/
```

Key Points:
  -NetworkPolicy blocks traffic by default: When a NetworkPolicy exists that selects a pod, all traffic not explicitly allowed is blocked
  -podSelector: Must match the exact labels on the target pod (np-test-1)
  -Ingress rules: Define what traffic is allowed to reach the pod
  -Port specification: Must match the port the application is listening on (80)
  from: []: Allows traffic from any source (most permissive)
  -Expected Result:
    -NetworkPolicy ingress-to-nptest is created successfully
    -Incoming connections to np-test-service on port 80 are now allowed
    -The service becomes accessible from other pods/services in the cluster
    -Connectivity tests pass without errors

Note: Make sure to adjust the podSelector.matchLabels based on the actual labels of the np-test-1 pod. Use kubectl get pod np-test-1 --show-labels to see the exact labels.


7. Taint the worker node node01 to be Unschedulable. Once done, create a pod called **dev-redis**, image **redis:alpine**, to ensure workloads are not scheduled to this worker node. Finally, create a new pod called **prod-redis** and image: **redis:alpine** with toleration to be scheduled on node01.

key: **env_type**, value: **production**, operator: **Equal** and effect: **NoSchedule**

Step-by-Step Solution
Step 1: Taint the node01 worker node

```sh
kubectl taint nodes node01 env_type=production:NoSchedule
```
Explanation: This command adds a taint to node01 with:

Key: env_type
Value: production
Effect: NoSchedule (prevents new pods from being scheduled)

Step 2: Verify the taint was applied

```sh
kubectl describe node node01 | grep -i taint
```

You should see output like:

Taints: env_type=production:NoSchedule

Step 3: Create the dev-redis pod (without toleration)
Method 1: Imperative command

```sh
kubectl run dev-redis --image=redis:alpine
```

Method 2: Declarative YAML

```yaml
# dev-redis.yaml
apiVersion: v1
kind: Pod
metadata:
  name: dev-redis
spec:
  containers:
  - name: dev-redis
    image: redis:alpine
```

Apply with:

```sh
kubectl apply -f dev-redis.yaml
```

Step 4: Create the prod-redis pod (with toleration)
Declarative YAML (Recommended)

```yaml
# prod-redis.yaml
apiVersion: v1
kind: Pod
metadata:
  name: prod-redis
spec:
  tolerations:
  - key: "env_type"
    operator: "Equal"
    value: "production"
    effect: "NoSchedule"
  containers:
  - name: prod-redis
    image: redis:alpine
```

Apply with:

```sh
kubectl apply -f prod-redis.yaml
```

Step 5: Verify the scheduling behavior
Check where pods are scheduled:

```sh
kubectl get pods -o wide
```

Expected results:

dev-redis: Should be scheduled on a node OTHER than node01 (like controlplane)
prod-redis: Should be scheduled on node01 (because it has the toleration)
Step 6: Additional verification commands
Check pod status:

```sh
kubectl get pods dev-redis -o wide
kubectl get pods prod-redis -o wide
```

Check node taints:

```sh
kubectl get nodes -o json | jq '.items[] | {name: .metadata.name, taints: .spec.taints}'
```

Describe pods to see scheduling decisions:

```sh
kubectl describe pod dev-redis
kubectl describe pod prod-redis
```

Key Concepts Explained
1. Taints

Applied to nodes to repel pods
Format: key=value:effect
Effects: NoSchedule, PreferNoSchedule, NoExecute
2. Tolerations

Applied to pods to tolerate specific taints
Must match the taint's key, value, operator, and effect
3. Scheduling Logic

Pods without tolerations cannot be scheduled on tainted nodes
Pods with matching tolerations can be scheduled on tainted nodes
Other untainted nodes remain available for all pods

Troubleshooting Tips
If dev-redis gets scheduled on node01:

Check if the taint was applied correctly
Verify no toleration exists in the pod spec
If prod-redis doesn't get scheduled on node01:

Verify the toleration matches exactly:
Key: env_type
Value: production
Operator: Equal
Effect: NoSchedule

If pods remain in Pending state:

Check if there are enough resources on available nodes
Use kubectl describe pod <pod-name> to see scheduling failures

Expected Final State

```sh
kubectl get pods -o wide
```

Should show something like:


NAME        READY   STATUS    RESTARTS   AGE   IP         NODE           
dev-redis   1/1     Running   0          2m    10.x.x.x   controlplane   
prod-redis  1/1     Running   0          1m    10.x.x.x   node01         

8. A PersistentVolumeClaim named **app-pvc** exists in the namespace **storage-ns**, but it is not getting bound to the available PersistentVolume named **app-pv**.

Inspect both the PVC and PV and identify why the PVC is not being bound and fix the issue so that the PVC successfully binds to the PV. Do not modify the PV resource.

Step-by-Step Solution
Step 1: Inspect the current state of PVC and PV
Check the PVC status:

```sh
kubectl get pvc app-pvc -n storage-ns
kubectl describe pvc app-pvc -n storage-ns
```

Check the PV status:

```sh
kubectl get pv app-pv
kubectl describe pv app-pv
```

Step 2: Identify common binding issues
The most common reasons why a PVC doesn't bind to a PV are:

1. Storage size mismatch - PVC requests more storage than PV provides
2. Access mode mismatch - PVC and PV have incompatible access modes
3. Storage class mismatch - Different storage classes specified
4. Selector mismatch - PVC has a selector that doesn't match PV labels
Node affinity - PV has node affinity that conflicts with available nodes

Step 3: Compare PVC and PV specifications
Get detailed YAML output:

```sh
kubectl get pvc app-pvc -n storage-ns -o yaml
kubectl get pv app-pv -o yaml
```

Key fields to compare:

Field	          PVC Location	                     PV Location
Storage Size	  spec.resources.requests.storage	  spec.capacity.storage
Access Modes	  spec.accessModes	                spec.accessModes
Storage Class	  spec.storageClassName	            spec.storageClassName
Selectors	      spec.selector	                    metadata.labels

Step 4: Most likely issues and solutions
Issue 1: Storage Size Mismatch

Problem: PVC requests more storage than PV provides

```yaml
# PVC might have:
spec:
  resources:
    requests:
      storage: 2Gi

# PV might have:
spec:
  capacity:
    storage: 1Gi
```

Solution: Edit the PVC to request less storage

```sh
kubectl edit pvc app-pvc -n storage-ns
```

Change the storage request to match or be less than the PV capacity.

Issue 2: Access Mode Mismatch
Problem: Incompatible access modes

```yaml
# PVC might have:
spec:
  accessModes:
    - ReadWriteMany

# PV might have:
spec:
  accessModes:
    - ReadWriteOnce
```

Solution: Edit the PVC to use compatible access modes

```sh
kubectl edit pvc app-pvc -n storage-ns
```

Issue 3: Storage Class Mismatch
Problem: Different storage classes

```yaml
# PVC might have:
spec:
  storageClassName: "fast-ssd"

# PV might have:
spec:
  storageClassName: "standard"
```

Solution: Edit the PVC to match PV's storage class or remove it

```sh
kubectl edit pvc app-pvc -n storage-ns
```

Issue 4: Selector Mismatch
Problem: PVC has a selector that doesn't match PV labels

```yaml
# PVC might have:
spec:
  selector:
    matchLabels:
      environment: production

# PV might have:
metadata:
  labels:
    environment: development
```

Solution: Remove or adjust the selector in PVC

```sh
kubectl edit pvc app-pvc -n storage-ns
```

Step 5: Complete troubleshooting workflow

```sh
# Step 1: Check current status
kubectl get pvc app-pvc -n storage-ns -o wide
kubectl get pv app-pv -o wide

# Step 2: Detailed inspection
echo "=== PVC Details ==="
kubectl describe pvc app-pvc -n storage-ns

echo "=== PV Details ==="
kubectl describe pv app-pv

# Step 3: Compare specifications side by side
echo "=== PVC Spec ==="
kubectl get pvc app-pvc -n storage-ns -o jsonpath='{.spec}' | jq

echo "=== PV Spec ==="
kubectl get pv app-pv -o jsonpath='{.spec}' | jq

# Step 4: Check events for clues
kubectl get events -n storage-ns --sort-by='.lastTimestamp'
```
Step 6: Example fix scenarios
Scenario A: Storage size too large

```sh
kubectl edit pvc app-pvc -n storage-ns
# Change from:
#   resources:
#     requests:
#       storage: 10Gi
# To:
#   resources:
#     requests:
#       storage: 5Gi  # Match PV capacity
```

Scenario B: Wrong access mode

```sh
kubectl edit pvc app-pvc -n storage-ns
# Change from:
#   accessModes:
#     - ReadWriteMany
# To:
#   accessModes:
#     - ReadWriteOnce  # Match PV access mode
```

Scenario C: Remove storage class

```sh
kubectl edit pvc app-pvc -n storage-ns
# Remove or comment out:
# storageClassName: "wrong-class"
# Or set to match PV:
# storageClassName: "correct-class"
```

Step 7: Verify the fix
Check binding status:

```sh
kubectl get pvc app-pvc -n storage-ns
# Should show STATUS: Bound

kubectl get pv app-pv
# Should show STATUS: Bound and CLAIM: storage-ns/app-pvc
```

Verify the binding:

```sh
kubectl describe pvc app-pvc -n storage-ns | grep -A 5 "Volume:"
kubectl describe pv app-pv | grep -A 5 "ClaimRef:"
```

Step 8: Test with a pod (optional)
Create a test pod to verify the volume works:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: test-pod
  namespace: storage-ns
spec:
  containers:
  - name: test-container
    image: busybox
    command: ["sleep", "3600"]
    volumeMounts:
    - name: test-volume
      mountPath: /data
  volumes:
  - name: test-volume
    persistentVolumeClaim:
      claimName: app-pvc
```

```sh
kubectl apply -f test-pod.yaml
kubectl exec -it test-pod -n storage-ns -- ls -la /data
```

Common Commands Summary

```sh
# Quick diagnosis
kubectl get pvc,pv -o wide

# Detailed inspection
kubectl describe pvc app-pvc -n storage-ns
kubectl describe pv app-pv

# Compare specs
kubectl get pvc app-pvc -n storage-ns -o yaml
kubectl get pv app-pv -o yaml

# Edit PVC if needed
kubectl edit pvc app-pvc -n storage-ns

# Verify binding
kubectl get pvc app-pvc -n storage-ns
kubectl get pv app-pv
```

Expected Outcome
After fixing the mismatch:

PVC status changes from Pending to Bound
PV status changes from Available to Bound
PV shows the correct ClaimRef pointing to storage-ns/app-pvc
The volume can be successfully mounted by pods
The key is to identify which specification mismatch is preventing the binding and adjust the PVC accordingly without modifying the PV resource.

9. A kubeconfig file called **super.kubeconfig** has been created under ``` /root/CKA ```. There is something wrong with the configuration. Troubleshoot and fix it.

Step 1. Examine the kubeconfig file
First, let's look at the file:

```sh
ls -la /root/CKA/
cat /root/CKA/super.kubeconfig
```

Check the file structure

```sh
kubectl config view --kubeconfig=/root/CKA/super.kubeconfig
```

Step 2: Test the current kubeconfig
Try to use the kubeconfig:

```sh
kubectl --kubeconfig=/root/CKA/super.kubeconfig get nodes
```

Check what errors appear:

```sh
kubectl --kubeconfig=/root/CKA/super.kubeconfig cluster-info
```

Step 3: Common kubeconfig issues to check
Issue 1: Incorrect API Server URL
Look for server address in clusters section:

```yaml
clusters:
- cluster:
    server: https://wrong-server:6443  # Wrong server
```

Fix: Update to correct server address

```yaml
clusters:
- cluster:
    server: https://controlplane:6443  # or correct IP
```

Issue 2: Wrong Certificate Authority Data
Check certificate-authority-data:

```yaml
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTi...  # Wrong/corrupted cert
```

Issue 3: Incorrect User Credentials
Check client certificate and key:

```yaml
users:
- user:
    client-certificate-data: LS0tLS1CRUdJTi...  # Wrong cert
    client-key-data: LS0tLS1CRUdJTi...          # Wrong key
```

Issue 4: Wrong Context Configuration
Check context settings:

```yaml
contexts:
- context:
    cluster: wrong-cluster-name    # Doesn't match cluster name
    user: wrong-user-name          # Doesn't match user name
```

Step 4: Systematic troubleshooting approach
Step 4a: Compare with working kubeconfig

```sh
# Compare structure with working config
kubectl config view --kubeconfig=/root/.kube/config
kubectl config view --kubeconfig=/root/CKA/super.kubeconfig
```

Step 4b: Check individual components

Test cluster connectivity:

```sh
# Extract server URL from kubeconfig
SERVER=$(kubectl config view --kubeconfig=/root/CKA/super.kubeconfig --minify -o jsonpath='{.clusters[0].cluster.server}')
echo "Server: $SERVER"

# Test if server is reachable
curl -k $SERVER/version
```

Validate certificates:

```sh
# Check if certificate data is valid base64
kubectl config view --kubeconfig=/root/CKA/super.kubeconfig --raw -o jsonpath='{.clusters[0].cluster.certificate-authority-data}' | base64 -d | openssl x509 -text -noout
```

Step 5: Common fixes
Fix 1: Correct the server URL

```sh
# If server URL is wrong, update it
kubectl config set-cluster kubernetes --server=https://controlplane:6443 --kubeconfig=/root/CKA/super.kubeconfig
```

Fix 2: Copy certificates from working config

```sh
# Copy CA certificate from working config
CA_DATA=$(kubectl config view --raw --minify -o jsonpath='{.clusters[0].cluster.certificate-authority-data}')
kubectl config set clusters.kubernetes.certificate-authority-data $CA_DATA --kubeconfig=/root/CKA/super.kubeconfig
```

Fix 3: Fix user credentials

```sh
# Copy user cert and key from working config
CLIENT_CERT=$(kubectl config view --raw --minify -o jsonpath='{.users[0].user.client-certificate-data}')
CLIENT_KEY=$(kubectl config view --raw --minify -o jsonpath='{.users[0].user.client-key-data}')

kubectl config set users.kubernetes-admin.client-certificate-data $CLIENT_CERT --kubeconfig=/root/CKA/super.kubeconfig
kubectl config set users.kubernetes-admin.client-key-data $CLIENT_KEY --kubeconfig=/root/CKA/super.kubeconfig
```

Fix 4: Correct context references

```sh
# Make sure context references exist
kubectl config set-context kubernetes-admin@kubernetes --cluster=kubernetes --user=kubernetes-admin --kubeconfig=/root/CKA/super.kubeconfig
kubectl config use-context kubernetes-admin@kubernetes --kubeconfig=/root/CKA/super.kubeconfig
```

Step 6: Complete troubleshooting workflow
Check the original file structure:

```sh
echo "=== Original kubeconfig structure ==="
kubectl config view --kubeconfig=/root/CKA/super.kubeconfig

echo "=== Working kubeconfig structure ==="
kubectl config view --kubeconfig=/root/.kube/config
```

Identify specific issues:

```sh
# Test connectivity with current config
echo "=== Testing connectivity ==="
kubectl --kubeconfig=/root/CKA/super.kubeconfig get nodes 2>&1

# Check if it's a server URL issue
echo "=== Checking server URL ==="
grep -A 5 "server:" /root/CKA/super.kubeconfig

# Check if it's a certificate issue
echo "=== Checking certificates ==="
kubectl config view --kubeconfig=/root/CKA/super.kubeconfig --raw | grep -A 2 -B 2 "certificate-authority-data"
```

Step 7: Most likely fix scenarios
Scenario A: Wrong server port (common mistake)

```sh
# Original (wrong):
# server: https://controlplane:6444

# Fix:
kubectl config set-cluster kubernetes --server=https://controlplane:6443 --kubeconfig=/root/CKA/super.kubeconfig
```

Scenario B: Missing or wrong cluster name reference

```sh
# Check current cluster name
kubectl config view --kubeconfig=/root/CKA/super.kubeconfig | grep "name:" | head -1

# Update context to use correct cluster name
kubectl config set-context kubernetes-admin@kubernetes --cluster=kubernetes --user=kubernetes-admin --kubeconfig=/root/CKA/super.kubeconfig
```

Scenario C: Corrupted certificate data

```sh
# Copy working certificates
cp /root/.kube/config /root/CKA/super.kubeconfig.backup

# Extract and copy CA cert from working config
kubectl config view --raw --minify -o jsonpath='{.clusters[0].cluster.certificate-authority-data}' | \
kubectl config set clusters.kubernetes.certificate-authority-data - --kubeconfig=/root/CKA/super.kubeconfig
```

Step 8: Comprehensive fix script

```sh
#!/bin/bash
# Comprehensive kubeconfig fix script

KUBECONFIG_FILE="/root/CKA/super.kubeconfig"
WORKING_CONFIG="/root/.kube/config"

echo "Fixing kubeconfig: $KUBECONFIG_FILE"

# 1. Set correct server URL
kubectl config set-cluster kubernetes \
  --server=https://controlplane:6443 \
  --kubeconfig=$KUBECONFIG_FILE

# 2. Copy CA certificate from working config
CA_DATA=$(kubectl config view --raw --kubeconfig=$WORKING_CONFIG --minify -o jsonpath='{.clusters[0].cluster.certificate-authority-data}')
kubectl config set clusters.kubernetes.certificate-authority-data "$CA_DATA" --kubeconfig=$KUBECONFIG_FILE

# 3. Copy client certificate and key
CLIENT_CERT=$(kubectl config view --raw --kubeconfig=$WORKING_CONFIG --minify -o jsonpath='{.users[0].user.client-certificate-data}')
CLIENT_KEY=$(kubectl config view --raw --kubeconfig=$WORKING_CONFIG --minify -o jsonpath='{.users[0].user.client-key-data}')

kubectl config set users.kubernetes-admin.client-certificate-data "$CLIENT_CERT" --kubeconfig=$KUBECONFIG_FILE
kubectl config set users.kubernetes-admin.client-key-data "$CLIENT_KEY" --kubeconfig=$KUBECONFIG_FILE

# 4. Set correct context
kubectl config set-context kubernetes-admin@kubernetes \
  --cluster=kubernetes \
  --user=kubernetes-admin \
  --kubeconfig=$KUBECONFIG_FILE

# 5. Use the context
kubectl config use-context kubernetes-admin@kubernetes --kubeconfig=$KUBECONFIG_FILE

echo "Testing fixed kubeconfig..."
kubectl --kubeconfig=$KUBECONFIG_FILE get nodes
```

Expected Outcome
After fixing the kubeconfig:

✅ kubectl --kubeconfig=/root/CKA/super.kubeconfig get nodes works
✅ No connection refused or certificate errors
✅ Can access cluster resources normally
✅ Kubeconfig structure is valid and complete
Common Error Messages and Solutions

Common Error Messages and Solutions

Error	                                      Likely Cause	            Solution
"connection refused"	                      Wrong server URL/port	    Fix server address
"certificate signed by unknown authority"	  Wrong CA certificate	    Copy CA from working config
"Unauthorized"	Wrong client credentials	  Copy client cert/key
"context not found"	Wrong context name	    Fix context configuration

The key is to systematically check each component of the kubeconfig (clusters, users, contexts) and compare with a working configuration to identify and fix the specific issue.

10. We have created a new deployment called **nginx-deploy**. Scale the deployment to **3 replicas**. Has the number of replicas increased? Troubleshoot and fix the issue.

Step 1. Check the current state of the deployment
Check deployment status:

```sh
kubectl get deployment nginx-deploy
kubectl get deployment nginx-deploy -o wide
```

Check current replica count:

```sh
kubectl describe deployment nginx-deploy
```

Check pods associated with the deployment:

```sh
kubectl get pods -l app=nginx-deploy
# or check with deployment selector
kubectl get pods --selector=$(kubectl get deployment nginx-deploy -o jsonpath='{.spec.selector.matchLabels}' | tr -d '{}' | tr ',' ' ' | sed 's/:/=/g')
```
This command retrieves the list of pods that match the label selector used by the Kubernetes Deployment named nginx-deploy.

🔍 Step-by-Step Breakdown
kubectl get deployment nginx-deploy -o jsonpath='{.spec.selector.matchLabels}'

This extracts the label selectors defined in the nginx-deploy deployment.

Example output: {app:nginx,tier=frontend}

tr -d '{}'

- Removes the curly braces {} from the output.

tr ',' ' '

- Replaces commas with spaces, so multiple labels become space-separated.

sed 's/:/=/g'

- Replaces colons (:) with equals signs (=), converting the format from key:value to key=value, which is the format expected by kubectl get pods --selector.

kubectl get pods --selector=...

Finally, this uses the transformed label selector to list all pods that match the same labels as the nginx-deploy deployment.

Step 2: Scale the deployment to 3 replicas
Scale using kubectl scale command:

```sh
kubectl scale deployment nginx-deploy --replicas=3
```

Alternative method using kubectl patch:

```sh
kubectl patch deployment nginx-deploy -p '{"spec":{"replicas":3}}'
```

Step 3: Monitor the scaling process
Watch the deployment scaling:

```sh
kubectl get deployment nginx-deploy -w
```

Check rollout status:

```sh
kubectl rollout status deployment/nginx-deploy
```

Monitor pods creation:

```sh
kubectl get pods -l app=nginx-deploy -w
```

Step 4: Troubleshoot if scaling fails
If the replicas don't increase to 3, check these common issues:

Issue 1: Resource Constraints
Check node resources:

```sh
kubectl top nodes
kubectl describe nodes
```

Check if pods are pending due to insufficient resources:

```sh
kubectl get pods -l app=nginx-deploy
kubectl describe pods <pending-pod-name>
```

Look for resource-related events:

```sh
kubectl get events --sort-by='.lastTimestamp' | grep nginx-deploy
```

Issue 2: Pod Security Policies or Admission Controllers
Check for admission controller issues:

```sh
kubectl describe deployment nginx-deploy
kubectl get events --sort-by='.lastTimestamp' | head -20
```

Issue 3: Image Pull Issues
Check if pods can't start due to image problems:

```sh
kubectl describe pods -l app=nginx-deploy | grep -A 5 -B 5 "Failed"
kubectl get pods -l app=nginx-deploy -o wide
```

Issue 4: Node Taints and Tolerations
Check if nodes have taints preventing pod scheduling:

```sh
kubectl get nodes -o json | jq '.items[] | {name: .metadata.name, taints: .spec.taints}'
kubectl describe nodes | grep -A 5 -B 5 "Taints"
```

Issue 5: Controller Manager Issues
Check if the deployment controller is working:

```sh
kubectl get pods -n kube-system | grep controller-manager
kubectl logs -n kube-system kube-controller-manager-controlplane
```

Step 5: Systematic troubleshooting workflow
Step 5a: Verify deployment configuration


```sh
echo "=== Deployment Status ==="
kubectl get deployment nginx-deploy -o yaml

echo "=== Current Pods ==="
kubectl get pods -l app=nginx-deploy -o wide

echo "=== Recent Events ==="
kubectl get events --sort-by='.lastTimestamp' | grep nginx-deploy | head -10
```

Step 5b: Check replica set

```sh
echo "=== ReplicaSet Status ==="
kubectl get replicaset -l app=nginx-deploy
kubectl describe replicaset -l app=nginx-deploy
```

Step 5c: Check scheduler and controller logs

```sh
echo "=== Scheduler Logs ==="
kubectl logs -n kube-system kube-scheduler-controlplane --tail=20

echo "=== Controller Manager Logs ==="
kubectl logs -n kube-system kube-controller-manager-controlplane --tail=20
```

Step 6: Common fixes for scaling issues
Fix 1: Insufficient Resources
If nodes don't have enough CPU/memory:

```sh
# Check resource requests in deployment
kubectl describe deployment nginx-deploy | grep -A 5 "Requests"

# Option 1: Reduce resource requests
kubectl patch deployment nginx-deploy -p='{"spec":{"template":{"spec":{"containers":[{"name":"nginx","resources":{"requests":{"cpu":"50m","memory":"64Mi"}}}]}}}}'

# Option 2: Add more nodes (if possible in the environment)
```

Fix 2: Node Taints
If nodes are tainted:

```sh
# Remove taints from nodes (if appropriate)
kubectl taint nodes <node-name> <taint-key>:<taint-value>:<effect>-

# Or add tolerations to deployment
kubectl patch deployment nginx-deploy -p='{"spec":{"template":{"spec":{"tolerations":[{"key":"<taint-key>","operator":"Equal","value":"<taint-value>","effect":"<effect>"}]}}}}'
```

Fix 3: Image Pull Issues
If image can't be pulled:


```sh
# Check image name in deployment
kubectl describe deployment nginx-deploy | grep Image

# Update to a working image if needed
kubectl set image deployment/nginx-deploy nginx=nginx:latest
```

Fix 4: Restart Controller Manager
If controller manager is stuck:

```sh
# Check controller manager status
kubectl get pods -n kube-system kube-controller-manager-controlplane

# If needed, restart by deleting the pod (it will auto-restart)
kubectl delete pod -n kube-system kube-controller-manager-controlplane
```

Step 7: Complete troubleshooting script

```sh
#!/bin/bash
echo "=== Deployment Scaling Troubleshooting ==="

# Step 1: Current state
echo "1. Current deployment state:"
kubectl get deployment nginx-deploy
kubectl get pods -l app=nginx-deploy

# Step 2: Scale deployment
echo "2. Scaling deployment to 3 replicas:"
kubectl scale deployment nginx-deploy --replicas=3

# Step 3: Wait and check
echo "3. Waiting for scaling to complete..."
sleep 10
kubectl get deployment nginx-deploy

# Step 4: Check if scaling worked
CURRENT_REPLICAS=$(kubectl get deployment nginx-deploy -o jsonpath='{.status.readyReplicas}')
DESIRED_REPLICAS=$(kubectl get deployment nginx-deploy -o jsonpath='{.spec.replicas}')

echo "Desired replicas: $DESIRED_REPLICAS"
echo "Current ready replicas: $CURRENT_REPLICAS"

if [ "$CURRENT_REPLICAS" != "$DESIRED_REPLICAS" ]; then
    echo "4. Scaling issue detected. Troubleshooting..."
    
    # Check pods status
    echo "Pod status:"
    kubectl get pods -l app=nginx-deploy
    
    # Check events
    echo "Recent events:"
    kubectl get events --sort-by='.lastTimestamp' | grep nginx-deploy | head -5
    
    # Check node resources
    echo "Node resources:"
    kubectl top nodes 2>/dev/null || echo "Metrics server not available"
    
    # Check pending pods
    PENDING_PODS=$(kubectl get pods -l app=nginx-deploy --field-selector=status.phase=Pending --no-headers 2>/dev/null | wc -l)
    if [ "$PENDING_PODS" -gt 0 ]; then
        echo "Found $PENDING_PODS pending pods. Describing first pending pod:"
        PENDING_POD=$(kubectl get pods -l app=nginx-deploy --field-selector=status.phase=Pending --no-headers -o custom-columns=NAME:.metadata.name | head -1)
        kubectl describe pod $PENDING_POD
    fi
else
    echo "4. Scaling successful!"
fi
```

Step 8: Monitor and verify the fix
Check final state:


```sh
kubectl get deployment nginx-deploy
kubectl get pods -l app=nginx-deploy
kubectl rollout status deployment/nginx-deploy
```

Verify all pods are running:

```sh
kubectl get pods -l app=nginx-deploy -o wide
kubectl describe deployment nginx-deploy | grep -A 10 "Replicas"
```

Step 9: Additional monitoring commands

```sh
# Watch deployment scaling in real-time
kubectl get deployment nginx-deploy -w

# Monitor pod creation
kubectl get pods -l app=nginx-deploy -w

# Check deployment history
kubectl rollout history deployment/nginx-deploy

# Verify service connectivity (if service exists)
kubectl get service nginx-deploy 2>/dev/null || echo "No service found for deployment"
```

Expected Outcomes
Successful scaling:

✅ Deployment shows 3/3 ready replicas
✅ Three pods are running and ready
✅ No pending or failed pods
✅ Events show successful pod creation
Common issues and their indicators:

Resource constraints: Pods stuck in Pending state with events about insufficient CPU/memory
Image issues: Pods in ImagePullBackOff or ErrImagePull state
Node taints: Pods pending with events about node not accepting pods
Controller issues: Deployment stuck, no new ReplicaSet created

Key Troubleshooting Commands Summary

```sh
# Quick diagnosis
kubectl get deployment nginx-deploy
kubectl get pods -l app=nginx-deploy
kubectl get events --sort-by='.lastTimestamp' | head -10

# Scale deployment
kubectl scale deployment nginx-deploy --replicas=3

# Monitor scaling
kubectl rollout status deployment/nginx-deploy

# Troubleshoot issues
kubectl describe deployment nginx-deploy
kubectl describe pods -l app=nginx-deploy
kubectl top nodes
```

The key is to systematically check the deployment status, scale it, monitor the process, and troubleshoot any issues that prevent the scaling from completing successfully.


11. Create a Horizontal Pod Autoscaler (HPA) **api-hpa** for the deployment named **api-deployment** located in the **api namespace**.
The HPA should scale the deployment based on a custom metric named **requests_per_second**, targeting an average value of **1000** requests per second across all pods.
Set the minimum number of replicas to **1** and the maximum to **20**.

Note: Deployment named **api-deployment** is available in **api namespace**. Ignore errors due to the metric **requests_per_second** not being tracked in **metrics-server**

12. Configure the web-route to split traffic between **web-service** and **web-service-v2**.The configuration should ensure that **80%** of the traffic is routed to **web-service** and **20%** is routed to **web-service-v2**.

Note: web-gateway, web-service, and web-service-v2 have already been created and are available on the cluster.

13. One application, **webpage-server-01**, is currently deployed on the Kubernetes cluster using Helm. A new version of the application is available in a Helm chart located at ``` /root/new-version ```.

Validate this new Helm chart, then install it as a new release named **webpage-server-02**. After confirming the new release is installed, uninstall the old release **webpage-server-01**.

14. While preparing to install a CNI plugin on your kubernetes cluster, you would typically want to identify the pod CIDR networks for your nodes. Identify the pod CIDR network of controlplane node in the kubernetes cluster. Output the pod CIDR network following the format x.x.x.x/x to a file at ``` /root/pod-cidr.txt ```.












