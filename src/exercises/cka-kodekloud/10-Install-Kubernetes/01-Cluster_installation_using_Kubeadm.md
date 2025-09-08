# Cluster Installation using KubeAdm

If you really look closely, most overnight successes took a long time.

– Steve Jobs


Success is not final, failure is not fatal: it is the courage to continue that counts.

– Winston Churchill


The only thing standing between you and outrageous success is continuous progress.

– Dan Waldschmidt

#### Resources

[Kubernetes Install](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)


1. Prepare both nodes (controlplane and node01) for Kubernetes by completing the following steps:

- Apply the necessary sysctl parameters for networking.
- Install the kubeadm and kubelet packages at the exact version 1.33.0-1.1 on both nodes.
- Install the kubectl package at the exact version 1.33.0-1.1 exclusively on the controlplane.

Refer to the official k8s documentation - https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/

and follow the installation steps.

These steps have to be performed on both nodes.

```bash
set net.bridge.bridge-nf-call-iptables to 1:

cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
br_netfilter
EOF

cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF

sudo sysctl --system
```
The container runtime has already been installed on both nodes, so you may skip this step.
Install kubeadm, kubectl and kubelet on all nodes:

```bash

sudo apt-get update

sudo apt-get install -y apt-transport-https ca-certificates curl

curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.33/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.33/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo apt-get update

# To see the new version labels
sudo apt-cache madison kubeadm

sudo apt-get install -y kubelet=1.33.0-1.1 kubeadm=1.33.0-1.1 kubectl=1.33.0-1.1

sudo apt-mark hold kubelet kubeadm kubectl

```

Repeat last steps to node01

```bash
ssh node01
```

**Explanation**

🧠 ¿Qué significa “set net.bridge.bridge-nf-call-iptables to 1”?
Este parámetro activa el filtrado de tráfico de red en el kernel para interfaces de tipo bridge (puente), que es común en redes de contenedores.

``` net.bridge.bridge-nf-call-iptables = 1```: permite que el tráfico que pasa por interfaces bridge sea inspeccionado por las reglas de iptables.

Esto es crítico para que las políticas de red y el enrutamiento funcionen correctamente en Kubernetes.

🧱 Paso 1: Cargar el módulo br_netfilter

```bash
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
br_netfilter
EOF
```

🔹 Esto crea un archivo de configuración que indica al sistema que debe cargar el módulo del kernel br_netfilter al arrancar. 🔹 Este módulo permite que el tráfico de red en interfaces bridge sea procesado por el sistema de filtrado del kernel.

🧱 Paso 2: Configurar parámetros del kernel

```bash
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
```

🔹 Esto crea un archivo de configuración persistente (/etc/sysctl.d/k8s.conf) que activa el filtrado de tráfico IPv4 e IPv6 en interfaces bridge.

🔹 Luego deberías aplicar los cambios con:

```bash
sudo sysctl --system
```

🔧 1. Preparar el sistema

```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl
```
Esto actualiza los paquetes del sistema y asegura que puedes descargar paquetes desde repositorios HTTPS.

🔐 2. Agregar la clave GPG del repositorio oficial de Kubernetes

```bash
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.33/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
```

Esto descarga y convierte la clave de firma del repositorio oficial de Kubernetes para que el sistema confíe en los paquetes que vas a instalar.

📦 3. Agregar el repositorio de Kubernetes

```bash
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.33/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
```

Esto añade el repositorio oficial de Kubernetes versión 1.33 a tu sistema para que puedas instalar kubeadm, kubectl y kubelet.

🔄 4. Actualizar la lista de paquetes

```bash
sudo apt-get update
```

Esto recarga la lista de paquetes disponibles, incluyendo los del nuevo repositorio.

🔍 5. Ver versiones disponibles

```bash
sudo apt-cache madison kubeadm
```
Este comando muestra las versiones disponibles de kubeadm, útil para elegir una versión específica.

📥 6. Instalar los componentes principales

```bash
sudo apt-get install -y kubelet=1.33.0-1.1 kubeadm=1.33.0-1.1 kubectl=1.33.0-1.1
```

Instala:

kubelet: el agente que corre en cada nodo y gestiona los Pods.
kubeadm: herramienta para inicializar y unir nodos al clúster.
kubectl: la CLI para interactuar con el clúster.

🔒 7. Marcar los paquetes para que no se actualicen automáticamente

```bash
sudo apt-mark hold kubelet kubeadm kubectl
```

