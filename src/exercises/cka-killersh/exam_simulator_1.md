# CKA Simulator A Kubernetes 1.32

[https://killer.sh](https://killer.sh/)

In the real exam each question will be solved on a different instance
whereas in the simulator multiple questions will be solved on same
instances.

Use `sudo -i`{.copy-on-click} to become root on any node in case
necessary.

## Question 1

Solve this question on: `ssh cka9412`

You\'re asked to extract the following information out of kubeconfig
file `/opt/course/1/kubeconfig`on `cka9412`:

1.  Write all kubeconfig context names into `/opt/course/1/contexts`, one per line

2.  Write the name of the current context into `/opt/course/1/current-context`

3.  Write the client-certificate of user `account-0027` base64-decoded into `/opt/course/1/cert`

##### Answer:

All that\'s asked for here could be extracted by manually reading the
kubeconfig file. But we\'re going to use kubectl for it.

###### Step 1

```sh
➜ ssh cka9412

➜ candidate@cka9412:~$ k --kubeconfig /opt/course/1/kubeconfig config get-contexts
CURRENT   NAME            CLUSTER      AUTHINFO                NAMESPACE
          cluster-admin   kubernetes   admin@internal          
          cluster-w100    kubernetes   account-0027@internal   
          cluster-w200    kubernetes   account-0028@internal  

```

```sh
➜ candidate@cka9412:~$ k --kubeconfig /opt/course/1/kubeconfig config get-contexts -oname
cluster-admin
cluster-w100
cluster-w200
```

```sh
➜ candidate@cka9412:~$ k --kubeconfig /opt/course/1/kubeconfig config get-contexts -oname > /opt/course/1/contexts
```

This will result in:

```sh
# cka9412:/opt/course/1/contexts
cluster-admin
cluster-w100
cluster-w200
```

We could also do extractions using jsonpath:

```sh
k --kubeconfig /opt/course/1/kubeconfig config view -o yaml
k --kubeconfig /opt/course/1/kubeconfig config view -o jsonpath="{.contexts[*].name}"
```

But it would probably be overkill for this task.
 
###### Step 2

And finally we extract the certificate and write it base64 decoded into the required location:

```sh
➜ candidate@cka9412:~$ k --kubeconfig /opt/course/1/kubeconfig config view -o yaml --raw
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tC...
    server: https://10.30.110.30:6443
  name: cluster1
contexts:
- context:
    cluster: kubernetes
    user: admin@internal
  name: cluster-admin
- context:
    cluster: kubernetes
    user: account-0027@internal
  name: cluster-w100
- context:
    cluster: kubernetes
    user: account-0028@internal
  name: cluster-w200
current-context: cluster-w200
kind: Config
preferences: {}
users:
- name: account-0027@internal
  user:
    client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN2RENDQWFRQ0ZIWWRqU1pGS0N5VUNSMUIybmFYQ2cvVWpTSExNQTBHQ1NxR1NJYjNEUUVCQ3dVQU1CVXgKRXpBUkJnTlZCQU1UQ210MVltVnlibVYwWlhNd0hoY05NalF4TURJNE1Ua3dPVFV3V2hjTk1qWXdNekV5TVRrdwpPVFV3V2pBZ01SNHdIQVlEVlFRRERCVmhZMk52ZFc1MExUQXdNamRBYVc1MFpYSnVZV3d3Z2dFaU1BMEdDU3FHClNJYjNEUUVCQVFVQUE0SUJEd0F3Z2dFS0FvSUJBUURwVXNRRERFVys0OEF2Wm1LYktTMndtc1phMGd5K2t6aWkKY1pEcFpnOG1nTys1MGpObkhRNElDcUFqRzNmRkhtUG5idWowc1pHWGYreW0wSjJkVkw5andHU3Q1TlZvTHJqagpUd2xCRzYzK2s0alJCeExCdjY0NzlpUFhYazBnaVAzOFRBb1MvL2R0SitPOGlzYlJNbmxiOWFJNUwySll4SGZOClZMMnFyRjlhckxmMUROK2gwaGF2RnhuOW5vSi9pWngvcWIvRkhnZVpxblRmN3pSNk9vdVJ1V0hHNTIzam5UcUEKMDZLK2c0azJvNmhnM3U3Sk0vY05iSEZNN1MycVNCRGtTMjY2Skp0dk10QytjcHNtZy9lVW5EaEEyMXRUYTR2ZwpsYnB3NnZ4bkpjd010NG4wS2FBZVMwajRMM09DODY5YWxweTFqdkkzQVRqRmp1Y2tMRVNMQWdNQkFBRXdEUVlKCktvWklodmNOQVFFTEJRQURnZ0VCQURRUUxHWVpvVVNyYnBnRlY2OXNIdk11b3huMllVdDFCNUZCbVFyeHdPbGkKZGVtOTM2cTJaTE1yMzRyUTVyQzF1VFFEcmFXWGE0NHlIbVZaMDd0ZElOa1Yydm9JZXhIalg5MWdWQytMaXJRcQpJS0d4aW9rOUNLTEU3TlJlRjYzcHAvN0JOZTcvUDZjT1JoME8yRURNNFRnSFhMcFhydDd0ZFBFWHd2ck4xdE1RCno1YXY5UG81VGQ0VmYwcGFPRHRsYWh3aElaNks3Y3RnVkdUMUtkUWxuMXFYRGIvVndxM1Z5WUJBSktsbU91OWwKYmozbm12YzdEOTllOXA0eTRHRkNrQWxieHY5VEQwVDR5dllnVkZ0UlRWYkdBa21hendVSHJmY1FuUlRWZktvegpTZnNZUnk2TDFSS3hqd2g3NEtuaEtKeiswOUpxWHByN01WZFFnamgwUmR3PQotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==
    client-key-data: LS0tLS1CRUdJTiBSU0EgUFJJVkFURSBLRVk...
...
```

###### Step 3

And finally we extract the certificate and write it base64 decoded into
the required location:

```sh
➜ candidate@cka9412:~$ k --kubeconfig /opt/course/1/kubeconfig config view -o yaml --raw
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tC...
    server: https://10.30.110.30:6443
  name: cluster1
contexts:
- context:
    cluster: kubernetes
    user: admin@internal
  name: cluster-admin
- context:
    cluster: kubernetes
    user: account-0027@internal
  name: cluster-w100
- context:
    cluster: kubernetes
    user: account-0028@internal
  name: cluster-w200
current-context: cluster-w200
kind: Config
preferences: {}
users:
- name: account-0027@internal
  user:
    client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN2RENDQWFRQ0ZIWWRqU1pGS0N5VUNSMUIybmFYQ2cvVWpTSExNQTBHQ1NxR1NJYjNEUUVCQ3dVQU1CVXgKRXpBUkJnTlZCQU1UQ210MVltVnlibVYwWlhNd0hoY05NalF4TURJNE1Ua3dPVFV3V2hjTk1qWXdNekV5TVRrdwpPVFV3V2pBZ01SNHdIQVlEVlFRRERCVmhZMk52ZFc1MExUQXdNamRBYVc1MFpYSnVZV3d3Z2dFaU1BMEdDU3FHClNJYjNEUUVCQVFVQUE0SUJEd0F3Z2dFS0FvSUJBUURwVXNRRERFVys0OEF2Wm1LYktTMndtc1phMGd5K2t6aWkKY1pEcFpnOG1nTys1MGpObkhRNElDcUFqRzNmRkhtUG5idWowc1pHWGYreW0wSjJkVkw5andHU3Q1TlZvTHJqagpUd2xCRzYzK2s0alJCeExCdjY0NzlpUFhYazBnaVAzOFRBb1MvL2R0SitPOGlzYlJNbmxiOWFJNUwySll4SGZOClZMMnFyRjlhckxmMUROK2gwaGF2RnhuOW5vSi9pWngvcWIvRkhnZVpxblRmN3pSNk9vdVJ1V0hHNTIzam5UcUEKMDZLK2c0azJvNmhnM3U3Sk0vY05iSEZNN1MycVNCRGtTMjY2Skp0dk10QytjcHNtZy9lVW5EaEEyMXRUYTR2ZwpsYnB3NnZ4bkpjd010NG4wS2FBZVMwajRMM09DODY5YWxweTFqdkkzQVRqRmp1Y2tMRVNMQWdNQkFBRXdEUVlKCktvWklodmNOQVFFTEJRQURnZ0VCQURRUUxHWVpvVVNyYnBnRlY2OXNIdk11b3huMllVdDFCNUZCbVFyeHdPbGkKZGVtOTM2cTJaTE1yMzRyUTVyQzF1VFFEcmFXWGE0NHlIbVZaMDd0ZElOa1Yydm9JZXhIalg5MWdWQytMaXJRcQpJS0d4aW9rOUNLTEU3TlJlRjYzcHAvN0JOZTcvUDZjT1JoME8yRURNNFRnSFhMcFhydDd0ZFBFWHd2ck4xdE1RCno1YXY5UG81VGQ0VmYwcGFPRHRsYWh3aElaNks3Y3RnVkdUMUtkUWxuMXFYRGIvVndxM1Z5WUJBSktsbU91OWwKYmozbm12YzdEOTllOXA0eTRHRkNrQWxieHY5VEQwVDR5dllnVkZ0UlRWYkdBa21hendVSHJmY1FuUlRWZktvegpTZnNZUnk2TDFSS3hqd2g3NEtuaEtKeiswOUpxWHByN01WZFFnamgwUmR3PQotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==
    client-key-data: LS0tLS1CRUdJTiBSU0EgUFJJVkFURSBLRVk...
``` 

Instead of using --raw to see the sensitive certificate information, we could also simply open the kubeconfig file in an editor. No matter how, we copy the whole value of client-certificate-data and base64 decode it:

```sh
➜ candidate@cka9412:~$ echo LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN2RE... | base64 -d > /opt/course/1/cert
```

Or if we like it automated:

```sh
➜ candidate@cka9412:~$ k --kubeconfig /opt/course/1/kubeconfig config view --raw -ojsonpath="{.users[0].user.client-certificate-data}" | base64 -d > /opt/course/1/cert
```

Which will result in:

```sh
# cka9412:/opt/course/1/cert
-----BEGIN CERTIFICATE-----
MIICvDCCAaQCFHYdjSZFKCyUCR1B2naXCg/UjSHLMA0GCSqGSIb3DQEBCwUAMBUx
EzARBgNVBAMTCmt1YmVybmV0ZXMwHhcNMjQxMDI4MTkwOTUwWhcNMjYwMzEyMTkw
OTUwWjAgMR4wHAYDVQQDDBVhY2NvdW50LTAwMjdAaW50ZXJuYWwwggEiMA0GCSqG
SIb3DQEBAQUAA4IBDwAwggEKAoIBAQDpUsQDDEW+48AvZmKbKS2wmsZa0gy+kzii
cZDpZg8mgO+50jNnHQ4ICqAjG3fFHmPnbuj0sZGXf+ym0J2dVL9jwGSt5NVoLrjj
TwlBG63+k4jRBxLBv6479iPXXk0giP38TAoS//dtJ+O8isbRMnlb9aI5L2JYxHfN
VL2qrF9arLf1DN+h0havFxn9noJ/iZx/qb/FHgeZqnTf7zR6OouRuWHG523jnTqA
06K+g4k2o6hg3u7JM/cNbHFM7S2qSBDkS266JJtvMtC+cpsmg/eUnDhA21tTa4vg
lbpw6vxnJcwMt4n0KaAeS0j4L3OC869alpy1jvI3ATjFjuckLESLAgMBAAEwDQYJ
KoZIhvcNAQELBQADggEBADQQLGYZoUSrbpgFV69sHvMuoxn2YUt1B5FBmQrxwOli
dem936q2ZLMr34rQ5rC1uTQDraWXa44yHmVZ07tdINkV2voIexHjX91gVC+LirQq
IKGxiok9CKLE7NReF63pp/7BNe7/P6cORh0O2EDM4TgHXLpXrt7tdPEXwvrN1tMQ
z5av9Po5Td4Vf0paODtlahwhIZ6K7ctgVGT1KdQln1qXDb/Vwq3VyYBAJKlmOu9l
bj3nmvc7D99e9p4y4GFCkAlbxv9TD0T4yvYgVFtRTVbGAkmazwUHrfcQnRTVfKoz
SfsYRy6L1RKxjwh74KnhKJz+09JqXpr7MVdQgjh0Rdw=
-----END CERTIFICATE-----
```
Task completed.

Solve this question on: `ssh cka7968`

 
Install the MinIO Operator using Helm in *Namespace* `minio`. Then configure and create the *Tenant* CRD:

1.  Create *Namespace* `minio`

2.  Install Helm chart `minio/operator` into the new *Namespace*. The Helm Release should be called
    `minio-operator`

3.  Update the `Tenant` resource in `/opt/course/2/minio-tenant.yaml` to include `enableSFTP: true` under `features`

4.  Create the `Tenant` resource from `/opt/course/2/minio-tenant.yaml`

> ℹ️ It is not required for MinIO to run properly. Installing the Helm
> Chart and the *Tenant* resource as requested is enough

 

##### Answer:

*Helm Chart*: Kubernetes YAML template-files combined into a single package, *Values* allow customisation

*Helm Release*: Installed instance of a *Chart*

*Helm Values*: Allow to customise the YAML template-files in a *Chart* when creating a *Release*

*Operator*: *Pod* that communicates with the Kubernetes API and might work with `CRDs`

*CRD*: Custom Resources are extensions of the Kubernetes API

###### Step 1 {step-1-2}

First we create the requested *Namespace* `minio`:

```sh
➜ ssh cka7968

➜ candidate@cka7968:~$ k create ns minio
namespace/minio created
```

###### Step 2 {step-2-2}

Now we install the MinIO Helm chart into it and name the release `minio-operator`:

```sh
➜ candidate@cka7968:~$ helm repo list
NAME    URL                  
minio   http://localhost:6000

➜ candidate@cka7968:~$ helm search repo
NAME            CHART VERSION   APP VERSION     DESCRIPTION                    
minio/operator  6.0.4           v6.0.4          A Helm chart for MinIO Operator

➜ candidate@cka7968:~$ helm -n minio install minio-operator minio/operator
NAME: minio-operator
LAST DEPLOYED: Sun Dec 22 17:04:37 2024
NAMESPACE: minio
STATUS: deployed
REVISION: 1
TEST SUITE: None

➜ candidate@cka7968:~$ helm -n minio ls
NAME            NAMESPACE   REVISION  ...  STATUS     CHART           APP VERSION
minio-operator  minio       1         ...  deployed   operator-6.0.4  v6.0.4   

➜ candidate@cka7968:~$ k -n minio get pod
NAME                              READY   STATUS    RESTARTS   AGE
minio-operator-7b595f559d-5hrj5   1/1     Running   0          24s
minio-operator-7b595f559d-sl22g   1/1     Running   0          25s
```

Because we installed the Helm chart there are now some CRDs available:

```sh
➜ candidate@cka7968:~$ k get crd
NAME                        CREATED AT
miniojobs.job.min.io        2024-12-22T17:04:38Z
policybindings.sts.min.io   2024-12-22T17:04:38Z
tenants.minio.min.io        2024-12-22T17:04:38Z
```



###### Step 3 {step-3-2}

We need to update the Yaml in the file which creates a *Tenant* resource:



We can see available fields for `features`{.copy-on-click} like this:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term" style="break-inside: unset;"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka7968:~$ k describe crd tenant | grep -i feature -A 20
```
:::

``` {.CodeMirror-line role="presentation"}
              Features:
```

``` {.CodeMirror-line role="presentation"}
                Properties:
```

``` {.CodeMirror-line role="presentation"}
                  Bucket DNS:
```

``` {.CodeMirror-line role="presentation"}
                    Type:  boolean
```

``` {.CodeMirror-line role="presentation"}
                  Domains:
```

``` {.CodeMirror-line role="presentation"}
                    Properties:
```

``` {.CodeMirror-line role="presentation"}
                      Console:
```

``` {.CodeMirror-line role="presentation"}
                        Type:  string
```

``` {.CodeMirror-line role="presentation"}
                      Minio:
```

``` {.CodeMirror-line role="presentation"}
                        Items:
```

``` {.CodeMirror-line role="presentation"}
                          Type:  string
```

``` {.CodeMirror-line role="presentation"}
                        Type:    array
```

``` {.CodeMirror-line role="presentation"}
                    Type:        object
```

``` {.CodeMirror-line role="presentation"}
                  Enable SFTP:
```

``` {.CodeMirror-line role="presentation"}
                    Type:  boolean
```

``` {.CodeMirror-line role="presentation"}
                Type:      object
```

``` {.CodeMirror-line role="presentation"}
              Image:
```

``` {.CodeMirror-line role="presentation"}
                Type:  string
```

``` {.CodeMirror-line role="presentation"}
              Image Pull Policy:
```

``` {.CodeMirror-line role="presentation"}
                Type:  string
```

``` {.CodeMirror-line role="presentation"}
              Image Pull Secret:
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 506px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 506px;"}
:::

 

###### Step 4

Finally we can create the *Tenant* resource:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka7968:~$ k -f /opt/course/2/minio-tenant.yaml apply
```
:::

``` {.CodeMirror-line role="presentation"}
tenant.minio.min.io/tenant created
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka7968:~$ k -n minio get tenant
```

``` {.CodeMirror-line role="presentation"}
NAME     STATE                      HEALTH   AGE
```

``` {.CodeMirror-line role="presentation"}
tenant   empty tenant credentials            21s
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 138px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 138px;"}
:::

In this scenario we installed an operator using Helm and created a *CRD*
with which that operator works. This is a common pattern in Kubernetes.

 

 

## Question 3 \| Scale down StatefulSet {#question-3-|-scale-down-statefulset}

 

Solve this question on: `ssh cka3962`{.copy-on-click}

 

There are two *Pods* named `o3db-*`{.copy-on-click} in *Namespace*
`project-h800`{.copy-on-click}. The Project H800 management asked you to
scale these down to one replica to save resources.

 

##### Answer: {#answer-3}

If we check the *Pods* we see two replicas:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ ssh cka3962
```
:::

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka3962:~$ k -n project-h800 get pod | grep o3db
```

``` {.CodeMirror-line role="presentation"}
o3db-0                                  1/1     Running   0          6d19h
```

``` {.CodeMirror-line role="presentation"}
o3db-1                                  1/1     Running   0          6d19h
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 115px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 115px;"}
:::

From their name it looks like these are managed by a *StatefulSet*. But
if we\'re unsure we could also check for the most common resources which
manage *Pods*:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka3962:~ k -n project-h800 get deploy,ds,sts | grep o3db
```
:::

``` {.CodeMirror-line role="presentation"}
statefulset.apps/o3db   2/2     6d19h
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 46px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 46px;"}
:::

Confirmed, we have to work with a *StatefulSet*. We could also look at
the *Pod* labels to find this out:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka3962:~ k -n project-h800 get pod --show-labels | grep o3db
```
:::

``` {.CodeMirror-line role="presentation"}
o3db-0                                  1/1     Running   0          6d19h   app=nginx,apps.kubernetes.io/pod-index=0,controller-revision-hash=o3db-5fbd4bb9cc,statefulset.kubernetes.io/pod-name=o3db-0
```

``` {.CodeMirror-line role="presentation"}
o3db-1                                  1/1     Running   0          6d19h   app=nginx,apps.kubernetes.io/pod-index=1,controller-revision-hash=o3db-5fbd4bb9cc,statefulset.kubernetes.io/pod-name=o3db-1
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 161px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 161px;"}
:::

To fulfil the task we simply run:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka3962:~ k -n project-h800 scale sts o3db --replicas 1
```
:::

``` {.CodeMirror-line role="presentation"}
statefulset.apps/o3db scaled
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka3962:~ k -n project-h800 get sts o3db
```

``` {.CodeMirror-line role="presentation"}
NAME   READY   AGE
```

``` {.CodeMirror-line role="presentation"}
o3db   1/1     6d19h
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 138px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 138px;"}
:::

The Project H800 management is happy again.

 

 

## Question 4 \| Find Pods first to be terminated {#question-4-|-find-pods-first-to-be-terminated}

 

Solve this question on: `ssh cka2556`{.copy-on-click}

 

Check all available *Pods* in the *Namespace*
`project-c13`{.copy-on-click} and find the names of those that would
probably be terminated first if the nodes run out of resources (cpu or
memory).

Write the *Pod* names into
`/opt/course/4/pods-terminated-first.txt`{.copy-on-click}.

 

##### Answer: {#answer-4}

When available cpu or memory resources on the nodes reach their limit,
Kubernetes will look for *Pods* that are using more resources than they
requested. These will be the first candidates for termination. If some
*Pods* containers have no resource requests/limits set, then by default
those are considered to use more than requested. Kubernetes assigns
Quality of Service classes to *Pods* based on the defined resources and
limits.

Hence we should look for *Pods* without resource requests defined, we
can do this with a manual approach:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ ssh cka2556
```
:::

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka2556:~$ k -n project-c13 describe pod | less -p Requests
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 69px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 69px;"}
:::

Or we do something like:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="bash"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
k -n project-c13 describe pod | grep -A 3 -E 'Requests|^Name:'
```
:::
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 23px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 23px;"}
:::

We see that the *Pods* of *Deployment*
`c13-3cc-runner-heavy`{.copy-on-click} don\'t have any resource requests
specified. Hence our answer would be:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang=""}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
# /opt/course/4/pods-terminated-first.txt
```
:::

``` {.CodeMirror-line role="presentation"}
c13-3cc-runner-heavy-65588d7d6-djtv9map
```

``` {.CodeMirror-line role="presentation"}
c13-3cc-runner-heavy-65588d7d6-v8kf5map
```

``` {.CodeMirror-line role="presentation"}
c13-3cc-runner-heavy-65588d7d6-wwpb4map
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 92px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 92px;"}
:::

 

###### Automatic way

Not necessary and probably too slow for this task, but to automate this
process you could use jsonpath:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term" style="break-inside: unset;"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka2556:~$ k -n project-c13 get pod -o jsonpath="{range .items[*]} {.metadata.name}{.spec.containers[*].resources}{'\n'}"
```
:::

``` {.CodeMirror-line role="presentation"}
 c13-2x3-api-c848b775d-7nggw{"requests":{"cpu":"50m","memory":"20Mi"}}
```

``` {.CodeMirror-line role="presentation"}
 c13-2x3-api-c848b775d-qrrlp{"requests":{"cpu":"50m","memory":"20Mi"}}
```

``` {.CodeMirror-line role="presentation"}
 c13-2x3-api-c848b775d-qtrs7{"requests":{"cpu":"50m","memory":"20Mi"}}
```

``` {.CodeMirror-line role="presentation"}
 c13-2x3-web-6989fb8dc6-4nc9z{"requests":{"cpu":"50m","memory":"10Mi"}}
```

``` {.CodeMirror-line role="presentation"}
 c13-2x3-web-6989fb8dc6-7xfdx{"requests":{"cpu":"50m","memory":"10Mi"}}
```

``` {.CodeMirror-line role="presentation"}
 c13-2x3-web-6989fb8dc6-98pr6{"requests":{"cpu":"50m","memory":"10Mi"}}
```

``` {.CodeMirror-line role="presentation"}
 c13-2x3-web-6989fb8dc6-9zpkj{"requests":{"cpu":"50m","memory":"10Mi"}}
```

``` {.CodeMirror-line role="presentation"}
 c13-2x3-web-6989fb8dc6-j2mgb{"requests":{"cpu":"50m","memory":"10Mi"}}
```

``` {.CodeMirror-line role="presentation"}
 c13-2x3-web-6989fb8dc6-jcwk9{"requests":{"cpu":"50m","memory":"10Mi"}}
```

``` {.CodeMirror-line role="presentation"}
 c13-3cc-data-96d47bf85-dc8d4{"requests":{"cpu":"30m","memory":"10Mi"}}
```

``` {.CodeMirror-line role="presentation"}
 c13-3cc-data-96d47bf85-f9gd2{"requests":{"cpu":"30m","memory":"10Mi"}}
```

``` {.CodeMirror-line role="presentation"}
 c13-3cc-data-96d47bf85-fd9lc{"requests":{"cpu":"30m","memory":"10Mi"}}
```

``` {.CodeMirror-line role="presentation"}
 c13-3cc-runner-heavy-8687d66dbb-gnxjh{}
```

``` {.CodeMirror-line role="presentation"}
 c13-3cc-runner-heavy-8687d66dbb-przdh{}
```

``` {.CodeMirror-line role="presentation"}
 c13-3cc-runner-heavy-8687d66dbb-wqwfz{}
```

``` {.CodeMirror-line role="presentation"}
 c13-3cc-web-767b98dd48-5b45q{"requests":{"cpu":"50m","memory":"10Mi"}}
```

``` {.CodeMirror-line role="presentation"}
 c13-3cc-web-767b98dd48-5vldf{"requests":{"cpu":"50m","memory":"10Mi"}}
```

``` {.CodeMirror-line role="presentation"}
 c13-3cc-web-767b98dd48-dd7mc{"requests":{"cpu":"50m","memory":"10Mi"}}
```

``` {.CodeMirror-line role="presentation"}
 c13-3cc-web-767b98dd48-pb67p{"requests":{"cpu":"50m","memory":"10Mi"}}
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 483px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 483px;"}
:::

This lists all *Pod* names and their requests/limits, hence we see the
three *Pods* without those defined.

Or we look for the Quality of Service classes:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term" style="break-inside: unset;"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka2556:~$ k get pods -n project-c13 -o jsonpath="{range .items[*]}{.metadata.name} {.status.qosClass}{'\n'}"
```
:::

``` {.CodeMirror-line role="presentation"}
c13-2x3-api-c848b775d-7nggw Burstable
```

``` {.CodeMirror-line role="presentation"}
c13-2x3-api-c848b775d-qrrlp Burstable
```

``` {.CodeMirror-line role="presentation"}
c13-2x3-api-c848b775d-qtrs7 Burstable
```

``` {.CodeMirror-line role="presentation"}
c13-2x3-web-6989fb8dc6-4nc9z Burstable
```

``` {.CodeMirror-line role="presentation"}
c13-2x3-web-6989fb8dc6-7xfdx Burstable
```

``` {.CodeMirror-line role="presentation"}
c13-2x3-web-6989fb8dc6-98pr6 Burstable
```

``` {.CodeMirror-line role="presentation"}
c13-2x3-web-6989fb8dc6-9zpkj Burstable
```

``` {.CodeMirror-line role="presentation"}
c13-2x3-web-6989fb8dc6-j2mgb Burstable
```

``` {.CodeMirror-line role="presentation"}
c13-2x3-web-6989fb8dc6-jcwk9 Burstable
```

``` {.CodeMirror-line role="presentation"}
c13-3cc-data-96d47bf85-dc8d4 Burstable
```

``` {.CodeMirror-line role="presentation"}
c13-3cc-data-96d47bf85-f9gd2 Burstable
```

``` {.CodeMirror-line role="presentation"}
c13-3cc-data-96d47bf85-fd9lc Burstable
```

``` {.CodeMirror-line role="presentation"}
c13-3cc-runner-heavy-8687d66dbb-gnxjh BestEffort
```

``` {.CodeMirror-line role="presentation"}
c13-3cc-runner-heavy-8687d66dbb-przdh BestEffort
```

``` {.CodeMirror-line role="presentation"}
c13-3cc-runner-heavy-8687d66dbb-wqwfz BestEffort
```

``` {.CodeMirror-line role="presentation"}
c13-3cc-web-767b98dd48-5b45q Burstable
```

``` {.CodeMirror-line role="presentation"}
c13-3cc-web-767b98dd48-5vldf Burstable
```

``` {.CodeMirror-line role="presentation"}
c13-3cc-web-767b98dd48-dd7mc Burstable
```

``` {.CodeMirror-line role="presentation"}
c13-3cc-web-767b98dd48-pb67p Burstable
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 483px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 483px;"}
:::

Here we see three with BestEffort, which *Pods* get that don\'t have any
memory or cpu limits or requests defined.

A good practice is to always set resource requests and limits. If you
don\'t know the values your containers should have you can find this out
using metric tools like Prometheus. You can also use
`kubectl top pod`{.copy-on-click} or even `kubectl exec`{.copy-on-click}
into the container and use `top`{.copy-on-click} and similar tools.

 

 

## Question 5 \| Kustomize configure HPA Autoscaler {#question-5-|-kustomize-configure-hpa-autoscaler}

 

Solve this question on: `ssh cka5774`{.copy-on-click}

 

Previously the application `api-gateway`{.copy-on-click} used some
external autoscaler which should now be replaced with a
*HorizontalPodAutoscaler* (*HPA*). The application has been deployed to
*Namespaces* `api-gateway-staging`{.copy-on-click} and
`api-gateway-prod`{.copy-on-click} like this:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang=""}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
kubectl kustomize /opt/course/5/api-gateway/staging | kubectl apply -f -
```
:::

``` {.CodeMirror-line role="presentation"}
kubectl kustomize /opt/course/5/api-gateway/prod | kubectl apply -f -
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 46px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 46px;"}
:::

Using the Kustomize config at
`/opt/course/5/api-gateway`{.copy-on-click} do the following:

1.  Remove the *ConfigMap* `horizontal-scaling-config`{.copy-on-click}
    completely

2.  Add *HPA* named `api-gateway`{.copy-on-click} for the *Deployment*
    `api-gateway`{.copy-on-click} with min `2`{.copy-on-click} and max
    `4`{.copy-on-click} replicas. It should scale at
    `50%`{.copy-on-click} average CPU utilisation

3.  In prod the *HPA* should have max `6`{.copy-on-click} replicas

4.  Apply your changes for staging and prod so they\'re reflected in the
    cluster

 

##### Answer {#answer-5}

Kustomize is a standalone tool to manage K8s Yaml files, but it also
comes included with kubectl. The common idea is to have a base set of
K8s Yaml and then override or extend it for different overlays, like
here done for staging and prod:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ ssh cka5774
```
:::

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka5774:~$ cd /opt/course/5/api-gateway
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka5774:/opt/course/5/api-gateway$ ls
```

``` {.CodeMirror-line role="presentation"}
base  prod  staging
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 138px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 138px;"}
:::

 

###### Investigate Base

Let\'s investigate the base first for better understanding:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term" style="break-inside: unset;"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka5774:/opt/course/5/api-gateway$ k kustomize base
```
:::

``` {.CodeMirror-line role="presentation"}
apiVersion: v1
```

``` {.CodeMirror-line role="presentation"}
kind: ServiceAccount
```

``` {.CodeMirror-line role="presentation"}
metadata:
```

``` {.CodeMirror-line role="presentation"}
  name: api-gateway
```

``` {.CodeMirror-line role="presentation"}
  namespace: NAMESPACE_REPLACE
```

``` {.CodeMirror-line role="presentation"}
---
```

``` {.CodeMirror-line role="presentation"}
apiVersion: v1
```

``` {.CodeMirror-line role="presentation"}
data:
```

``` {.CodeMirror-line role="presentation"}
  horizontal-scaling: "70"
```

``` {.CodeMirror-line role="presentation"}
kind: ConfigMap
```

``` {.CodeMirror-line role="presentation"}
metadata:
```

``` {.CodeMirror-line role="presentation"}
  name: horizontal-scaling-config
```

``` {.CodeMirror-line role="presentation"}
  namespace: NAMESPACE_REPLACE
```

``` {.CodeMirror-line role="presentation"}
---
```

``` {.CodeMirror-line role="presentation"}
apiVersion: apps/v1
```

``` {.CodeMirror-line role="presentation"}
kind: Deployment
```

``` {.CodeMirror-line role="presentation"}
metadata:
```

``` {.CodeMirror-line role="presentation"}
  name: api-gateway
```

``` {.CodeMirror-line role="presentation"}
  namespace: NAMESPACE_REPLACE
```

``` {.CodeMirror-line role="presentation"}
spec:
```

``` {.CodeMirror-line role="presentation"}
  replicas: 1
```

``` {.CodeMirror-line role="presentation"}
  selector:
```

``` {.CodeMirror-line role="presentation"}
    matchLabels:
```

``` {.CodeMirror-line role="presentation"}
      id: api-gateway
```

``` {.CodeMirror-line role="presentation"}
  template:
```

``` {.CodeMirror-line role="presentation"}
    metadata:
```

``` {.CodeMirror-line role="presentation"}
      labels:
```

``` {.CodeMirror-line role="presentation"}
        id: api-gateway
```

``` {.CodeMirror-line role="presentation"}
    spec:
```

``` {.CodeMirror-line role="presentation"}
      containers:
```

``` {.CodeMirror-line role="presentation"}
      - image: httpd:2-alpine
```

``` {.CodeMirror-line role="presentation"}
        name: httpd
```

``` {.CodeMirror-line role="presentation"}
      serviceAccountName: api-gateway
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 782px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 782px;"}
:::

Running `kubectl kustomize DIR`{.copy-on-click} will build the whole
Yaml based on whatever is defined in the
`kustomization.yaml`{.copy-on-click}.

In the case above we did build for the base directory, which produces
Yaml that is not expected to be deployed just like that. We can see for
example that all resources contain
`namespace: NAMESPACE_REPLACE`{.copy-on-click} entries which won\'t be
possible to apply because *Namespace* names need to be lowercase.

But for debugging it can be useful to build the base Yaml.

 

###### Investigate Staging

Now we look at the staging directory:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term" style="break-inside: unset;"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka5774:/opt/course/5/api-gateway$ k kustomize staging
```
:::

``` {.CodeMirror-line role="presentation"}
apiVersion: v1
```

``` {.CodeMirror-line role="presentation"}
kind: ServiceAccount
```

``` {.CodeMirror-line role="presentation"}
metadata:
```

``` {.CodeMirror-line role="presentation"}
  name: api-gateway
```

``` {.CodeMirror-line role="presentation"}
  namespace: api-gateway-staging
```

``` {.CodeMirror-line role="presentation"}
---
```

``` {.CodeMirror-line role="presentation"}
apiVersion: v1
```

``` {.CodeMirror-line role="presentation"}
data:
```

``` {.CodeMirror-line role="presentation"}
  horizontal-scaling: "60"
```

``` {.CodeMirror-line role="presentation"}
kind: ConfigMap
```

``` {.CodeMirror-line role="presentation"}
metadata:
```

``` {.CodeMirror-line role="presentation"}
  name: horizontal-scaling-config
```

``` {.CodeMirror-line role="presentation"}
  namespace: api-gateway-staging
```

``` {.CodeMirror-line role="presentation"}
---
```

``` {.CodeMirror-line role="presentation"}
apiVersion: apps/v1
```

``` {.CodeMirror-line role="presentation"}
kind: Deployment
```

``` {.CodeMirror-line role="presentation"}
metadata:
```

``` {.CodeMirror-line role="presentation"}
  labels:
```

``` {.CodeMirror-line role="presentation"}
    env: staging
```

``` {.CodeMirror-line role="presentation"}
  name: api-gateway
```

``` {.CodeMirror-line role="presentation"}
  namespace: api-gateway-staging
```

``` {.CodeMirror-line role="presentation"}
spec:
```

``` {.CodeMirror-line role="presentation"}
  replicas: 1
```

``` {.CodeMirror-line role="presentation"}
  selector:
```

``` {.CodeMirror-line role="presentation"}
    matchLabels:
```

``` {.CodeMirror-line role="presentation"}
      id: api-gateway
```

``` {.CodeMirror-line role="presentation"}
  template:
```

``` {.CodeMirror-line role="presentation"}
    metadata:
```

``` {.CodeMirror-line role="presentation"}
      labels:
```

``` {.CodeMirror-line role="presentation"}
        id: api-gateway
```

``` {.CodeMirror-line role="presentation"}
    spec:
```

``` {.CodeMirror-line role="presentation"}
      containers:
```

``` {.CodeMirror-line role="presentation"}
      - image: httpd:2-alpine
```

``` {.CodeMirror-line role="presentation"}
        name: httpd
```

``` {.CodeMirror-line role="presentation"}
      serviceAccountName: api-gateway
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 828px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 828px;"}
:::

We can see that all resources now have
`namespace: api-gateway-staging`{.copy-on-click}. Also staging seems to
change the *ConfigMap* value to
`horizontal-scaling: "60"`{.copy-on-click}. And it adds the additional
label `env: staging`{.copy-on-click} to the *Deployment*. The rest is
taken from base.

This all happens because of the `kustomization.yaml`{.copy-on-click}:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="yaml" style="break-inside: unset;"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
# cka5774:/opt/course/5/api-gateway/staging/kustomization.yaml
```
:::

``` {.CodeMirror-line role="presentation"}
apiVersion: kustomize.config.k8s.io/v1beta1
```

``` {.CodeMirror-line role="presentation"}
kind: Kustomization
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
resources:
```

``` {.CodeMirror-line role="presentation"}
  - ../base
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
patches:
```

``` {.CodeMirror-line role="presentation"}
  - path: api-gateway.yaml
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
transformers:
```

``` {.CodeMirror-line role="presentation"}
  - |-
```

``` {.CodeMirror-line role="presentation"}
    apiVersion: builtin
```

``` {.CodeMirror-line role="presentation"}
    kind: NamespaceTransformer
```

``` {.CodeMirror-line role="presentation"}
    metadata:
```

``` {.CodeMirror-line role="presentation"}
      name: notImportantHere
```

``` {.CodeMirror-line role="presentation"}
      namespace: api-gateway-staging
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 340px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 340px;"}
:::

-   The `resources:`{.copy-on-click} section is the directory on which
    everything will be based on

-   The `patches:`{.copy-on-click} section specifies Yaml files with
    alterations or additions applied on the base files

-   The `transformers:`{.copy-on-click} section in this case sets the
    *Namespace* for all resources

We should be able to build and deploy the staging Yaml:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka5774:/opt/course/5/api-gateway$ k kustomize staging | kubectl diff -f -
```
:::

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka5774:/opt/course/5/api-gateway$ k kustomize staging | kubectl apply -f -
```

``` {.CodeMirror-line role="presentation"}
serviceaccount/api-gateway unchanged
```

``` {.CodeMirror-line role="presentation"}
configmap/horizontal-scaling-config unchanged
```

``` {.CodeMirror-line role="presentation"}
deployment.apps/api-gateway unchanged
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 138px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 138px;"}
:::

Actually we see that no changes were performed, because everything is
already deployed:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka5774:/opt/course/5/api-gateway$ k -n api-gateway-staging get deploy,cm
```
:::

``` {.CodeMirror-line role="presentation"}
NAME                          READY   UP-TO-DATE   AVAILABLE   AGE
```

``` {.CodeMirror-line role="presentation"}
deployment.apps/api-gateway   1/1     1            1           20m
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
NAME                                  DATA   AGE
```

``` {.CodeMirror-line role="presentation"}
configmap/horizontal-scaling-config   1      20m
```

``` {.CodeMirror-line role="presentation"}
configmap/kube-root-ca.crt            1      21m
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 161px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 161px;"}
:::

 

###### Investigate Prod

Everything said about staging is also true about prod, there are just
different values of resources changed. Hence we should also see that
there are no changes to be applied:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka5774:/opt/course/5/api-gateway$ k kustomize prod
```
:::

``` {.CodeMirror-line role="presentation"}
apiVersion: v1
```

``` {.CodeMirror-line role="presentation"}
kind: ServiceAccount
```

``` {.CodeMirror-line role="presentation"}
metadata:
```

``` {.CodeMirror-line role="presentation"}
  name: api-gateway
```

``` {.CodeMirror-line role="presentation"}
  namespace: api-gateway-prod
```

``` {.CodeMirror-line role="presentation"}
...
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 161px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 161px;"}
:::

We can see that now *Namespace* `api-gateway-prod`{.copy-on-click} is
being used.

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka5774:/opt/course/5/api-gateway$ k kustomize prod | kubectl diff -f -
```
:::

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka5774:/opt/course/5/api-gateway$ k kustomize prod | kubectl apply -f -
```

``` {.CodeMirror-line role="presentation"}
serviceaccount/api-gateway unchanged
```

``` {.CodeMirror-line role="presentation"}
configmap/horizontal-scaling-config unchanged
```

``` {.CodeMirror-line role="presentation"}
deployment.apps/api-gateway unchanged
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 138px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 138px;"}
:::

And everything seems to be up to date for prod as well.

 

###### Step 1 {#step-1-3}

We need to remove the *ConfigMap* from base, staging and prod because
staging and prod both reference it as a patch. If we would only remove
it from base we would run into an error when trying to build staging for
example:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka5774:/opt/course/5/api-gateway$ k kustomize staging
```
:::

``` {.CodeMirror-line role="presentation"}
error: no resource matches strategic merge patch "ConfigMap.v1.[noGrp]/horizontal-scaling-config.[noNs]": no matches for Id ConfigMap.v1.[noGrp]/horizontal-scaling-config.[noNs]; failed to find unique target for patch ConfigMap.v1.[noGrp]/horizontal-scaling-config.[noNs]
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 92px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 92px;"}
:::

So we edit files `base/api-gateway.yaml`{.copy-on-click},
`staging/api-gateway.yaml`{.copy-on-click} and
`prod/api-gateway.yaml`{.copy-on-click} and remove the *ConfigMap*.
Afterwards we should get no errors and Yaml without that *ConfigMap*:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term" style="break-inside: unset;"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka5774:/opt/course/5/api-gateway$ k kustomize staging
```
:::

``` {.CodeMirror-line role="presentation"}
apiVersion: v1
```

``` {.CodeMirror-line role="presentation"}
kind: ServiceAccount
```

``` {.CodeMirror-line role="presentation"}
metadata:
```

``` {.CodeMirror-line role="presentation"}
  name: api-gateway
```

``` {.CodeMirror-line role="presentation"}
  namespace: api-gateway-staging
```

``` {.CodeMirror-line role="presentation"}
---
```

``` {.CodeMirror-line role="presentation"}
apiVersion: apps/v1
```

``` {.CodeMirror-line role="presentation"}
kind: Deployment
```

``` {.CodeMirror-line role="presentation"}
metadata:
```

``` {.CodeMirror-line role="presentation"}
  labels:
```

``` {.CodeMirror-line role="presentation"}
    env: staging
```

``` {.CodeMirror-line role="presentation"}
  name: api-gateway
```

``` {.CodeMirror-line role="presentation"}
  namespace: api-gateway-staging
```

``` {.CodeMirror-line role="presentation"}
spec:
```

``` {.CodeMirror-line role="presentation"}
  replicas: 1
```

``` {.CodeMirror-line role="presentation"}
  selector:
```

``` {.CodeMirror-line role="presentation"}
    matchLabels:
```

``` {.CodeMirror-line role="presentation"}
      id: api-gateway
```

``` {.CodeMirror-line role="presentation"}
  template:
```

``` {.CodeMirror-line role="presentation"}
    metadata:
```

``` {.CodeMirror-line role="presentation"}
      labels:
```

``` {.CodeMirror-line role="presentation"}
        id: api-gateway
```

``` {.CodeMirror-line role="presentation"}
    spec:
```

``` {.CodeMirror-line role="presentation"}
      containers:
```

``` {.CodeMirror-line role="presentation"}
      - image: httpd:2-alpine
```

``` {.CodeMirror-line role="presentation"}
        name: httpd
```

``` {.CodeMirror-line role="presentation"}
      serviceAccountName: api-gateway
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka5774:/opt/course/5/api-gateway$ k kustomize prod
```

``` {.CodeMirror-line role="presentation"}
apiVersion: v1
```

``` {.CodeMirror-line role="presentation"}
kind: ServiceAccount
```

``` {.CodeMirror-line role="presentation"}
metadata:
```

``` {.CodeMirror-line role="presentation"}
  name: api-gateway
```

``` {.CodeMirror-line role="presentation"}
  namespace: api-gateway-prod
```

``` {.CodeMirror-line role="presentation"}
---
```

``` {.CodeMirror-line role="presentation"}
apiVersion: apps/v1
```

``` {.CodeMirror-line role="presentation"}
kind: Deployment
```

``` {.CodeMirror-line role="presentation"}
metadata:
```

``` {.CodeMirror-line role="presentation"}
  labels:
```

``` {.CodeMirror-line role="presentation"}
    env: prod
```

``` {.CodeMirror-line role="presentation"}
  name: api-gateway
```

``` {.CodeMirror-line role="presentation"}
  namespace: api-gateway-prod
```

``` {.CodeMirror-line role="presentation"}
spec:
```

``` {.CodeMirror-line role="presentation"}
  replicas: 1
```

``` {.CodeMirror-line role="presentation"}
  selector:
```

``` {.CodeMirror-line role="presentation"}
    matchLabels:
```

``` {.CodeMirror-line role="presentation"}
      id: api-gateway
```

``` {.CodeMirror-line role="presentation"}
  template:
```

``` {.CodeMirror-line role="presentation"}
    metadata:
```

``` {.CodeMirror-line role="presentation"}
      labels:
```

``` {.CodeMirror-line role="presentation"}
        id: api-gateway
```

``` {.CodeMirror-line role="presentation"}
    spec:
```

``` {.CodeMirror-line role="presentation"}
      containers:
```

``` {.CodeMirror-line role="presentation"}
      - image: httpd:2-alpine
```

``` {.CodeMirror-line role="presentation"}
        name: httpd
```

``` {.CodeMirror-line role="presentation"}
      serviceAccountName: api-gateway
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 1311px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 1311px;"}
:::

 

###### Step 2 {#step-2-3}

We\'re going to add the requested *HPA* into the base config file:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="yaml" style="break-inside: unset;"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
# cka5774:/opt/course/5/api-gateway/base/api-gateway.yaml
```
:::

``` {.CodeMirror-line role="presentation"}
apiVersion: autoscaling/v2
```

``` {.CodeMirror-line role="presentation"}
kind: HorizontalPodAutoscaler
```

``` {.CodeMirror-line role="presentation"}
metadata:
```

``` {.CodeMirror-line role="presentation"}
  name: api-gateway
```

``` {.CodeMirror-line role="presentation"}
spec:
```

``` {.CodeMirror-line role="presentation"}
  scaleTargetRef:
```

``` {.CodeMirror-line role="presentation"}
    apiVersion: apps/v1
```

``` {.CodeMirror-line role="presentation"}
    kind: Deployment
```

``` {.CodeMirror-line role="presentation"}
    name: api-gateway
```

``` {.CodeMirror-line role="presentation"}
  minReplicas: 2
```

``` {.CodeMirror-line role="presentation"}
  maxReplicas: 4
```

``` {.CodeMirror-line role="presentation"}
  metrics:
```

``` {.CodeMirror-line role="presentation"}
    - type: Resource
```

``` {.CodeMirror-line role="presentation"}
      resource:
```

``` {.CodeMirror-line role="presentation"}
        name: cpu
```

``` {.CodeMirror-line role="presentation"}
        target:
```

``` {.CodeMirror-line role="presentation"}
          type: Utilization
```

``` {.CodeMirror-line role="presentation"}
          averageUtilization: 50
```

``` {.CodeMirror-line role="presentation"}
---
```

``` {.CodeMirror-line role="presentation"}
apiVersion: v1
```

``` {.CodeMirror-line role="presentation"}
kind: ServiceAccount
```

``` {.CodeMirror-line role="presentation"}
metadata:
```

``` {.CodeMirror-line role="presentation"}
  name: api-gateway
```

``` {.CodeMirror-line role="presentation"}
---
```

``` {.CodeMirror-line role="presentation"}
apiVersion: apps/v1
```

``` {.CodeMirror-line role="presentation"}
kind: Deployment
```

``` {.CodeMirror-line role="presentation"}
metadata:
```

``` {.CodeMirror-line role="presentation"}
  name: api-gateway
```

``` {.CodeMirror-line role="presentation"}
spec:
```

``` {.CodeMirror-line role="presentation"}
  replicas: 1
```

``` {.CodeMirror-line role="presentation"}
  selector:
```

``` {.CodeMirror-line role="presentation"}
    matchLabels:
```

``` {.CodeMirror-line role="presentation"}
      id: api-gateway
```

``` {.CodeMirror-line role="presentation"}
  template:
```

``` {.CodeMirror-line role="presentation"}
    metadata:
```

``` {.CodeMirror-line role="presentation"}
      labels:
```

``` {.CodeMirror-line role="presentation"}
        id: api-gateway
```

``` {.CodeMirror-line role="presentation"}
    spec:
```

``` {.CodeMirror-line role="presentation"}
      serviceAccountName: api-gateway
```

``` {.CodeMirror-line role="presentation"}
      containers:
```

``` {.CodeMirror-line role="presentation"}
        - image: httpd:2-alpine
```

``` {.CodeMirror-line role="presentation"}
          name: httpd
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 860px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 860px;"}
:::

Notice that we don\'t specify a *Namespace* here as done also for the
other resources. The *Namespace* will be set by staging and prod
overlays automatically.

 

###### Step 3 {#step-3-3}

In prod the *HPA* should have max replicas set to `6`{.copy-on-click} so
we add this to the prod patch:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="yaml" style="break-inside: unset;"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
# cka5774:/opt/course/5/api-gateway/prod/api-gateway.yaml
```
:::

``` {.CodeMirror-line role="presentation"}
apiVersion: autoscaling/v2
```

``` {.CodeMirror-line role="presentation"}
kind: HorizontalPodAutoscaler
```

``` {.CodeMirror-line role="presentation"}
metadata:
```

``` {.CodeMirror-line role="presentation"}
  name: api-gateway
```

``` {.CodeMirror-line role="presentation"}
spec:
```

``` {.CodeMirror-line role="presentation"}
  maxReplicas: 6
```

``` {.CodeMirror-line role="presentation"}
---
```

``` {.CodeMirror-line role="presentation"}
apiVersion: apps/v1
```

``` {.CodeMirror-line role="presentation"}
kind: Deployment
```

``` {.CodeMirror-line role="presentation"}
metadata:
```

``` {.CodeMirror-line role="presentation"}
  name: api-gateway
```

``` {.CodeMirror-line role="presentation"}
  labels:
```

``` {.CodeMirror-line role="presentation"}
    env: prod
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 280px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 280px;"}
:::

With that change we should see that staging will have the *HPA* with
`maxReplicas: 4`{.copy-on-click} from base, whereas prod will have
`maxReplicas: 6`{.copy-on-click}:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term" style="break-inside: unset;"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka5774:/opt/course/5/api-gateway$ k kustomize staging | grep maxReplicas -B5
```
:::

``` {.CodeMirror-line role="presentation"}
kind: HorizontalPodAutoscaler
```

``` {.CodeMirror-line role="presentation"}
metadata:
```

``` {.CodeMirror-line role="presentation"}
  name: api-gateway
```

``` {.CodeMirror-line role="presentation"}
  namespace: api-gateway-staging
```

``` {.CodeMirror-line role="presentation"}
spec:
```

``` {.CodeMirror-line role="presentation"}
  maxReplicas: 4
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka5774:/opt/course/5/api-gateway$ k kustomize prod | grep maxReplicas -B5
```

``` {.CodeMirror-line role="presentation"}
kind: HorizontalPodAutoscaler
```

``` {.CodeMirror-line role="presentation"}
metadata:
```

``` {.CodeMirror-line role="presentation"}
  name: api-gateway
```

``` {.CodeMirror-line role="presentation"}
  namespace: api-gateway-prod
```

``` {.CodeMirror-line role="presentation"}
spec:
```

``` {.CodeMirror-line role="presentation"}
  maxReplicas: 6
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 345px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 345px;"}
:::

 

###### Step 4 {#step-4-2}

Finally we apply the changes, first staging:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term" style="break-inside: unset;"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka5774:/opt/course/5/api-gateway$ k kustomize staging | kubectl diff -f -
```
:::

``` {.CodeMirror-line role="presentation"}
diff -u -N /tmp/LIVE-3038173353/autoscaling.v2.HorizontalPodAutoscaler.api-gateway-staging.api-gateway /tmp/MERGED-332240272/autoscaling.v2.HorizontalPodAutoscaler.api-gateway-staging.api-gateway
```

``` {.CodeMirror-line role="presentation"}
--- /tmp/LIVE-3038173353/autoscaling.v2.HorizontalPodAutoscaler.api-gateway-staging.api-gateway 2024-12-23 16:21:47.771211074 +0000
```

``` {.CodeMirror-line role="presentation"}
+++ /tmp/MERGED-332240272/autoscaling.v2.HorizontalPodAutoscaler.api-gateway-staging.api-gateway        2024-12-23 16:21:47.772211169 +0000
```

``` {.CodeMirror-line role="presentation"}
@@ -0,0 +1,24 @@
```

``` {.CodeMirror-line role="presentation"}
+apiVersion: autoscaling/v2
```

``` {.CodeMirror-line role="presentation"}
+kind: HorizontalPodAutoscaler
```

``` {.CodeMirror-line role="presentation"}
+metadata:
```

``` {.CodeMirror-line role="presentation"}
+  creationTimestamp: "2024-12-23T16:21:47Z"
```

``` {.CodeMirror-line role="presentation"}
+  name: api-gateway
```

``` {.CodeMirror-line role="presentation"}
+  namespace: api-gateway-staging
```

``` {.CodeMirror-line role="presentation"}
+  uid: d846f349-e695-4538-b3f8-ba514fc02ea5
```

``` {.CodeMirror-line role="presentation"}
+spec:
```

``` {.CodeMirror-line role="presentation"}
+  maxReplicas: 4
```

``` {.CodeMirror-line role="presentation"}
+  metrics:
```

``` {.CodeMirror-line role="presentation"}
+  - resource:
```

``` {.CodeMirror-line role="presentation"}
+      name: cpu
```

``` {.CodeMirror-line role="presentation"}
+      target:
```

``` {.CodeMirror-line role="presentation"}
+        averageUtilization: 50
```

``` {.CodeMirror-line role="presentation"}
+        type: Utilization
```

``` {.CodeMirror-line role="presentation"}
+    type: Resource
```

``` {.CodeMirror-line role="presentation"}
+  minReplicas: 2
```

``` {.CodeMirror-line role="presentation"}
+  scaleTargetRef:
```

``` {.CodeMirror-line role="presentation"}
+    apiVersion: apps/v1
```

``` {.CodeMirror-line role="presentation"}
+    kind: Deployment
```

``` {.CodeMirror-line role="presentation"}
+    name: api-gateway
```

``` {.CodeMirror-line role="presentation"}
+status:
```

``` {.CodeMirror-line role="presentation"}
+  currentMetrics: null
```

``` {.CodeMirror-line role="presentation"}
+  desiredReplicas: 0
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka5774:/opt/course/5/api-gateway$ k kustomize staging | kubectl apply -f -
```

``` {.CodeMirror-line role="presentation"}
serviceaccount/api-gateway unchanged
```

``` {.CodeMirror-line role="presentation"}
deployment.apps/api-gateway unchanged
```

``` {.CodeMirror-line role="presentation"}
horizontalpodautoscaler.autoscaling/api-gateway created
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka5774:/opt/course/5/api-gateway$ k kustomize staging | kubectl diff -f -
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 897px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 897px;"}
:::

And next for prod:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka5774:/opt/course/5/api-gateway$ k kustomize prod | kubectl apply -f -
```
:::

``` {.CodeMirror-line role="presentation"}
serviceaccount/api-gateway unchanged
```

``` {.CodeMirror-line role="presentation"}
deployment.apps/api-gateway unchanged
```

``` {.CodeMirror-line role="presentation"}
horizontalpodautoscaler.autoscaling/api-gateway created
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka5774:/opt/course/5/api-gateway$ k kustomize prod | kubectl diff -f -
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 138px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 138px;"}
:::

We notice that the *HPA* was created as expected, but nothing was done
with the *ConfigMap* that we removed from the Yaml files earlier. We
need to delete the remote *ConfigMaps* manually, why is explained in
more detail at the end of this solution.

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term" style="break-inside: unset;"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka5774:/opt/course/5/api-gateway$ k -n api-gateway-staging get cm
```
:::

``` {.CodeMirror-line role="presentation"}
NAME                        DATA   AGE
```

``` {.CodeMirror-line role="presentation"}
horizontal-scaling-config   1      61m
```

``` {.CodeMirror-line role="presentation"}
kube-root-ca.crt            1      61m
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka5774:/opt/course/5/api-gateway$ k -n api-gateway-staging delete cm horizontal-scaling-config 
```

``` {.CodeMirror-line role="presentation"}
configmap "horizontal-scaling-config" deleted
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka5774:/opt/course/5/api-gateway$ k -n api-gateway-prod get cm
```

``` {.CodeMirror-line role="presentation"}
NAME                        DATA   AGE
```

``` {.CodeMirror-line role="presentation"}
horizontal-scaling-config   2      61m
```

``` {.CodeMirror-line role="presentation"}
kube-root-ca.crt            1      62m
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka5774:/opt/course/5/api-gateway$ k -n api-gateway-prod delete cm horizontal-scaling-config 
```

``` {.CodeMirror-line role="presentation"}
configmap "horizontal-scaling-config" deleted
```

``` {.CodeMirror-line role="presentation"}
candidate@cka5774:/opt/course/5/api-gateway$ 
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 368px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 368px;"}
:::

Done!

 

###### Diff output after solution complete

After deleting the *ConfigMaps* manually we should not see any changes
when running a diff. This is because the *ConfigMap* does not exist any
longer in our Yaml and we already applied all changes. But we might see
something like this:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term" style="break-inside: unset;"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka5774:/opt/course/5/api-gateway$ k kustomize prod | kubectl diff -f -
```
:::

``` {.CodeMirror-line role="presentation"}
diff -u -N /tmp/LIVE-849078037/apps.v1.Deployment.api-gateway-prod.api-gateway /tmp/MERGED-2513424623/apps.v1.Deployment.api-gateway-prod.api-gateway
```

``` {.CodeMirror-line role="presentation"}
--- /tmp/LIVE-849078037/apps.v1.Deployment.api-gateway-prod.api-gateway 2024-12-23 16:37:44.763088538 +0000
```

``` {.CodeMirror-line role="presentation"}
+++ /tmp/MERGED-2513424623/apps.v1.Deployment.api-gateway-prod.api-gateway      2024-12-23 16:37:44.766088823 +0000
```

``` {.CodeMirror-line role="presentation"}
@@ -6,7 +6,7 @@
```

``` {.CodeMirror-line role="presentation"}
     kubectl.kubernetes.io/last-applied-configuration: |
```

``` {.CodeMirror-line role="presentation"}
       {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"labels":{"env":"prod"},"name":"api-gateway","namespace":"api-gateway-prod"},"spec":{"replicas":1,"selector":{"matchLabels":{"id":"api-gateway"}},"template":{"metadata":{"labels":{"id":"api-gateway"}},"spec":{"containers":[{"image":"httpd:2-alpine","name":"httpd"}],"serviceAccountName":"api-gateway"}}}}
