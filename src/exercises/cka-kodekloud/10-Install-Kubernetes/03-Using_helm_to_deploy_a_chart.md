# Using Helm to deploy a chart

Arise, awake, and stop not till the goal is reached.

– Swami Vivekananda

1. The helm package that contains all of the resource definitions necessary to run an application, tool, or service inside of a Kubernetes cluster is known as a …

**chart**

2. We cannot install the same chart multiple times on the same Kubernetes Cluster.

**False**

3. Which command is used to search for a wordpress helm chart package from the Artifact Hub?

**helm search hub wordpress**

```bash
URL                                                     CHART VERSION   APP VERSION             DESCRIPTION                                       
https://artifacthub.io/packages/helm/kube-wordp...      0.1.0           1.1                     this is my wordpress package                      
https://artifacthub.io/packages/helm/wordpress-...      1.0.2           1.0.0                   A Helm chart for deploying Wordpress+Mariadb st...
https://artifacthub.io/packages/helm/bizlinked/...      26.0.0          6.8.2                   WordPress is the world's most popular blogging ...
https://artifacthub.io/packages/helm/bitnami/wo...      26.0.0          6.8.2                   WordPress is the world's most popular blogging ...
https://artifacthub.io/packages/helm/bitnami-ak...      15.2.13         6.1.0                   WordPress is the world's most popular blogging ...
https://artifacthub.io/packages/helm/shubham-wo...      0.1.0           1.16.0                  A Helm chart for Kubernetes          
...
```

4. Search for a consul helm chart package from the Artifact Hub and identify the APP VERSION for the Official HashiCorp Consul Chart.

**1.21.4**

```bash
helm search hub consul | grep hashicorp

https://artifacthub.io/packages/helm/hashicorp/...      1.8.1           1.21.4          Official HashiCorp Consul Chart 
```

5. Add bitnami helm chart repository in the controlplane node.
The url for bitnami repository is https://charts.bitnami.com/bitnami

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
```

6. Which command is used to search for the wordpress package from the newly added bitnami repository?

**helm search repo wordpress**

Este comando busca el paquete de WordPress en todos los repositorios añadidos, incluyendo el repositorio de Bitnami.

```bash
helm search repo wordpress
NAME                    CHART VERSION   APP VERSION     DESCRIPTION                                       
bitnami/wordpress       26.0.0          6.8.2           WordPress is the world's most popular blogging ...
bitnami/wordpress-intel 2.1.31          6.1.1           DEPRECATED WordPress for Intel is the most popu...
```

7. How many helm chart repositories are there in the controlplane node now?
We have added a few helm chart repositories in the controlplane node now.

**three**

```bash
helm repo list
NAME            URL                                                 
bitnami         https://charts.bitnami.com/bitnami                  
puppet          https://puppetlabs.github.io/puppetserver-helm-chart
hashicorp       https://helm.releases.hashicorp.com     
```

8. Deploy the Apache application on the cluster using the apache from the bitnami repository.
Set the release Name to: amaze-surf

Run the following command to install:

```bash
helm install amaze-surf bitnami/apache
```

```bash
Pulled: us-central1-docker.pkg.dev/kk-lab-prod/helm-charts/bitnami/apache:11.3.2
Digest: sha256:1bd45c97bb7a0000534e3abc5797143661e34ea7165aa33068853c567e6df9f2
NAME: amaze-surf
LAST DEPLOYED: Mon Sep  8 10:22:17 2025
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
CHART NAME: apache
CHART VERSION: 11.3.2
APP VERSION: 2.4.63
...
```

9. What version of apache did we just install on the cluster using the helm chart?

**2.4.63**

10. How many releases of nginx charts can you see installed in the cluster now?

Note: We just installed some charts

**two**

```bash
helm list
NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
amaze-surf      default         1               2025-09-08 10:22:17.497025402 +0000 UTC deployed        apache-11.3.2   2.4.63     
crazy-web       default         1               2025-09-08 10:24:39.730663221 +0000 UTC deployed        nginx-19.0.0    1.27.4     
happy-browse    default         1               2025-09-08 10:24:37.898161821 +0000 UTC deployed        nginx-19.0.0    1.27.4   
```

11. Uninstall the nginx chart release happy-browse from the cluster.

```bash
helm uninstall happy-browse
```

12. Remove the Hashicorp helm repository from the cluster.

```bash
helm repo remove hashicorp
```







