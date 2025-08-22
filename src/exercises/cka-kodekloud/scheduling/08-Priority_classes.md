# Priority classes

The things always happens that you really believe in; and the belief in a thing makes it happen.

– Frank Lloyd Wright


In this lab, we will learn about PriorityClasses in Kubernetes.

1. Which of the following PriorityClasses are part of a default Kubernetes setup?

system-cluster-critical

Hint: To list all the existing PriorityClasses, run the following command:

```bash
kubectl get priorityClass
NAME                      VALUE        GLOBAL-DEFAULT   AGE    PREEMPTIONPOLICY
system-cluster-critical   2000000000   false            7m2s   PreemptLowerPriority
system-node-critical      2000001000   false            7m2s   PreemptLowerPriority
```
2. What is the priority value assigned to system-node-critical?
2000001000

3. What is the value of preemptionPolicy in system-node-critical?

PreemptLowerPriority

```bash
kubectl get  priorityClass system-node-critical -o yaml
apiVersion: scheduling.k8s.io/v1
description: Used for system critical pods that must not be moved from their current
  node.
kind: PriorityClass
metadata:
  creationTimestamp: "2025-08-20T10:17:03Z"
  generation: 1
  name: system-node-critical
  resourceVersion: "72"
  uid: 26313789-36e6-4852-9627-5bceeae73fbe
preemptionPolicy: PreemptLowerPriority
value: 2000001000
```
4. Create a PriorityClass named high-priority, a value of 100000, and a preemption policy of PreemptLowerPriority. Do not set this class as a global default.

Hint:
kubectl get priorityclass high-priority

Solution:

Option one: Create a PriorityClass with imperative form:

```bash
kubectl create priorityclass high-priority --value=100000 \
--description="This priority class is used for high-priority pods" \
--preemption-policy=PreemptLowerPriority

```

Option two: To create the class high-priority, create a new yaml file:

vi high-priority.yaml
Add the contents of the yaml file:

```yaml
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata:
  name: high-priority
value: 100000
globalDefault: false
description: "This priority class is used for high-priority pods."
preemptionPolicy: PreemptLowerPriority
```

Save the file using :wq. To create the resource:

```bash
kubectl apply -f high-priority.yaml
```
You should be able to see the new resource if you listed the available priority classes using:

```bash
kubectl get priorityClasses
NAME                      VALUE        GLOBAL-DEFAULT   AGE   PREEMPTIONPOLICY
high-priority             100000       false            63s   PreemptLowerPriority
system-cluster-critical   2000000000   false            26m   PreemptLowerPriority
system-node-critical      2000001000   false            26m   PreemptLowerPriority
```

5. Create another PriorityClass named low-priority with a value of 1000. Do not set this class as a global default.


```bash
kubectl create priorityclass low-priority --value=1000 \
--description="This priority class is used for low-priority pods" \
--preemption-policy=PreemptLowerPriority
```

Option two: To create the class low-priority, create a new yaml file:

```bash
vi low-priority.yaml
```
Add the contents of the yaml file:

```yaml
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata:
  name: low-priority
value: 1000
globalDefault: false
description: "This priority class is used for low-priority pods."
```
Save the file using :wq. To create the resource:

```bash
kubectl apply -f low-priority.yaml
```
You should be able to see the new resource if you listed the available priority classes using:

```bash
kubectl get priorityclasses
```

6. In the default namespace, create a pod named low-prio-pod that runs an nginx image and uses the low-priority PriorityClass.

Review: https://kubernetes.io/docs/concepts/scheduling-eviction/pod-priority-preemption/

```bash
kubectl run low-prio-pod --image nginx --restart Never --dry-run=client -o yaml > low-prio-pod.yaml
```

```yaml
# low-prio-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: low-prio-pod
  name: low-prio-pod
spec:
  containers:
  - image: nginx
    name: low-prio-pod
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
  priorityClassName: low-priority
```

To create the resource:

```bash
kubectl apply -f low-prio-pod.yaml
```

7. Now, create another pod in the default namespace named high-prio-pod that runs an nginx image and uses the high-priority PriorityClass.

```bash
kubectl run high-prio-pod --image nginx --restart Never --dry-run=client -o yaml > high-prio-pod.yaml
```

```yaml
# high-prio-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: high-prio-pod
  name: high-prio-pod
spec:
  containers:
  - image: nginx
    name: high-prio-pod
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
  priorityClassName: high-priority
```
To create the resource:

```bash
kubectl apply -f high-prio-pod.yaml
```

8. You can compare the priority classes on both pods using the following command:

```bash
kubectl get pods -o custom-columns="NAME:.metadata.name,PRIORITY:.spec.priorityClassName"
NAME            PRIORITY
high-prio-pod   high-priority
low-prio-pod    low-priority
```

The following assets have been provisioned on the environment:

A pod named low-app
A pod named critical-app
View the pod status using the following command:

```bash
kubectl get pods
NAME            READY   STATUS    RESTARTS   AGE
critical-app    0/1     Pending   0          46s
high-prio-pod   1/1     Running   0          3m6s
low-app         1/1     Running   0          46s
low-prio-pod    1/1     Running   0          8m39s
```

Solution:

To resolve this situation and get the critical-app to a running state, you should:

Assign the high-priority class to the critical-app
Delete and recreate the pod with the new priority
Make sure the pod is in running state after applying the actions.

o update the manifest file of the critical-app pod, save a copy of the manifest file:

```bash
kubectl get pod critical-app -o yaml > critical-app.yaml
```

Edit the saved copy as follows (Output truncated below):

```yaml
# critical-app.yaml
apiVersion: v1
kind: Pod
metadata:
  ...
  name: critical-app
  ...
spec:
  containers:
  - image: nginx
    imagePullPolicy: Always
    name: critical-container
    ...
  dnsPolicy: ClusterFirst
  priorityClassName: high-priority   # Add the high-priority class
  enableServiceLinks: true
  preemptionPolicy: PreemptLowerPriority
  priority: 0  # Remove this line as this is the old default priority
  ...
```
Save the file using :wq! and then delete the old critical-app pod:

```bash
kubectl delete pod critical-app
```
Apply the new updated manifest file:

```bash
kubectl apply -f critical-app.yaml
```bash

```yaml
# complete critical-app.yaml
apiVersion: v1
kind: Pod
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"v1","kind":"Pod","metadata":{"annotations":{},"name":"critical-app","namespace":"default"},"sp>
  creationTimestamp: "2025-08-20T11:09:01Z"
  generation: 1
  name: critical-app
  namespace: default
  resourceVersion: "4518"
  uid: 009f8d52-9577-49ed-81c7-826087401f3e
spec:
  containers:
  - image: nginx
    imagePullPolicy: Always
    name: critical-container
    resources:
      limits:
        memory: 3Gi
      requests:
        cpu: "1"
        memory: 3Gi
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-5mb6d
      readOnly: true
  dnsPolicy: ClusterFirst
  priorityClassName: high-priority   # Add the high-priority class
  enableServiceLinks: true
  preemptionPolicy: PreemptLowerPriority
  restartPolicy: Always
  schedulerName: default-scheduler
  securityContext: {}
  serviceAccount: default
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
  - name: kube-api-access-5mb6d
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
    lastTransitionTime: "2025-08-20T11:09:01Z"
    message: '0/1 nodes are available: 1 Insufficient cpu, 1 Insufficient memory.
      preemption: 0/1 nodes are available: 1 No preemption victims found for incoming
      pod.'
    reason: Unschedulable
    status: "False"
    type: PodScheduled
  phase: Pending
  qosClass: Burstable
```





