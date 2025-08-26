# Certificates API

If you really look closely, most overnight successes took a long time.

– Steve Jobs

1. A new member akshay joined our team. He requires access to our cluster. The Certificate Signing Request is at the ``` /root location ```.

Inspect it

```bash
ls
akshay.csr  akshay.key
```

2. Create a **CertificateSigningRequest** object with the name akshay with the contents of the akshay.csr file

As of kubernetes 1.19, the API to use for CSR is certificates.k8s.io/v1.

Please note that an additional field called signerName should also be added when creating CSR. For client authentication to the API server we will use the built-in signer kubernetes.io/kube-apiserver-client.

Use this command to generate the base64 encoded format as following: -

```bash
cat akshay.csr | base64 -w 0
```

Finally, save the below YAML in a file and create a CSR name akshay as follows: -

```yaml
---
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: akshay
spec:
  groups:
  - system:authenticated
  request: <Paste the base64 encoded value of the CSR file>
  signerName: kubernetes.io/kube-apiserver-client
  usages:
  - client auth
```

```bash
kubectl apply -f akshay-csr.yaml
```

2. What is the Condition of the newly created Certificate Signing Request object?

**Run** the command kubectl get csr

**Pending**

```bash
k get csr
NAME        AGE     SIGNERNAME                                    REQUESTOR                  REQUESTEDDURATION   CONDITION
akshay      2m34s   kubernetes.io/kube-apiserver-client           kubernetes-admin           <none>              Pending
csr-hj4h7   31m     kubernetes.io/kube-apiserver-client-kubelet   system:node:controlplane   <none>              Approved,Issued
```

3. Approve the CSR Request

**Run** the command kubectl certificate approve akshay

```bash
k certificate approve akshay
certificatesigningrequest.certificates.k8s.io/akshay approved
```

4. How many CSR requests are available on the cluster?

Including approved and pending

**Run** the command kubectl get csr

**2**

```bash
k get csr
NAME        AGE     SIGNERNAME                                    REQUESTOR                  REQUESTEDDURATION   CONDITION
akshay      7m17s   kubernetes.io/kube-apiserver-client           kubernetes-admin           <none>              Approved,Issued
csr-hj4h7   36m     kubernetes.io/kube-apiserver-client-kubelet   system:node:controlplane   <none>              Approved,Issued
```

5. During a routine check you realized that there is a new CSR request in place. What is the name of this request?

**gent-smith**


```bash
k get csr
NAME          AGE     SIGNERNAME                                    REQUESTOR                  REQUESTEDDURATION   CONDITION
agent-smith   67s     kubernetes.io/kube-apiserver-client           agent-x                    <none>              Pending
akshay        9m55s   kubernetes.io/kube-apiserver-client           kubernetes-admin           <none>              Approved,Issued
csr-hj4h7     39m     kubernetes.io/kube-apiserver-client-kubelet   system:node:controlplane   <none>              Approved,Issued
```

6. Hmmm.. You are not aware of a request coming in. What groups is this CSR requesting access to?

Check the details about the request. Preferably in YAML.

**system:masters**

**Run** the command ``` kubectl get csr agent-smith -o yaml ```

```bash
k get csr agent-smith -o yaml
```

```yaml
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  creationTimestamp: "2025-08-26T08:04:53Z"
  name: agent-smith
  resourceVersion: "3396"
  uid: b7c53778-1432-4205-a77f-fcd82f310ab0
spec:
  extra:
    authentication.kubernetes.io/credential-id:
    - X509SHA256=ce89c45675549e8c37bed92fd2fe3134a843dee38d5f1c31b7ffd1e4fb4648e0
  groups:
  - system:masters
  - system:authenticated
  ...
```

7. That doesn't look very right. Reject that request.

**Run** the command ``` kubectl certificate deny agent-smith ```

```bash
k certificate deny agent-smith
certificatesigningrequest.certificates.k8s.io/agent-smith denied
```

8. Let's get rid of it. Delete the new CSR object

**Run** the command ``` kubectl delete csr agent-smith ```

```bash
k delete csr agent-smith 
certificatesigningrequest.certificates.k8s.io "agent-smith" deleted
```







