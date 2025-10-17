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

4. Create a ConfigMap named **app-config** in the namespace **cm-namespace** with the following key-value pairs:

- ENV=production
- LOG_LEVEL=info

Then, modify the existing Deployment named **cm-webapp** in the same namespace to use the **app-config** ConfigMap by setting the environment variables **ENV** and **LOG_LEVEL** in the container from the ConfigMap.

5. Create a PriorityClass named **low-priority** with a value of **50000**. A pod named **lp-pod** exists in the namespace **low-priority**. Modify the pod to use the priority class you created. Recreate the pod if necessary.

6. We have deployed a new pod called **np-test-1** and a service called **np-test-service**. Incoming connections to this service are not working. Troubleshoot and fix it.
Create NetworkPolicy, by the name **ingress-to-nptest** that allows incoming connections to the service over port **80**.

Important: Don't delete any current objects deployed.

7. Taint the worker node node01 to be Unschedulable. Once done, create a pod called **dev-redis**, image **redis:alpine**, to ensure workloads are not scheduled to this worker node. Finally, create a new pod called **prod-redis** and image: **redis:alpine** with toleration to be scheduled on node01.

key: **env_type**, value: **production**, operator: **Equal** and effect: **NoSchedule**

8. A PersistentVolumeClaim named **app-pvc** exists in the namespace **storage-ns**, but it is not getting bound to the available PersistentVolume named **app-pv**.

Inspect both the PVC and PV and identify why the PVC is not being bound and fix the issue so that the PVC successfully binds to the PV. Do not modify the PV resource.

9. A kubeconfig file called **super.kubeconfig** has been created under ``` /root/CKA ```. There is something wrong with the configuration. Troubleshoot and fix it.

10. We have created a new deployment called **nginx-deploy**. Scale the deployment to **3 replicas**. Has the number of replicas increased? Troubleshoot and fix the issue.

11. Create a Horizontal Pod Autoscaler (HPA) **api-hpa** for the deployment named **api-deployment** located in the **api namespace**.
The HPA should scale the deployment based on a custom metric named **requests_per_second**, targeting an average value of **1000** requests per second across all pods.
Set the minimum number of replicas to **1** and the maximum to **20**.

Note: Deployment named **api-deployment** is available in **api namespace**. Ignore errors due to the metric **requests_per_second** not being tracked in **metrics-server**

12. Configure the web-route to split traffic between **web-service** and **web-service-v2**.The configuration should ensure that **80%** of the traffic is routed to **web-service** and **20%** is routed to **web-service-v2**.

Note: web-gateway, web-service, and web-service-v2 have already been created and are available on the cluster.

13. One application, **webpage-server-01**, is currently deployed on the Kubernetes cluster using Helm. A new version of the application is available in a Helm chart located at ``` /root/new-version ```.

Validate this new Helm chart, then install it as a new release named **webpage-server-02**. After confirming the new release is installed, uninstall the old release **webpage-server-01**.

14. While preparing to install a CNI plugin on your kubernetes cluster, you would typically want to identify the pod CIDR networks for your nodes. Identify the pod CIDR network of controlplane node in the kubernetes cluster. Output the pod CIDR network following the format x.x.x.x/x to a file at ``` /root/pod-cidr.txt ```.












