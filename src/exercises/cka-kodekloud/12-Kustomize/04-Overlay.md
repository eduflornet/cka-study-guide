# Overlay

Tell me and I forget. Teach me and I remember. Involve me and I learn.

– Benjamin Franklin

The greatest glory in living, lies not in never falling, but in rising every time we fall.

– Nelson Mandela

**Kustomize** has the concepts of bases and overlays.
In this lab we will explore and understand how to work with these features of Kustomize.

1. In /root/code/k8s , we have already created the base and overlays directories with various kubernetes object files.

Please inspect the directories and files and answer the following questions

**base/** 

```yaml
# kustomization.yaml
resources:
  - api-deployment.yaml
  - db-configMap.yaml
  - mongo-depl.yaml
```

```yaml
# api-depòyment.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: api-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      component: api
  template:
    metadata:
      labels:
        component: api
    spec:
      containers:
        - name: api
          image: nginx
          env:
            - name: DB_CONNECTION
              value: db.kodekloud.com
```

```yaml
# db-configMap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: db-creds
data:
  username: mongo
  password: mypassword
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
          env:
            - name: MONGO_INITDB_ROOT_USERNAME
              valueFrom:
                configMapKeyRef:
                  name: db-creds
                  key: username
            - name: MONGO_INITDB_ROOT_PASSWORD
              valueFrom:
                configMapKeyRef:
                  name: db-creds
                  key: password
```

2. When deploying application to prod environment, what type of image will be used for the api-deployment?

**memcached**

**overlays/**

/prod

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
        - name: api
          image: memcached
```

3. How many replicas for api-deployment will get deployed in prod?

**2**

/prod

```yaml
# redis-depl.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      component: redis
  template:
    metadata:
      labels:
        component: redis
    spec:
      containers:
        - name: redis
          image: redis
```

4. What will be the value of the environment variable MONGO_INITDB_ROOT_PASSWORD in the mongo-deployment container in the staging environment?

**superp@ssword123**


/staging


```yaml
# configMap-patch.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: db-creds
data:
  username: mongo
  password: superp@ssword123
```

5. When deploying to prod how many total pods are created?

**five**


**Solution**

The prod environment will **deploy 2 api pods, 2 redis pods and 1 mongo pod**.

```bash
overlays/prod/kustomization.yaml
```

```yaml
resources:
  - redis-depl.yaml
```

```bash
overlays/prod/redis-depl.yaml
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-deployment
spec:
  replicas: 2
```

5. How many environment variables are set on the nginx container in the api-deployment in dev environment?

**three**

**Solution**
In the base configuration for api-deployment.yaml, 1 environment variable DB_CONNECTION is set.

In the dev overlay, a patch adds 2 extra environment variables DB_USERNAME & DB_PASSWORD.

```bash
base/api-deployment.yaml
```

```yaml
# api-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      component: api
  template:
    metadata:
      labels:
        component: api
    spec:
      containers:
        - name: api
          image: nginx
          env:
            - name: DB_CONNECTION
              value: db.kodekloud.com
```

```yaml
...
      containers:
        - name: api
          image: nginx
          env:
            - name: DB_CONNECTION
              value: db.kodekloud.com
...

```

```bash
overlays/dev/api-patch.yaml
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api-deployment
.
.
.
      containers:
        - name: api
          image: nginx
          env:
            - name: DB_USERNAME
              valueFrom:
                configMapKeyRef:
                  name: db-creds
                  key: username
            - name: DB_PASSWORD
              valueFrom:
                configMapKeyRef:
                  name: db-creds
                  key: password
```

6. Update the api image in the api-deployment to use caddy docker image in the QA environment.
Perform this using an inline JSON6902 patch.

Note: Please ensure to apply the updated config for QA environment before validation.

- Patch defined for correct k8s resource?
- replace patch for caddy docker image defined in kustomization.yaml?
- Deployment created with correct docker image?

In the ``` kustomization.yaml ``` file which is located at the ``` /root/code/k8s/overlays/QA ``` directory, add a json6902 patch to update the image as shown below:

```yaml
# kustomization.yaml
bases:
  - ../../base
commonLabels:
  environment: QA

  patches:
  - target:
      kind: Deployment
      name: api-deployment
    patch: |-
      - op: replace
        path: /spec/template/spec/containers/0/image
        value: caddy
```

After modifications, apply the changes to create updated deployments in QA environment:

```bash
kubectl apply -k /root/code/k8s/overlays/QA
```

7. A mysql database needs to be added only in the staging environment.

Create a mysql deployment in a file called mysql-depl.yaml and define the deployment name as mysql-deployment.

Deploy 1 replica of the mysql container using mysql image and set the following env variables:

```yaml
- name: MYSQL_ROOT_PASSWORD
  value: mypassword
```

NOTE: Please ensure to deploy the changes committed in the staging environment before validation.

- mysql-deployment deployment created?
- Replicas is set to 1?
- Correct container and image name used?
- Staging environment applied?
- mysql-depl.yaml included to staging kustomization.yaml?

First let's create ``` overlays/staging/mysql-depl.yaml ``` as per the requirements:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      component: mysql
  template:
    metadata:
      labels:
        component: mysql
    spec:
      containers:
        - name: mysql
          image: mysql
          env:
            - name: MYSQL_ROOT_PASSWORD
              value: mypassword
```

Now update the staging environment kustomization.yaml file to include the deployment file we created earlier:

```bash
overlays/staging/kustomization.yaml
```

```yaml
resources:
  - mysql-depl.yaml
```

Applying the changes to k8s cluster for staging environment:

```bash
kubectl apply -k /root/code/k8s/overlays/staging
```







