# Admission Controllers

One big reason for a winning attitude is that you will take the necessary steps and not quit when the going gets difficult.

– Don M.Green

1. What is not a function of admission controller?

Hint:
Admission controllers in Kubernetes are components that intercept requests to the API server after authentication and authorization, but before the object is persisted. They are used to enforce policies, validate configurations, and perform additional operations on resources being created or modified. However, they do not handle user authentication.

2. Which admission controller is not enabled by default?

3. Which admission controller is enabled in this cluster which is normally disabled?

Check enable-admission-plugins in /etc/kubernetes/manifests/kube-apiserver.yaml

```bash
cat /etc/kubernetes/manifests/kube-apiserver.yaml | grep enable-admission-plugins
- --enable-admission-plugins=NodeRestriction
```

4. Create an nginx pod in the blue namespace. Please note that the blue namespace does not currently exist. Do not create the blue namespace at this time.

Execute the following command to deploy a pod using the nginx image within the blue namespace:

kubectl run nginx --image nginx -n blue

Note : Expected error in execution

5. The previous step failed due to the NamespaceExists admission controller being enabled in Kubernetes. This controller rejects requests for namespaces that do not already exist. Therefore, to automatically create a namespace that does not exist, you should enable the NamespaceAutoProvision admission controller.


Enable the NamespaceAutoProvision admission controller

Note: Once you update kube-apiserver yaml file, please wait for a few minutes for the kube-apiserver to restart completely.

Hint:
Edit the API server manifest file:
```/etc/kubernetes/manifests/kube-apiserver.yaml ``` on the control plane node.
Add NamespaceAutoProvision to the ``` --enable-admission-plugins ``` list.
Important: Use the exact capitalization: NamespaceAutoProvision.
The line should look like:
``` --enable-admission-plugins=NodeRestriction,NamespaceAutoProvision ```  
(Add to the existing list if there are other plugins.)

Save the file and exit.
Wait a few minutes for the kube-apiserver pod to automatically restart and pick up the new configuration.
Check pod status with:
kubectl get pods -n kube-system

Solution:

Add NamespaceAutoProvision admission controller to the ``` --enable-admission-plugins ``` list
in ``` /etc/kubernetes/manifests/kube-apiserver.yaml ```

It should look like this:

```yaml
spec:
  containers:
    - name: kube-apiserver
      image: registry.k8s.io/kube-apiserver:v1.33.0
      imagePullPolicy: IfNotPresent
      command:
        - kube-apiserver
        - --enable-admission-plugins=NodeRestriction,NamespaceAutoProvision
        # ...other flags...
```
After saving the file, the API server will automatically restart and pick up this configuration.
Check pod status with:

```bash
kubectl get pods -n kube-system
NAME                                   READY   STATUS    RESTARTS        AGE
coredns-7484cd47db-cktsp               1/1     Running   0               26m
coredns-7484cd47db-npnmw               1/1     Running   0               26m
etcd-controlplane                      1/1     Running   0               26m
kube-apiserver-controlplane            1/1     Running   0               2m27s
kube-controller-manager-controlplane   1/1     Running   1 (3m9s ago)    26m
kube-proxy-txff6                       1/1     Running   0               26m
kube-scheduler-controlplane            1/1     Running   1 (3m10s ago)   26m
```
Note: This command may not yield immediate results due to the updated configuration of the kube-apiserver.

6. Now, let's run the nginx pod in blue namespace again and check if it succeeds.

```bash
kubectl run nginx --image nginx -n blue
pod/nginx created
```

7. Please be aware that the NamespaceExists and NamespaceAutoProvision admission controllers have been deprecated and are now succeeded by the **NamespaceLifecycle** admission controller.

The **NamespaceLifecycle** admission controller ensures that any requests made to a non-existent namespace are rejected, and it safeguards the default namespaces, including default, kube-system, and kube-public, from being deleted.

8. A file named myclaim.yaml is available in your working directory. Please examine its contents and apply it to the cluster.

File Location: ``` /root/myclaim.yaml ```

Run command :

```bash
kubectl apply -f myclaim.yaml
```

```yaml
# myclaim.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: myclaim
  namespace: default
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 0.5Gi
```

After applying myclaim.yaml, which StorageClass was assigned to the PVC myclaim?

Hint:
Check the output of kubectl get pvc myclaim -o yaml and look under spec.storageClassName.

Note:
The default StorageClass in this cluster is configured with volumeBindingMode: WaitForFirstConsumer. As a result, the Persistent Volume Claim (PVC) will remain in the Pending state until a Pod that uses this PVC is created and scheduled.

```bash
kubectl get pvc myclaim -o yaml
```
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"v1","kind":"PersistentVolumeClaim","metadata":{"annotations":{},"name":"myclaim","namespace":"default"},"spec":{"accessModes":["ReadWriteOnce"],"resources":{"requests":{"storage":"0.5Gi"}}}}
  creationTimestamp: "2025-08-21T20:38:08Z"
  finalizers:
  - kubernetes.io/pvc-protection
  name: myclaim
  namespace: default
  resourceVersion: "2840"
  uid: 3e8bfa43-3e17-48cb-8432-2a4024ab0057
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 512Mi
  storageClassName: default
  volumeMode: Filesystem
