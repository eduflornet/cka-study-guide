# Networking CNIs

An investment in knowledge pays the best interest.

– Benjamin Franklin

1. Welcome to the Kubernetes CNI Lab. In this lab, we are going to explore various Kubernetes CNIs and some differences between them.

Let's explore the current setup. Which CNI plugin is currently installed on the cluster?

Inspect the CNI plugins by checking the deployed system pods or the CNI configs under ``` /etc/cni/net.d/ ```.

```bash
 ls /etc/cni/net.d/
10-flannel.conflist
```

Run the following command to list the running pods:

```bash
k get pods -A
NAMESPACE      NAME                                   READY   STATUS    RESTARTS   AGE
default        backend                                1/1     Running   0          4m11s
default        frontend                               1/1     Running   0          4m11s
kube-flannel   kube-flannel-ds-vk5vq                  1/1     Running   0          9m2s
kube-system    coredns-7484cd47db-5qn7h               1/1     Running   0          9m2s
kube-system    coredns-7484cd47db-652zd               1/1     Running   0          9m2s
kube-system    etcd-controlplane                      1/1     Running   0          9m8s
kube-system    kube-apiserver-controlplane            1/1     Running   0          9m8s
kube-system    kube-controller-manager-controlplane   1/1     Running   0          9m8s
kube-system    kube-proxy-42gct                       1/1     Running   0          9m2s
kube-system    kube-scheduler-controlplane            1/1     Running   0          9m8s
```
Look for pods named kube-flannel-ds, which indicate that Flannel is installed

```bash
k -n kube-flannel describe pod kube-flannel-ds-vk5vq | grep -i cni
  install-cni-plugin:
    Image:         docker.io/flannel/flannel-cni-plugin:v1.2.0
    Image ID:      docker.io/flannel/flannel-cni-plugin@sha256:ca6779c6ad63b77af8a00151cefc08578241197b9a6fe144b0e55484bc52b852
      /opt/cni/bin/flannel
      /opt/cni/bin from cni-plugin (rw)
  install-cni:
      /etc/kube-flannel/cni-conf.json
      /etc/cni/net.d/10-flannel.conflist
      /etc/cni/net.d from cni (rw)
  cni-plugin:
    Path:          /opt/cni/bin
  cni:
    Path:          /etc/cni/net.d
  Normal  Pulled     11m   kubelet            Container image "docker.io/flannel/flannel-cni-plugin:v1.2.0" already present on machine
  Normal  Created    11m   kubelet            Created container: install-cni-plugin
  Normal  Started    11m   kubelet            Started container install-cni-plugin
  Normal  Created    11m   kubelet            Created container: install-cni
  Normal  Started    11m   kubelet            Started container install-cni
```

2. Two applications, frontend and backend, have been provisioned in the default namespace in the cluster. A network policy deny-backend has been provisioned to block traffic to the backend app. To test whether the policy is working or not, use the following command:

```bash
kubectl exec -it frontend -- curl -m 5 <BACKEND-POD-IP

kubectl exec -it frontend -- curl -m 5 172.17.0.5

>body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>
...
```

You need to replace <BACKEND-POD-IP> with the IP of the backend pod. To retrieve the IP:

```bash
k -n default get pods -o wide
NAME       READY   STATUS    RESTARTS   AGE   IP           NODE           NOMINATED NODE   READINESS GATES
backend    1/1     Running   0          10m   172.17.0.5   controlplane   <none>           <none>
frontend   1/1     Running   0          10m   172.17.0.4   controlplane   <none>           <none>
```

Can the frontend pod reach the backend pod?

**Yes**

3. As you have seen, the curl command from the frontend app to the backend app succeeded and returned the NGINX welcome page. However, this should not be the expected behavior given that we have a deny-backend network policy in place to prohibit this from happening.
Why did the curl command succeed?

**CNI does not support network policies**

Refer to the Flannel CNI documentation available at https://github.com/flannel-io/flannel

**Kubernetes does not block traffic by default**. A NetworkPolicy only affects Pods that match its podSelector. If no policy selects a Pod, that Pod remains unrestricted — it can send and receive traffic freely.

So even if you have a deny-backend policy, it will only block traffic to the backend Pod if:

The backend Pod is selected by the policy’s podSelector

The policy explicitly denies ingress from the frontend Pod

But here's the catch: If the backend Pod is not selected by any NetworkPolicy, or if the policy is not enforced by a CNI plugin that supports NetworkPolicies, then traffic will still flow.

```bash
k get networkpolicies -A
NAMESPACE   NAME           POD-SELECTOR   AGE
default     deny-backend   app=backend    20m
```

```bash
 k describe networkpolicies deny-backend 
Name:         deny-backend
Namespace:    default
Created on:   2025-08-29 11:22:46 +0000 UTC
Labels:       <none>
Annotations:  <none>
Spec:
  PodSelector:     app=backend
  Allowing ingress traffic:
    <none> (Selected pods are isolated for ingress connectivity)
  Not affecting egress traffic
  Policy Types: Ingress
```

