# Manual Scaling

You don't get what you wish for. You get what you work for.

– Daniel Milstein

1. Manual Scaling of a Kubernetes Deployment
**Objectives**

- Understand the concept of scaling in Kubernetes
- Manually scale a deployment up and down
- Observe the effects of scaling on the application and resources

2. Create a Deployment

Using the ``` /root/deployment.yml ``` manifest file provided , create a Kubernetes deployment for the Flask application.
Discovery

- Use ``` kubectl get deployments ``` to observe the deployment status.
- Use ``` kubectl get pods ``` to see the running pods.


```yaml
# deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: flask-web-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: flask-app
  template:
    metadata:
      labels:
        app: flask-app
    spec:
      containers:
      - name: flask
        image: rakshithraka/flask-web-app
        ports:
        - containerPort: 80

apiVersion: v1
kind: Service
metadata:
  name: flask-web-app-service
spec:
  type: ClusterIP
  selector:
    app: flask-app
  ports:
   - port: 80
     targetPort: 80    
```

```bash
k get pods
NAME                             READY   STATUS    RESTARTS   AGE
flask-web-app-584dd886c8-btbj7   1/1     Running   0          2m27s
flask-web-app-584dd886c8-gn44r   1/1     Running   0          2m27s

k get deploy
NAME            READY   UP-TO-DATE   AVAILABLE   AGE
flask-web-app   2/2     2            2           2m41s
```

3. What is the primary purpose of the kubectl scale command?

4. Can the ``` kubectl scale ``` command be used to scale down a statefulset in Kubernetes?

The kubectl ``` scale ``` command can be used to scale both deployments and statefulsets. When scaling a statefulset, Kubernetes ensures that the state and order of the pods are maintained, unlike in deployments where pods can be created and destroyed in any order.

4. Manual Scale

Manually scale the deployment named flask-web-app to have 3 replicas.

**Observation**

Observe the changes with ``` kubectl get deployments ``` and ``` kubectl get pods ```.


To view the application, click on the Ingress button at the top of the terminal, or click on Skooner to access the monitoring tool and view the resources in the Kubernetes cluster.

Token for the Skooner can be found in ``` /root/skooner-sa-token.txt ```

5. If you scale a deployment using ``` kubectl scale ``` to a higher number of replicas, but the cluster has insufficient resources to accommodate all new replicas, what will happen?

When you scale a deployment to a higher number of replicas than the cluster can support due to resource constraints, Kubernetes will create as many replicas as possible within the available resources. The remaining replicas will be in a pending state until sufficient resources are freed up or added to the cluster. This behavior allows Kubernetes to manage resources dynamically while maintaining the desired state as closely as possible.






