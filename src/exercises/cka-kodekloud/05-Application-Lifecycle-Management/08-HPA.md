# Horizontal Pod Autoscaler

Practice as if you are the worst, Perform as if you are the best.

1. Objectives

- HPA with Imperative commands
- Requirements for HPA to work
- What happens when resources.limit is not mentioned

2. Create a Deployment

Using the ``` /root/deployment.yml ``` manifest file provided , create a Kubernetes deployment for the nginx application.

Click on Skooner to access the monitoring tool and view the resources in the Kubernetes cluster.

Token for the Skooner can be found in ``` /root/skooner-sa-token.txt ```

```yaml
# deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 7
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```

3. We have a manifest file to create autoscaling for the Nginx deployment located at ``` /root/autoscale.yml ```. Review the manifest file and identify the current replicas and desired replicas?

- A. Current replicas= 7
    Desired replicas= 3

- B. Current replicas= 3
    Desired replicas= 7

- C. Current replicas= 7
    Desired replicas= 1

- D. Current replicas= 0
    Desired replicas= 0

**Solution**

To find the current replicas and desired replicas, check the /root/autoscale.yml and look for the value associated with the attribute currentReplicas and desiredReplicas.

**desiredReplicas**: 0
**currentReplicas**: 0

```yaml 
# autoscale.yml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  creationTimestamp: null
  name: nginx-deployment
spec:
  maxReplicas: 3
  metrics:
  - resource:
      name: cpu
      target:
        averageUtilization: 80
        type: Utilization
    type: Resource
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: nginx-deployment
status:
  currentMetrics: null
  desiredReplicas: 0
  currentReplicas: 0
```

4. Create an autoscaler for the nginx-deployment with a maximum of 3 replicas and a CPU utilization target of 80%.

**Solution** Run the imperative command to create autoscale:

```bash
kubectl autoscale deployment nginx-deployment --max=3 --cpu-percent=80
```

Or use the ``` /root/autoscale.yml ``` manifest file to create autoscale.

```bash
kubectl apply -f /root/autoscale.yml
```

5. What is the primary purpose of the Horizontal Pod Autoscaler (HPA) in Kubernetes?

The primary purpose of the Horizontal Pod Autoscaler (HPA) in Kubernetes is to automatically scale the number of pod replicas in a deployment, replica set, or stateful set based on observed resource usage, such as CPU or memory.

HPA helps maintain application performance and resource efficiency by increasing or decreasing the number of pods in response to real-time demand. It uses metrics from the Kubernetes Metrics Server and adjusts the replicas field accordingly.

🧪 Practical example
```bash
kubectl autoscale deployment my-app --cpu-percent=50 --min=2 --max=10
```
This command creates an HPA that keeps CPU usage around 50%, scaling between 2 and 10 replicas as needed.

6. What component in a Kubernetes cluster is responsible for providing metrics to the HPA?

**metric-server**

7. What is the current replica count of nginx-deployment after deploying the autoscaler?

**3**

```bash
k get all
NAME                                    READY   STATUS    RESTARTS   AGE
pod/nginx-deployment-647677fc66-66nqv   1/1     Running   0          20m
pod/nginx-deployment-647677fc66-fh65g   1/1     Running   0          20m
pod/nginx-deployment-647677fc66-rcfn4   1/1     Running   0          20m
```

8. What is the status of HPA target?

**unknown>/80%**

```bash
k get horizontalpodautoscaler
NAME               REFERENCE                     TARGETS              MINPODS   MAXPODS   REPLICAS   AGE
nginx-deployment   Deployment/nginx-deployment   cpu: <unknown>/80%   1         3         3          5m22s
```

9. The HPA status shows /80 for the CPU target. what could be a possible reason?

**It seems that in the deployment it was not defined**

```bash
k describe deploy nginx-deployment
Name:                   nginx-deployment
Namespace:              default
CreationTimestamp:      Sun, 24 Aug 2025 10:07:48 +0000
Labels:                 app=nginx
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=nginx
Replicas:               3 desired | 3 updated | 3 total | 3 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=nginx
  Containers:
   nginx:
    Image:         nginx:1.14.2
    Port:          80/TCP
    Host Port:     0/TCP
    Environment:   <none>
    Mounts:        <none>
  Volumes:         <none>
  Node-Selectors:  <none>
  Tolerations:     <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   nginx-deployment-647677fc66 (3/3 replicas created)
Events:
  Type    Reason             Age    From                   Message
  ----    ------             ----   ----                   -------
  Normal  ScalingReplicaSet  27m    deployment-controller  Scaled up replica set nginx-deployment-647677fc66 from 0 to 7
  Normal  ScalingReplicaSet  9m17s  deployment-controller  Scaled down replica set nginx-deployment-647677fc66 from 7 to 7
```

