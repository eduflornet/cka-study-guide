# Multi Container Pods

If you can't explain it simply you don't understand it well enough.

– Albert Einstein

A winner is a dreamer who never gives up.

– Nelson Mandela

1. Identify the number of containers created in the red pod.

**3** containers busybox

```bash
k get pod red -o yaml | grep -i image
    image: busybox
    imagePullPolicy: Always
    image: busybox
    imagePullPolicy: Always
    image: busybox
    imagePullPolicy: Always
    image: docker.io/library/busybox:latest
    imageID: docker.io/library/busybox@sha256:ab33eacc8251e3807b85bb6dba570e4698c3998eca6f0fc2ccb60575a563ea74
    image: docker.io/library/busybox:latest
    imageID: docker.io/library/busybox@sha256:ab33eacc8251e3807b85bb6dba570e4698c3998eca6f0fc2ccb60575a563ea74
    image: docker.io/library/busybox:latest
    imageID: docker.io/library/busybox@sha256:ab33eacc8251e3807b85bb6dba570e4698c3998eca6f0fc2ccb60575a563ea74

```

2. dentify the name of the containers running in the blue pod.

**teal & navy**

```bash
k describe pod blue

vents:
  Type    Reason     Age    From               Message
  ----    ------     ----   ----               -------
  Normal  Scheduled  3m56s  default-scheduler  Successfully assigned default/blue to controlplane
  Normal  Pulling    3m55s  kubelet            Pulling image "busybox"
  Normal  Pulled     3m55s  kubelet            Successfully pulled image "busybox" in 161ms (161ms including waiting). Image size: 2223685 bytes.
  Normal  Created    3m55s  kubelet            Created container: teal
  Normal  Started    3m55s  kubelet            Started container teal
  Normal  Pulling    3m55s  kubelet            Pulling image "busybox"
  Normal  Pulled     3m55s  kubelet            Successfully pulled image "busybox" in 142ms (142ms including waiting). Image size: 2223685 bytes.
  Normal  Created    3m55s  kubelet            Created container: navy
  Normal  Started    3m55s  kubelet            Started container navy
```

3. Create a multi-container pod named yellow that includes 2 containers as specified below:

- Container 1
    - Name: lemon
    - Image: busybox

- Container 2
    - Name: gold
    - Image: redis

If the pod encounters a crashloopbackoff status, modify the lemon container to include the command sleep 1000.

- Is the pod named yellow created?

- Is Container 1 named lemon?

- Does Container 1 utilize the busybox image?

- Is Container 2 named gold?

- Does Container 2 utilize the redis image?

- Container 1 lemon -> busybox, 
- Container 2 gold -> redis,

4. We have deployed an application logging stack in the elastic-stack namespace. Inspect it.

Before proceeding with the next set of questions, please wait for all the pods in the elastic-stack namespace to be ready. This can take a few minutes.


```bash
k get all -n elastic-stack 
NAME                 READY   STATUS    RESTARTS   AGE
pod/app              1/1     Running   0          9m25s
pod/elastic-search   1/1     Running   0          9m25s
pod/kibana           1/1     Running   0          9m24s

NAME                    TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)                         AGE
service/elasticsearch   NodePort   172.20.76.131   <none>        9200:30200/TCP,9300:30300/TCP   9m24s
service/kibana          NodePort   172.20.123.22   <none>        5601:30601/TCP                  9m24s
```

5. Once the pod is in a ready state, inspect the Kibana UI using the link above your terminal. There shouldn't be any logs for now.

We will configure a sidecar container for the application to send logs to Elastic Search.

NOTE: It can take a couple of minutes for the Kibana UI to be ready after the Kibana pod is ready.

You can inspect the Kibana logs by running:

```bash 
kubectl -n elastic-stack logs kibana 
```

6. Inspect the app pod and identify the number of containers in it.

It is deployed in the elastic-stack namespace.

**1** app

