# CNI

I am experienced enough to do this. I am knowledgeable enough to do this. I am prepared enough to do this. I am mature enough to do this. I am brave enough to do this.

– Alexandria Ocasio-Cortez

1. Inspect the kubelet service and identify the container runtime endpoint value is set for Kubernetes.

**unix:///var/run/containerd/containerd.sock**

Run the command: 

```bash
ps -aux | grep kubelet | grep --color container-runtime-endpoint

bad data in /proc/uptime
root        3451  0.0  0.1 2935952 83928 ?       Ssl  11:02   0:07 /usr/bin/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/kubelet.conf --config=/var/lib/kubelet/config.yaml --container-runtime-endpoint=unix:///var/run/containerd/containerd.sock --pod-infra-container-image=registry.k8s.io/pause:3.10
```

and look at the configured --container-runtime-endpoint flag.

2. What is the path configured with all binaries of CNI supported plugins?

The CNI binaries are located under ``` /opt/cni/bin ``` by default.

```bash
ls /opt/cni/bin
bandwidth  dhcp   firewall  host-device  ipvlan   loopback  portmap  README.md  static  tuning  vrf
bridge     dummy  flannel   host-local   LICENSE  macvlan   ptp      sbr        tap     vlan
```

3. Identify which of the below plugins is not available in the list of available CNI plugins on this host?

**cisco**

Run the command: ls /opt/cni/bin and identify the one not present at that directory.

4. What is the CNI plugin configured to be used on this kubernetes cluster?

un the command: ``` ls /etc/cni/net.d/ ``` and identify the name of the plugin.

```bash
ls /etc/cni/net.d/
10-flannel.conflist
```

5. What binary executable file will be run by kubelet after a container and its associated namespace are created?

**flannel**

Look at the type field in file ``` /etc/cni/net.d/10-flannel.conflist ```.

```bash
cat /etc/cni/net.d/10-flannel.conflist
```

```yaml
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
```














