# Security Contexts

One big reason for a winning attitude is that you will take the necessary steps and not quit when the going gets difficult.

– Don M.Green

1. What is the user used to execute the sleep process within the ubuntu-sleeper pod?

In the current(default) namespace.

**root**

Run the command: 

```bash
kubectl exec ubuntu-sleeper -- whoami
```
and check the user that is running the container.

2. Edit the pod ubuntu-sleeper to run the sleep process with user ID 1010.

Note: Only make the necessary changes. Do not modify the name or image of the pod.

Set a security context to run as user 1010.

To delete the existing ubuntu-sleeper pod:

```bash
k get pod ubuntu-sleeper -o yaml > ubuntu-sleeper-pod.yaml
```

After that apply solution manifest file to run as user 1010 as follows:

```yaml
# ubuntu-sleeper-pod.yaml
---
apiVersion: v1
kind: Pod
metadata:
  name: ubuntu-sleeper
  namespace: default
spec:
  securityContext:
    runAsUser: 1010
  containers:
  - command:
    - sleep
    - "4800"
    image: ubuntu
    name: ubuntu-sleeper
```

```bash
kubectl delete po ubuntu-sleeper 

k apply -f ubuntu-sleeper-pod.yaml 
```

NOTE: TO delete the pod faster, you can run kubectl delete pod ubuntu-sleeper --force. This can be done for any pod in the lab or the actual exam. It is not recommended to run this in Production, so keep a note of that.

3. A Pod definition file named multi-pod.yaml is given. With what user are the processes in the web container started?

The pod is created with multiple containers and security contexts defined at the Pod and Container level.

= el usuario que esta definido en el container: **1002**

```yaml
# multi-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: multi-pod
spec:
  securityContext:
    runAsUser: 1001
  containers:
  -  image: ubuntu
     name: web
     command: ["sleep", "5000"]
     securityContext:
      runAsUser: 1002

  -  image: ubuntu
     name: sidecar
     command: ["sleep", "5000"]
```

4. With what user are the processes in the sidecar container started?

The pod is created with multiple containers and security contexts defined at the Pod and Container level.

= Con el usuario que esta definido a nivel de pod: 1001

5. Update pod ubuntu-sleeper to run as Root user and with the SYS_TIME capability.

Note:
Only make the necessary changes. Do not modify the name of the pod.

Set the capabilities within the container, not at the pod level, as setting them at the pod level is not supported.

Add **SYS_TIME** capability to the container's Security Context.

To delete the existing pod:

```bash
kubectl delete po ubuntu-sleeper
```
After that apply solution manifest file to add capabilities in ubuntu-sleeper pod:

```yaml
# ubuntu-sleeper-pod.yaml 
---
apiVersion: v1
kind: Pod
metadata:
  name: ubuntu-sleeper
  namespace: default
spec:
  containers:
  - command:
    - sleep
    - "4800"
    image: ubuntu
    name: ubuntu-sleeper
    securityContext:
      capabilities:
        add: ["SYS_TIME"]
```
then run the command kubectl apply -f <file-name>.yaml to create a pod from given definition file.

6. Now update the pod to also make use of the **NET_ADMIN** capability.

Note:
Only make the necessary changes. Do not modify the name of the pod.

Set the capabilities within the container, not at the pod level, as setting them at the pod level is not supported.

Add **NET_ADMIN** capability to the container's Security Context.

To delete the existing pod:

```bash
kubectl delete po ubuntu-sleeper
```
After that apply solution manifest file to add capabilities in ubuntu-sleeper pod:

```yaml
---
apiVersion: v1
kind: Pod
metadata:
  name: ubuntu-sleeper
  namespace: default
spec:
  containers:
  - command:
    - sleep
    - "4800"
    image: ubuntu
    name: ubuntu-sleeper
    securityContext:
      capabilities:
        add: ["SYS_TIME", "NET_ADMIN"]
```
then run the command kubectl apply -f <file-name>.yaml to create a pod from given definition file.






