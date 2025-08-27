# Service Accounts

Success doesn’t come to you, you go to it.

– Marva Collins


1. How many Service Accounts exist in the default namespace?

```bash
k -n default get sa
NAME      SECRETS   AGE
default   0         6m6s
dev       0         113s
```

2. What is the secret token used by the default service account?

In Kubernetes versions prior to 1.24, each ServiceAccount was automatically associated with a secret containing its token. Starting from Kubernetes 1.24, this automatic secret creation is disabled by default for security reasons.

Run the command kubectl describe serviceaccount default and look at the Tokens field.

```bash
k -n default describe sa default | grep -i token
Tokens:              <none>
```

3. We just deployed the Dashboard application. Inspect the deployment. What is the image used by the deployment?

**gcr.io/kodekloud/customimage/my-kubernetes-dashboard**

```bash
k -n default describe deploy web-dashboard | grep -i image
    Image:      gcr.io/kodekloud/customimage/my-kubernetes-dashboard
```

4. Wait for the deployment to be ready. Access the custom-dashboard by clicking on the link to dashboard portal (web-dashboard).

```bash
pods is forbidden: User "system:serviceaccount:default:default" cannot list resource "pods" in API group "" in the namespace "default"
```

5. What is the state of the dashboard? Have the pod details loaded successfully?

**failled**

6. What type of account does the Dashboard application use to query the Kubernetes API?

**Service Account**

7. Which account does the Dashboard application use to query the Kubernetes API ?

**Default**

8. Inspect the Dashboard Application POD and identify the Service Account mounted on it.

**default**


```bash
k -n default describe pod web-dashboard-5f88cdc488-5wnlg 
Name:             web-dashboard-5f88cdc488-5wnlg
Namespace:        default
Priority:         0
Service Account:  default
Node:             controlplane/192.168.81.10
```

9. At what location is the ServiceAccount credentials available within the pod?

In the kubectl describe pod output, check the Mounts section under the container details.

**/var/run/secrets/kubernetes.io/serviceaccount**


```bash
Environment:
      PYTHONUNBUFFERED:  1
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-wx6gq (ro)
```

10. The application needs a ServiceAccount with the right permissions to authenticate to Kubernetes. The default ServiceAccount has limited access.

Create a new ServiceAccount named dashboard-sa.

k create serviceaccount dashboard-sa

11. We just added additional permissions for the newly created dashboard-sa account using RBAC.

If you are interested in checking out the files used to configure RBAC, look into /var/rbac directory. We will discuss RBAC in a separate section.

12. The dashboard application requires a ServiceAccount token for authentication.

Generate an access token for the dashboard-sa ServiceAccount, copy the generated token, and paste it into the token field of the dashboard UI.
After entering the token, click the "Load Dashboard" button to access the dashboard.

Command to generate the token for dashboard sa :

```bash
kubectl create token dashboard-sa
```

13. You shouldn't have to copy and paste the token each time. The Dashboard application is programmed to read the token automatically from the ServiceAccount credentials mounted inside the pod.

Currently, the default ServiceAccount is mounted. Update the deployment to use the newly created dashboard-sa ServiceAccount instead.

```bash
k get pod  web-dashboard-5f88cdc488-5wnlg -o yaml > web-dashboard-pod.yaml
```

```yaml
# web-dashboard-pod.yaml
restartPolicy: Always
  schedulerName: default-scheduler
  securityContext: {}
  serviceAccount: dashboard-sa
  serviceAccountName: default
```

Use the kubectl edit command for the deployment and specify the serviceAccountName field inside the spec.template.spec.

OR

Make use of the kubectl set command. Run the following command to use the newly created service account: - 

```bash
kubectl set serviceaccount deploy/web-dashboard dashboard-sa
```

14. Refresh the Dashboard application UI and you should now see the PODs listed automatically.

This time you shouldn't have to put in the token manually.



