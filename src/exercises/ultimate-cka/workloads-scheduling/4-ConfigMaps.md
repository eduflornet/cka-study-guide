# ConfigMaps

## Reference

* https://kubernetes.io/docs/concepts/configuration/configmap/
* https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/
* https://cloud.google.com/kubernetes-engine/docs/concepts/configmap
* https://matthewpalmer.net/kubernetes-app-developer/articles/ultimate-configmap-guide-kubernetes.html

In this demo:

We will create the ConfigMap using both Imperatively and Declaratively
Next, we will use above ConfigMap into Pod, as environment variables, arguments, and files inside vol.

NOTE: Create ConfigMap before you use it inside Pod

1. Creating Configmap Declaratively (Using YAML file):

Example-1:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: env-config-yaml
data:
  ENV_ONE: "va1ue1" 
  ENV_TWO: "va1ue2"
```

Example-2:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-nginx-config-yaml
data:
  my-nginx-config.conf: |-
    server {
      listen 80;
      server_name www.kubia-example.com;
      gzip on;
      gzip_types text/plain application/xml;
      location / {
        root /usr/share/nginx/html;
        index index.html index.htm;
      }
    }
    sleep-interval: 25
```

2. Creating ConfigMap Imperatively (from Command line):
kubectl create configmap <NAME> <SOURCE>

From Literal value:

```bash
kubectl create configmap env-config-cmd --from-literal=ENV_ONE=value1 --from-literal=ENV_TWO=value2
```

3. Displaying ConfigMap:

kubectl get configmap <NAME>
kubectl get configmap <NAME> -o wide
kubectl get configmap <NAME> -o yaml
kubectl get configmap <NAME> -o json

kubectl describe configmap <NAME>

4. Editing ConfigMap:

kubectl edit configmap <NAME>

5. Injecting ConfigMap into Pod As Environment Variables (1/3):

```yaml
# cm-pod-env.yaml
apiVersion: v1
kind: Pod
metadata:
  name: cm-pod-env
spec:
  containers:
    - name: test-container
      image: nginx
      env:
      - name: ENV_VARIABLE_1
        valueFrom:
          configMapKeyRef:
            name: env-config-cmd
            key: ENV_ONE
      - name: ENV_VARIABLE_2
        valueFrom:
          configMapKeyRef:
            name: env-config-cmd
            key: ENV_TWO
  restartPolicy: Never
```

Deploy:

```bash
kubectl apply -f cm-pod-env.yaml
```

Validate:

```bash
kubectl exec cm-pod-env -- env | grep ENV

ENV_VARIABLE_1=value1
ENV_VARIABLE_2=value2

```
6. Injecting ConfigMap into Pod As Arguments(2/2):

```yaml
# cm-pod-arg.yaml
apiVersion: v1
kind: Pod
metadata:
  name: cm-pod-arg
spec:
  containers:
    - name: test-container
      image: busybox
      command: [ "/bin/sh", "-c", "echo $(ENV_VARIABLE_1) and $(ENV_VARIABLE_2)" ]
      env:
      - name: ENV_VARIABLE_1
        valueFrom:
          configMapKeyRef:
            name: env-config-cmd
            key: ENV_ONE
      - name: ENV_VARIABLE_2
        valueFrom:
          configMapKeyRef:
            name: env-config-cmd
            key: ENV_TWO
  restartPolicy: Never
```

Deploy:

```bash
kubectl apply -f cm-pod-arg.yaml
```

Validate:

```bash
kubectl logs cm-pod-arg

value1 and value2
```

7. Injecting ConfigMap into As Files inside Volume(3/3):

a. Create the necessary ConfigMap
First, make sure you have the configuration file you want to set up. For example, if you have a file called nginx.conf, you can create the ConfigMap like this:

Create nginx.conf

```bash
events {}
http {
  server {
    listen 80;
    location / {
      return 200 'Hello from ConfigMap!';
    }
  }
}
```

b. Create configmap my-nginx-config-yaml

``` bash
kubectl create configmap my-nginx-config-yaml --from-file=nginx.conf
```

```yaml
# cm-pod-file-vol.yaml
apiVersion: v1
kind: Pod
metadata:
  name: cm-pod-file-vol
spec:
  volumes:
    - name: mapvol
      configMap:
        name: my-nginx-config-yaml
  containers:
    - name: test-container
      image: nginx
      volumeMounts:
      - name: mapvol
        mountPath: /etc/config
  restartPolicy: Never
```

This will create the Pod that mounts the ConfigMap file in /etc/config inside the container.

```bash
kubectl apply -f cm-pod-file-vol.yaml
```

Verify that the Pod is running

```bash
kubectl get pods
----------------
kubectl describe pod cm-pod-file-vol 
Name:             cm-pod-file-vol
Namespace:        default
Priority:         0
Service Account:  default
Node:             kind-control-plane/172.19.0.2
Start Time:       Mon, 18 Aug 2025 09:34:35 +0200
Labels:           <none>
Annotations:      <none>
Status:           Running
IP:               10.244.0.10
IPs:
  IP:  10.244.0.10
Containers:
  test-container:
    Container ID:   containerd://00ec681079ed1a4549d2f456f763a2b1bc7884d2688752aee08b7a3b2befeb6b
    Image:          nginx
    Image ID:       docker.io/library/nginx@sha256:33e0bbc7ca9ecf108140af6288c7c9d1ecc77548cbfd3952fd8466a75edefe57
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Mon, 18 Aug 2025 09:34:37 +0200
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /etc/config from mapvol (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-zjws4 (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       True 
  ContainersReady             True 
  PodScheduled                True 
Volumes:
  mapvol:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      my-nginx-config-yaml
  ...
```

Validate:
And to see if the file was mounted correctly:

```bash
kubectl exec -it cm-pod-file-vol -- ls /etc/config
nginx.conf
```

```bash
kubectl exec cm-pod-file-vol -- cat /etc/config/nginx.conf
```

8. Running operations directly on the YAML file:

kubectl [OPERATION] –f [FILE-NAME.yaml]

kubectl get confi -f [FILE-NAME.yaml]
kubectl create -f [FILE-NAME.yaml]
kubectl delete –f [FILE-NAME.yaml]

9. Delete ConfigMap:

kubectl delete configmap <NAME>

```bash
kubectl delete configmap my-nginx-config-yaml
```