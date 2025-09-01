# CoreDNS in Kubernetes

Success doesn't come from what you do occasionally. It comes from what you do consistently.

– Marie Forleo

Always bear in mind that your own resolution to success is more important than any other one thing.

– Abraham Lincoln

1. Identify the DNS solution implemented in this cluster.

**CoreDNS**

Run the command: 

```bash
k -n kube-system get pods
NAME                                   READY   STATUS    RESTARTS   AGE
coredns-7484cd47db-mrnrr               1/1     Running   0          6m7s
coredns-7484cd47db-tsjxq               1/1     Running   0          6m7s
etcd-controlplane                      1/1     Running   0          6m14s
kube-apiserver-controlplane            1/1     Running   0          6m14s
kube-controller-manager-controlplane   1/1     Running   0          6m14s
kube-proxy-zt2vn                       1/1     Running   0          6m7s
kube-scheduler-controlplane            1/1     Running   0          6m14s
```

and look for the DNS pods.

2. How many pods of the DNS server are deployed?

**Two**

3. What is the name of the service created for accessing CoreDNS?

**kube-dns**

```bash
k -n kube-system get svc
NAME       TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)                  AGE
kube-dns   ClusterIP   172.20.0.10   <none>        53/UDP,53/TCP,9153/TCP   9m46s
```

4. What is the IP of the CoreDNS server that should be configured on PODs to resolve services?

**172.20.0.10**

Run the command: 

```bash
kubectl get service -n kube-system 
```

and look for cluster IP value.

5. Where is the configuration file located for configuring the CoreDNS service?

**/etc/coredns/Corefile**

Inspect the **Args** field of the coredns deployment and check the file used.

```yaml
Service Account:  coredns
  Containers:
   coredns:
    Image:       registry.k8s.io/coredns/coredns:v1.10.1
    Ports:       53/UDP, 53/TCP, 9153/TCP
    Host Ports:  0/UDP, 0/TCP, 0/TCP
    Args:
      -conf
      /etc/coredns/Corefile
```

Run the command: 

```bash
kubectl -n kube-system describe deployments.apps coredns | grep -A2 Args | grep Corefile

/etc/coredns/Corefile
```

6. How is the Corefile passed into the CoreDNS POD?

**Configured as ConfigMap object**

Use the kubectl get configmap command for kube-system namespace and inspect the correct ConfigMap.

```bash
k -n kube-system get configmap
NAME                                                   DATA   AGE
coredns                                                1      26m
extension-apiserver-authentication                     6      26m
kube-apiserver-legacy-service-account-token-tracking   1      26m
kube-proxy                                             2      26m
kube-root-ca.crt                                       1      26m
kubeadm-config                                         1      26m
kubelet-config  
```

7. What is the name of the ConfigMap object created for Corefile?

**coredns**

8. What is the root domain/zone configured for this kubernetes cluster?

**cluster.local**

Run the command: 

 ```bash
kubectl describe configmap coredns -n kube-system 
```

```yaml
Name:         coredns
Namespace:    kube-system
Labels:       <none>
Annotations:  <none>

Data
====
Corefile:
----
.:53 {
    errors
    health {
       lameduck 5s
    }
    ready
    kubernetes cluster.local in-addr.arpa ip6.arpa {
       pods insecure
       fallthrough in-addr.arpa ip6.arpa
       ttl 30
    }
    prometheus :9153
    forward . /etc/resolv.conf {
       max_concurrent 1000
    }
    cache 30
    loop
    reload
    loadbalance
}

BinaryData
====

Events:  <none>
```
and look for the entry after kubernetes.



9. We have deployed a set of PODs and Services in the default and payroll namespaces. Inspect them and go to the next question.

```bash
 -n default get svc,pods
NAME                   TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
service/kubernetes     ClusterIP   172.20.0.1       <none>        443/TCP        43m
service/test-service   NodePort    172.20.225.112   <none>        80:30080/TCP   39m
service/web-service    ClusterIP   172.20.192.92    <none>        80/TCP         39m

NAME                    READY   STATUS    RESTARTS   AGE
pod/hr                  1/1     Running   0          39m
pod/simple-webapp-1     1/1     Running   0          39m
pod/simple-webapp-122   1/1     Running   0          39m
pod/test                1/1     Running   0          39m
```

10. What name can be used to access the hr web server from the test Application?

**web-service**

You can execute a curl command on the test pod to test. Alternatively, the test Application also has a UI. Access it using the tab at the top of your terminal named test-app.

Use the command kubectl get svc after viewing the available services, write the correct service name and port.

11. Which of the names CANNOT be used to access the HR service from the test pod?

**web-service.dafault.pod**

12. Which of the below name can be used to access the payroll service from the test application?

**web-service.payroll**

13. Which of the below name CANNOT be used to access the payroll service from the test application?

**web-service.payroll.svc.cluster**

14. We just deployed a web server - webapp - that accesses a database mysql - server. However the web server is failing to connect to the database server. Troubleshoot and fix the issue.

They could be in different namespaces. First locate the applications. The web server interface can be seen by clicking the tab Web Server at the top of your terminal.

- Web Server: webapp

- Uses the right DB_Host name

Set the DB_Host environment variable to use mysql.payroll.

Run the command: 

```
kubectl edit deploy webapp

k delete deploy webapp --force

```

and correct the DB_Host value.

```yaml
spec:
      containers:
      - env:
        - name: DB_Host
          value: mysql.payroll
        - name: DB_User
          value: root
        - name: DB_Password
          value: paswrd
        image: mmumshad/simple-webapp-mysql
        imagePullPolicy: Always
        name: simple-webapp-mysql
        ports:
        - containerPort: 8080
          protocol: TCP
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
```

15. From the hr pod nslookup the mysql service and redirect the output to a file /root/CKA/nslookup.out

Run the command: 

```bash
kubectl exec -it hr -- nslookup mysql.payroll > /root/CKA/nslookup.out
```















