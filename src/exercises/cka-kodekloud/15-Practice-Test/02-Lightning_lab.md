# Lightning Lab 1

#### Reference

https://kubernetes.io/docs/reference/setup-tools/kubeadm/kubeadm-upgrade/


You don’t understand anything until you learn it more than one way.

– Marvin Minsky

1. Upgrade the current version of kubernetes from 1.32.0 to 1.33.0 exactly using the kubeadm utility. Make sure that the upgrade is carried out one node at a time starting with the controlplane node. To minimize downtime, the deployment gold-nginx should be rescheduled on an alternate node before upgrading each node.


Upgrade controlplane node first and drain node node01 before upgrading it. Pods for gold-nginx should run on the controlplane node subsequently.

```bash
kubeadm upgrade plan

kubeadm upgrade apply v1.33.0
```

To seamlessly transition from Kubernetes v1.32 to v1.33 and gain access to the packages specific to the desired Kubernetes minor version, follow these essential steps during the upgrade process. This ensures that your environment is appropriately configured and aligned with the features and improvements introduced in Kubernetes v1.33.

On the controlplane node:

Use any text editor you prefer to open the file that defines the Kubernetes apt repository.

```bash
vim /etc/apt/sources.list.d/kubernetes.list
```
Update the version in the URL to the next available minor release, i.e v1.33.

```bash
deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.33/deb/ /
```

After making changes, save the file and exit from your text editor. Proceed with the next instruction.

```bash
apt update

apt-cache madison kubeadm
```

Based on the version information displayed by apt-cache madison, it indicates that for Kubernetes version 1.33.0, the available package version is 1.33.0-1.1. Therefore, to install kubeadm for Kubernetes v1.33.0, use the following command:

```bash
apt-get install kubeadm=1.33.0-1.1
```

Run the following command to upgrade the Kubernetes cluster.

```bash
kubeadm upgrade plan v1.33.0

kubeadm upgrade apply v1.33.0
```

Note that the above steps can take a few minutes to complete.

Now, upgrade the Kubelet version. Also, mark the node (in this case, the "controlplane" node) as schedulable.

```bash
apt-get install kubelet=1.33.0-1.1
```
Run the following commands to refresh the systemd configuration and apply changes to the Kubelet service:

```bash
systemctl daemon-reload

systemctl restart kubelet
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
kubectl -n admin2406 get deployments -o=jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.spec.template.spec.containers[0].image}{"\t"}{.status.readyReplicas}{"\t"}{.metadata.namespace}{"\n"}{end}' | sort > /opt/admin2406_data
```

With the header as in the example, use:

```bash
echo -e "DEPLOYMENT\tCONTAINER_IMAGE\tREADY_REPLICAS\tNAMESPACE" > /opt/admin2406_data
kubectl -n admin2406 get deployments -o=jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.spec.template.spec.containers[0].image}{"\t"}{.status.readyReplicas}{"\t"}{.metadata.namespace}{"\n"}{end}' | sort >> /opt/admin2406_data
```



3. A kubeconfig file called admin.kubeconfig has been created in /root/CKA. There is something wrong with the configuration. Troubleshoot and fix it.

