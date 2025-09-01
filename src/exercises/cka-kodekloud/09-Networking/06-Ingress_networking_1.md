# Ingress Networking 1

Hard work beats talent when talent doesn’t work hard.

– Tim Notke

People who are really serious about software should make their own hardware.

– Alan Kay


1. We have deployed Ingress Controller, resources and applications. Explore the setup.

Note: They are in different namespaces.

2. Which namespace is the Ingress Controller deployed in?

**ingress-nginx**

```bash
k get all -n ingress-nginx 
NAME                                            READY   STATUS      RESTARTS   AGE
pod/ingress-nginx-admission-create-5kc6s        0/1     Completed   0          4m20s
pod/ingress-nginx-admission-patch-wpz9p         0/1     Completed   0          4m20s
pod/ingress-nginx-controller-6556f7f5b8-m8p6f   1/1     Running     0          4m20s

NAME                                         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                      AGE
service/ingress-nginx-controller             NodePort    172.20.17.206    <none>        80:30080/TCP,443:32103/TCP   4m20s
service/ingress-nginx-controller-admission   ClusterIP   172.20.179.128   <none>        443/TCP                      4m20s

NAME                                       READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/ingress-nginx-controller   1/1     1            1           4m20s

NAME                                                  DESIRED   CURRENT   READY   AGE
replicaset.apps/ingress-nginx-controller-6556f7f5b8   1         1         1       4m20s

NAME                                       STATUS     COMPLETIONS   DURATION   AGE
job.batch/ingress-nginx-admission-create   Complete   1/1           10s        4m20s
job.batch/ingress-nginx-admission-patch    Complete   1/1           10s        4m20s
```


3. What name is the Ingress Controller deployed in?

**ingress-nginx-controller**

4. Which namespace are the applications deployed in?

**app-space**

```bash
    k get all -n app-space 
NAME                                   READY   STATUS    RESTARTS   AGE
pod/default-backend-569f95b877-hfk7r   1/1     Running   0          10m
pod/webapp-video-7d6646445c-vdhg2      1/1     Running   0          10m
pod/webapp-wear-7cf6df9954-88twh       1/1     Running   0          10m

NAME                              TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
service/default-backend-service   ClusterIP   172.20.92.26     <none>        80/TCP     10m
service/video-service             ClusterIP   172.20.65.88     <none>        8080/TCP   10m
service/wear-service              ClusterIP   172.20.154.236   <none>        8080/TCP   10m

NAME                              READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/default-backend   1/1     1            1           10m
deployment.apps/webapp-video      1/1     1            1           10m
deployment.apps/webapp-wear       1/1     1            1           10m

NAME                                         DESIRED   CURRENT   READY   AGE
replicaset.apps/default-backend-569f95b877   1         1         1       10m
replicaset.apps/webapp-video-7d6646445c      1         1         1       10m
replicaset.apps/webapp-wear-7cf6df9954       1         1         1       10m
```
5. How many applications are deployed in the app-space namespace?

**Three apps**

Count the number of deployments in this namespace.

Run the command: kubectl get deploy --namespace app-space and count the number of deployments.

6. Which namespace is the Ingress Resource deployed in?

**app-space**

Run the command: kubectl get ingress --all-namespaces and identify the namespace.

```bash
k get ingress -A
NAMESPACE   NAME                 CLASS    HOSTS   ADDRESS         PORTS   AGE
app-space   ingress-wear-watch   <none>   *       172.20.17.206   80      16m
```

7. What is the name of the Ingress Resource?

**ingress-wear-watch**

Run the command: 

```bash
k get ingress -A
NAMESPACE   NAME                 CLASS    HOSTS   ADDRESS         PORTS   AGE
app-space   ingress-wear-watch   <none>   *       172.20.17.206   80      16m
```
identify the name of Ingress Resource.

8. What is the Host configured on the Ingress Resource?

**Rules: all hosts** 

The host entry defines the domain name that users use to reach the application like www.google.com

