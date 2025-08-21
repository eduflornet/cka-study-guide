# Understand extension interfaces (CNI, CSI, CRI, etc.)

Kubelet uses 3 key interfaces to enable flexible container runtime, network, and storage features:

Container Runtime Interface (CRI) – enables the kubelet to use any CRI compliant container runtime to run containers
Container Network Interface (CNI) – enables the kubelet to use any CNI compliant container networking solution to attach containers to networks
Container Storage Interface (CSI) – enables the kubelet to use any CSI compliant container storage implementation connect containers to storage volumes
These interfaces are implemented as either:

- an executable (CNI)
- or daemon (CRI, CSI)

**CRI**
CRI enables pluggable container runtimes in Kubernetes. Kubelet previously had integral support for Docker and Rocket (rkt). CRI Consists of:

- Specifications and requirements
- Defines a gRPC API comprised of two services: 
    - ImageService – pull, inspect, and remove images
    - RuntimeService – manage containers (create / start / exec / attach / etc.)
**Kubelet** calls the container runtime (or a shim) over a Unix socket. Depending on your container runtime of choice, you will need to supply the appropriate value for the ``` --cri-socket``` option:  ``` sudo kubeadm init --cri-socket=unix:///var/run/containerd containerd.sock ```

**CNI**
Kubernetes kubelets create Pods, which requires the kubelet to configure pod networking. The CNI plugin is selected by passing kubelet the ``` --network-plugin=cni ``` command-line option. **Kubelet** reads a file from ```--cni-conf-dir ``` (default ```/etc/cni/net.d ```) and uses the CNI configuration from that file to set up each pod’s network. The CNI configuration file must match the CNI specification. Any required CNI plugins referenced by the configuration must be present in ``` --cni-bin-dir ``` (default ``` /opt/cni/bin ```). It is the CNI plugin’s responsibility to, among other things, assign IP addresses to pods in the cluster. Different CNI plugins will achieve this in different ways.

**CSI**
CSI is a standard for exposing block and file storage systems to containerized workloads on container orchestration systems like Kubernetes. Allows third parties to write and deploy plugins exposing new storage systems in Kubernetes without ever having to touch the core Kubernetes code.

How it works:

Administrators deploy an in-cluster agent, known as a driver, which acts as provisioner for a Kubernetes storage class
Users define a PVC that uses the driver’s storage class
When the PVC is created, the driver will use an external provisioner that calls the backend to create the a volume for the requested PV that binds the PVC.

It's essential for the CKA exam, as it helps you understand how Kubernetes integrates with networking, storage, and runtimes in a modular way. We'll break it down with clear explanations, practical examples, and commands you could use on the exam.

🧩 What are Extension Interfaces?
Kubernetes uses a modular architecture that allows you to extend its capabilities without modifying the core. The three key interfaces you should master are:

CRI (Container Runtime Interface) -> Run containers -> containerd, CRI-O
CNI (Container Network Interface) -> Connect Pods to networks -> Calico, Cilium, Flannel
CSI (Container Storage Interface) -> Connect Pods to storage -> OpenEBS, Longhorn, Ceph

🧱 1. CRI – Container Runtime Interface
🔹 What does it do?
Allows the kubelet to communicate with the container runtime (such as containerd or CRI-O) using a gRPC API.

🔧 Practical example

```bash
# Verificar el socket del runtime
ps aux | grep kubelet | grep cri

# Usar containerd como runtime
sudo kubeadm init --cri-socket=unix:///var/run/containerd/containerd.sock
```

🧠 Exam Tip
Know how to change the runtime using --cri-socket

Recognize the differences between Docker (deprecated) and containerd

🌐 2. CNI – Container Network Interface
🔹 What does it do?
Connects Pods to the cluster network. Assigns IPs, configures routes, and applies network policies.

🔧 Practical example

```bash
# Ver configuración CNI
ls /etc/cni/net.d/

# Instalar Calico como CNI
kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
```

🧠 Exam Tip
Know where the binaries (/opt/cni/bin) and configurations (/etc/cni/net.d) are located.

Understand how Pods obtain IP addresses using the plugin.

📦 3. CSI – Container Storage Interface
🔹 What does it do?
Allows Kubernetes to connect to external storage systems (block, file, etc.).

🔧 Practical example

```yaml
# Crear un PVC que usa un CSI driver
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: demo-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
  storageClassName: csi-hostpath-sc
```

```bash
kubectl apply -f pvc.yaml
```

🧠 Exam Tip
Understand how the PVC → PV → CSI driver flow works.

Recognize drivers such as OpenEBS, Longhorn, etc.

CKA Question Simulation
TASK: Install a CNI plugin and verify that Pods are successfully obtaining IP addresses.

```bash
kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
kubectl run test-pod --image=nginx --restart=Never
kubectl get pod test-pod -o wide
```

### Recommended resources

[Kubernetes Core & Extensions | CNI, CSI, CRI, Add-Ons & Plugins Explained | CKA Course 2025](https://www.youtube.com/live/AVovCH0dvyM)

