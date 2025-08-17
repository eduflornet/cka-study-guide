## Node Affinity and Node Selectors

Keep your face always toward the sunshine, and shadows will fall behind you.
– Walt Whitman

1. How many Labels exist on node node01?
Run the command ``` kubectl describe node node01 ``` and count the number of labels.

```bash
kubectl describe nodes node01
Name:               node01
Roles:              <none>
Labels:             beta.kubernetes.io/arch=amd64
                    beta.kubernetes.io/os=linux
                    kubernetes.io/arch=amd64
                    kubernetes.io/hostname=node01
                    kubernetes.io/os=linux
Annotations:        flannel.alpha.coreos.com/backend-data: {"VNI":1,"VtepMAC":"7e:bb:78:08:cf:1a"}
                    flannel.alpha.coreos.com/backend-type: vxlan
                    flannel.alpha.coreos.com/kube-subnet-manager: true
                    flannel.alpha.coreos.com/public-ip: 192.168.145.152
                    kubeadm.alpha.kubernetes.io/cri-socket: unix:///var/run/containerd/containerd.sock
                    node.alpha.kubernetes.io/ttl: 0
                    volumes.kubernetes.io/controller-managed-attach-detach: true
```

2. What is the value set to the label key ``` beta.kubernetes.io/arch ``` on node01?
amd64

3. Apply a label color=blue to node node01

```bash
kubectl label nodes node01 color=blue
```

4. Create a new deployment named blue with the nginx image and 3 replicas.

```bash
kubectl create deployment blue --image nginx --replicas 3
```

5. Which nodes can the pods for the blue deployment be placed on?
¿En qué nodos se pueden colocar los pods del deployment blue?
Make sure to check taints on both nodes (if any) !

Solution:
Check if controlplane and node01 have any taints on them that will prevent the pods to be scheduled on them. If there are no taints, the pods can be scheduled on either node.

So run the following command to check the taints on both nodes.

```bash

kubectl describe node controlplane | grep -i taints

kubectl describe node node01 | grep -i taints

```

node01 y controlplane

6. Set Node Affinity to the blue deployment to place the pods on node01 only.

Ensure that node01 has the label color=blue.

Requirements:
- Use requiredDuringSchedulingIgnoredDuringExecution node affinity
- Key: color
- Value: blue

If the label is not already set, apply it to node01 before updating the deployment.

Hint:
Use ``` kubectl edit deployment blue ``` to modify the deployment.
Add the affinity section under spec.template.spec.
Make sure node01 has the label color=blue (check with ``` kubectl get nodes --show-labels ```).
If pods remain pending, verify that node01 is Ready and has the correct label.

Opción: Editar el deployment sin usar kubectl edit
Si prefieres evitar el editor por completo, puedes hacerlo en dos pasos:

6.1. Exporta el YAML del deployment:

```bash
kubectl get deployment <nombre-del-deployment> -o yaml > deployment.yaml
kubectl get deployment blue -o yaml > blue-deployment.yaml
```

6.2. Edita el archivo con nano:

```bash
nano blue-deployment.yaml
```
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: blue
  labels:
    app: blue
spec:
  replicas: 3
  revisionHistoryLimit: 10
  progressDeadlineSeconds: 600
  selector:
    matchLabels:
      app: blue
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
  template:
    metadata:
      labels:
        app: blue
    spec:
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
              - matchExpressions:
                  - key: color
                    operator: In
                    values:
                      - blue
      containers:
        - name: nginx
          image: nginx
          imagePullPolicy: Always
          resources: {}
          terminationMessagePath: /dev/termination-log
          terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
status:
  availableReplicas: 3
  conditions:
  - lastTransitionTime: "2025-08-14T08:40:48Z"
    lastUpdateTime: "2025-08-14T08:40:48Z"
    message: Deployment has minimum availability.
    reason: MinimumReplicasAvailable
    status: "True"
    type: Available
  - lastTransitionTime: "2025-08-14T08:40:40Z"
    lastUpdateTime: "2025-08-14T08:40:48Z"
    message: ReplicaSet "blue-d7967dc55" has successfully progressed.
    reason: NewReplicaSetAvailable
    status: "True"
    type: Progressing
  observedGeneration: 1
  readyReplicas: 3
  replicas: 3
  updatedReplicas: 3
```

Haz los cambios que necesites, guarda y luego aplica el nuevo archivo:

```bash
kubectl apply -f blue-deployment.yaml
```

7. Create a new deployment named red with the nginx image and 2 replicas, and ensure it gets placed on the controlplane node only.


Use the label key - ``` node-role.kubernetes.io/control-plane ``` - which is already set on the controlplane node.

```bash
kubectl create deployment red --image nginx --replicas 2
kubectl get deploy red -o yaml > red-deployment.yaml 
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: "1"
  creationTimestamp: "2025-08-14T09:04:30Z"
  generation: 1
  labels:
    app: red
  name: red
  namespace: default
  resourceVersion: "3709"
  uid: 82567420-53b5-45cf-baa0-2f930208fbff
spec:
  progressDeadlineSeconds: 600
  replicas: 2
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      app: red
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: red
    spec:
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
              - matchExpressions:
                  - key: node-role.kubernetes.io/control-plane
                    operator: Exists
      containers:
      - image: nginx
        imagePullPolicy: Always
        name: nginx
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
status:
  availableReplicas: 2
  conditions:
  - lastTransitionTime: "2025-08-14T09:04:36Z"
    lastUpdateTime: "2025-08-14T09:04:36Z"
    message: Deployment has minimum availability.
    reason: MinimumReplicasAvailable
    status: "True"
    type: Available
  - lastTransitionTime: "2025-08-14T09:04:30Z"
    lastUpdateTime: "2025-08-14T09:04:36Z"
    message: ReplicaSet "red-5784d99ff8" has successfully progressed.
    reason: NewReplicaSetAvailable
    status: "True"
    type: Progressing
  observedGeneration: 1
```

```bash
kubectl apply -f red-deployment.yaml
```
