# Node Selector

## Reference

* https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/

In this demo:
a. We will schedule the Pods to certain nodes, which statisfy the label assigned to the node.

1. Labeling Node:

```bash
kubectl get nodes --show-labels
kubectl label nodes worker-1 disktype=ssd
```

```bash
kubectl get nodes --show-labels
kubectl get pods -o wide
```

2. Deploying Node-Selector YAML:

```yaml
# nodeselector-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nodeselector-pod
  labels:
    env: test
spec:
  containers:
  - name: nginx
    image: nginx
    imagePullPolicy: IfNotPresent
  nodeSelector:
    disktype: ssd
```


Equivalent imperative command

```bash
kubectl run nodeselector-pod \
  --image=nginx \
  --restart=Never \
  --image-pull-policy=IfNotPresent \
  --labels=env=test \
  --dry-run=client -o yaml > nodeselector-pod.yaml \
  --overrides='
{
  "apiVersion": "v1",
  "spec": {
    "nodeSelector": {
      "disktype": "ssd"
    }
  }
}'
```

🔍 What does this command do?
--image=nginx: Uses the NGINX image

--restart=Never: Creates a Pod (not a Deployment)

--image-pull-policy=IfNotPresent: Prevents downloading the image if it already exists

--labels=env=test: Assigns the env=test label

--overrides: Allows you to insert additional fields like nodeSelector that don't have direct flags

Apply:

```bash
kubectl apply -f nodeselector-pod.yaml
```

3. Testing:

```bash
kubectl get pods -o wide
kubectl get nodes --show-labels
```

Let's Delete and Deploy "again" to ensure Pod is deployed on the same node which is lablelled above.

```bash
kubectl delete -f ns.yaml
kubectl apply -f ns.yaml

kubectl get pods -o wide
kubectl get nodes --show-labels
```
