# Multiple Schedulers

You teach best what you most need to learn.

Be not in despair, the way is very difficult, like walking on the edge of a razor; yet despair not, arise, awake, and find the ideal, the goal.

– Swami Vivekananda


1. What is the name of the POD that deploys the default kubernetes scheduler in this environment?
kube-scheduler-controlplane

```bash
kubectl get pods -n kube-system
NAME                                   READY   STATUS    RESTARTS   AGE
coredns-7484cd47db-5j7kw               1/1     Running   0          7m2s
coredns-7484cd47db-fqhq6               1/1     Running   0          7m2s
etcd-controlplane                      1/1     Running   0          7m8s
kube-apiserver-controlplane            1/1     Running   0          7m8s
kube-controller-manager-controlplane   1/1     Running   0          7m8s
kube-proxy-77dwp                       1/1     Running   0          7m3s
kube-scheduler-controlplane            1/1     Running   0          7m8s
```

2. What is the image used to deploy the kubernetes scheduler?
image: registry.k8s.io/kube-scheduler:v1.33.0
Inspect the kubernetes scheduler pod and identify the image

```bash
kubectl get pod -n kube-system kube-scheduler-controlplane -o yaml | grep -i image
    image: registry.k8s.io/kube-scheduler:v1.33.0
    imagePullPolicy: IfNotPresent
    image: registry.k8s.io/kube-scheduler:v1.33.0
    imageID: registry.k8s.io/kube-scheduler@sha256:8dd2fbeb7f711da53a89ded239e54133f34110d98de887a39a9021e651b51f1f

```
3. We have already created the ServiceAccount and ClusterRoleBinding that our custom scheduler will make use of.

Checkout the following Kubernetes objects:

ServiceAccount: my-scheduler (kube-system namespace)
ClusterRoleBinding: my-scheduler-as-kube-scheduler
ClusterRoleBinding: my-scheduler-as-volume-scheduler


Run the command: kubectl get serviceaccount -n kube-system and kubectl get clusterrolebinding


Note: - Don't worry if you are not familiar with these resources. We will cover it later on.


```bash
kubectl get sa -n kube-system
NAME                                          SECRETS   AGE
attachdetach-controller                       0         14m
bootstrap-signer                              0         14m
certificate-controller                        0         14m
clusterrole-aggregation-controller            0         14m
coredns                                       0         14m
cronjob-controller                            0         14m
daemon-set-controller                         0         14m
default                                       0         14m
deployment-controller                         0         14m
disruption-controller                         0         14m
endpoint-controller                           0         14m
endpointslice-controller                      0         14m
endpointslicemirroring-controller             0         14m
ephemeral-volume-controller                   0         14m
expand-controller                             0         14m
generic-garbage-collector                     0         14m
horizontal-pod-autoscaler                     0         14m
job-controller                                0         14m
kube-proxy                                    0         14m
legacy-service-account-token-cleaner          0         14m
my-scheduler                                  0         4m51s
namespace-controller                          0         14m
node-controller                               0         14m
persistent-volume-binder                      0         14m
pod-garbage-collector                         0         14m
pv-protection-controller                      0         14m
pvc-protection-controller                     0         14m
replicaset-controller                         0         14m
replication-controller                        0         14m
resourcequota-controller                      0         14m
```

```bash
kubectl get clusterrolebinding -n kube-system
NAME                                                            ROLE                                                                               AGE
cluster-admin                                                   ClusterRole/cluster-admin                                                          15m
flannel                                                         ClusterRole/flannel                                                                15m
kubeadm:cluster-admins                                          ClusterRole/cluster-admin                                                          15m
kubeadm:get-nodes                                               ClusterRole/kubeadm:get-nodes                                                      15m
kubeadm:kubelet-bootstrap                                       ClusterRole/system:node-bootstrapper                                               15m
kubeadm:node-autoapprove-bootstrap                              ClusterRole/system:certificates.k8s.io:certificatesigningrequests:nodeclient       15m
kubeadm:node-autoapprove-certificate-rotation                   ClusterRole/system:certificates.k8s.io:certificatesigningrequests:selfnodeclient   15m
kubeadm:node-proxier                                            ClusterRole/system:node-proxier                                                    15m
my-scheduler-as-kube-scheduler                                  ClusterRole/system:kube-scheduler                                                  5m25s
my-scheduler-as-volume-scheduler                                ClusterRole/system:volume-scheduler                                                5m25s
...
```

