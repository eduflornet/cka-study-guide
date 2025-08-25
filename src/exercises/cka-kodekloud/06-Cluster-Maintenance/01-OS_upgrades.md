# OS Upgrades

Great things never came from comfort zones.

– Tony Luziaya

1. Let us explore the environment first. How many nodes do you see in the cluster?

Including the controlplane and worker nodes.

**2**

```bash
k get nodes
NAME           STATUS   ROLES           AGE     VERSION
controlplane   Ready    control-plane   7m20s   v1.33.0
node01         Ready    <none>          6m50s   v1.33.0
```

2. How many applications do you see hosted on the cluster?

Check the number of deployments in the default namespace.

**1**

```bash
k get deploy
NAME   READY   UP-TO-DATE   AVAILABLE   AGE
blue   3/3     3            3           104s
```

3. We need to take node01 out for maintenance. Empty the node of all applications and mark it unschedulable.

**Solution**
Run the command ``` kubectl drain node01 --ignore-daemonsets ```

```bash
k drain node01 --ignore-daemonsets 
node/node01 cordoned
Warning: ignoring DaemonSet-managed Pods: kube-flannel/kube-flannel-ds-lvgpv, kube-system/kube-proxy-27plv
evicting pod default/blue-69968556cc-hcfmg
evicting pod default/blue-69968556cc-8mdfp
pod/blue-69968556cc-hcfmg evicted
pod/blue-69968556cc-8mdfp evicted
node/node01 drained
```

4. What nodes are the apps on now?

**control plane**

```bash
k get nodes
NAME           STATUS                     ROLES           AGE   VERSION
controlplane   Ready                      control-plane   14m   v1.33.0
node01         Ready,SchedulingDisabled   <none>          13m   v1.33.0
```

5. The maintenance tasks have been completed. Configure the node node01 to be schedulable again.

**Solution**

Run the command  ```kubectl uncordon node01 ```

6. How many pods are scheduled on node01 now in the default namespace?

**0**

```bash
k get pods -o  wide
NAME                    READY   STATUS    RESTARTS   AGE   IP           NODE           NOMINATED NODE   READINESS GATES
blue-69968556cc-dvzdc   1/1     Running   0          10m   172.17.0.4   controlplane   <none>           <none>
blue-69968556cc-nzmm4   1/1     Running   0          6m    172.17.0.6   controlplane   <none>           <none>
blue-69968556cc-pc8lc   1/1     Running   0          6m    172.17.0.5   controlplane   <none>           <none>
```

7. Why are there no pods on node01?

Only when there are created PODS will they be scheduled.

8. Why are the pods placed on the controlplane node?
Check the controlplane node details.

**Because the control plane has no Taints**

```bash
k describe  nodes controlplane 
Name:               controlplane
Roles:              control-plane
Labels:             beta.kubernetes.io/arch=amd64
                    beta.kubernetes.io/os=linux
                    kubernetes.io/arch=amd64
                    kubernetes.io/hostname=controlplane
                    kubernetes.io/os=linux
                    node-role.kubernetes.io/control-plane=
                    node.kubernetes.io/exclude-from-external-load-balancers=
Annotations:        flannel.alpha.coreos.com/backend-data: {"VNI":1,"VtepMAC":"8e:dc:90:95:51:69"}
                    flannel.alpha.coreos.com/backend-type: vxlan
                    flannel.alpha.coreos.com/kube-subnet-manager: true
                    flannel.alpha.coreos.com/public-ip: 192.168.59.157
                    kubeadm.alpha.kubernetes.io/cri-socket: unix:///var/run/containerd/containerd.sock
                    node.alpha.kubernetes.io/ttl: 0
                    volumes.kubernetes.io/controller-managed-attach-detach: true
CreationTimestamp:  Mon, 25 Aug 2025 07:32:56 +0000
Taints:             <none>
Unschedulable:      false
...
```

9. We need to carry out a maintenance activity on node01 again. Try draining the node again using the same command as before: ``` kubectl drain node01 --ignore-daemonsets ```

Did that work?

```bash
k drain node01 --ignore-daemonsets 
node/node01 cordoned
error: unable to drain node "node01" due to error: cannot delete cannot delete Pods that declare no controller (use --force to override): default/hr-app, continuing command...
There are pending nodes to be drained:
 node01
cannot delete cannot delete Pods that declare no controller (use --force to override): default/hr-app
```

10. Why did the drain command fail on node01? It worked the first time!

**cannot delete cannot delete Pods that declare no controller**

11. What is the name of the POD hosted on node01 that is not part of a replicaset?

**hr-app**

```bash
k get all -o wide
NAME                        READY   STATUS    RESTARTS   AGE     IP           NODE           NOMINATED NODE   READINESS GATES
pod/blue-69968556cc-dvzdc   1/1     Running   0          22m     172.17.0.4   controlplane   <none>           <none>
pod/blue-69968556cc-nzmm4   1/1     Running   0          17m     172.17.0.6   controlplane   <none>           <none>
pod/blue-69968556cc-pc8lc   1/1     Running   0          17m     172.17.0.5   controlplane   <none>           <none>
pod/hr-app                  1/1     Running   0          7m35s   172.17.1.4   node01         <none>           <none>
```

12. What would happen to hr-app if node01 is drained forcefully?

Try it and see for yourself.

**hr-app will be lost forever**

**Hint**
A forceful drain of the node will delete any pod that is not part of a replicaset.

```bash
k drain node01 --ignore-daemonsets --force 
node/node01 already cordoned
Warning: ignoring DaemonSet-managed Pods: kube-flannel/kube-flannel-ds-lvgpv, kube-system/kube-proxy-27plv; deleting Pods that declare no controller: default/hr-app
evicting pod default/hr-app
```

13. Oops! We did not want to do that! hr-app is a critical application that should not be destroyed. We have now reverted back to the previous state and re-deployed hr-app as a deployment.

14. hr-app is a critical app and we do not want it to be removed and we do not want to schedule any more pods on node01.
Mark node01 as unschedulable so that no new pods are scheduled on this node.

Make sure that hr-app is not affected.

**Hint**

Run the command ``` kubectl cordon node01 ```

**Solution**

Do not drain node01, instead use the ``` kubectl cordon node01 ``` command. This will ensure that no new pods are scheduled on this node and the existing pods will not be affected by this operation.

```bash
controlplane ~ ➜  k get nodes -o wide
NAME           STATUS   ROLES           AGE   VERSION   INTERNAL-IP       EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION    CONTAINER-RUNTIME
controlplane   Ready    control-plane   41m   v1.33.0   192.168.59.157    <none>        Ubuntu 22.04.5 LTS   5.15.0-1083-gcp   containerd://1.6.26
node01         Ready    <none>          41m   v1.33.0   192.168.219.189   <none>        Ubuntu 22.04.5 LTS   5.15.0-1083-gcp   containerd://1.6.26

controlplane ~ ➜  k cordon node01 
node/node01 cordoned

controlplane ~ ➜  k get nodes -o wide
NAME           STATUS                     ROLES           AGE   VERSION   INTERNAL-IP       EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION    CONTAINER-RUNTIME
controlplane   Ready                      control-plane   43m   v1.33.0   192.168.59.157    <none>        Ubuntu 22.04.5 LTS   5.15.0-1083-gcp   containerd://1.6.26
node01         Ready,SchedulingDisabled   <none>          43m   v1.33.0   192.168.219.189   <none>        Ubuntu 22.04.5 LTS   5.15.0-1083-gcp   containerd://1.6.26
```