```yaml
# CKA/admin.kubeconfig
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURCVENDQWUyZ0F3SUJBZ0lJQ0FHVENLRjRNYzB3RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TlRBNU1Ua3hPREk0TkROYUZ3MHpOVEE1TVRjeE9ETXpORE5hTUJVeApFekFSQmdOVkJBTVRDbXQxWW1WeWJtVjBaWE13Z2dFaU1BMEdDU3FHU0liM0RRRUJBUVVBQTRJQkR3QXdnZ0VLCkFvSUJBUURJYW5HTWFhVDdrQW1jV1AvMEgxMlZWZnhlTWpBeXlDYWlpU3BNK2NFeklOaXB6NlFDUExPZnVXdWcKRE16QnlhRUx3MnVLY2s0YmIveHZ4N3JPYUJBZUZ2UHN6VXZUQVE3OVNUQVJDNWZFaUYwZldkSlVKL0ZrNUhBOQp5NUNVUnB4MDFIN1h3R2wwNlBUZ3pFWk9UcXNZNE9TMUdKeit5RkdKK3MreDNiUHZlTFh5K3FIbDJCSU9DMWR0CmFSTUNGc3FGYlNkcHMxVStDLzZMcnhWMmZralk0TlZVS3dObmUzUlpuZlQxTlhmV1Q3TnI5NmdaNWhzSStFcFQKTktjeUNNUGZWSXhCK3RUOHM4UkFPcS9zR1VoT1I1L1NBOHBib3dPYmpvUkVSNzBBNDVnUFgxNHFBUVFCOUVCVQpycEZTdUJNQkZ1SGhLcFNqelZHQWljb05pbU12QWdNQkFBR2pXVEJYTUE0R0ExVWREd0VCL3dRRUF3SUNwREFQCkJnTlZIUk1CQWY4RUJUQURBUUgvTUIwR0ExVWREZ1FXQkJUTmk3STBPR2JBNUFBUmRlbUlYRlBMdWFONXlqQVYKQmdOVkhSRUVEakFNZ2dwcmRXSmxjbTVsZEdWek1BMEdDU3FHU0liM0RRRUJDd1VBQTRJQkFRQ2NyYy9WZnpDOApLUnp0WEplOUFISkxwd0xTYTlETHBFVUNyTVFpczg5QUx0Z0JxVGRsOUdZdXE1NkZYR0s3T1htckpZSTlsR3VmCmVsMTlpQitqVGJ1MHhRVW5KOTNVS2dBWStSNHN1QjFxbXQ0QzlwUnIrN1NMbkZzbE5QZnBrSzdrRmdGdjJONjEKcXdzcmJ1SHRjRGJyak05OFNoZytYZ1MwZDRiMTY0NDFvdVJOcVVXMzZQTTg4QWtFT0w3c3lGL3RlTDBBTE82ZwpSV093ZS9Bb1JyS2s4dmhhMnpjcnpUZHYxZ0NqK25mczV3alVKWDdrQi9aNUVzTUQ1K1NzTjR2d2VoOHdJVE1FCjlONktoNy9QK2xpODhpcThHaDEwNTlVVEpVVklQMkFmcUh1SXRZWDU1L0g5MkhLT0h5YXgzKzU3WVh4ZFk3cGwKWFU4K1dmbmZjaXN4Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
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
    client-key-data: LS....
```

**Solution**
Explanation of the most common error in this context:

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

4. Create a new deployment called nginx-deploy, with image nginx:1.16 and 1 replica.
Next, upgrade the deployment to version 1.17 using rolling update and add the annotation message
Updated nginx image to 1.17.


Image: nginx:1.16

Task: Upgrade the version of the deployment to 1:17

**Solution**

1. Create initial deployment

```bash
kubectl create deployment nginx-deploy --image=nginx:1.16 --replicas=1
```

2. Verify deployment

```bash
kubectl get deployments
```

3. Update image to nginx:1.17 using rolling update

```bash
kubectl set image deployment/nginx-deploy nginx-deploy=nginx:1.17
```

4. Add annotationn required

```bash
kubectl annotate deployment nginx-deploy message='Updated nginx image to 1.17'
```

5. Verify that the update and annotation have been applied

```bash
kubectl describe deployment nginx-deploy
```

5. A new deployment called **alpha-mysql** has been deployed in the **alpha** namespace. However, the pods are not running. Troubleshoot and fix the issue. The deployment should make use of the persistent volume **alpha-pv** to be mounted at ``` /var/lib/mysql ``` and should use the environment variable ``` MYSQL_ALLOW_EMPTY_PASSWORD=1 ``` to make use of an empty root password.


Important: Do not alter the persistent volume.

6. Take the backup of ETCD at the location ``` /opt/etcd-backup.db ``` on the controlplane node.

7. Create a pod called **secret-1401** in the **admin1401** namespace using the busybox image. The container within the pod should be called **secret-admin** and should sleep for **4800** seconds.

The container should mount a read-only secret volume called secret-volume at the path ``` /etc/secret-volume ```. The secret being mounted has already been created for you and is called dotfile-secret.







