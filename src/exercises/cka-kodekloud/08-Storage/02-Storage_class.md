# Storage Class

Learning never exhausts the mind.

– Leonardo da Vinci

1. How many StorageClasses exist in the cluster right now?

**One**

```bash
k get storageclasses
NAME                   PROVISIONER             RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
local-path (default)   rancher.io/local-path   Delete          WaitForFirstConsumer   false                  11m
```

2. How about now? How many Storage Classes exist in the cluster?

We just created a few new Storage Classes. Inspect them.

**Three**

```bash
 k get storageclasses
NAME                        PROVISIONER                     RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
local-path (default)        rancher.io/local-path           Delete          WaitForFirstConsumer   false                  13m
local-storage               kubernetes.io/no-provisioner    Delete          WaitForFirstConsumer   false                  26s
portworx-io-priority-high   kubernetes.io/portworx-volume   Delete          Immediate              false                  26s
```

3. What is the name of the Storage Class that does not support dynamic volume provisioning?

**local-storage**

4. What is the Volume Binding Mode used for this storage class (the one identified in the previous question)?

**WaitForFirstConsumer**

5. What is the Provisioner used for the storage class called portworx-io-priority-high?

**portworx-volume**

6. Create a new **PersistentVolumeClaim** named local-pvc with the following configuration:

- StorageClass: local-path
- Access Mode: ReadWriteOnce
- Requested Storage: 500Mi
- Do not use the volumeName field in the PVC.

Note: If the persistent volume claim (PVC) is in a Pending state, disregard it and proceed to the next task.

Inspect the persistent volume and look for the Access Mode, Storage and StorageClassName used. Use this information to create the PVC.


```yaml
# local-pvc.yaml 
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: local-pvc
spec:
  storageClassName: local-path
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 500Mi
```

7. What is the status of the newly created Persistent Volume Claim?

**Pending**

```bash
k get pvc
NAME        STATUS    VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   VOLUMEATTRIBUTESCLASS   AGE
local-pvc   Pending                                      local-path     <unset>                 36s
```

8. Why is the PVC still in a pending state, even though it includes a valid request using the local-path storage class?

**waiting for first consumer to be created before binding**


Inspect the PVC events.

```bash
k events pvc local-pvc.yaml 

28m                 Normal    Synced                           Node/controlplane                 Node synced successfully
28m                 Normal    RegisteredNode                   Node/controlplane                 Node controlplane event: Registered Node controlplane in Controller
8s (x9 over 2m1s)   Normal    WaitForFirstConsumer             PersistentVolumeClaim/local-pvc   waiting for first consumer to be created before binding
```

9. The Storage Class called local-path makes use of VolumeBindingMode set to WaitForFirstConsumer. This will delay the binding and provisioning of a PersistentVolume until a Pod using the PersistentVolumeClaim is created.

```bash
k get storageclasses
NAME                        PROVISIONER                     RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
local-path (default)        rancher.io/local-path           Delete          WaitForFirstConsumer   false                  32m
local-storage               kubernetes.io/no-provisioner    Delete          WaitForFirstConsumer   false                  19m
portworx-io-priority-high   kubernetes.io/portworx-volume   Delete          Immediate              false                  19m
```

10. Create a new pod called nginx with the image nginx:alpine. The Pod should make use of the PVC local-pvc and mount the volume at the path /var/www/html.

The PVC local-pvc should be in a bound state.

Use the command kubectl run to create a pod definition file for nginx pod with image nginx:alpine. Add mount volume path /var/www/html and pvc given in the details.

Alternatively, run the command:

```bash
kubectl run nginx --image=nginx:alpine --dry-run=client -oyaml > nginx.yaml
```

Solution manifest file to create a pod called nginx is as follows:

```yaml
# nginx.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    name: nginx
spec:
  containers:
  - name: nginx
    image: nginx:alpine
    volumeMounts:
      - name: local-persistent-storage
        mountPath: /var/www/html
  volumes:
    - name: local-persistent-storage
      persistentVolumeClaim:
        claimName: local-pvc
```

```bash
k get pod
NAME    READY   STATUS    RESTARTS   AGE
nginx   1/1     Running   0          15s
```

11. What is the status of the local-pvc Persistent Volume Claim now?

**Bound**

```bash
k get pvc
NAME        STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   VOLUMEATTRIBUTESCLASS   AGE
local-pvc   Bound    pvc-13d150dc-61c3-4426-8b55-07d62db68ada   500Mi      RWO            local-path     <unset>                 17m
```

12. Create a new Storage Class called delayed-volume-sc that makes use of the below specs:

- provisioner: kubernetes.io/no-provisioner
- volumeBindingMode: WaitForFirstConsumer

- Storage Class uses: kubernetes.io/no-provisioner ?
- Storage Class volumeBindingMode set to WaitForFirstConsumer ?

```yaml
# delayed-volume-sc.yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: delayed-volume-sc
  annotations:
    storageclass.kubernetes.io/is-default-class: "false"
provisioner: kubernetes.io/no-provisioner
allowVolumeExpansion: true
mountOptions:
  - discard # this might enable UNMAP / TRIM at the block storage layer
volumeBindingMode: WaitForFirstConsumer
parameters:
  guaranteedReadWriteLatency: "true" # provider-specific
```
