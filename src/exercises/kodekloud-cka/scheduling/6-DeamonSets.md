# DeamonSets

Losers visualize the penalties of failure, Winners visualize the rewards of success.
– Dr.Rob Gilbert

1. How many DaemonSets are created in the cluster in all namespaces?

Check all namespaces

```bash
kubectl get ds -A
NAMESPACE      NAME              DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR            AGE
kube-flannel   kube-flannel-ds   1         1         1       1            1           <none>                   5m12s
kube-system    kube-proxy        1         1         1       1            1           kubernetes.io/os=linux   5m14s
```

2. Which namespace is the kube-proxy Daemonset created in?

```bash
kube-system
```

3. Which of the below is a DaemonSet?

```bash
kube-flannel-ds
```

4. On how many nodes are the pods scheduled by the DaemonSet kube-proxy?
Run the command kubectl describe daemonset kube-proxy --namespace=kube-system
= 1

```bash
kubectl describe daemonset kube-proxy -n kube-system
Name:           kube-proxy
Namespace:      kube-system
Selector:       k8s-app=kube-proxy
Node-Selector:  kubernetes.io/os=linux
Labels:         k8s-app=kube-proxy
Annotations:    deprecated.daemonset.template.generation: 1
Desired Number of Nodes Scheduled: 1
Current Number of Nodes Scheduled: 1
Number of Nodes Scheduled with Up-to-date Pods: 1
Number of Nodes Scheduled with Available Pods: 1
Number of Nodes Misscheduled: 0
Pods Status:  1 Running / 0 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:           k8s-app=kube-proxy
  Service Account:  kube-proxy
  Containers:
   kube-proxy:
    Image:      registry.k8s.io/kube-proxy:v1.33.0
    Port:       <none>
    Host Port:  <none>
    Command:
      /usr/local/bin/kube-proxy
      --config=/var/lib/kube-proxy/config.conf
      --hostname-override=$(NODE_NAME)
    Environment:
      NODE_NAME:   (v1:spec.nodeName)
    Mounts:
      /lib/modules from lib-modules (ro)
      /run/xtables.lock from xtables-lock (rw)
      /var/lib/kube-proxy from kube-proxy (rw)
  Volumes:
   kube-proxy:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      kube-proxy
    Optional:  false
   xtables-lock:
    Type:          HostPath (bare host directory volume)
    Path:          /run/xtables.lock
    HostPathType:  FileOrCreate
   lib-modules:
    Type:               HostPath (bare host directory volume)
    Path:               /lib/modules
    HostPathType:       
  Priority Class Name:  system-node-critical
  Node-Selectors:       kubernetes.io/os=linux
  Tolerations:          op=Exists
Events:
  Type    Reason            Age    From                  Message
  ----    ------            ----   ----                  -------
  Normal  SuccessfulCreate  9m47s  daemonset-controller  Created pod: kube-proxy-wwcc6
  ```

  5. What is the image used by the POD deployed by the kube-flannel-ds DaemonSet?

```bash
kubectl describe daemonset kube-flannel-ds -n kube-flannel | grep -i image
    Image:      docker.io/flannel/flannel-cni-plugin:v1.2.0
    Image:      docker.io/flannel/flannel:v0.23.0
    Image:      docker.io/flannel/flannel:v0.23.0
```

All information about ``` kube-flannel-ds ```

