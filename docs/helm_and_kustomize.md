# Helm and Kustomize

**Helm**
- It's a package manager for Kubernetes.
- It uses charts (YAML templates + values) to install applications.
- It allows you to easily install, update, and remove components.

✅ **Kustomize**
- It's a native kubectl tool.
- It allows you to customize YAMLs without templates.
- Ideal for managing multiple environments (dev, staging, prod) with clean transformations.

🛠️ **HELM**: Practice for the exam

1. Installing a component with Helm

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
helm install my-nginx bitnami/nginx
```

2. View status and resources

```bash
helm list
kubectl get all -l app.kubernetes.io/instance=my-nginx
```

3. Uninstall

```bash
helm uninstall my-nginx
```

4. Install with custom values

```bash
helm show values bitnami/nginx > custom-values.yaml
helm install my-nginx bitnami/nginx -f custom-values.yaml
```

🧩 KUSTOMIZE: Practice for the exam

🔹 1. Basic structure
```bash
mkdir kustomize-demo
cd kustomize-demo
```

Create the files:

**deployment.yaml**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
```

**kustomization.yaml**
```yaml
resources:
  - deployment.yaml
commonLabels:
  env: test
namePrefix: demo-
```

🔹 2. Apply with Kustomize
```bash
kubectl apply -k .
```

🔹 3. Validate
```bash
kubectl get deployment
kubectl describe deployment demo-nginx
```

🧠 Bonus: Overlay para múltiples entornos
```bash
mkdir overlays/dev
cp deployment.yaml overlays/dev/
```

**overlays/dev/kustomization.yaml**
```yaml
resources:
  - ../deployment.yaml
nameSuffix: -dev
replicas: 1
```

```bash
kubectl apply -k overlays/dev
```

📌 Tips for the exam
✅ Use kubectl explain if you forget the structure of a resource.

✅ Use --dry-run=client -o yaml to quickly generate manifests.

✅ Use kubectl apply -k to apply Kustomize without installing anything.

✅ Use helm show values to understand what you can customize in a chart.


Bonus: Use a patch to modify replicas
1. Recommended Structure
Let's say you have this deployment.yaml in base/:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
```

2. In your overlay (overlays/dev/), create a patch

**replicas-patch.yaml**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 1
```

3. And your kustomization.yaml in overlays/dev/ should look like this:

```yaml
resources:
  - ../../base/deployment.yaml

patchesStrategicMerge:
  - replicas-patch.yaml

nameSuffix: -dev
```

4. Apply with Kustomize
```bash
kubectl apply -k overlays/dev/
```

🧠 Key differences (for the exam)
- Helm -> Package manager -> Templates + values -> Ideal for complex apps with dependencies -> helm install, helm upgrade 	                                
- Kustomize -> YAML Customizer -> Pure YAML  + transformations -> Environments with variations -> kubectl apply -k


 ### 📚 Recommended resources

[Kustomize explained with practical examples](https://notes.kodekloud.com/docs/CKA-Certification-Course-Certified-Kubernetes-Administrator/2025-Updates-Kustomize-Basics/kustomization)

[Kustomize demo video for CKA 2025](https://www.youtube.com/watch?v=AKr5tc4nN2w)

https://kubernetes.io/docs/tasks/manage-kubernetes-objects/kustomization/

https://www.digitalocean.com/community/tutorials/how-to-manage-your-kubernetes-configurations-with-kustomize

https://www.digitalocean.com/community/tutorials/an-introduction-to-helm-the-package-manager-for-kubernetes



