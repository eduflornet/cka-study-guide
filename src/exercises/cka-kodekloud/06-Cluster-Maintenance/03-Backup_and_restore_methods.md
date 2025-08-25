# Backup and Restore Methods

Learning never exhausts the mind.

– Leonardo da Vinci

1. We have a working Kubernetes cluster with a set of web applications running. Let us first explore the setup.

How many deployments exist in the cluster in default namespace?

**2** = blue, red

```bash
k -n default get deploy
NAME   READY   UP-TO-DATE   AVAILABLE   AGE
blue   3/3     3            3           2m55s
red    2/2     2            2           2m55s
```

2. What is the version of ETCD running on the cluster?

Check the ETCD Pod or Process

**v 3.5.21**

```bash
etcdctl version
etcdctl version: 3.5.16
API version: 3.5
```

3. At what address can you reach the ETCD cluster from the controlplane node?

Check the ETCD Service configuration in the ETCD POD

Use the command ``` kubectl describe pod etcd-controlplane -n kube-system ``` and look for ``` --listen-client-urls ```

```bash
k -n kube-system describe pods etcd-controlplane | grep "listen-client-urls"
      --listen-client-urls=https://127.0.0.1:2379,https://192.168.126.171:2379
```

4. Where is the ETCD server certificate file located?

Note this path down as you will need to use it later

5.

6. Where is the ETCD CA Certificate file located?

Note this path down as you will need to use it later.

Check the ETCD pod configuration kubectl describe pod etcd-controlplane  -n kube-system

**Path: /etc/kubernetes/pki/etcd**

Check the ETCD pod configuration with the command: kubectl describe pod etcd-controlplane  -n kube-system and look for the value of --trusted-ca-file:

```bash
root@controlplane:~# kubectl -n kube-system describe pod etcd-controlplane | grep '\--trusted-ca-file'
      --trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
root@controlplane:~#
```

7. The controlplane node in our cluster is planned for a regular maintenance reboot tonight. While we do not anticipate anything to go wrong, we are required to take the necessary backups. Take a snapshot of the ETCD database using the built-in snapshot functionality. Store the backup file at location ``` /opt/snapshot-pre-boot.db ```

Use the ``` etcdctl snapshot save ``` command. You will have to make use of additional flags to connect to the ETCD server.

--endpoints: Optional Flag, points to the address where ETCD is running (127.0.0.1:2379)

--cacert: Mandatory Flag (Absolute Path to the CA certificate file)

--cert: Mandatory Flag (Absolute Path to the Server certificate file)

--key: Mandatory Flag (Absolute Path to the Key file)

**Solution**

Using etcdctl command, create a snapshot for the etcd database in /opt/snapshot-pre-boot.db:

```bash
etcdctl --endpoints=https://[127.0.0.1]:2379 \
--cacert=/etc/kubernetes/pki/etcd/ca.crt \
--cert=/etc/kubernetes/pki/etcd/server.crt \
--key=/etc/kubernetes/pki/etcd/server.key \
snapshot save /opt/snapshot-pre-boot.db
```
Example output:

```bash
controlplane ~ ➜  etcdctl --endpoints=https://[127.0.0.1]:2379 \
--cacert=/etc/kubernetes/pki/etcd/ca.crt \
--cert=/etc/kubernetes/pki/etcd/server.crt \
--key=/etc/kubernetes/pki/etcd/server.key \
snapshot save /opt/snapshot-pre-boot.db
{"level":"info","ts":"2025-04-24T09:36:04.813156Z","caller":"snapshot/v3_snapshot.go:65","msg":"created temporary db file","path":"/opt/snapshot-pre-boot.db.part"}
{"level":"info","ts":"2025-04-24T09:36:04.820464Z","logger":"client","caller":"v3@v3.5.16/maintenance.go:212","msg":"opened snapshot stream; downloading"}
{"level":"info","ts":"2025-04-24T09:36:04.820534Z","caller":"snapshot/v3_snapshot.go:73","msg":"fetching snapshot","endpoint":"https://[127.0.0.1]:2379"}
{"level":"info","ts":"2025-04-24T09:36:04.853763Z","logger":"client","caller":"v3@v3.5.16/maintenance.go:220","msg":"completed snapshot read; closing"}
{"level":"info","ts":"2025-04-24T09:36:04.853939Z","caller":"snapshot/v3_snapshot.go:88","msg":"fetched snapshot","endpoint":"https://[127.0.0.1]:2379","size":"1.7 MB","took":"now"}
{"level":"info","ts":"2025-04-24T09:36:04.854024Z","caller":"snapshot/v3_snapshot.go:97","msg":"saved","path":"/opt/snapshot-pre-boot.db"}
Snapshot saved at /opt/snapshot-pre-boot.db
```

