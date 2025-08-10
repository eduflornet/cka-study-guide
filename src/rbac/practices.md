# RBAC Practices with KinD (k8s in docker)

This document provides practices I have carried out during CKA preparation focused on Role-Based Access Control (RBAC). Here you will find explanations and commands.

## Create a CSR for the user or service

```bash
# Generate private key for johndoe
openssl genrsa -out johndoe.key 2048
```

## Create CSR

```bash
openssl req -new -key johndoe.key \
  -subj "/CN=johndoe/O=cka-study-guide" \
  -out johndoe.csr
```

## Get the certificate and CA key from the control-plane container:

🔧 Option 1: Use KinD cluster CA
KinD creates its own internal CA to sign certificates, but it does not expose it easily. To sign your CSR (johndoe.csr) with KinD's CA, you need to:

1. 🔍 Extract the cluster CA

You can get the CA certificate and key from the control-plane container:

```bash
# Find the name of the control-plane container
docker ps --filter name=kind-control-plane

# Copy the CA files from the container
docker cp kind-control-plane:/etc/kubernetes/pki/ca.crt ./ca.crt
docker cp kind-control-plane:/etc/kubernetes/pki/ca.key ./ca.key
```

2. ✅ Adapt the OpenSSL command
Once you have ca.crt and ca.key on your local machine, you can sign the CSR like this:

```bash
openssl x509 -req -in johndoe.csr \
  -CA ca.crt \
  -CAkey ca.key \
  -CAcreateserial \
  -out johndoe.crt \
  -days 364
```
📦 What's next?
Once you have johndoe.crt, you can:
- Mount it in a pod as a Secret or Volume.
- Use it for authentication with the API server if you configure it as a user in kubeconfig.
- Integrate it in a webhook, ingress, or any component that requires TLS.

## Use the certificate in Kubernetes (KinD)

✅ Option A: Authenticate as a user in the cluster
You can create a new context in your kubeconfig to use this certificate:

```bash
kubectl config set-credentials johndoe \
  --client-certificate=johndoe.crt \
  --client-key=johndoe.key

kubectl config set-context johndoe-context \
  --cluster=kind-kind \
  --user=johndoe

kubectl config use-context johndoe-context
```

✅ Option B: Mount the certificate in a pod
You can create a Secret with the certificate and mount it in a pod:

```bash
kubectl create secret tls johndoe-tls \
  --cert=johndoe.crt \
  --key=johndoe.key
```
Then use it in a Deployment, Ingress, or Webhook.

## 📤 Copy files from the container to the host
1. 🔍 Identify the name of the control-plane container
It is usually called something like kind-control-plane. To confirm:

```bash
docker ps --filter name=kind-control-plane --format "{{.Names}}"
```
Suppose the name is kind-control-plane.

## Adapt these instructions for kind: openssl x509 -req -in johndoe.csr -CA /.minikube/ca.crt -CAkey \ /.minikube/ca.key -CAcreateserial -out johndoe.crt -days 364 Signature ok subject=/CN=johndoe/O=cka-study-guide Getting CA Private Key

Let's adapt that command for a typical Minikube and OpenSSL environment. The goal is to sign a certificate (johndoe.csr) using Minikube's CA.

🛠️ Adapted command version
```bash
openssl x509 -req -in johndoe.csr \
  -CA ~/.minikube/ca.crt \
  -CAkey ~/.minikube/ca.key \
  -CAcreateserial \
  -out johndoe.crt \
  -days 364
```
✅ Explanation of each part
-req -in johndoe.csr: Uses johndoe's certificate request (CSR).

-CA ~/.minikube/ca.crt: Path to Minikube's CA certificate.

-CAkey ~/.minikube/ca.key: Path to the CA private key.

-CAcreateserial: Creates a serial number for the certificate.

-out johndoe.crt: Output file with the signed certificate.

-days 364: The certificate will be valid for 364 days.

📁 Important notes
Make sure ca.crt and ca.key exist in ~/.minikube/.

Run the command from the directory where johndoe.csr is, or adjust the path.

If a ca.srl file already exists, you can omit -CAcreateserial.

Do you want help generating the CSR (johndoe.csr) as well?