Esto evita que futuras actualizaciones del sistema modifiquen las versiones instaladas de estos componentes, lo cual es importante para mantener la compatibilidad del clúster.


Finally:

- Sysctl settings applied on controlplane?
- Sysctl settings applied on node01?
- kubeadm installed on controlplane?
- kubelet installed on controlplane?
- Kubeadm installed on worker node01?
- Kubelet installed on worker node01 ?
- kubectl installed on controlplane?

2. What is the version of kubelet installed?

**v1.33.0**

Run:

```bash
kubelet --version 
```

on the controlplane node.

3. How many nodes are part of kubernetes cluster currently?

Are you able to run kubectl get nodes?

**Cero**

4. Let's now bootstrap a kubernetes cluster using kubeadm, configuring custom networking for pods and services, optimizing API server exposure, and overall cluster functionality.

The latest version of Kubernetes will be installed.

5. Initialize Control Plane Node (Master Node). Use the following options:

- apiserver-advertise-address - Use the IP address allocated to eth0 on the controlplane node

- apiserver-cert-extra-sans - Set it to controlplane

- pod-network-cidr - Set to 172.17.0.0/16

- service-cidr - Set to 172.20.0.0/16

Once done, set up the default kubeconfig file and wait for node to be part of the cluster.

**Hint** 

```bash 
kubeadm init --apiserver-cert-extra-sans=controlplane --apiserver-advertise-address 192.217.72.10 --pod-network-cidr=172.17.0.0/16 --service-cidr=172.20.0.0/16
```

The IP address used here is just an example. It will change for your lab session. Make sure to check the IP address allocated to eth0 by running:

```bash
root@controlplane:~# ifconfig eth0
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1450
        inet 192.217.72.10  netmask 255.255.255.0  broadcast 192.217.72.255
        ether 02:42:c0:d9:48:0a  txqueuelen 0  (Ethernet)
        RX packets 4730  bytes 674845 (674.8 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 4550  bytes 1572687 (1.5 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

root@controlplane:~#
```
In this example, the IP address is 192.217.72.10


**Solution**

```bash
IP_ADDR=$(ip addr show eth0 | grep -oP '(?<=inet\s)\d+(\.\d+){3}')
kubeadm init --apiserver-cert-extra-sans=controlplane --apiserver-advertise-address $IP_ADDR --pod-network-cidr=172.17.0.0/16 --service-cidr=172.20.0.0/16
```

Once you run the init command, you should see an output similar to below:

