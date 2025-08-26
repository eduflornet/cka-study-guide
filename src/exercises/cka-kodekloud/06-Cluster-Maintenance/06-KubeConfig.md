# KubeConfig

The more that you read, the more things you will know. The more that you learn, the more places you'll go.

– Dr. Seuss

1. Where is the default kubeconfig file located in the current environment?

Find the current home directory by looking at the HOME environment variable.

**Use** the command ``` ls -a ``` and look for the kube config file under ``` /root/.kube ```.

2. How many clusters are defined in the default kubeconfig file?

One = **https://controlplane:6443**

```bash
cat .kube/config 
```

```yaml
server: https://controlplane:6443
  name: kubernetes
contexts:
- context:
    cluster: kubernetes
    user: kubernetes-admin
  name: kubernetes-admin@kubernetes
current-context: kuberne
```

3. How many Users are defined in the default kubeconfig file?

One = **kubernetes-admin**

```yaml
server: https://controlplane:6443
  name: kubernetes
contexts:
- context:
    cluster: kubernetes
    user: kubernetes-admin
  name: kubernetes-admin@kubernetes
current-context: kubernetes-admin@kubernetes
kind: Config
preferences: {}
users:
- name: kubernetes-admin
  user:
    client-certificate-data:
```

4. How many contexts are defined in the default kubeconfig file?

One = **current-context: kubernetes-admin@kubernetes**

```yaml
contexts:
- context:
    cluster: kubernetes
    user: kubernetes-admin
  name: kubernetes-admin@kubernetes
current-context: kubernetes-admin@kubernetes
kind: Config
preferences: {}
users:
- name: kubernetes-admin
  user:
    client-certificate-data:
```

5. What is the user configured in the current context?

**kubernetes-admin**

6. What is the name of the cluster configured in the default kubeconfig file?

**kubernetes**

7. A new kubeconfig file named my-kube-config is created. It is placed in the ``` /root directory ```. How many clusters are defined in that kubeconfig file?

**Four** = production, development, kubernetes-on-ws, test-cluster-1

```yaml
clusters:
- name: production
  cluster:
    certificate-authority: /etc/kubernetes/pki/ca.crt
    server: https://controlplane:6443

- name: development
  cluster:
    certificate-authority: /etc/kubernetes/pki/ca.crt
    server: https://controlplane:6443

- name: kubernetes-on-aws
  cluster:
    certificate-authority: /etc/kubernetes/pki/ca.crt
    server: https://controlplane:6443

- name: test-cluster-1
  cluster:
    certificate-authority: /etc/kubernetes/pki/ca.crt
    server: https://controlplane:6443
```

8. How many contexts are configured in the my-kube-config file?

**Four** = test-user@development, aws-user@kubernetes-on-ws, test-user@production, research

```yaml
contexts:
- name: test-user@development
  context:
    cluster: development
    user: test-user

- name: aws-user@kubernetes-on-aws
  context:
    cluster: kubernetes-on-aws
    user: aws-user

- name: test-user@production
  context:
    cluster: production
    user: test-user

- name: research
  context:
    cluster: test-cluster-1
    user: dev-user
```

9. What user is configured in the research context?

**dev-user**

10. What is the name of the client-certificate file configured for the aws-user?

**aws-user.crt**

```yaml
users:
- name: test-user
  user:
    client-certificate: /etc/kubernetes/pki/users/test-user/test-user.crt
    client-key: /etc/kubernetes/pki/users/test-user/test-user.key
- name: dev-user
  user:
    client-certificate: /etc/kubernetes/pki/users/dev-user/developer-user.crt
    client-key: /etc/kubernetes/pki/users/dev-user/dev-user.key
- name: aws-user
  user:
    client-certificate: /etc/kubernetes/pki/users/aws-user/aws-user.crt
    client-key: /etc/kubernetes/pki/users/aws-user/aws-user.key

current-context: test-user@development
preferences: {}
```

11. What is the current context set to in the my-kube-config file?

**current-context: test-user@development**

12. I would like to use the dev-user to access test-cluster-1. Set the current context to the right one so I can do that.


Once the right context is identified, use the ``` kubectl config use-context ``` command.

To use that context, run the command: 

```bash
kubectl config --kubeconfig=/root/my-kube-config use-context research 
```

To know the current context, run the command: 

```bash
kubectl config --kubeconfig=/root/my-kube-config current-context 
```

13. We don't want to specify the kubeconfig file option on each kubectl command.

Set the my-kube-config file as the default kubeconfig file and make it persistent across all sessions without overwriting the existing ``` ~/.kube/config ```. Ensure any configuration changes persist across reboots and new shell sessions.


Note: Don't forget to source the configuration file to take effect in the existing session. Example:

``` source ~/.bashrc ```

- Default kubeconfig file configured

- Persistent across sessions

**The solution** should set the KUBECONFIG environment variable to point to your kubeconfig file. This can be done by adding an export statement to your shell configuration file (like ~/.bashrc).

Add the my-kube-config file to the KUBECONFIG environment variable.

Open your shell configuration file:

```bash
vi ~/.bashrc
```

Add one of these lines to export the variable:

```bash
export KUBECONFIG=/root/my-kube-config
```
# OR
```bash
export KUBECONFIG=~/my-kube-config
```
# OR
```bash
export KUBECONFIG=$HOME/my-kube-config
```
Apply the Changes:

Reload the shell configuration to apply the changes in the current session:

```bash
source ~/.bashrc
```

14. With the current-context set to research, we are trying to access the cluster. However something seems to be wrong. Identify and fix the issue.


Try running the ``` kubectl get pods ``` command and look for the error. All users certificates are stored at ``` /etc/kubernetes/pki/users ```.

The path to certificate is incorrect in the kubeconfig file. Correct the certificate name which is available at ``` /etc/kubernetes/pki/users/ ```.


Before the fix , You will notice you cant get the pods in the cluster :

```bash
controlplane ~ ➜  kubectl get pods
error: unable to read client-cert /etc/kubernetes/pki/users/dev-user/developer-user.crt for dev-user due to open /etc/kubernetes/pki/users/dev-user/developer-user.crt: no such file or directory
Solution steps:
```

**Identify the Current Certificate Path**

```bash
kubectl config view --kubeconfig=/root/my-kube-config | grep -A5 "name: dev-user"

- name: dev-user
  user:
    client-certificate: /etc/kubernetes/pki/users/dev-user/developer-user.crt
    client-key: /etc/kubernetes/pki/users/dev-user/dev-user.key
- name: test-user
  user:

```

**Verify the Actual Certificate Location**

```bash
ls -l /etc/kubernetes/pki/users/dev-user/
```

Edit the Kubeconfig File

```bash
kubectl config set-credentials dev-user \
  --client-certificate=/etc/kubernetes/pki/users/dev-user/dev-user.crt \
  --client-key=/etc/kubernetes/pki/users/dev-user/dev-user.key \
  --kubeconfig=/root/my-kube-config
```

**Test Cluster Access**

```bash
controlplane ~ ➜  kubectl get pods
No resources found in default namespace.
```


