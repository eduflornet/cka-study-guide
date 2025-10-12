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

[Reference Service Networking/Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/)

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


4. Create a new deployment called **nginx-deploy**, with image **nginx:1.16** and **1** replica. Next, upgrade the deployment to version **1.17** using rolling update.

- Deployment: nginx-deploy, Image: nginx:1.16
- Image: nginx:1.16
- Version upgraded to 1.17

Solution Steps
Step 1: Create the initial deployment

Imperative Method:

```sh
kubectl create deployment nginx-deploy --image=nginx:1.16 --replicas=1
```

Declarative Method: Create a YAML file :

```yaml
# nginx-deploy.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deploy
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx-deploy
  template:
    metadata:
      labels:
        app: nginx-deploy
    spec:
      containers:
      - name: nginx
        image: nginx:1.16
```

Apply the manifest:

```sh
kubectl apply -f nginx-deploy.yaml
```

Step 2: Verify the deployment was created:

```sh
kubectl get deployment nginx-deploy
kubectl get pods -l app=nginx-deploy
```

Step 3: Upgrade the deployment to nginx:1.17 using rolling update

Imperative Method (recommended for quick upgrade):

```sh
kubectl set image deployment/nginx-deploy nginx=nginx:1.17
```

Alternative Declarative Method: Edit the deployment:

```sh
kubectl edit deployment nginx-deploy
```

Change the image from nginx:1.16 to nginx:1.17 and save.

Step 4: Monitor the rolling update:

```sh
kubectl rollout status deployment/nginx-deploy
kubectl get pods -l app=nginx-deploy
```

Step 5: Verify the upgrade was successful:

```sh
kubectl describe deployment nginx-deploy | grep Image
kubectl get deployment nginx-deploy -o jsonpath='{.spec.template.spec.containers[0].image}'
```

Step 6: (Optional) Check rollout history:

```sh
kubectl rollout history deployment/nginx-deploy
```

Key Points:
-Rolling Update: The default deployment strategy that gradually replaces old pods with new ones
-Zero Downtime: The rolling update ensures the application remains available during the upgrade
-Image Update: The kubectl set image command is the fastest way to update container images
-Verification: Always verify the deployment shows the correct image version after the upgrade

Expected Output:
-The deployment should show nginx:1.17 as the current image
-The rollout should complete successfully with 1/1 replicas available
-The old pod with nginx:1.16 will be terminated and replaced with a new pod running nginx:1.17


5. Create a new user called **john**. Grant him access to the cluster using a csr named **john-developer**. Create a role developer which should grant John the permission to create, list, get, update and delete pods in the development namespace . The private key exists in the location: ``` /root/CKA/john.key ``` and csr at ```/root/CKA/john.csr ```.


Important Note: As of kubernetes 1.19, the CertificateSigningRequest object expects a signerName.

