# Rolling Updates and Rollbacks

You don’t understand anything until you learn it more than one way.

– Marvin Minsky

1. We have deployed a simple web application. Inspect the PODs and the Services

Wait for the application to fully deploy and view the application using the link called Webapp Portal above your terminal.

**blue**

https://30080-port-t2ijc2nyqve5pb2s.labs.kodekloud.com/

```
kubectl get pods
NAME                        READY   STATUS    RESTARTS   AGE
frontend-6765b99794-6f82d   1/1     Running   0          26s
frontend-6765b99794-9d2z8   1/1     Running   0          26s
frontend-6765b99794-b4bfg   1/1     Running   0          26s
frontend-6765b99794-tvf54   1/1     Running   0          26s


kubectl get svc
NAME             TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)          AGE
kubernetes       ClusterIP   10.43.0.1     <none>        443/TCP          6m4s
webapp-service   NodePort    10.43.123.4   <none>        8080:30080/TCP   92s
```
2. What is the current color of the web application?
Access the Webapp Portal.

**blue**

3. Run the script named curl-test.sh to send multiple requests to test the web application. Take a note of the output.

Execute the script at /root/curl-test.sh.

``` bash /root/curl-test.sh ```

```sh
controlplane ~ ➜  cat /root/curl-test.sh
for i in {1..35}; do
   kubectl exec --namespace=kube-public curl -- sh -c 'test=`wget -qO- -T 2  http://webapp-service.default.svc.cluster.local:8080/info 2>&1` && echo "$test OK" || echo "Failed"';
   echo ""
done
```

4. Inspect the deployment in the default namespace and identify the number of PODs deployed by it
 They are **4 pods**

``` 
frontend-6765b99794-6f82d   1/1     Running   0          26s
frontend-6765b99794-9d2z8   1/1     Running   0          26s
frontend-6765b99794-b4bfg   1/1     Running   0          26s
frontend-6765b99794-tvf54   1/1     Running   0          26s
```


``` kubectl get deploy frontend -o yaml ```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: "1"
  creationTimestamp: "2025-08-22T14:43:59Z"
  generation: 1
  name: frontend
  namespace: default
  resourceVersion: "883"
  uid: 87851ba4-5be7-4e40-80ef-30018f0f43df
spec:
  minReadySeconds: 20
  progressDeadlineSeconds: 600
  replicas: 4
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      name: webapp
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      creationTimestamp: null
      labels:
        name: webapp
    spec:
      containers:
      - image: kodekloud/webapp-color:v1
        imagePullPolicy: IfNotPresent
        name: simple-webapp
         ports:
        - containerPort: 8080
          protocol: TCP
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
status:
  availableReplicas: 4
  conditions:
  - lastTransitionTime: "2025-08-22T14:44:24Z"
    lastUpdateTime: "2025-08-22T14:44:24Z"
    message: Deployment has minimum availability.
    reason: MinimumReplicasAvailable
    status: "True"
    type: Available
  - lastTransitionTime: "2025-08-22T14:43:59Z"
    lastUpdateTime: "2025-08-22T14:44:24Z"
    message: ReplicaSet "frontend-6765b99794" has successfully progressed.
    reason: NewReplicaSetAvailable
    status: "True"
    type: Progressing
  observedGeneration: 1
  readyReplicas: 4
  replicas: 4
  updatedReplicas: 4
```

5. What container image is used to deploy the applications?

**kodekloud/webapp-color:v1**

 ```bash 
 kubectl get deploy frontend -o yaml | grep -i image
      - image: kodekloud/webapp-color:v1
        imagePullPolicy: IfNotPresent
```

6. Inspect the deployment and identify the current strategy

**RollingUpdate**

```bash 
kubectl get deploy frontend -o yaml | grep -i type
    type: RollingUpdate
    type: Available
    type: Progressing
```

7. If you were to upgrade the application now what would happen?

**Hint**
The Kubernetes Deployment uses the **RollingUpdate** strategy with maxUnavailable set to **25%**.
This means that during an upgrade, at most **25%** of the pods can be unavailable at any given time.

8. Let us try that. Upgrade the application by setting the image on the deployment to kodekloud/webapp-color:v2
Do not delete and re-create the deployment. Only set the new image name for the existing deployment.

**Hint**
Run the command ``` kubectl edit deployment frontend ``` and modify the required field.
The kubectl edit command allows you to open and directly edit a Kubernetes resource (such as a Deployment) in your default text editor. When you save and close the editor, your changes are automatically applied to the cluster.

```bash
kubectl get deploy frontend -o yaml > frontend-deploy.yaml 
nano frontend-deploy.yaml 

# changed
- image: kodekloud/webapp-color:v2
```

9. Run the script curl-test.sh again. Notice the requests now hit both the old and newer versions (If checked immediately). However none of them fail.

Execute the script at ``` /root/curl-test.sh ```

``` bash /root/curl-test.sh ```

```bash
cat curl-test.sh 
for i in {1..35}; do
   kubectl exec --namespace=kube-public curl -- sh -c 'test=`wget -qO- -T 2  http://webapp-service.default.svc.cluster.local:8080/info 2>&1` && echo "$test OK" || echo "Failed"';
   echo ""
done
```
10. Up to how many PODs can be down for upgrade at a time
Consider the current strategy settings and number of PODs - 4

**1**

11. Change the deployment strategy to Recreate

Delete and re-create the deployment if necessary. Only update the strategy type for the existing deployment.

- Deployment Name: frontend

- Deployment Image: kodekloud/webapp-color:v2

- Strategy: Recreate

**Hint**

Run the command kubectl edit deployment frontend and modify the required field. Make sure to delete the properties of rollingUpdate as well, set at spec.strategy.rollingUpdate.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
  namespace: default
spec:
  replicas: 4
  selector:
    matchLabels:
      name: webapp
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        name: webapp
    spec:
      containers:
      - image: kodekloud/webapp-color:v2
        name: simple-webapp
        ports:
        - containerPort: 8080
          protocol: TCP
```

12. Upgrade the application by setting the image on the deployment to kodekloud/webapp-color:v3

Do not delete and re-create the deployment. Only set the new image name for the existing deployment.

**Hint**
Run the command: ``` kubectl edit deployment frontend ``` and modify the required field

```yaml
# changed v3
- image: kodekloud/webapp-color:v3
```

```bash
kubectl delete deploy frontend 

kubectl apply -f frontend-deploy.yaml 
```

13. Run the script curl-test.sh again. Notice the failures. Wait for the new application to be ready. Notice that the requests now do not hit both the versions

Execute the script at ``` /root/curl-test.sh ```

``` bash /root/curl-test.sh ```