10. Since the HPA was failing due to the resource field missing in the nginx-deployment, the resource field has been updated in /root/deployment.yml. Update the nginx-deployment using this manifest. Watch the changes made to the nginx-deployment by the HPA after upgrading by using the kubectl get hpa --watch command.


```bash
k get hpa --watch
NAME               REFERENCE                     TARGETS              MINPODS   MAXPODS   REPLICAS   AGE
nginx-deployment   Deployment/nginx-deployment   cpu: <unknown>/80%   1         3         3          15m
```

**Solution**

To update the nginx-deployment, run the below command:

```bash
kubectl apply -f /root/deployment.yml
```
To watch the changes made to the nginx-deployment by the HPA, use the below command:

```bash
kubectl get hpa --watch

nginx-deployment   Deployment/nginx-deployment   cpu: <unknown>/80%   1         3         3          21m
nginx-deployment   Deployment/nginx-deployment   cpu: 0%/80%          1         3         3          21m
nginx-deployment   Deployment/nginx-deployment   cpu: 0%/80%          1         3         1          21m
```

11. What does the event ScalingReplicaSet in the nginx-deployment HPA indicate?

The number of PODS is increasing

**Solution**

To find out what the event ScalingReplicaSet means in the HPA, run the command below:

```bash
kubectl events hpa nginx-deployment | grep -i "ScalingReplicaSet"

5m                     Normal    ScalingReplicaSet              Deployment/nginx-deployment                Scaled up replica set nginx-deployment-647677fc66 from 0 to 7
27m                     Normal    ScalingReplicaSet              Deployment/nginx-deployment                Scaled down replica set nginx-deployment-647677fc66 from 7 to 3
6m56s                   Normal    ScalingReplicaSet              Deployment/nginx-deployment                Scaled up replica set nginx-deployment-647677fc66 from 3 to 7
6m56s                   Normal    ScalingReplicaSet              Deployment/nginx-deployment                Scaled up replica set nginx-deployment-7998fdcbb8 from 0 to 2
6m56s                   Normal    ScalingReplicaSet              Deployment/nginx-deployment                Scaled down replica set nginx-deployment-647677fc66 from 7 to 6
6m55s                   Normal    ScalingReplicaSet              Deployment/nginx-deployment                Scaled down replica set nginx-deployment-647677fc66 from 6 to 3
6m50s                   Normal    ScalingReplicaSet              Deployment/nginx-deployment                Scaled up replica set nginx-deployment-7998fdcbb8 from 1 to 2
6m50s                   Normal    ScalingReplicaSet              Deployment/nginx-deployment                Scaled down replica set nginx-deployment-647677fc66 from 3 to 2
6m46s                   Normal    ScalingReplicaSet              Deployment/nginx-deployment                Scaled down replica set nginx-deployment-647677fc66 from 2 to 1
6m46s (x2 over 6m56s)   Normal    ScalingReplicaSet              Deployment/nginx-deployment                Scaled up replica set nginx-deployment-7998fdcbb8 from 2 to 3
6m44s                   Normal    ScalingReplicaSet              Deployment/nginx-deployment                (combined from similar events): Scaled down replica set nginx-deployment-647677fc66 from 1 to 0
6m25s (x2 over 6m55s)   Normal    ScalingReplicaSet              Deployment/nginx-deployment                Scaled down replica set nginx-deployment-7998fdcbb8 from 3 to 1
```

12. What is the cause of the FailedGetResourceMetric event in the nginx-deployment HPA?

**cpu o memory request are missing for a container**

```bash
k events hpa nginx-deployment | grep -i "FailedGetResourceMetric"
30m                  Warning   FailedGetResourceMetric        HorizontalPodAutoscaler/nginx-deployment   failed to get cpu utilization: missing request for cpu in container nginx of Pod nginx-deployment-647677fc66-fh65g
28m                  Warning   FailedGetResourceMetric        HorizontalPodAutoscaler/nginx-deployment   failed to get cpu utilization: missing request for cpu in container nginx of Pod nginx-deployment-647677fc66-rcfn4
11m (x65 over 31m)   Warning   FailedGetResourceMetric        HorizontalPodAutoscaler/nginx-deployment   failed to get cpu utilization: missing request for cpu in container nginx of Pod nginx-deployment-647677fc66-66nqv
```