```bash
k get pod -n elastic-stack app -o yaml
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: "2025-08-23T12:58:42Z"
  generation: 1
  labels:
    name: app
  name: app
  namespace: elastic-stack
  resourceVersion: "1141"
  uid: 0f5a1386-1985-4671-bcac-498cf2f5e33a
spec:
  containers:
  - image: kodekloud/event-simulator
    imagePullPolicy: Always
    name: app
    resources: {}
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /log
      name: log-volume
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-crgsh
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
  - hostPath:
      path: /var/log/webapp
      type: DirectoryOrCreate
    name: log-volume
  - name: kube-api-access-crgsh
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
    lastTransitionTime: "2025-08-23T12:58:47Z"
    status: "True"
    type: PodReadyToStartContainers
  - lastProbeTime: null
    lastTransitionTime: "2025-08-23T12:58:42Z"
    status: "True"
    type: Initialized
  - lastProbeTime: null
    lastTransitionTime: "2025-08-23T12:58:47Z"
    status: "True"
    type: Ready
  - lastProbeTime: null
    lastTransitionTime: "2025-08-23T12:58:47Z"
    status: "True"
    type: ContainersReady
  - lastProbeTime: null
    lastTransitionTime: "2025-08-23T12:58:42Z"
    status: "True"
    type: PodScheduled
  containerStatuses:
  - containerID: containerd://bd1d1e22d97cf0bf34352729093d5daf8e750acd285e6fe9b2d8b3db09af1da5
    image: docker.io/kodekloud/event-simulator:latest
    imageID: docker.io/kodekloud/event-simulator@sha256:1e3e9c72136bbc76c96dd98f29c04f298c3ae241c7d44e2bf70bcc209b030bf9
    lastState: {}
    name: app
    ready: true
    resources: {}
    restartCount: 0
    started: true
    state:
      running:
        startedAt: "2025-08-23T12:58:46Z"
    volumeMounts:
    - mountPath: /log
      name: log-volume
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-crgsh
      readOnly: true
      recursiveReadOnly: Disabled
  hostIP: 192.168.56.147
  hostIPs:
  - ip: 192.168.56.147
  phase: Running
  podIP: 172.17.0.4
  podIPs:
  - ip: 172.17.0.4
  qosClass: BestEffort
  startTime: "2025-08-23T12:58:42Z"
  ```

7. The application outputs logs to the file /log/app.log. View the logs and try to identify the user having issues with Login.

Inspect the log file inside the pod.

**Hint** Exec in to the container and inspect logs stored in /log/app.log.
**Run** the command: ``` kubectl -n elastic-stack exec -it app -- cat /log/app.log ```

**USER5**

```
[2025-08-23 13:19:25,361] INFO in event-simulator: USER4 logged in
[2025-08-23 13:19:26,357] INFO in event-simulator: USER4 logged out
[2025-08-23 13:19:26,361] INFO in event-simulator: USER4 is viewing page3
[2025-08-23 13:19:27,358] INFO in event-simulator: USER1 is viewing page1
[2025-08-23 13:19:27,362] INFO in event-simulator: USER2 logged out
[2025-08-23 13:19:28,359] WARNING in event-simulator: USER5 Failed to Login as the account is locked due to MANY FAILED ATTEMPTS.
[2025-08-23 13:19:28,359] WARNING in event-simulator: USER7 Order failed as the item is OUT OF STOCK.
...
```

8. Edit the app pod in the elastic-stack namespace to add a sidecar container named sidecar to send logs to Elastic Search. Mount the log volume to the sidecar container at path /var/log/event-simulator/.

Only update the pod to include the new container. Do not modify anything else. You might need to delete and re-create the app pod. Refer to the diagram below for your configuration.

Note: State persistence concepts are discussed in detail later in this course. For now, please make use of the following documentation link for updating the concerning pod: https://kubernetes.io/docs/concepts/workloads/pods/sidecar-containers/


Does the app pod exist?

Is the sidecar container created?

Is the image kodekloud/filebeat-configured configured for the sidecar container?

Is the volumeMount properly configured for the sidecar?

Is log-volume mounted at path /var/log/event-simulator/ in the sidecar container?

Is the sidecar container properly configured to stay running?


**Solution**

Utilize the manifest below to re-create the app pod with the updated configuration:

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    name: app
  name: app
  namespace: elastic-stack
spec:
  initContainers:
  - name: sidecar
    image: kodekloud/filebeat-configured
    restartPolicy: Always
    volumeMounts:
      - name: log-volume
        mountPath: /var/log/event-simulator
  containers:
  - image: kodekloud/event-simulator
    name: app
    resources: {}
    volumeMounts:
    - mountPath: /log
      name: log-volume

  volumes:
  - hostPath:
      path: /var/log/webapp
      type: DirectoryOrCreate
    name: log-volume
```

10. Inspect the Kibana UI. You should now see logs appearing in the Discover section.

You might have to wait for a couple of minutes for the logs to populate. You might have to create an index pattern to list the logs. If not sure check this video: https://bit.ly/2EXYdHf

https://30601-port-v75k7b7dlisu3drx.labs.kodekloud.com/app/kibana#/discover?_g=()&_a=(columns:!(_source),index:'filebeat-*',interval:auto,query:(language:lucene,query:''),sort:!('@timestamp',desc))








