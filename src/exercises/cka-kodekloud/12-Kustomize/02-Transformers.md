# Transformers

The more that you read, the more things you will know. The more that you learn, the more places you'll go.

– Dr. Seuss

A winner is a dreamer who never gives up.

– Nelson Mandela

1. Explore the files and directories which are available at the /root/code/k8s directory and be ready to answer the upcoming questions.

**sandbox: dev**

```yaml
# root/kustomization.yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

resources:
  - db/
  - monitoring/
  - nginx/

commonLabels:
  sandbox: dev
```

2. What is the name that will be prefixed before all database resources?

**data-**

```yaml
# db/kustomization.yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

resources:
  - NoSql/
  - Sql/
  - db-config.yaml

namePrefix: data-
```

3. What is the namespace that all the monitoring resources will be deployed to?

**logging**

```yaml
# monitoring/kustomization.yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

resources:
  - grafana-depl.yaml
  - grafana-service.yaml

namespace: logging
```

4. Assign the following annotation to all nginx and monitoring resources:

owner: bob@gmail.com

**Solution**
As we want to apply the annotation only to nginx and monitoring resources, we will modify the following files as shown below:

``` k8s/nginx/kustomization.yaml ``` and ``` k8s/monitoring/kustomization.yaml ```

```yaml
commonAnnotations:
  owner: bob@gmail.com
```

5. Transform all postgres images in the project to mysql.

**Solution**
Since the requirement was to change all postgres images to mysql this means adding an image transformer to the root kustomization.yaml file.

```bash
k8s/kustomization.yaml
```

```yaml
images:
  - name: postgres
    newName: mysql
```

6. Transform all nginx images in the nginx directory to nginx:1.23.

**Solution**
For this task, the kustomization.yaml file in the nginx directory needs to be modified with an image transformer.

Since the image itself isn’t changing and only a new tag is getting assigned, add the newTag property as shown below:

```bash
k8s/nginx/kustomization.yaml
```

```yaml
images:
  - name: nginx
    newTag: "1.23"
```