```bash
I0907 09:34:38.687214   20054 version.go:261] remote version is much newer: v1.34.0; falling back to: stable-1.33
[init] Using Kubernetes version: v1.33.4
[preflight] Running pre-flight checks
        [WARNING SystemVerification]: cgroups v1 support is in maintenance mode, please migrate to cgroups v2
[preflight] Pulling images required for setting up a Kubernetes cluster
[preflight] This might take a minute or two, depending on the speed of your internet connection
[preflight] You can also perform this action beforehand using 'kubeadm config images pull'
W0907 09:34:38.909542   20054 checks.go:846] detected that the sandbox image "registry.k8s.io/pause:3.6" of the container runtime is inconsistent with that used by kubeadm.It is recommended to use "registry.k8s.io/pause:3.10" as the CRI sandbox image.
[certs] Using certificateDir folder "/etc/kubernetes/pki"
[certs] Generating "ca" certificate and key
[certs] Generating "apiserver" certificate and key
[certs] apiserver serving cert is signed for DNS names [controlplane kubernetes kubernetes.default kubernetes.default.svc kubernetes.default.svc.cluster.local] and IPs [172.20.0.1 192.168.100.167]
[certs] Generating "apiserver-kubelet-client" certificate and key
[certs] Generating "front-proxy-ca" certificate and key
[certs] Generating "front-proxy-client" certificate and key
[certs] Generating "etcd/ca" certificate and key
[certs] Generating "etcd/server" certificate and key
[certs] etcd/server serving cert is signed for DNS names [controlplane localhost] and IPs [192.168.100.167 127.0.0.1 ::1]
[certs] Generating "etcd/peer" certificate and key
[certs] etcd/peer serving cert is signed for DNS names [controlplane localhost] and IPs [192.168.100.167 127.0.0.1 ::1]
[certs] Generating "etcd/healthcheck-client" certificate and key
[certs] Generating "apiserver-etcd-client" certificate and key
[certs] Generating "sa" key and public key
[kubeconfig] Using kubeconfig folder "/etc/kubernetes"
[kubeconfig] Writing "admin.conf" kubeconfig file
[kubeconfig] Writing "super-admin.conf" kubeconfig file
[kubeconfig] Writing "kubelet.conf" kubeconfig file
[kubeconfig] Writing "controller-manager.conf" kubeconfig file
[kubeconfig] Writing "scheduler.conf" kubeconfig file
[etcd] Creating static Pod manifest for local etcd in "/etc/kubernetes/manifests"
[control-plane] Using manifest folder "/etc/kubernetes/manifests"
[control-plane] Creating static Pod manifest for "kube-apiserver"
[control-plane] Creating static Pod manifest for "kube-controller-manager"
[control-plane] Creating static Pod manifest for "kube-scheduler"
[kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[kubelet-start] Starting the kubelet
[wait-control-plane] Waiting for the kubelet to boot up the control plane as static Pods from directory "/etc/kubernetes/manifests"
[kubelet-check] Waiting for a healthy kubelet at http://127.0.0.1:10248/healthz. This can take up to 4m0s
[kubelet-check] The kubelet is healthy after 1.001714119s
[control-plane-check] Waiting for healthy control plane components. This can take up to 4m0s
[control-plane-check] Checking kube-apiserver at https://192.168.100.167:6443/livez
[control-plane-check] Checking kube-controller-manager at https://127.0.0.1:10257/healthz
[control-plane-check] Checking kube-scheduler at https://127.0.0.1:10259/livez
[control-plane-check] kube-scheduler is healthy after 12.50335544s
[control-plane-check] kube-controller-manager is healthy after 16.004270169s
[control-plane-check] kube-apiserver is healthy after 18.001373511s
[upload-config] Storing the configuration used in ConfigMap "kubeadm-config" in the "kube-system" Namespace
[kubelet] Creating a ConfigMap "kubelet-config" in namespace kube-system with the configuration for the kubelets in the cluster
[upload-certs] Skipping phase. Please see --upload-certs
[mark-control-plane] Marking the node controlplane as control-plane by adding the labels: [node-role.kubernetes.io/control-plane node.kubernetes.io/exclude-from-external-load-balancers]
[mark-control-plane] Marking the node controlplane as control-plane by adding the taints [node-role.kubernetes.io/control-plane:NoSchedule]
[bootstrap-token] Using token: crejm3.23cqtyy3dqts8q6s
[bootstrap-token] Configuring bootstrap tokens, cluster-info ConfigMap, RBAC Roles
[bootstrap-token] Configured RBAC rules to allow Node Bootstrap tokens to get nodes
[bootstrap-token] Configured RBAC rules to allow Node Bootstrap tokens to post CSRs in order for nodes to get long term certificate credentials
[bootstrap-token] Configured RBAC rules to allow the csrapprover controller automatically approve CSRs from a Node Bootstrap Token
[bootstrap-token] Configured RBAC rules to allow certificate rotation for all node client certificates in the cluster
[bootstrap-token] Creating the "cluster-info" ConfigMap in the "kube-public" namespace
[kubelet-finalize] Updating "/etc/kubernetes/kubelet.conf" to point to a rotatable kubelet client certificate and key
[addons] Applied essential addon: CoreDNS
[addons] Applied essential addon: kube-proxy

Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 192.168.100.167:6443 --token crejm3.23cqtyy3dqts8q6s \
        --discovery-token-ca-cert-hash sha256:e9586a93c990e87a9dffe70fc9df60f3c8304314a5e33438b7d9ecdc1815787e 
```

Once the command has been run successfully, set up the kubeconfig:

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

6. Generate a kubeadm join token

Or copy the one that was generated by kubeadm init command

**Hint**
Use the join token provided by the kubeadm command or create a new token.

To create token:

```bash
kubeadm token create --print-join-command
```

kubeadm join 192.168.100.167:6443 --token fahev1.pypxt8mkplv541fj --discovery-token-ca-cert-hash sha256:e9586a93c990e87a9dffe70fc9df60f3c8304314a5e33438b7d9ecdc1815787e 

next, SSH to the node01 and run the join command on the terminal:

```bash
ssh node01
```
```bash
kubeadm join 192.168.100.167:6443 --token fahev1.pypxt8mkplv541fj --discovery-token-ca-cert-hash sha256:e9586a93c990e87a9dffe70fc9df60f3c8304314a5e33438b7d9ecdc1815787e 
```