```

``` {.CodeMirror-line role="presentation"}
   creationTimestamp: "2024-12-23T15:34:06Z"
```

``` {.CodeMirror-line role="presentation"}
-  generation: 2
```

``` {.CodeMirror-line role="presentation"}
+  generation: 3
```

``` {.CodeMirror-line role="presentation"}
   labels:
```

``` {.CodeMirror-line role="presentation"}
     env: prod
```

``` {.CodeMirror-line role="presentation"}
   name: api-gateway
```

``` {.CodeMirror-line role="presentation"}
@@ -15,7 +15,7 @@
```

``` {.CodeMirror-line role="presentation"}
   uid: ca3b43c9-d33b-4bdc-98ae-172cd9ee8cdb
```

``` {.CodeMirror-line role="presentation"}
 spec:
```

``` {.CodeMirror-line role="presentation"}
   progressDeadlineSeconds: 600
```

``` {.CodeMirror-line role="presentation"}
-  replicas: 2
```

``` {.CodeMirror-line role="presentation"}
+  replicas: 1
```

``` {.CodeMirror-line role="presentation"}
   revisionHistoryLimit: 10
```

``` {.CodeMirror-line role="presentation"}
   selector:
```

``` {.CodeMirror-line role="presentation"}
     matchLabels:
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 621px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 621px;"}
:::

