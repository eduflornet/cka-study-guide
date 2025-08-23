# Init Containers

Question everything. Learn something. Answer nothing.

– Euripides

Success doesn’t come to you, you go to it.

– Marva Collins

1. Identify the pod that has an initContainer configured.

**Hint** Run the command ``` kubectl describe pod ``` and check which pod has InitContainers.

```bash
k get pod blue -o yaml | grep -i initContainer
  initContainers:
  initContainerStatuses:
```
**Solution**
Pod blue has one initContainer called **init-myservice**, which you can confirm by running ``` kubectl describe pod blue ``` and looking for the initContainers section.

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: "2025-08-23T20:08:39Z"
  generation: 1
  name: blue
  namespace: default
  resourceVersion: "972"
  uid: 7b8a3705-1d8e-41b9-b18d-7a9579bd39a0
spec:
  containers:
  - command:
    - sh
    - -c
    - echo The app is running! && sleep 3600
    image: busybox:1.28
    imagePullPolicy: IfNotPresent
    name: green-container-1
    resources: {}
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-nblbs
      readOnly: true
  dnsPolicy: ClusterFirst
  enableServiceLinks: true
  initContainers:
  - command:
    - sh
    - -c
    - sleep 5
    image: busybox
    imagePullPolicy: Always
    name: init-myservice
    resources: {}
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-nblbs
      readOnly: true
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
  - name: kube-api-access-nblbs
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
    lastTransitionTime: "2025-08-23T20:08:40Z"
    status: "True"
    type: PodReadyToStartContainers
  - lastProbeTime: null
    lastTransitionTime: "2025-08-23T20:08:46Z"
    status: "True"
    type: Initialized
  - lastProbeTime: null
    lastTransitionTime: "2025-08-23T20:08:47Z"
    status: "True"
    type: Ready
  - lastProbeTime: null
    lastTransitionTime: "2025-08-23T20:08:47Z"
    status: "True"
    type: ContainersReady
  - lastProbeTime: null
    lastTransitionTime: "2025-08-23T20:08:39Z"
    status: "True"
    type: PodScheduled
    containerStatuses:
  - containerID: containerd://63bee45226befa2fdfcf48d0d2af9db8ac33958754790abb95d473b0b3c3daad
    image: docker.io/library/busybox:1.28
    imageID: docker.io/library/busybox@sha256:141c253bc4c3fd0a201d32dc1f493bcf3fff003b6df416dea4f41046e0f37d47
    lastState: {}
    name: green-container-1
    ready: true
    resources: {}
    restartCount: 0
    started: true
    state:
      running:
        startedAt: "2025-08-23T20:08:46Z"
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-nblbs
      readOnly: true
      recursiveReadOnly: Disabled
    hostIP: 192.168.163.32
    hostIPs:
  - ip: 192.168.163.32
  initContainerStatuses:
  - containerID: containerd://bb2f3c5c482ef7677d8daa632d84588335f34d059d77fe362069cf915b89a280
    image: docker.io/library/busybox:latest
    imageID: docker.io/library/busybox@sha256:ab33eacc8251e3807b85bb6dba570e4698c3998eca6f0fc2ccb60575a563ea74
    lastState: {}
    name: init-myservice
    ready: true
    resources: {}
    restartCount: 0
    started: false
    state:
      terminated:
        containerID: containerd://bb2f3c5c482ef7677d8daa632d84588335f34d059d77fe362069cf915b89a280
        exitCode: 0
        finishedAt: "2025-08-23T20:08:45Z"
        reason: Completed
        startedAt: "2025-08-23T20:08:40Z"
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
     name: kube-api-access-nblbs
      readOnly: true
      recursiveReadOnly: Disabled
  phase: Running
  podIP: 10.22.0.11
  podIPs:
  - ip: 10.22.0.11
  qosClass: BestEffort
  startTime: "2025-08-23T20:08:39Z"
```

2. What is the image used by the initContainer on the blue pod?
**busybox**

3. What is the state of the initContainer on pod blue?

**Terminated**

```bash
Name:             blue
Namespace:        default
Priority:         0
Service Account:  default
Node:             controlplane/192.168.163.32
Start Time:       Sat, 23 Aug 2025 20:08:39 +0000
Labels:           <none>
Annotations:      <none>
Status:           Running
IP:               10.22.0.11
IPs:
  IP:  10.22.0.11
Init Containers:
  init-myservice:
    Container ID:  containerd://bb2f3c5c482ef7677d8daa632d84588335f34d059d77fe362069cf915b89a280
    Image:         busybox
    Image ID:      docker.io/library/busybox@sha256:ab33eacc8251e3807b85bb6dba570e4698c3998eca6f0fc2ccb60575a563ea74
    Port:          <none>
    Host Port:     <none>
    Command:
      sh
      -c
      sleep 5
    State:          Terminated
      Reason:       Completed
      Exit Code:    0
      Started:      Sat, 23 Aug 2025 20:08:40 +0000
      Finished:     Sat, 23 Aug 2025 20:08:45 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-nblbs (ro)
