
# Resource limits
The most important thing to do in solving a problem is to begin.
– Frank Tyger

1. A pod called rabbit is deployed. Identify the CPU requirements set on the Pod

in the current(default) namespace

Solucion: 
Request: cpu: 500m)

```shell
kubectl describe pod rabbit 
Name:             rabbit
Namespace:        default
Priority:         0
Service Account:  default
Node:             controlplane/192.168.28.62
Start Time:       Fri, 15 Aug 2025 09:14:54 +0000
Labels:           <none>
Annotations:      <none>
Status:           Running
IP:               10.22.0.9
IPs:
  IP:  10.22.0.9
Containers:
  cpu-stress:
    Container ID:  containerd://297b01d544ff231cdd1d440f0cd46c5e5d83a13098448f5e2af59e43ab00dfc4
    Image:         ubuntu
    Image ID:      docker.io/library/ubuntu@sha256:7c06e91f61fa88c08cc74f7e1b7c69ae24910d745357e0dfe1d2c0322aaf20f9
    Port:          <none>
    Host Port:     <none>
    Args:
      sleep
      1000
    State:          Running
      Started:      Fri, 15 Aug 2025 09:14:56 +0000
    Ready:          True
    Restart Count:  0
    Limits:
      cpu:  1
    Requests:
      cpu:        500m
    Environment:  <none>
    Mounts:
    ...
```

Delete the rabbit Pod.
Once deleted, wait for the pod to fully terminate.


3. Another pod called elephant has been deployed in the default namespace. It fails to get to a running state. Inspect this pod and identify the Reason why it is not running.

```shell
kubectl get pods -A
NAMESPACE     NAME                                      READY   STATUS             RESTARTS      AGE
default       elephant                                  0/1     CrashLoopBackOff   1 (10s 
```

```shell
kubectl describe pod  elephant

Events:
  Type     Reason     Age                From               Message
  ----     ------     ----               ----               -------
  Normal   Scheduled  89s                default-scheduler  Successfully assigned default/elephant to controlplane
  Normal   Pulled     88s                kubelet            Successfully pulled image "polinux/stress" in 1.303s (1.303s including waiting). Image size: 4041495 bytes.
  Normal   Pulled     87s                kubelet            Successfully pulled image "polinux/stress" in 146ms (146ms including waiting). Image size: 4041495 bytes.
  Normal   Pulled     73s                kubelet            Successfully pulled image "polinux/stress" in 138ms (138ms including waiting). Image size: 4041495 bytes.
  Normal   Pulling    46s (x4 over 89s)  kubelet            Pulling image "polinux/stress"
  Normal   Created    46s (x4 over 88s)  kubelet            Created container: mem-stress
  Normal   Started    46s (x4 over 88s)  kubelet            Started container mem-stress
  Normal   Pulled     46s                kubelet            Successfully pulled image "polinux/stress" in 157ms (157ms including waiting). Image size: 4041495 bytes.
  Warning  BackOff    7s (x8 over 86s)   kubelet            Back-off restarting failed container mem-stress in pod elephant_default(03875849-d19a-4342-8d21-4c232358285c)
```

4. The status OOMKilled indicates that it is failing because the pod ran out of memory. Identify the memory limit set on the POD.

5. The elephant pod runs a process that consumes 15Mi of memory. Increase the limit of the elephant pod to 20Mi.

Delete and recreate the pod if required. Do not modify anything other than the required fields.

```shell
kubectl get pod elephant -o yaml > pod-elephant.yaml

nano pod-elephant.yaml
```

Solution:
Create the file elephant.yaml by running command ``` kubectl get po elephant -o yaml > elephant.yaml ``` and edit the file such as memory limit is set to 20Mi as follows:


```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: "2025-08-15T09:21:34Z"
  generation: 1
  name: elephant
  namespace: default
  resourceVersion: "1130"
  uid: 03875849-d19a-4342-8d21-4c232358285c
spec:
  containers:
  - args:
    - --vm
    - "1"
    - --vm-bytes
    - 15M
    - --vm-hang
    - "1"
    command:
    - stress
    image: polinux/stress
    imagePullPolicy: Always
    name: mem-stress
    resources:
      limits:
        memory: 10Mi
      requests:
        memory: 5Mi
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-9mz7f
      readOnly: true
  dnsPolicy: ClusterFirst
  enableServiceLinks: true
  nodeName: controlplane
  ```
then run ``` kubectl replace -f elephant.yaml --force ```. This command will delete the existing one first and recreate a new one from the YAML file.

```shell
 Error from server (Conflict): error when replacing "elephant.yaml": Operation cannot be fulfilled on pods "elephant": the object has been modified; please apply your changes to the latest version and try again
 ```shell

```shell
kubectl apply -f elephant.yaml 
Warning: resource pods/elephant is missing the kubectl.kubernetes.io/last-applied-configuration annotation which is required by kubectl apply. kubectl apply should only be used on resources created declaratively by either kubectl create --save-config or kubectl apply. The missing annotation will be patched automatically.

```

Solution:
I can see you tried to update the pod by editing the YAML and then using ``` kubectl replace``` or ``` kubectl apply ```, but encountered conflicts. The main issue is that pods are immutable objects in Kubernetes—once created, you can't modify their spec directly. Instead, you need to delete the existing pod and recreate it with the new configuration.

So, to fix this:

Delete the current elephant pod:

```shell
kubectl delete pod elephant
```

Recreate it with the updated YAML:

```shell
kubectl apply -f elephant.yaml
```

This approach ensures the memory limit is set to 20Mi as required.

6. Inspect the status of POD. Make sure it's running

```shell
kubectl describe pod elephant
Name:             elephant
Namespace:        default
Priority:         0
Service Account:  default
Node:             controlplane/192.168.28.62
Start Time:       Fri, 15 Aug 2025 09:40:17 +0000
Labels:           <none>
Annotations:      <none>
Status:           Running
IP:               10.22.0.11
IPs:
  IP:  10.22.0.11
Containers:
  mem-stress:
    Container ID:  containerd://91e0a83e30f725a97db8ba9ef018d096242db33f965dd01c22e31484000f1626
    Image:         polinux/stress
    Image ID:      docker.io/polinux/stress@sha256:b6144f84f9c15dac80deb48d3a646b55c7043ab1d83ea0a697c09097aaad21aa
    Port:          <none>
    Host Port:     <none>
    Command:
      stress
    Args:
      --vm
      1
      --vm-bytes
      15M
      --vm-hang
      1
    State:          Running
      Started:      Fri, 15 Aug 2025 09:40:18 +0000
    Ready:          True
    Restart Count:  0
    Limits:
      memory:  20Mi
    Requests:
      memory:     5Mi
    Environment:  <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-9mz7f (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       True 
  ContainersReady             True 
  PodScheduled                True 
Volumes:
  kube-api-access-9mz7f:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    Optional:                false
    DownwardAPI:             true
QoS Class:                   Burstable
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason   Age   From     Message
  ----    ------   ----  ----     -------
  Normal  Pulling  94s   kubelet  Pulling image "polinux/stress"
  Normal  Pulled   94s   kubelet  Successfully pulled image "polinux/stress" in 146ms (146ms including waiting). Image size: 4041495 bytes.
  Normal  Created  94s   kubelet  Created container: mem-stress
  Normal  Started  94s   kubelet  Started container mem-stress

```

7. Delete the elephant Pod.

Once deleted, wait for the pod to fully terminate.

## Documentation References

[Manage Memory, CPU and API Resources](https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/)

[LimitRange for CPU](https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/cpu-default-namespace/)

[LimitRange for Memory](https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/)