Above we can see that we would change the replicas from 2 to 1. This is
because the *HPA* already set the replicas to the
`minReplicas`{.copy-on-click} that we defined and it\'s different than
the default `replicas:`{.copy-on-click} of the *Deployment*:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka5774:/opt/course/5/api-gateway$ k -n api-gateway-prod get hpa
```
:::

``` {.CodeMirror-line role="presentation"}
NAME          ...   MINPODS   MAXPODS   REPLICAS   AGE
```

``` {.CodeMirror-line role="presentation"}
api-gateway   ...   2         6         2          15m
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 69px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 69px;"}
:::

This means each time we deploy our Kustomize built Yaml, the replicas
that the *HPA* applied would be overwritten, which is not cool. It does
not matter for the scoring of this question but to prevent this we could
simply remove the `replicas:`{.copy-on-click} setting from the
*Deployment* in base, staging and prod.

 

###### Kustomize / Helm and State {#kustomize--helm-and-state}

We had to delete the remote *ConfigMaps* manually. Kustomize won\'t
delete remote resources if they only exist remote. This is because it
does not keep any state and hence doesn\'t know which remote resources
were created by Kustomize or by anything else.

Helm will remove remote resources if they only exist remote and if they
were created by Helm. It can do this because it keeps a state of all
performed changes.

Both approaches have pros and cons:

-   Kustomize is less complex by not having to manage state, but might
    need more manual work cleaning up

-   Helm can keep better track of remote resources, but things can get
    complex and messy if there is a state error or mismatch. State
    changes (Helm actions) at the same time need to be prevented or
    accounted for.

 

 

## Question 6 \| Storage, PV, PVC, Pod volume {#question-6-|-storage-pv-pvc-pod-volume}

 

Solve this question on: `ssh cka7968`{.copy-on-click}

 

Create a new *PersistentVolume* named `safari-pv`{.copy-on-click}. It
should have a capacity of *2Gi*, accessMode *ReadWriteOnce*, hostPath
`/Volumes/Data`{.copy-on-click} and no storageClassName defined.

Next create a new *PersistentVolumeClaim* in *Namespace*
`project-t230`{.copy-on-click} named `safari-pvc`{.copy-on-click} . It
should request *2Gi* storage, accessMode *ReadWriteOnce* and should not
define a storageClassName. The *PVC* should bound to the *PV* correctly.

Finally create a new *Deployment* `safari`{.copy-on-click} in
*Namespace* `project-t230`{.copy-on-click} which mounts that volume at
`/tmp/safari-data`{.copy-on-click}. The *Pods* of that *Deployment*
should be of image `httpd:2-alpine`{.copy-on-click}.

 

##### Answer {#answer-6}

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ ssh cka7968
```
:::

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka7968:~$ vim 6_pv.yaml
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 69px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 69px;"}
:::

Find an example from
[https://kubernetes.io/docs](https://kubernetes.io/docs){.url
target="_blank"} and alter it:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="yaml"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
# cka7968:/home/candidate/6_pv.yaml
```
:::

``` {.CodeMirror-line role="presentation"}
kind: PersistentVolume
```

``` {.CodeMirror-line role="presentation"}
apiVersion: v1
```

``` {.CodeMirror-line role="presentation"}
metadata:
```

``` {.CodeMirror-line role="presentation"}
 name: safari-pv
```

``` {.CodeMirror-line role="presentation"}
spec:
```

``` {.CodeMirror-line role="presentation"}
 capacity:
```

``` {.CodeMirror-line role="presentation"}
  storage: 2Gi
```

``` {.CodeMirror-line role="presentation"}
 accessModes:
```

``` {.CodeMirror-line role="presentation"}
  - ReadWriteOnce
```

``` {.CodeMirror-line role="presentation"}
 hostPath:
```

``` {.CodeMirror-line role="presentation"}
  path: "/Volumes/Data"
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 240px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 240px;"}
:::

> ℹ️ Using the hostPath volume type presents many security risks, avoid
> if possible. Be aware that data stored in the hostPath directory will
> not be shared across nodes. The data available for a *Pod* depends on
> which node the *Pod* is scheduled.

Then create it:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka7968:~$ k -f 6_pv.yaml create
```
:::

``` {.CodeMirror-line role="presentation"}
persistentvolume/safari-pv created
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 46px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 46px;"}
:::

Next the *PersistentVolumeClaim*:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka7968:~ vim 6_pvc.yaml
```
:::
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 23px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 23px;"}
:::

Find an example from the K8s Docs and alter it:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="yaml"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
# cka7968:/home/candidate/6_pvc.yaml
```
:::

``` {.CodeMirror-line role="presentation"}
kind: PersistentVolumeClaim
```

``` {.CodeMirror-line role="presentation"}
apiVersion: v1
```

``` {.CodeMirror-line role="presentation"}
metadata:
```

``` {.CodeMirror-line role="presentation"}
  name: safari-pvc
```

``` {.CodeMirror-line role="presentation"}
  namespace: project-t230
```

``` {.CodeMirror-line role="presentation"}
spec:
```

``` {.CodeMirror-line role="presentation"}
  accessModes:
```

``` {.CodeMirror-line role="presentation"}
    - ReadWriteOnce
```

``` {.CodeMirror-line role="presentation"}
  resources:
```

``` {.CodeMirror-line role="presentation"}
    requests:
```

``` {.CodeMirror-line role="presentation"}
     storage: 2Gi
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 240px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 240px;"}
:::

Then create:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka7968:~$ k -f 6_pvc.yaml create
```
:::

``` {.CodeMirror-line role="presentation"}
persistentvolumeclaim/safari-pvc created
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 46px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 46px;"}
:::

And check that both have the status Bound:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka7968:~$ k -n project-t230 get pv,pvc
```
:::

``` {.CodeMirror-line role="presentation"}
NAME                         CAPACITY  ... STATUS   CLAIM                    ...
```

``` {.CodeMirror-line role="presentation"}
persistentvolume/safari-pv   2Gi       ... Bound    project-t230/safari-pvc ...
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
NAME                               STATUS   VOLUME      CAPACITY ...
```

``` {.CodeMirror-line role="presentation"}
persistentvolumeclaim/safari-pvc   Bound    safari-pv   2Gi      ...
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 138px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 138px;"}
:::

Next we create a *Deployment* and mount that volume:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka7968:~$ k -n project-t230 create deploy safari --image=httpd:2-alpine --dry-run=client -o yaml > 6_dep.yaml
```
:::

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka7968:~$ vim 6_dep.yaml
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 92px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 92px;"}
:::

Alter the yaml to mount the volume:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="yaml" style="break-inside: unset;"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
# cka7968:/home/candidate/6_dep.yaml
```
:::

``` {.CodeMirror-line role="presentation"}
apiVersion: apps/v1
```

``` {.CodeMirror-line role="presentation"}
kind: Deployment
```

``` {.CodeMirror-line role="presentation"}
metadata:
```

``` {.CodeMirror-line role="presentation"}
  creationTimestamp: null
```

``` {.CodeMirror-line role="presentation"}
  labels:
```

``` {.CodeMirror-line role="presentation"}
    app: safari
```

``` {.CodeMirror-line role="presentation"}
  name: safari
```

``` {.CodeMirror-line role="presentation"}
  namespace: project-t230
```

``` {.CodeMirror-line role="presentation"}
spec:
```

``` {.CodeMirror-line role="presentation"}
  replicas: 1
```

``` {.CodeMirror-line role="presentation"}
  selector:
```

``` {.CodeMirror-line role="presentation"}
    matchLabels:
```

``` {.CodeMirror-line role="presentation"}
      app: safari
```

``` {.CodeMirror-line role="presentation"}
  strategy: {}
```

``` {.CodeMirror-line role="presentation"}
  template:
```

``` {.CodeMirror-line role="presentation"}
    metadata:
```

``` {.CodeMirror-line role="presentation"}
      creationTimestamp: null
```

``` {.CodeMirror-line role="presentation"}
      labels:
```

``` {.CodeMirror-line role="presentation"}
        app: safari
```

``` {.CodeMirror-line role="presentation"}
    spec:
```

``` {.CodeMirror-line role="presentation"}
      volumes:                                      # add
```

``` {.CodeMirror-line role="presentation"}
      - name: data                                  # add
```

``` {.CodeMirror-line role="presentation"}
        persistentVolumeClaim:                      # add
```

``` {.CodeMirror-line role="presentation"}
          claimName: safari-pvc                     # add
```

``` {.CodeMirror-line role="presentation"}
      containers:
```

``` {.CodeMirror-line role="presentation"}
      - image: httpd:2-alpine
```

``` {.CodeMirror-line role="presentation"}
        name: container
```

``` {.CodeMirror-line role="presentation"}
        volumeMounts:                               # add
```

``` {.CodeMirror-line role="presentation"}
        - name: data                                # add
```

``` {.CodeMirror-line role="presentation"}
          mountPath: /tmp/safari-data               # add
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 620px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 620px;"}
:::

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka7968:~$ k -f 6_dep.yaml create
```
:::

``` {.CodeMirror-line role="presentation"}
deployment.apps/safari created
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 46px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 46px;"}
:::

We can confirm it\'s mounting correctly:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka7968:~$ k -n project-t230 describe pod safari-b499cc5b9-x7d7h | grep -A2 Mounts:
```
:::

``` {.CodeMirror-line role="presentation"}
    Mounts:
```

``` {.CodeMirror-line role="presentation"}
      /tmp/safari-data from data (rw)
```

``` {.CodeMirror-line role="presentation"}
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-xght8 (ro)
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 92px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 92px;"}
:::

 

 

## Question 7 \| Node and Pod Resource Usage {#question-7-|-node-and-pod-resource-usage}

 

Solve this question on: `ssh cka5774`{.copy-on-click}

 

The metrics-server has been installed in the cluster. Write two bash
scripts which use `kubectl`{.copy-on-click}:

1.  Script `/opt/course/7/node.sh`{.copy-on-click} should show resource
    usage of *Nodes*

2.  Script `/opt/course/7/pod.sh`{.copy-on-click} should show resource
    usage of *Pods* and their containers

 

##### Answer: {#answer-7}

The command we need to use here is top:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term" style="break-inside: unset;"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ ssh cka5774
```
:::

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka5774:~$ k top -h
```

``` {.CodeMirror-line role="presentation"}
Display resource (CPU/memory) usage.
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
 The top command allows you to see the resource consumption for nodes or pods.
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
 This command requires Metrics Server to be correctly configured and working on the server.
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
Available Commands:
```

``` {.CodeMirror-line role="presentation"}
  node          Display resource (CPU/memory) usage of nodes
```

``` {.CodeMirror-line role="presentation"}
  pod           Display resource (CPU/memory) usage of pods
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
Usage:
```

``` {.CodeMirror-line role="presentation"}
  kubectl top [flags] [options]
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
Use "kubectl top <command> --help" for more information about a given command.
```

``` {.CodeMirror-line role="presentation"}
Use "kubectl options" for a list of global command-line options (applies to all commands).
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 414px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 414px;"}
:::

We see that the metrics server provides information about resource
usage:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka5774:~$ k top node
```
:::

``` {.CodeMirror-line role="presentation"}
NAME            CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%   
```

``` {.CodeMirror-line role="presentation"}
cka5774         104m         10%    1121Mi          60%       
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 69px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 69px;"}
:::

We create the first file, ensure to **not** use aliases but instead the
full command names:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang=""}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
# cka5774:/opt/course/7/node.sh
```
:::

``` {.CodeMirror-line role="presentation"}
kubectl top node
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 46px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 46px;"}
:::

For the second file we might need to check the docs again:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka5774:~$ k top pod -h
```
:::

``` {.CodeMirror-line role="presentation"}
Display resource (CPU/memory) usage of pods.
```

``` {.CodeMirror-line role="presentation"}
...
```

``` {.CodeMirror-line role="presentation"}
    --containers=false:
```

``` {.CodeMirror-line role="presentation"}
        If present, print usage of containers within a pod.
```

``` {.CodeMirror-line role="presentation"}
...
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 138px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 138px;"}
:::

With this we can finish this task:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang=""}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
# cka5774:/opt/course/7/pod.sh
```
:::

``` {.CodeMirror-line role="presentation"}
kubectl top pod --containers=true
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 46px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 46px;"}
:::

 

 

## Question 8 \| Update Kubernetes Version and join cluster {#question-8-|-update-kubernetes-version-and-join-cluster}

 

Solve this question on: `ssh cka3962`{.copy-on-click}

 

Your coworker notified you that node `cka3962-node1`{.copy-on-click} is
running an older Kubernetes version and is not even part of the cluster
yet.

1.  Update the node\'s Kubernetes to the exact version of the
    controlplane

2.  Add the node to the cluster using kubeadm

 

> ℹ️ You can connect to the worker node using
> `ssh cka3962-node1`{.copy-on-click} from `cka3962`{.copy-on-click}

 

 

##### Answer: {#answer-8}

###### Update Kubernetes to controlplane version

Search in the docs for [kubeadm
upgrade](https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade):

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ ssh cka3962
```
:::

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka3962:~$ k get node
```

``` {.CodeMirror-line role="presentation"}
NAME      STATUS   ROLES           AGE    VERSION
```

``` {.CodeMirror-line role="presentation"}
cka3962   Ready    control-plane   169m   v1.32.1
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 115px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 115px;"}
:::

The controlplane node seems to be running Kubernetes 1.32.1.

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term" style="break-inside: unset;"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka3962:~$ ssh cka3962-node1
```
:::

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka3962-node1:~$ sudo -i
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ root@cka3962-node1:~# kubectl version
```

``` {.CodeMirror-line role="presentation"}
Client Version: v1.31.5
```

``` {.CodeMirror-line role="presentation"}
Kustomize Version: v5.4.2
```

``` {.CodeMirror-line role="presentation"}
The connection to the server localhost:8080 was refused - did you specify the right host or port?
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ root@cka3962-node1:~# kubelet --version
```

``` {.CodeMirror-line role="presentation"}
Kubernetes v1.31.5
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ root@cka3962-node1:~# kubeadm version
```

``` {.CodeMirror-line role="presentation"}
kubeadm version: &version.Info{Major:"1", Minor:"32", GitVersion:"v1.32.1", GitCommit:"e9c9be4007d1664e68796af02b8978640d2c1b26", GitTreeState:"clean", BuildDate:"2025-01-15T14:39:14Z", GoVersion:"go1.23.4", Compiler:"gc", Platform:"linux/amd64"}
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 368px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 368px;"}
:::

Above we can see that kubeadm is already installed in the exact needed
version, otherwise we would need to install it using
`apt install kubeadm=1.32.1-1.1`{.copy-on-click}.

With the correct kubeadm version we can continue:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ root@cka3962-node1:~# kubeadm upgrade node
```
:::

``` {.CodeMirror-line role="presentation"}
couldn't create a Kubernetes client from file "/etc/kubernetes/kubelet.conf": failed to load admin kubeconfig: open /etc/kubernetes/kubelet.conf: no such file or directory
```

``` {.CodeMirror-line role="presentation"}
To see the stack trace of this error execute with --v=5 or higher
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 92px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 92px;"}
:::

This is usually the proper command to upgrade a worker node. But as
mentioned in the question description, this node is not yet part of the
cluster. Hence there is nothing to update. We\'ll add the node to the
cluster later using `kubeadm join`{.copy-on-click}. For now we can
continue with updating kubelet and kubectl:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term" style="break-inside: unset;"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ root@cka3962-node1:~# apt update
```
:::

``` {.CodeMirror-line role="presentation"}
Hit:1 https://prod-cdn.packages.k8s.io/repositories/isv:/kubernetes:/core:/stable:/v1.32/deb  InRelease
```

``` {.CodeMirror-line role="presentation"}
Hit:2 https://prod-cdn.packages.k8s.io/repositories/isv:/kubernetes:/core:/stable:/v1.31/deb  InRelease
```

``` {.CodeMirror-line role="presentation"}
Reading package lists... Done
```

``` {.CodeMirror-line role="presentation"}
Building dependency tree... Done
```

``` {.CodeMirror-line role="presentation"}
Reading state information... Done
```

``` {.CodeMirror-line role="presentation"}
2 packages can be upgraded. Run 'apt list --upgradable' to see them.
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ root@cka3962-node1:~# apt show kubectl -a | grep 1.32
```

``` {.CodeMirror-line role="presentation"}
Version: 1.32.1-1.1
```

``` {.CodeMirror-line role="presentation"}
APT-Sources: https://pkgs.k8s.io/core:/stable:/v1.32/deb  Packages
```

``` {.CodeMirror-line role="presentation"}
Version: 1.32.0-1.1
```

``` {.CodeMirror-line role="presentation"}
APT-Sources: https://pkgs.k8s.io/core:/stable:/v1.32/deb  Packages
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ root@cka3962-node1:~# apt install kubectl=1.32.1-1.1 kubelet=1.32.1-1.1
```

``` {.CodeMirror-line role="presentation"}
Reading package lists... Done
```

``` {.CodeMirror-line role="presentation"}
Building dependency tree... Done
```

``` {.CodeMirror-line role="presentation"}
Reading state information... Done
```

``` {.CodeMirror-line role="presentation"}
The following package was automatically installed and is no longer required:
```

``` {.CodeMirror-line role="presentation"}
  squashfs-tools
```

``` {.CodeMirror-line role="presentation"}
Use 'apt autoremove' to remove it.
```

``` {.CodeMirror-line role="presentation"}
The following packages will be upgraded:
```

``` {.CodeMirror-line role="presentation"}
  kubectl kubelet
```

``` {.CodeMirror-line role="presentation"}
2 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

``` {.CodeMirror-line role="presentation"}
Need to get 26.4 MB of archives.
```

``` {.CodeMirror-line role="presentation"}
After this operation, 1430 kB of additional disk space will be used.
```

``` {.CodeMirror-line role="presentation"}
Get:1 https://prod-cdn.packages.k8s.io/repositories/isv:/kubernetes:/core:/stable:/v1.32/deb  kubectl 1.32.1-1.1 [11.3 MB]
```

``` {.CodeMirror-line role="presentation"}
Get:2 https://prod-cdn.packages.k8s.io/repositories/isv:/kubernetes:/core:/stable:/v1.32/deb  kubelet 1.32.1-1.1 [15.2 MB]
```

``` {.CodeMirror-line role="presentation"}
Fetched 26.4 MB in 1s (30.0 MB/s) 
```

``` {.CodeMirror-line role="presentation"}
(Reading database ... 72574 files and directories currently installed.)
```

``` {.CodeMirror-line role="presentation"}
Preparing to unpack .../kubectl_1.32.1-1.1_amd64.deb ...
```

``` {.CodeMirror-line role="presentation"}
Unpacking kubectl (1.32.1-1.1) over (1.31.5-1.1) ...
```

``` {.CodeMirror-line role="presentation"}
Preparing to unpack .../kubelet_1.32.1-1.1_amd64.deb ...
```

``` {.CodeMirror-line role="presentation"}
Unpacking kubelet (1.32.1-1.1) over (1.31.5-1.1) ...
```

``` {.CodeMirror-line role="presentation"}
Setting up kubectl (1.32.1-1.1) ...
```

``` {.CodeMirror-line role="presentation"}
Setting up kubelet (1.32.1-1.1) ...
```

``` {.CodeMirror-line role="presentation"}
...
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ root@cka3962-node1:~# kubelet --version
```

``` {.CodeMirror-line role="presentation"}
Kubernetes v1.32.1
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 966px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 966px;"}
:::

Now that we\'re up to date with kubeadm, kubectl and kubelet we can
restart the kubelet:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ root@cka3962-node1:~# service kubelet restart
```
:::

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ root@cka3962-node1:~# service kubelet status
```

``` {.CodeMirror-line role="presentation"}
● kubelet.service - kubelet: The Kubernetes Node Agent
```

``` {.CodeMirror-line role="presentation"}
     Loaded: loaded (/usr/lib/systemd/system/kubelet.service; enabled; preset: enabled)
```

``` {.CodeMirror-line role="presentation"}
    Drop-In: /usr/lib/systemd/system/kubelet.service.d
```

``` {.CodeMirror-line role="presentation"}
             └─10-kubeadm.conf
```

``` {.CodeMirror-line role="presentation"}
     Active: activating (auto-restart) (Result: exit-code) since Wed 2025-02-05 17:52:14 UTC; 5s ago
```

``` {.CodeMirror-line role="presentation"}
       Docs: https://kubernetes.io/docs/
```

``` {.CodeMirror-line role="presentation"}
    Process: 14013 ExecStart=/usr/bin/kubelet $KUBELET_KUBECONFIG_ARGS $KUBELET_CONFIG_ARGS $KUBELET_KUBEADM_ARGS $KUBELET_EXTRA_AR>
```

``` {.CodeMirror-line role="presentation"}
   Main PID: 14013 (code=exited, status=1/FAILURE)
```

``` {.CodeMirror-line role="presentation"}
        CPU: 86ms
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 299px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 299px;"}
:::

These errors occur because we still need to run
`kubeadm join`{.copy-on-click} to join the node into the cluster. Let\'s
do this in the next step.

 

###### Add cka3962-node1 to cluster

First we log into the controlplane node and generate a new TLS bootstrap
token, also printing out the join command:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ ssh cka3962
```
:::

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka3962:~$ sudo -i
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ root@cka3962:~# kubeadm token create --print-join-command
```

``` {.CodeMirror-line role="presentation"}
kubeadm join 192.168.100.31:6443 --token pwq11h.uevwb20rt81e6whd --discovery-token-ca-cert-hash sha256:cb299d7b2025adf683779793a4a0a2051ac7611da668f188770259b0da68376c 
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ root@cka3962:~# kubeadm token list
```

``` {.CodeMirror-line role="presentation"}
TOKEN                     TTL         EXPIRES   ...
```

``` {.CodeMirror-line role="presentation"}
a3py1z.lq1aiephfk3k8o08   <forever>   <never>   ...
```

``` {.CodeMirror-line role="presentation"}
oq905j.i1q45s76clmm3hkn   21h         2025-02-06T15:00:12Z   ...
```

