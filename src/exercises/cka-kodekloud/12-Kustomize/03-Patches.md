# Patches

Real difficulties can be overcome; it is only the imaginary ones that are unconquerable.

– Theodore N. Vail

Losers visualize the penalties of failure, Winners visualize the rewards of success.

– Dr.Rob Gilbert

1. In this lab we will explore Kustomize patches.

It is another method to modifying Kubernetes configs.

2. We have created several Kubernetes resource files in /root/code/k8s along with it's corresponding 
kustomization.yaml file.

**three**

```yaml
# kustomization.yaml
resources:
  - mongo-depl.yaml
  - nginx-depl.yaml
  - mongo-service.yaml

patches:
  - target:
      kind: Deployment
      name: nginx-deployment
    patch: |-
      - op: replace
        path: /spec/replicas
        value: 3

  - target:
      kind: Deployment
      name: mongo-deployment
    path: mongo-label-patch.yaml

  - target:
      kind: Service
      name: mongo-cluster-ip-service
    patch: |-
      - op: replace
        path: /spec/ports/0/port
        value: 30000

      - op: replace
        path: /spec/ports/0/targetPort
        value: 30000
```

```bash
k apply -k .

service/mongo-cluster-ip-service created
deployment.apps/mongo-deployment created
deployment.apps/nginx-deployment created

k get pods

NAME                                READY   STATUS              RESTARTS   AGE
mongo-deployment-bf544b788-lmjpq    0/1     ContainerCreating   0          7s
nginx-deployment-69ff98dbb7-7gj8m   0/1     ContainerCreating   0          7s
nginx-deployment-69ff98dbb7-8thhc   0/1     ContainerCreating   0          7s
nginx-deployment-69ff98dbb7-fqfq4   0/1     ContainerCreating   0          7s
```

3. What are the labels that will be applied to the mongo deployment?

**cluster=staging, component=mongo, feature=db**

```yaml
# mongo-label-patch.yaml 
- op: add
  path: /spec/template/metadata/labels/cluster
  value: staging

- op: add
  path: /spec/template/metadata/labels/feature
  value: db
```

```yaml
# mongo-depl.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongo-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      component: mongo
  template:
    metadata:
      labels:
        component: mongo
    spec:
      containers:
        - name: mongo
          image: mongo
```

4. What is the target port of the mongo-cluster-ip-service?

**30000**

```bash
k describe svc mongo-cluster-ip-service | grep -i port
Port:                     <unset>  30000/TCP
TargetPort:               30000/TCP
```

```yaml
IP Families:              IPv4
IP:                       172.20.45.215
IPs:                      172.20.45.215
Port:                     <unset>  30000/TCP
TargetPort:               30000/TCP
Endpoints:                172.17.0.4:30000
Session Affinity:         None
Internal Traffic Policy:  Cluster
Events:                   <none>
...
```

```yaml
# mongo-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: mongo-cluster-ip-service
spec:
  type: ClusterIP
  selector:
    component: mongo
  ports:
    - port: 27017
      targetPort: 27017
```

5. We just added few files along with some modifications in the k8s directory, observe the changes and answer the following questions.

How many containers are in the api pod?

**Solution**
The api-deployment has one container defined but the patch in api-patch.yaml adds a new container using memcached image:


```yaml
# api-patch.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api-deployment
spec:
  template:
    spec:
      containers:
        - name: memcached
          image: memcached
```

6. What path in the mongo container is the mongo-volume volume mounted at?

**/data/db**


**Solution**

The patch in mongo-patch.yaml mounts the mongo-volume in /data/db:mongo-patch.yaml

```yaml
# mongo-patch.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongo-deployment
spec:
  template:
    spec:
      containers:
        - name: mongo
          volumeMounts:
            - mountPath: /data/db
              name: mongo-volume
      volumes:
        - name: mongo-volume
          persistentVolumeClaim:
            claimName: host-pvc
```

7. In api-patch.yaml create a strategic merge patch to remove the memcached container.

**Solution**

```yaml
# api-patch.yaml file

apiVersion: apps/v1
kind: Deployment
metadata:
  name: api-deployment
spec:
  template:
    spec:
      containers:
        - $patch: delete
          name: memcached
```
Let's apply the config too:

```bash
kubectl apply -k /root/code/k8s/
```

- Correct target deployment specified in api-patch.yaml?
- Correct operation and container specified in api-patch.yaml ?

8. Create an inline json6902 patch in the kustomization.yaml file to remove the label org: KodeKloud from the mongo-deployment.

**Solution**

```yaml
# kustomization.yaml
patches:
  - target:
      kind: Deployment
      name: mongo-deployment
    patch: |-
      - op: remove
        path: /spec/template/metadata/labels/org
```

Let's apply the config too:

```bash
kubectl apply -k /root/code/k8s/
```

- Correct target for patch specified ?
- Correct operation for label specified?