## Using k8s in docker and need to adapt this
Perfect, if you are using Kubernetes in Docker (KinD) instead of Minikube, the approach changes a bit because KinD does not directly expose a CA like Minikube. But you can follow these steps to adapt the certificate signing process in KinD:

🔧 Option 1: Use KinD cluster CA
KinD creates its own internal CA to sign certificates, but it does not expose it easily. To sign your CSR (johndoe.csr) with KinD's CA, you need to:

1. 🔍 Extract the cluster CA
You can get the CA certificate and key from the control-plane container:

```bash
# Find the name of the control-plane container
docker ps --filter name=kind-control-plane

# Copy the CA files from the container
docker cp kind-control-plane:/etc/kubernetes/pki/ca.crt ./ca.crt
docker cp kind-control-plane:/etc/kubernetes/pki/ca.key ./ca.key
```
2. ✅ Adapt the OpenSSL command
Once you have ca.crt and ca.key on your local machine, you can sign the CSR like this:

```bash
openssl x509 -req -in johndoe.csr \
  -CA ca.crt \
  -CAkey ca.key \
  -CAcreateserial \
  -out johndoe.crt \
  -days 364
```
🧪 Option 2: Create your own CA (more secure and isolated)
If you don't want to touch KinD's internal CA, you can create your own CA and use it to sign certificates that you can then mount in your pods or use for authentication.

1. Create your own CA
```bash
openssl genrsa -out myCA.key 2048
openssl req -x509 -new -nodes -key myCA.key -subj "/CN=my-ca" -days 365 -out myCA.crt
```
2. Sign the CSR
```bash
openssl x509 -req -in johndoe.csr \
  -CA myCA.crt \
  -CAkey myCA.key \
  -CAcreateserial \
  -out johndoe.crt \
  -days 364
```
📦 What's next?
Once you have johndoe.crt, you can:

Mount it in a pod as a Secret or Volume.

Use it for authentication with the API server if you configure it as a user in kubeconfig.

Integrate it in a webhook, ingress, or any component that requires TLS.

Do you want help creating the CSR or configuring the certificate in Kubernetes?

## Copy johndoe.crt and johndoe.key from the control-plane container to the host

Since you have johndoe.crt and johndoe.key inside the KinD control-plane container, you can copy them to the host using the docker cp command.

📤 Copy files from the container to the host
1. 🔍 Identify the name of the control-plane container
It is usually called something like kind-control-plane. To confirm:

```bash
docker ps --filter name=kind-control-plane --format "{{.Names}}"
```
Suppose the name is kind-control-plane.

2. 📁 Copy the files to the host

```bash
# Copy johndoe.crt to the host
docker cp kind-control-plane:/path_inside_container/johndoe.crt ~/cka/cka_study_guide/rbac/johndoe.crt

# Copy johndoe.key to the host
docker cp kind-control-plane:/path_inside_container/johndoe.key ~/cka/cka_study_guide/rbac/johndoe.key
```

🔍 Important: Replace /path_inside_container/ with the actual path where the files are inside the container.

✅ Verify that they were copied correctly

```bash
ls ~/cka/cka_study_guide/rbac/
```
You should see johndoe.crt and johndoe.key ready to use.

Now that you have johndoe.crt and johndoe.key on your host, you can create a custom entry in your kubeconfig so that the johndoe user can authenticate with Kubernetes using certificates.

🛠️ Steps to add the johndoe user to kubeconfig
1. 📍 Define the certificate paths
Assuming the files are at:

```bash
~/cka/cka_study_guide/rbac/johndoe.crt
~/cka/cka_study_guide/rbac/johndoe.key
```

🧩 Add the user to kubeconfig
```bash
kubectl config set-credentials johndoe \
  --client-certificate=/home/eduardo/cka/cka_study_guide/rbac/johndoe.crt \
  --client-key=/home/eduardo/cka/cka_study_guide/rbac/johndoe.key
```

3. 🧠 Add a context for the user
First, get the current cluster name:

```bash
kubectl config get-clusters
```
Suppose the cluster is called kind-kind. Now create a context for johndoe:

```bash
kubectl config set-context johndoe-context \
  --cluster=kind-kind \
  --user=johndoe
```

