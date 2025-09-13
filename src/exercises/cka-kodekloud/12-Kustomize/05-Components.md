# Components

In learning you will teach, and in teaching you will learn.

– Phil Collins

1. What components are enabled in the community overlay?
All the configuration files reside inside the /root/code/project_mercury/ directory.


**auth**


```yaml
# /community/kustomization.yaml
bases:
  - ../../base

components:
  - ../../components/auth
```

2. What components are enabled in the dev overlay?

**auth, db, logging**

```yaml
# /overlays/dev/kustomization.yaml
bases:
  - ../../base

components:
  - ../../components/auth
  - ../../components/db
  - ../../components/logging
```

3. How many environment variables does the db component add to the api-deployment?

**two**

4. 

**Solution**

Navigate to the ``` /root/code/project_mercury/components ``` directory.

The db component includes a patch file named ``` api-patch.yaml ```, which modifies the api-deployment by adding two environment variables:

- DB_CONNECTION
- DB_PASSWORD
These variables are injected into the api container to enable connectivity with the database.

Below is the relevant configuration from the patch file:

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
          env:
            - name: DB_CONNECTION
              value: postgres-service
            - name: DB_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: db-creds
                  key: password
```

4. What is the name of the secret generator created in the db component?

**Solution**

Within the db component, the ``` kustomization.yaml ``` file defines a **secretGenerator** named **db-creds**. This generator creates a Kubernetes Secret with a key-value pair for the database password.

Below is the relevant configuration:

File Location: components/db/kustomization.yaml

```yaml
# components/db/kustomization.yaml
apiVersion: kustomize.config.k8s.io/v1alpha1
kind: Component

resources:
  - db-deployment.yaml
  - db-service.yaml

secretGenerator:
  - name: db-creds
    literals:
      - password=password1

patches:
  - path: api-patch.yaml
```

This secret is referenced in other configurations (e.g., the api-patch.yaml) to inject credentials securely into the application.

**Correct Answer:** The secret generator is named db-creds.

5. The community edition of the application should now ship with the logging component.
Please add the logging component to the community overlay. Once done, apply the updated configuration using:

```bash
kubectl apply -k /root/code/project_mercury/overlays/community
```

**Solution**

To include the logging functionality in the community edition of the application, update the ``` kustomization.yaml ``` file under the community overlay by adding the logging component path.

Update File:
``` code/project_mercury/overlays/community/kustomization.yaml ```

```yaml
components:
  - ../../components/auth
  - ../../components/logging
```

Ensure that ``` ../../components/logging ``` is listed under components along with any existing components like auth.

How to apply?

```bash
kubectl apply -k /root/code/project_mercury/overlays/community
```

6. A new caching component needs to be created for the application.

There is already a directory located at:

```bash
project_mercury/components/caching/
```

This directory contains the following files:

```yaml
redis-depl.yaml
redis-service.yaml
```

Finish setting up this component by creating a kustomization.yaml file in the same directory and importing the above Redis configuration files.

```yaml
apiVersion: kustomize.config.k8s.io/v1alpha1
kind: Component

resources:
  - redis-depl.yaml
  - redis-service.yaml
```

7. With the database setup for the caching component complete, we now need to update the api-deployment so that it can connect to the Redis instance.

Create a Strategic Merge Patch to add the following environment variable to the container in the deployment:

- Name: REDIS_CONNECTION
- Value: redis-service

Note:

The patch file must be created at:

```bash
project_mercury/components/caching/ with name api-patch.yaml
```

After creating the patch file, you must also update the ``` kustomization.yaml ``` file in the same directory ``` (components/caching/) ``` to include this patch under the patches field.

This step is essential — without updating ``` kustomization.yaml ```, the patch will not be applied when the component is used in an overlay.

**Solution**

Navigate to the ``` /root/code/project_mercury/ ``` directory.

1. Create the patch file
Location: ``` components/caching/api-patch.yaml ```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api-deployment
spec:
  template:
    spec:
      containers:
        - name: api
          env:
            - name: REDIS_CONNECTION
              value: redis-service
```

This patch ensures that the api container can access Redis using the specified environment variable.

2. Update the component's kustomization.yaml
Location: ``` components/caching/kustomization.yaml ```

Ensure that the ``` kustomization.yaml ``` file includes the ``` api-patch.yaml ``` under the patches field

```yaml
apiVersion: kustomize.config.k8s.io/v1alpha1
kind: Component

resources:
  - redis-depl.yaml
  - redis-service.yaml

patches:
  - path: api-patch.yaml

```

Note:
If you want to apply this component independently (e.g., using ``` kubectl apply -k components/caching/ ```), you will need to include ```../../base/ ``` in the resources field so that Kustomize can locate the original api-deployment for patching.

However, when this component is used within an overlay like ```overlays/enterprise/ ```, the base is already included at a higher level. Adding ``` ../../base/ ``` again inside the component will cause a duplicate resource error during ``` kubectl apply -k overlays/enterprise/. ```


8. Finally, let's add the caching component to the Enterprise edition of the application.

You need to update the ``` kustomization.yaml ``` file located in the ```code/project_mercury/overlays/enterprise/ ``` directory to include the caching component.

Once done, apply the updated configuration using:

```bash
kubectl apply -k /root/code/project_mercury/overlays/enterprise
```

```yaml
bases:
  - ../../base

components:
  - ../../components/auth
  - ../../components/db
  - ../../components/caching
```

















