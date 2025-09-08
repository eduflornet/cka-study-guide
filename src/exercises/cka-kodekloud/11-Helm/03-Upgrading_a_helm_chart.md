# Upgrading a helm chart

In learning you will teach, and in teaching you will learn.

– Phil Collins

The way to get started is to quit talking and begin doing.

– Walt Disney


1. Add bitnami helm chart repository to the cluster.

Bitnami chart repo: https://charts.bitnami.com/bitnami

Note: - Make sure to add the bitnami chart to the cluster before moving to the next questions. 

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
```

2. How many releases of nginx can you see in the cluster now?

**one**

```bash
helm list
NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
dazzling-web    default         3               2025-09-08 10:48:15.590039783 +0000 UTC deployed        nginx-12.0.4    1.22.0 
```

3. How many revisions of nginx exists in the cluster?

**three**

```bash
helm history dazzling-web
REVISION        UPDATED                         STATUS          CHART           APP VERSION     DESCRIPTION     
1               Mon Sep  8 10:48:13 2025        superseded      nginx-12.0.4    1.22.0          Install complete
2               Mon Sep  8 10:48:13 2025        superseded      nginx-12.0.5    1.22.0          Upgrade complete
3               Mon Sep  8 10:48:15 2025        deployed        nginx-12.0.4    1.22.0          Upgrade complete
```

4. Which version of nginx is currently running in the cluster?

**1.22.0**

```bash
helm list
NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
dazzling-web    default         3               2025-09-08 10:48:15.590039783 +0000 UTC deployed        nginx-12.0.4    1.22.0     
```

5. The DevOps team has decided to upgrade the nginx version to 1.27.x and use the Helm chart version 18.3.6 from the Bitnami repository.

Ensure that the nginx version running in the cluster is 1.27.x.

```bash
helm upgrade dazzling-web bitnami/nginx --version 18.3.6

Pulled: us-central1-docker.pkg.dev/kk-lab-prod/helm-charts/bitnami/nginx:18.3.6
Digest: sha256:19a3e4578765369a8c361efd98fe167cc4e4d7f8b4ee42da899ae86e5f2be263
Release "dazzling-web" has been upgraded. Happy Helming!
NAME: dazzling-web
LAST DEPLOYED: Mon Sep  8 10:57:31 2025
NAMESPACE: default
STATUS: deployed
REVISION: 4
TEST SUITE: None
NOTES:
CHART NAME: nginx
CHART VERSION: 18.3.6
APP VERSION: 1.27.4

```

```bash
helm list
NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
dazzling-web    default         4               2025-09-08 10:57:31.277199565 +0000 UTC deployed        nginx-18.3.6    1.27.4  
```

6. To which version is the nginx currently upgraded?

**1.27.4**

7. Oops!.. There seems to be a minor issue in the website and the DevOps Team is asked to rollback the nginx to previous version!

Please rollback the nginx to previous version.

```bash
helm rollback dazzling-web
Rollback was a success! Happy Helming!
```



