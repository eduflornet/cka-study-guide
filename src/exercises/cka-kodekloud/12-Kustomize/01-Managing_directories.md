# Managing Directories

If you can't explain it simply you don't understand it well enough.

– Albert Einstein

1. Explore the directories and files within /root/code/k8s directory and answer the below question.

How many directories have been pre-defined in the k8s directory?

**three**

```bash
ls code/k8s/
db  message-broker  nginx
```


2. Let's create a single kustomization.yaml file in the root of the k8s directory and import all resources defined for db, message-broker, nginx into it.

Please ensure to apply the config after creating kustomization.yaml file.

```yaml
# kustomization.yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

# kubernetes resources to be managed by kustomize
resources:
  - db/db-config.yaml
  - db/db-depl.yaml
  - db/db-service.yaml
  - message-broker/rabbitmq-config.yaml
  - message-broker/rabbitmq-depl.yaml
  - message-broker/rabbitmq-service.yaml
  - nginx/nginx-depl.yaml
  - nginx/nginx-service.yaml
```

```bash
cd /root/code/k8s

kubectl apply -k .
-----------OR---------------
controlplane ~/code ➜  kustomize build k8s/ | kubectl apply -f -

configmap/db-credentials created
configmap/redis-credentials created
service/db-service created
service/nginx-service created
service/rabbit-cluster-ip-service created
deployment.apps/db-deployment created
deployment.apps/nginx-deployment created
deployment.apps/rabbitmq-deployment created
```

3. How many pods were deployed?
In the current(default) namespace.

**five**

```bash
k get pods
NAME                                   READY   STATUS    RESTARTS   AGE
db-deployment-7c4b7b54bf-86hzj         1/1     Running   0          2m34s
nginx-deployment-69ff98dbb7-4lnqd      1/1     Running   0          2m34s
nginx-deployment-69ff98dbb7-6kqxr      1/1     Running   0          2m34s
nginx-deployment-69ff98dbb7-rs4q9      1/1     Running   0          2m34s
rabbitmq-deployment-6fbd45d675-b2vvd   1/1     Running   0          2m34s

```

4. What is the type of the service that has been deployed for the message-broker?

**ClusterIP**

rabbit-cluster-ip-service   ClusterIP   172.20.85.95    <none>        5672/TCP          4m47s


```bash
k get svc
NAME                        TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)           AGE
db-service                  NodePort    172.20.127.84   <none>        27017:32603/TCP   4m47s
kubernetes                  ClusterIP   172.20.0.1      <none>        443/TCP           16m
nginx-service               NodePort    172.20.40.145   <none>        80:32212/TCP      4m47s
rabbit-cluster-ip-service   ClusterIP   172.20.85.95    <none>        5672/TCP          4m47s
```

5. Let's create a kustomization.yaml file in each of the subdirectories and import only the resources within that directory.

Please ensure to specify those directories within the root kustomization.yaml file.

NOTE: Don't forget to deploy the resources.

```bash
cd code/k8s/db
touch kustomization.yaml

```

```yaml
# db/kustomization.yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

# kubernetes resources to be managed by kustomize
resources:
  - db-config.yaml
  - db-depl.yaml
  - db-service.yaml
```

```yaml
# message-broker/kustomization.yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

# kubernetes resources to be managed by kustomize
resources:
  - rabbitmq-config.yaml
  - rabbitmq-depl.yaml
  - rabbitmq-service.yaml
```

```yaml
# nginx/kustomization.yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

# kubernetes resources to be managed by kustomize
resources:
  - nginx-depl.yaml
  - nginx-service.yaml
```


```yaml
# Root kustomization.yaml file for k8s directory
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

# kubernetes resources to be managed by kustomize
resources:
  - db/
  - message-broker/
  - nginx/
#Customizations that need to be made
```

6. How many pods were created in total?

**six**

```bash
k get pods
NAME                                   READY   STATUS    RESTARTS   AGE
db-deployment-7c4b7b54bf-qj5jb         1/1     Running   0          29s
nginx-deployment-69ff98dbb7-ktfqf      1/1     Running   0          29s
nginx-deployment-69ff98dbb7-m2z6h      1/1     Running   0          29s
nginx-deployment-69ff98dbb7-qdnwl      1/1     Running   0          29s
rabbitmq-deployment-6fbd45d675-g9pl4   1/1     Running   0          29s
rabbitmq-deployment-6fbd45d675-wtlch   1/1     Running   0          29s
```

