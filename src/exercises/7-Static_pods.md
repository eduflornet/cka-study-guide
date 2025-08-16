# Static Pods

Neither comprehension nor learning can take place in an atmosphere of anxiety.

– Rose Kennedy

1. How many static pods exist in this cluster in all namespaces?

```shell
kubectl get pods -A
NAMESPACE      NAME                                   READY   STATUS    RESTARTS   AGE
kube-flannel   kube-flannel-ds-jw5hf                  1/1     Running   0          10m
kube-flannel   kube-flannel-ds-qjxvv                  1/1     Running   0          10m
kube-system    coredns-7484cd47db-hhc7q               1/1     Running   0          10m
kube-system    coredns-7484cd47db-tmt8j               1/1     Running   0          10m
kube-system    etcd-controlplane                      1/1     Running   0          10m
kube-system    kube-apiserver-controlplane            1/1     Running   0          10m
kube-system    kube-controller-manager-controlplane   1/1     Running   0          10m
kube-system    kube-proxy-46zvp                       1/1     Running   0          10m
kube-system    kube-proxy-smfgk                       1/1     Running   0          10m
kube-system    kube-scheduler-controlplane            1/1     Running   0          10m

```

```shell
sudo ls -lF /etc/kubernetes/manifests/
total 16
-rw------- 1 root root 2559 Aug 16 08:09 etcd.yaml
-rw------- 1 root root 3893 Aug 16 08:09 kube-apiserver.yaml
-rw------- 1 root root 3394 Aug 16 08:09 kube-controller-manager.yaml
-rw------- 1 root root 1656 Aug 16 08:09 kube-scheduler.yaml
```

Solution: 4 static pods

2. Which of the below components is NOT deployed as a static pod?
coredns, because it is not part of the files placed on /etc/kubernetes/manifests/

3. Which of the below components is NOT deployed as a static pod?
kube-proxy, because it is not part of the files placed on /etc/kubernetes/manifests/

5. On which nodes are the static pods created currently?

Run the ``` kubectl get pods --all-namespaces -o wide ``` and identify the node in which static pods are deployed.

```shell
controlplane ~ ➜  kubectl get pods -A -o wide
NAMESPACE      NAME                                   READY   STATUS    RESTARTS   AGE   IP               NODE           NOMINATED NODE   READINESS GATES
kube-flannel   kube-flannel-ds-jw5hf                  1/1     Running   0          23m   192.168.31.10    node01         <none>           <none>
kube-flannel   kube-flannel-ds-qjxvv                  1/1     Running   0          23m   192.168.65.231   controlplane   <none>           <none>
kube-system    coredns-7484cd47db-hhc7q               1/1     Running   0          23m   172.17.0.3       controlplane   <none>           <none>
kube-system    coredns-7484cd47db-tmt8j               1/1     Running   0          23m   172.17.0.2       controlplane   <none>           <none>
kube-system    etcd-controlplane                      1/1     Running   0          24m   192.168.65.231   controlplane   <none>           <none>
kube-system    kube-apiserver-controlplane            1/1     Running   0          24m   192.168.65.231   controlplane   <none>           <none>
kube-system    kube-controller-manager-controlplane   1/1     Running   0          24m   192.168.65.231   controlplane   <none>           <none>
kube-system    kube-proxy-46zvp                       1/1     Running   0          23m   192.168.65.231   controlplane   <none>           <none>
kube-system    kube-proxy-smfgk                       1/1     Running   0          23m   192.168.31.10    node01         <none>           <none>
kube-system    kube-scheduler-controlplane            1/1     Running   0          24m   192.168.65.231   controlplane   <none>           <none>
```

Solution:

By default, static pods are created for the controlplane components and hence, they are only created in the controlplane node.

6. What is the path of the directory holding the static pod definition files?

```shell
/etc/kubernetes/manifests/
```


7. How many pod definition files are present in the manifests directory?
= 4

```shell
sudo ls -lF /etc/kubernetes/manifests/
total 16
-rw------- 1 root root 2559 Aug 16 08:09 etcd.yaml
-rw------- 1 root root 3893 Aug 16 08:09 kube-apiserver.yaml
-rw------- 1 root root 3394 Aug 16 08:09 kube-controller-manager.yaml
-rw------- 1 root root 1656 Aug 16 08:09 kube-scheduler.yaml
```

8. What is the docker image used to deploy the kube-api server as a static pod?

image: registry.k8s.io/kube-apiserver:v1.33.0

```shell
sudo cat /etc/kubernetes/manifests/kube-apiserver.yaml | grep -i image
    image: registry.k8s.io/kube-apiserver:v1.33.0
    imagePullPolicy: IfNotPresent
```

9. Create a static pod named static-busybox that uses the busybox image, run in the default namespace and the command sleep 1000

Hint:
Create a pod definition file called static-busybox.yaml with the provided specs and place it under /etc/kubernetes/manifests directory.

Solution:
Create a pod definition file in the manifests folder. To do this, run the command:

```shell
kubectl run --restart=Never --image=busybox static-busybox --dry-run=client -o yaml --command -- sleep 1000 > /etc/kubernetes/manifests/static-busybox.yaml
```

10. Edit the image on the static pod you created in the previous task to use busybox:1.28.4

Was image busybox used in the static pod now ?

Solution:

Simply edit the static pod definition file and save it. If that does not re-create the pod, run: ``` kubectl run --restart=Never --image=busybox:1.28.4 static-busybox --dry-run=client -o yaml --command -- sleep 1000 > /etc/kubernetes/manifests/static-busybox.yaml ```


```shell
nano /etc/kubernetes/manifests/static-busybox.yaml 
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: static-busybox
  name: static-busybox
spec:
  containers:
  - command:
    - sleep
    - "1000"
    image: busybox:1.28.4
    name: static-busybox
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}

```

11. We just created a new static pod named static-greenbox. Find it and delete it.

This question is a bit tricky. But if you use the knowledge you gained in the previous questions in this lab, you should be able to find the answer to it.

Hint:
Identify which node the static pod is created on, ssh to the node and delete the pod definition file.
If you don't know the IP of the node, run the ``` kubectl get nodes -o wide ``` command and identify the IP.
Then, SSH to the node using that IP. For static pod manifest path look at the file ``` /var/lib/kubelet/config.yaml ``` on node01


Solution:

First, let's identify the node in which the pod called static-greenbox is created. To do this, run:

```shell 
root@controlplane:~# kubectl get pods --all-namespaces -o wide  | grep static-greenbox
default       static-greenbox-node01                 1/1     Running   0          19s     10.244.1.2   node01       <none>           <none>
root@controlplane:~#
```

From the result of this command, we can see that the pod is running on node01.

Next, SSH to node01 and identify the path configured for static pods in this node.

Important: The path need not be ``` /etc/kubernetes/manifests ```. Make sure to check the path configured in the kubelet configuration file.

```shell
root@controlplane:~# ssh node01 
root@node01:~# ps -ef |  grep /usr/bin/kubelet 
root        4147       1  0 14:05 ?        00:00:00 /usr/bin/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/kubelet.conf --config=/var/lib/kubelet/config.yaml --container-runtime-endpoint=unix:///var/run/containerd/containerd.sock --pod-infra-container-image=registry.k8s.io/pause:3.9
root        4773    4733  0 14:05 pts/0    00:00:00 grep /usr/bin/kubelet


root@node01:~# grep -i staticpod /var/lib/kubelet/config.yaml
staticPodPath: /etc/just-to-mess-with-you

sudo cat /var/lib/kubelet/config.yaml | grep -i staticpod
staticPodPath: /etc/just-to-mess-with-you

root@node01:~# 
```
Complete file config.yaml

```yaml
apiVersion: kubelet.config.k8s.io/v1beta1
authentication:
  anonymous:
    enabled: false
  webhook:
    cacheTTL: 0s
    enabled: true
  x509:
    clientCAFile: /etc/kubernetes/pki/ca.crt
authorization:
  mode: Webhook
  webhook:
    cacheAuthorizedTTL: 0s
    cacheUnauthorizedTTL: 0s
cgroupDriver: cgroupfs
clusterDNS:
- 172.20.0.10
clusterDomain: cluster.local
containerRuntimeEndpoint: ""
cpuManagerReconcilePeriod: 0s
crashLoopBackOff: {}
evictionPressureTransitionPeriod: 0s
fileCheckFrequency: 0s
healthzBindAddress: 127.0.0.1
healthzPort: 10248
httpCheckFrequency: 0s
imageMaximumGCAge: 0s
imageMinimumGCAge: 0s
kind: KubeletConfiguration
logging:
  flushFrequency: 0
  options:
    json:
      infoBufferSize: "0"
    text:
      infoBufferSize: "0"
  verbosity: 0
  memorySwap: {}
nodeStatusReportFrequency: 0s
nodeStatusUpdateFrequency: 0s
resolvConf: /run/systemd/resolve/resolv.conf
rotateCertificates: true
runtimeRequestTimeout: 0s
shutdownGracePeriod: 0s
shutdownGracePeriodCriticalPods: 0s
staticPodPath: /etc/just-to-mess-with-you
streamingConnectionIdleTimeout: 0s
syncFrequency: 0s
volumeStatsAggPeriod: 0s
```

Here the staticPodPath is ``` /etc/just-to-mess-with-you ```

Navigate to this directory and delete the YAML file:

```shell
root@node01:/etc/just-to-mess-with-you# ls
greenbox.yaml
root@node01:/etc/just-to-mess-with-you# rm -rf greenbox.yaml 
root@node01:/etc/just-to-mess-with-you#
```

wait for 30 seconds.

Exit out of node01 using CTRL + D or type exit. You should return to the controlplane node, Check if the static-greenbox pod has been deleted:

```shell
root@controlplane:~# kubectl get pods --all-namespaces -o wide  | grep static-greenbox
root@controlplane:~# 
```