4. 🔄 Switch to the new context
```bash
kubectl config use-context johndoe-context
```

5. ✅ Verify you are using the new user
```bash
kubectl config view --minify
```
You should see that the current user is johndoe.

You should see:

```yaml
client-certificate: /home/eduardo/cka/cka_study_guide/rbac/johndoe.crt
client-key: /home/eduardo/cka/cka_study_guide/rbac/johndoe.key
```

Let's make sure the johndoe user has access to the cluster.

🧩 Step 1: Create a ClusterRole
This defines what actions johndoe can perform. For example, if you want them to list pods in all namespaces:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list", "watch"]
```

Save it as pod-reader-role.yaml and apply it:

```bash
kubectl apply -f pod-reader-role.yaml
```

Step 2: Create a ClusterRoleBinding
This binds the johndoe user to the role you just created:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: johndoe-pod-reader-binding
subjects:
- kind: User
  name: johndoe
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```
Save it as johndoe-binding.yaml and apply it:

```bash
kubectl apply -f johndoe-binding.yaml
```

Step 3: Test access
Now use the johndoe context to test if you can list pods:

```bash
kubectl --context=johndoe-context get pods --all-namespaces
```

The server might respond:

```bash
Error from server (Unauthorized)
```
🛠️ Solution: Switch to the context with admin permissions
You need to use a context that has permissions to create roles and bindings. Normally it's the admin context or the one you used to install the cluster.

1. View available contexts:
```bash
kubectl config get-contexts

CURRENT   NAME              CLUSTER     AUTHINFO    NAMESPACE
          johndoe-context   kind-kind   johndoe     
*         kind-kind         kind-kind   kind-kind   
```

2. Switch to the context with permissions:

```bash
kubectl config use-context johndoe-context
```
(Replace with the correct name if different.)

3. Apply the files:
```bash
kubectl apply -f pod-reader-role.yaml
kubectl apply -f johndoe-binding.yaml
```

✅ Then test with the johndoe context
```bash
kubectl --context=johndoe-context get pods --all-namespaces
```

Imperative command to check the namespace your current context is using:

```bash
kubectl config view --minify --output 'jsonpath={..namespace}'
```

🧠 What does this command do?
--minify: filters only the current context information

--output 'jsonpath={..namespace}': extracts the namespace field from the context

Since Kubernetes v1.24 and later, tokens are no longer created automatically as before.

🛠️ Solution: Manually create a token for your ServiceAccount
Suppose your ServiceAccount is called cka-user and is in the cka-test namespace.

1. 🔐 Create a token secret linked to the ServiceAccount

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: cka-user-token
  annotations:
    kubernetes.io/service-account.name: cka-user
type: kubernetes.io/service-account-token
```
Save it as cka-user-token.yaml and apply it:

```bash
kubectl apply -f cka-user-token.yaml --namespace=cka-test
```

2. 🔍 Verify that the token is mounted

```bash
kubectl describe serviceaccount cka-user --namespace=cka-test
```
Now you should see:

Mountable secrets:   cka-user-token
Tokens:              cka-user-token

3. 🧪 Get the token for authentication (optional)
```bash
kubectl get secret cka-user-token --namespace=cka-test -o jsonpath='{.data.token}' | base64 -d
```
You can use this token to authenticate with the API server if needed.

Token does not appear

1. 📋 Verify that the ServiceAccount exists
```bash
kubectl get serviceaccount cka-user --namespace=cka-test
```
If it does not appear, create it:

```bash
kubectl create serviceaccount cka-user --namespace=cka-test
```

2. 🔐 Create the kubernetes.io/service-account-token type secret
This type of secret must have the correct annotation for Kubernetes to generate the token automatically.

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: cka-user-token
  namespace: cka-test
  annotations:
    kubernetes.io/service-account.name: cka-user
type: kubernetes.io/service-account-token
```
Save it as cka-user-token.yaml and apply it:

```bash
kubectl apply -f cka-user-token.yaml
```

3. ⏳ Wait a few seconds and verify that the token has been generated
```bash
kubectl get secret cka-user-token --namespace=cka-test -o yaml
```
Look for the .data.token field. If it is empty, wait a few seconds and run the command again. If after 30 seconds it still does not appear, the token controller may not be working correctly.

