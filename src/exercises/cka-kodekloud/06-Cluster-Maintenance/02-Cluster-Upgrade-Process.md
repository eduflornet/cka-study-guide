# Cluster Upgrade Process

Question everything. Learn something. Answer nothing.

– Euripides

1. This lab tests your skills on upgrading a kubernetes cluster. We have a production cluster with applications running on it. Let us explore the setup first.

What is the current version of the cluster?

**Run:** ``` kubectl get nodes ``` and look at the VERSION

```bash
controlplane ~ ➜  k get nodes
NAME           STATUS   ROLES           AGE   VERSION
controlplane   Ready    control-plane   12m   v1.32.0
node01         Ready    <none>          11m   v1.32.0
```

2. How many nodes are part of this cluster?

Including controlplane and worker nodes

**2**

3. How many nodes can host workloads in this cluster?

Inspect the applications and taints set on the nodes.

**2** because both have not taints

```bash
k get nodes -o wide
NAME           STATUS   ROLES           AGE   VERSION   INTERNAL-IP       EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION    CONTAINER-RUNTIME
controlplane   Ready    control-plane   14m   v1.32.0   192.168.100.184   <none>        Ubuntu 22.04.5 LTS   5.15.0-1083-gcp   containerd://1.6.26
node01         Ready    <none>          13m   v1.32.0   192.168.139.24    <none>        Ubuntu 22.04.4 LTS   5.15.0-1083-gcp   containerd://1.6.26
```

**Check** the taints on both controlplane and node01. If none exists, then both nodes can host workloads.

**Solution**

By running the ``` kubectl describe node ``` command, we can see that neither nodes have taints.

```bash
root@controlplane:~# kubectl describe nodes  controlplane | grep -i taint
Taints:             <none>
root@controlplane:~# 
root@controlplane:~# kubectl describe nodes  node01 | grep -i taint
Taints:             <none>
root@controlplane:~# 
This means that both nodes have the ability to schedule workloads on them.
```

4. How many applications are hosted on the cluster?

Count the number of deployments in the default namespace.

**1** deployment.apps/blue

```bash
k get all -o wide
NAME                        READY   STATUS    RESTARTS   AGE     IP           NODE           NOMINATED NODE   READINESS GATES
pod/blue-69968556cc-h774g   1/1     Running   0          8m45s   172.17.1.3   node01         <none>           <none>
pod/blue-69968556cc-kztvk   1/1     Running   0          8m45s   172.17.0.5   controlplane   <none>           <none>
pod/blue-69968556cc-p97zw   1/1     Running   0          8m45s   172.17.1.4   node01         <none>           <none>
pod/blue-69968556cc-rlfz5   1/1     Running   0          8m45s   172.17.1.2   node01         <none>           <none>
pod/blue-69968556cc-svbgl   1/1     Running   0          8m45s   172.17.0.4   controlplane   <none>           <none>

NAME                  TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE     SELECTOR
service/kubernetes    ClusterIP   172.20.0.1      <none>        443/TCP        19m     <none>
service/red-service   NodePort    172.20.229.11   <none>        80:30080/TCP   8m45s   app=red

NAME                   READY   UP-TO-DATE   AVAILABLE   AGE     CONTAINERS   IMAGES         SELECTOR
deployment.apps/blue   5/5     5            5           8m45s   nginx        nginx:alpine   app=blue

NAME                              DESIRED   CURRENT   READY   AGE     CONTAINERS   IMAGES         SELECTOR
replicaset.apps/blue-69968556cc   5         5         5       8m45s   nginx        nginx:alpine   app=blue,pod-template-hash=69968556cc
```

5. You are tasked to upgrade the cluster. Users accessing the applications must not be impacted, and you cannot provision new VMs. What strategy would you use to upgrade the cluster?

In order to ensure minimum downtime, upgrade the cluster one node at a time, while moving the workloads to another node.
In the upcoming tasks you will get to practice how to do that.

**upgrade one node at a time while moving the workloads to the other**

6. What is the latest version available for an upgrade with the current version of the kubeadm tool installed?

Use the kubeadm tool

**Run** the ``` kubeadm upgrade plan ``` command

**v1.32.8**

