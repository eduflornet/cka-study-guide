# View Certificate Details

Those who hoard gold have riches for a moment.
Those who hoard knowledge and skills have riches for a lifetime.

– The Diary of a CEO

1. Identify the certificate file used for the kube-api server.

**Run** the command ``` cat /etc/kubernetes/manifests/kube-apiserver.yaml ``` and look for the line ```--tls-cert-file ```.

**/etc/kubernetes/pki/apiserver.crt**

```bash
cat /etc/kubernetes/manifests//kube-apiserver.yaml | grep tls-cert-file
    - --tls-cert-file=/etc/kubernetes/pki/apiserver.crt
```

2. Identify the Certificate file used to authenticate kube-apiserver as a client to ETCD Server.

**Run** the command ``` cat /etc/kubernetes/manifests/kube-apiserver.yaml ``` and look for value of etcd-certfile flag.

**/etc/kubernetes/pki/apiserver-etcd-client.crt**

```bash
cat /etc/kubernetes/manifests/kube-apiserver.yaml | grep etcd-certfile
    - --etcd-certfile=/etc/kubernetes/pki/apiserver-etcd-client.crt
```

3. Identify the key used to authenticate kubeapi-server to the kubelet server.

Look for ``` kubelet-client-key ``` option in the file ``` /etc/kubernetes/manifests/kube-apiserver.yaml ```.

**/etc/kubernetes/pki/apiserver-kubelet-client.key**

```bash
cat /etc/kubernetes/manifests/kube-apiserver.yaml | grep kubelet-client-key
    - --kubelet-client-key=/etc/kubernetes/pki/apiserver-kubelet-client.key
```

4. Identify the ETCD Server Certificate used to host ETCD server.

Look for cert-file option in the file ```/etc/kubernetes/manifests/etcd.yaml ```.

**/etc/kubernetes/pki/etcd/server.crt**

```bash
 cat /etc/kubernetes/manifests/etcd.yaml | grep cert-file
    - --cert-file=/etc/kubernetes/pki/etcd/server.crt
    - --peer-cert-file=/etc/kubernetes/pki/etcd/peer.crt
```

5. Identify the ETCD Server CA Root Certificate used to serve ETCD Server.

ETCD can have its own CA. So this may be a different CA certificate than the one used by kube-api server.

Look for CA Certificate (trusted-ca-file) in file ``` /etc/kubernetes/manifests/etcd.yaml ```.

**/etc/kubernetes/pki/etcd/ca.crt**

```bash
cat /etc/kubernetes/manifests/etcd.yaml | grep trusted-ca-file
    - --peer-trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
    - --trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
```

6. What is the Common Name (CN) configured on the Kube API Server Certificate?

OpenSSL Syntax: openssl x509 -in file-path.crt -text -noout

**Run** the command ``` openssl x509 -in /etc/kubernetes/pki/apiserver.crt -text ``` and look for Subject CN.

**Subject: CN = kube-apiserver**

```bash
controlplane ~ ➜  openssl x509 -in /etc/kubernetes/pki/apiserver.crt  -text
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number: 2517624713961670767 (0x22f06656a16e086f)
        Signature Algorithm: sha256WithRSAEncryption
        Issuer: CN = kubernetes
        Validity
            Not Before: Aug 25 15:11:50 2025 GMT
            Not After : Aug 25 15:16:50 2026 GMT
        Subject: CN = kube-apiserver
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (2048 bit)
                Modulus:
                    00:cf:12:d7:2d:d3:6f:3d:4c:c8:ef:6b:05:37:f5:
...
```

7. What is the name of the CA who issued the Kube API Server Certificate?

Run the command ``` openssl x509 -in /etc/kubernetes/pki/apiserver.crt -text ``` and look for issuer.

**Issuer: CN = kubernetes**

```bash
 Signature Algorithm: sha256WithRSAEncryption
        Issuer: CN = kubernetes
```

8. Which of the below alternate names is not configured on the Kube API Server Certificate?

**Run** the command ``` openssl x509 -in /etc/kubernetes/pki/apiserver.crt -text ``` and look at Alternative 
Names.

**kube-master**

```bash
X509v3 Subject Alternative Name: 
                DNS:controlplane, DNS:kubernetes, DNS:kubernetes.default, DNS:kubernetes.default.svc, DNS:kubernetes.default.svc.cluster.local, IP Address:172.20.0.1, IP Address:192.168.138.196
    Signature Algorithm: sha256WithRSAEncryption
    Signature Value:
```

9. What is the Common Name (CN) configured on the ETCD Server certificate?

**Run** the command ``` openssl x509 -in /etc/kubernetes/pki/etcd/server.crt -text ``` and look for Subject CN.

**controlplane**

```bash
openssl x509 -in /etc/kubernetes/pki/etcd/server.crt  -text
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number: 2554436549048519265 (0x23732e81e34fca61)
        Signature Algorithm: sha256WithRSAEncryption
        Issuer: CN = etcd-ca
        Validity
            Not Before: Aug 25 15:11:50 2025 GMT
            Not After : Aug 25 15:16:50 2026 GMT
        Subject: CN = controlplane
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (2048 bit)
```

10. How long, from the issued date, is the Kube-API Server Certificate valid for?

File:  ``` /etc/kubernetes/pki/apiserver.crt ```

**1 year**

11. How long, from the issued date, is the Root CA Certificate valid for?

File: ``` /etc/kubernetes/pki/ca.crt ```

12. How long, from the issued date, is the Root CA Certificate valid for?

File: /etc/kubernetes/pki/ca.crt


Run the command openssl x509 -in /etc/kubernetes/pki/ca.crt -text and look for the validity.

**10 years**

13. Kubectl suddenly stops responding to your commands. Check it out! Someone recently modified the  ``` /etc/kubernetes/manifests/etcd.yaml ``` file

You are asked to investigate and fix the issue. Once you fix the issue wait for sometime for kubectl to respond. Check the logs of the ETCD container.

Inspect the --cert-file option in the manifests file.

**Solution**

The certificate file used here is incorrect. It is set to ``` /etc/kubernetes/pki/etcd/server-certificate.crt ``` which does not exist. As we saw in the previous questions the correct path should be ``` /etc/kubernetes/pki/etcd/server.crt ```.

```bash
root@controlplane:~# ls -l /etc/kubernetes/pki/etcd/server* | grep .crt
-rw-r--r-- 1 root root 1188 May 20 00:41 /etc/kubernetes/pki/etcd/server.crt
root@controlplane:~# 
```

**Update** the YAML file with the correct certificate path and wait for the ETCD pod to be recreated. wait for the kube-apiserver to get to a Ready state.

NOTE: It may take a few minutes for the kubectl commands to work again so please be patient.






