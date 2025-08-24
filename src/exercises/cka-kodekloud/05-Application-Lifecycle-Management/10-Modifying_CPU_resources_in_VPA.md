# Modifying CPU resources in VPA

Learning never exhausts the mind.

– Leonardo da Vinci

Losers visualize the penalties of failure, Winners visualize the rewards of success.

– Dr.Rob Gilbert

## VPA CPU Optimization Lab

In this lab, you will deploy a sample application on Kubernetes, monitor its CPU resource usage, and utilize a Vertical Pod Autoscaler (VPA) to manage and adjust the resource allocation for the pods. The goal is to observe how the VPA automatically recommends and adjusts memory resource limits, particularly when the application experiences increased load.

**Objectives:**
Deploy a sample application and verify that the pods are running correctly.
Monitor pod resource usage using the kubectl top command to assess CPU and memory consumption.
Deploy a Vertical Pod Autoscaler (VPA) to observe how it adjusts CPU and memory resource recommendations based on the application’s current usage.
Introduce load to the application and monitor how VPA dynamically updates its recommendations in response to increased demand.
Interpret VPA recommendations by understanding key parameters like **lowerBound**, **upperBound**, and **uncappedTarget** for resource management.
By the end of this lab, you will have a clear understanding of how VPA optimizes CPU resource usage in a Kubernetes environment, improving application performance and efficiency under varying workloads.

### 1 

A file named ``` vpa-cpu-testing.yml ``` has been prepared and is located in the ``` /root directory ```. Proceed to deploy this file.

Have you deployed the specified file?

```yaml
# vpa-cpu-testing.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: flask-app-4
  labels:
    app: flask-app-4
spec:
  replicas: 2
  selector:
    matchLabels:
      app: flask-app-4
  template:
    metadata:
      labels:
        app: flask-app-4
    spec:
      containers:
      - name: flask-app-4
        image:  kodekloud/flask-session-app:1 
        ports:
        - name: http
          containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: flask-app-4-service
  labels:
    app: flask-app-4
spec:
  type: NodePort
  selector:
    app: flask-app-4
  ports:
  - name: http
    protocol: TCP
    port: 80
    targetPort: 8080
    nodePort: 30080
```
```bash
k apply -f vpa-cpu-testing.yml 

deployment.apps/flask-app-4 created
service/flask-app-4-service created

k get all

NAME                               READY   STATUS    RESTARTS   AGE
pod/flask-app-4-7dcd9549fc-w47x2   1/1     Running   0          6s
pod/flask-app-4-7dcd9549fc-wv97l   1/1     Running   0          6s

NAME                          TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
service/flask-app-4-service   NodePort    172.20.52.173   <none>        80:30080/TCP   6s
service/kubernetes            ClusterIP   172.20.0.1      <none>        443/TCP        79m

NAME                          READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/flask-app-4   2/2     2            2           6s

NAME                                     DESIRED   CURRENT   READY   AGE
replicaset.apps/flask-app-4-7dcd9549fc   2         2         2       6s
```

### 2

Monitoring Pod Resource Usage
To check the current resource usage (CPU and memory) of all running pods in your Kubernetes cluster, use the ``` kubectl top pod ``` command. This command retrieves and displays resource metrics directly from the cluster's resource metrics API. It is particularly useful for tracking how efficiently your workloads are consuming cluster resources.

To view the resource consumption of the pods, run the following command:

```bash
kubectl top pod
```

The output will display a table with each pod's name, namespace, CPU usage (in millicores), and memory usage (in mebibytes), allowing you to monitor resource usage in real time.

For example, the output may look like this:

```bash
NAME                           CPU(cores)   MEMORY(bytes)   
flask-app-4-5cfb5d78c4-p9l2m   1m           19Mi            
flask-app-4-5cfb5d78c4-t229n   1m           19Mi            
```

This output provides an overview of how much CPU and memory each pod is currently consuming.

Note: The metrics server may take some time to collect metrics from newly deployed pods.

### 3

A file named ``` vpa-cpu.yml ``` has been created in the ``` /root directory ```. Proceed to deploy this file.

Once deployed, check the cpu consumption by running the below command:

```bash
kubectl get vpa
```