``` {.CodeMirror-line role="presentation"}
pwq11h.uevwb20rt81e6whd   23h         2025-02-06T17:52:45Z   ...
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 299px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 299px;"}
:::

We see the expiration of 23h for our token, we could adjust this by
passing the ttl argument.

Next we connect again to `cka3962-node1`{.copy-on-click} and simply
execute the join command from above:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term" style="break-inside: unset;"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ root@cka3962:~# ssh cka3962-node1
```
:::

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ root@cka3962-node1:~# kubeadm join 192.168.100.31:6443 --token pwq11h.uevwb20rt81e6whd --discovery-token-ca-cert-hash sha256:cb299d7b2025adf683779793a4a0a2051ac7611da668f188770259b0da68376c 
```

``` {.CodeMirror-line role="presentation"}
[preflight] Running pre-flight checks
```

``` {.CodeMirror-line role="presentation"}
[preflight] Reading configuration from the "kubeadm-config" ConfigMap in namespace "kube-system"...
```

``` {.CodeMirror-line role="presentation"}
[preflight] Use 'kubeadm init phase upload-config --config your-config.yaml' to re-upload it.
```

``` {.CodeMirror-line role="presentation"}
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
```

``` {.CodeMirror-line role="presentation"}
[kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
```

``` {.CodeMirror-line role="presentation"}
[kubelet-start] Starting the kubelet
```

``` {.CodeMirror-line role="presentation"}
[kubelet-check] Waiting for a healthy kubelet at http://127.0.0.1:10248/healthz. This can take up to 4m0s
```

``` {.CodeMirror-line role="presentation"}
[kubelet-check] The kubelet is healthy after 1.016619495s
```

``` {.CodeMirror-line role="presentation"}
[kubelet-start] Waiting for the kubelet to perform the TLS Bootstrap
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
This node has joined the cluster:
```

``` {.CodeMirror-line role="presentation"}
* Certificate signing request was sent to apiserver and a response was received.
```

``` {.CodeMirror-line role="presentation"}
* The Kubelet was informed of the new secure connection details.
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
Run 'kubectl get nodes' on the control-plane to see this node join the cluster.
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ root@cka3962-node1:~# service kubelet status
```

``` {.CodeMirror-line role="presentation"}
● kubelet.service - kubelet: The Kubernetes Node Agent
```

``` {.CodeMirror-line role="presentation"}
     Loaded: loaded (/usr/lib/systemd/system/kubelet.service; enabled; preset: enabled)
```

``` {.CodeMirror-line role="presentation"}
    Drop-In: /usr/lib/systemd/system/kubelet.service.d
```

``` {.CodeMirror-line role="presentation"}
             └─10-kubeadm.conf
```

``` {.CodeMirror-line role="presentation"}
     Active: active (running) since Wed 2025-02-05 17:53:35 UTC; 16s ago
```

``` {.CodeMirror-line role="presentation"}
       Docs: https://kubernetes.io/docs/
```

``` {.CodeMirror-line role="presentation"}
   Main PID: 14204 (kubelet)
```

``` {.CodeMirror-line role="presentation"}
      Tasks: 10 (limit: 1113)
```

``` {.CodeMirror-line role="presentation"}
     Memory: 23.8M (peak: 24.0M)
```

``` {.CodeMirror-line role="presentation"}
        CPU: 1.460s
```

``` {.CodeMirror-line role="presentation"}
     CGroup: /system.slice/kubelet.service
```

``` {.CodeMirror-line role="presentation"}
             └─14204 /usr/bin/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/ku>
```

``` {.CodeMirror-line role="presentation"}
...
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 805px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 805px;"}
:::

> ℹ️ If you have troubles with `kubeadm join`{.copy-on-click} you might
> need to run `kubeadm reset`{.copy-on-click} before

Finally we check the node status:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ root@cka3962:~# k get node
```
:::

``` {.CodeMirror-line role="presentation"}
NAME            STATUS      ROLES           AGE    VERSION
```

``` {.CodeMirror-line role="presentation"}
cka3962         Ready       control-plane   173m   v1.32.1
```

``` {.CodeMirror-line role="presentation"}
cka3962-node1   NotReady    <none>          20s    v1.32.1
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 92px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 92px;"}
:::

Give it a bit of time till the node is ready.

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ root@cka3962:~# k get node
```
:::

``` {.CodeMirror-line role="presentation"}
NAME            STATUS   ROLES           AGE    VERSION
```

``` {.CodeMirror-line role="presentation"}
cka3962         Ready    control-plane   173m   v1.32.1
```

``` {.CodeMirror-line role="presentation"}
cka3962-node1   Ready    <none>          29s    v1.32.1
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 92px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 92px;"}
:::

We see `cka3962-node1`{.copy-on-click} is now available and up to date.

 

 

## Question 9 \| Contact K8s Api from inside Pod {#question-9-|-contact-k8s-api-from-inside-pod}

 

Solve this question on: `ssh cka9412`{.copy-on-click}

 

There is *ServiceAccount* `secret-reader`{.copy-on-click} in *Namespace*
`project-swan`{.copy-on-click}. Create a *Pod* of image
`nginx:1-alpine`{.copy-on-click} named `api-contact`{.copy-on-click}
which uses this *ServiceAccount*.

Exec into the *Pod* and use `curl`{.copy-on-click} to manually query all
*Secrets* from the Kubernetes Api.

Write the result into file `/opt/course/9/result.json`{.copy-on-click}.

 

##### Answer: {#answer-9}

[https://kubernetes.io/docs/tasks/run-application/access-api-from-pod](https://kubernetes.io/docs/tasks/run-application/access-api-from-pod){.url
target="_blank"}

You can find information in the K8s Docs by searching for \"curl api\"
for example.

 

###### Create Pod which uses ServiceAccount

First we create the *Pod*:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ ssh cka9412
```
:::

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka9412:~$ k run api-contact --image=nginx:1-alpine --dry-run=client -o yaml > 9.yaml
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka9412:~$ vim 9.yaml
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 115px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 115px;"}
:::

Add the serviceAccountName and *Namespace*:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="yaml" style="break-inside: unset;"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
# cka9412:/home/candidate/9.yaml
```
:::

``` {.CodeMirror-line role="presentation"}
apiVersion: v1
```

``` {.CodeMirror-line role="presentation"}
kind: Pod
```

``` {.CodeMirror-line role="presentation"}
metadata:
```

``` {.CodeMirror-line role="presentation"}
  creationTimestamp: null
```

``` {.CodeMirror-line role="presentation"}
  labels:
```

``` {.CodeMirror-line role="presentation"}
    run: api-contact
```

``` {.CodeMirror-line role="presentation"}
  name: api-contact
```

``` {.CodeMirror-line role="presentation"}
  namespace: project-swan             # add
```

``` {.CodeMirror-line role="presentation"}
spec:
```

``` {.CodeMirror-line role="presentation"}
  serviceAccountName: secret-reader   # add
```

``` {.CodeMirror-line role="presentation"}
  containers:
```

``` {.CodeMirror-line role="presentation"}
  - image: nginx:1-alpine
```

``` {.CodeMirror-line role="presentation"}
    name: api-contact
```

``` {.CodeMirror-line role="presentation"}
    resources: {}
```

``` {.CodeMirror-line role="presentation"}
  dnsPolicy: ClusterFirst
```

``` {.CodeMirror-line role="presentation"}
  restartPolicy: Always
```

``` {.CodeMirror-line role="presentation"}
status: {}
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 360px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 360px;"}
:::

Create it:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka9412:~$ k -f 9.yaml apply
```
:::

``` {.CodeMirror-line role="presentation"}
pod/api-contact created
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 46px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 46px;"}
:::

 

###### Contact K8s Api from inside Pod

Once in the container we can connect to the K8s Api using
`curl`{.copy-on-click}, it\'s usually available via the *Service* named
`kubernetes`{.copy-on-click} in *Namespace* `default`{.copy-on-click}.
Because of K8s internal DNS resolution we can use the url
`kubernetes.default`{.copy-on-click}.

> ℹ️ Otherwise we can find the K8s Api IP via environment variables
> inside the *Pod*, simply run `env`{.copy-on-click}

So we can try to contact the K8s Api:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term" style="break-inside: unset;"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka9412:~$ k -n project-swan exec api-contact -it -- sh
```
:::

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ / # curl https://kubernetes.default
```

``` {.CodeMirror-line role="presentation"}
curl: (60) SSL peer certificate or SSH remote key was not OK
```

``` {.CodeMirror-line role="presentation"}
More details here: https://curl.se/docs/sslcerts.html
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
curl failed to verify the legitimacy of the server and therefore could not
```

``` {.CodeMirror-line role="presentation"}
establish a secure connection to it. To learn more about this situation and
```

``` {.CodeMirror-line role="presentation"}
how to fix it, please visit the webpage mentioned above.
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ / # curl -k https://kubernetes.default
```

``` {.CodeMirror-line role="presentation"}
{
```

``` {.CodeMirror-line role="presentation"}
  "kind": "Status",
```

``` {.CodeMirror-line role="presentation"}
  "apiVersion": "v1",
```

``` {.CodeMirror-line role="presentation"}
  "metadata": {},
```

``` {.CodeMirror-line role="presentation"}
  "status": "Failure",
```

``` {.CodeMirror-line role="presentation"}
  "message": "forbidden: User \"system:anonymous\" cannot get path \"/\"",
```

``` {.CodeMirror-line role="presentation"}
  "reason": "Forbidden",
```

``` {.CodeMirror-line role="presentation"}
  "details": {},
```

``` {.CodeMirror-line role="presentation"}
  "code": 403
```

``` {.CodeMirror-line role="presentation"}
}~ $ 
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ / # curl -k https://kubernetes.default/api/v1/secrets
```

``` {.CodeMirror-line role="presentation"}
{
```

``` {.CodeMirror-line role="presentation"}
  "kind": "Status",
```

``` {.CodeMirror-line role="presentation"}
  "apiVersion": "v1",
```

``` {.CodeMirror-line role="presentation"}
  "metadata": {},
```

``` {.CodeMirror-line role="presentation"}
  "status": "Failure",
```

``` {.CodeMirror-line role="presentation"}
  "message": "secrets is forbidden: User \"system:anonymous\" cannot list resource \"secrets\" in API group \"\" at the cluster scope",
```

``` {.CodeMirror-line role="presentation"}
  "reason": "Forbidden",
```

``` {.CodeMirror-line role="presentation"}
  "details": {
```

``` {.CodeMirror-line role="presentation"}
    "kind": "secrets"
```

``` {.CodeMirror-line role="presentation"}
  },
```

``` {.CodeMirror-line role="presentation"}
  "code": 403
```

``` {.CodeMirror-line role="presentation"}
}
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 828px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 828px;"}
:::

The first command fails because of an untrusted certificate, but we can
ignore this with `-k`{.copy-on-click} for this scenario. We explain at
the end how we can add the correct certificate instead of having to use
the insecure `-k`{.copy-on-click} option.

The last command shows 403 forbidden, this is because we are not passing
any authorisation information. For the K8s Api we are connecting as
`system:anonymous`{.copy-on-click}, which should not have permission to
perform the query. We want to change this and connect using the *Pod\'s*
*ServiceAccount* named `secret-reader`{.copy-on-click}.

 

###### Use ServiceAccount Token to authenticate

We find the token at
`/var/run/secrets/kubernetes.io/serviceaccount`{.copy-on-click}, so we
do:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term" style="break-inside: unset;"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ / # TOKEN=$(cat /var/run/secrets/kubernetes.io/serviceaccount/token)
```
:::

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ / # curl -k https://kubernetes.default/api/v1/secrets -H "Authorization: Bearer ${TOKEN}"
```

``` {.CodeMirror-line role="presentation"}
{
```

``` {.CodeMirror-line role="presentation"}
  "kind": "SecretList",
```

``` {.CodeMirror-line role="presentation"}
  "apiVersion": "v1",
```

``` {.CodeMirror-line role="presentation"}
  "metadata": {
```

``` {.CodeMirror-line role="presentation"}
    "resourceVersion": "4881"
```

``` {.CodeMirror-line role="presentation"}
  },
```

``` {.CodeMirror-line role="presentation"}
  "items": [
```

``` {.CodeMirror-line role="presentation"}
    {
```

``` {.CodeMirror-line role="presentation"}
...
```

``` {.CodeMirror-line role="presentation"}
    {
```

``` {.CodeMirror-line role="presentation"}
      "metadata": {
```

``` {.CodeMirror-line role="presentation"}
        "name": "read-me",
```

``` {.CodeMirror-line role="presentation"}
        "namespace": "project-swan",
```

``` {.CodeMirror-line role="presentation"}
...
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 391px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 391px;"}
:::

Now we\'re able to list all *Secrets* as the *Pod\'s* *ServiceAccount*
`secret-reader`{.copy-on-click}.

For troubleshooting we could also check if the *ServiceAccount* is
actually able to list *Secrets*:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka9412:~$ k auth can-i get secret --as system:serviceaccount:project-swan:secret-reader
```
:::

``` {.CodeMirror-line role="presentation"}
yes
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 46px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 46px;"}
:::

 

###### Store result at requested location

We write the full result into
`/opt/course/9/result.json`{.copy-on-click}:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="" style="break-inside: unset;"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
# cka9412:/opt/course/9/result.json
```
:::

``` {.CodeMirror-line role="presentation"}
{
```

``` {.CodeMirror-line role="presentation"}
  "kind": "SecretList",
```

``` {.CodeMirror-line role="presentation"}
  "apiVersion": "v1",
```

``` {.CodeMirror-line role="presentation"}
  "metadata": {
```

``` {.CodeMirror-line role="presentation"}
    "resourceVersion": "4881"
```

``` {.CodeMirror-line role="presentation"}
  },
```

``` {.CodeMirror-line role="presentation"}
  "items": [
```

``` {.CodeMirror-line role="presentation"}
    {
```

``` {.CodeMirror-line role="presentation"}
...
```

``` {.CodeMirror-line role="presentation"}
    {
```

``` {.CodeMirror-line role="presentation"}
      "metadata": {
```

``` {.CodeMirror-line role="presentation"}
        "name": "read-me",
```

``` {.CodeMirror-line role="presentation"}
        "namespace": "project-swan",
```

``` {.CodeMirror-line role="presentation"}
        "uid": "f7c9a279-9609-4f9a-aa30-d29e175b7a6c",
```

``` {.CodeMirror-line role="presentation"}
        "resourceVersion": "3380",
```

``` {.CodeMirror-line role="presentation"}
        "creationTimestamp": "2024-12-05T15:11:58Z",
```

``` {.CodeMirror-line role="presentation"}
        "managedFields": [
```

``` {.CodeMirror-line role="presentation"}
          {
```

``` {.CodeMirror-line role="presentation"}
            "manager": "kubectl-create",
```

``` {.CodeMirror-line role="presentation"}
            "operation": "Update",
```

``` {.CodeMirror-line role="presentation"}
            "apiVersion": "v1",
```

``` {.CodeMirror-line role="presentation"}
            "time": "2024-12-05T15:11:58Z",
```

``` {.CodeMirror-line role="presentation"}
            "fieldsType": "FieldsV1",
```

``` {.CodeMirror-line role="presentation"}
            "fieldsV1": {
```

``` {.CodeMirror-line role="presentation"}
              "f:data": {
```

``` {.CodeMirror-line role="presentation"}
                ".": {},
```

``` {.CodeMirror-line role="presentation"}
                "f:token": {}
```

``` {.CodeMirror-line role="presentation"}
              },
```

``` {.CodeMirror-line role="presentation"}
              "f:type": {}
```

``` {.CodeMirror-line role="presentation"}
            }
```

``` {.CodeMirror-line role="presentation"}
          }
```

``` {.CodeMirror-line role="presentation"}
        ]
```

``` {.CodeMirror-line role="presentation"}
      },
```

``` {.CodeMirror-line role="presentation"}
      "data": {
```

``` {.CodeMirror-line role="presentation"}
        "token": "ZjMyMDEzOTYtZjVkOC00NTg0LWE2ZjEtNmYyZGZkYjM4NzVl"
```

``` {.CodeMirror-line role="presentation"}
      },
```

``` {.CodeMirror-line role="presentation"}
      "type": "Opaque"
```

``` {.CodeMirror-line role="presentation"}
    }
```

``` {.CodeMirror-line role="presentation"}
  ]
```

``` {.CodeMirror-line role="presentation"}
}
```

``` {.CodeMirror-line role="presentation"}
...
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 966px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 966px;"}
:::

The easiest way would probably be to copy and paste the result manually.
But if it\'s too long or not possible we could also do:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ / # curl -k https://kubernetes.default/api/v1/secrets -H "Authorization: Bearer ${TOKEN}" > result.json
```
:::

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ / # exit
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka9412:~$ k -n project-swan exec api-contact -it -- cat result.json > /opt/course/9/result.json
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 115px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 115px;"}
:::

 

###### Connect via HTTPS with correct CA

To connect without `curl -k`{.copy-on-click} we can specify the
CertificateAuthority (CA):

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ / # CACERT=/var/run/secrets/kubernetes.io/serviceaccount/ca.crt
```
:::

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ / # curl --cacert ${CACERT} https://kubernetes.default/api/v1/secrets -H "Authorization: Bearer ${TOKEN}"
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 69px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 69px;"}
:::

 

 

## Question 10 \| RBAC ServiceAccount Role RoleBinding {#question-10-|-rbac-serviceaccount-role-rolebinding}

 

Solve this question on: `ssh cka3962`{.copy-on-click}

 

Create a new *ServiceAccount* `processor`{.copy-on-click} in *Namespace*
`project-hamster`{.copy-on-click}. Create a *Role* and *RoleBinding*,
both named `processor`{.copy-on-click} as well. These should allow the
new *SA* to only create *Secrets* and *ConfigMaps* in that *Namespace*.

 

##### Answer: {#answer-10}

###### Let\'s talk a little about RBAC resources

A *ClusterRole*\|*Role* defines a set of permissions and **where it is
available**, in the whole cluster or just a single *Namespace*.

A *ClusterRoleBinding*\|*RoleBinding* connects a set of permissions with
an account and defines **where it is applied**, in the whole cluster or
just a single *Namespace*.

Because of this there are 4 different RBAC combinations and 3 valid
ones:

1.  *Role* + *RoleBinding* (available in single *Namespace*, applied in
    single *Namespace*)

2.  *ClusterRole* + *ClusterRoleBinding* (available cluster-wide,
    applied cluster-wide)

3.  *ClusterRole* + *RoleBinding* (available cluster-wide, applied in
    single *Namespace*)

4.  *Role* + *ClusterRoleBinding* (**NOT POSSIBLE:** available in single
    *Namespace*, applied cluster-wide)

###### To the solution

We first create the *ServiceAccount*:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ ssh cka3962
```
:::

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka3962:~$ k -n project-hamster create sa processor
```

``` {.CodeMirror-line role="presentation"}
serviceaccount/processor created
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 92px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 92px;"}
:::

For the *Role* we can first view examples:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="bash"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
k -n project-hamster create role -h
```
:::
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 23px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 23px;"}
:::

So we execute:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka3962:~$ k -n project-hamster create role processor --verb=create --resource=secret --resource=configmap
```
:::

``` {.CodeMirror-line role="presentation"}
role.rbac.authorization.k8s.io/processor created
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 69px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 69px;"}
:::

Which will create a *Role* like:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="yaml" style="break-inside: unset;"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
# kubectl -n project-hamster create role processor --verb=create --resource=secret --resource=configmap
```
:::

``` {.CodeMirror-line role="presentation"}
apiVersion: rbac.authorization.k8s.io/v1
```

``` {.CodeMirror-line role="presentation"}
kind: Role
```

``` {.CodeMirror-line role="presentation"}
metadata:
```

``` {.CodeMirror-line role="presentation"}
  name: processor
```

``` {.CodeMirror-line role="presentation"}
  namespace: project-hamster
```

``` {.CodeMirror-line role="presentation"}
rules:
```

``` {.CodeMirror-line role="presentation"}
- apiGroups:
```

``` {.CodeMirror-line role="presentation"}
  - ""
```

``` {.CodeMirror-line role="presentation"}
  resources:
```

``` {.CodeMirror-line role="presentation"}
  - secrets
```

``` {.CodeMirror-line role="presentation"}
  - configmaps
```

``` {.CodeMirror-line role="presentation"}
  verbs:
```

``` {.CodeMirror-line role="presentation"}
  - create
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 280px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 280px;"}
:::

Now we bind the *Role* to the *ServiceAccount*, and for this we can also
view examples:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="bash"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
k -n project-hamster create rolebinding -h # examples
```
:::
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 23px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 23px;"}
:::

So we create it:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka3962:~$ k -n project-hamster create rolebinding processor --role processor --serviceaccount project-hamster:processor
```
:::

``` {.CodeMirror-line role="presentation"}
rolebinding.rbac.authorization.k8s.io/processor created
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 69px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 69px;"}
:::

This will create a *RoleBinding* like:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="yaml" style="break-inside: unset;"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
# kubectl -n project-hamster create rolebinding processor --role processor --serviceaccount project-hamster:processor
```
:::

``` {.CodeMirror-line role="presentation"}
apiVersion: rbac.authorization.k8s.io/v1
```

``` {.CodeMirror-line role="presentation"}
kind: RoleBinding
```

``` {.CodeMirror-line role="presentation"}
metadata:
```

``` {.CodeMirror-line role="presentation"}
  name: processor
```

``` {.CodeMirror-line role="presentation"}
  namespace: project-hamster
```

``` {.CodeMirror-line role="presentation"}
roleRef:
```

``` {.CodeMirror-line role="presentation"}
  apiGroup: rbac.authorization.k8s.io
```

``` {.CodeMirror-line role="presentation"}
  kind: Role
```

``` {.CodeMirror-line role="presentation"}
  name: processor
```

``` {.CodeMirror-line role="presentation"}
subjects:
```

``` {.CodeMirror-line role="presentation"}
- kind: ServiceAccount
```

``` {.CodeMirror-line role="presentation"}
  name: processor
```

``` {.CodeMirror-line role="presentation"}
  namespace: project-hamster
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 300px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 300px;"}
:::

To test our RBAC setup we can use `kubectl auth can-i`{.copy-on-click}:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="bash"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
k auth can-i -h # examples
```
:::
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 23px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 23px;"}
:::

Like this:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term" style="break-inside: unset;"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka3962:~$ k -n project-hamster auth can-i create secret --as system:serviceaccount:project-hamster:processor
```
:::

``` {.CodeMirror-line role="presentation"}
yes
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka3962:~$ k -n project-hamster auth can-i create configmap --as system:serviceaccount:project-hamster:processor
```

``` {.CodeMirror-line role="presentation"}
yes
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka3962:~$ k -n project-hamster auth can-i create pod --as system:serviceaccount:project-hamster:processor
```

``` {.CodeMirror-line role="presentation"}
no
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka3962:~$ k -n project-hamster auth can-i delete secret --as system:serviceaccount:project-hamster:processor
```

``` {.CodeMirror-line role="presentation"}
no
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka3962:~$ k -n project-hamster auth can-i get configmap --as system:serviceaccount:project-hamster:processor
```

``` {.CodeMirror-line role="presentation"}
no
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 437px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 437px;"}
:::

Done.

 

 

## Question 11 \| DaemonSet on all Nodes {#question-11-|-daemonset-on-all-nodes}

 

Solve this question on: `ssh cka2556`{.copy-on-click}

 

Use *Namespace* `project-tiger`{.copy-on-click} for the following.
Create a *DaemonSet* named `ds-important`{.copy-on-click} with image
`httpd:2-alpine`{.copy-on-click} and labels
`id=ds-important`{.copy-on-click} and
`uuid=18426a0b-5f59-4e10-923f-c0e078e82462`{.copy-on-click}. The *Pods*
it creates should request 10 millicore cpu and 10 mebibyte memory. The
*Pods* of that *DaemonSet* should run on all nodes, also controlplanes.

 

##### Answer: {#answer-11}

As of now we aren\'t able to create a *DaemonSet* directly using
`kubectl`{.copy-on-click}, so we create a *Deployment* and just change
it up:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ ssh cka2556
```
:::

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka2556:~$ k -n project-tiger create deployment --image=httpd:2.4-alpine ds-important --dry-run=client -o yaml > 11.yaml
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 92px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 92px;"}
:::

Or we could search for a *DaemonSet* example yaml in the K8s docs and
alter it to our needs.

We adjust the yaml to:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="yaml" style="break-inside: unset;"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
# cka2556:/home/candidate/11.yaml
```
:::

``` {.CodeMirror-line role="presentation"}
apiVersion: apps/v1
```

``` {.CodeMirror-line role="presentation"}
kind: DaemonSet                                     # change from Deployment to Daemonset
```

``` {.CodeMirror-line role="presentation"}
metadata:
```

``` {.CodeMirror-line role="presentation"}
  creationTimestamp: null
```

``` {.CodeMirror-line role="presentation"}
  labels:                                           # add
```

``` {.CodeMirror-line role="presentation"}
    id: ds-important                                # add
```

``` {.CodeMirror-line role="presentation"}
    uuid: 18426a0b-5f59-4e10-923f-c0e078e82462      # add
```

``` {.CodeMirror-line role="presentation"}
  name: ds-important
```

``` {.CodeMirror-line role="presentation"}
  namespace: project-tiger                          # important
```

``` {.CodeMirror-line role="presentation"}
spec:
```

``` {.CodeMirror-line role="presentation"}
  #replicas: 1                                      # remove
```

``` {.CodeMirror-line role="presentation"}
  selector:
```

``` {.CodeMirror-line role="presentation"}
    matchLabels:
```

``` {.CodeMirror-line role="presentation"}
      id: ds-important                              # add
```

``` {.CodeMirror-line role="presentation"}
      uuid: 18426a0b-5f59-4e10-923f-c0e078e82462    # add
```

``` {.CodeMirror-line role="presentation"}
  #strategy: {}                                     # remove
```

``` {.CodeMirror-line role="presentation"}
  template:
```

``` {.CodeMirror-line role="presentation"}
    metadata:
```

``` {.CodeMirror-line role="presentation"}
      creationTimestamp: null
```

``` {.CodeMirror-line role="presentation"}
      labels:
```

``` {.CodeMirror-line role="presentation"}
        id: ds-important                            # add
```

``` {.CodeMirror-line role="presentation"}
        uuid: 18426a0b-5f59-4e10-923f-c0e078e82462  # add
```

``` {.CodeMirror-line role="presentation"}
    spec:
```

``` {.CodeMirror-line role="presentation"}
      containers:
```

``` {.CodeMirror-line role="presentation"}
      - image: httpd:2-alpine
```

``` {.CodeMirror-line role="presentation"}
        name: ds-important
```

``` {.CodeMirror-line role="presentation"}
        resources:
```

``` {.CodeMirror-line role="presentation"}
          requests:                                 # add
```

``` {.CodeMirror-line role="presentation"}
            cpu: 10m                                # add
```

``` {.CodeMirror-line role="presentation"}
            memory: 10Mi                            # add
```

``` {.CodeMirror-line role="presentation"}
      tolerations:                                  # add
```

``` {.CodeMirror-line role="presentation"}
      - effect: NoSchedule                          # add
```

``` {.CodeMirror-line role="presentation"}
        key: node-role.kubernetes.io/control-plane  # add
```

``` {.CodeMirror-line role="presentation"}
#status: {}                                         # remove
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 700px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 700px;"}
:::

It was requested that the *DaemonSet* runs on all nodes, so we need to
specify the toleration for this.

Let\'s give it a go:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka2556:~$ k -f 11.yaml create
```
:::

``` {.CodeMirror-line role="presentation"}
daemonset.apps/ds-important created
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka2556:~$ k -n project-tiger get ds
```

``` {.CodeMirror-line role="presentation"}
NAME           DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
```

``` {.CodeMirror-line role="presentation"}
ds-important   3         3         3       3            3           <none>          8s
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka2556:~$ k -n project-tiger get pod -l id=ds-important -o wide
```

``` {.CodeMirror-line role="presentation"}
NAME                 READY   STATUS    ...    NODE            ...
```

``` {.CodeMirror-line role="presentation"}
ds-important-26456   1/1     Running   ...    cka2556-node2   ...
```

``` {.CodeMirror-line role="presentation"}
ds-important-wnt5p   1/1     Running   ...    cka2556         ...
```

``` {.CodeMirror-line role="presentation"}
ds-important-wrbjd   1/1     Running   ...    cka2556-node1   ...
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 276px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 276px;"}
:::

Above we can see one *Pod* on each node, including the controlplane one.

 

 

## Question 12 \| Deployment on all Nodes {#question-12-|-deployment-on-all-nodes}

 

Solve this question on: `ssh cka2556`{.copy-on-click}

 

Implement the following in *Namespace* `project-tiger`{.copy-on-click}:

-   Create a *Deployment* named `deploy-important`{.copy-on-click} with
    `3`{.copy-on-click} replicas

-   The *Deployment* and its *Pods* should have label
    `id=very-important`{.copy-on-click}

-   First container named `container1`{.copy-on-click} with image
    `nginx:1-alpine`{.copy-on-click}

-   Second container named `container2`{.copy-on-click} with image
    `google/pause`{.copy-on-click}

-   There should only ever be **one** *Pod* of that *Deployment* running
    on **one** worker node, use
    `topologyKey: kubernetes.io/hostname`{.copy-on-click} for this

 

> ℹ️ Because there are two worker nodes and the *Deployment* has three
> replicas the result should be that the third *Pod* won\'t be
> scheduled. In a way this scenario simulates the behaviour of a
> *DaemonSet*, but using a *Deployment* with a fixed number of replicas

 

 

##### Answer: {#answer-12}

There are two possible ways, one using `podAntiAffinity`{.copy-on-click}
and one using `topologySpreadConstraint`{.copy-on-click}.

 

###### PodAntiAffinity

The idea here is that we create a \"Inter-pod anti-affinity\" which
allows us to say a *Pod* should only be scheduled on a node where
another *Pod* of a specific label (here the same label) is not already
running.

Let\'s begin by creating the *Deployment* template:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ ssh cka2556
```
:::

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka2556:~$ k -n project-tiger create deployment --image=nginx:1-alpine deploy-important --dry-run=client -o yaml > 12.yaml
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 92px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 92px;"}
:::

Then change the yaml to:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="yaml" style="break-inside: unset;"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
# cka2556:/home/candidate/12.yaml
```
:::

``` {.CodeMirror-line role="presentation"}
apiVersion: apps/v1
```

``` {.CodeMirror-line role="presentation"}
kind: Deployment
```

``` {.CodeMirror-line role="presentation"}
metadata:
```

``` {.CodeMirror-line role="presentation"}
  creationTimestamp: null
```

``` {.CodeMirror-line role="presentation"}
  labels:
```

``` {.CodeMirror-line role="presentation"}
    id: very-important                  # change
```

``` {.CodeMirror-line role="presentation"}
  name: deploy-important
```

``` {.CodeMirror-line role="presentation"}
  namespace: project-tiger              # important
```

``` {.CodeMirror-line role="presentation"}
spec:
```

``` {.CodeMirror-line role="presentation"}
  replicas: 3                           # change
```

``` {.CodeMirror-line role="presentation"}
  selector:
```

``` {.CodeMirror-line role="presentation"}
    matchLabels:
```

``` {.CodeMirror-line role="presentation"}
      id: very-important                # change
```

``` {.CodeMirror-line role="presentation"}
  strategy: {}
```

``` {.CodeMirror-line role="presentation"}
  template:
```

``` {.CodeMirror-line role="presentation"}
    metadata:
```

``` {.CodeMirror-line role="presentation"}
      creationTimestamp: null
```

``` {.CodeMirror-line role="presentation"}
      labels:
```

``` {.CodeMirror-line role="presentation"}
        id: very-important              # change
```

``` {.CodeMirror-line role="presentation"}
    spec:
```

``` {.CodeMirror-line role="presentation"}
      containers:
```

``` {.CodeMirror-line role="presentation"}
      - image: nginx:1-alpine
```

``` {.CodeMirror-line role="presentation"}
        name: container1                # change
```

``` {.CodeMirror-line role="presentation"}
        resources: {}
```

``` {.CodeMirror-line role="presentation"}
      - image: google/pause             # add
```

``` {.CodeMirror-line role="presentation"}
        name: container2                # add
```

``` {.CodeMirror-line role="presentation"}
      affinity:                                             # add
```

``` {.CodeMirror-line role="presentation"}
        podAntiAffinity:                                    # add
```

``` {.CodeMirror-line role="presentation"}
          requiredDuringSchedulingIgnoredDuringExecution:   # add
```

``` {.CodeMirror-line role="presentation"}
          - labelSelector:                                  # add
```

``` {.CodeMirror-line role="presentation"}
              matchExpressions:                             # add
```

``` {.CodeMirror-line role="presentation"}
              - key: id                                     # add
```

``` {.CodeMirror-line role="presentation"}
                operator: In                                # add
```

``` {.CodeMirror-line role="presentation"}
                values:                                     # add
```

``` {.CodeMirror-line role="presentation"}
                - very-important                            # add
```

``` {.CodeMirror-line role="presentation"}
            topologyKey: kubernetes.io/hostname             # add
```

``` {.CodeMirror-line role="presentation"}
status: {}
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 760px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 760px;"}
:::

Specify a topologyKey, which is a pre-populated Kubernetes label, you
can find this by describing a node.

 

###### TopologySpreadConstraints

We can achieve the same with
`topologySpreadConstraints`{.copy-on-click}. Best to try out and play
with both.

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="yaml" style="break-inside: unset;"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
# cka2556:/home/candidate/12.yaml
```
:::

``` {.CodeMirror-line role="presentation"}
apiVersion: apps/v1
```

``` {.CodeMirror-line role="presentation"}
kind: Deployment
```

``` {.CodeMirror-line role="presentation"}
metadata:
```

``` {.CodeMirror-line role="presentation"}
  creationTimestamp: null
```

``` {.CodeMirror-line role="presentation"}
  labels:
```

``` {.CodeMirror-line role="presentation"}
    id: very-important                  # change
```

``` {.CodeMirror-line role="presentation"}
  name: deploy-important
```

``` {.CodeMirror-line role="presentation"}
  namespace: project-tiger              # important
```

``` {.CodeMirror-line role="presentation"}
spec:
```

``` {.CodeMirror-line role="presentation"}
  replicas: 3                           # change
```

``` {.CodeMirror-line role="presentation"}
  selector:
```

``` {.CodeMirror-line role="presentation"}
    matchLabels:
```

``` {.CodeMirror-line role="presentation"}
      id: very-important                # change
```

``` {.CodeMirror-line role="presentation"}
  strategy: {}
```

``` {.CodeMirror-line role="presentation"}
  template:
```

``` {.CodeMirror-line role="presentation"}
    metadata:
```

``` {.CodeMirror-line role="presentation"}
      creationTimestamp: null
```

``` {.CodeMirror-line role="presentation"}
      labels:
```

``` {.CodeMirror-line role="presentation"}
        id: very-important              # change
```

``` {.CodeMirror-line role="presentation"}
    spec:
```

``` {.CodeMirror-line role="presentation"}
      containers:
```

``` {.CodeMirror-line role="presentation"}
      - image: nginx:1-alpine
```

``` {.CodeMirror-line role="presentation"}
        name: container1                # change
```

``` {.CodeMirror-line role="presentation"}
        resources: {}
```

``` {.CodeMirror-line role="presentation"}
      - image: google/pause             # add
```

``` {.CodeMirror-line role="presentation"}
        name: container2                # add
```

``` {.CodeMirror-line role="presentation"}
      topologySpreadConstraints:                 # add
```

``` {.CodeMirror-line role="presentation"}
      - maxSkew: 1                               # add
```

``` {.CodeMirror-line role="presentation"}
        topologyKey: kubernetes.io/hostname      # add
```

``` {.CodeMirror-line role="presentation"}
        whenUnsatisfiable: DoNotSchedule         # add
```

``` {.CodeMirror-line role="presentation"}
        labelSelector:                           # add
```

``` {.CodeMirror-line role="presentation"}
          matchLabels:                           # add
```

``` {.CodeMirror-line role="presentation"}
            id: very-important                   # add
```

``` {.CodeMirror-line role="presentation"}
status: {}
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 700px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 700px;"}
:::

 

###### Apply and Run

Let\'s run it:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka2556:~$ k -f 12.yaml create
```
:::

``` {.CodeMirror-line role="presentation"}
deployment.apps/deploy-important created
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 46px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 46px;"}
:::

Then we check the *Deployment* status where it shows 2/3 ready count:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka2556:~$ k -n project-tiger get deploy -l id=very-important
```
:::

``` {.CodeMirror-line role="presentation"}
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
```

``` {.CodeMirror-line role="presentation"}
deploy-important   2/3     3            2           19s
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 69px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 69px;"}
:::

And running the following we see one *Pod* on each worker node and one
not scheduled.

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka2556:~$ k -n project-tiger get pod -o wide -l id=very-important
```
:::

``` {.CodeMirror-line role="presentation"}
NAME                                READY   STATUS   ...   IP           NODE
```

``` {.CodeMirror-line role="presentation"}
deploy-important-78f98b75f9-5s6js   0/2     Pending  ...   <none>       <none>
```

``` {.CodeMirror-line role="presentation"}
deploy-important-78f98b75f9-657hx   2/2     Running  ...   10.44.0.33   cka2556-node1
```

``` {.CodeMirror-line role="presentation"}
deploy-important-78f98b75f9-9bz8q   2/2     Running  ...   10.36.0.20   cka2556-node2
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 115px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 115px;"}
:::

If we kubectl describe the not scheduled *Pod* it will show us the
reason `didn't match pod anti-affinity rules`{.copy-on-click}:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang=""}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
Warning  FailedScheduling  119s (x2 over 2m1s)  default-scheduler  0/3 nodes are available: 1 node(s) had untolerated taint {node-role.kubernetes.io/control-plane: }, 2 node(s) didn't match pod anti-affinity rules. preemption: 0/3 nodes are available: 1 Preemption is not helpful for scheduling, 2 No preemption victims found for incoming pod.
```
:::
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 92px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 92px;"}
:::

Or our topologySpreadConstraints reason
`didn't match pod topology spread constraints`{.copy-on-click}:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang=""}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
Warning  FailedScheduling  20s (x2 over 22s)  default-scheduler  0/3 nodes are available: 1 node(s) had untolerated taint {node-role.kubernetes.io/control-plane: }, 2 node(s) didn't match pod topology spread constraints. preemption: 0/3 nodes are available: 1 Preemption is not helpful for scheduling, 2 No preemption victims found for incoming pod.
```
:::
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 92px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 92px;"}
:::

 

 

## Question 13 \| Gateway Api Ingress {#question-13-|-gateway-api-ingress}

 

Solve this question on: `ssh cka7968`{.copy-on-click}

 

The team from Project r500 wants to replace their Ingress
(networking.k8s.io) with a Gateway Api (gateway.networking.k8s.io)
solution. The old Ingress is available at
`/opt/course/13/ingress.yaml`{.copy-on-click}.

Perform the following in *Namespace* `project-r500`{.copy-on-click} and
for the already existing *Gateway*:

1.  Create a new *HTTPRoute* named `traffic-director`{.copy-on-click}
    which replicates the routes from the old Ingress

2.  Extend the new *HTTPRoute* with path `/auto`{.copy-on-click} which
    redirects to mobile if the User-Agent is exactly
    `mobile`{.copy-on-click} and to desktop otherwise

The existing *Gateway* is reachable at
`http://r500.gateway:30080`{.copy-on-click} which means your
implementation should work for these commands:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang=""}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
curl r500.gateway:30080/desktop
```
:::

``` {.CodeMirror-line role="presentation"}
curl r500.gateway:30080/mobile
```

``` {.CodeMirror-line role="presentation"}
curl r500.gateway:30080/auto -H "User-Agent: mobile" 
```

``` {.CodeMirror-line role="presentation"}
curl r500.gateway:30080/auto
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 92px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 92px;"}
:::

 

 