```bash
[preflight] Running pre-flight checks
        [WARNING SystemVerification]: cgroups v1 support is in maintenance mode, please migrate to cgroups v2
[preflight] Reading configuration from the "kubeadm-config" ConfigMap in namespace "kube-system"...
[preflight] Use 'kubeadm init phase upload-config --config your-config-file' to re-upload it.
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[kubelet-start] Starting the kubelet
[kubelet-check] Waiting for a healthy kubelet at http://127.0.0.1:10248/healthz. This can take up to 4m0s
[kubelet-check] The kubelet is healthy after 501.915481ms
[kubelet-start] Waiting for the kubelet to perform the TLS Bootstrap

This node has joined the cluster:
* Certificate signing request was sent to apiserver and a response was received.
* The Kubelet was informed of the new secure connection details.

```

Run 'kubectl get nodes' on the control-plane to see this node join the cluster.

7. To install a network plugin, we will go with Flannel as the default choice. For inter-host communication, we will utilize the eth0 interface and update the Network field accordingly.

Ensure that the Flannel manifest includes the appropriate options for this configuration.

For detailed instructions, refer to the official documentation linked in the upper right corner above the terminal.

**Hint**

- Install flannel CNI and make sure to specify the interface to eth0 and update the Network field.

On the controlplane node, run the following set of commands to deploy the network plugin:

- Download the original YAML file and save it as kube-flannel.yml:
curl -LO https://raw.githubusercontent.com/flannel-io/flannel/v0.20.2/Documentation/kube-flannel.yml
Open the kube-flannel.yml file using a text editor.

We are using a custom PodCIDR (172.17.0.0/16) instead of the default 10.244.0.0/16 when bootstrapping the Kubernetes cluster. However, the Flannel manifest by default is configured to use 10.244.0.0/16 as its network, which does not align with the specified PodCIDR. To resolve this, we need to update the Network field in the kube-flannel-cfg ConfigMap to match the custom PodCIDR defined during cluster initialization.

- Network Plugin deployed?

- Is Flannel using "eth0" interface for inter-host communication ?


On the controlplane node, run the following set of commands to deploy the network plugin:

Download the original YAML file and save it as kube-flannel.yml:
curl -LO https://raw.githubusercontent.com/flannel-io/flannel/v0.20.2/Documentation/kube-flannel.yml
Open the kube-flannel.yml file using a text editor.

We are using a custom PodCIDR (172.17.0.0/16) instead of the default 10.244.0.0/16 when bootstrapping the Kubernetes cluster. However, the Flannel manifest by default is configured to use 10.244.0.0/16 as its network, which does not align with the specified PodCIDR. To resolve this, we need to update the Network field in the kube-flannel-cfg ConfigMap to match the custom PodCIDR defined during cluster initialization.

```bash
net-conf.json: |
    {
      "Network": "10.244.0.0/16", # Update this to match the custom PodCIDR
      "Backend": {
        "Type": "vxlan"
      }
```

Locate the args section within the kube-flannel container definition. It should look like this:
```bash
  args:
  - --ip-masq
  - --kube-subnet-mgr
```
Add the additional argument ``` - --iface=eth0 ``` to the existing list of arguments.

Now apply the modified manifest ``` kube-flannel.yml ``` file using kubectl:

```bash
kubectl apply -f kube-flannel.yml
```

After applying the manifest, wait for all the pods to become in the Ready state. You can use the watch command to monitor the pod status:

```bash
watch kubectl get pods -A
```

Example of expected pods:

```bash
controlplane ~ ➜  kubectl get pods -A
NAMESPACE      NAME                                   READY   STATUS    RESTARTS   AGE
kube-flannel   kube-flannel-ds-gc5kf                  1/1     Running   0          54s
kube-flannel   kube-flannel-ds-mtjd6                  1/1     Running   0          54s
kube-system    coredns-668d6bf9bc-7lf7s               1/1     Running   0          3m31s
kube-system    coredns-668d6bf9bc-jl8t6               1/1     Running   0          3m31s
kube-system    etcd-controlplane                      1/1     Running   0          3m37s
kube-system    kube-apiserver-controlplane            1/1     Running   0          3m37s
kube-system    kube-controller-manager-controlplane   1/1     Running   0          3m37s
kube-system    kube-proxy-t5wrt                       1/1     Running   0          3m31s
kube-system    kube-proxy-trmhs                       1/1     Running   0          3m8s
kube-system    kube-scheduler-controlplane            1/1     Running   0          3m37s
After all the pods are in the Ready state, the status of both nodes should now become Ready:

controlplane ~ ➜  kubectl get nodes 
NAME           STATUS   ROLES           AGE   VERSION 
controlplane   Ready    control-plane   15m   v1.33.0 
node01         Ready    <none>          15m   v1.33.0 

```