4. 🧪 Extract the token
```bash
kubectl get secret cka-user-token --namespace=cka-test -o jsonpath='{.data.token}' | base64 -d
```

🛠️ Create a kubeconfig for the ServiceAccount
Suppose:

Namespace: cka-test

ServiceAccount: cka-user

Token: you got it with kubectl get secret ...

Cluster name: kind-kind

API server: you can get it with:

```bash
kubectl config view --minify -o jsonpath='{.clusters[0].cluster.server}'
```

✏️ Create the cka-user.kubeconfig file

```bash
# Set the port
PORT=40527  
# Extract the token
TOKEN=$(kubectl get secret cka-user-token --namespace=cka-test -o jsonpath='{.data.token}' | base64 -d)

# Extract the CA
kubectl get secret cka-user-token --namespace=cka-test -o jsonpath='{.data.ca\.crt}' | base64 -d > ca.crt

# Create the kubeconfig
kubectl config set-cluster kind-kind \
  --server=https://127.0.0.1:$PORT \
  --certificate-authority=ca.crt \
  --kubeconfig=cka-user.kubeconfig \
  --embed-certs=true

kubectl config set-credentials cka-user \
  --token="$TOKEN" \
  --kubeconfig=cka-user.kubeconfig

kubectl config set-context cka-user-context \
  --cluster=kind-kind \
  --user=cka-user \
  --namespace=cka-test \
  --kubeconfig=cka-user.kubeconfig

kubectl config use-context cka-user-context --kubeconfig=cka-user.kubeconfig
```

✅ Test access
```bash
kubectl --kubeconfig=cka-user.kubeconfig get pods
```
If everything is fine, you will see the pods in the cka-test namespace.

```bash
Error from server (Forbidden): pods is forbidden: User "system:serviceaccount:cka-test:cka-user" cannot list resource "pods" in API group "" in the namespace "cka-test"
```

🔐 What's happening?
Your ServiceAccount (cka-user) is trying to run get pods, but is not authorized. In Kubernetes, permissions are managed through Roles and RoleBindings (or ClusterRoles and ClusterRoleBindings if global).

✅ Solution: Create a Role and a RoleBinding
1. 🧾 Create a Role that allows listing pods

```yaml
# role.yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: cka-test
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list", "watch"]
```

2. 🔗 Associate the Role to the ServiceAccount with a RoleBinding

```yaml
# rolebinding.yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods-binding
  namespace: cka-test
subjects:
- kind: ServiceAccount
  name: cka-user
  namespace: cka-test
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

3. 🚀 Apply both resources

```bash
kubectl apply -f role.yaml
kubectl apply -f rolebinding.yaml
```

🔁 Test again
```bash
kubectl --kubeconfig=cka-user.kubeconfig get pods
```
Now you should see the pods in the cka-test namespace without problems.

Expand the permissions of the ServiceAccount cka-user so it can create, list, update, and delete resources like pods, deployments, services, etc., within the cka-test namespace.

🛠️ Step 1: Create a Role with extended permissions

```yaml
# extended-role.yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: namespace-admin
  namespace: cka-test
rules:
- apiGroups: [""]
  resources: ["pods", "services", "configmaps", "secrets"]
  verbs: ["get", "list", "watch", "create", "update", "delete"]
- apiGroups: ["apps"]
  resources: ["deployments", "replicasets", "statefulsets"]
  verbs: ["get", "list", "watch", "create", "update", "delete"]
```

🔗 Step 2: Create the corresponding RoleBinding

```yaml
# extended-rolebinding.yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: namespace-admin-binding
  namespace: cka-test
subjects:
- kind: ServiceAccount
  name: cka-user
  namespace: cka-test
roleRef:
  kind: Role
  name: namespace-admin
  apiGroup: rbac.authorization.k8s.io