```bash
  kubectl describe daemonset kube-flannel-ds -n kube-flannel
Name:           kube-flannel-ds
Namespace:      kube-flannel
Selector:       app=flannel,k8s-app=flannel
Node-Selector:  <none>
Labels:         app=flannel
                k8s-app=flannel
                tier=node
Annotations:    deprecated.daemonset.template.generation: 1
Desired Number of Nodes Scheduled: 1
Current Number of Nodes Scheduled: 1
Number of Nodes Scheduled with Up-to-date Pods: 1
Number of Nodes Scheduled with Available Pods: 1
Number of Nodes Misscheduled: 0
Pods Status:  1 Running / 0 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:           app=flannel
                    k8s-app=flannel
                    tier=node
  Service Account:  flannel
  Init Containers:
   install-cni-plugin:
    Image:      docker.io/flannel/flannel-cni-plugin:v1.2.0
    Port:       <none>
    Host Port:  <none>
    Command:
      cp
    Args:
      -f
      /flannel
      /opt/cni/bin/flannel
    Environment:  <none>
    Mounts:
      /opt/cni/bin from cni-plugin (rw)
   install-cni:
    Image:      docker.io/flannel/flannel:v0.23.0
    Port:       <none>
    Host Port:  <none>
    Command:
      cp
    Args:
      -f
      /etc/kube-flannel/cni-conf.json
      /etc/cni/net.d/10-flannel.conflist
    Environment:  <none>
    Mounts:
      /etc/cni/net.d from cni (rw)
      /etc/kube-flannel/ from flannel-cfg (rw)
  Containers:
   kube-flannel:
    Image:      docker.io/flannel/flannel:v0.23.0
    Port:       <none>
    Host Port:  <none>
    Command:
      /opt/bin/flanneld
    Args:
      --ip-masq
      --kube-subnet-mgr
      --iface=eth0
    Requests:
      cpu:     100m
      memory:  50Mi
    Environment:
      POD_NAME:            (v1:metadata.name)
      POD_NAMESPACE:       (v1:metadata.namespace)
      EVENT_QUEUE_DEPTH:  5000
    Mounts:
      /etc/kube-flannel/ from flannel-cfg (rw)
      /run/flannel from run (rw)
      /run/xtables.lock from xtables-lock (rw)
  Volumes:
   run:
    Type:          HostPath (bare host directory volume)
    Path:          /run/flannel
    HostPathType:  
   cni-plugin:
    Type:          HostPath (bare host directory volume)
    Path:          /opt/cni/bin
    HostPathType:  
   cni:
    Type:          HostPath (bare host directory volume)
    Path:          /etc/cni/net.d
    HostPathType:  
   flannel-cfg:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      kube-flannel-cfg
    Optional:  false
   xtables-lock:
    Type:               HostPath (bare host directory volume)
    Path:               /run/xtables.lock
    HostPathType:       FileOrCreate
  Priority Class Name:  system-node-critical
  Node-Selectors:       <none>
  Tolerations:          :NoSchedule op=Exists
Events:
  Type    Reason            Age   From                  Message
  ----    ------            ----  ----                  -------
  Normal  SuccessfulCreate  12m   daemonset-controller  Created pod: kube-flannel-ds-smqjt
```

6. Deploy a DaemonSet for FluentD Logging.
Use the given specifications.

- Name: elasticsearch
- Namespace: kube-system
- Image: registry.k8s.io/fluentd-elasticsearch:1.20

Solution:
An easy way to create a DaemonSet is to start by generating a Deployment YAML using:

```bash
kubectl create deployment elasticsearch
--image=registry.k8s.io/fluentd-elasticsearch:1.20
-n kube-system --dry-run=client -o yaml > fluentd.yaml
```

Then, edit the YAML as follows:

- Change: kind: Deployment → kind: DaemonSet
- Ensure: apiVersion: apps/v1
- Remove these fields from the YAML:
    spec.replicas
    spec.strategy
    status (entire section, if present)
- Finally, create the DaemonSet using: ``` kubectl create -f fluentd.yaml ```

Practice:

```bash
kubectl create deployment elasticsearch -n kube-system --image registry.k8s.io/fluentd-elasticsearch:1.20 --dry-run=client -o yaml > fluentd.yaml
```

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  creationTimestamp: null
  labels:
    app: elasticsearch
  name: elasticsearch
  namespace: kube-system
spec:
  selector:
    matchLabels:
      app: elasticsearch
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: elasticsearch
    spec:
      containers:
      - image: registry.k8s.io/fluentd-elasticsearch:1.20
        name: fluentd-elasticsearch
        resources: {}
```

```bash
kubectl create -f fluentd.yaml 
daemonset.apps/elasticsearch created
```