Run the command: kubectl describe ingress --namespace app-space and look at Host under the Rules section.

```bash
k -n app-space describe ingress
Name:             ingress-wear-watch
Labels:           <none>
Namespace:        app-space
Address:          172.20.17.206
Ingress Class:    <none>
Default backend:  <default>
Rules:
  Host        Path  Backends
  ----        ----  --------
  *           
              /wear    wear-service:8080 (172.17.0.4:8080)
              /watch   video-service:8080 (172.17.0.5:8080)
Annotations:  nginx.ingress.kubernetes.io/rewrite-target: /
              nginx.ingress.kubernetes.io/ssl-redirect: false
Events:
  Type    Reason  Age                From                      Message
  ----    ------  ----               ----                      -------
  Normal  Sync    26m (x2 over 26m)  nginx-ingress-controller  Scheduled for sync
```

9. What backend is the /wear path on the Ingress configured with?

**wear-service**

Run the command: kubectl describe ingress --namespace app-space and look under the Rules section.

10. At what path is the video streaming application made available on the Ingress?

**/watch**

11. If the requirement does not match any of the configured paths in the Ingress, to which service are the requests forwarded?

**default-backend-service**

Execute the command ``` kubectl describe ingress --namespace app-space ``` and examine the Default backend field. If it displays <default>, proceed to inspect the ingress controller's manifest by executing ``` kubectl get deploy ingress-nginx-controller -n ingress-nginx -o yaml ```. In the manifest, search for the argument --default-backend-service

```bash
k -n ingress-nginx get deploy ingress-nginx-controller -o yaml | grep -i  "default-backend-service"
        - --default-backend-service=app-space/default-backend-service
```

12. Now view the Ingress Service using the tab at the top of the terminal. Which page do you see?
Click on the tab named Ingress.

**Error 404**

13. View the applications by appending /wear and /watch to the URL you opened in the previous step.

14. You are requested to change the URLs at which the applications are made available.
Make the video application available at /stream.

- Ingress: ingress-wear-watch

- Path: /stream

- Backend Service: video-service

- Backend Service Port: 8080

Is the application accessible at the /stream?

Run the command: kubectl edit ingress --namespace app-space and change the path to the video streaming application to /stream.

Solution manifest file to change the path to the video streaming application to /stream as follows:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
  name: ingress-wear-watch
  namespace: app-space
spec:
  rules:
  - http:
      paths:
      - backend:
          service:
            name: wear-service
            port: 
              number: 8080
        path: /wear
        pathType: Prefix
      - backend:
          service:
            name: video-service
            port: 
              number: 8080
        path: /stream
        pathType: Prefix
```

15. View the Video application using the /stream URL in your browser.

Click on the Ingress tab above your terminal, if its not open already, and append /stream to the URL in the browser.

16. A user is trying to view the /eat URL on the Ingress Service. Which page would he see?

If not open already, click on the Ingress tab above your terminal, and append /eat to the URL in the browser.

**404 error page**

17. Due to increased demand, your business decides to take on a new venture. You acquired a food delivery company. Their applications have been migrated over to your cluster.

Inspect the new deployments in the app-space namespace.

18. You are requested to add a new path to your ingress to make the food delivery application available to your customers.

- Make the new application available at /eat.

- ngress: ingress-wear-watch

- Path: /eat

- Backend Service: food-service

- Backend Service Port: 8080

- Is the application accessible at the /eat?

Run the command: kubectl edit ingress --namespace app-space and add a new Path entry for the new service.

Solution manifest file to add a new path to our ingress service to make the application available at /eat as follows:


```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
  name: ingress-wear-watch
  namespace: app-space
spec:
  rules:
  - http:
      paths:
      - backend:
          service:
            name: wear-service
            port: 
              number: 8080
        path: /wear
        pathType: Prefix
      - backend:
          service:
            name: video-service
            port: 
              number: 8080
        path: /stream
        pathType: Prefix
      - backend:
          service:
            name: food-service
            port: 
              number: 8080
        path: /eat
        pathType: Prefix
```