```bash
kubeadm upgrade plan
[preflight] Running pre-flight checks.
[upgrade/config] Reading configuration from the "kubeadm-config" ConfigMap in namespace "kube-system"...
[upgrade/config] Use 'kubeadm init phase upload-config --config your-config.yaml' to re-upload it.
[upgrade] Running cluster health checks
[upgrade] Fetching available versions to upgrade to
[upgrade/versions] Cluster version: 1.32.0
[upgrade/versions] kubeadm version: v1.32.0
I0825 10:20:19.105804   21884 version.go:261] remote version is much newer: v1.33.4; falling back to: stable-1.32
[upgrade/versions] Target version: v1.32.8
[upgrade/versions] Latest version in the v1.32 series: v1.32.8

Components that must be upgraded manually after you have upgraded the control plane with 'kubeadm upgrade apply':
COMPONENT   NODE           CURRENT   TARGET
kubelet     controlplane   v1.32.0   v1.32.8
kubelet     node01         v1.32.0   v1.32.8

Upgrade to the latest version in the v1.32 series:

COMPONENT                 NODE           CURRENT    TARGET
kube-apiserver            controlplane   v1.32.0    v1.32.8
kube-controller-manager   controlplane   v1.32.0    v1.32.8
kube-scheduler            controlplane   v1.32.0    v1.32.8
kube-proxy                               1.32.0     v1.32.8
CoreDNS                                  v1.10.1    v1.11.3
etcd                      controlplane   3.5.16-0   3.5.16-0

You can now apply the upgrade by executing the following command:

        kubeadm upgrade apply v1.32.8

Note: Before you can perform this upgrade, you have to update kubeadm to v1.32.8.

_____________________________________________________________________


The table below shows the current state of component configs as understood by this version of kubeadm.
```

7. We will be upgrading the controlplane node first. Drain the controlplane node of workloads and mark it UnSchedulable

Controlplane Node: SchedulingDisabled

```bash
k drain controlplane --ignore-daemonsets
node/controlplane cordoned
Warning: ignoring DaemonSet-managed Pods: kube-flannel/kube-flannel-ds-bgbgk, kube-system/kube-proxy-mk64p
evicting pod kube-system/coredns-7484cd47db-pgf9x
evicting pod default/blue-69968556cc-svbgl
evicting pod default/blue-69968556cc-kztvk
evicting pod kube-system/coredns-7484cd47db-5clbh
pod/blue-69968556cc-svbgl evicted
pod/blue-69968556cc-kztvk evicted
pod/coredns-7484cd47db-pgf9x evicted
pod/coredns-7484cd47db-5clbh evicted
node/controlplane drained
```

8. Upgrade the controlplane components to exact version v1.33.0


Upgrade the kubeadm tool (if not already), then the controlplane components, and finally the kubelet. Practice referring to the Kubernetes documentation page.

- Controlplane Node Upgraded to v1.33.0

- Controlplane Kubelet Upgraded to v1.33.0

Make sure that the correct version of kubeadm is installed and then proceed to upgrade the controlplane node. Once this is done, upgrade the kubelet on the node.

**Solution**

To seamlessly transition from Kubernetes ``` v1.32 ``` to  ``` v1.33 ``` and gain access to the packages specific to the desired Kubernetes minor version, follow these essential steps during the upgrade process. This ensures that your environment is appropriately configured and aligned with the features and improvements introduced in Kubernetes ``` v1.33 ```.

On the controlplane node:

Use any text editor you prefer to open the file that defines the Kubernetes apt repository.

```bash
vim /etc/apt/sources.list.d/kubernetes.list
```

**Update** the version in the URL to the next available minor release, i.e v1.33.

```bash
deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.33/deb/ /
```
After making changes, save the file and exit from your text editor. Proceed with the next instruction.

```bash
apt update

apt-cache madison kubeadm
```

Based on the version information displayed by ``` apt-cache madison ```, it indicates that for Kubernetes ``` version 1.33.0 ```, the available package version is ``` 1.33.0-1.1 ```. Therefore, to install kubeadm for Kubernetes ```v1.33.0```, use the following command:

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

**Run** the following commands to refresh the systemd configuration and apply changes to the Kubelet service:

```bash
systemctl daemon-reload

systemctl restart kubelet
```