4. Please create a ConfigMap that the new scheduler will utilize, implementing the concept of ConfigMap as a volume.
A ConfigMap definition file named my-scheduler-configmap.yaml has been provided at the /root/ path. This file will be used to create a ConfigMap with the name my-scheduler-config, utilizing the content from the file located at /root/my-scheduler-config.yaml.

Hint:
Execute the command kubectl create -f to generate the configmap from the provided definition file.

Run the below command to create the configMap:

```yaml
apiVersion: kubescheduler.config.k8s.io/v1
kind: KubeSchedulerConfiguration
profiles:
  - schedulerName: my-scheduler
leaderElection:
  leaderElect: false
```

```bash
kubectl create -f /root/my-scheduler-configmap.yaml.
```

5. Deploy an additional scheduler to the cluster following the given specification.

Utilize the manifest file located at ``` /root/my-scheduler.yaml ```. Ensure that you are using the same image as that of the default Kubernetes scheduler.

To verify the image used by the default Kubernetes scheduler, execute the following command:

```bash
kubectl describe pod kube-scheduler-controlplane --namespace=kube-system | grep -i image
    Image:         registry.k8s.io/kube-scheduler:v1.33.0
    Image ID:      registry.k8s.io/kube-scheduler@sha256:8dd2fbeb7f711da53a89ded239e54133f34110d98de887a39a9021e651b51f1f
```

Note : Deploying the new scheduler may take a few seconds to reach a running state.

Hint:
Please utilize the file located at ``` /root/my-scheduler.yaml ``` to establish your own scheduler, ensuring that you update the image accordingly.

```yaml
# /root/my-scheduler.yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: my-scheduler
  name: my-scheduler
  namespace: kube-system
spec:
  serviceAccountName: my-scheduler
  containers:
  - command:
    - /usr/local/bin/kube-scheduler
    - --config=/etc/kubernetes/my-scheduler/my-scheduler-config.yaml
    image: registry.k8s.io/kube-scheduler:v1.33.0 # changed
    livenessProbe:
      httpGet:
        path: /healthz
        port: 10259
        scheme: HTTPS
      initialDelaySeconds: 15
    name: kube-second-scheduler
    readinessProbe:
      httpGet:
        path: /healthz
        port: 10259
        scheme: HTTPS
    resources:
      requests:
        cpu: '0.1'
    securityContext:
      privileged: false
    volumeMounts:
      - name: config-volume
        mountPath: /etc/kubernetes/my-scheduler
  hostNetwork: false
  hostPID: false
  volumes:
    - name: config-volume
      configMap:
        name: my-scheduler-config
```

Run:

```bash 
kubectl create -f my-scheduler.yaml and wait sometime for the container to be in running state.
```

6. Please modify the provided Pod manifest file located at ``` /root/nginx-pod.yaml ``` to specify that the Pod should be scheduled by your custom scheduler, which is named my-scheduler.

```bash
# /root/nginx-pod.yaml
apiVersion: v1 
kind: Pod 
metadata:
  name: nginx 
spec:
  schedulerName: my-scheduler #changed
  containers:
  - image: nginx
    name: nginx
```

After updating, create the Pod in the default namespace and verify it is scheduled by your custom scheduler.

Note : The pod may take a few seconds to reach a running state.

Run

```bash
kubectl create -f nginx-pod.yaml
```