```yaml
# kube-flannel.yml
kind: Namespace
apiVersion: v1
metadata:
  name: kube-flannel
  labels:
    pod-security.kubernetes.io/enforce: privileged
---
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: flannel
rules:
- apiGroups:
  - ""
  resources:
  - pods
  verbs:
  - get
- apiGroups:
  - ""
  resources:
  - nodes
  verbs:
  - get
  - list
  - watch
- apiGroups:
  - ""
  resources:
  - nodes/status
  verbs:
  - patch
---
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: flannel
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: flannel
subjects:
- kind: ServiceAccount
  name: flannel
  namespace: kube-flannel
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: flannel
  namespace: kube-flannel
---
kind: ConfigMap
apiVersion: v1
metadata:
  name: kube-flannel-cfg
  namespace: kube-flannel
  labels:
    tier: node
    app: flannel
data:
  cni-conf.json: |
    {
      "name": "cbr0",
      "cniVersion": "0.3.1",
      "plugins": [
        {
          "type": "flannel",
          "delegate": {
            "hairpinMode": true,
            "isDefaultGateway": true
          }
        },
        {
          "type": "portmap",
          "capabilities": {
            "portMappings": true
          }
        }
      ]
    }
  net-conf.json: |
    {
      "Network": "192.168.57.19",
      "Backend": {
        "Type": "vxlan"
      }
    }
---
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: kube-flannel-ds
  namespace: kube-flannel
  labels:
    tier: node
    app: flannel
spec:
  selector:
    matchLabels:
      app: flannel
  template:
    metadata:
      labels:
        tier: node
        app: flannel
    spec:
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: kubernetes.io/os
                operator: In
                values:
                - linux
      hostNetwork: true
      priorityClassName: system-node-critical
      tolerations:
      - operator: Exists
        effect: NoSchedule
      serviceAccountName: flannel
      initContainers:
      - name: install-cni-plugin
       #image: flannelcni/flannel-cni-plugin:v1.1.0 for ppc64le and mips64le (dockerhub limitations may apply)
        image: docker.io/rancher/mirrored-flannelcni-flannel-cni-plugin:v1.1.0
        command:
        - cp
        args:
        - -f
        - /flannel
        - /opt/cni/bin/flannel
        volumeMounts:
        - name: cni-plugin
          mountPath: /opt/cni/bin
      - name: install-cni
       #image: flannelcni/flannel:v0.20.2 for ppc64le and mips64le (dockerhub limitations may apply)
        image: docker.io/rancher/mirrored-flannelcni-flannel:v0.20.2
        command:
        - cp
        args:
        - -f
        - /etc/kube-flannel/cni-conf.json
        - /etc/cni/net.d/10-flannel.conflist
        volumeMounts:
        - name: cni
          mountPath: /etc/cni/net.d
        - name: flannel-cfg
          mountPath: /etc/kube-flannel/
      containers:
      - name: kube-flannel
       #image: flannelcni/flannel:v0.20.2 for ppc64le and mips64le (dockerhub limitations may apply)
        image: docker.io/rancher/mirrored-flannelcni-flannel:v0.20.2
        command:
        - /opt/bin/flanneld
        args:
        - --ip-masq
        - --kube-subnet-mgr
        - --iface=eth0
        resources:
          requests:
            cpu: "100m"
            memory: "50Mi"
          limits:
            cpu: "100m"
            memory: "50Mi"
        securityContext:
          privileged: false
          capabilities:
            add: ["NET_ADMIN", "NET_RAW"]
        env:
        - name: POD_NAME
          valueFrom:
            fieldRef:
              fieldPath: metadata.name
        - name: POD_NAMESPACE
          valueFrom:
            fieldRef:
              fieldPath: metadata.namespace
        - name: EVENT_QUEUE_DEPTH
          value: "5000"
 volumeMounts:
        - name: run
          mountPath: /run/flannel
        - name: flannel-cfg
          mountPath: /etc/kube-flannel/
        - name: xtables-lock
          mountPath: /run/xtables.lock
      volumes:
      - name: run
        hostPath:
          path: /run/flannel
      - name: cni-plugin
        hostPath:
          path: /opt/cni/bin
      - name: cni
        hostPath:
          path: /etc/cni/net.d
      - name: flannel-cfg
        configMap:
          name: kube-flannel-cfg
      - name: xtables-lock
        hostPath:
          path: /run/xtables.lock
          type: FileOrCreate
```