Containers:
  green-container-1:
    Container ID:  containerd://63bee45226befa2fdfcf48d0d2af9db8ac33958754790abb95d473b0b3c3daad
    Image:         busybox:1.28
    Image ID:      docker.io/library/busybox@sha256:141c253bc4c3fd0a201d32dc1f493bcf3fff003b6df416dea4f41046e0f37d47
    Port:          <none>
    Host Port:     <none>
    Command:
      sh
      -c
      echo The app is running! && sleep 3600
    State:          Running
      Started:      Sat, 23 Aug 2025 20:08:46 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-nblbs (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       True 
  ContainersReady             True 
  PodScheduled                True 
Volumes:
  kube-api-access-nblbs:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    Optional:                false
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  19m   default-scheduler  Successfully assigned default/blue to controlplane
  Normal  Pulling    19m   kubelet            Pulling image "busybox"
  Normal  Pulled     19m   kubelet            Successfully pulled image "busybox" in 363ms (363ms including waiting). Image size: 2223685 bytes.
  Normal  Created    19m   kubelet            Created container: init-myservice
  Normal  Started    19m   kubelet            Started container init-myservice
  Normal  Pulled     19m   kubelet            Container image "busybox:1.28" already present on machine
  Normal  Created    19m   kubelet            Created container: green-container-1
  Normal  Started    19m   kubelet            Started container green-container-1
```
4. Why is the initContainer terminated? What is the reason?
**Completed**

5. We just created a new pod named purple. How many initContainers does it have?

**2**

```bash
k describe pod purple
Name:             purple
Namespace:        default
Priority:         0
Service Account:  default
Node:             controlplane/192.168.163.32
Start Time:       Sat, 23 Aug 2025 20:38:06 +0000
Labels:           <none>
Annotations:      <none>
Status:           Pending
IP:               10.22.0.12
IPs:
  IP:  10.22.0.12
Init Containers:
  warm-up-1:
    Container ID:  containerd://6e196aeaf994e7d288fa4e6abbf055cbf3ab8433341390612a08d0c75030a426
    Image:         busybox:1.28
    Image ID:      docker.io/library/busybox@sha256:141c253bc4c3fd0a201d32dc1f493bcf3fff003b6df416dea4f41046e0f37d47
    Port:          <none>
    Host Port:     <none>
    Command:
      sh
      -c
      sleep 600
    State:          Running
      Started:      Sat, 23 Aug 2025 20:38:07 +0000
    Ready:          False
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-zg64m (ro)
  warm-up-2:
    Container ID:  
    Image:         busybox:1.28
    Image ID:      
    Port:          <none>
    Host Port:     <none>
    Command:
      sh
      -c
      sleep 1200
    State:          Waiting
      Reason:       PodInitializing
    Ready:          False
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-zg64m (ro)
Containers:
  purple-container:
    Container ID:  
    Image:         busybox:1.28
    Image ID:      
    Port:          <none>
    Host Port:     <none>
    Command:
      sh
      -c
      echo The app is running! && sleep 3600
    State:          Waiting
      Reason:       PodInitializing
    Ready:          False
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-zg64m (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 False 
  Ready                       False 
  ContainersReady             False 
  PodScheduled                True 
Volumes:
  kube-api-access-zg64m:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    Optional:                false
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  33s   default-scheduler  Successfully assigned default/purple to controlplane
  Normal  Pulled     32s   kubelet            Container image "busybox:1.28" already present on machine
  Normal  Created    32s   kubelet            Created container: warm-up-1
  Normal  Started    32s   kubelet            Started container warm-up-1
```

6. What is the status of the purple POD?

**Pending**

```bash
k get pod purple
NAME     READY   STATUS     RESTARTS   AGE
purple   0/1     Init:0/2   0          3m21s
```

7. How long after the creation of the purple POD will the application come up and be available to users?

**Hint**
Check the commands used in the initContainers. The first one sleeps for 600 seconds (10 minutes) and the second one sleeps for 1200 seconds (20 minutes)

**Solution**
Adding the sleep times for both initContainers, the application will start after 1800 seconds or 30 minutes.

8. Update the pod red to use an initContainer that uses the busybox image and sleeps for 20 seconds

Delete and re-create the pod if necessary. But make sure no other configurations change.

Is the red pod running?

initContainer uses the busybox image ?

initContainer runs the command sleep 20

```bash
k delete pod red --force
```

```yaml
piVersion: v1
kind: Pod
metadata:
  creationTimestamp: "2025-08-23T20:08:39Z"
  generation: 1
  name: red
  namespace: default
  resourceVersion: "965"
  uid: 58451c20-e5d3-49e2-ba7c-ec6ceec2cb46
spec:
  containers:
  - command:
    - sh
    - -c
    - echo The app is running! && sleep 3600
    image: busybox:1.28
    imagePullPolicy: IfNotPresent
    name: red-container
    initContainers:
     - image: busybox
       name: red-initcontainer
       command:
        - "sleep"
        - "20"
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-kj9qw
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
```

9. A new application orange is deployed. There is something wrong with it. Identify and fix the issue.

Once fixed, wait for the application to run before checking solution.

**Hint**
Check the command used by the initContainer and correct it.

**Solution**

There is a typo in the initContainer's command of the orange pod that prevents it from running correctly.

Follow these steps to fix the issue:

Export the pod's YAML definition to a file:

```bash
kubectl get pod orange -o yaml > orange.yaml
```
Open the ``` orange.yaml ``` file in a text editor.

Locate the initContainers section and find the command field. Correct the typo:

```bash
command:
- sh
- -c
- sleep 2
```

(Change sleeeep to sleep.)

Delete the existing faulty pod:

```bash
kubectl delete pod orange
```

Recreate the pod using the corrected YAML file:

```bash
kubectl create -f orange.yaml
```

Wait for the pod to be in the Running state before checking your solution

```yaml
spec:
  containers:
  - command:
    - sh
    - -c
    - sleep 2
    image: busybox:1.28
    imagePullPolicy: IfNotPresent
    name: orange-container
    resources: {}
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-fqnz6
      readOnly: true
  dnsPolicy: ClusterFirst
  enableServiceLinks: true
  initContainers:
  - command:
    - sh
    - -c
    - echo The app is running! && sleep 3600
    image: busybox
    imagePullPolicy: Always
    name: init-myservice
    resources: {}
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
```
