# Mock Exam 3

Whatever we believe about ourselves and our ability comes true for us.

– Susan L. Taylor

1. You are an administrator preparing your environment to deploy a Kubernetes cluster using kubeadm. Adjust the following network parameters on the system to the following values, and make sure your changes persist reboots:

- net.ipv4.ip_forward = 1
- net.bridge.bridge-nf-call-iptables = 1

2. Create a new service account with the name **pvviewer**. Grant this Service account access to list all PersistentVolumes in the cluster by creating an appropriate cluster role called **pvviewer-role** and ClusterRoleBinding called **pvviewer-role-binding**.
Next, create a pod called **pvviewer** with the image: **redis** and serviceAccount: pvviewer in the default namespace.

3. Create a StorageClass named **rancher-sc** with the following specifications:

- The provisioner should be **rancher.io/local-path**.
- The volume binding mode should be **WaitForFirstConsumer**.
- Volume expansion should be **enabled**.

4. Create a ConfigMap named **app-config** in the namespace **cm-namespace** with the following key-value pairs:

- ENV=production
- LOG_LEVEL=info

Then, modify the existing Deployment named **cm-webapp** in the same namespace to use the **app-config** ConfigMap by setting the environment variables **ENV** and **LOG_LEVEL** in the container from the ConfigMap.

5. Create a PriorityClass named **low-priority** with a value of **50000**. A pod named **lp-pod** exists in the namespace **low-priority**. Modify the pod to use the priority class you created. Recreate the pod if necessary.

6. We have deployed a new pod called **np-test-1** and a service called **np-test-service**. Incoming connections to this service are not working. Troubleshoot and fix it.
Create NetworkPolicy, by the name **ingress-to-nptest** that allows incoming connections to the service over port **80**.

Important: Don't delete any current objects deployed.

7. Taint the worker node node01 to be Unschedulable. Once done, create a pod called **dev-redis**, image **redis:alpine**, to ensure workloads are not scheduled to this worker node. Finally, create a new pod called **prod-redis** and image: **redis:alpine** with toleration to be scheduled on node01.

key: **env_type**, value: **production**, operator: **Equal** and effect: **NoSchedule**

8. A PersistentVolumeClaim named **app-pvc** exists in the namespace **storage-ns**, but it is not getting bound to the available PersistentVolume named **app-pv**.

Inspect both the PVC and PV and identify why the PVC is not being bound and fix the issue so that the PVC successfully binds to the PV. Do not modify the PV resource.

9. A kubeconfig file called **super.kubeconfig** has been created under ``` /root/CKA ```. There is something wrong with the configuration. Troubleshoot and fix it.

10. We have created a new deployment called **nginx-deploy**. Scale the deployment to **3 replicas**. Has the number of replicas increased? Troubleshoot and fix the issue.

11. Create a Horizontal Pod Autoscaler (HPA) **api-hpa** for the deployment named **api-deployment** located in the **api namespace**.
The HPA should scale the deployment based on a custom metric named **requests_per_second**, targeting an average value of **1000** requests per second across all pods.
Set the minimum number of replicas to **1** and the maximum to **20**.

Note: Deployment named **api-deployment** is available in **api namespace**. Ignore errors due to the metric **requests_per_second** not being tracked in **metrics-server**

12. Configure the web-route to split traffic between **web-service** and **web-service-v2**.The configuration should ensure that **80%** of the traffic is routed to **web-service** and **20%** is routed to **web-service-v2**.

Note: web-gateway, web-service, and web-service-v2 have already been created and are available on the cluster.

13. One application, **webpage-server-01**, is currently deployed on the Kubernetes cluster using Helm. A new version of the application is available in a Helm chart located at ``` /root/new-version ```.

Validate this new Helm chart, then install it as a new release named **webpage-server-02**. After confirming the new release is installed, uninstall the old release **webpage-server-01**.

14. While preparing to install a CNI plugin on your kubernetes cluster, you would typically want to identify the pod CIDR networks for your nodes. Identify the pod CIDR network of controlplane node in the kubernetes cluster. Output the pod CIDR network following the format x.x.x.x/x to a file at ``` /root/pod-cidr.txt ```.