8. Great! Let us now wait for the maintenance window to finish. Go get some sleep. (Don't go for real)

Click Ok to Continue

9. Wake up! We have a conference call! After the reboot the master nodes came back online, but none of our applications are accessible. Check the status of the applications on the cluster. What's wrong?

Are you able to see any deployments/pods or services in the default namespace?

```bash
k -n default get all
NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   172.20.0.1   <none>        443/TCP   107s
```

10. Luckily we took a backup. Restore the original state of the cluster using the backup file.

**Restore** the etcd to a new directory from the snapshot by using the ``` etcdctl snapshot restore ``` command. Once the directory is restored, update the ETCD configuration to use the restored directory.

First, using the etcdutl command, restore the snapshot:

```bash
etcdutl snapshot restore /opt/snapshot-pre-boot.db --data-dir /var/lib/etcd-from-backup
```

Example output:

```bash
025-08-25T14:58:31Z    info    snapshot/v3_snapshot.go:265     restoring snapshot      {"path": "/opt/snapshot-pre-boot.db", "wal-dir": "/var/lib/etcd-from-backup/member/wal", "data-dir": "/var/lib/etcd-from-backup", "snap-dir": "/var/lib/etcd-from-backup/member/snap", "initial-memory-map-size": 10737418240}
2025-08-25T14:58:31Z    info    membership/store.go:141 Trimming membership information from the backend...
2025-08-25T14:58:31Z    info    membership/cluster.go:421       added member    {"cluster-id": "cdf818194e3a8c32", "local-member-id": "0", "added-peer-id": "8e9e05c52164694d", "added-peer-peer-urls": ["http://localhost:2380"]}
2025-08-25T14:58:31Z    info    snapshot/v3_snapshot.go:293     restored snapshot       {"path": "/opt/snapshot-pre-boot.db", "wal-dir": "/var/lib/etcd-from-backup/member/wal", "data-dir": "/var/lib/etcd-from-backup", "snap-dir": "/var/lib/etcd-from-backup/member/snap", "initial-memory-map-size": 10737418240}
```

Note: In this case, we are restoring the snapshot to a different directory which is in the same server where we took the backup (the controlplane node). As a result, the only required option for the restore command is the ``` --data-dir ```.

Next, we need to update the ``` /etc/kubernetes/manifests/etcd.yaml ``` to point to the newly restored directory, which is ``` /var/lib/etcd-from-backup ```. The only change that we need to make to the YAML file, is to change the hostPath for the volume called etcd-data from old directory ``` /var/lib/etcd ``` to the new directory ``` /var/lib/etcd-from-backup ```:


```bash
  ...
  volumes:
  - hostPath:
      path: /var/lib/etcd-from-backup # Newly restored backup directory
      type: DirectoryOrCreate
    name: etcd-data
```

With this change, ``` /var/lib/etcd ``` on the container points to ``` /var/lib/etcd-from-backup ``` on the controlplane.

When this file is updated, the ETCD pod is automatically re-created as this is a static pod placed under the 
``` /etc/kubernetes/manifests ``` directory. This may take a few minutes, and it is expected that kube-controller-manager and kube-scheduler will also restart. To check the containers being restarted:

```bash
watch crictl ps


CONTAINER           IMAGE               CREATED              STATE               NAME                      ATTEMPT             POD I
D              POD                                    NAMESPACE
62baefcd8e309       499038711c081       38 seconds ago       Running             etcd                      0                   c3c9e
0f5db540       etcd-controlplane                      kube-system
8ab9904bb59be       8d72586a76469       About a minute ago   Running             kube-scheduler            1                   d570d
15f2360f       kube-scheduler-controlplane            kube-system
eb152a7d537ed       1d579cb6d6967       About a minute ago   Running             kube-controller-manager   1                   72ff7
5222abac       kube-controller-manager-controlplane   kube-system
57f94a9724731       ead0a4a53df89       48 minutes ago       Running             coredns                   0                   b8bf1
2641658f       coredns-7484cd47db-jzxfd               kube-system
3d0b4a33d7bb4       ead0a4a53df89       48 minutes ago       Running             coredns                   0                   b88dc
11b3403a       coredns-7484cd47db-bl5gn               kube-system
57b92a2389aae       01cdfa8dd262f       48 minutes ago       Running             kube-flannel              0                   f4601
70accd41       kube-flannel-ds-cnnz7                  kube-flannel
7790d5d846dbb       f1184a0bd7fe5       48 minutes ago       Running             kube-proxy                0                   6c32e
32bbb633       kube-proxy-cr5cr                       kube-system
3f310e4ad17fc       6ba9545b2183e       48 minutes ago       Running             kube-apiserver            0                   7d057
23538db3       kube-apiserver-controlplane            kube-system
```

Once the updated etcd container and the kube-apiserver containers are up, you can verify that the missing deployments (2 deployments) and services (3 services) are restored again:

```bash
kubectl get deployments,services
```

```bash
k -n default get all
NAME                        READY   STATUS    RESTARTS   AGE
pod/blue-69968556cc-222sl   1/1     Running   0          48m
pod/blue-69968556cc-l4hkf   1/1     Running   0          48m
pod/blue-69968556cc-w6pf5   1/1     Running   0          48m
pod/red-5799fd84f8-dc9lx    1/1     Running   0          48m
pod/red-5799fd84f8-mcf6k    1/1     Running   0          48m

NAME                   TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
service/blue-service   NodePort    172.20.42.27     <none>        80:30082/TCP   48m
service/kubernetes     ClusterIP   172.20.0.1       <none>        443/TCP        52m
service/red-service    NodePort    172.20.199.119   <none>        80:30080/TCP   48m

NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/blue   3/3     3            3           48m
deployment.apps/red    2/2     2            2           48m

NAME                              DESIRED   CURRENT   READY   AGE
replicaset.apps/blue-69968556cc   3         3         3       48m
replicaset.apps/red-5799fd84f8    2         2         2       48m
```
