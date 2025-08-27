# Image Security

Sometimes things aren’t clear right away. That’s where you need to be patient and persevere and see where things lead.

– Mary Pierce

Arise, awake, and stop not till the goal is reached.

– Swami Vivekananda

1. What secret type must we choose for docker registry?

**docker-registry type**

```bash
k create secret --help

Create a secret with specified type.

 A docker-registry type secret is for accessing a container registry.

 A generic type secret indicate an Opaque secret type.

 A tls type secret holds TLS certificate and its associated key.

Available Commands:
  docker-registry   Create a secret for use with a Docker registry
  generic           Create a secret from a local file, directory, or literal value
  tls               Create a TLS secret

Usage:
  kubectl create secret (docker-registry | generic | tls) [options]

Use "kubectl create secret <command> --help" for more information about a given command.
Use "kubectl options" for a list of global command-line options (applies to all commands).
```

2. We have an application running on our cluster. Let us explore it first. What image is the application using?

**nginx:alpine**

```bash
k describe pod web-5bd64f6ccb-lb6cv | grep -i image
    Image:          nginx:alpine
    Image ID:       docker.io/library/nginx@sha256:42a516af16b852e33b7682d5ef8acbd5d13fe08fecadc7ed98605ba5e3b26ab8
  Normal  Pulling    25m   kubelet            Pulling image "nginx:alpine"
  Normal  Pulled     25m   kubelet            Successfully pulled image "nginx:alpine" in 203ms (1.925s including waiting). Image size: 22477192 bytes.
```

3. We decided to use a modified version of the application from an internal private registry. Update the image of the deployment to use a new image from myprivateregistry.com:5000

The registry is located at myprivateregistry.com:5000. Don't worry about the credentials for now. We will configure them in the upcoming steps.

```yaml
spec:
      containers:
      - image: myprivateregistry.com:5000/nginx:alpine
        imagePullPolicy: IfNotPresent
        name: nginx
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
```

```bash
root@controlplane ~ ➜  k delete deploy web --force

Warning: Immediate deletion does not wait for confirmation that the running resource has been terminated. The resource may continue to run on the cluster indefinitely.
deployment.apps "web" force deleted

root@controlplane ~ ➜  k apply -f web-deploy.yaml 

deployment.apps/web created

root@controlplane ~ ➜  k get deploy

NAME   READY   UP-TO-DATE   AVAILABLE   AGE
web    0/2     2            0           11s

root@controlplane ~ ➜  k get pods

NAME                   READY   STATUS             RESTARTS   AGE
web-7968dfbf7f-h2hdp   0/1     ImagePullBackOff   0          22s
web-7968dfbf7f-vdccs   0/1     ImagePullBackOff   0          22s
```

4. Se han actualizado las imagenes en los pods?, funcionana correctamente?

**No**, because ImagePullBackOff.


```bash
k get pods web-7968dfbf7f-h2hdp | grep -i image
web-7968dfbf7f-h2hdp   0/1     ImagePullBackOff   0          2m13s
```
5. Create a secret object with the credentials required to access the registry.

Name: private-reg-cred
Username: dock_user
Password: dock_password
Server: myprivateregistry.com:5000
Email: dock_user@myprivateregistry.com

Run the command: 

```bash
kubectl create secret docker-registry private-reg-cred --docker-username=dock_user --docker-password=dock_password --docker-server=myprivateregistry.com:5000 --docker-email=dock_user@myprivateregistry.com
```

6. Configure the deployment to use credentials from the new secret to pull images from the private registry

Edit deployment using kubectl edit deploy web command and add imagePullSecrets section. Use private-reg-cred.

```bash
k delete deploy web --force
k apply web-deploy.yaml
```

```yaml
# web-deploy.yaml
spec:
      containers:
      - image: myprivateregistry.com:5000/nginx:alpine
        imagePullPolicy: IfNotPresent
        name: nginx
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      imagePullSecrets:
      - name: private-reg-cred
      dnsPolicy: ClusterFirst
```

7. Check the status of PODs. Wait for them to be running. You have now successfully configured a Deployment to pull images from the private registry.

```bash
k get pods web-67c9b9bf8-zp7zd
NAME                  READY   STATUS    RESTARTS   AGE
web-67c9b9bf8-zp7zd   1/1     Running   0          3m13s
```