##### Answer: {#answer-13}

Comparing for example the older *Ingress* (networking.k8s.io/v1) and
newer *HTTPRoute* (gateway.networking.k8s.io/v1) *CRDs* then they look
quite similar in what they offer. They have a different config structure
but provide the same idea of functionality.

The magic of the Gateway Api comes more to shine because of further
resources (*GRPCRoute*, *TCPRoute*) and the architecture which is
designed to be more flexible and extendable. This will provide better
integration into existing cloud infrastructure and providers like GCP or
AWS will be able to develop their own Gateway Api implementations.

 

###### Investigate CRDs

It was mentioned that a *Gateway* already exists, let\'s verify this:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term" style="break-inside: unset;"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ ssh cka7968
```
:::

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka7968:~$ k get crd
```

``` {.CodeMirror-line role="presentation"}
NAME                                        CREATED AT
```

``` {.CodeMirror-line role="presentation"}
clientsettingspolicies.gateway.nginx.org    2024-12-28T13:11:21Z
```

``` {.CodeMirror-line role="presentation"}
gatewayclasses.gateway.networking.k8s.io    2024-12-28T13:11:21Z
```

``` {.CodeMirror-line role="presentation"}
gateways.gateway.networking.k8s.io          2024-12-28T13:11:21Z
```

``` {.CodeMirror-line role="presentation"}
grpcroutes.gateway.networking.k8s.io        2024-12-28T13:11:21Z
```

``` {.CodeMirror-line role="presentation"}
httproutes.gateway.networking.k8s.io        2024-12-28T13:11:22Z
```

``` {.CodeMirror-line role="presentation"}
nginxgateways.gateway.nginx.org             2024-12-28T13:11:23Z
```

``` {.CodeMirror-line role="presentation"}
nginxproxies.gateway.nginx.org              2024-12-28T13:11:23Z
```

``` {.CodeMirror-line role="presentation"}
observabilitypolicies.gateway.nginx.org     2024-12-28T13:11:23Z
```

``` {.CodeMirror-line role="presentation"}
referencegrants.gateway.networking.k8s.io   2024-12-28T13:11:23Z
```

``` {.CodeMirror-line role="presentation"}
snippetsfilters.gateway.nginx.org           2024-12-28T13:11:23Z
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka7968:~$ k get gateway -A
```

``` {.CodeMirror-line role="presentation"}
NAMESPACE      NAME   CLASS   ADDRESS   PROGRAMMED   AGE
```

``` {.CodeMirror-line role="presentation"}
project-r500   main   nginx             True         2m
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka7968:~$ k get gatewayclass -A
```

``` {.CodeMirror-line role="presentation"}
NAME    CONTROLLER                                   ACCEPTED   AGE
```

``` {.CodeMirror-line role="presentation"}
nginx   gateway.nginx.org/nginx-gateway-controller   True       2m12s
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 506px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 506px;"}
:::

We can see that various *CRDs* from gateway.networking.k8s.io are
available. In this scenario we\'ll only work directly with *HTTPRoute*
which we need to create. It will reference the existing *Gateway*
`main`{.copy-on-click} which references the existing *GatewayClass*
`nginx`{.copy-on-click}:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term" style="break-inside: unset;"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka7968:~$ k -n project-r500 get gateway main -oyaml
```
:::

``` {.CodeMirror-line role="presentation"}
apiVersion: gateway.networking.k8s.io/v1
```

``` {.CodeMirror-line role="presentation"}
kind: Gateway
```

``` {.CodeMirror-line role="presentation"}
metadata:
```

``` {.CodeMirror-line role="presentation"}
...
```

``` {.CodeMirror-line role="presentation"}
  name: main
```

``` {.CodeMirror-line role="presentation"}
  namespace: project-r500
```

``` {.CodeMirror-line role="presentation"}
spec:
```

``` {.CodeMirror-line role="presentation"}
  gatewayClassName: nginx
```

``` {.CodeMirror-line role="presentation"}
  listeners:
```

``` {.CodeMirror-line role="presentation"}
  - allowedRoutes:
```

``` {.CodeMirror-line role="presentation"}
      namespaces:
```

``` {.CodeMirror-line role="presentation"}
        from: Same
```

``` {.CodeMirror-line role="presentation"}
    name: http
```

``` {.CodeMirror-line role="presentation"}
    port: 80
```

``` {.CodeMirror-line role="presentation"}
    protocol: HTTP
```

``` {.CodeMirror-line role="presentation"}
...
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 391px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 391px;"}
:::

 

###### Investigate URL reachability

We can already contact the *Gateway* like this:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka7968:~$ curl r500.gateway:30080
```
:::

``` {.CodeMirror-line role="presentation"}
<html>
```

``` {.CodeMirror-line role="presentation"}
<head><title>404 Not Found</title></head>
```

``` {.CodeMirror-line role="presentation"}
<body>
```

``` {.CodeMirror-line role="presentation"}
<center><h1>404 Not Found</h1></center>
```

``` {.CodeMirror-line role="presentation"}
<hr><center>nginx</center>
```

``` {.CodeMirror-line role="presentation"}
</body>
```

``` {.CodeMirror-line role="presentation"}
</html>
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 184px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 184px;"}
:::

We receive a 404 because no routes have been defined yet. We receive
this 404 from a Nginx because the Gateway Api implementation in this
scenario has been done via the Nginx Gateway Fabric. But for this
scenario it wouldn\'t matter if another implementation (Traefik, Envoy,
\...) would\'ve been used, because all will work with the same Gateway
Api *CRDs*.

The url `r500.gateway:30080`{.copy-on-click} is reachable because of a
static entry in `/etc/hosts`{.copy-on-click} which points to the only
node in the cluster. And on that node, as well as on all others if there
would be more, port 30080 is open because of a NodePort *Service*:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka7968:~$ k -n nginx-gateway get svc
```
:::

``` {.CodeMirror-line role="presentation"}
NAME            TYPE       CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
```

``` {.CodeMirror-line role="presentation"}
nginx-gateway   NodePort   10.103.36.0   <none>        80:30080/TCP   58m
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 69px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 69px;"}
:::

 

###### Step 1 {#step-1-4}

Now we\'ll have a look at the provided *Ingress* Yaml which we need to
convert:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka7968:~$ vim /opt/course/13/ingress.yaml
```
:::
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 23px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 23px;"}
:::

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="yaml" style="break-inside: unset;"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
# cka7968:/opt/course/13/ingress.yaml
```
:::

``` {.CodeMirror-line role="presentation"}
apiVersion: networking.k8s.io/v1
```

``` {.CodeMirror-line role="presentation"}
kind: Ingress
```

``` {.CodeMirror-line role="presentation"}
metadata:
```

``` {.CodeMirror-line role="presentation"}
  name: traffic-director
```

``` {.CodeMirror-line role="presentation"}
spec:
```

``` {.CodeMirror-line role="presentation"}
  ingressClassName: nginx
```

``` {.CodeMirror-line role="presentation"}
  rules:
```

``` {.CodeMirror-line role="presentation"}
    - host: r500.gateway
```

``` {.CodeMirror-line role="presentation"}
      http:
```

``` {.CodeMirror-line role="presentation"}
        paths:
```

``` {.CodeMirror-line role="presentation"}
          - backend:
```

``` {.CodeMirror-line role="presentation"}
              service:
```

``` {.CodeMirror-line role="presentation"}
                name: web-desktop
```

``` {.CodeMirror-line role="presentation"}
                port:
```

``` {.CodeMirror-line role="presentation"}
                  number: 80
```

``` {.CodeMirror-line role="presentation"}
            path: /desktop
```

``` {.CodeMirror-line role="presentation"}
            pathType: Prefix
```

``` {.CodeMirror-line role="presentation"}
          - backend:
```

``` {.CodeMirror-line role="presentation"}
              service:
```

``` {.CodeMirror-line role="presentation"}
                name: web-mobile
```

``` {.CodeMirror-line role="presentation"}
                port:
```

``` {.CodeMirror-line role="presentation"}
                  number: 80
```

``` {.CodeMirror-line role="presentation"}
            path: /mobile
```

``` {.CodeMirror-line role="presentation"}
            pathType: Prefix
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 500px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 500px;"}
:::

We can see two paths `/desktop`{.copy-on-click} and
`/mobile`{.copy-on-click} which point to the K8s *Services*
`web-desktop`{.copy-on-click} and `web-mobile`{.copy-on-click}. Based on
this we create a *HTTPRoute* which replicates the behaviour and in which
we reference the existing *Gateway*:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="yaml" style="break-inside: unset;"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
apiVersion: gateway.networking.k8s.io/v1
```
:::

``` {.CodeMirror-line role="presentation"}
kind: HTTPRoute
```

``` {.CodeMirror-line role="presentation"}
metadata:
```

``` {.CodeMirror-line role="presentation"}
  name: traffic-director
```

``` {.CodeMirror-line role="presentation"}
  namespace: project-r500
```

``` {.CodeMirror-line role="presentation"}
spec:
```

``` {.CodeMirror-line role="presentation"}
  parentRefs:
```

``` {.CodeMirror-line role="presentation"}
    - name: main   # use the name of the existing Gateway
```

``` {.CodeMirror-line role="presentation"}
  hostnames:
```

``` {.CodeMirror-line role="presentation"}
    - "r500.gateway"
```

``` {.CodeMirror-line role="presentation"}
  rules:
```

``` {.CodeMirror-line role="presentation"}
    - matches:
```

``` {.CodeMirror-line role="presentation"}
        - path:
```

``` {.CodeMirror-line role="presentation"}
            type: PathPrefix
```

``` {.CodeMirror-line role="presentation"}
            value: /desktop
```

``` {.CodeMirror-line role="presentation"}
      backendRefs:
```

``` {.CodeMirror-line role="presentation"}
        - name: web-desktop
```

``` {.CodeMirror-line role="presentation"}
          port: 80
```

``` {.CodeMirror-line role="presentation"}
    - matches:
```

``` {.CodeMirror-line role="presentation"}
        - path:
```

``` {.CodeMirror-line role="presentation"}
            type: PathPrefix
```

``` {.CodeMirror-line role="presentation"}
            value: /mobile
```

``` {.CodeMirror-line role="presentation"}
      backendRefs:
```

``` {.CodeMirror-line role="presentation"}
        - name: web-mobile
```

``` {.CodeMirror-line role="presentation"}
          port: 80
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 500px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 500px;"}
:::

After creation we can test:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term" style="break-inside: unset;"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka7968:~$ k -n project-r500 get httproute
```
:::

``` {.CodeMirror-line role="presentation"}
NAME               HOSTNAMES       AGE
```

``` {.CodeMirror-line role="presentation"}
traffic-director   ["r500.gateway"]   7s
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka7968:~$ curl r500.gateway:30080/desktop
```

``` {.CodeMirror-line role="presentation"}
Web Desktop App
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka7968:~$ curl r500.gateway:30080/mobile
```

``` {.CodeMirror-line role="presentation"}
Web Mobile App
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka7968:~$ curl r500.gateway:30080
```

``` {.CodeMirror-line role="presentation"}
<html>
```

``` {.CodeMirror-line role="presentation"}
<head><title>404 Not Found</title></head>
```

``` {.CodeMirror-line role="presentation"}
<body>
```

``` {.CodeMirror-line role="presentation"}
<center><h1>404 Not Found</h1></center>
```

``` {.CodeMirror-line role="presentation"}
<hr><center>nginx</center>
```

``` {.CodeMirror-line role="presentation"}
</body>
```

``` {.CodeMirror-line role="presentation"}
</html>
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 414px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 414px;"}
:::

This looks like what we want!

 

###### Step 2 {#step-2-4}

Now things get more interesting and we need to add new path
`/auto`{.copy-on-click} which redirects depending on the User-Agent. The
User-Agent is handled as a HTTP header and we only have to check for the
exact value, hence we can extend our *HTTPRoute* like this:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="yaml" style="break-inside: unset;"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
apiVersion: gateway.networking.k8s.io/v1
```
:::

``` {.CodeMirror-line role="presentation"}
kind: HTTPRoute
```

``` {.CodeMirror-line role="presentation"}
metadata:
```

``` {.CodeMirror-line role="presentation"}
  name: traffic-director
```

``` {.CodeMirror-line role="presentation"}
  namespace: project-r500
```

``` {.CodeMirror-line role="presentation"}
spec:
```

``` {.CodeMirror-line role="presentation"}
  parentRefs:
```

``` {.CodeMirror-line role="presentation"}
    - name: main
```

``` {.CodeMirror-line role="presentation"}
  hostnames:
```

``` {.CodeMirror-line role="presentation"}
    - "r500.gateway"
```

``` {.CodeMirror-line role="presentation"}
  rules:
```

``` {.CodeMirror-line role="presentation"}
    - matches:
```

``` {.CodeMirror-line role="presentation"}
        - path:
```

``` {.CodeMirror-line role="presentation"}
            type: PathPrefix
```

``` {.CodeMirror-line role="presentation"}
            value: /desktop
```

``` {.CodeMirror-line role="presentation"}
      backendRefs:
```

``` {.CodeMirror-line role="presentation"}
        - name: web-desktop
```

``` {.CodeMirror-line role="presentation"}
          port: 80
```

``` {.CodeMirror-line role="presentation"}
    - matches:
```

``` {.CodeMirror-line role="presentation"}
        - path:
```

``` {.CodeMirror-line role="presentation"}
            type: PathPrefix
```

``` {.CodeMirror-line role="presentation"}
            value: /mobile
```

``` {.CodeMirror-line role="presentation"}
      backendRefs:
```

``` {.CodeMirror-line role="presentation"}
        - name: web-mobile
```

``` {.CodeMirror-line role="presentation"}
          port: 80
```

``` {.CodeMirror-line role="presentation"}
# NEW FROM HERE ON
```

``` {.CodeMirror-line role="presentation"}
    - matches:
```

``` {.CodeMirror-line role="presentation"}
        - path:
```

``` {.CodeMirror-line role="presentation"}
            type: PathPrefix
```

``` {.CodeMirror-line role="presentation"}
            value: /auto
```

``` {.CodeMirror-line role="presentation"}
          headers:
```

``` {.CodeMirror-line role="presentation"}
          - type: Exact
```

``` {.CodeMirror-line role="presentation"}
            name: user-agent
```

``` {.CodeMirror-line role="presentation"}
            value: mobile
```

``` {.CodeMirror-line role="presentation"}
      backendRefs:
```

``` {.CodeMirror-line role="presentation"}
        - name: web-mobile
```

``` {.CodeMirror-line role="presentation"}
          port: 80
```

``` {.CodeMirror-line role="presentation"}
    - matches:
```

``` {.CodeMirror-line role="presentation"}
        - path:
```

``` {.CodeMirror-line role="presentation"}
            type: PathPrefix
```

``` {.CodeMirror-line role="presentation"}
            value: /auto
```

``` {.CodeMirror-line role="presentation"}
      backendRefs:
```

``` {.CodeMirror-line role="presentation"}
        - name: web-desktop
```

``` {.CodeMirror-line role="presentation"}
          port: 80
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 880px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 880px;"}
:::

We added two new rules, the first redirects to mobile conditionally on
header value and the second redirects to desktop.

If the question text mentions something like \"add one new path /auth\"
then this doesn\'t necessarily mean just one entry in the rules array,
it can depend on conditions. We added at first the following rule:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="yaml"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
    - matches:
```
:::

``` {.CodeMirror-line role="presentation"}
        - path:
```

``` {.CodeMirror-line role="presentation"}
            type: PathPrefix
```

``` {.CodeMirror-line role="presentation"}
            value: /auto
```

``` {.CodeMirror-line role="presentation"}
          headers:
```

``` {.CodeMirror-line role="presentation"}
          - type: Exact
```

``` {.CodeMirror-line role="presentation"}
            name: user-agent
```

``` {.CodeMirror-line role="presentation"}
            value: mobile
```

``` {.CodeMirror-line role="presentation"}
      backendRefs:
```

``` {.CodeMirror-line role="presentation"}
        - name: web-mobile
```

``` {.CodeMirror-line role="presentation"}
          port: 80
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 220px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 220px;"}
:::

Note that we use `- path:`{.copy-on-click} and
`header:`{.copy-on-click}, not `- path:`{.copy-on-click} and
`- header:`{.copy-on-click}. This means both `path`{.copy-on-click} and
`header`{.copy-on-click} will be connected **AND**. So only if the path
is `/auto`{.copy-on-click} **AND** the header user-agent is
`mobile`{.copy-on-click} we route to mobile.

If we would do the following then these would be connected **OR** and it
would be wrong for this question:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="yaml"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
# WRONG EXAMPLE for explanation
```
:::

``` {.CodeMirror-line role="presentation"}
    - matches:
```

``` {.CodeMirror-line role="presentation"}
        - path:
```

``` {.CodeMirror-line role="presentation"}
            type: PathPrefix
```

``` {.CodeMirror-line role="presentation"}
            value: /auto
```

``` {.CodeMirror-line role="presentation"}
        - headers:            # WRONG because now path and header are connected OR
```

``` {.CodeMirror-line role="presentation"}
          - type: Exact
```

``` {.CodeMirror-line role="presentation"}
            name: user-agent
```

``` {.CodeMirror-line role="presentation"}
            value: mobile
```

``` {.CodeMirror-line role="presentation"}
      backendRefs:
```

``` {.CodeMirror-line role="presentation"}
        - name: web-mobile
```

``` {.CodeMirror-line role="presentation"}
          port: 80
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 240px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 240px;"}
:::

The next rule we added is the one for desktop, at the very end:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="yaml"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
    - matches:
```
:::

``` {.CodeMirror-line role="presentation"}
        - path:
```

``` {.CodeMirror-line role="presentation"}
            type: PathPrefix
```

``` {.CodeMirror-line role="presentation"}
            value: /auto
```

``` {.CodeMirror-line role="presentation"}
      backendRefs:
```

``` {.CodeMirror-line role="presentation"}
        - name: web-desktop
```

``` {.CodeMirror-line role="presentation"}
          port: 80
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 140px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 140px;"}
:::

In this one we don\'t have to check any header value again because the
question required that \"otherwise\" traffic should be redirected to
desktop. So this acts as a \"catch all\" for route
`/auto`{.copy-on-click}.

We need to understand that the order of rules matters. If we would add
the desktop rule before the mobile one it wouldn\'t work because no
requests would ever reach the mobile rule.

Our solution should result in this:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka7968:~$ curl -H "User-Agent: mobile" r500.gateway:30080/auto
```
:::

``` {.CodeMirror-line role="presentation"}
Web Mobile App
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka7968:~$ curl -H "User-Agent: something" r500.gateway:30080/auto
```

``` {.CodeMirror-line role="presentation"}
Web Desktop App
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka7968:~$ curl r500.gateway:30080/auto
```

``` {.CodeMirror-line role="presentation"}
Web Desktop App
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 184px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 184px;"}
:::

Great, Gateway Api ftw!

 

 

## Question 14 \| Check how long certificates are valid {#question-14-|-check-how-long-certificates-are-valid}

 

Solve this question on: `ssh cka9412`{.copy-on-click}

 

Perform some tasks on cluster certificates:

1.  Check how long the kube-apiserver server certificate is valid using
    openssl or cfssl. Write the expiration date into
    `/opt/course/14/expiration`{.copy-on-click}. Run the
    `kubeadm`{.copy-on-click} command to list the expiration dates and
    confirm both methods show the same one

2.  Write the `kubeadm`{.copy-on-click} command that would renew the
    kube-apiserver certificate into
    `/opt/course/14/kubeadm-renew-certs.sh`{.copy-on-click}

 

##### Answer: {#answer-14}

First let\'s find that certificate:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ ssh cka9412
```
:::

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka9412:~$ sudo -i
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ root@cka9412:~# find /etc/kubernetes/pki | grep apiserver
```

``` {.CodeMirror-line role="presentation"}
/etc/kubernetes/pki/apiserver-etcd-client.key
```

``` {.CodeMirror-line role="presentation"}
/etc/kubernetes/pki/apiserver-kubelet-client.key
```

``` {.CodeMirror-line role="presentation"}
/etc/kubernetes/pki/apiserver-etcd-client.crt
```

``` {.CodeMirror-line role="presentation"}
/etc/kubernetes/pki/apiserver.key
```

``` {.CodeMirror-line role="presentation"}
/etc/kubernetes/pki/apiserver-kubelet-client.crt
```

``` {.CodeMirror-line role="presentation"}
/etc/kubernetes/pki/apiserver.crt
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 253px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 253px;"}
:::

Next we use openssl to find out the expiration date:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ root@cka9412:~# openssl x509 -noout -text -in /etc/kubernetes/pki/apiserver.crt | grep Validity -A2
```
:::

``` {.CodeMirror-line role="presentation"}
        Validity
```

``` {.CodeMirror-line role="presentation"}
            Not Before: Oct 29 14:14:27 2024 GMT
```

``` {.CodeMirror-line role="presentation"}
            Not After : Oct 29 14:19:27 2025 GMT
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 92px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 92px;"}
:::

There we have it, so we write it in the required location:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang=""}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
# cka9412:/opt/course/14/expiration
```
:::

``` {.CodeMirror-line role="presentation"}
Oct 29 14:19:27 2025 GMT
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 46px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 46px;"}
:::

And we use kubeadm to get the expiration to compare:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ root@cka9412:~# kubeadm certs check-expiration | grep apiserver
```
:::

``` {.CodeMirror-line role="presentation"}
apiserver                  Oct 29, 2025 14:19 UTC   356d    ca         no      
```

``` {.CodeMirror-line role="presentation"}
apiserver-etcd-client      Oct 29, 2025 14:19 UTC   356d    etcd-ca    no      
```

``` {.CodeMirror-line role="presentation"}
apiserver-kubelet-client   Oct 29, 2025 14:19 UTC   356d    ca         no 
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 92px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 92px;"}
:::

Looking good, both are the same.

And finally we write the command that would renew the kube-apiserver
certificate into the requested location:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang=""}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
# cka9412:/opt/course/14/kubeadm-renew-certs.sh
```
:::

``` {.CodeMirror-line role="presentation"}
kubeadm certs renew apiserver
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 46px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 46px;"}
:::

 

 

## Question 15 \| NetworkPolicy {#question-15-|-networkpolicy}

 

Solve this question on: `ssh cka7968`{.copy-on-click}

 

There was a security incident where an intruder was able to access the
whole cluster from a single hacked backend *Pod*.

To prevent this create a *NetworkPolicy* called
`np-backend`{.copy-on-click} in *Namespace*
`project-snake`{.copy-on-click}. It should allow the
`backend-*`{.copy-on-click} *Pods* only to:

-   Connect to `db1-*`{.copy-on-click} *Pods* on port
    `1111`{.copy-on-click}

-   Connect to `db2-*`{.copy-on-click} *Pods* on port
    `2222`{.copy-on-click}

Use the `app`{.copy-on-click} *Pod* labels in your policy.

 

> ℹ️ All *Pods* in the *Namespace* run plain Nginx images. This allows
> simple connectivity tests like:
> `k -n project-snake exec POD_NAME -- curl POD_IP:PORT`{.copy-on-click}

 

> ℹ️ For example, connections from `backend-*`{.copy-on-click} *Pods* to
> `vault-*`{.copy-on-click} *Pods* on port `3333`{.copy-on-click} should
> no longer work

 

 

##### Answer: {#answer-15}

First we look at the existing *Pods* and their labels:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term" style="break-inside: unset;"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ ssh cka7968
```
:::

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka7968:~$ k -n project-snake get pod
```

``` {.CodeMirror-line role="presentation"}
NAME        READY   STATUS    RESTARTS   AGE
```

``` {.CodeMirror-line role="presentation"}
backend-0   1/1     Running   0          8d
```

``` {.CodeMirror-line role="presentation"}
db1-0       1/1     Running   0          8d
```

``` {.CodeMirror-line role="presentation"}
db2-0       1/1     Running   0          8d
```

``` {.CodeMirror-line role="presentation"}
vault-0     1/1     Running   0          8d
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka7968:~$ k -n project-snake get pod -L app
```

``` {.CodeMirror-line role="presentation"}
NAME        READY   STATUS    RESTARTS   AGE   APP
```

``` {.CodeMirror-line role="presentation"}
backend-0   1/1     Running   0          8d    backend
```

``` {.CodeMirror-line role="presentation"}
db1-0       1/1     Running   0          8d    db1
```

``` {.CodeMirror-line role="presentation"}
db2-0       1/1     Running   0          8d    db2
```

``` {.CodeMirror-line role="presentation"}
vault-0     1/1     Running   0          8d    vault
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 345px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 345px;"}
:::

We test the current connection situation and see nothing is restricted:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term" style="break-inside: unset;"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka7968:~$ k -n project-snake get pod -o wide
```
:::

``` {.CodeMirror-line role="presentation"}
NAME        READY   STATUS    RESTARTS   AGE     IP          ...
```

``` {.CodeMirror-line role="presentation"}
backend-0   1/1     Running   0          8d      10.44.0.24  ...
```

``` {.CodeMirror-line role="presentation"}
db1-0       1/1     Running   0          8d      10.44.0.25  ...
```

``` {.CodeMirror-line role="presentation"}
db2-0       1/1     Running   0          8d      10.44.0.23  ...
```

``` {.CodeMirror-line role="presentation"}
vault-0     1/1     Running   0          8d      10.44.0.22  ...
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka7968:~$ k -n project-snake exec backend-0 -- curl -s 10.44.0.25:1111
```

``` {.CodeMirror-line role="presentation"}
database one
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka7968:~$ k -n project-snake exec backend-0 -- curl -s 10.44.0.23:2222
```

``` {.CodeMirror-line role="presentation"}
database two
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka7968:~$ k -n project-snake exec backend-0 -- curl -s 10.44.0.22:3333
```

``` {.CodeMirror-line role="presentation"}
vault secret storage
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 345px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 345px;"}
:::

Now we create the *NP* by copying and changing an example from the K8s
Docs:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="yaml" style="break-inside: unset;"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
# cka7968:/home/candidate/15_np.yaml
```
:::

``` {.CodeMirror-line role="presentation"}
apiVersion: networking.k8s.io/v1
```

``` {.CodeMirror-line role="presentation"}
kind: NetworkPolicy
```

``` {.CodeMirror-line role="presentation"}
metadata:
```

``` {.CodeMirror-line role="presentation"}
  name: np-backend
```

``` {.CodeMirror-line role="presentation"}
  namespace: project-snake
```

``` {.CodeMirror-line role="presentation"}
spec:
```

``` {.CodeMirror-line role="presentation"}
  podSelector:
```

``` {.CodeMirror-line role="presentation"}
    matchLabels:
```

``` {.CodeMirror-line role="presentation"}
      app: backend
```

``` {.CodeMirror-line role="presentation"}
  policyTypes:
```

``` {.CodeMirror-line role="presentation"}
    - Egress                    # policy is only about Egress
```

``` {.CodeMirror-line role="presentation"}
  egress:
```

``` {.CodeMirror-line role="presentation"}
    -                           # first rule
```

``` {.CodeMirror-line role="presentation"}
      to:                           # first condition "to"
```

``` {.CodeMirror-line role="presentation"}
      - podSelector:
```

``` {.CodeMirror-line role="presentation"}
          matchLabels:
```

``` {.CodeMirror-line role="presentation"}
            app: db1
```

``` {.CodeMirror-line role="presentation"}
      ports:                        # second condition "port"
```

``` {.CodeMirror-line role="presentation"}
      - protocol: TCP
```

``` {.CodeMirror-line role="presentation"}
        port: 1111
```

``` {.CodeMirror-line role="presentation"}
    -                           # second rule
```

``` {.CodeMirror-line role="presentation"}
      to:                           # first condition "to"
```

``` {.CodeMirror-line role="presentation"}
      - podSelector:
```

``` {.CodeMirror-line role="presentation"}
          matchLabels:
```

``` {.CodeMirror-line role="presentation"}
            app: db2
```

``` {.CodeMirror-line role="presentation"}
      ports:                        # second condition "port"
```

``` {.CodeMirror-line role="presentation"}
      - protocol: TCP
```

``` {.CodeMirror-line role="presentation"}
        port: 2222
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 580px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 580px;"}
:::

The *NP* above has two rules with two conditions each, it can be read
as:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang=""}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
allow outgoing traffic if:
```
:::

``` {.CodeMirror-line role="presentation"}
  (destination pod has label app=db1 AND port is 1111)
```

``` {.CodeMirror-line role="presentation"}
  OR
```

``` {.CodeMirror-line role="presentation"}
  (destination pod has label app=db2 AND port is 2222)
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 92px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 92px;"}
:::

 

###### Wrong example

Now let\'s shortly look at a wrong example:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="yaml" style="break-inside: unset;"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
# WRONG
```
:::

``` {.CodeMirror-line role="presentation"}
apiVersion: networking.k8s.io/v1
```

``` {.CodeMirror-line role="presentation"}
kind: NetworkPolicy
```

``` {.CodeMirror-line role="presentation"}
metadata:
```

``` {.CodeMirror-line role="presentation"}
  name: np-backend
```

``` {.CodeMirror-line role="presentation"}
  namespace: project-snake
```

``` {.CodeMirror-line role="presentation"}
spec:
```

``` {.CodeMirror-line role="presentation"}
  podSelector:
```

``` {.CodeMirror-line role="presentation"}
    matchLabels:
```

``` {.CodeMirror-line role="presentation"}
      app: backend
```

``` {.CodeMirror-line role="presentation"}
  policyTypes:
```

``` {.CodeMirror-line role="presentation"}
    - Egress
```

``` {.CodeMirror-line role="presentation"}
  egress:
```

``` {.CodeMirror-line role="presentation"}
    -                           # first rule
```

``` {.CodeMirror-line role="presentation"}
      to:                           # first condition "to"
```

``` {.CodeMirror-line role="presentation"}
      - podSelector:                    # first "to" possibility
```

``` {.CodeMirror-line role="presentation"}
          matchLabels:
```

``` {.CodeMirror-line role="presentation"}
            app: db1
```

``` {.CodeMirror-line role="presentation"}
      - podSelector:                    # second "to" possibility
```

``` {.CodeMirror-line role="presentation"}
          matchLabels:
```

``` {.CodeMirror-line role="presentation"}
            app: db2
```

``` {.CodeMirror-line role="presentation"}
      ports:                        # second condition "ports"
```

``` {.CodeMirror-line role="presentation"}
      - protocol: TCP                   # first "ports" possibility
```

``` {.CodeMirror-line role="presentation"}
        port: 1111
```

``` {.CodeMirror-line role="presentation"}
      - protocol: TCP                   # second "ports" possibility
```

``` {.CodeMirror-line role="presentation"}
        port: 2222
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 520px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 520px;"}
:::

The *NP* above has one rule with two conditions and two
condition-entries each, it can be read as:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang=""}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
allow outgoing traffic if:
```
:::

``` {.CodeMirror-line role="presentation"}
  (destination pod has label app=db1 OR destination pod has label app=db2)
```

``` {.CodeMirror-line role="presentation"}
  AND
```

``` {.CodeMirror-line role="presentation"}
  (destination port is 1111 OR destination port is 2222)
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 92px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 92px;"}
:::

Using this *NP* it would still be possible for
`backend-*`{.copy-on-click} *Pods* to connect to `db2-*`{.copy-on-click}
*Pods* on port 1111 for example which should be forbidden.

 

###### Create NetworkPolicy

We create the correct *NP*:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka7968:~$ k -f 15_np.yaml create
```
:::
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 23px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 23px;"}
:::

And to verify:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka7968:~$ k -n project-snake exec backend-0 -- curl -s 10.44.0.25:1111
```
:::

``` {.CodeMirror-line role="presentation"}
database one
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka7968:~$ k -n project-snake exec backend-0 -- curl -s 10.44.0.23:2222
```

``` {.CodeMirror-line role="presentation"}
database two
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka7968:~$ k -n project-snake exec backend-0 -- curl -s 10.44.0.22:3333
```

``` {.CodeMirror-line role="presentation"}
^C
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 184px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 184px;"}
:::

Also helpful to use `kubectl describe`{.copy-on-click} on the *NP* to
see how K8s has interpreted the policy.

 

 

## Question 16 \| Update CoreDNS Configuration {#question-16-|-update-coredns-configuration}

 

Solve this question on: `ssh cka5774`{.copy-on-click}

 

The CoreDNS configuration in the cluster needs to be updated:

1.  Make a backup of the existing configuration Yaml and store it at
    `/opt/course/16/coredns_backup.yaml`{.copy-on-click}. You should be
    able to fast recover from the backup

2.  Update the CoreDNS configuration in the cluster so that DNS
    resolution for `SERVICE.NAMESPACE.custom-domain`{.copy-on-click}
    will work exactly like and in addition to
    `SERVICE.NAMESPACE.cluster.local`{.copy-on-click}

Test your configuration for example from a *Pod* with
`busybox:1`{.copy-on-click} image. These commands should result in an IP
address:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang=""}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
nslookup kubernetes.default.svc.cluster.local
```
:::

``` {.CodeMirror-line role="presentation"}
nslookup kubernetes.default.svc.custom-domain
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 46px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 46px;"}
:::

 

##### Answer: {#answer-16}

We have a look at the CoreDNS *Pods*:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ ssh cka5774
```
:::

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka5774:~$ k -n kube-system get deploy,pod
```

``` {.CodeMirror-line role="presentation"}
NAME                             READY   UP-TO-DATE   AVAILABLE   AGE
```

``` {.CodeMirror-line role="presentation"}
deployment.apps/coredns          2/2     2            2           42h
```

``` {.CodeMirror-line role="presentation"}
...
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
NAME                                  READY   STATUS    RESTARTS      AGE
```

``` {.CodeMirror-line role="presentation"}
pod/coredns-74f75f8b69-c4z47          1/1     Running   0             42h
```

``` {.CodeMirror-line role="presentation"}
pod/coredns-74f75f8b69-wsnfr          1/1     Running   0             42h
```

``` {.CodeMirror-line role="presentation"}
...
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 253px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 253px;"}
:::

It looks like CoreDNS is running as a *Deployment* with two replicas.

 

###### Step 1 {#step-1-5}

CoreDNS uses a *ConfigMap* by default when installed via Kubeadm.
Creating a backup is always a good idea before performing sensitive
changes:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka5774:~$ k -n kube-system get cm
```
:::

``` {.CodeMirror-line role="presentation"}
NAME                                                   DATA   AGE
```

``` {.CodeMirror-line role="presentation"}
coredns                                                1      42h
```

``` {.CodeMirror-line role="presentation"}
...
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka5774:~$ k -n kube-system get cm coredns -oyaml > /opt/course/16/coredns_backup.yaml
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 138px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 138px;"}
:::

The current configuration looks like this:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="yaml" style="break-inside: unset;"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
apiVersion: v1
```
:::

``` {.CodeMirror-line role="presentation"}
data:
```

``` {.CodeMirror-line role="presentation"}
  Corefile: |
```

``` {.CodeMirror-line role="presentation"}
    .:53 {
```

``` {.CodeMirror-line role="presentation"}
        errors
```

``` {.CodeMirror-line role="presentation"}
        health {
```

``` {.CodeMirror-line role="presentation"}
           lameduck 5s
```

``` {.CodeMirror-line role="presentation"}
        }
```

``` {.CodeMirror-line role="presentation"}
        ready
```

``` {.CodeMirror-line role="presentation"}
        kubernetes cluster.local in-addr.arpa ip6.arpa {
```

``` {.CodeMirror-line role="presentation"}
           pods insecure
```

``` {.CodeMirror-line role="presentation"}
           fallthrough in-addr.arpa ip6.arpa
```

``` {.CodeMirror-line role="presentation"}
           ttl 30
```

``` {.CodeMirror-line role="presentation"}
        }
```

``` {.CodeMirror-line role="presentation"}
        prometheus :9153
```

``` {.CodeMirror-line role="presentation"}
        forward . /etc/resolv.conf {
```

``` {.CodeMirror-line role="presentation"}
           max_concurrent 1000
```

``` {.CodeMirror-line role="presentation"}
        }
```

``` {.CodeMirror-line role="presentation"}
        cache 30 {
```

``` {.CodeMirror-line role="presentation"}
           disable success cluster.local
```

``` {.CodeMirror-line role="presentation"}
           disable denial cluster.local
```

``` {.CodeMirror-line role="presentation"}
        }
```

``` {.CodeMirror-line role="presentation"}
        loop
```

``` {.CodeMirror-line role="presentation"}
        reload
```

``` {.CodeMirror-line role="presentation"}
        loadbalance
```

``` {.CodeMirror-line role="presentation"}
    }
```

``` {.CodeMirror-line role="presentation"}
kind: ConfigMap
```

``` {.CodeMirror-line role="presentation"}
metadata:
```

``` {.CodeMirror-line role="presentation"}
  name: coredns
```

``` {.CodeMirror-line role="presentation"}
  namespace: kube-system
```

``` {.CodeMirror-line role="presentation"}
...
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 620px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 620px;"}
:::

 

###### Step 2 {#step-2-5}

We update the config:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka5774:~$ k -n kube-system edit cm coredns
```
:::
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 23px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 23px;"}
:::

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="yaml" style="break-inside: unset;"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
apiVersion: v1
```
:::

``` {.CodeMirror-line role="presentation"}
data:
```

``` {.CodeMirror-line role="presentation"}
  Corefile: |
```

``` {.CodeMirror-line role="presentation"}
    .:53 {
```

``` {.CodeMirror-line role="presentation"}
        errors
```

``` {.CodeMirror-line role="presentation"}
        health {
```

``` {.CodeMirror-line role="presentation"}
           lameduck 5s
```

``` {.CodeMirror-line role="presentation"}
        }
```

``` {.CodeMirror-line role="presentation"}
        ready
```

``` {.CodeMirror-line role="presentation"}
        kubernetes custom-domain cluster.local in-addr.arpa ip6.arpa {
```

``` {.CodeMirror-line role="presentation"}
           pods insecure
```

``` {.CodeMirror-line role="presentation"}
           fallthrough in-addr.arpa ip6.arpa
```

``` {.CodeMirror-line role="presentation"}
           ttl 30
```

``` {.CodeMirror-line role="presentation"}
        }
```

``` {.CodeMirror-line role="presentation"}
        prometheus :9153
```

``` {.CodeMirror-line role="presentation"}
        forward . /etc/resolv.conf {
```

``` {.CodeMirror-line role="presentation"}
           max_concurrent 1000
```

``` {.CodeMirror-line role="presentation"}
        }
```

``` {.CodeMirror-line role="presentation"}
        cache 30 {
```

``` {.CodeMirror-line role="presentation"}
           disable success cluster.local
```

``` {.CodeMirror-line role="presentation"}
           disable denial cluster.local
```

``` {.CodeMirror-line role="presentation"}
        }
```

``` {.CodeMirror-line role="presentation"}
        loop
```

``` {.CodeMirror-line role="presentation"}
        reload
```

``` {.CodeMirror-line role="presentation"}
        loadbalance
```

``` {.CodeMirror-line role="presentation"}
    }
```

``` {.CodeMirror-line role="presentation"}
kind: ConfigMap
```

``` {.CodeMirror-line role="presentation"}
metadata:
```

``` {.CodeMirror-line role="presentation"}
  creationTimestamp: "2024-12-26T20:35:11Z"
```

``` {.CodeMirror-line role="presentation"}
  name: coredns
```

``` {.CodeMirror-line role="presentation"}
  namespace: kube-system
```

``` {.CodeMirror-line role="presentation"}
  resourceVersion: "262"
```

``` {.CodeMirror-line role="presentation"}
  uid: c76d208f-1bc8-4c0f-a8e8-a8bfa440870e
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 660px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 660px;"}
:::

Note that we added `custom-domain`{.copy-on-click} in the same line
where `cluster.local`{.copy-on-click} is already defined.

Now we need to restart the *Deployment*:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka5774:~$ k -n kube-system rollout restart deploy coredns
```
:::

``` {.CodeMirror-line role="presentation"}
deployment.apps/coredns restarted
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka5774:~$ k -n kube-system get pod
```

``` {.CodeMirror-line role="presentation"}
NAME                              READY   STATUS    RESTARTS      AGE
```

``` {.CodeMirror-line role="presentation"}
coredns-77d6976b98-jkvqn          1/1     Running   0             13s
```

``` {.CodeMirror-line role="presentation"}
coredns-77d6976b98-zdxw8          1/1     Running   0             13s
```

``` {.CodeMirror-line role="presentation"}
...
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 184px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 184px;"}
:::

We should see both *Pods* restarted and running without errors, this is
only the case if there are no syntax errors in the CoreDNS config.

To test the updated configuration we create a *Pod*, image
`busybox:1`{.copy-on-click} contains `nslookup`{.copy-on-click} already:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term" style="break-inside: unset;"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka5774:~$ k run bb --image=busybox:1 -- sh -c 'sleep 1d'
```
:::

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka5774:~$ k exec -it bb -- sh
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ / # nslookup kubernetes.default.svc.custom-domain
```

``` {.CodeMirror-line role="presentation"}
Server:         10.96.0.10
```

``` {.CodeMirror-line role="presentation"}
Address:        10.96.0.10:53
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
Name:   kubernetes.default.svc.custom-domain
```

``` {.CodeMirror-line role="presentation"}
Address: 10.96.0.1
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ / # nslookup kubernetes.default.svc.cluster.local
```

``` {.CodeMirror-line role="presentation"}
Server:         10.96.0.10
```

``` {.CodeMirror-line role="presentation"}
Address:        10.96.0.10:53
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
Name:   kubernetes.default.svc.cluster.local
```

``` {.CodeMirror-line role="presentation"}
Address: 10.96.0.1
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 391px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 391px;"}
:::

We see that now `kubernetes.default.svc.custom-domain`{.copy-on-click}
and `kubernetes.default.svc.cluster.local`{.copy-on-click} resolve to IP
address `10.96.0.1`{.copy-on-click}. Which is the Kubernetes *Service*
in the `default`{.copy-on-click} *Namespace*:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka5774:~$ k -n default get svc
```
:::

``` {.CodeMirror-line role="presentation"}
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
```

``` {.CodeMirror-line role="presentation"}
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   43h
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 69px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 69px;"}
:::

This *Service* is often used from *Pods* that need to communicate with
the K8s Api, like operators.

 

###### Recover from backup

If we messed something up we could do:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term" style="break-inside: unset;"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka5774:~$ k diff -f /opt/course/16/coredns_backup.yaml 
```
:::

``` {.CodeMirror-line role="presentation"}
diff -u -N /tmp/LIVE-591628213/v1.ConfigMap.kube-system.coredns /tmp/MERGED-4230802928/v1.ConfigMap.kube-system.coredns
```

``` {.CodeMirror-line role="presentation"}
--- /tmp/LIVE-591628213/v1.ConfigMap.kube-system.coredns        2024-12-28 16:14:03.158949709 +0000
```

``` {.CodeMirror-line role="presentation"}
+++ /tmp/MERGED-4230802928/v1.ConfigMap.kube-system.coredns     2024-12-28 16:14:03.159949781 +0000
```

``` {.CodeMirror-line role="presentation"}
@@ -7,7 +7,7 @@
```

``` {.CodeMirror-line role="presentation"}
            lameduck 5s
```

``` {.CodeMirror-line role="presentation"}
         }
```

``` {.CodeMirror-line role="presentation"}
         ready
