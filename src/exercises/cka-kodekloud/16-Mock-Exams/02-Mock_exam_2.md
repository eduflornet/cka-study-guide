# Mock Exam 2

A winner is a dreamer who never gives up.

– Nelson Mandela

1. Create a StorageClass named **local-sc** with the following specifications and set it as the default storage class:

- The provisioner should be **kubernetes.io/no-provisioner**
- The volume binding mode should be **WaitForFirstConsumer**
- Volume expansion should be enabled

Solution Steps
Method: Create YAML manifest for the StorageClass:

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: local-sc
  annotations:
    storageclass.kubernetes.io/is-default-class: "true"
provisioner: kubernetes.io/no-provisioner
volumeBindingMode: WaitForFirstConsumer
allowVolumeExpansion: true
```

Apply the manifest:

```sh
kubectl apply -f local-sc.yaml
```

[Reference StorageClass](https://kubernetes.io/docs/concepts/storage/storage-classes/)

2. Create a deployment named **logging-deployment** in the namespace **logging-ns** with **1** replica, with the following specifications:

- The main container should be named **app-container**, use the image **busybox**, and should run the following command to simulate writing logs:


``` sh -c "while true; do echo 'Log entry' >> /var/log/app/app.log; sleep 5; done" ```

Add a sidecar container named **log-agent** that also uses the **busybox** image and runs the command:

``` tail -f /var/log/app/app.log ```

**log-agent** logs should display the entries logged by the main app-container

Solution Steps
Step 1: Create the namespace (if it doesn't exist):

```sh
kubectl create namespace logging-ns
```

Step 2: Create the deployment

Imperative Method (partial - requires editing):

```sh
kubectl create deployment logging-deployment --image=busybox --replicas=1 -n logging-ns --dry-run=client -o yaml > logging-deployment.yaml
```

Then edit the generated YAML to add the sidecar container and commands.

Declarative Method (recommended):

Create a YAML file:

```yaml
# logging-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: logging-deployment
  namespace: logging-ns
spec:
  replicas: 1
  selector:
    matchLabels:
      app: logging-deployment
  template:
    metadata:
      labels:
        app: logging-deployment
    spec:
      containers:
      - name: app-container
        image: busybox
        command: ["sh", "-c", "while true; do echo 'Log entry' >> /var/log/app/app.log; sleep 5; done"]
        volumeMounts:
        - name: log-volume
          mountPath: /var/log/app
      - name: log-agent
        image: busybox
        command: ["tail", "-f", "/var/log/app/app.log"]
        volumeMounts:
        - name: log-volume
          mountPath: /var/log/app
      volumes:
      - name: log-volume
        emptyDir: {}
```

Apply the manifest:

```sh
kubectl apply -f logging-deployment.yaml
```

Verification:

```sh
kubectl get deployment logging-deployment -n logging-ns
kubectl get pods -n logging-ns
kubectl logs <pod-name> -c log-agent -n logging-ns
```
Key Points:

- Shared Volume: Both containers share an ``` emptyDir ``` volume mounted at ``` /var/log/app ```
- Main Container: Writes logs to ``` /var/log/app/app.log ``` every 5 seconds
- Sidecar Container: Continuously reads and displays the log file
- Communication: The containers communicate via the shared filesystem

Why Declarative is Better Here: The imperative method cannot directly create multi-container deployments with shared volumes, so the declarative YAML approach is more suitable for this complex configuration.


3. A Deployment named **webapp-deploy** is running in the **ingress-ns** namespace and is exposed via a Service named **webapp-svc**.

Create an Ingress resource called **webapp-ingress** in the same namespace that will route traffic to the service. The Ingress must:

- Use pathType: Prefix
- Route requests sent to path **/** to the backend service
- Forward traffic to port **80** of the service
- Be configured for the host **kodekloud-ingress.app**
- Test app availablility using the following command:

``` curl -s http://kodekloud-ingress.app/ ```

Solution Steps
Step 1: Verify the existing deployment and service:

```sh
kubectl get deployment webapp-deploy -n ingress-ns
kubectl get service webapp-svc -n ingress-ns
```

Step 2: Create the Ingress resource

Declarative Method (recommended):

