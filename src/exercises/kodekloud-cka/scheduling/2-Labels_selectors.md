
## Labels and selectors

Practice as if you are the worst, Perform as if you are the best.

You didn't do anything wrong—it's just that the scheduler isn't running in your cluster, which is why the pod is stuck in Pending. The scheduler is responsible for assigning pods to nodes. Since it's missing, your pod can't be scheduled, leading to the Pending state.

To fix this, ensure the scheduler is running properly in your control plane.

Run the command: ``` kubectl get pods --namespace kube-system ``` to see the status of scheduler pod. We have removed the scheduler from this Kubernetes cluster. As a result, as it stands, the pod will remain in a pending state forever.

1. Manually schedule the pod on node01.

- Delete and recreate the POD if necessary.
- Delete the existing pod first. Run the below command:

```bash 
kubectl delete pod nginx
```
To list and know the names of available nodes on the cluster:

```bash
kubectl get nodes
```
Add the nodeName field under the spec section in the nginx.yaml file with node01 as the value:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  nodeName: node01
  containers:
  -  image: nginx
     name: nginx
```

Then run the command ``` kubectl create -f nginx.yaml ``` to create a pod from the definition file.

To check the status of a nginx pod and to know the node name: 

```bash
kubectl get pods -o wide
```

2. Now schedule the same pod on the controlplane node.

Delete and recreate the POD if necessary.

- Status: Running
- Pod: nginx
- Node: controlplane

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  nodeName: controlplane
  containers:
  -  image: nginx
     name: nginx
```
     