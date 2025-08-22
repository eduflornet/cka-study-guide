# Monitor Cluster Components

You never know what you can do until you try.

– William Cobbett

1. We have deployed a few PODs running workloads. Inspect them.

Wait for the pods to be ready before proceeding to the next question.

```bash
kubectl get pods
NAME       READY   STATUS    RESTARTS   AGE
elephant   1/1     Running   0          30s
lion       1/1     Running   0          30s
rabbit     1/1     Running   0          30s
```

2. Let us deploy the Metrics Server to enable monitoring of the PODs and Nodes in the cluster.

3. Deploy the Metrics Server in your Kubernetes cluster by applying the latest release components.yaml manifest using the following command:

Run the ``` kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml ```
4. It takes a few minutes for the metrics server to start gathering data.
Run the kubectl top node command and wait for a valid output.

```bash
kubectl top nodes
NAME           CPU(cores)   CPU(%)   MEMORY(bytes)   MEMORY(%)   
controlplane   308m         1%       897Mi           1%          
node01         32m          0%       153Mi           0%   
```

5. Identify the node that consumes the most CPU(cores).
**controlplane**

6. Identify the node that consumes the most Memory(bytes).
**controlplane**

7. Identify the POD that consumes the most Memory(bytes) in default namespace.
**rabbit**

```bash
kubectl top  pods -n default
NAME       CPU(cores)   MEMORY(bytes)   
elephant   23m          30Mi            
lion       1m           16Mi            
rabbit     152m         250Mi  
```
8.Identify the POD that consumes the least CPU(cores) in default namespace.
**lion**