status:
  phase: Pending
  ```
Solution:

Since myclaim.yaml does not specify a storageClassName, the default StorageClass is automatically assigned by the **DefaultStorageClass** admission controller.

Note:
The default StorageClass in this cluster is configured with **volumeBindingMode: WaitForFirstConsumer**. As a result, the Persistent Volume Claim (PVC) will remain in the Pending state until a Pod that uses this PVC is created and scheduled.

9. To disable the **DefaultStorageClass** admission controller, please edit the file located at ``` /etc/kubernetes/manifests/kube-apiserver.yaml ```. In the kube-apiserver command section, add the following line:

``` --disable-admission-plugins=DefaultStorageClass ```
After making this change, save the file and allow a few minutes for the kube-apiserver to restart.

Note: After implementing this change, Kubernetes will cease to automatically assign the default **StorageClass** to new **PersistentVolumeClaims** (PVCs) that do not explicitly define a storageClassName. However, to fully prevent automatic provisioning, ensure that no StorageClass is marked as default in your cluster.

Solution:

Disable the **DefaultStorageClass** Admission Controller:

Edit ``` /etc/kubernetes/manifests/kube-apiserver.yaml ```.
In the command: section for the kube-apiserver container, add:
  ``` - --disable-admission-plugins=DefaultStorageClass ```
Example snippet:

```yaml
  spec:
    containers:
      - name: kube-apiserver
        command:
          - kube-apiserver
          # ...other flags...
          - --disable-admission-plugins=DefaultStorageClass
```
Save the file and wait a few minutes for the kube-apiserver to restart.

```yaml
# kube-apiserver.yaml
apiVersion: v1
kind: Pod
metadata:
  annotations:
    kubeadm.kubernetes.io/kube-apiserver.advertise-address.endpoint: 192.168.59.162:6443
  creationTimestamp: null
  labels:
    component: kube-apiserver
    tier: control-plane
  name: kube-apiserver
  namespace: kube-system
spec:
  containers:
  - command:
    - kube-apiserver
    - --advertise-address=192.168.59.162
    - --allow-privileged=true
    - --authorization-mode=Node,RBAC
    - --client-ca-file=/etc/kubernetes/pki/ca.crt
    - --enable-admission-plugins=NodeRestriction
    - --enable-bootstrap-token-auth=true
    - --etcd-cafile=/etc/kubernetes/pki/etcd/ca.crt
    - --etcd-certfile=/etc/kubernetes/pki/apiserver-etcd-client.crt
    - --etcd-keyfile=/etc/kubernetes/pki/apiserver-etcd-client.key
    - --etcd-servers=https://127.0.0.1:2379
    - --kubelet-client-certificate=/etc/kubernetes/pki/apiserver-kubelet-client.crt
    - --kubelet-client-key=/etc/kubernetes/pki/apiserver-kubelet-client.key
    - --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname
    - --proxy-client-cert-file=/etc/kubernetes/pki/front-proxy-client.crt
    - --proxy-client-key-file=/etc/kubernetes/pki/front-proxy-client.key
    - --requestheader-allowed-names=front-proxy-client
    - --requestheader-client-ca-file=/etc/kubernetes/pki/front-proxy-ca.crt
    - --requestheader-extra-headers-prefix=X-Remote-Extra-
    - --requestheader-group-headers=X-Remote-Group
    - --requestheader-username-headers=X-Remote-User
    - --secure-port=6443
    - --service-account-issuer=https://kubernetes.default.svc.cluster.local
    - --service-account-key-file=/etc/kubernetes/pki/sa.pub
    - --service-account-signing-key-file=/etc/kubernetes/pki/sa.key
    - --service-cluster-ip-range=172.20.0.0/16
    - --tls-cert-file=/etc/kubernetes/pki/apiserver.crt
    - --tls-private-key-file=/etc/kubernetes/pki/apiserver.key
    - --enable-admission-plugins=NodeRestriction,NamespaceAutoProvision
    - --disable-admission-plugins=DefaultStorageClass
    image: registry.k8s.io/kube-apiserver:v1.33.0
    imagePullPolicy: IfNotPresent 
    livenessProbe:
      failureThreshold: 8
      httpGet:
        host: 192.168.59.162
        path: /livez
        port: 6443
        scheme: HTTPS
      initialDelaySeconds: 10
      periodSeconds: 10
      timeoutSeconds: 15
    name: kube-apiserver
    readinessProbe:
      failureThreshold: 3
      httpGet:
        host: 192.168.59.162
        path: /readyz
        port: 6443
        scheme: HTTPS
      periodSeconds: 1
      timeoutSeconds: 15
    resources:
      requests:
        cpu: 250m
    startupProbe:
      failureThreshold: 24
      httpGet:
        host: 192.168.59.162
        path: /livez
        port: 6443
        scheme: HTTPS
      initialDelaySeconds: 10
      periodSeconds: 10
      timeoutSeconds: 15
    volumeMounts:
    - mountPath: /etc/ssl/certs
      name: ca-certs
      readOnly: true
    - mountPath: /etc/ca-certificates
      name: etc-ca-certificates
      readOnly: true
    - mountPath: /etc/kubernetes/pki
      name: k8s-certs
      readOnly: true
    - mountPath: /usr/local/share/ca-certificates
      name: usr-local-share-ca-certificates
      readOnly: true
    - mountPath: /usr/share/ca-certificates
      name: usr-share-ca-certificates
      readOnly: true
  hostNetwork: true
  priority: 2000001000
  priorityClassName: system-node-critical
  securityContext:
    seccompProfile:
      type: RuntimeDefault
  volumes:
  - hostPath:
      path: /etc/ssl/certs
      type: DirectoryOrCreate
    name: ca-certs
  - hostPath:
      path: /etc/ca-certificates
      type: DirectoryOrCreate
    name: etc-ca-certificates
  - hostPath:
      path: /etc/kubernetes/pki
      type: DirectoryOrCreate
    name: k8s-certs
  - hostPath:
      path: /usr/local/share/ca-certificates
      type: DirectoryOrCreate
       name: usr-local-share-ca-certificates
  - hostPath:
      path: /usr/share/ca-certificates
      type: DirectoryOrCreate
    name: usr-share-ca-certificates