Please refer to the documentation to see an example. [The documentation tab is available](https://kubernetes.io/docs/reference/access-authn-authz/certificate-signing-requests/#create-certificatesigningrequest)

Solution Steps
Step 1: Create the CertificateSigningRequest (CSR)

Create a YAML file:

```yaml
# john-csr.yaml
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: john-developer
spec:
  request: LS0tLS1CRUdJTi... # (base64 encoded CSR content)
  signerName: kubernetes.io/kube-apiserver-client
  expirationSeconds: 86400  # one day
  usages:
  - client auth
```

To get the base64 encoded CSR content:

```sh
cat /root/CKA/john.csr | base64 | tr -d "\n"
```

Replace the request field with the output from the above command.

Step 2: Apply the CSR:

```sh
kubectl apply -f john-csr.yaml
```

Step 3: Approve the CSR:

```sh
kubectl certificate approve john-developer
```

Step 4: Extract the signed certificate:

```sh
kubectl get csr john-developer -o jsonpath='{.status.certificate}' | base64 -d > /root/CKA/john.crt
```

Step 5: Create the development namespace (if it doesn't exist):

```sh
kubectl create namespace development
```

Step 6: Create the Role in the development namespace

Create a YAML file:

```yaml
# developer-role.yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: development
  name: developer
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["create", "list", "get", "update", "delete"]
```

[Reference Role](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)

Apply the role:

```sh
kubectl apply -f developer-role.yaml
```

Step 7: Create a RoleBinding to bind the user to the role

Create a YAML file:

```yaml
# john-rolebinding.yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: john-developer-binding
  namespace: development
subjects:
- kind: User
  name: john
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: developer
  apiGroup: rbac.authorization.k8s.io

```

[kubectl create rolebinding](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_create/kubectl_create_rolebinding/)

Apply the rolebinding:

```sh
kubectl apply -f john-rolebinding.yaml
```

Step 8: (Optional) Create a kubeconfig for john

```sh
kubectl config set-credentials john --client-key=/root/CKA/john.key --client-certificate=/root/CKA/john.crt --embed-certs=true
kubectl config set-context john --cluster=kubernetes --user=john
```

Step 9: Verification:

```sh
kubectl get csr
kubectl describe role developer -n development
kubectl describe rolebinding john-developer-binding -n development
```

Test john's access:

```sh
kubectl auth can-i create pods --namespace=development --as=john
kubectl auth can-i delete pods --namespace=development --as=john
kubectl auth can-i create pods --namespace=default --as=john  # Should be "no"
```

Key Points:
-CSR Process: Create → Apply → Approve → Extract certificate
-RBAC: Role defines permissions, RoleBinding assigns permissions to users
-Namespace Scoped: The role only grants permissions within the development namespace
-Verification: Always test the permissions to ensure they work as expected
Important Notes:
-As of Kubernetes 1.19+, the signerName field is required in CSRs
-The role only allows pod operations in the development namespace
-John will not have access to any other resources or namespaces

6. Create an nginx pod called **nginx-resolver** using the image **nginx** and expose it internally with a service called **nginx-resolver-service**. Test that you are able to look up the service and pod names from within the cluster. Use the image: **busybox:1.28** for dns lookup. Record results in ``` /root/CKA/nginx.svc ``` and  ```/root/CKA/nginx.pod ```

Solution Steps
Step 1: Create the nginx pod

Imperative Method:

```sh
kubectl run nginx-resolver --image=nginx
```

Declarative Method: Create a YAML file:

```yaml
# nginx-resolver.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-resolver
  labels:
    app: nginx-resolver
spec:
  containers:
  - name: nginx
    image: nginx
```

Apply the manifest:

```sh
kubectl apply -f nginx-resolver.yaml
```

Step 2: Expose the pod with a service

Imperative Method:

```sh
kubectl expose pod nginx-resolver --name=nginx-resolver-service --port=80 --target-port=80
```

Declarative Method: Create a YAML file:

```yaml
# nginx-resolver-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-resolver-service
spec:
  selector:
    app: nginx-resolver
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
```

Apply the service:

```sh
kubectl apply -f nginx-resolver-service.yaml
```

Step 3: Verify the pod and service are created:

```sh
kubectl get pod nginx-resolver
kubectl get service nginx-resolver-service
```

Step 4: Test DNS lookup using busybox:1.28

Get the pod IP for DNS lookup:

```sh
kubectl get pod nginx-resolver -o wide
```

Create a busybox pod for testing DNS:

```sh
kubectl run test-nslookup --image=busybox:1.28 --rm -it --restart=Never -- /bin/sh
```

Step 5: Inside the busybox pod, perform DNS lookups:

For the service:

```sh
nslookup nginx-resolver-service
```

For the pod (using pod IP and default domain):

```sh
nslookup <pod-ip>.default.pod.cluster.local
```

Exit the busybox pod:

```sh
exit
```

Step 6: Record the results to files

For service DNS lookup:

```sh
kubectl run test-nslookup --image=busybox:1.28 --rm -it --restart=Never -- nslookup nginx-resolver-service > /root/CKA/nginx.svc
```

Step 7: Verify the recorded results:

```sh
cat /root/CKA/nginx.svc
cat /root/CKA/nginx.pod
```

Alternative Method using a single busybox pod:

```sh
# Create a busybox pod that stays running
kubectl run dns-test --image=busybox:1.28 --command -- sleep 3600

# Get pod IP
POD_IP=$(kubectl get pod nginx-resolver -o jsonpath='{.status.podIP}')

# Test service DNS and save result
kubectl exec dns-test -- nslookup nginx-resolver-service > /root/CKA/nginx.svc

# Test pod DNS and save result  
kubectl exec dns-test -- nslookup $POD_IP.default.pod.cluster.local > /root/CKA/nginx.pod

# Clean up test pod
kubectl delete pod dns-test

```

Key Points:
-Service DNS: Services are accessible via <service-name>.<namespace>.svc.cluster.local
-Pod DNS: Pods are accessible via <pod-ip>.<namespace>.pod.cluster.local
-busybox:1.28: This specific version includes the nslookup command
-Internal DNS: Kubernetes provides internal DNS resolution for services and pods
-Default namespace: If no namespace is specified, resources are created in the default namespace

Expected Results:
-/root/CKA/nginx.svc should contain DNS resolution for the service
-/root/CKA/nginx.pod should contain DNS resolution for the pod IP
-Both lookups should successfully resolve and show the correct IP addresses


7. Create a static pod on node01 called **nginx-critical** with the image **nginx**. Make sure that it is **recreated/restarted** automatically in case of a failure.

[The documentation of Static Pod is available](https://kubernetes.io/docs/tasks/configure-pod-container/static-pod/)

For example, use ``` /etc/kubernetes/manifests ``` as the static Pod path.

Solution Steps
Step 1: SSH into node01

```sh
ssh node01
```

Step 2: Navigate to the static pod directory

```sh
cd /etc/kubernetes/manifests
```

Step 3: Create the static pod manifest

Method 1: Create YAML file directly

Create a file named :

```yaml
# nginx-critical.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-critical
  labels:
    app: nginx-critical
spec:
  containers:
  - name: nginx-critical
    image: nginx
    ports:
    - containerPort: 80
  restartPolicy: Always
```

Method 2: Generate YAML using kubectl (from controlplane)

```sh
# From controlplane, generate the YAML
kubectl run nginx-critical --image=nginx --dry-run=client -o yaml > nginx-critical.yaml

# Copy to node01
scp nginx-critical.yaml node01:/etc/kubernetes/manifests/

```

Step 4: Save the file in the manifests directory

```sh
# If created directly on node01
sudo vi /etc/kubernetes/manifests/nginx-critical.yaml
# Paste the YAML content and save
```

Step 5: Verify the static pod is created

Wait a few seconds, then check from the controlplane:

```sh
kubectl get pods -o wide | grep nginx-critical
```

The pod should show as running on node01 with a name like **nginx-critical-node01**.

Step 6: Test automatic restart

Delete the pod to test if it gets recreated automatically:

```sh
kubectl delete pod nginx-critical-node01
```

After a few seconds, check again:

```sh
kubectl get pods | grep nginx-critical
```

The pod should be recreated automatically.

Key Points:
Static Pod Location: Static pods are defined by placing YAML files in /etc/kubernetes/manifests (default kubelet static pod path)
Automatic Management: The kubelet on node01 automatically manages static pods in this directory
Pod Naming: Static pods get a suffix with the node name (e.g., nginx-critical-node01)
Automatic Restart: If the pod fails or is deleted, kubelet automatically recreates it
restartPolicy: Set to Always (default) to ensure automatic restart on failure
Verification Commands:

```sh
# Check if the static pod is running 
kubectl get pods -o wide | grep nginx-critical
# Check pod details
kubectl describe pod nginx-critical-node01
# Test automatic restart by deleting the pod
kubectl delete pod nginx-critical-node01
# Verify it gets recreated
kubectl get pods | grep nginx-critical
```

Important Notes:
-Static pods are managed by the kubelet, not the API server
-They appear in kubectl get pods but cannot be managed through typical kubectl commands
-To modify or delete a static pod, you must edit or remove the YAML file from /etc/kubernetes/manifests
-The kubelet monitors this directory and automatically creates/updates/deletes pods based on the manifest files

8. Create a Horizontal Pod Autoscaler with name **backend-hpa** for the deployment named **backend-deployment** in the backend namespace with the **webapp-hpa.yaml** file located under the root folder.
Ensure that the HPA scales the deployment based on memory utilization, maintaining an average memory usage of **65%** across all pods.
Configure the HPA with a minimum of 3 replicas and a maximum of **15**.

Solution Steps
Step 1: Verify the existing deployment

```sh
kubectl get deployment backend-deployment -n backend
```

Step 2: Create the HPA YAML manifest

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: backend-hpa
  namespace: backend
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: backend-deployment
  minReplicas: 3
  maxReplicas: 15
  metrics:
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 65
  behavior:
    scaleUp:
      stabilizationWindowSeconds: 0
      policies:
      - type: Percent
        value: 100
        periodSeconds: 15
    scaleDown:
      stabilizationWindowSeconds: 300
      policies:
      - type: Percent
        value: 10
        periodSeconds: 60
```

[The documentation of Horizontal Pod Autoscaler Walkthrough](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/)


Step 3: Apply the HPA manifest

```sh
kubectl apply -f /root/webapp-hpa.yaml
```

Step 4: Verification

```sh
kubectl get hpa backend-hpa -n backend
kubectl describe hpa backend-hpa -n backend
```

Step 5: Check the current status

```sh
kubectl get hpa backend-hpa -n backend -w
```

Alternative: Using Imperative Command (Basic Setup)

```sh
kubectl autoscale deployment backend-deployment --name=backend-hpa --cpu-percent=65 --min=3 --max=15 -n backend
```

[Reference kubectl autoscale](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_autoscale/)


Then edit to change from CPU to memory:

```sh
kubectl edit hpa backend-hpa -n backend
```

Change the resource.name from cpu to memory and adjust the target utilization.

Key Points:
-Memory-based scaling: The HPA monitors memory utilization instead of CPU
-Target utilization: Maintains 65% average memory usage across all pods
-Replica limits: Minimum 3 replicas, maximum 15 replicas
-Namespace: All resources are in the backend namespace
-Scaling behavior: Optional configuration to control how fast the HPA scales up/down
Important Prerequisites:
-Metrics Server: Must be installed and running in the cluster
-Resource requests: The backend-deployment pods must have memory requests defined
-Memory limits: Recommended to have memory limits set on the containers

Verification Commands:

```sh
# Check if metrics server is running
kubectl get pods -n kube-system | grep metrics-server

# Check if the deployment has resource requests
kubectl describe deployment backend-deployment -n backend

# Monitor HPA status
kubectl get hpa backend-hpa -n backend --watch

# Check current resource usage
kubectl top pods -n backend
```

Expected Output:
-The HPA should show backend-deployment as the target
-Memory utilization should be displayed (may show <unknown> initially)
-The HPA will scale the deployment between 3-15 replicas based on memory usage
-When memory usage exceeds 65%, it scales up; when below 65%, it scales down

Note: The memory metrics may take a few minutes to appear after creating the HPA, as the metrics server needs time to collect data.


9. Modify the existing web-gateway on **cka5673 namespace** to handle HTTPS traffic on port **443** for **kodekloud.com**, using a TLS certificate stored in a secret named **kodekloud-tls**.

Solution Steps
Step 1: Check the existing Gateway resource


```sh
kubectl get gateway web-gateway -n cka5673
kubectl describe gateway web-gateway -n cka5673
```

Step 2: Verify the TLS secret exists

```sh
kubectl get secret kodekloud-tls -n cka5673
kubectl describe secret kodekloud-tls -n cka5673
```

Step 3: Edit the existing Gateway to add HTTPS support

Method 1: Using kubectl edit (imperative)

```sh
kubectl edit gateway web-gateway -n cka5673
```

Add the HTTPS listener configuration:

Method 2: Create/Update YAML manifest (declarative)

Create or update the Gateway YAML file:

```yaml
# web-gateway.yaml
apiVersion: gateway.networking.k8s.io/v1beta1
kind: Gateway
metadata:
  name: web-gateway
  namespace: cka5673
spec:
  gatewayClassName: nginx  # Keep existing gateway class
  listeners:
  - name: http
    port: 80
    protocol: HTTP
    hostname: kodekloud.com
  - name: https
    port: 443
    protocol: HTTPS
    hostname: kodekloud.com
    tls:
      mode: Terminate
      certificateRefs:
      - kind: Secret
        name: kodekloud-tls
```

Step 4: Apply the updated configuration

```sh
kubectl apply -f web-gateway.yaml
```

Step 5: Verification

```sh
kubectl get gateway web-gateway -n cka5673
kubectl describe gateway web-gateway -n cka5673
```

Check that the Gateway shows both HTTP (port 80) and HTTPS (port 443) listeners.

Step 6: Test HTTPS connectivity (if possible)

```sh
# Test if HTTPS is working
curl -k https://kodekloud.com:443/
```

Key Configuration Elements:
1. HTTPS Listener:
  - Port: 443
  - Protocol: HTTPS
  - Hostname: kodekloud.com
2. TLS Configuration:
  - Mode: Terminate (Gateway terminates TLS)
  - Certificate reference points to the kodekloud-tls secret
3. Preserve existing HTTP listener (if it exists) on port 80


Alternative: Minimal edit approach
If you want to modify only the specific parts, you can patch the Gateway:

```sh
kubectl patch gateway web-gateway -n cka5673 --type='json' -p='[
  {
    "op": "add",
    "path": "/spec/listeners/-",
    "value": {
      "name": "https",
      "port": 443,
      "protocol": "HTTPS",
      "hostname": "kodekloud.com",
      "tls": {
        "mode": "Terminate",
        "certificateRefs": [
          {
            "kind": "Secret",
            "name": "kodekloud-tls"
          }
        ]
      }
    }
  }
]'
```

Key Points:
-TLS Termination: The Gateway terminates HTTPS traffic and can forward HTTP to backend services
-Certificate Secret: Must contain tls.crt and tls.key data
-Hostname matching: Ensures only traffic for kodekloud.com uses this HTTPS configuration
-Preserve existing config: Don't remove existing HTTP listeners unless specified

Expected Result:
-The Gateway should accept both HTTP (port 80) and HTTPS (port 443) traffic
-HTTPS traffic for kodekloud.com will be encrypted using the certificate from kodekloud-tls secret
-The Gateway will terminate TLS and forward decrypted traffic to backend services

10. On the cluster, the team has installed multiple helm charts on a different namespace. By mistake, those deployed resources include one of the vulnerable images called **kodekloud/webapp-color:v1**. Find out the release name and uninstall it.

[The documentation of Helm Charts](https://helm.sh/docs/topics/charts/)

Solution Steps
Step 1: List all helm releases across all namespaces

```sh
helm list --all-namespaces
```
This will show all helm releases with their namespaces, release names, and chart information.

Step 2: Search for releases that might contain the vulnerable image

Method 1: Check helm release values

```sh
# For each release found in step 1, check the values
helm get values <release-name> -n <namespace>
```

Look for any reference to kodekloud/webapp-color:v1 in the output.

Method 2: Check helm release manifests

```sh
# Check the actual manifests deployed by each release
helm get manifest <release-name> -n <namespace>
```

Search for the vulnerable image in the output.

Step 3: Find pods using the vulnerable image

```sh
# Search for pods using the vulnerable image across all namespaces
kubectl get pods --all-namespaces -o jsonpath='{range .items[*]}{.metadata.namespace}{"\t"}{.metadata.name}{"\t"}{.spec.containers[*].image}{"\n"}{end}' | grep "kodekloud/webapp-color:v1"
```

Step 4: Identify which helm release deployed the vulnerable pod

```sh
# Check the labels on the vulnerable pod to identify the helm release
kubectl get pod <pod-name> -n <namespace> -o yaml | grep -E "(helm|chart|release)"
```

Look for labels like:

- app.kubernetes.io/managed-by: Helm
- helm.sh/chart: <chart-name>
- app.kubernetes.io/instance: <release-name>

Step 5: Alternative method - Search by helm release annotations

```sh
# List all deployments and check for helm annotations
kubectl get deployments --all-namespaces -o yaml | grep -B 10 -A 5 "kodekloud/webapp-color:v1"
```

Step 6: Once you identify the release name, uninstall it

```sh
helm uninstall <release-name> -n <namespace>
```

Step 7: Verify the release has been uninstalled

```sh
helm list --all-namespaces
kubectl get pods --all-namespaces | grep "kodekloud/webapp-color:v1"
```

Complete Example Workflow

```sh
# Step 1: List all helm releases
helm list --all-namespaces

# Example output:
# NAME        NAMESPACE   REVISION  UPDATED                   STATUS    CHART           APP VERSION
# webapp-v1   frontend    1         2024-01-01 10:00:00 UTC   deployed  webapp-0.1.0    v1
# backend-app backend     1         2024-01-01 10:05:00 UTC   deployed  backend-0.2.0   v2

# Step 2: Check each release for the vulnerable image
helm get values webapp-v1 -n frontend
helm get manifest webapp-v1 -n frontend | grep "kodekloud/webapp-color:v1"

# If found, uninstall the release
helm uninstall webapp-v1 -n frontend
```

Key Points:
-Helm releases are namespace-scoped: Always specify the correct namespace when checking or uninstalling
-Check both values and manifests: The vulnerable image might be specified in values.yaml or directly in templates
-Pod labels identify helm releases: Look for helm-specific labels and annotations
-Verify uninstallation: Always confirm the release and pods are completely removed

Expected Result:
-The helm release containing the kodekloud/webapp-color:v1 image will be identified and uninstalled
-All resources deployed by that helm release will be removed from the cluster
-No pods should remain running with the vulnerable image

Note: Make sure to only uninstall the release with the vulnerable image, not all releases. Double-check the release name and namespace before uninstalling.

11. You are requested to create a NetworkPolicy to allow traffic from frontend apps located in the **frontend namespace**, to backend apps located in the **backend namespace**, but not from the databases in the **databases namespace**. There are three policies available in the /root folder. Apply the most restrictive policy from the provided YAML files to achieve the desired result. Do not delete any existing policies.

[The documentation of NetworkPolicy](https://kubernetes.io/docs/concepts/services-networking/network-policies/) 















