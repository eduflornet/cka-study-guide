# Validating and Mutating Admission Controllers

Study hard what interests you the most in the most undisciplined, irreverent and original manner possible.

– Richard P. Feynman

1. Which of the below combination is correct for Mutating and validating admission controllers ?

2. What is the flow of invocation of admission controllers?

**first mutate and then validate**

3. Create namespace webhook-demo where we will deploy webhook components

```bash
kubectl create namespace webhook-demo
```

4. Create a TLS secret named webhook-server-tls in the webhook-demo namespace.

This secret will be used by the admission webhook server for secure communication over HTTPS.

We have already created below cert and key for webhook server which should be used to create secret.

- Certificate : /root/keys/webhook-server-tls.crt
- Key : /root/keys/webhook-server-tls.key

**Hint**
Use the Kubernetes command to create a TLS-type secret with the provided certificate and key files.
The --cert flag specifies the certificate file and the --key flag specifies the private key file.

**Solution**

Run below command:
```bash
kubectl -n webhook-demo create secret tls webhook-server-tls \
    --cert "/root/keys/webhook-server-tls.crt" \
    --key "/root/keys/webhook-server-tls.key"
```

My command:
```bash
kubectl -n webhook-demo create secret tls webhook-server-tls --cert /root/keys/webhook-server-tls.crt --key /root/keys/webhook-server-tls.key

secret/webhook-server-tls created
```

5. Create the webhook deployment that will run the admission webhook server.

    We have already provided the deployment manifest at:
    ``` /root/webhook-deployment.yaml ```
    Create the deployement using this definition.

**Solution**
```bash
kubectl create -f /root/webhook-deployment.yaml
```

```yaml
# webhook-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webhook-server
  namespace: webhook-demo
  labels:
    app: webhook-server
spec:
  replicas: 1
  selector:
    matchLabels:
      app: webhook-server
  template:
    metadata:
      labels:
        app: webhook-server
    spec:
      securityContext:
        runAsNonRoot: true
        runAsUser: 1234
      containers:
      - name: server
        image: stackrox/admission-controller-webhook-demo:latest
        imagePullPolicy: Always
        ports:
        - containerPort: 8443
          name: webhook-api
        volumeMounts:
        - name: webhook-tls-certs
          mountPath: /run/secrets/tls
          readOnly: true
      volumes:
      - name: webhook-tls-certs
        secret:
          secretName: webhook-server-tls
```

6. Create a service that exposes the webhook server so that the admission controller can communicate with it.
    We have already provided the service manifest at:
    ``` /root/webhook-service.yaml ```
    Create the service using this definition.

**Solution**

Run command:

 ``` kubectl create -f /root/webhook-service.yaml ```

```yaml
# /webhook-service.yaml
 apiVersion: v1
kind: Service
metadata:
  name: webhook-server
  namespace: webhook-demo
spec:
  selector:
    app: webhook-server
  ports:
    - port: 443
      targetPort: webhook-api
```

9. We have added the **MutatingWebhookConfiguration** under /root/webhook-configuration.yaml. Upon applying this configuration, which resources and actions will it impact?

Pod create operations.

**Hint**
Inspect the operations and resources sections under rules in the file located at ``` /root/webhook-configuration.yaml ```.


```yaml
# webhook-configuration.yaml  
apiVersion: admissionregistration.k8s.io/v1
kind: MutatingWebhookConfiguration
metadata:
  name: demo-webhook
webhooks:
  - name: webhook-server.webhook-demo.svc
    clientConfig:
      service:
        name: webhook-server
        namespace: webhook-demo
        path: "/mutate"
      caBundle: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURQekNDQWllZ0F3SUJBZ0lVTUp6Q21mK0p3clpQOFJOYlZpMWdHcXVjVVF3d0RRWUpLb1pJaHZ>
    rules:
      - operations: [ "CREATE" ]
        apiGroups: [""]
        apiVersions: ["v1"]
        resources: ["pods"]
    admissionReviewVersions: ["v1beta1"]
    sideEffects: None
```

