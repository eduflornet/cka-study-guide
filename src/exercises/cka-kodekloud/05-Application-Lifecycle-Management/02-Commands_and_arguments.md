# Commands and Arguments


There is only one success - to be able to spend your life in your own way.

– Christopher Morley


1. How many PODs exist on the system?

In the current(default) namespace
```bash
k get pods -n default
NAME             READY   STATUS    RESTARTS   AGE
ubuntu-sleeper   1/1     Running   0          5m15s
```

2. What is the command used to run the pod ubuntu-sleeper?

``` k get pod ubuntu-sleeper -o yaml ```

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: "2025-08-22T18:53:40Z"
  generation: 1
  name: ubuntu-sleeper
  namespace: default
  resourceVersion: "878"
  uid: 9b015c62-36bc-40db-982b-e798dba9c302
spec:
  containers:
  - command:
    - sleep
    - "4800"
    image: ubuntu
    imagePullPolicy: Always
    name: ubuntu
    resources: {}
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-w8cr9
      readOnly: true
  dnsPolicy: ClusterFirst
  enableServiceLinks: true
  nodeName: controlplane
  preemptionPolicy: PreemptLowerPriority
  priority: 0
  restartPolicy: Always
  schedulerName: default-scheduler
  securityContext: {}
  serviceAccount: default
  serviceAccountName: default
  terminationGracePeriodSeconds: 30
  tolerations:
  - effect: NoExecute
    key: node.kubernetes.io/not-ready
    operator: Exists
    tolerationSeconds: 300
  - effect: NoExecute
    key: node.kubernetes.io/unreachable
    operator: Exists
    tolerationSeconds: 300
  volumes:
  - name: kube-api-access-w8cr9
    projected:
      defaultMode: 420
      sources:
      - serviceAccountToken:
          expirationSeconds: 3607
          path: token
      - configMap:
          items:
          - key: ca.crt
            path: ca.crt
          name: kube-root-ca.crt
      - downwardAPI:
          items:
          - fieldRef:
              apiVersion: v1
              fieldPath: metadata.namespace
            path: namespace
status:
  conditions:
  - lastProbeTime: null
    lastTransitionTime: "2025-08-22T18:53:42Z"
    status: "True"
    type: PodReadyToStartContainers
  - lastProbeTime: null
    lastTransitionTime: "2025-08-22T18:53:40Z"
    status: "True"
    type: Initialized
  - lastProbeTime: null
    lastTransitionTime: "2025-08-22T18:53:42Z"
    status: "True"
    type: Ready
  - lastProbeTime: null
    lastTransitionTime: "2025-08-22T18:53:42Z"
    status: "True"
    type: ContainersReady
  - lastProbeTime: null
    lastTransitionTime: "2025-08-22T18:53:40Z"
    status: "True"
    type: PodScheduled
  containerStatuses:
  - containerID: containerd://b83c1860a4e837fb06f63eeb86b621849b12bfe2e290dc1239db4af01722030b
    image: docker.io/library/ubuntu:latest
    imageID: docker.io/library/ubuntu@sha256:7c06e91f61fa88c08cc74f7e1b7c69ae24910d745357e0dfe1d2c0322aaf20f9
    lastState: {}
    name: ubuntu
    ready: true
    resources: {}
    restartCount: 0
    started: true
    state:
      running:
        startedAt: "2025-08-22T18:53:42Z"
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-w8cr9
      readOnly: true
      recursiveReadOnly: Disabled
  hostIP: 192.168.81.56
  hostIPs:
  - ip: 192.168.81.56
  phase: Running
  podIP: 10.22.0.9
  podIPs:
  - ip: 10.22.0.9
  qosClass: BestEffort
  startTime: "2025-08-22T18:53:40Z"
  ```

  3. Create a pod with the ubuntu image to run a container to sleep for 5000 seconds. Modify the file ubuntu-sleeper-2.yaml.
Note: Only make the necessary changes. Do not modify the name.

```bash
k run ubuntu-sleeper-2 --image ubuntu --command sleep 5000
pod/ubuntu-sleeper-2 created
```

4. Create a pod using the file named ubuntu-sleeper-3.yaml. There is something wrong with it. Try to fix it!
Note: Only make the necessary changes. Do not modify the name.

```yaml
# ubuntu-sleeper-3.yaml
apiVersion: v1
kind: Pod
metadata:
  name: ubuntu-sleeper-3
spec:
  containers:
  - name: ubuntu
    image: ubuntu
    command:
      - sleep
      - "1200"
```

5. Update pod ubuntu-sleeper-3 to sleep for 2000 seconds.

Note: Only make the necessary changes. Do not modify the name of the pod. Delete and recreate the pod if necessary.

```bash
k delete pod ubuntu-sleeper-3
nano  ubuntu-sleeper-3.yaml
k apply -f ubuntu-sleeper-3.yaml 
```

6. Inspect the file Dockerfile given at /root/webapp-color directory. What command is run at container startup?

**python app.py**]

```dockerfile
# Dockerfile
FROM python:3.6-alpine

RUN pip install flask

COPY . /opt/

EXPOSE 8080

WORKDIR /opt

ENTRYPOINT ["python", "app.py"]
```

7. Inspect the file Dockerfile2 given at /root/webapp-color directory. What command is run at container startup?

**python app.py --color red** ]

```dockerfile
FROM python:3.6-alpine

RUN pip install flask

COPY . /opt/

EXPOSE 8080

WORKDIR /opt

ENTRYPOINT ["python", "app.py"]

CMD ["--color", "red"]
```

8. Inspect the two files under directory webapp-color-2. What command is run at container startup?
Assume the image was created from the Dockerfile in this directory.

**--color green**

```dockerfile
# Dockerfile
FROM python:3.6-alpine

RUN pip install flask

COPY . /opt/

EXPOSE 8080

WORKDIR /opt

ENTRYPOINT ["python", "app.py"]

CMD ["--color", "red"]
```

```yaml
# webapp-color-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: webapp-green
  labels:
      name: webapp-green
spec:
  containers:
  - name: simple-webapp
    image: kodekloud/webapp-color
    command: ["--color","green"]
```

9. Inspect the two files under directory webapp-color-3. What command is run at container startup?

Assume the image was created from the Dockerfile in this directory.

```
command: ["python", "app.py"]
args: ["--color", "pink"]
```

```dockerfile
FROM python:3.6-alpine

RUN pip install flask

COPY . /opt/

EXPOSE 8080

WORKDIR /opt

ENTRYPOINT ["python", "app.py"]

CMD ["--color", "red"]
```

```yaml
piVersion: v1
kind: Pod
metadata:
  name: webapp-green
  labels:
      name: webapp-green
spec:
  containers:
  - name: simple-webapp
    image: kodekloud/webapp-color
    command: ["python", "app.py"]
    args: ["--color", "pink"]
```

10. Create a pod with the given specifications. By default it displays a blue background. Set the given command line arguments to change it to green.

- Pod Name: webapp-green

- Image: kodekloud/webapp-color

- Command line arguments: --color=green


```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: webapp-green
  name: webapp-green
spec:
  containers:
  - image: kodekloud/webapp-color
    name: webapp-green
    args: ["--color", "green"]
  restartPolicy: Never 
```