```yaml
vpa-cpu.yml
---
apiVersion: "autoscaling.k8s.io/v1"
kind: VerticalPodAutoscaler
metadata:
  name: flask-app
spec:
  targetRef:
    apiVersion: "apps/v1"
    kind: Deployment
    name: flask-app-4
  updatePolicy:
    updateMode: "Off"  # You can set this to "Auto" if you want automatic updates
  resourcePolicy:
    containerPolicies:
      - containerName: '*'
        minAllowed:
          cpu: 100m
        maxAllowed:
          cpu: 1000m
        controlledResources: ["cpu"]
```
```bash
controlplane ~ ➜  k apply -f vpa-cpu.yml 
verticalpodautoscaler.autoscaling.k8s.io/flask-app created

controlplane ~ ➜  k get vpa
NAME        MODE   CPU    MEM   PROVIDED   AGE
flask-app   Off    100m         True       9s
```

### 4

Initiate the load on the flask-app-4 deployment by executing the script located at ``` /root/load.sh ```.

Note:

The ``` /root/load.sh ``` script initiates continuous background load on the ``` flask-app-4``` deployment.
Please do not terminate or interrupt the load process, as Task 6 depends on the VPA having enough usage data to generate a target CPU recommendation.

```bash
controlplane ~ ➜  cat load.sh 
#!/bin/bash

echo "Load initiated in the background. Please do not terminate this process."

timeout 1000s bash -c 'for i in {1..10}; do (while true; do curl -s http://controlplane:30080 > /dev/null; done) & done; wait'
```

**Solution**

To introduce load on the flask-app-4 deployment, please execute the following command:

```bash
/root/load.sh &
```

### 5

Capture the recommended target CPU value from the flask-app VPA and store it in ``` /root/target ```.
Note:

If you do not see a command prompt after running the load script, it means the terminal is still busy running the script in the foreground.
In that case, please open a new terminal tab to complete this task without interrupting the load.

**Solution**

Use the ``` kubectl get ``` command to identify the recommended target CPU value:

```bash
   kubectl get vpa flask-app -o yaml
```

- Look for the Recommendation section under the Status field.
- Identify the target.cpu value for the relevant container (e.g., flask-app-4).
- Manually note the target CPU value and store it in ``` /root/target ```.
Example Output

```yaml
# flash-app
apiVersion: autoscaling.k8s.io/v1
kind: VerticalPodAutoscaler
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"autoscaling.k8s.io/v1","kind":"VerticalPodAutoscaler","metadata":{"annotations":{},"name":"flask-app","namespace":"default"},"spec":{"resourcePolicy":{"containerPolicies":[{"containerName":"*","controlledResources":["cpu"],"maxAllowed":{"cpu":"1000m"},"minAllowed":{"cpu":"100m"}}]},"targetRef":{"apiVersion":"apps/v1","kind":"Deployment","name":"flask-app-4"},"updatePolicy":{"updateMode":"Off"}}}
  creationTimestamp: "2025-08-24T19:46:51Z"
  generation: 1
  name: flask-app
  namespace: default
  resourceVersion: "8675"
  uid: 67cf565b-4b7f-4982-955a-84b4d3e572f7
spec:
  resourcePolicy:
    containerPolicies:
    - containerName: '*'
      controlledResources:
      - cpu
      maxAllowed:
        cpu: 1000m
      minAllowed:
        cpu: 100m
  targetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: flask-app-4
  updatePolicy:
    updateMode: "Off"
status:
  conditions:
  - lastTransitionTime: "2025-08-24T19:46:56Z"
    status: "True"
    type: RecommendationProvided
  recommendation:
    containerRecommendations:
    - containerName: flask-app-4
      lowerBound:
        cpu: 100m
      target:
        cpu: 410m
      uncappedTarget:
        cpu: 410m
      upperBound:
        cpu: "1"
```


En la última tarea, el objetivo es capturar el valor recomendado de CPU del VPA para el contenedor flask-app-4 y almacenarlo en el archivo /root/target.

El valor recomendado de CPU que viste en los logs es 548m. Sin embargo, en el archivo target actualmente hay 143m, lo que indica que el valor en el archivo no coincide con la recomendación actual.

Para completar la tarea, debes:

Extraer el valor recomendado de CPU del comando kubectl.
Guardarlo en /root/target.
El comando que debes usar en la terminal del control plane sería:

```bash
kubectl get vpa flask-app -o jsonpath='{.status.recommendation.containerRecommendations[?(@.containerName=="flask-app-4")].target.cpu}' | tr -d 'm' > /root/target
```

