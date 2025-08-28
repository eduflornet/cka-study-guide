# Persistent Volume Claims

The greatest glory in living, lies not in never falling, but in rising every time we fall.

– Nelson Mandela

1. We have deployed a POD. Inspect the POD and wait for it to start running.

In the current(default) namespace.

2. The application stores logs at location /log/app.log. View the logs.

You can exec in to the container and open the file:

```bash
kubectl exec webapp -- cat /log/app.log

[2025-08-28 15:27:51,773] WARNING in event-simulator: USER7 Order failed as the item is OUT OF STOCK.
[2025-08-28 15:27:51,773] INFO in event-simulator: USER1 logged out
[2025-08-28 15:27:52,775] INFO in event-simulator: USER1 is viewing page3
[2025-08-28 15:27:53,776] WARNING in event-simulator: USER5 Failed to Login as the account is locked due to MANY FAILED ATTEMPTS.

```

3. If the POD was to get deleted now, would you be able to view these logs.

**No**

Use the command kubectl delete to delete a webapp pod and try to view those logs again.

The logs are stored in the Container's file system that lives only as long as the Container does. Once the pod is destroyed, you cannot view the logs again.

4. Configure a volume to store these logs at /var/log/webapp on the host.
Use the spec provided below.

- Name: webapp

- Image Name: kodekloud/event-simulator

- Volume HostPath: /var/log/webapp

- Volume Mount: /log

Use the command ``` kubectl get po webapp -o yaml > webapp.yaml ``` and add the given properties under the spec.volumes and spec.containers.volumeMounts.

OR

Use the command ``` kubectl run ``` to create a new pod and use the flag ``` --dry-run=client -o yaml ``` to generate the manifest file.

In the manifest file add spec.volumes and spec.containers.volumeMounts property.

After that, run the following command to create a pod called webapp: -

```bash
kubectl replace -f webapp.yaml --force
```

**Solution**

First delete the existing pod by running the following command: -

```bash
kubectl delete po webapp
```

then use the below manifest file to create a webapp pod with given properties as follows:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: webapp
spec:
  containers:
  - name: event-simulator
    image: kodekloud/event-simulator
    env:
    - name: LOG_HANDLERS
      value: file
    volumeMounts:
    - mountPath: /log
      name: log-volume

  volumes:
  - name: log-volume
    hostPath:
      # directory location on host
      path: /var/log/webapp
      # this field is optional
      type: Directory
```

Then run the command kubectl create -f <file-name>.yaml to create a pod.

5. Create a Persistent Volume with the given specification.

- Volume Name: pv-log

- Storage: 100Mi

- Access Modes: ReadWriteMany

- Host Path: /pv/log

- Reclaim Policy: Retain

Use the following manifest file to create a pv-log persistent volume:

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-log
spec:
  persistentVolumeReclaimPolicy: Retain
  accessModes:
    - ReadWriteMany
  capacity:
    storage: 100Mi
  hostPath:
    path: /pv/log
```

Then run the command kubectl create -f <file-name>.yaml to create a PV from manifest file.

6. Let us claim some of that storage for our application. Create a Persistent Volume Claim with the given specification.

- Persistent Volume Claim: claim-log-1

- Storage Request: 50Mi

- Access Modes: ReadWriteOnce

**Solution** manifest file to create a claim-log-1 PVC with given properties as follows:

```yaml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: claim-log-1
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 50Mi
```
Then run kubectl create -f <file-name>.yaml to create a PVC from the manifest file.

7. What is the state of the Persistent Volume Claim?

**Pending**

```bash
k get pvc
NAME          STATUS    VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   VOLUMEATTRIBUTESCLASS   AGE
claim-log-1   Pending  
```

8. What is the state of the Persistent Volume?

**Available**

```bash
k get pv
NAME     CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   VOLUMEATTRIBUTESCLASS   REASON   AGE
pv-log   100Mi      RWX            Retain           Available                          <unset>                          4m2s
```

9. Why is the claim not bound to the available Persistent Volume?

Access Modes mismastch

Run the command: kubectl get pv,pvc and look at the Access Modes.

```bash
k get pv,pvc -o wide
NAME                      CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   VOLUMEATTRIBUTESCLASS   REASON   AGE     VOLUMEMODE
persistentvolume/pv-log   100Mi      RWX            Retain           Available                          <unset>                          6m22s   Filesystem

NAME                                STATUS    VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   VOLUMEATTRIBUTESCLASS   AGE     VOLUMEMODE
persistentvolumeclaim/claim-log-1   Pending                                                     <unset>                 3m37s   Filesystem
```

Run the command: kubectl get pv,pvc and look under the Access Modes section.
The Access Modes set on the PV and the PVC do not match.

10. Update the Access Mode on the claim to bind it to the PV.
Delete and recreate the claim-log-1.

- Persistent Volume Claim: claim-log-1

- Storage Request: 50Mi

- PVol: pv-log

- Status: Bound

Set the Access Mode on the PVC to ReadWriteMany.

```yaml
 accessModes:
    - ReadWriteMany
```
To delete the existing pvc:

```bash
$ kubectl delete pvc claim-log-1
```
Solution manifest file to create a claim-log-1 PVC with correct Access Modes as follows:

```yaml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: claim-log-1
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 50Mi
```
Then run kubectl create -f <file-name>.yaml

11. You requested for 50Mi, how much capacity is now available to the PVC?

**100Mi**

```bash
k get pvc
NAME          STATUS   VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   VOLUMEATTRIBUTESCLASS   AGE
claim-log-1   Bound    pv-log   100Mi      RWX                           <unset>                 44s
```

12. Update the webapp pod to use the persistent volume claim as its storage.
Replace hostPath configured earlier with the newly created PersistentVolumeClaim.

- Name: webapp

- Image Name: kodekloud/event-simulator

- Volume: PersistentVolumeClaim=claim-log-1

- Volume Mount: /log

To delete the webapp pod first:

```bash
$ kubectl delete po webapp
```

Add --force flag in above command, if you would like to delete the pod without any delay.

To create a new webapp pod with given properties as follows:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: webapp
spec:
  containers:
  - name: event-simulator
    image: kodekloud/event-simulator
    env:
    - name: LOG_HANDLERS
      value: file
    volumeMounts:
    - mountPath: /log
      name: log-volume

  volumes:
  - name: log-volume
    persistentVolumeClaim:
      claimName: claim-log-1
```
Then run the command kubectl create -f <file-name>.yaml to create a pod from the definition file.

13. What is the Reclaim Policy set on the Persistent Volume pv-log?

```bash
k get pv pv-log 
NAME     CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                 STORAGECLASS   VOLUMEATTRIBUTESCLASS   REASON   AGE
pv-log   100Mi      RWX            Retain           Bound    default/claim-log-1                  <unset>                          20m
```

14. What would happen to the PV if the PVC was destroyed?

**the pv is nor delete buy not available**

15. Try deleting the PVC and notice what happens.

If the command hangs, you can use CTRL + C to get back to the bash prompt OR check the status of the pvc from another terminal

**pvc is stuck** 

16. Why is the PVC stuck in Terminating state?

```bash
k get pvc
NAME          STATUS        VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   VOLUMEATTRIBUTESCLASS   AGE
claim-log-1   Terminating   pv-log   100Mi      RWX                           <unset>                 12m
```

PVC is being used by a pod

17. Let us now delete the webapp Pod.

Once deleted, wait for the pod to fully terminate.

18. What is the state of the PVC now?

**Deleted**

19. What is the state of the Persistent Volume now?

**Released**









