# Lightning Lab 1

#### Reference

[kubeadm-upgrade](https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/)


You don’t understand anything until you learn it more than one way.

– Marvin Minsky

The beautiful thing about learning is that nobody can take it away from you.

Picture yourself as an indomitable power filled with positive attitude and faith that you are achieving your goals.

– Napolean Hill

You teach best what you most need to learn.

– Richard Bach

If you can't explain it simply you don't understand it well enough.

– Albert Einstein

 I am experienced enough to do this. I am knowledgeable enough to do this. I am prepared enough to do this. I am mature enough to do this. I am brave enough to do this.

– Alexandria Ocasio-Cortez

You are exactly where you need to be. You are not behind.

1. Upgrade the current version of kubernetes from 1.32.0 to 1.33.0 exactly using the kubeadm utility. Make sure that the upgrade is carried out one node at a time starting with the controlplane node. To minimize downtime, the deployment gold-nginx should be rescheduled on an alternate node before upgrading each node.


Upgrade controlplane node first and drain node node01 before upgrading it. Pods for gold-nginx should run on the controlplane node subsequently.

**Solution**

https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/

Select: Upgrading a kubeadm cluster from 1.32 to 1.33

Select [Changing the package repository](https://v1-33.docs.kubernetes.io/docs/tasks/administer-cluster/kubeadm/change-package-repository/)

Select [official announcement](https://v1-33.docs.kubernetes.io/blog/2023/08/15/pkgs-k8s-io-introduction/)

How to migrate to the Kubernetes community-owned repositories?

Replace the apt repository definition so that apt points to the new repository instead of the Google-hosted repository. Make sure to replace the Kubernetes minor version in the command below with the minor version that you're currently using:

```bash
echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.33/deb/ /" | sudo tee /etc/apt/sources.list.d/kubernetes.list
```

Download the public signing key for the Kubernetes package repositories. The same signing key is used for all repositories, so you can disregard the version in the URL:

```bash
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.33/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
```

Update the apt package index:

```bash
sudo apt-get update
```

Close the page and come back to the begining

Upgrading control plane nodes


```bash
# replace x in 1.33.x-* with the latest patch version
sudo apt-mark unhold kubeadm && \
sudo apt-get update && sudo apt-get install -y kubeadm='1.33.0-1.1*' && \
sudo apt-mark hold kubeadm
```

Verify that the download works and has the expected version:

```bash
kubeadm version
```

Verify the upgrade plan:

```bash
sudo kubeadm upgrade plan

sudo kubeadm upgrade apply v1.33.0
```

For the other control plane nodes

Same as the first control plane node but use:

```bash
sudo kubeadm upgrade node
```

instead of:

```bash
sudo kubeadm upgrade apply
```

Drain the node
Prepare the node for maintenance by marking it unschedulable and evicting the workloads:


```bash
# replace <node-to-drain> with the name of your node you are draining
kubectl drain controlplane --ignore-daemonsets
```

Upgrade kubelet and kubectl 
# replace x in 1.33.x-* with the latest patch version

```bash
sudo apt-mark unhold kubelet kubectl && \
sudo apt-get update && sudo apt-get install -y kubelet='1.33.0-1.1*' kubectl='1.33.0-1.1' && \
sudo apt-mark hold kubelet kubectl
```

Restart the kubelet:

```bash
sudo systemctl daemon-reload
sudo systemctl restart kubelet
```

Uncordon the node
Bring the node back online by marking it schedulable:

```bash
# replace <node-to-uncordon> with the name of your node
kubectl uncordon controlplane
```

**Now it's time to update the worker node with the same steps.**

Finally


**Kubernetes upgrade verification**

```bash
kubectl get nodes
kubectl get nodes -o wide
```

Check the kubelet version on each node

```bash
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.status.nodeInfo.kubeletVersion}{"\n"}{end}'
```

You should see v1.33.0 on all nodes.

```bash
kubeadm version
```

Check the API server version:

```bash
kubectl version --short
```

Both Client Version and Server Version should show v1.33.0.

Verify that the gold-nginx deployment is running on the correct node:

```bash
kubectl get pods -o wide -l app=gold-nginx
```

2. Print the names of all deployments in the admin2406 namespace in the following format:

DEPLOYMENT   CONTAINER_IMAGE   READY_REPLICAS   NAMESPACE

<deployment name>   <container image used>   <ready replica count>   <Namespace>
. The data should be sorted by the increasing order of the deployment name.


Example:

DEPLOYMENT   CONTAINER_IMAGE   READY_REPLICAS   NAMESPACE
deploy0   nginx:alpine   1   admin2406
Write the result to the file /opt/admin2406_data.

**answer2**

```bash
kubectl -n admin2406 get deployment -o custom-columns=DEPLOYMENT:.metadata.name,CONTAINER_IMAGE:.spec.template.spec.containers[].image,READY_REPLICAS:.status.readyReplicas,NAMESPACE:.metadata.namespace --sort-by=.metadata.name > /opt/admin2406_data
```

3. A kubeconfig file called **admin.kubeconfig** has been created in /root/CKA. There is something wrong with the configuration. Troubleshoot and fix it.

```yaml
# CKA/admin.kubeconfig
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1...
    server: https://controlplane:4380
  name: kubernetes
contexts:
- context:
    cluster: kubernetes
    user: kubernetes-admin
  name: kubernetes-admin@kubernetes
current-context: kubernetes-admin@kubernetes
kind: Config
preferences: {}
users:
- name: kubernetes-admin
  user:
    client-certificate-data: L...
    client-key-data: LS...
```

**Solution**

```bash
kubectl get pods --kubeconfig /root/CKA/admin.kubeconfig
```

Notice the error message.

Now look at the default kubeconfig for the correct setting.

```bash
cat ~/.kube/config
```

```yaml
# ~/.kube/config
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJT...
    server: https://controlplane:6443
  name: kubernetes
contexts:
- context:
    cluster: kubernetes
    user: kubernetes-admin
  name: kubernetes-admin@kubernetes
current-context: kubernetes-admin@kubernetes
kind: Config
preferences: {}
users:
- name: kubernetes-admin
  user:
    client-certificate-data: LS...
```

Make the correction

The API server port (4380) isn't the standard port (**6443**)

```bash
nano /root/CKA/admin.kubeconfig
```

Test

```bash
kubectl get pods --kubeconfig /root/CKA/admin.kubeconfig
```

**Explanation of the most common error in this context:**

In most cases, the error in a kubeconfig file is usually in one of these places:

The server field points to a hostname that doesn't exist or isn't resolvable (for example, https://controlplane:4380 should be a valid IP or DNS name accessible from where you run kubeconfig).
The certificate data (certificate-authority-data, client-certificate-data, client-key-data) may be corrupted, incomplete, or copied incorrectly.
The username, context, or cluster name doesn't match those defined in the rest of the file.
The API server port (4380) isn't the standard port (6443), unless your cluster is specifically configured that way.
In your example:

server: https://controlplane:4380
If the controlplane hostname isn't resolvable from where you run kubectl, you'll get a connection error.
If port 4380 is not correct for the API server, you will not be able to connect either.
How to fix this?

Change the server value to the correct IP or hostname of the API server and the correct port (usually **6443**):

server: https://<controlplane-IP-or-hostname>:6443
Verify that the certificate data is complete and copied correctly (not truncated).

Make sure the username, context, and cluster names are consistent.


4. Create a new deployment called **nginx-deploy**, with image **nginx:1.16** and 1 replica.
Next, upgrade the deployment to version 1.17 using rolling update and add the annotation message
Updated nginx image to 1.17.


Image: nginx:1.16

Task: Upgrade the version of the deployment to 1:17

**Solution**

4.1. Create initial deployment

```bash
kubectl create deployment nginx-deploy --image=nginx:1.16 --replicas=1
```

4.2. Verify deployment

```bash
kubectl get deployments
```

4.3. Update image to nginx:1.17 using rolling update

```bash
kubectl set image deployment/nginx-deploy nginx=nginx:1.17
```

4.4. Add annotationn required

```bash
kubectl annotate deployment nginx-deploy message='Updated nginx image to 1.17'
```

4.5. Verify that the update and annotation have been applied

```bash
kubectl describe deployment nginx-deploy
```

5. A new deployment called **alpha-mysql** has been deployed in the **alpha** namespace. However, the pods are not running. Troubleshoot and fix the issue. The deployment should make use of the persistent volume **alpha-pv** to be mounted at ``` /var/lib/mysql ``` and should use the environment variable ``` MYSQL_ALLOW_EMPTY_PASSWORD=1 ``` to make use of an empty root password.

**Solution**

Important: Do not alter the persistent volume.

Inspect the deployment to check the environment variable is set. Here I'm using yq which is like jq but for YAML to not have to view the entire deployment YAML, just the section beneath containers in the deployment spec.


```bash
kubectl get deployment -n alpha alpha-mysql  -o yaml | yq e .spec.template.spec.containers -
```

**yq**: This is a tool for processing YAML files from the command line (similar to jq for JSON).

**e** .spec.template.spec.containers: Specifically extracts the .spec.template.spec.containers field from YAML.

**-**: Indicates that the input is coming from stdin (the output of the previous command).

```yaml
- env:
    - name: MYSQL_ALLOW_EMPTY_PASSWORD
      value: "1"
  image: mysql:5.6
  imagePullPolicy: Always
  name: mysql
  ports:
    - containerPort: 3306
      protocol: TCP
  resources: {}
  terminationMessagePath: /dev/termination-log
  terminationMessagePolicy: File
  volumeMounts:
    - mountPath: /var/lib/mysql
      name: mysql-data

```

Find out why the deployment does not have minimum availability. We'll have to find out the name of the deployment's pod first, then describe the pod to see the error.

```bash
kubectl get pods -n alpha
kubectl describe pod -n alpha alpha-mysql-85765c4c56-7bdnr

Volumes:
  mysql-data:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  mysql-alpha-pvc
    ReadOnly:   false
  kube-api-access-xq6kq:

Events:
  Type     Reason            Age                From               Message
  ----     ------            ----               ----               -------
  Warning  FailedScheduling  28m                default-scheduler  0/2 nodes are available: persistentvolumeclaim "mysql-alpha-pvc" not found. preemption: 0/2 nodes are available: 2 Preemption is not helpful for scheduling.
  Warning  FailedScheduling  12m (x3 over 22m)  default-scheduler  0/2 nodes are available: persistentvolumeclaim "mysql-alpha-pvc" not found. preemption: 0/2 nodes are available: 2 Preemption is not helpful for scheduling.
```

```bash
kubectl get pv alpha-pv
```

**Reference**

https://kubernetes.io/docs/tasks/configure-pod-container/configure-persistent-volume-storage/#create-a-persistentvolumeclaim


```yaml
# mysql-alpha-pvc.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-alpha-pvc
  namespace: alpha
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
  storageClassName: slow
```

```bash
 k apply -f mysql-alpha-pvc.yaml 
```

```yaml
# alpha-pv.yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: alpha-pv
  namespace: alpha
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /mnt/data
  persistentVolumeReclaimPolicy: Retain
```
Important: Do not alter the persistent volume.


```yaml
# alpha-claim.yaml 
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: alpha-pvc
  namespace: alpha
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
  volumeName: alpha-pv
```

What could be the problem?
The deployment is using a PVC with a different name than the one defined in **alpha-claim.yaml**.
The PVC does not have the volumeName: alpha-pv field and is therefore not specifically bound to that PV.
The PVC is in a namespace other than **alpha**.
The **size** or accessModes do not match between the PV and PVC.
The deployment does not mount the PVC correctly in /var/lib/mysql.
The PV is already bound to another PVC or is in the Released/Failed state.

Recommended solution:
Make sure the PVC has:

The same name as the one used in the deployment (claimName: alpha-pvc).
The volumeName: alpha-pv field.
The correct namespace (alpha).

Check the status of the resources:

```bash
kubectl -n alpha get pvc
kubectl get pv
```

5.1. Check the status of the deployment and pods:

```bash
kubectl -n alpha get deployment alpha-mysql
kubectl -n alpha get pods
kubectl -n alpha describe pod alpha-mysql-85765c4c56-7bdnr >
```

5.2. Look for error messages related to volumes, PVCs, or environment variables.

Check if a **PersistentVolumeClaim** (PVC) exists and is linked to the PV alpha-pv:

```bash
kubectl -n alpha get pvc
kubectl get pv
```


6. Take the backup of ETCD at the location ``` /opt/etcd-backup.db ``` on the controlplane node.

**Step-by-Step Solution**
Access the controlplane node (if you're not already there).

Check the location of the etcdctl binary.
It's usually in **etcdctl**.

Get the etcd connection parameters.
You can find them in the etcd manifest:

```bash
cat /etc/kubernetes/manifests/etcd.yaml
```

Take the backup of ETCD at the location /opt/etcd-backup.db on the controlplane node.
This question is a bit poorly worded. It requires us to make a backup of etcd and store the backup at the given location.

Know that the certificates we need for authentication of etcdctl are located in ``` /etc/kubernetes/pki/etcd ```

```bash
ETCDCTL_API='3' etcdctl snapshot save \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key \
  /opt/etcd-backup.db
```

Verify that the ``` /opt/etcd-backup.db ``` file was created correctly:

```bash
ls -lh /opt/etcd-backup.db
```

**Tips for the exam**

Find the certificate paths and the endpoint address.

Run the backup.
Use the following command (adjust the paths if they are different on your cluster):

```bash
ETCDCTL_API=3 etcdctl snapshot save /opt/etcd-backup.db   --endpoints=https://127.0.0.1:2379   --cacert=/etc/kubernetes/pki/etcd/ca.crt   --cert=/etc/kubernetes/pki/etcd/server.crt   --key=/etc/kubernetes/pki/etcd/server.key
```

```bash
--endpoints: etcd address (usually https://127.0.0.1:2379)
--cacert, --cert, --key: Paths to certificates
```


7. Create a pod called **secret-1401** in the **admin1401** namespace using the busybox image. The container within the pod should be called **secret-admin** and should sleep for **4800** seconds.

The container should mount a read-only secret volume called secret-volume at the path ``` /etc/secret-volume ```. The secret being mounted has already been created for you and is called dotfile-secret.

**Solution**

Create a pod called secret-1401 in the admin1401 namespace using the busybox image....
The container within the pod should be called secret-admin and should sleep for 4800 seconds.

The container should mount a read-only secret volume called secret-volume at the path ``` /etc/secret-volume ```. The secret being mounted has already been created for you and is called **dotfile-secret**.

Use imperative command to get a starter manifest

```bash
kubectl run secret-1401 -n admin1401 --image busybox --dry-run=client -o yaml --command -- sleep 4800 > admin.yaml
```

```yaml
# admin.yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: secret-1401
  name: secret-1401
  namespace: admin1401
spec:
  volumes:
  - name: secret-volume
    secret:
      secretName: dotfile-secret
  containers:
  - command:
    - sleep
    - "4800"
    image: busybox
    name: secret-admin
    volumeMounts:
    - name: secret-volume
      readOnly: true
      mountPath: /etc/secret-volume
```

Steps to apply it:
Save the above manifest to a file, for example: admin.yaml.
Apply the manifest:

```bash
kubectl apply -f admin.yaml
```

Explanation:

The pod is created in the **admin1401** namespace.
The container is named **secret-admin**, uses the **busybox** image, and runs sleep **4800**.
The secret-volume volume mounts the secret **dotfile-secret** in ``` /etc/secret-volume ``` as read-only.

