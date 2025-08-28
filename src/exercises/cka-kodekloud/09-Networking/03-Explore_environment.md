# Explore Environment

Success doesn’t come to you, you go to it.

– Marva Collins

1. How many nodes are part of this cluster?

Including the controlplane and worker nodes.

**Two**

```bash
controlplane ~ ➜  k get nodes
NAME           STATUS   ROLES           AGE   VERSION
controlplane   Ready    control-plane   20m   v1.33.0
node01         Ready    <none>          19m   v1.33.0
```

2. What is the Internal IP address of the controlplane node in this cluster?

**192.168.114.219**

```bash
Addresses:
  InternalIP:  192.168.114.219
  Hostname:    controlplane
```

```bash
k describe node controlplane | grep -i internalip
  InternalIP:  192.168.114.219
```

3. What is the network interface configured for cluster connectivity on the controlplane node?

Run the ip a / ip link command and identify the interface.

Run:

```bash
kubectl get nodes -o wide 
```

to see the IP address assigned to the controlplane node.

```bash
 k get nodes controlplane -o wide
NAME           STATUS   ROLES           AGE   VERSION   INTERNAL-IP       EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION    CONTAINER-RUNTIME
controlplane   Ready    control-plane   33m   v1.33.0   192.168.114.219   <none>        Ubuntu 22.04.5 LTS   5.15.0-1083-gcp   containerd://1.6.26
```

In this case, the internal IP address used for node to node communication is **192.168.114.219**.

Important Note : The result above is just an example, the node IP address will vary for each lab.

Next, find the network interface to which this IP is assigned by making use of the ip a command:

```bash
ip a | grep -B2 192.168.114.219
3: eth0@if7762: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1410 qdisc noqueue state UP group default 
    link/ether f6:d3:58:a2:0e:7a brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 192.168.114.219/32 scope global eth0
```
Here you can see that the interface associated with this IP is eth0 on the host.

4. What is the MAC address of the interface on the controlplane node?

**f6:d3:58:a2:0e:7a**

Run the command: ip link show eth0

```bash
ip link show eth0 
3: eth0@if7762: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1410 qdisc noqueue state UP mode DEFAULT group default 
    link/ether f6:d3:58:a2:0e:7a brd ff:ff:ff:ff:ff:ff link-netnsid 0
```

5. What is the IP address assigned to node01?

**192.168.227.186**

```bash
k describe node node01 | grep -i internalip
  InternalIP:  192.168.227.186
```

6. What is the MAC address assigned to node01?

**6a:0b:e5:8a:72:51**

``` bash
# connect to node01
ssh node01

# Get link
ip a | grep -B2 192.168.227.186
3: eth0@if562: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1410 qdisc noqueue state UP group default 
    link/ether 6a:0b:e5:8a:72:51 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 192.168.227.186/32 scope global eth0

# Get MAC address
ip link show eth0
3: eth0@if562: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1410 qdisc noqueue state UP mode DEFAULT group default 
    link/ether 6a:0b:e5:8a:72:51 brd ff:ff:ff:ff:ff:ff link-netnsid 0
```

7. We use Containerd as our container runtime. What is the interface/bridge created by Containerd on the controlplane node?

**cni0**

Run the command: ip link and look for a bridge interface created by containerd.

```bash
ip link
5: cni0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1360 qdisc noqueue state UP mode DEFAULT group default qlen 1000
    link/ether 56:25:a8:a3:49:7c brd ff:ff:ff:ff:ff:ff
```

8. What is the state of the interface cni0?
**state UP**

Run the command: ip link show cni0 and look for the state.

```bash
ip link show cni0 
5: cni0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1360 qdisc noqueue state UP mode DEFAULT group default qlen 1000
    link/ether 56:25:a8:a3:49:7c brd ff:ff:ff:ff:ff:ff
```

9. If you were to ping google from the controlplane node, which route does it take?

What is the IP address of the Default Gateway?

```bash
ping google.com
PING google.com (74.125.69.138) 56(84) bytes of data.
64 bytes from iq-in-f138.1e100.net (74.125.69.138):
```

Run the command: ip route show default and look at for default gateway.

```bash
ip route show default
default via 169.254.1.1 dev eth0 
```
10. What is the port the kube-scheduler is listening on in the controlplane node?

**10259**

Use the command: netstat -nplt

```bash
etstat -nplt
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 127.0.0.1:2379          0.0.0.0:*               LISTEN      2766/etcd           
tcp        0      0 127.0.0.1:2381          0.0.0.0:*               LISTEN      2766/etcd           
tcp        0      0 192.168.114.219:2380    0.0.0.0:*               LISTEN      2766/etcd           
tcp        0      0 192.168.114.219:2379    0.0.0.0:*               LISTEN      2766/etcd           
tcp        0      0 127.0.0.1:44905         0.0.0.0:*               LISTEN      915/containerd      
tcp        0      0 127.0.0.1:10248         0.0.0.0:*               LISTEN      3462/kubelet        
tcp        0      0 127.0.0.1:10249         0.0.0.0:*               LISTEN      3861/kube-proxy     
tcp        0      0 127.0.0.1:10259         0.0.0.0:*               LISTEN      2688/kube-scheduler 
tcp        0      0 127.0.0.1:10257         0.0.0.0:*               LISTEN      2725/kube-controlle 
tcp        0      0 0.0.0.0:8080            0.0.0.0:*               LISTEN      924/ttyd            
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      923/sshd: /usr/sbin 
tcp6       0      0 :::6443                 :::*                    LISTEN      2793/kube-apiserver 
tcp6       0      0 :::22                   :::*                    LISTEN      923/sshd: /usr/sbin 
tcp6       0      0 :::10250                :::*                    LISTEN      3462/kubelet        
tcp6       0      0 :::10256                :::*                    LISTEN      3861/kube-proxy     
tcp6       0      0 :::8888                 :::*                    LISTEN      3604/kubectl  
```

11. Notice that ETCD is listening on two ports. Which of these have more client connections established?

**2379**

```bash
tcp        0      0 127.0.0.1:2379          0.0.0.0:*               LISTEN      2766/etcd
```

Correct! That's because 2379 is the port of ETCD to which all control plane components connect to. 2380 is only for etcd peer-to-peer connectivity. When you have multiple controlplane nodes. In this case we don't.

```bash
tcp        0      0 192.168.114.219:2380    0.0.0.0:*               LISTEN      2766/etcd
```