```

``` {.CodeMirror-line role="presentation"}
-        kubernetes custom-domain cluster.local in-addr.arpa ip6.arpa {
```

``` {.CodeMirror-line role="presentation"}
+        kubernetes cluster.local in-addr.arpa ip6.arpa {
```

``` {.CodeMirror-line role="presentation"}
            pods insecure
```

``` {.CodeMirror-line role="presentation"}
            fallthrough in-addr.arpa ip6.arpa
```

``` {.CodeMirror-line role="presentation"}
            ttl 30
```

``` {.CodeMirror-line role="presentation"}
            
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka5774:~$ k delete -f /opt/course/16/coredns_backup.yaml 
```

``` {.CodeMirror-line role="presentation"}
configmap "coredns" deleted
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka5774:~$ k apply -f /opt/course/16/coredns_backup.yaml 
```

``` {.CodeMirror-line role="presentation"}
configmap/coredns created
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka5774:~$ k -n kube-system rollout restart deploy coredns
```

``` {.CodeMirror-line role="presentation"}
deployment.apps/coredns restarted
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka5774:~$ k -n kube-system get pod
```

``` {.CodeMirror-line role="presentation"}
NAME                              READY   STATUS    RESTARTS      AGE
```

``` {.CodeMirror-line role="presentation"}
coredns-79f94f8fc8-h8z7t          1/1     Running   0             11s
```

``` {.CodeMirror-line role="presentation"}
coredns-79f94f8fc8-tj7hg          1/1     Running   0             10s
```

``` {.CodeMirror-line role="presentation"}
...
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 667px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 667px;"}
:::

But this only works if a backup is available!

 

 

## Question 17 \| Find Container of Pod and check info {#question-17-|-find-container-of-pod-and-check-info}

 

Solve this question on: `ssh cka2556`{.copy-on-click}

 

In *Namespace* `project-tiger`{.copy-on-click} create a *Pod* named
`tigers-reunite`{.copy-on-click} of image
`httpd:2-alpine`{.copy-on-click} with labels
`pod=container`{.copy-on-click} and `container=pod`{.copy-on-click}.
Find out on which node the *Pod* is scheduled. Ssh into that node and
find the containerd container belonging to that *Pod*.

Using command `crictl`{.copy-on-click}:

1.  Write the ID of the container and the
    `info.runtimeType`{.copy-on-click} into
    `/opt/course/17/pod-container.txt`{.copy-on-click}

2.  Write the logs of the container into
    `/opt/course/17/pod-container.log`{.copy-on-click}

 

> ℹ️ You can connect to a worker node using
> `ssh cka2556-node1`{.copy-on-click} or
> `ssh cka2556-node2`{.copy-on-click} from `cka2556`{.copy-on-click}

 

##### Answer: {#answer-17}

> ℹ️ In this environment `crictl`{.copy-on-click} can be used for
> container management. In the real exam this could also be
> `docker`{.copy-on-click}. Both commands can be used with the same
> arguments.

 

First we create the *Pod*:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ ssh cka2556
```
:::

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka2556:~$ k -n project-tiger run tigers-reunite --image=httpd:2-alpine --labels "pod=container,container=pod"
```

``` {.CodeMirror-line role="presentation"}
pod/tigers-reunite created
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 115px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 115px;"}
:::

Next we find out the node it\'s scheduled on:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka2556:~$ candidate@cka2556:~$ k -n project-tiger get pod -o wide
```
:::

``` {.CodeMirror-line role="presentation"}
NAME                                   READY   ...   NODE
```

``` {.CodeMirror-line role="presentation"}
tigers-for-rent-web-57558cfbf8-4tldr   1/1     ...   cka2556-node1
```

``` {.CodeMirror-line role="presentation"}
tigers-for-rent-web-57558cfbf8-5pz4z   1/1     ...   cka2556-node2
```

``` {.CodeMirror-line role="presentation"}
tigers-reunite                         1/1     ...   cka2556-node1
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 115px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 115px;"}
:::

Here it\'s `cka2556-node1`{.copy-on-click} so we ssh into that node and
and check the container info:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka2556:~$ ssh cka2556-node1
```
:::

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka2556-node1:~$ sudo -i
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ root@cka2556-node1:~# crictl ps | grep tigers-reunite
```

``` {.CodeMirror-line role="presentation"}
ba62e5d465ff0   a7ccaadd632cf   2 minutes ago   Running   tigers-reunite   ...
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 138px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 138px;"}
:::

 

###### Step 1 {#step-1-6}

Having the container we can `crictl inspect`{.copy-on-click} it for the
runtimeType:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ root@cka2556-node1:~# crictl inspect ba62e5d465ff0 | grep runtimeType
```
:::

``` {.CodeMirror-line role="presentation"}
    "runtimeType": "io.containerd.runc.v2",
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 46px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 46px;"}
:::

Now we create the requested file on `cka2556`{.copy-on-click}:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang=""}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
# cka2556:/opt/course/17/pod-container.txt
```
:::

``` {.CodeMirror-line role="presentation"}
ba62e5d465ff0 io.containerd.runc.v2
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 46px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 46px;"}
:::

 

###### Step 2 {#step-2-6}

Finally we query the container logs:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ root@cka2556-node1:~# crictl logs ba62e5d465ff0
```
:::

``` {.CodeMirror-line role="presentation"}
AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 10.44.0.29. Set the 'ServerName' directive globally to suppress this message
```

``` {.CodeMirror-line role="presentation"}
AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 10.44.0.29. Set the 'ServerName' directive globally to suppress this message
```

``` {.CodeMirror-line role="presentation"}
[Tue Oct 29 15:12:57.211347 2024] [mpm_event:notice] [pid 1:tid 1] AH00489: Apache/2.4.62 (Unix) configured -- resuming normal operations
```

``` {.CodeMirror-line role="presentation"}
[Tue Oct 29 15:12:57.211841 2024] [core:notice] [pid 1:tid 1] AH00094: Command line: 'httpd -D FOREGROUND'
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 184px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 184px;"}
:::

Here we run `crictl logs`{.copy-on-click} on the worker node and copy
the content manually, that works if it\'s not a lot of logs. Otherwise
we could write the logs into a file on `cka2556-node1`{.copy-on-click}
and download the file via scp from `cka2556`{.copy-on-click}.

The file should look like this:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang=""}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
# cka2556:/opt/course/17/pod-container.log
```
:::

``` {.CodeMirror-line role="presentation"}
AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 10.44.0.37. Set the 'ServerName' directive globally to suppress this message
```

``` {.CodeMirror-line role="presentation"}
AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 10.44.0.37. Set the 'ServerName' directive globally to suppress this message
```

``` {.CodeMirror-line role="presentation"}
[Mon Sep 13 13:32:18.555280 2021] [mpm_event:notice] [pid 1:tid 139929534545224] AH00489: Apache/2.4.41 (Unix) configured -- resuming normal operations
```

``` {.CodeMirror-line role="presentation"}
[Mon Sep 13 13:32:18.555610 2021] [core:notice] [pid 1:tid 139929534545224] AH00094: Command line: 'httpd -D FOREGROUND'
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 207px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 207px;"}
:::

 

 

 

 

 

 

 

