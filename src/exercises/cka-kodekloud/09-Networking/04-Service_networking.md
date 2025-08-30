# Service Networking

Always bear in mind that your own resolution to success is more important than any other one thing.

– Abraham Lincoln

IT'S THE will, NOT THE skill..

– Jim Bunney

You teach best what you most need to learn.

– Richard Bach


1. What is the IP address and subnet mask assigned to the controlplane node's primary network interface?

**192.168.59.181/32**

Run the command:

```bash
 ip addr show eth0 
3: eth0@if33293: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1410 qdisc noqueue state UP group default 
    link/ether 32:d1:51:8e:c5:f4 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 192.168.59.181/32 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::30d1:51ff:fe8e:c5f4/64 scope link 
       valid_lft forever preferred_lft forever
```

 address and subnet mask assigned to the eth0 interface.

 2. What is the range of IP addresses configured for PODs on this cluster?

 **cluster-cidr=172.17.0.0/16**

 The network is configured with canal. Check the canal pods logs using the command ``` kubectl logs <canal-pod-name> -n kube-system ``` and look for default IPv4 pool range.

Or

Check the ``` kube-controller-manager.yaml ``` manifest file and look for the ``` --cluster-cidr ``` parameter, which defines the IP range assigned to Pods in the cluster.

```bash
k -n kube-system get pod kube-controller-manager-controlplane -o yaml
```

```yaml
spec:
  containers:
  - command:
    - kube-controller-manager
    - --allocate-node-cidrs=true
    - --authentication-kubeconfig=/etc/kubernetes/controller-manager.conf
    - --authorization-kubeconfig=/etc/kubernetes/controller-manager.conf
    - --bind-address=127.0.0.1
    - --client-ca-file=/etc/kubernetes/pki/ca.crt
    - --cluster-cidr=172.17.0.0/16
```

```bash
cat /etc/kubernetes/manifests/kube-controller-manager.yaml   | grep cluster-cidr
```

3. What is the IP Range configured for the services within the cluster?

**--service-cluster-ip-range=172.20.0.0/16**

```bash
k -n kube-system get pod kube-controller-manager-controlplane -o yaml
```

```yaml
- --service-cluster-ip-range=172.20.0.0/16
```

Or

Inspect the setting on kube-api server by running on command:

```bash
cat /etc/kubernetes/manifests/kube-apiserver.yaml   | grep cluster-ip-range
```

4. How many kube-proxy pods are deployed in this cluster?

kube-proxy-s2g87
kube-proxy-vbr69


```bash
k -n kube-system get pods
NAME                                       READY   STATUS    RESTARTS   AGE
calico-kube-controllers-5745477d4d-jkpxd   1/1     Running   0          92m
canal-bkz25                                2/2     Running   0          92m
canal-qnt62                                2/2     Running   0          92m
coredns-7484cd47db-cjp9t                   1/1     Running   0          92m
coredns-7484cd47db-nl7q5                   1/1     Running   0          92m
etcd-controlplane                          1/1     Running   0          92m
kube-apiserver-controlplane                1/1     Running   0          92m
kube-controller-manager-controlplane       1/1     Running   0          92m
kube-proxy-s2g87                           1/1     Running   0          92m
kube-proxy-vbr69                           1/1     Running   0          92m
kube-scheduler-controlplane                1/1     Running   0          92m
```

5. What type of proxy is the kube-proxy configured to use?

**iptables**

Check the logs of the kube-proxy pods. Run the command: 

```bash
k -n kube-system logs kube-proxy-s2g87 

ie iptables.localhostNodePorts (--iptables-localhost-nodeports) or set nodePortAddresses (--nodeport-addresses) to filter loopback addresses" ipFamily="IPv4"
```

6. How does this Kubernetes cluster ensure that a kube-proxy pod runs on all nodes in the cluster?
Inspect the kube-proxy pods and try to identify how they are deployed.

**using deamonsets**

Run the command:

```bash
# Get deamonsets in cluster
kubectl get ds -n kube-system
NAME         DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR            AGE
canal        2         2         2       2            2           kubernetes.io/os=linux   106m
kube-proxy   2         2         2       2            2           kubernetes.io/os=linux   106m
```















