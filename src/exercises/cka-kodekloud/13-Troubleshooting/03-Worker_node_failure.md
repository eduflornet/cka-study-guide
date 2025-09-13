# Worker Node Failure

Real difficulties can be overcome; it is only the imaginary ones that are unconquerable.

– Theodore N. Vail

1. Fix the broken cluster

**Hint**

- Step1. Check the status of services on the nodes.

- Step2. Check the service logs using ``` journalctl -u kubelet ```.

- Step3. If it's stopped then start the stopped services.

Alternatively, run the command:

``` ssh node01 "service kubelet start" ```

- Step1: Check the status of the nodes:

```bash
controlplane:~> kubectl get nodes
NAME           STATUS     ROLES           AGE   VERSION
controlplane   Ready      control-plane   19m   v1.27.0
node01         NotReady   <none>          19m   v1.27.0
```

controlplane:~> 
Step 2: SSH to node01 and check the status of the container runtime (containerd, in this case) and the kubelet service.

```bash
root@node01:~> systemctl status containerd
containerd.service - containerd container runtime
     Loaded: loaded (/lib/systemd/system/containerd.service; enabled; vendor preset: enabled)
     Active: active (running) since Thu 2025-09-11 19:21:26 UTC; 20min ago
       Docs: https://containerd.io
   Main PID: 896 (containerd)
      Tasks: 47
     Memory: 38.3M
     CGroup: /system.slice/containerd.service
             ├─ 896 /usr/bin/containerd
             ├─2638 /usr/bin/containerd-shim-runc-v2 -namespace k8s.io -id daf949b4acfd089c763c1b3faed17e6462e227988248daca7cda83000>
             └─2639 /usr/bin/containerd-shim-runc-v2 -namespace k8s.io -id 26fbf3408a092d5009ca4bd51b15ced70e20d5fd945e0eac8f265e0a8>

root@node01:~>

root@node01:~> systemctl status kubelet
 kubelet.service - kubelet: The Kubernetes Node Agent
     Loaded: loaded (/lib/systemd/system/kubelet.service; enabled; vendor preset: enabled)
    Drop-In: /usr/lib/systemd/system/kubelet.service.d
             └─10-kubeadm.conf
     Active: active (running) since Thu 2025-09-11 19:22:25 UTC; 20min ago
       Docs: https://kubernetes.io/docs/
   Main PID: 3461 (kubelet)
      Tasks: 23 (limit: 77143)
     Memory: 47.5M
     CGroup: /system.slice/kubelet.service
             └─3461 /usr/bin/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/kube>

```

Since the kubelet is not running, attempt to start it by running the following command:

```bash
root@node01:~> systemctl start kubelet

root@node01:~> systemctl status kubelet
ubelet.service - kubelet: The Kubernetes Node Agent
     Loaded: loaded (/lib/systemd/system/kubelet.service; enabled; vendor preset: enabled)
    Drop-In: /usr/lib/systemd/system/kubelet.service.d
             └─10-kubeadm.conf
     Active: active (running) since Thu 2025-09-11 19:22:25 UTC; 22min ago
       Docs: https://kubernetes.io/docs/
   Main PID: 3461 (kubelet)
      Tasks: 23 (limit: 77143)
     Memory: 47.5M
     CGroup: /system.slice/kubelet.service
             └─3461 /usr/bin/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/kube>
```

node01 should go back to ready state now.

2. The cluster is broken again. Investigate and fix the issue.

**Hint**

Inspect the logs using ``` journalctl -u kubelet -f ```. Fix the issue in the file.

