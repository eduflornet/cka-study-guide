## Taints and Tolerations
Arise, awake, and stop not till the goal is reached.
– Swami Vivekananda

1. Do any taints exist on node01 node?
hint: Use the kubectl describe command to see the taint property.

```shell
kubectl describe nodes node01 | grep Tain
```

2. Create a taint on node01 with key of spray, value of mortein and effect of NoSchedule
- Key = spray
- Value = mortein
- Effect = NoSchedule

Run the command: 

```shell 
kubectl taint nodes node01 spray=mortein:NoSchedule 
```

3. Create a new pod with the nginx image and pod name as mosquito.
- Image name: nginx

```shell
kubectl run mosquito --image nginx 
```

What is the state of the POD?

```shell 
kubectl get pods ```
NAME       READY   STATUS    RESTARTS   AGE
mosquito   0/1     Pending   0          45s
```

Why do you think the pod is in a pending state?

```shell 
kubectl describe pod mosquito

Warning  FailedScheduling  2m35s  default-scheduler  0/2 nodes are available: 1 node(s) had untolerated taint {node-role.kubernetes.io/control-plane: }, 1 node(s) had untolerated taint {spray: mortein}. preemption: 0/2 nodes are available: 2 Preemption is not helpful for scheduling.
```

Describe complete:

```shell
Name:             mosquito
Namespace:        default
Priority:         0
Service Account:  default
Node:             <none>
Labels:           run=mosquito
Annotations:      <none>
Status:           Pending
IP:               
IPs:              <none>
Containers:
  mosquito:
    Image:        nginx
    Port:         <none>
    Host Port:    <none>
    Environment:  <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-gbrqw (ro)
Conditions:
  Type           Status
  PodScheduled   False 
Volumes:
  kube-api-access-gbrqw:
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
  Type     Reason            Age    From               Message
  ----     ------            ----   ----               -------
  Warning  FailedScheduling  2m35s  default-scheduler  0/2 nodes are available: 1 node(s) had untolerated taint {node-role.kubernetes.io/control-plane: }, 1 node(s) had untolerated taint {spray: mortein}. preemption: 0/2 nodes are available: 2 Preemption is not helpful for scheduling.
```


4. Create another pod named bee with the nginx image, which has a toleration set to the taint mortein.

- Image name: nginx
- Key: spray
- Value: mortein
- Effect: NoSchedule
- Status: Running

Run the following command and see how to use ```--overrides=```

```shell
kubectl run bee \
  --image=nginx \
  --overrides='
{
  "apiVersion": "v1",
  "spec": {
    "tolerations": [
      {
        "key": "spray",
        "operator": "Equal",
        "value": "mortein",
        "effect": "NoSchedule"
      }
    ]
  }
}'
```

🧠 ¿Qué hace este comando?
kubectl run bee: crea un pod llamado bee.
--image=nginx: usa la imagen nginx.
--overrides: permite añadir configuraciones avanzadas como tolerations directamente en el comando.

✅ Resultado esperado
El pod bee se ejecutará en un nodo que tenga el taint spray=mortein:NoSchedule, siempre que no haya otras restricciones que lo impidan.

Otra forma es crear un yaml declarativo y editarlo para agregar tolerations y al final aplicar.

```shell
kubectl run been --image nginx --dry-run='client' -o yaml > pod-toleration.yaml
```

```shell
nano pod-toleration.yaml
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: bee
spec:
  containers:
  - image: nginx
    name: bee
  tolerations:
  - key: spray
    value: mortein
    operator: Equal
    effect: NoSchedule
```
```shell
kubectl apply -f pod-tolerations.yaml
```

Observe that the bee pod has been scheduled on node node01 due to the toleration that has been configured for the pod.

```shell
kubectl get pods
NAME       READY   STATUS    RESTARTS   AGE
bee        1/1     Running   0          4m50s
mosquito   0/1     Pending   0          24m
```

Do you see any taints on controlplane node?

```shell
kubectl describe nodes controlplane | grep Tain
Taints:             node-role.kubernetes.io/control-plane:NoSchedule
```

Remove the taint on controlplane, which currently has the taint effect of NoSchedule.
Node name: controlplane

hint:
Run the command: ```kubectl taint nodes controlplane node-role.kubernetes.io/control-plane:NoSchedule-``` to untaint the node.

The error occurs because the taint node-role.kubernetes.io/control-plane:NoSchedule already exists on the node controlplane. Since --overwrite is not used, Kubernetes won't replace the existing taint. To remove it, run:

kubectl taint nodes controlplane node-role.kubernetes.io/control-plane:NoSchedule-

This command will untaint the node, removing the NoSchedule effect.

¡Claro! Ese error te está diciendo que el nodo controlplane ya tiene el taint node-role.kubernetes.io/control-plane:NoSchedule, y como no estás usando --overwrite, Kubernetes no te deja repetirlo.

🛠 Solución
Si lo que quieres es actualizar o reaplicar ese taint, simplemente añade la opción ```--overwrite```:

```shell
kubectl taint nodes controlplane node-role.kubernetes.io/control-plane:NoSchedule --overwrite
```

🧼 Alternativas útiles
Si lo que querías era quitar ese taint, puedes hacerlo así:

```shell
kubectl taint nodes controlplane node-role.kubernetes.io/control-plane:NoSchedule-
```

El guion al final (-) indica que quieres eliminar ese taint.

Verifica los taints actuales
Para ver los taints que tiene el nodo:

```shell
kubectl describe node controlplane | grep Taint
```

What is the state of the pod mosquito now?

```shell
kubectl get pods mosquito
NAME       READY   STATUS    RESTARTS   AGE
mosquito   1/1     Running   0          40m
```

Which node is the POD mosquito on now?
controlplane, look at the result of the describe.

```shell
kubectl describe pod mosquito

Name:             mosquito
Namespace:        default
Priority:         0
Service Account:  default
Node:             controlplane/192.168.114.211
Start Time:       Wed, 13 Aug 2025 09:29:21 +0000
Labels:           run=mosquito
Annotations:      <none>
Status:           Running
IP:               172.17.0.4
IPs:
  IP:  172.17.0.4
Containers:
  mosquito:
    Container ID:   containerd://629b1745c74ef006f39399f85c2fa76b013b47eeab8afe5fe8264ad7942802bc
    Image:          nginx
    Image ID:       docker.io/library/nginx@sha256:bb395301e0be72b272d301a41bdcfe09241c0114b8fd53e9626c04a9ee672620
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Wed, 13 Aug 2025 09:29:31 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-gbrqw (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       True 
  ContainersReady             True 
  PodScheduled                True 
Volumes:
  kube-api-access-gbrqw:
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
  Type     Reason            Age                  From               Message
  ----     ------            ----                 ----               -------
  Warning  FailedScheduling  6m39s (x8 over 41m)  default-scheduler  0/2 nodes are available: 1 node(s) had untolerated taint {node-role.kubernetes.io/control-plane: }, 1 node(s) had untolerated taint {spray: mortein}. preemption: 0/2 nodes are available: 2 Preemption is not helpful for scheduling.
  Normal   Scheduled         2m20s                default-scheduler  Successfully assigned default/mosquito to controlplane
  Normal   Pulling           2m19s                kubelet            Pulling image "nginx"
  Normal   Pulled            2m10s                kubelet            Successfully pulled image "nginx" in 8.611s (8.611s including waiting). Image size: 72227568 bytes.
  Normal   Created           2m10s                kubelet            Created container: mosquito
  Normal   Started           2m10s                kubelet            Started container mosquito
```