4. Which of the following statements about Flannel's NetworkPolicy support is accurate?

Refer to the Flannel CNI documentation available at https://github.com/flannel-io/flannel

Flannel does not implement Kubernetes NetworkPolicies by default.

You can confirm this by checking the official Flannel documentation or testing it by applying NetworkPolicies.

5. The Flannel CNI does not support NetworkPolicies.

Delete Flannel CNI.

- Is the Flannel controller pod running?

- Is the Flannel configuration ConfigMap present?

- Is the Flannel configuration file available?

To clean up Flannel from the cluster, delete the relevant DaemonSet, ConfigMap, and configuration file.

To clean up the Flannel CNI from the cluster, delete the Flannel DaemonSet, configuration file, as well as the ConfigMap hosting the Flannel configuration:


```bash
kubectl delete daemonset -n kube-flannel kube-flannel-ds
kubectl delete cm kube-flannel-cfg -n kube-flannel
rm /etc/cni/net.d/10-flannel.conflist
```

6. Calico CNI is a CNI that supports NetworkPolicies. Let's install Calico CNI to enable NetworkPolicy enforcement.

[Refer to the Calico installation guide accessible via the links on the top-right corner of the terminal](https://docs.tigera.io/calico/latest/getting-started/kubernetes/quickstart). Make sure to download the custom resource definition file, and edit the CIDR field to 172.17.0.0/16 for pod communication to work successfully on the cluster.

Before proceeding, make sure the calico pods are in the running state:

watch 

```bash
kubectl get pods -A
```

You may notice some errors on the csi-node-driver pod deployed in the calico-system namespace after you deploy Calico. You can safely ignore these errors.

- Is the Calico DaemonSet running?

- Is the correct pod CIDR configured?

Refer to the Calico Installation guide linked above the terminal pane.

Download and apply the Calico manifest:

```bash
kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.29.3/manifests/tigera-operator.yaml
```
Download the custom resource definition yaml:

```bash
curl https://raw.githubusercontent.com/projectcalico/calico/v3.29.3/manifests/custom-resources.yaml -O
```
Edit the file to update the CIDR network:

```yaml
# custom-resources.yaml
...
apiVersion: operator.tigera.io/v1
kind: Installation
metadata:
  name: default
spec:
  # Configures Calico networking.
  calicoNetwork:
    ipPools:
    - name: default-ipv4-ippool
      blockSize: 26
      cidr: 172.17.0.0/16     # Update this field
      encapsulation: VXLANCrossSubnet
      natOutgoing: Enabled
      nodeSelector: all()
...
```

Apply the manifest file:

```bash
kubectl apply -f custom-resources.yaml
```

Now, wait until the calico pods are running:

```bash
watch k get pods -A
calico-apiserver   calico-apiserver-74dd778998-6gn76          1/1     Running                0          6m23s
calico-apiserver   calico-apiserver-74dd778998-vccxr          1/1     Running                0          6m23s
calico-system      calico-kube-controllers-76779d4c9f-f6cfl   1/1     Running                0          86s
calico-system      calico-node-6ptp6                          0/1     Running                0          24s
calico-system      calico-typha-59bcf5694c-pb94r              1/1     Running                0          87s
calico-system      csi-node-driver-5g74k                      1/2     CreateContainerError   0          86s
default            backend                                    1/1     Running                0          51m
default            frontend                                   1/1     Running                0          51m
kube-system        coredns-7484cd47db-5qn7h                   1/1     Running                0          56m
kube-system        coredns-7484cd47db-652zd                   1/1     Running                0          56m
kube-system        etcd-controlplane                          1/1     Running                0          56m
kube-system        kube-apiserver-controlplane                1/1     Running                0          56m
kube-system        kube-controller-manager-controlplane       1/1     Running                0          56m
kube-system        kube-proxy-42gct                           1/1     Running                0          56m
kube-system        kube-scheduler-controlplane                1/1     Running                0          56m
tigera-operator    tigera-operator-789496d6f5-4nqs6           1/1     Running                0          10m
```

You may ignore errors on the csi-node-driver pod deployed in the calico-system namespace.


7. After we deployed the Calico CNI, we redeployed the applications. Now, Let's re-test the connectivity from the frontend app to the backend app:

```bash
ubectl exec -it frontend -- curl -m 5 172.17.49.70
curl: (28) Connection timed out after 5000 milliseconds
command terminated with exit code 28
```

You need to replace <BACKEND-POD-IP> with the IP of the backend pod. To retrieve the IP:

```bash
kubectl get pods -o wide
```

Can the frontend pod reach the backend pod?

**there time out**

8. The curl command times out following the installation of the Calico CNI, which supports Network Policies. As a result, the deny-backend policy began to take effect after the deployed applications were restarted.




