10. Now lets deploy **MutatingWebhookConfiguration** in /root/webhook-configuration.yaml

Run command:

 ``` kubectl create -f /root/webhook-configuration.yaml ```

11. In the previous steps, you have set up and deployed a demo webhook with the following behaviors:

Denies all requests for pods to run as root in a container if no securityContext is provided.
Defaults: If runAsNonRoot is not set, the webhook automatically adds runAsNonRoot: true and sets the user ID to 1234.
Explicit root access: The webhook allows containers to run as root only if you explicitly set runAsNonRoot: false in the pod's securityContext.
In the next steps, you will find pod definition files for each scenario. Please deploy these pods using the provided definition files and validate the behavior of our webhook.

12. Deploy a pod that does not explicitly define a securityContext.
This will help verify that the webhook applies default values.
We have already provided the manifest:
/root/pod-with-defaults.yaml

**Solution**

Run command: ``` kubectl apply -f /root/pod-with-defaults.yaml ```

```yaml
# pod-with-defaults.yaml 
# A pod with no securityContext specified.
# Without the webhook, it would run as user root (0). The webhook mutates it
# to run as the non-root user with uid 1234.
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-defaults
  labels:
    app: pod-with-defaults
spec:
  restartPolicy: OnFailure
  containers:
    - name: busybox
      image: busybox
      command: ["sh", "-c", "echo I am running as user $(id -u)"]
```


13. Check the securityContext of the pod created in the previous step (pod-with-defaults).
Even though we did not specify any values in the pod definition, the mutation webhook should have injected default values.

Use kubectl get pod -o yaml to inspect the pod spec and look for the securityContext fields.

**Solution**
Execute the following command to retrieve the security context settings for the specified pod:

```bash
kubectl get pod pod-with-defaults -o yaml | grep -A2 "securityContext:"
securityContext:
    runAsNonRoot: true
    runAsUser: 1234
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"v1","kind":"Pod","metadata":{"annotations":{},"labels":{"app":"pod-with-defaults"},"name":"pod-with-defaults","namespace":"default"},"spec":{"containers":[{"command":["sh","-c","echo I am running as user $(id -u)"],"image":"busybox","name":"busybox"}],"restartPolicy":"OnFailure"}}
  creationTimestamp: "2025-08-22T09:10:57Z"
  generation: 1
  labels:
    app: pod-with-defaults
  name: pod-with-defaults
  namespace: default
  resourceVersion: "4314"
  uid: 742e9da0-6cbe-413a-a02c-2c56a93bfaef
spec:
  containers:
  - command:
    - sh
    - -c
    - echo I am running as user $(id -u)
    image: busybox
    imagePullPolicy: Always
    name: busybox
    resources: {}
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-6c222
      readOnly: true
  dnsPolicy: ClusterFirst
  enableServiceLinks: true
  nodeName: controlplane
  preemptionPolicy: PreemptLowerPriority
  priority: 0
  restartPolicy: OnFailure
  schedulerName: default-scheduler
  securityContext:
    runAsNonRoot: true
    runAsUser: 1234
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
  - name: kube-api-access-6c222
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
    lastTransitionTime: "2025-08-22T09:11:00Z"
    status: "False"
    type: PodReadyToStartContainers
  - lastProbeTime: null
    lastTransitionTime: "2025-08-22T09:10:57Z"
    reason: PodCompleted
    status: "True"
    type: Initialized
  - lastProbeTime: null
    lastTransitionTime: "2025-08-22T09:10:57Z"
    reason: PodCompleted
    status: "False"
    type: Ready
  - lastProbeTime: null
    lastTransitionTime: "2025-08-22T09:10:57Z"
    reason: PodCompleted
    status: "False"
    type: ContainersReady
  - lastProbeTime: null
    lastTransitionTime: "2025-08-22T09:10:57Z"
    status: "True"
    type: PodScheduled
  containerStatuses:
  - containerID: containerd://9c56b32d3dbab1a04d07305138b4444e3d1eb977e752f771cca9b0edeef8ea22
    image: docker.io/library/busybox:latest
    imageID: docker.io/library/busybox@sha256:ab33eacc8251e3807b85bb6dba570e4698c3998eca6f0fc2ccb60575a563ea74
    lastState: {}
    name: busybox
    ready: false
    resources: {}
    restartCount: 0
    started: false
    state:
      terminated:
        containerID: containerd://9c56b32d3dbab1a04d07305138b4444e3d1eb977e752f771cca9b0edeef8ea22
        exitCode: 0
        finishedAt: "2025-08-22T09:10:58Z"
        reason: Completed
        startedAt: "2025-08-22T09:10:58Z"
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-6c222
      readOnly: true
      recursiveReadOnly: Disabled
  hostIP: 192.168.60.137
  hostIPs:
  - ip: 192.168.60.137
  phase: Succeeded
  podIP: 172.17.0.6
  podIPs:
  - ip: 172.17.0.6
  qosClass: BestEffort
  startTime: "2025-08-22T09:10:57Z"
```