```bash
ep 11 19:22:34 controlplane kubelet[3461]: E0911 19:22:34.966735    3461 kuberuntime_manager.go:1252] "CreatePodSandbox for pod failed" err="rpc error: code = Unknown desc = failed to setup network for sandbox \"e5a8e7ad13f2628e35bf08b06924cf006d67164345b0878910868fc68d43b1ff\": plugin type=\"flannel\" failed (add): loadFlannelSubnetEnv failed: open /run/flannel/subnet.env: no such file or directory" pod="kube-system/coredns-7484cd47db-kmg55"
Sep 11 19:22:34 controlplane kubelet[3461]: E0911 19:22:34.966790    3461 pod_workers.go:1301] "Error syncing pod, skipping" err="failed to \"CreatePodSandbox\" for \"coredns-7484cd47db-kmg55_kube-system(5972853f-beb3-4e6d-a54d-cd9b52ba415f)\" with CreatePodSandboxError: \"Failed to create sandbox for pod \\\"coredns-7484cd47db-kmg55_kube-system(5972853f-beb3-4e6d-a54d-cd9b52ba415f)\\\": rpc error: code = Unknown desc = failed to setup network for sandbox \\\"e5a8e7ad13f2628e35bf08b06924cf006d67164345b0878910868fc68d43b1ff\\\": plugin type=\\\"flannel\\\" failed (add): loadFlannelSubnetEnv failed: open /run/flannel/subnet.env: no such file or directory\"" pod="kube-system/coredns-7484cd47db-kmg55" podUID="5972853f-beb3-4e6d-a54d-cd9b52ba415f"
Sep 11 19:22:35 controlplane kubelet[3461]: I0911 19:22:35.744440    3461 pod_startup_latency_tracker.go:104] "Observed pod startup duration" pod="kube-flannel/kube-flannel-ds-b8hjs" podStartSLOduration=5.744415212 podStartE2EDuration="5.744415212s" podCreationTimestamp="2025-09-11 19:22:30 +0000 UTC" firstStartedPulling="0001-01-01 00:00:00 +0000 UTC" lastFinishedPulling="0001-01-01 00:00:00 +0000 UTC" observedRunningTime="2025-09-11 19:22:35.744322467 +0000 UTC m=+9.974335924" watchObservedRunningTime="2025-09-11 19:22:35.744415212 +0000 UTC m=+9.974428663"
Sep 11 19:22:47 controlplane kubelet[3461]: I0911 19:22:47.709701    3461 pod_startup_latency_tracker.go:104] "Observed pod startup duration" pod="kube-system/coredns-7484cd47db-kmg55" podStartSLOduration=16.709682126 podStartE2EDuration="16.709682126s" podCreationTimestamp="2025-09-11 19:22:31 +0000 UTC" firstStartedPulling="0001-01-01 00:00:00 +0000 UTC" lastFinishedPulling="0001-01-01 00:00:00 +0000 UTC" observedRunningTime="2025-09-11 19:22:47.708920175 +0000 UTC m=+21.938933626" watchObservedRunningTime="2025-09-11 19:22:47.709682126 +0000 UTC m=+21.939695573"
Sep 11 19:22:50 controlplane kubelet[3461]: I0911 19:22:50.734305    3461 pod_startup_latency_tracker.go:104] "Observed pod startup duration" pod="kube-system/coredns-7484cd47db-wrrv4" podStartSLOduration=19.734265922 podStartE2EDuration="19.734265922s" podCreationTimestamp="2025-09-11 19:22:31 +0000 UTC" firstStartedPulling="0001-01-01 00:00:00 +0000 UTC" lastFinishedPulling="0001-01-01 00:00:00 +0000 UTC" observedRunningTime="2025-09-11 19:22:50.718790657 +0000 UTC m=+24.948804119" watchObservedRunningTime="2025-09-11 19:22:50.734265922 +0000 UTC m=+24.964279373"
Sep 11 19:27:26 controlplane kubelet[3461]: E0911 19:27:26.661273    3461 info.go:104] Failed to get disk map: could not parse device numbers from  for device md127
Sep 11 19:32:26 controlplane kubelet[3461]: E0911 19:32:26.662617    3461 info.go:104] Failed to get disk map: could not parse device numbers from  for device md127
Sep 11 19:37:26 controlplane kubelet[3461]: E0911 19:37:26.660553    3461 info.go:104] Failed to get disk map: could not parse device numbers from  for device md127
Sep 11 19:42:26 controlplane kubelet[3461]: E0911 19:42:26.660518    3461 info.go:104] Failed to get disk map: could not parse device numbers from  for device md127
Sep 11 19:47:26 controlplane kubelet[3461]: E0911 19:47:26.660276    3461 info.go:104] Failed to get disk map: could not parse device numbers from  for device md127
```

**Solution**

kubelet has stopped running on node01 again. Since this is a systemd managed system, we can check the kubelet log by running journalctl command. Here is a snippet showing the error with kubelet:

```bash
root@node01:~# journalctl -u kubelet 
.
.
May 30 13:08:20 node01 kubelet[4554]: E0530 13:08:20.141826    4554 run.go:74] "command failed" err="failed to construct kubelet dependencies: unable to load client CA file /etc/kubernetes/pki/WRONG-CA-FILE.crt: open /etc/kubernetes/pki/WRONG-CA-FILE.crt: no such file or directory"
.
.
```

There appears to be a mistake path used for the CA certificate in the kubelet configuration.

This can be corrected by updating the file ``` /var/lib/kubelet/config.yaml ``` as follows: -

```yaml
  x509:
    clientCAFile: /etc/kubernetes/pki/WRONG-CA-FILE.crt
```

Update the CA certificate file **WRONG-CA-FILE.crt** to ca.crt.

Once this is fixed, restart the kubelet service, (like we did in the previous question) and node01 should return back to a working state.


3. The cluster is broken again. Investigate and fix the issue.

**Hint**
Check the kubelet.conf file at ``` /etc/kubernetes/kubelet.conf ```.

Once again the kubelet service has stopped working. Checking the logs, we can see that this time, it is not able to reach the kube-apiserver.

```bash
root@node01:~# journalctl -u kubelet 
.
.
.
May 30 13:43:55 node01 kubelet[8858]: E0530 13:43:55.004939    8858 reflector.go:148] vendor/k8s.io/client-go/informers/factory.go:150: Failed to watch *v1.Node: failed to list *v1.Node: Get "https://controlplane:6553/api/v1/nodes?fieldSelector=metadata.name%3Dnode01&limit=500&resourceVersion=0": dial tcp 192.24.132.5:6553: connect: connection refused
.
.
.
```

As we can clearly see, kubelet is trying to connect to the API server on the controlplane node on port 6553. This is incorrect.
To fix, correct the port on the kubeconfig file used by the kubelet.

```yaml
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data:
    --REDACTED---
    server: https://controlplane:6443
```

Restart the kubelet service after this change.

```bash
systemctl restart kubelet
```

