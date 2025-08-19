# Resource limits

## Reference

* https://kubernetes.io/docs/concepts/configuration/manage-resources-containers
* https://kubernetes.io/docs/tasks/configure-pod-container/assign-memory-resource/
* https://kubernetes.io/docs/tasks/configure-pod-container/assign-cpu-resource/
* https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#requests-and-limits
* https://cloud.google.com/blog/products/gcp/kubernetes-best-practices-resource-requests-and-limits
* https://medium.com/@betz.mark/understanding-resource-limits-in-kubernetes-memory-6b41e9a955f9
* https://medium.com/@betz.mark/understanding-resource-limits-in-kubernetes-cpu-time-9eff74d3161b

KNOW BEFORE YOU RUN:

1. In this exercise, you create a Pod that has one Container. 
The Container has a memory request of 100 MiB and a memory limit of 200 MiB. 
Here's the configuration file for the Pod:

2. My Env: K8s running on GCE VMs

1. Configuring Container with "Memory" Requests and Limits:

a. First, get the output of kubectl top command to find resources that are "currently consumed".

```bash
kubectl top nodes
```

b. Then, deploy this Pod with memory requests and limits as mentioned below

```yaml
# memory-demo.yaml
apiVersion: v1
kind: Pod
metadata:
  name: memory-demo
spec:
  containers:
  - name: memory-demo-ctr
    image: polinux/stress
    resources:
      requests:
        memory: "100Mi"
      limits:
        memory: "200Mi"
    command: ["stress"]
    args: ["--vm", "1", "--vm-bytes", "150M", "--vm-hang", "1"]

# The args section in the configuration file provides arguments for the Container when it starts. 
# The "--vm-bytes", "150M" arguments tell the Container to attempt to allocate 150 MiB of memory
```



c. Deploy:

```bash
kubectl apply -f memory-demo.yaml
```

d. Validate

```bash
kubectl get pods -o wide

kubectl top nodes
```

If the respective worker node has morethan 150Mi memory, then Pod should be running successfully.
Requests - specify, Pod should be scheduled on node which can minimum gurantee this Pod with 100Mi
Limits - If the node has morethan 100Mi memory free space, it can use remaining space upto 200Mi Max.
Exercise: Try scheduling the same Pod with too big for any of node in your cluster.

2. Configuring Container with "CPU" Requests and Limits:

You can follow below link. It should be straight forward.

https://kubernetes.io/docs/tasks/configure-pod-container/assign-cpu-resource/
