
## Imperative commands

All the questions in this lab can be done imperatively. However, for some questions, you may need to first create the YAML file using imperative methods ```(for example, with --dry-run=client -o yaml). You can then modify the YAML as needed and create the object.


```shell
kubectl run redis --image redis:alpine --dry-run=client -o yaml > redis-pod.yaml
```

Create a service named redis-service to expose the existing redis pod within the cluster on port 6379.

Use imperative commands.

- Service: redis-service
- Port: 6379
- Type: ClusterIP

```shell
kubectl expose pod redis --name=redis-service --port=6379 --target-port=6379 --protocol=TCP
```

📌 Desglose del comando:
expose pod redis: indica que estás exponiendo el Pod llamado redis.
--name=redis-service: nombre del Service que se creará.
--port=6379: puerto por el que el Service será accesible.
--target-port=6379: puerto en el contenedor al que se redirige el tráfico.
--protocol=TCP: protocolo utilizado (Redis usa TCP).

Este comando crea un Service de tipo ClusterIP por defecto.


Create a deployment named webapp using the image kodekloud/webapp-color with 3 replicas.
Try to use imperative commands only. Do not create definition files.

- Name: webapp
- Image: kodekloud/webapp-color
- Replicas: 3

```shell
kubectl create deployment webapp --image kodekloud/webapp-color --replicas 3
```

Create a new pod called custom-nginx using the nginx image and run it on container port 8080.

```shell
kubectl run custom-nginx --image nginx --port 8080
```

Create a new deployment called redis-deploy in the dev-ns namespace with the redis image. It should have 2 replicas.

Use imperative commands.

```shell
kubectl create deployment redis-deploy -n dev-ns --image redis --replicas 2
```

Create a pod named httpd using the image httpd:alpine in the default namespace.
Then, create a service of type ClusterIP with the same name (httpd) that exposes the pod on port 80.


Try to do this with as few steps as possible.

```shell
kubectl expose pod httpd --name httpd  --port=80 --target-port=80 --protocol TCP
```






