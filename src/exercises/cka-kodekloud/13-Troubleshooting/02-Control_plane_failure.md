# Control plane failure

Real difficulties can be overcome; it is only the imaginary ones that are unconquerable.

– Theodore N. Vail

You teach best what you most need to learn.

– Richard Bach

The more that you read, the more things you will know. The more that you learn, the more places you'll go.

– Dr. Seuss

There is only one success - to be able to spend your life in your own way.

– Christopher Morley

1. The cluster is broken. We tried deploying an application but it's not working. Troubleshoot and fix the issue.


Start looking at the deployments.

**Hint**
Check the status of all control plane components and identify the component's pod which has an issue.

**Solution**

Run the command: ``` kubectl get pods -n kube-system ``` and check the status of **kube-scheduler** pod.
We need to check the kube-scheduler manifest file to fix the issue.

```bash
k -n kube-system get pods
NAME                                   READY   STATUS             RESTARTS      AGE
coredns-7484cd47db-d8zkx               1/1     Running            0             9m27s
coredns-7484cd47db-t2x58               1/1     Running            0             9m27s
etcd-controlplane                      1/1     Running            0             9m34s
kube-apiserver-controlplane            1/1     Running            0             9m34s
kube-controller-manager-controlplane   1/1     Running            0             9m34s
kube-proxy-gszlb                       1/1     Running            0             9m27s
kube-scheduler-controlplane            0/1     CrashLoopBackOff   5 (49s ago)   3m48s
```

The command run by the scheduler pod is incorrect. Here is a snippet of the YAML file.

```yaml
spec:
  containers:
  - command:
    - kube-scheduler
    - --authentication-kubeconfig=/etc/kubernetes/scheduler.conf
    - --authorization-kubeconfig=/etc/kubernetes/scheduler.conf
    - --bind-address=127.0.0.1
    - --kubeconfig=/etc/kubernetes/scheduler.conf
    - --leader-elect=true
    ...
```
.
Once this is corrected, the scheduler pod will be recreated.



2. Scale the deployment app to 2 pods.

```bash
 k -n default scale deploy app --replicas=2
 ```

 3. Even though the deployment was scaled to 2, the number of PODs does not seem to increase. Investigate and fix the issue.

Inspect the component responsible for managing deployments and replicasets.

```bash
k -n default events deploy app
LAST SEEN   TYPE      REASON                    OBJECT                      MESSAGE
18m         Normal    Starting                  Node/controlplane           Starting kubelet.
18m         Warning   CgroupV1                  Node/controlplane           cgroup v1 support is in maintenance mode, please migrate to cgroup v2
18m         Warning   InvalidDiskCapacity       Node/controlplane           invalid capacity 0 on image filesystem
18m         Normal    NodeAllocatableEnforced   Node/controlplane           Updated Node Allocatable limit across pods
18m         Normal    NodeHasSufficientMemory   Node/controlplane           Node controlplane status is now: NodeHasSufficientMemory
18m         Normal    NodeHasNoDiskPressure     Node/controlplane           Node controlplane status is now: NodeHasNoDiskPressure
18m         Normal    NodeHasSufficientPID      Node/controlplane           Node controlplane status is now: NodeHasSufficientPID
18m         Normal    RegisteredNode            Node/controlplane           Node controlplane event: Registered Node controlplane in Controller
18m         Normal    Starting                  Node/controlplane           
18m         Normal    NodeReady                 Node/controlplane           Node controlplane status is now: NodeReady
16m         Normal    SuccessfulCreate          ReplicaSet/app-7f9667c9d9   Created pod: app-7f9667c9d9-bbxqt
16m         Normal    ScalingReplicaSet         Deployment/app              Scaled up replica set app-7f9667c9d9 from 0 to 1

```

**Hint**
Check the status of all control plane components and identify the component's pod which has an issue.

**Solution**

Run the command: ``` kubectl get po -n kube-system ``` and check the logs of **kube-controller-manager** pod to know the failure reason by running command: ``` kubectl logs -n kube-system kube-controller-manager-controlplane ```

Then check the **kube-controller-manager** configuration file at ``` /etc/kubernetes/manifests/kube-controller-manager.yaml ``` and fix the issue.

```bash
root@controlplane:/etc/kubernetes/manifests# kubectl -n kube-system logs kube-controller-manager-controlplane
I0916 13:10:47.059336       1 serving.go:348] Generated self-signed cert in-memory
stat /etc/kubernetes/controller-manager-XXXX.conf: no such file or directory

root@controlplane:/etc/kubernetes/manifests# 
```

The configuration file specified ``` (/etc/kubernetes/controller-manager-XXXX.conf) ``` does not exist.
Correct the path: ``` /etc/kubernetes/controller-manager.conf ```

4. Something is wrong with scaling again. We just tried scaling the deployment to 3 replicas. But it's not happening.

Investigate and fix the issue.

**Hint**
Use the command ``` kubectl get pods -n kube-system ``` and check the logs of one of the fail control plane component and try to fix the cause of issue.

**Solution**

Check the volume mount path in kube-controller-manager manifest file at  ``` /etc/kubernetes/manifests ```.
Just as we did in the previous question, inspect the logs of the kube-controller-manager pod:

```bash
root@controlplane:/etc/kubernetes/manifests# kubectl -n kube-system logs kube-controller-manager-controlplane
I0916 13:17:27.452539       1 serving.go:348] Generated self-signed cert in-memory
unable to load client CA provider: open /etc/kubernetes/pki/ca.crt: no such file or directory

root@controlplane:/etc/kubernetes/manifests# 
```

It appears the path ``` /etc/kubernetes/pki ``` is not mounted from the controlplane to the **kube-controller-manager** pod. If we inspect the pod manifest file, we can see that the incorrect hostPath is used for the volume:

WRONG:

```yaml
- hostPath:
      path: /etc/kubernetes/WRONG-PKI-DIRECTORY
      type: DirectoryOrCreate
```

CORRECT:

```yaml
- hostPath: 
    path: /etc/kubernetes/pki 
    type: DirectoryOrCreate 
```

Once the path is corrected, the pod will be recreated and our deployment should eventually scale up to 3 replicas.