```

🚀 Step 3: Apply the resources
```bash
kubectl apply -f extended-role.yaml
kubectl apply -f extended-rolebinding.yaml
```

✅ Verify the new permissions
Test some actions with the cka-user kubeconfig:

```bash
kubectl --kubeconfig=cka-user.kubeconfig create deployment nginx --image=nginx
kubectl --kubeconfig=cka-user.kubeconfig get deployments
kubectl --kubeconfig=cka-user.kubeconfig delete deployment nginx
```

All this should work within the cka-test namespace.

## Assign a serviceAccount to a pod

In the latest version of Kubernetes, you can use the imperative kubectl run command to create a Pod and assign it a custom ServiceAccount. Although the --serviceaccount flag was removed in recent versions, you can achieve this using the --overrides parameter.

🧪 Imperative command with --overrides
```bash
kubectl run my-pod \
  --image=nginx \
  --overrides='{
    "apiVersion": "v1",
    "spec": {
      "serviceAccountName": "my-service-account"
    }
  }'
```

What does this command do?
--image=nginx: Uses the Nginx image.

--overrides: Directly inserts a portion of the manifest in JSON format to modify the Pod spec.

"serviceAccountName": "my-service-account": Assigns the desired ServiceAccount.

Make sure the ServiceAccount my-service-account already exists in the namespace where you are running the command. You can check with:

```bash
kubectl get serviceaccount my-service-account
```

#### Convert to Script

Let's turn that command into a reusable script that:
Checks if the ServiceAccount exists.
Creates it if it does not exist.
Runs the Pod with that ServiceAccount.
Allows you to customize image, Pod name, and namespace.

🧰 Reusable Bash Script

```bash
#!/bin/bash

# Customizable parameters
POD_NAME="my-pod"
IMAGE="nginx"
SERVICE_ACCOUNT="my-service-account"
NAMESPACE="default"

# 1. Check if the ServiceAccount exists
echo "🔍 Checking ServiceAccount '$SERVICE_ACCOUNT' in namespace '$NAMESPACE'..."
if ! kubectl get serviceaccount "$SERVICE_ACCOUNT" -n "$NAMESPACE" &>/dev/null; then
  echo "⚠️  ServiceAccount not found. Creating..."
  kubectl create serviceaccount "$SERVICE_ACCOUNT" -n "$NAMESPACE"
else
  echo "✅ ServiceAccount already exists."
fi

# 2. Run the Pod with the ServiceAccount
echo "🚀 Creating Pod '$POD_NAME' with image '$IMAGE' and ServiceAccount '$SERVICE_ACCOUNT'..."
kubectl run "$POD_NAME" \
  --image="$IMAGE" \
  --namespace="$NAMESPACE" \
  --overrides="$(cat <<EOF
{
  "apiVersion": "v1",
  "spec": {
    "serviceAccountName": "$SERVICE_ACCOUNT"
  }
}
EOF
)"

echo "✅ Pod created successfully."
```

🧩 How to use it
Save the script as create_pod.sh.

Give it execution permissions:

```bash
chmod +x create_pod.sh
```

Run it:

```bash
./create_pod.sh
```

#### kubeconfig file 
It is essential to interact with a Kubernetes cluster, as it contains access information such as users, contexts, and credentials. Here is how to check it and add users.

How to check the kubeconfig file?
By default, the file is located at:

```bash
~/.kube/config
```

You can view it with:

```bash
cat ~/.kube/config
```

Or for a clearer view:

```bash
kubectl config view
```

If you have multiple kubeconfig files, you can combine them or specify one with:

```bash
KUBECONFIG=/path_to_config kubectl config view
```

How to add users to the kubeconfig file?

You can do it manually by editing the file or using kubectl config commands. Here are both ways:

🔧 Option 1: Edit manually
Add a users section like this:

```yaml
users:
- name: example-user
  user:
    client-certificate: /path/to/cert.crt
    client-key: /path/to/key.key
```

Or if you use token authentication:

```yaml
users:
- name: token-user
  user:
    token: your-token-here
```

Then you can create a context that uses that user:

```yaml
contexts:
- name: example-user-context
  context:
    cluster: your-cluster
    user: example-user
```

And select it with:

```bash
kubectl config use-context example-user-context
```

⚙️ Option 2: Use kubectl config commands
Example to add a user with a token:

```bash
kubectl config set-credentials token-user --token=your-token-here
kubectl config set-context token-context --cluster=your-cluster --user=token-user
kubectl config use-context token
```