status: {}
```

10. Now that the **DefaultStorageClass** admission controller has been disabled, please revisit the original Persistent Volume Claim (PVC) named myclaim to observe the new behavior.

Delete the existing PVC and reapply it to observe the effects of disabling the default StorageClass.

Delete the existing PVC named myclaim:

```bash
kubectl delete pvc myclaim
```

Reapply the same manifest:

```bash
kubectl apply -f myclaim.yaml
```

Check the status of the PVC:

```bash
kubectl get pvc myclaim
kubectl get pvc myclaim -o yaml 
```

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"v1","kind":"PersistentVolumeClaim","metadata":{"annotations":{},"name":"myclaim","namespace":"default"},"spec":{"accessModes":["ReadWriteOnce"],"resources":{"requests":{"storage":"0.5Gi"}}}}
  creationTimestamp: "2025-08-21T20:54:12Z"
  finalizers:
  - kubernetes.io/pvc-protection
  name: myclaim
  namespace: default
  resourceVersion: "4098"
  uid: eda8d324-649c-461a-9e50-4fea47395a3d
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 512Mi
  volumeMode: Filesystem
status:
  phase: Pending
```

Note: Disabling the DefaultStorageClass admission controller removes the automatic assignment of the default StorageClass. The StorageClass that was previously marked as default has already been patched.

Hint:
After disabling the DefaultStorageClass admission controller, new PVCs without a storageClassName will not be dynamically provisioned. Check if the PVC remains in the Pending state.

If a StorageClass is still marked as default in your cluster, Kubernetes may continue to assign it to PVCs even after disabling the admission controller. To fully prevent automatic assignment, ensure that no StorageClass is annotated as default (storageclass.kubernetes.io/is-default-class: "true").
You can check and remove the default annotation with:

11. Since the kube-apiserver is running as pod you can check the process to see enabled and disabled plugins.

```bash
ps -ef | grep kube-apiserver | grep admission-plugins
root       25322   25198  0 20:48 ?        00:00:18 kube-apiserver --advertise-address=192.168.59.162 --allow-privileged=true --authorization-mode=Node,RBAC --client-ca-file=/etc/kubernetes/pki/ca.crt --enable-admission-plugins=NodeRestriction --enable-bootstrap-token-auth=true --etcd-cafile=/etc/kubernetes/pki/etcd/ca.crt --etcd-certfile=/etc/kubernetes/pki/apiserver-etcd-client.crt --etcd-keyfile=/etc/kubernetes/pki/apiserver-etcd-client.key --etcd-servers=https://127.0.0.1:2379 --kubelet-client-certificate=/etc/kubernetes/pki/apiserver-kubelet-client.crt --kubelet-client-key=/etc/kubernetes/pki/apiserver-kubelet-client.key --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname --proxy-client-cert-file=/etc/kubernetes/pki/front-proxy-client.crt --proxy-client-key-file=/etc/kubernetes/pki/front-proxy-client.key --requestheader-allowed-names=front-proxy-client --requestheader-client-ca-file=/etc/kubernetes/pki/front-proxy-ca.crt --requestheader-extra-headers-prefix=X-Remote-Extra- --requestheader-group-headers=X-Remote-Group --requestheader-username-headers=X-Remote-User --secure-port=6443 --service-account-issuer=https://kubernetes.default.svc.cluster.local --service-account-key-file=/etc/kubernetes/pki/sa.pub --service-account-signing-key-file=/etc/kubernetes/pki/sa.key --service-cluster-ip-range=172.20.0.0/16 --tls-cert-file=/etc/kubernetes/pki/apiserver.crt --tls-private-key-file=/etc/kubernetes/pki/apiserver.key --enable-admission-plugins=NodeRestriction,NamespaceAutoProvision --disable-admission-plugins=DefaultStorageClass
```