[]{#preview v-1242acb3=""}

::: {.root v-8a94ff0e="" v-1242acb3=""}
::: {v-8a94ff0e=""}
[[ ]{v-8a94ff0e=""}]{v-8a94ff0e=""}

preview

::: typora-export-content
::: {#write}
# CKA Simulator Preview Kubernetes 1.32 {#cka-simulator-preview-kubernetes-132}

[https://killer.sh](https://killer.sh/){.url target="_blank"}

 

This is a preview of the CKA Simulator content. The full CKA Simulator
is available in two versions: A and B. Each version contains at least 17
different questions. These preview questions are in addition to the
provided ones and can also be solved in the interactive environment.

 

 

## Preview Question 1 \| ETCD Information {#preview-question-1-|-etcd-information}

 

Solve this question on: `ssh cka9412`{.copy-on-click}

 

The cluster admin asked you to find out the following information about
etcd running on `cka9412`{.copy-on-click}:

-   Server private key location

-   Server certificate expiration date

-   Is client certificate authentication enabled

Write these information into
`/opt/course/p1/etcd-info.txt`{.copy-on-click}

 

##### Answer: {#answer}

###### Find out etcd information

Let\'s check the nodes:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
​x➜ ssh cka9412
```
:::

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka9412:~$ k get node
```

``` {.CodeMirror-line role="presentation"}
NAME            STATUS   ROLES           AGE   VERSION
```

``` {.CodeMirror-line role="presentation"}
cka9412         Ready    control-plane   9d    v1.32.0
```

``` {.CodeMirror-line role="presentation"}
cka9412-node1   Ready    <none>          9d    v1.32.0
```
:::
:::
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 138px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 138px;"}
:::

First we check how etcd is setup in this cluster:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term" style="break-inside: unset;"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka9412:~$ sudo -i
```
:::

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ root@cka9412:~# k -n kube-system get pod
```

``` {.CodeMirror-line role="presentation"}
NAME                              READY   STATUS    RESTARTS     AGE
```

``` {.CodeMirror-line role="presentation"}
coredns-6f4c58b94d-djpgr          1/1     Running   0            8d
```

``` {.CodeMirror-line role="presentation"}
coredns-6f4c58b94d-ds6ch          1/1     Running   0            8d
```

``` {.CodeMirror-line role="presentation"}
etcd-cka9412                      1/1     Running   0            9d
```

``` {.CodeMirror-line role="presentation"}
kube-apiserver-cka9412            1/1     Running   0            9d
```

``` {.CodeMirror-line role="presentation"}
kube-controller-manager-cka9412   1/1     Running   0            9d
```

``` {.CodeMirror-line role="presentation"}
kube-proxy-7zhtk                  1/1     Running   0            9d
```

``` {.CodeMirror-line role="presentation"}
kube-proxy-nbzrt                  1/1     Running   0            9d
```

``` {.CodeMirror-line role="presentation"}
kube-scheduler-cka9412            1/1     Running   0            9d
```

``` {.CodeMirror-line role="presentation"}
weave-net-h7n8j                   2/2     Running   1 (9d ago)   9d
```

``` {.CodeMirror-line role="presentation"}
weave-net-rbhgl                   2/2     Running   1 (9d ago)   9d
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 322px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 322px;"}
:::

We see it\'s running as a *Pod*, more specific a static *Pod*. So we
check for the default kubelet directory for static manifests:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ root@cka9412:~# find /etc/kubernetes/manifests/
```
:::

``` {.CodeMirror-line role="presentation"}
/etc/kubernetes/manifests/
```

``` {.CodeMirror-line role="presentation"}
/etc/kubernetes/manifests/kube-controller-manager.yaml
```

``` {.CodeMirror-line role="presentation"}
/etc/kubernetes/manifests/kube-apiserver.yaml
```

``` {.CodeMirror-line role="presentation"}
/etc/kubernetes/manifests/etcd.yaml
```

``` {.CodeMirror-line role="presentation"}
/etc/kubernetes/manifests/kube-scheduler.yaml
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ root@cka9412:~# vim /etc/kubernetes/manifests/etcd.yaml
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 184px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 184px;"}
:::

So we look at the yaml and the parameters with which etcd is started:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="yaml" style="break-inside: unset;"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
# cka9412:/etc/kubernetes/manifests/etcd.yaml
```
:::

``` {.CodeMirror-line role="presentation"}
apiVersion: v1
```

``` {.CodeMirror-line role="presentation"}
kind: Pod
```

``` {.CodeMirror-line role="presentation"}
metadata:
```

``` {.CodeMirror-line role="presentation"}
  annotations:
```

``` {.CodeMirror-line role="presentation"}
    kubeadm.kubernetes.io/etcd.advertise-client-urls: https://192.168.100.21:2379
```

``` {.CodeMirror-line role="presentation"}
  creationTimestamp: null
```

``` {.CodeMirror-line role="presentation"}
  labels:
```

``` {.CodeMirror-line role="presentation"}
    component: etcd
```

``` {.CodeMirror-line role="presentation"}
    tier: control-plane
```

``` {.CodeMirror-line role="presentation"}
  name: etcd
```

``` {.CodeMirror-line role="presentation"}
  namespace: kube-system
```

``` {.CodeMirror-line role="presentation"}
spec:
```

``` {.CodeMirror-line role="presentation"}
  containers:
```

``` {.CodeMirror-line role="presentation"}
  - command:
```

``` {.CodeMirror-line role="presentation"}
    - etcd
```

``` {.CodeMirror-line role="presentation"}
    - --advertise-client-urls=https://192.168.100.21:2379
```

``` {.CodeMirror-line role="presentation"}
    - --cert-file=/etc/kubernetes/pki/etcd/server.crt            # server certificate
```

``` {.CodeMirror-line role="presentation"}
    - --client-cert-auth=true                                    # enabled
```

``` {.CodeMirror-line role="presentation"}
    - --data-dir=/var/lib/etcd
```

``` {.CodeMirror-line role="presentation"}
    - --experimental-initial-corrupt-check=true
```

``` {.CodeMirror-line role="presentation"}
    - --experimental-watch-progress-notify-interval=5s
```

``` {.CodeMirror-line role="presentation"}
    - --initial-advertise-peer-urls=https://192.168.100.21:2380
```

``` {.CodeMirror-line role="presentation"}
    - --initial-cluster=cka9412=https://192.168.100.21:2380
```

``` {.CodeMirror-line role="presentation"}
    - --key-file=/etc/kubernetes/pki/etcd/server.key             # server private key
```

``` {.CodeMirror-line role="presentation"}
    - --listen-client-urls=https://127.0.0.1:2379,https://192.168.100.21:2379
```

``` {.CodeMirror-line role="presentation"}
    - --listen-metrics-urls=http://127.0.0.1:2381
```

``` {.CodeMirror-line role="presentation"}
    - --listen-peer-urls=https://192.168.100.21:2380
```

``` {.CodeMirror-line role="presentation"}
    - --name=cka9412
```

``` {.CodeMirror-line role="presentation"}
    - --peer-cert-file=/etc/kubernetes/pki/etcd/peer.crt
```

``` {.CodeMirror-line role="presentation"}
    - --peer-client-cert-auth=true
```

``` {.CodeMirror-line role="presentation"}
    - --peer-key-file=/etc/kubernetes/pki/etcd/peer.key
```

``` {.CodeMirror-line role="presentation"}
    - --peer-trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
```

``` {.CodeMirror-line role="presentation"}
    - --snapshot-count=10000
```

``` {.CodeMirror-line role="presentation"}
    - --trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
```

``` {.CodeMirror-line role="presentation"}
    image: registry.k8s.io/etcd:3.5.15-0
```

``` {.CodeMirror-line role="presentation"}
    imagePullPolicy: IfNotPresent
```

``` {.CodeMirror-line role="presentation"}
...
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 760px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 760px;"}
:::

We see that client authentication is enabled and also the requested path
to the server private key, now let\'s find out the expiration of the
server certificate:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ root@cka9412:~# openssl x509  -noout -text -in /etc/kubernetes/pki/etcd/server.crt | grep Validity -A2
```
:::

``` {.CodeMirror-line role="presentation"}
        Validity
```

``` {.CodeMirror-line role="presentation"}
            Not Before: Oct 29 14:14:27 2024 GMT
```

``` {.CodeMirror-line role="presentation"}
            Not After : Oct 29 14:19:27 2025 GMT
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 115px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 115px;"}
:::

There we have it. Let\'s write the information into the requested file:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang=""}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
# /opt/course/p1/etcd-info.txt
```
:::

``` {.CodeMirror-line role="presentation"}
Server private key location: /etc/kubernetes/pki/etcd/server.key
```

``` {.CodeMirror-line role="presentation"}
Server certificate expiration date: Oct 29 14:19:27 2025 GMT
```

``` {.CodeMirror-line role="presentation"}
Is client certificate authentication enabled: yes
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 92px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 92px;"}
:::

 

 

## Preview Question 2 \| Kube-Proxy iptables {#preview-question-2-|-kube-proxy-iptables}

 

Solve this question on: `ssh cka2556`{.copy-on-click}

 

You\'re asked to confirm that kube-proxy is running correctly. For this
perform the following in *Namespace* `project-hamster`{.copy-on-click}:

1.  Create *Pod* `p2-pod`{.copy-on-click} with image
    `nginx:1-alpine`{.copy-on-click}

2.  Create *Service* `p2-service`{.copy-on-click} which exposes the
    *Pod* internally in the cluster on port `3000->80`{.copy-on-click}

3.  Write the iptables rules of node `cka2556`{.copy-on-click} belonging
    the created *Service* `p2-service`{.copy-on-click} into file
    `/opt/course/p2/iptables.txt`{.copy-on-click}

4.  Delete the *Service* and confirm that the iptables rules are gone
    again

 

##### Answer: {#answer-2}

 

###### Step 1: Create the *Pod*

First we create the *Pod*:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ ssh cka2556
```
:::

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka2556:~$ k -n project-hamster run p2-pod --image=nginx:1-alpine
```

``` {.CodeMirror-line role="presentation"}
pod/p2-pod created
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 92px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 92px;"}
:::

 

###### Step 2: Create the *Service*

Next we create the *Service*:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka2556:~$ k -n project-hamster expose pod p2-pod --name p2-service --port 3000 --target-port 80
```
:::

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka2556:~$ k -n project-hamster get pod,svc,ep
```

``` {.CodeMirror-line role="presentation"}
NAME                 READY   STATUS    RESTARTS   AGE
```

``` {.CodeMirror-line role="presentation"}
pod/p2-pod           1/1     Running   0          2m31s
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
NAME                 TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
```

``` {.CodeMirror-line role="presentation"}
service/p2-service   ClusterIP   10.105.128.247   <none>        3000/TCP   1s
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
NAME                   ENDPOINTS       AGE
```

``` {.CodeMirror-line role="presentation"}
endpoints/p2-service   10.44.0.31:80   1s
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 276px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 276px;"}
:::

We should see that *Pods* and *Services* are connected, hence the
*Service* should have *Endpoints*.

 

###### (Optional) Confirm kube-proxy is running and is using iptables

The idea here is to find the kube-proxy container and check its logs:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka2556:~$ sudo -i
```
:::

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ root@cka2556$ crictl ps | grep kube-proxy
```

``` {.CodeMirror-line role="presentation"}
67cccaf8310a1   505d571f5fd56   9 days ago      Running    kube-proxy ...
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ root@cka2556~# crictl logs 67cccaf8310a1
```

``` {.CodeMirror-line role="presentation"}
I1029 14:10:23.984360       1 server_linux.go:66] "Using iptables proxy"
```

``` {.CodeMirror-line role="presentation"}
...
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 184px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 184px;"}
:::

This could be repeated on each controlplane and worker node where the
result should be the same.

 

###### Step 3: Check kube-proxy is creating iptables rules

Now we check the iptables rules on every node first manually:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ root@cka2556:~# iptables-save | grep p2-service
```
:::

``` {.CodeMirror-line role="presentation"}
-A KUBE-SEP-55IRFJIRWHLCQ6QX -s 10.44.0.31/32 -m comment --comment "project-hamster/p2-service" -j KUBE-MARK-MASQ
```

``` {.CodeMirror-line role="presentation"}
-A KUBE-SEP-55IRFJIRWHLCQ6QX -p tcp -m comment --comment "project-hamster/p2-service" -m tcp -j DNAT --to-destination 10.44.0.31:80
```

``` {.CodeMirror-line role="presentation"}
-A KUBE-SERVICES -d 10.105.128.247/32 -p tcp -m comment --comment "project-hamster/p2-service cluster IP" -m tcp --dport 3000 -j KUBE-SVC-U5ZRKF27Y7YDAZTN
```

``` {.CodeMirror-line role="presentation"}
-A KUBE-SVC-U5ZRKF27Y7YDAZTN ! -s 10.244.0.0/16 -d 10.105.128.247/32 -p tcp -m comment --comment "project-hamster/p2-service cluster IP" -m tcp --dport 3000 -j KUBE-MARK-MASQ
```

``` {.CodeMirror-line role="presentation"}
-A KUBE-SVC-U5ZRKF27Y7YDAZTN -m comment --comment "project-hamster/p2-service -> 10.44.0.31:80" -j KUBE-SEP-55IRFJIRWHLCQ6QX
```

``` {.CodeMirror-line role="presentation"}
# Warning: iptables-legacy tables present, use iptables-legacy-save to see them
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 276px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 276px;"}
:::

Great. Now let\'s write these logs into the requested file:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ root@cka2556:~# iptables-save | grep p2-service > /opt/course/p2/iptables.txt
```
:::
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 23px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 23px;"}
:::

 

###### Delete the *Service* and confirm iptables rules are gone

Delete the *Service* and confirm the iptables rules are gone::

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ root@cka2556:~# k -n project-hamster delete svc p2-service
```
:::

``` {.CodeMirror-line role="presentation"}
service "p2-service" deleted
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ root@cka2556:~# iptables-save | grep p2-service
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ root@cka2556:~#
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 138px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 138px;"}
:::

Kubernetes *Services* are implemented using iptables rules (with default
config) on all nodes. Every time a *Service* has been altered, created,
deleted or *Endpoints* of a *Service* have changed, the kube-apiserver
contacts every node\'s kube-proxy to update the iptables rules according
to the current state.

 

 

## Preview Question 3 \| Change Service CIDR {#preview-question-3-|-change-service-cidr}

 

Solve this question on: `ssh cka9412`{.copy-on-click}

 

1.  Create a *Pod* named `check-ip`{.copy-on-click} in *Namespace*
    `default`{.copy-on-click} using image
    `httpd:2-alpine`{.copy-on-click}

2.  Expose it on port `80`{.copy-on-click} as a ClusterIP *Service*
    named `check-ip-service`{.copy-on-click}. Remember/output the IP of
    that *Service*

3.  Change the Service CIDR to `11.96.0.0/12`{.copy-on-click} for the
    cluster

4.  Create a second *Service* named `check-ip-service2`{.copy-on-click}
    pointing to the same *Pod*

 

> ℹ️ The second *Service* should get an IP address from the new CIDR
> range

 

 

##### Answer: {#answer-3}

Let\'s create the *Pod* and expose it:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ ssh cka9412
```
:::

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka9412:~$ k run check-ip --image=httpd:2-alpine
```

``` {.CodeMirror-line role="presentation"}
pod/check-ip created
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka9412:~$ k expose pod check-ip --name check-ip-service --port 80
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 138px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 138px;"}
:::

And check the *Service* IP:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka9412:~$ k get svc
```
:::

``` {.CodeMirror-line role="presentation"}
NAME               TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
```

``` {.CodeMirror-line role="presentation"}
check-ip-service   ClusterIP   10.109.84.110   <none>        80/TCP    13s
```

``` {.CodeMirror-line role="presentation"}
kubernetes         ClusterIP   10.96.0.1       <none>        443/TCP   9d
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 92px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 92px;"}
:::

Now we change the *Service* CIDR in the kube-apiserver manifest:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ candidate@cka9412:~$ sudo -i
```
:::

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ root@cka9412:~# vim /etc/kubernetes/manifests/kube-apiserver.yaml
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 69px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 69px;"}
:::

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="yaml" style="break-inside: unset;"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
# cka9412:/etc/kubernetes/manifests/kube-apiserver.yaml
```
:::

``` {.CodeMirror-line role="presentation"}
apiVersion: v1
```

``` {.CodeMirror-line role="presentation"}
kind: Pod
```

``` {.CodeMirror-line role="presentation"}
metadata:
```

``` {.CodeMirror-line role="presentation"}
  creationTimestamp: null
```

``` {.CodeMirror-line role="presentation"}
  labels:
```

``` {.CodeMirror-line role="presentation"}
    component: kube-apiserver
```

``` {.CodeMirror-line role="presentation"}
    tier: control-plane
```

``` {.CodeMirror-line role="presentation"}
  name: kube-apiserver
```

``` {.CodeMirror-line role="presentation"}
  namespace: kube-system
```

``` {.CodeMirror-line role="presentation"}
spec:
```

``` {.CodeMirror-line role="presentation"}
  containers:
```

``` {.CodeMirror-line role="presentation"}
  - command:
```

``` {.CodeMirror-line role="presentation"}
    - kube-apiserver
```

``` {.CodeMirror-line role="presentation"}
    - --advertise-address=192.168.100.21
```

``` {.CodeMirror-line role="presentation"}
...
```

``` {.CodeMirror-line role="presentation"}
    - --service-account-key-file=/etc/kubernetes/pki/sa.pub
```

``` {.CodeMirror-line role="presentation"}
    - --service-cluster-ip-range=11.96.0.0/12             # change
```

``` {.CodeMirror-line role="presentation"}
    - --tls-cert-file=/etc/kubernetes/pki/apiserver.crt
```

``` {.CodeMirror-line role="presentation"}
    - --tls-private-key-file=/etc/kubernetes/pki/apiserver.key
```

``` {.CodeMirror-line role="presentation"}
...
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 420px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 420px;"}
:::

We wait for the kube-apiserver to be restarted, which can take a minute:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ root@cka9412:~# watch crictl ps
```
:::

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ root@cka9412:~# kubectl -n kube-system get pod | grep api
```

``` {.CodeMirror-line role="presentation"}
kube-apiserver-cka9412            1/1     Running   0             20s
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 92px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 92px;"}
:::

Now we do the same for the controller manager:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ root@cka9412:~# vim /etc/kubernetes/manifests/kube-controller-manager.yaml
```
:::
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 23px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 23px;"}
:::

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="yaml" style="break-inside: unset;"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
# /etc/kubernetes/manifests/kube-controller-manager.yaml
```
:::

``` {.CodeMirror-line role="presentation"}
apiVersion: v1
```

``` {.CodeMirror-line role="presentation"}
kind: Pod
```

``` {.CodeMirror-line role="presentation"}
metadata:
```

``` {.CodeMirror-line role="presentation"}
  creationTimestamp: null
```

``` {.CodeMirror-line role="presentation"}
  labels:
```

``` {.CodeMirror-line role="presentation"}
    component: kube-controller-manager
```

``` {.CodeMirror-line role="presentation"}
    tier: control-plane
```

``` {.CodeMirror-line role="presentation"}
  name: kube-controller-manager
```

``` {.CodeMirror-line role="presentation"}
  namespace: kube-system
```

``` {.CodeMirror-line role="presentation"}
spec:
```

``` {.CodeMirror-line role="presentation"}
  containers:
```

``` {.CodeMirror-line role="presentation"}
  - command:
```

``` {.CodeMirror-line role="presentation"}
    - kube-controller-manager
```

``` {.CodeMirror-line role="presentation"}
    - --allocate-node-cidrs=true
```

``` {.CodeMirror-line role="presentation"}
    - --authentication-kubeconfig=/etc/kubernetes/controller-manager.conf
```

``` {.CodeMirror-line role="presentation"}
    - --authorization-kubeconfig=/etc/kubernetes/controller-manager.conf
```

``` {.CodeMirror-line role="presentation"}
    - --bind-address=127.0.0.1
```

``` {.CodeMirror-line role="presentation"}
    - --client-ca-file=/etc/kubernetes/pki/ca.crt
```

``` {.CodeMirror-line role="presentation"}
    - --cluster-cidr=10.244.0.0/16
```

``` {.CodeMirror-line role="presentation"}
    - --cluster-name=kubernetes
```

``` {.CodeMirror-line role="presentation"}
    - --cluster-signing-cert-file=/etc/kubernetes/pki/ca.crt
```

``` {.CodeMirror-line role="presentation"}
    - --cluster-signing-key-file=/etc/kubernetes/pki/ca.key
```

``` {.CodeMirror-line role="presentation"}
    - --controllers=*,bootstrapsigner,tokencleaner
```

``` {.CodeMirror-line role="presentation"}
    - --kubeconfig=/etc/kubernetes/controller-manager.conf
```

``` {.CodeMirror-line role="presentation"}
    - --leader-elect=true
```

``` {.CodeMirror-line role="presentation"}
    - --node-cidr-mask-size=24
```

``` {.CodeMirror-line role="presentation"}
    - --requestheader-client-ca-file=/etc/kubernetes/pki/front-proxy-ca.crt
```

``` {.CodeMirror-line role="presentation"}
    - --root-ca-file=/etc/kubernetes/pki/ca.crt
```

``` {.CodeMirror-line role="presentation"}
    - --service-account-private-key-file=/etc/kubernetes/pki/sa.key
```

``` {.CodeMirror-line role="presentation"}
    - --service-cluster-ip-range=11.96.0.0/12         # change
```

``` {.CodeMirror-line role="presentation"}
    - --use-service-account-credentials=true
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 640px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 640px;"}
:::

We wait for the kube-controller-manager to be restarted, which can take
a minute:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ root@cka9412:~# watch crictl ps
```
:::

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
➜ root@cka9412:~# kubectl -n kube-system get pod | grep controller
```

``` {.CodeMirror-line role="presentation"}
kube-controller-manager-cka9412   1/1     Running   0               39s
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 92px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 92px;"}
:::

Checking our *Service* again:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ root@cka9412:~$ k get svc
```
:::

``` {.CodeMirror-line role="presentation"}
NAME               TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
```

``` {.CodeMirror-line role="presentation"}
check-ip-service   ClusterIP   10.109.84.110   <none>        80/TCP    5m3s
```

``` {.CodeMirror-line role="presentation"}
kubernetes         ClusterIP   10.96.0.1       <none>        443/TCP   9d
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 92px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 92px;"}
:::

Nothing changed so far. Now we create the second *Service*:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ root@cka9412:~$ k expose pod check-ip --name check-ip-service2 --port 80
```
:::
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 23px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 23px;"}
:::

And check again:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="term"}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation" style=""}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
➜ root@cka9412:~$ k get svc
```
:::

``` {.CodeMirror-line role="presentation"}
NAME                        TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
```

``` {.CodeMirror-line role="presentation"}
service/check-ip-service    ClusterIP   10.109.84.110   <none>        80/TCP    5m55s
```

``` {.CodeMirror-line role="presentation"}
service/check-ip-service2   ClusterIP   11.105.52.114   <none>        80/TCP    29s
```

``` {.CodeMirror-line role="presentation"}
service/kubernetes          ClusterIP   10.96.0.1       <none>        443/TCP   9d
```

``` {.CodeMirror-line role="presentation"}
```

``` {.CodeMirror-line role="presentation"}
NAME                          ENDPOINTS             AGE
```

``` {.CodeMirror-line role="presentation"}
endpoints/check-ip-service    10.44.0.3:80          5m55s
```

``` {.CodeMirror-line role="presentation"}
endpoints/check-ip-service2   10.44.0.3:80          29s
```

``` {.CodeMirror-line role="presentation"}
endpoints/kubernetes          192.168.100.21:6443   9d
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 230px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 230px;"}
:::

There we go, the new *Service* got an IP of the updated range assigned.
We also see that both *Services* have our *Pod* as endpoint.

 

[]{#tips v-1242acb3=""}

::: {.root v-7ffbb0f0="" v-1242acb3=""}
::: {v-7ffbb0f0=""}
[[ ]{v-7ffbb0f0=""}]{v-7ffbb0f0=""}

tips

::: typora-export-content
::: {#write}
# CKA Tips Kubernetes 1.32 {#cka-tips-kubernetes-132}

In this section we\'ll provide some tips on how to handle the CKA exam
and browser terminal.

 

## Knowledge

Study all topics as proposed in the curriculum till you feel comfortable
with all.

 

**General**

-   Study all topics as proposed in the curriculum till you feel
    comfortable with all

-   Do 1 or 2 test session with this CKA Simulator. Understand the
    solutions and maybe try out other ways to achieve the same thing.

-   Setup your aliases, be fast and breath `kubectl`{.copy-on-click}

-   The majority of tasks in the CKA will also be around creating
    Kubernetes resources, like it\'s tested in the CKAD. So preparing a
    bit for the CKAD can\'t hurt.

-   Learn and Study the in-browser scenarios on
    [https://killercoda.com/killer-shell-cka](https://killercoda.com/killer-shell-cka){.url
    target="_blank"} (and maybe for CKAD
    [https://killercoda.com/killer-shell-ckad](https://killercoda.com/killer-shell-ckad){.url
    target="_blank"})

-   Imagine and create your own scenarios to solve

 

**Components**

-   Understanding Kubernetes components and being able to fix and
    investigate clusters:
    [https://kubernetes.io/docs/tasks/debug-application-cluster/debug-cluster](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-cluster){.url
    target="_blank"}

-   Know advanced scheduling:
    [https://kubernetes.io/docs/concepts/scheduling/kube-scheduler](https://kubernetes.io/docs/concepts/scheduling/kube-scheduler){.url
    target="_blank"}

-   When you have to fix a component (like kubelet) in one cluster, just
    check how it\'s setup on another node in the same or even another
    cluster. You can copy config files over etc

-   If you like you can look at [Kubernetes The Hard
    Way](https://github.com/kelseyhightower/kubernetes-the-hard-way)
    once. But it\'s NOT necessary to do, the CKA is not that complex.
    But KTHW helps understanding the concepts

-   You should install your own cluster using kubeadm (one controlplane,
    one worker) in a VM or using a cloud provider and investigate the
    components

-   Know how to use Kubeadm to for example add nodes to a cluster

-   Know how to create an Ingress resources

-   Know how to snapshot/restore ETCD from another machine

 

 

## CKA Exam Info

**Read the Curriculum**

[https://github.com/cncf/curriculum](https://github.com/cncf/curriculum){.url
target="_blank"}

**Read the Handbook**

[https://docs.linuxfoundation.org/tc-docs/certification/lf-handbook2](https://docs.linuxfoundation.org/tc-docs/certification/lf-handbook2){.url
target="_blank"}

**Read the important tips**

[https://docs.linuxfoundation.org/tc-docs/certification/tips-cka-and-ckad](https://docs.linuxfoundation.org/tc-docs/certification/tips-cka-and-ckad){.url
target="_blank"}

**Read the FAQ**

[https://docs.linuxfoundation.org/tc-docs/certification/faq-cka-ckad](https://docs.linuxfoundation.org/tc-docs/certification/faq-cka-ckad){.url
target="_blank"}

 

## Kubernetes documentation

Get familiar with the Kubernetes documentation and be able to use the
search. Allowed resources are:

-   [https://kubernetes.io/docs](https://kubernetes.io/docs){.url
    target="_blank"}

-   [https://kubernetes.io/blog](https://kubernetes.io/blog){.url
    target="_blank"}

-   [https://helm.sh/docs](https://helm.sh/docs){.url target="_blank"}

-   [https://gateway-api.sigs.k8s.io](https://gateway-api.sigs.k8s.io/){.url
    target="_blank"}

> ℹ️ Verify the list
> [here](https://docs.linuxfoundation.org/tc-docs/certification/certification-resources-allowed)

 

## The Exam UI / Remote Desktop {#the-exam-ui--remote-desktop}

The real exam, as well as the simulator, provides a Remote Desktop
(XFCE) on Ubuntu/Debian. Coming from OSX/Windows there will be changes
in copy&paste for example.

**Official Information**

[ExamUI: Performance Based
Exams](https://docs.linuxfoundation.org/tc-docs/certification/lf-handbook2/exam-user-interface/examui-performance-based-exams)

**Lagging**

There could be some lagging, definitely make sure you are using a good
internet connection because your webcam and screen are transferring all
the time.

**Kubectl autocompletion and commands**

The following are installed or pre-configured, verify the list
[here](https://docs.linuxfoundation.org/tc-docs/certification/tips-cka-and-ckad):

-   `kubectl`{.copy-on-click} with `k`{.copy-on-click} alias and Bash
    autocompletion

-   `yq`{.copy-on-click} or YAML processing

-   `curl`{.copy-on-click} and `wget`{.copy-on-click} for testing web
    services

-   `man`{.copy-on-click} and man pages for further documentation

> ℹ️ You\'re allowed to install tools, like `tmux`{.copy-on-click} for
> terminal multiplexing or `jq`{.copy-on-click} for JSON processing

**Copy & Paste**

Copy and pasting will work like normal in a Linux Environment:

What always works: copy+paste using right mouse context menu What works
in Terminal: Ctrl+Shift+c and Ctrl+Shift+v What works in other apps like
Firefox: Ctrl+c and Ctrl+v

**Score**

There are 15-20 questions in the exam. Your results will be
automatically checked according to the handbook. If you don\'t agree
with the results you can request a review by contacting the Linux
Foundation Support.

**Notepad & Flagging Questions**

You can flag questions to return to later. This is just a marker for
yourself and won\'t affect scoring. You also have access to a simple
notepad in the browser which can be used to store any kind of plain
text. It might make sense to use this and write down additional
information about flagged questions. Instead of using the notepad you
could also open Mousepad (XFCE application inside the Remote Desktop) or
create a file with Vim.

**VSCodium**

You can use VSCodium to edit files and you can also use its terminal to
run commands. You\'re not allowed to install any VSCodium extensions.

**Servers**

Each question needs to be solved on a specific instance other than your
main terminal. You\'ll need to connect to the correct instance via ssh,
the command is provided before each question.

 

## PSI Bridge

Starting with [PSI
Bridge](https://training.linuxfoundation.org/bridge-migration-2021):

-   The exam will now be taken using the PSI Secure Browser, which can
    be downloaded using the newest versions of Microsoft Edge, Safari,
    Chrome, or Firefox

-   Multiple monitors will no longer be permitted

-   Use of personal bookmarks will no longer be permitted

The new ExamUI includes improved features such as:

-   A remote desktop configured with the tools and software needed to
    complete the tasks

-   A timer that displays the actual time remaining (in minutes) and
    provides an alert with 30, 15, or 5 minute remaining

-   The content panel remains the same (presented on the Left Hand Side
    of the ExamUI)

Read more
[here](https://training.linuxfoundation.org/bridge-migration-2021).

 

## Terminal Handling

 

### Bash Aliases

In the real exam, each question has to be solved on a different instance
to which you connect via ssh. This means it\'s not advised to configure
bash aliases because they wouldn\'t be available on the instances
accessed by ssh.

 

### Be fast

Use the `history`{.copy-on-click} command to reuse already entered
commands or use even faster history search through **Ctrl r **.

If a command takes some time to execute, like sometimes
`kubectl delete pod x`{.copy-on-click}. You can put a task in the
background using **Ctrl z** and pull it back into foreground running
command `fg`{.copy-on-click}.

You can delete *pods* fast with:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang="bash"}
k delete pod x --grace-period 0 --force
```
:::
:::
:::
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 23px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 23px;"}
:::

 

### Vim

Be great with vim.

**Settings**

In case you face a situation where vim is not configured properly and
you face for example issues with pasting copied content you should be
able to configure via `~/.vimrc`{.copy-on-click} or by entering manually
in vim settings mode:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang=""}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
set tabstop=2
```
:::

``` {.CodeMirror-line role="presentation"}
set expandtab
```

``` {.CodeMirror-line role="presentation"}
set shiftwidth=2
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 69px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 69px;"}
:::

The `expandtab`{.copy-on-click} make sure to use spaces for tabs.

Note that changes in `~/.vimrc`{.copy-on-click} will not be transferred
when connecting to other instances via ssh.

**Toggle vim line numbers**

When in `vim`{.copy-on-click} you can press **Esc** and type
`:set number`{.copy-on-click} or `:set nonumber`{.copy-on-click}
followed by **Enter** to toggle line numbers. This can be useful when
finding syntax errors based on line - but can be bad when wanting to
mark&copy by mouse. You can also just jump to a line number with **Esc**
`:22`{.copy-on-click} + **Enter**.

**Copy&Paste**

Get used to copy/paste/cut with vim:

``` {.md-fences .md-end-block .ty-contain-cm .modeLoaded spellcheck="false" lang=""}
xxxxxxxxxx
```

::: CodeMirror-measure
:::

::: {style="position: relative; z-index: 1;"}
:::

::: {.CodeMirror-code role="presentation"}
::: {.CodeMirror-activeline style="position: relative;"}
::: {.CodeMirror-activeline-background .CodeMirror-linebackground}
:::

::: {.CodeMirror-gutter-background .CodeMirror-activeline-gutter style="left: 0px; width: 0px;"}
:::

``` {.CodeMirror-line role="presentation"}
Mark lines: Esc+V (then arrow keys)
```
:::

``` {.CodeMirror-line role="presentation"}
Copy marked lines: y
```

``` {.CodeMirror-line role="presentation"}
Cut marked lines: d
```

``` {.CodeMirror-line role="presentation"}
Past lines: p or P
```
:::

::: {style="position: absolute; height: 0px; width: 1px; border-bottom-width: 0px; border-bottom-style: solid; border-bottom-color: transparent; top: 92px;"}
:::

::: {.CodeMirror-gutters style="display: none; height: 92px;"}
:::

**Indent multiple lines**

To indent multiple lines press **Esc** and type
`:set shiftwidth=2`{.copy-on-click}. First mark multiple lines using
`Shift v`{.copy-on-click} and the up/down keys. Then to indent the
marked lines press `>`{.copy-on-click} or `<`{.copy-on-click}. You can
then press `.`{.copy-on-click} to repeat the action.

::: {.container .mx-auto}
::: {.row .d-flex .justify-content-center}
::: {.col-sm-12 .col-md-4}
[![logo](data:image/svg+xml,%3c?xml%20version='1.0'%20encoding='utf-8'?%3e%3c!--%20Generator:%20Adobe%20Illustrator%2026.0.2,%20SVG%20Export%20Plug-In%20.%20SVG%20Version:%206.00%20Build%200)%20--%3e%3csvg%20version='1.1'%20id='Ebene_1'%20xmlns='http://www.w3.org/2000/svg'%20xmlns:xlink='http://www.w3.org/1999/xlink'%20x='0px'%20y='0px'%20viewBox='0%200%201917.9%20781.8'%20style='enable-background:new%200%200%201917.9%20781.8;'%20xml:space='preserve'%3e%3cstyle%20type='text/css'%3e%20.st0%7Bfill:%23FFFFFF;%7D%20%3c/style%3e%3cpath%20class='st0'%20d='M1743.1,138.3L1890.8,0h-235.6v286.3h43V150.1l138.7,136.2h58.9L1743.1,138.3z%20M1698.1,42.9h94.1l-94.1,90.4%20V42.9z'/%3e%3cg%3e%3cdefs%3e%3crect%20id='SVGID_1_'%20x='-6.4'%20y='0'%20width='1902.1'%20height='781.8'/%3e%3c/defs%3e%3cclipPath%20id='SVGID_00000106116965778409552490000011615941348305333637_'%3e%3cuse%20xlink:href='%23SVGID_1_'%20style='overflow:visible;'/%3e%3c/clipPath%3e%3c/g%3e%3cpolygon%20class='st0'%20points='1358.8,777.7%201358.8,739.7%201194,739.7%201194,491.4%201151,491.4%201151,777.7%20'/%3e%3cpolygon%20class='st0'%20points='1863,777.7%201863,739.7%201698.1,739.7%201698.1,491.4%201655.2,491.4%201655.2,777.7%20'/%3e%3cpolygon%20class='st0'%20points='756.4,489.2%20756.4,609.6%20612.6,609.6%20612.6,489.2%20571.6,489.2%20571.6,777.7%20612.6,777.7%20612.6,647.3%20756.4,647.3%20756.4,777.7%20797.4,777.7%20797.4,489.2%20'/%3e%3cpolygon%20class='st0'%20points='193.6,0%2054.9,133.4%2054.9,0%2012,0%2012,286.3%2054.9,286.3%2054.9,150.1%20193.6,286.3%20252.5,286.3%2099.9,138.3%20247.6,0%20'/%3e%3cpolygon%20class='st0'%20points='580.6,0%20580.6,286.3%20788.4,286.3%20788.4,248.3%20623.5,248.3%20623.5,0%20'/%3e%3cpolygon%20class='st0'%20points='1151,0%201151,286.3%201358.8,286.3%201358.8,248.3%201194,248.3%201194,0%20'/%3e%3cpath%20class='st0'%20d='M211.8,661.7c-5.1-10.1-12-18.5-20.3-24.9c-8.3-6.4-17.5-11.7-27.5-15.8c-9.8-4.1-19.9-7.9-30-11.4%20c-9.9-3.4-19.1-7.2-27.3-11.3c-8.1-4-14.7-9-19.7-14.8c-4.9-5.7-7.4-13.3-7.4-22.6c0-11.7,4.4-20.9,13.2-27.4c8.8-6.6,21-9.9,36-9.9%20c12.4,0,23.5,2.5,33.1,7.3c9.6,4.9,18.7,12.3,27.1,22l0.8,0.9l27.9-27.9l-0.6-0.8c-9.4-11.9-21.7-21.6-36.7-29%20c-14.9-7.3-32-11-50.8-11c-17.1,0-32.7,3.2-46.4,9.4c-13.7,6.2-24.7,15.2-32.6,26.8c-7.9,11.5-11.9,25.7-11.9,42.1%20c0,14.9,2.5,27.4,7.5,37c5,9.6,11.9,17.6,20.3,23.7c8.4,6.1,17.7,11.2,27.7,15.2c9.8,4,19.8,7.6,29.8,11c9.8,3.3,18.9,7.1,27.1,11.3%20c8.1,4.2,14.7,9.5,19.6,16c4.9,6.4,7.4,14.9,7.4,25.3c0,12.5-5,22.4-15,29.6c-10.1,7.3-23.7,10.9-40.4,10.9%20c-17.1,0-31.6-3.2-43.2-9.6c-11.6-6.3-22-15.9-31-28.3l-0.8-1.1l-28,28l0.6,0.8c12.4,15.7,26.8,27.9,42.8,36.2%20c16,8.3,35.5,12.5,57.8,12.5c30.1,0,54.2-7.3,71.8-21.7c17.6-14.4,26.6-34.7,26.6-60.2C219.6,684.7,217,671.8,211.8,661.7z'/%3e%3c/svg%3e){width="90px"}](https://killer.sh/)[](https://killer.sh/)

About

[](https://killer.sh/faq)

FAQ

[](https://killer.sh/support)

Support

[](https://killer.sh/order)

Store

[](https://killer.sh/pricing)

Pricing

[](https://killer.sh/impressum.html){target="_blank"}

Impressum

[](https://killer.sh/datenschutz.html){target="_blank"}

Datenschutz

[](https://killer.sh/agb.html){target="_blank"}

AGB
:::

::: {.col-sm-12 .col-md-4}
CONTENT

[](https://killer.sh/cks)

CKS

[](https://killer.sh/cka)

CKA

[](https://killer.sh/ckad)

CKAD

[](https://killer.sh/lfcs)

LFCS
:::

::: {.col-sm-12 .col-md-4}
LINKS

[Killercoda](https://killercoda.com/){.small target="_blank"}

[Kim Wuestkamp](https://www.linkedin.com/in/kimwuestkamp){.small
target="_blank"}

[![](data:image/svg+xml;base64,PHN2ZyBjbGFzcz0ic3ZnLWlubGluZS0tZmEgZmEtdHdpdHRlciBmYS1sZyBtZS0zIiBhcmlhLWhpZGRlbj0idHJ1ZSIgZm9jdXNhYmxlPSJmYWxzZSIgZGF0YS1wcmVmaXg9ImZhYiIgZGF0YS1pY29uPSJ0d2l0dGVyIiByb2xlPSJpbWciIHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8yMDAwL3N2ZyIgdmlld2JveD0iMCAwIDUxMiA1MTIiPjxwYXRoIGNsYXNzIGZpbGw9ImN1cnJlbnRDb2xvciIgZD0iTTQ1OS4zNyAxNTEuNzE2Yy4zMjUgNC41NDguMzI1IDkuMDk3LjMyNSAxMy42NDUgMCAxMzguNzItMTA1LjU4MyAyOTguNTU4LTI5OC41NTggMjk4LjU1OC01OS40NTIgMC0xMTQuNjgtMTcuMjE5LTE2MS4xMzctNDcuMTA2IDguNDQ3Ljk3NCAxNi41NjggMS4yOTkgMjUuMzQgMS4yOTkgNDkuMDU1IDAgOTQuMjEzLTE2LjU2OCAxMzAuMjc0LTQ0LjgzMi00Ni4xMzItLjk3NS04NC43OTItMzEuMTg4LTk4LjExMi03Mi43NzIgNi40OTguOTc0IDEyLjk5NSAxLjYyNCAxOS44MTggMS42MjQgOS40MjEgMCAxOC44NDMtMS4zIDI3LjYxNC0zLjU3My00OC4wODEtOS43NDctODQuMTQzLTUxLjk4LTg0LjE0My0xMDIuOTg1di0xLjI5OWMxMy45NjkgNy43OTcgMzAuMjE0IDEyLjY3IDQ3LjQzMSAxMy4zMTktMjguMjY0LTE4Ljg0My00Ni43ODEtNTEuMDA1LTQ2Ljc4MS04Ny4zOTEgMC0xOS40OTIgNS4xOTctMzcuMzYgMTQuMjk0LTUyLjk1NCA1MS42NTUgNjMuNjc1IDEyOS4zIDEwNS4yNTggMjE2LjM2NSAxMDkuODA3LTEuNjI0LTcuNzk3LTIuNTk5LTE1LjkxOC0yLjU5OS0yNC4wNCAwLTU3LjgyOCA0Ni43ODItMTA0LjkzNCAxMDQuOTM0LTEwNC45MzQgMzAuMjEzIDAgNTcuNTAyIDEyLjY3IDc2LjY3IDMzLjEzNyAyMy43MTUtNC41NDggNDYuNDU2LTEzLjMyIDY2LjU5OS0yNS4zNC03Ljc5OCAyNC4zNjYtMjQuMzY2IDQ0LjgzMy00Ni4xMzIgNTcuODI3IDIxLjExNy0yLjI3MyA0MS41ODQtOC4xMjIgNjAuNDI2LTE2LjI0My0xNC4yOTIgMjAuNzkxLTMyLjE2MSAzOS4zMDgtNTIuNjI4IDU0LjI1M3oiIC8+PC9zdmc+){.svg-inline--fa
.fa-twitter .fa-lg .me-3}](https://twitter.com/_killer_shell){.small
target="_blank"}

[![](data:image/svg+xml;base64,PHN2ZyBjbGFzcz0ic3ZnLWlubGluZS0tZmEgZmEtbGlua2VkaW4gZmEtbGcgbWUtMyIgYXJpYS1oaWRkZW49InRydWUiIGZvY3VzYWJsZT0iZmFsc2UiIGRhdGEtcHJlZml4PSJmYWIiIGRhdGEtaWNvbj0ibGlua2VkaW4iIHJvbGU9ImltZyIgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvc3ZnIiB2aWV3Ym94PSIwIDAgNDQ4IDUxMiI+PHBhdGggY2xhc3MgZmlsbD0iY3VycmVudENvbG9yIiBkPSJNNDE2IDMySDMxLjlDMTQuMyAzMiAwIDQ2LjUgMCA2NC4zdjM4My40QzAgNDY1LjUgMTQuMyA0ODAgMzEuOSA0ODBINDE2YzE3LjYgMCAzMi0xNC41IDMyLTMyLjNWNjQuM2MwLTE3LjgtMTQuNC0zMi4zLTMyLTMyLjN6TTEzNS40IDQxNkg2OVYyMDIuMmg2Ni41VjQxNnptLTMzLjItMjQzYy0yMS4zIDAtMzguNS0xNy4zLTM4LjUtMzguNVM4MC45IDk2IDEwMi4yIDk2YzIxLjIgMCAzOC41IDE3LjMgMzguNSAzOC41IDAgMjEuMy0xNy4yIDM4LjUtMzguNSAzOC41em0yODIuMSAyNDNoLTY2LjRWMzEyYzAtMjQuOC0uNS01Ni43LTM0LjUtNTYuNy0zNC42IDAtMzkuOSAyNy0zOS45IDU0LjlWNDE2aC02Ni40VjIwMi4yaDYzLjd2MjkuMmguOWM4LjktMTYuOCAzMC42LTM0LjUgNjIuOS0zNC41IDY3LjIgMCA3OS43IDQ0LjMgNzkuNyAxMDEuOVY0MTZ6IiAvPjwvc3ZnPg==){.svg-inline--fa
.fa-linkedin .fa-lg
.me-3}](https://www.linkedin.com/company/killer-shell){.small
target="_blank"}

[![](data:image/svg+xml;base64,PHN2ZyBjbGFzcz0ic3ZnLWlubGluZS0tZmEgZmEtc2xhY2sgZmEtbGcgbWUtMyIgYXJpYS1oaWRkZW49InRydWUiIGZvY3VzYWJsZT0iZmFsc2UiIGRhdGEtcHJlZml4PSJmYWIiIGRhdGEtaWNvbj0ic2xhY2siIHJvbGU9ImltZyIgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvc3ZnIiB2aWV3Ym94PSIwIDAgNDQ4IDUxMiI+PHBhdGggY2xhc3MgZmlsbD0iY3VycmVudENvbG9yIiBkPSJNOTQuMTIgMzE1LjFjMCAyNS45LTIxLjE2IDQ3LjA2LTQ3LjA2IDQ3LjA2UzAgMzQxIDAgMzE1LjFjMC0yNS45IDIxLjE2LTQ3LjA2IDQ3LjA2LTQ3LjA2aDQ3LjA2djQ3LjA2em0yMy43MiAwYzAtMjUuOSAyMS4xNi00Ny4wNiA0Ny4wNi00Ny4wNnM0Ny4wNiAyMS4xNiA0Ny4wNiA0Ny4wNnYxMTcuODRjMCAyNS45LTIxLjE2IDQ3LjA2LTQ3LjA2IDQ3LjA2cy00Ny4wNi0yMS4xNi00Ny4wNi00Ny4wNlYzMTUuMXptNDcuMDYtMTg4Ljk4Yy0yNS45IDAtNDcuMDYtMjEuMTYtNDcuMDYtNDcuMDZTMTM5IDMyIDE2NC45IDMyczQ3LjA2IDIxLjE2IDQ3LjA2IDQ3LjA2djQ3LjA2SDE2NC45em0wIDIzLjcyYzI1LjkgMCA0Ny4wNiAyMS4xNiA0Ny4wNiA0Ny4wNnMtMjEuMTYgNDcuMDYtNDcuMDYgNDcuMDZINDcuMDZDMjEuMTYgMjQzLjk2IDAgMjIyLjggMCAxOTYuOXMyMS4xNi00Ny4wNiA0Ny4wNi00Ny4wNkgxNjQuOXptMTg4Ljk4IDQ3LjA2YzAtMjUuOSAyMS4xNi00Ny4wNiA0Ny4wNi00Ny4wNiAyNS45IDAgNDcuMDYgMjEuMTYgNDcuMDYgNDcuMDZzLTIxLjE2IDQ3LjA2LTQ3LjA2IDQ3LjA2aC00Ny4wNlYxOTYuOXptLTIzLjcyIDBjMCAyNS45LTIxLjE2IDQ3LjA2LTQ3LjA2IDQ3LjA2LTI1LjkgMC00Ny4wNi0yMS4xNi00Ny4wNi00Ny4wNlY3OS4wNmMwLTI1LjkgMjEuMTYtNDcuMDYgNDcuMDYtNDcuMDYgMjUuOSAwIDQ3LjA2IDIxLjE2IDQ3LjA2IDQ3LjA2VjE5Ni45ek0yODMuMSAzODUuODhjMjUuOSAwIDQ3LjA2IDIxLjE2IDQ3LjA2IDQ3LjA2IDAgMjUuOS0yMS4xNiA0Ny4wNi00Ny4wNiA0Ny4wNi0yNS45IDAtNDcuMDYtMjEuMTYtNDcuMDYtNDcuMDZ2LTQ3LjA2aDQ3LjA2em0wLTIzLjcyYy0yNS45IDAtNDcuMDYtMjEuMTYtNDcuMDYtNDcuMDYgMC0yNS45IDIxLjE2LTQ3LjA2IDQ3LjA2LTQ3LjA2aDExNy44NGMyNS45IDAgNDcuMDYgMjEuMTYgNDcuMDYgNDcuMDYgMCAyNS45LTIxLjE2IDQ3LjA2LTQ3LjA2IDQ3LjA2SDI4My4xeiIgLz48L3N2Zz4=){.svg-inline--fa
.fa-slack .fa-lg .me-3}](https://killer.sh/slack){.small}
:::
:::
:::

<div>

</div>