Create a YAML file :

```yaml
# webapp-ingress.yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: webapp-ingress
  namespace: ingress-ns
spec:
  rules:
  - host: kodekloud-ingress.app
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: webapp-svc
            port:
              number: 80

```

Apply the manifest:

```sh
kubectl apply -f webapp-ingress.yaml
```

Step 3: Verification:

```sh
kubectl get ingress webapp-ingress -n ingress-ns
kubectl describe ingress webapp-ingress -n ingress-ns
```

Step 4: Test the application:

```sh
curl -s http://kodekloud-ingress.app/
```

Alternative: Imperative Method (limited functionality):

```sh
kubectl create ingress webapp-ingress --rule="kodekloud-ingress.app/=webapp-svc:80" -n ingress-ns
```

Key Points:
-Host-based routing: Traffic for kodekloud-ingress.app is routed to the service
-Path-based routing: All requests to / (and subpaths due to Prefix) are forwarded
-Service backend: Traffic is forwarded to webapp-svc on port 80
-Namespace: All resources (Ingress, Service, Deployment) are in ingress-ns

-Note: For the curl command to work properly, you may need to ensure that kodekloud-ingress.app resolves to your cluster's Ingress controller IP address (either through DNS or by adding an entry to hosts).

[Reference Service Networking/Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/)


4. Create a new deployment called **nginx-deploy**, with image **nginx:1.16** and **1** replica. Next, upgrade the deployment to version **1.17** using rolling update.

- Deployment: nginx-deploy, Image: nginx:1.16
- Image: nginx:1.16
- Version upgraded to 1.17

5. Create a new user called **john**. Grant him access to the cluster using a csr named **john-developer**. Create a role developer which should grant John the permission to create, list, get, update and delete pods in the development namespace . The private key exists in the location: ``` /root/CKA/john.key ``` and csr at ```/root/CKA/john.csr ```.


Important Note: As of kubernetes 1.19, the CertificateSigningRequest object expects a signerName.

Please refer to the documentation to see an example. [The documentation tab is available](https://kubernetes.io/docs/reference/access-authn-authz/certificate-signing-requests/#create-certificatesigningrequest)

6. Create an nginx pod called **nginx-resolver** using the image **nginx** and expose it internally with a service called **nginx-resolver-service**. Test that you are able to look up the service and pod names from within the cluster. Use the image: **busybox:1.28** for dns lookup. Record results in ``` /root/CKA/nginx.svc ``` and  ```/root/CKA/nginx.pod ```

7. Create a static pod on node01 called **nginx-critical** with the image **nginx**. Make sure that it is **recreated/restarted** automatically in case of a failure.

[The documentation of Static Pod is available](https://kubernetes.io/docs/tasks/configure-pod-container/static-pod/)

For example, use ``` /etc/kubernetes/manifests ``` as the static Pod path.

8. Create a Horizontal Pod Autoscaler with name **backend-hpa** for the deployment named **backend-deployment** in the backend namespace with the **webapp-hpa.yaml** file located under the root folder.
Ensure that the HPA scales the deployment based on memory utilization, maintaining an average memory usage of **65%** across all pods.
Configure the HPA with a minimum of 3 replicas and a maximum of **15**.

[The documentation of Horizontal Pod Autoscaler Walkthrough](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/)

9. Modify the existing web-gateway on **cka5673 namespace** to handle HTTPS traffic on port **443** for **kodekloud.com**, using a TLS certificate stored in a secret named **kodekloud-tls**.

10. On the cluster, the team has installed multiple helm charts on a different namespace. By mistake, those deployed resources include one of the vulnerable images called **kodekloud/webapp-color:v1**. Find out the release name and uninstall it.

[The documentation of Helm Charts](https://helm.sh/docs/topics/charts/)

11. You are requested to create a NetworkPolicy to allow traffic from frontend apps located in the **frontend namespace**, to backend apps located in the **backend namespace**, but not from the databases in the **databases namespace**. There are three policies available in the /root folder. Apply the most restrictive policy from the provided YAML files to achieve the desired result. Do not delete any existing policies.

[The documentation of NetworkPolicy](https://kubernetes.io/docs/concepts/services-networking/network-policies/) 