9. Mark the controlplane node as "Schedulable" again

```bash
k get nodes -o wide
NAME           STATUS                     ROLES           AGE   VERSION   INTERNAL-IP       EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION    CONTAINER-RUNTIME
controlplane   Ready,SchedulingDisabled   control-plane   48m   v1.33.0   192.168.100.184   <none>        Ubuntu 22.04.5 LTS   5.15.0-1083-gcp   containerd://1.6.26
node01         Ready                      <none>          48m   v1.32.0   192.168.139.24    <none>        Ubuntu 22.04.4 LTS   5.15.0-1083-gcp   containerd://1.6.26

```

**Use** the kubectl uncordon command
**Run** the command: kubectl uncordon controlplane

```bash
k get nodes -o wide
NAME           STATUS   ROLES           AGE   VERSION   INTERNAL-IP       EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION    CONTAINER-RUNTIME
controlplane   Ready    control-plane   50m   v1.33.0   192.168.100.184   <none>        Ubuntu 22.04.5 LTS   5.15.0-1083-gcp   containerd://1.6.26
node01         Ready    <none>          49m   v1.32.0   192.168.139.24    <none>        Ubuntu 22.04.4 LTS   5.15.0-1083-gcp   containerd://1.6.26
```

10. Next is the worker node. Drain the worker node of the workloads and mark it UnSchedulable

```bash
k drain node01 --ignore-daemonsets 
node/node01 cordoned
Warning: ignoring DaemonSet-managed Pods: kube-flannel/kube-flannel-ds-cxfdj, kube-system/kube-proxy-l4rxk
evicting pod kube-system/coredns-674b8bbfcf-pps98
evicting pod default/blue-69968556cc-k7w2m
evicting pod default/blue-69968556cc-rlfz5
evicting pod default/blue-69968556cc-cdjf5
evicting pod default/blue-69968556cc-p97zw
evicting pod kube-system/coredns-674b8bbfcf-gr25g
evicting pod default/blue-69968556cc-h774g
pod/blue-69968556cc-k7w2m evicted
pod/blue-69968556cc-cdjf5 evicted
pod/blue-69968556cc-h774g evicted
pod/blue-69968556cc-rlfz5 evicted
pod/blue-69968556cc-p97zw evicted
pod/coredns-674b8bbfcf-gr25g evicted
pod/coredns-674b8bbfcf-pps98 evicted
node/node01 drained
```

```bash
controlplane ~ ➜  k get nodes -o wide
NAME           STATUS                     ROLES           AGE   VERSION   INTERNAL-IP       EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION    CONTAINER-RUNTIME
controlplane   Ready                      control-plane   52m   v1.33.0   192.168.100.184   <none>        Ubuntu 22.04.5 LTS   5.15.0-1083-gcp   containerd://1.6.26
node01         Ready,SchedulingDisabled   <none>          52m   v1.32.0   192.168.139.24    <none>        Ubuntu 22.04.4 LTS   5.15.0-1083-gcp   containerd://1.6.26
```

11. Upgrade the worker node to the exact version v1.33.0

- Worker Node Upgraded to v1.33.0

- Worker Node Ready

**Hint** 
Make sure that the correct version of kubeadm is installed and then proceed to upgrade the node01 node. Once this is done, upgrade the kubelet on the node.

**Solution**

On the node01 node, run the following commands:

If you are on the controlplane node, run ``` ssh node01 ``` to log in to the node01.

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
```

```bash
apt-cache madison kubeadm
```

Based on the version information displayed by apt-cache madison, it indicates that for Kubernetes version 1.33.0, the available package version is 1.33.0-1.1. Therefore, to install kubeadm for Kubernetes v1.33.0, use the following command:

```bash
apt-get install kubeadm=1.33.0-1.1

# Upgrade the node 
kubeadm upgrade node
```

Now, upgrade the Kubelet version.

```bash
apt-get install kubelet=1.33.0-1.1
```bash

Run the following commands to refresh the systemd configuration and apply changes to the Kubelet service:

```bash
systemctl daemon-reload

systemctl restart kubelet
```

12. Remove the restriction and mark the worker node as schedulable again.

**Use** the kubectl uncordon command

```bash
kubectl uncordon node01
```