14. Deploy pod with a securityContext explicitly allowing it to run as root

We have added pod definition file under

``` /root/pod-with-override.yaml ```

Validate securityContext after you deploy this pod

**Solution**

Run command:

``` kubectl apply -f /root/pod-with-override.yaml ```

then validate securityContext using the following command:

``` kubectl get po pod-with-override -o yaml | grep -A2 " securityContext:" ```

```yaml
# A pod with a securityContext explicitly allowing it to run as root.
# The effect of deploying this with and without the webhook is the same. The
# explicit setting however prevents the webhook from applying more secure
# defaults.
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-override
  labels:
    app: pod-with-override
spec:
  restartPolicy: OnFailure
  securityContext:
    runAsNonRoot: false
  containers:
    - name: busybox
      image: busybox
      command: ["sh", "-c", "echo I am running as user $(id -u)"]

    kubectl get pod pod-with-override -o yaml | grep -A2 "securityContext"
      {"apiVersion":"v1","kind":"Pod","metadata":{"annotations":{},"labels":{"app":"pod-with-override"},"name":"pod-with-override","namespace":"default"},"spec":{"containers":[{"command":["sh","-c","echo I am running as user $(id -u)"],"image":"busybox","name":"busybox"}],"restartPolicy":"OnFailure","securityContext":{"runAsNonRoot":false}}}
  creationTimestamp: "2025-08-22T09:19:40Z"
  generation: 1
--
  securityContext:
    runAsNonRoot: false
  serviceAccount: default
```

12. Deploy a pod that specifies a conflicting securityContext.

The pod requests to run with runAsUser: 0 (root).
But it does not explicitly set runAsNonRoot: false.

According to our webhook rules, this request should be denied.

We have already provided the manifest at:

``` /root/pod-with-conflict.yaml ```


**Solution**

Run command:

``` kubectl apply -f /root/pod-with-conflict.yaml ```

Expected Outcome:
The admission webhook should reject this pod. You will see an error similar to:

Error from server: error when creating "/root/pod-with-conflict.yaml": admission webhook "webhook-server.webhook-demo.svc" denied the request: runAsNonRoot specified, but runAsUser set to 0 (the root user)

```yaml
# pod-with-conflict.yaml
# A pod with a conflicting securityContext setting: it has to run as a non-root
# user, but we explicitly request a user id of 0 (root).
# Without the webhook, the pod could be created, but would be unable to launch
# due to an unenforceable security context leading to it being stuck in a
# 'CreateContainerConfigError' status. With the webhook, the creation of
# the pod is outright rejected.
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-conflict
  labels:
    app: pod-with-conflict
spec:
  restartPolicy: OnFailure
  securityContext:
    runAsNonRoot: true
    runAsUser: 0
  containers:
    - name: busybox
      image: busybox
      command: ["sh", "-c", "echo I am running as user $(id -u)"]
```