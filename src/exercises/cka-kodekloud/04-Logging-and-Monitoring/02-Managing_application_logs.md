# Managing Application Logs

You don’t understand anything until you learn it more than one way.

– Marvin Minsky

1. We have deployed a POD hosting an application. Inspect it. Wait for it to start.

``` kubectl get pods webapp-1 -o yaml ```

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: "2025-08-22T10:07:46Z"
  generation: 1
  labels:
    name: webapp-1
  name: webapp-1
  namespace: default
  resourceVersion: "796"
  uid: 2775d564-0286-4d44-88fc-406a4307bb47
spec:
  containers:
  - image: kodekloud/event-simulator
    imagePullPolicy: Always
    name: simple-webapp
    ports:
    - containerPort: 8080
      protocol: TCP
    resources: {}
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-h82p7
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
  - name: kube-api-access-h82p7
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
    lastTransitionTime: "2025-08-22T10:07:50Z"
    status: "True"
    type: PodReadyToStartContainers
  - lastProbeTime: null
    lastTransitionTime: "2025-08-22T10:07:46Z"
    status: "True"
    type: Initialized
  - lastProbeTime: null
    lastTransitionTime: "2025-08-22T10:07:50Z"
    status: "True"
    type: Ready
  - lastProbeTime: null
    lastTransitionTime: "2025-08-22T10:07:50Z"
    status: "True"
    type: ContainersReady
  - lastProbeTime: null
    lastTransitionTime: "2025-08-22T10:07:46Z"
    status: "True"
    type: PodScheduled
  containerStatuses:
  - containerID: containerd://70fe7d174736c4d6e1d4406a9b1d574d16e9a4b5689bce4bf025396554f75a5b
    image: docker.io/kodekloud/event-simulator:latest
    imageID: docker.io/kodekloud/event-simulator@sha256:1e3e9c72136bbc76c96dd98f29c04f298c3ae241c7d44e2bf70bcc209b030bf9
    lastState: {}
    name: simple-webapp
    ready: true
    resources: {}
```

2. A user - USER5 - has expressed concerns accessing the application. Identify the cause of the issue.
Inspect the logs of the POD

**USER5 Failed to Login as the account is locked due to MANY FAILED**

```
[2025-08-22 10:07:50,207] INFO in event-simulator: USER1 logged out
[2025-08-22 10:07:51,207] INFO in event-simulator: USER2 is viewing page2
[2025-08-22 10:07:52,208] INFO in event-simulator: USER1 is viewing page2
[2025-08-22 10:07:53,210] INFO in event-simulator: USER1 is viewing page2
[2025-08-22 10:07:54,211] INFO in event-simulator: USER4 is viewing page2
[2025-08-22 10:07:55,213] WARNING in event-simulator: USER5 Failed to Login as the account is locked due to MANY FAILED ATTEMPTS.
[2025-08-22 10:07:55,213] INFO in event-simulator: USER4 logged in
[2025-08-22 10:07:56,214] INFO in event-simulator: USER4 is viewing page2
[2025-08-22 10:07:57,215] INFO in event-simulator: USER2 is viewing page3
[2025-08-22 10:07:58,217] WARNING in event-simulator: USER7 Order failed as the item is OUT OF STOCK.
[2025-08-22 10:07:58,217] INFO in event-simulator: USER3 logged in
[2025-08-22 10:07:59,227] INFO in event-simulator: USER1 logged out
[2025-08-22 10:08:00,228] WARNING in event-simulator: USER5 Failed to Login as the account is locked due to MANY FAILED ATTEMPTS.
```
3. We have deployed a new POD - webapp-2 - hosting an application. Inspect it. Wait for it to start.

4. A user is reporting issues while trying to purchase an item. Identify the user and the cause of the issue.

Inspect the logs of the web application running in the webapp-2 pod.

**USER30 Order failed as the item is OUT OF STOCK**

```
efaulted container "simple-webapp" out of: simple-webapp, db
[2025-08-22 10:15:14,561] INFO in event-simulator: USER3 logged in
[2025-08-22 10:15:15,563] INFO in event-simulator: USER3 is viewing page2
[2025-08-22 10:15:16,564] INFO in event-simulator: USER1 is viewing page1
[2025-08-22 10:15:17,565] INFO in event-simulator: USER3 is viewing page1
[2025-08-22 10:15:18,565] INFO in event-simulator: USER2 is viewing page1
[2025-08-22 10:15:19,566] WARNING in event-simulator: USER5 Failed to Login as the account is locked due to MANY FAILED ATTEMPTS.
[2025-08-22 10:15:19,567] INFO in event-simulator: USER2 is viewing page3
[2025-08-22 10:15:20,568] INFO in event-simulator: USER1 is viewing page1
[2025-08-22 10:15:21,569] INFO in event-simulator: USER1 logged out
[2025-08-22 10:15:22,570] WARNING in event-simulator: USER30 Order failed as the item is OUT OF STOCK.
[2025-08-22 10:15:22,571] INFO in event-simulator: USER2 is viewing page3
[2025-08-22 10:15:23,571] INFO in event-simulator: USER1 logged in
[2025-08-22 10:15:24,573] WARNING in event-simulator: USER5 Failed to Login as the account is locked due to MANY FAILED ATTEMPTS.
[2025-08-22 10:15:24,573] INFO in event-simulator: USER3 logged out
[2025-08-22 10:15:25,574] INFO in event-simulator: USER1 is viewing page3
[2025-08-22 10:15:26,575] INFO in event-simulator: USER4 logged out
[2025-08-22 10:15:27,576] INFO in event-simulator: USER4 is viewing page2
[2025-08-22 10:15:28,577] INFO in event-simulator: USER1 logged out
[2025-08-22 10:15:29,579] WARNING in event-simulator: USER5 Failed to Login as the account is locked due to MANY FAILED ATTEMPTS.
[2025-08-22 10:15:29,579] INFO in event-simulator: USER2 logged out
[2025-08-22 10:15:30,579] WARNING in event-simulator: USER30 Order failed as the item is OUT OF STOCK.
```

