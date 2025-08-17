# Rolling Update and Rolling Back

In this Demo:
-------------
We will create deploy sample application. Next we will update deployment by setting new Image version.
What if we incorrectly put Image version? we will rollout. You will also learn about rolling back.


1. Creating Deployment "Imperatively" (from command line):

kubectl create deployment NAME --image=[IMAGE-NAME] --replicas=[NUMBER]


EX: 

```bash
kubectl create deployment nginx-deploy --image=nginx:1.18 --replicas=4
```

2. Upgrading Deployment with new Image:

kubectl set image deploy [DEPLOYMENT-NAME] [CONTAINER-NAME]=[CONTAINER-IMAGE]:[TAG]

EX:

```bash
kubectl set image deploy nginx-deploy nginx=nginx:1.91
```


Aplicar anotacion manual:

```bash
kubectl annotate deployment nginx-deploy kubernetes.io/change-cause="Actualización a nginx:1.91"
```

3. Checking Rollout Status:

kubectl rollout status deploy [DEPLOYMENT-NAME]

EX: 

```bash
kubectl rollout status deploy nginx-deploy
Waiting for deployment "nginx-deploy" rollout to finish: 2 out of 4 new replicas have been updated...
```

NOTE: There is some issue. To dig deep, let's check rollout history.


4. Checking Rollout History:

kubectl rollout history deploy [DEPLOYMENT-NAME]


EX:

```bash
kubectl rollout history deploy nginx-deploy
deployment.apps/nginx-deploy 
REVISION  CHANGE-CAUSE
1         <none>
2         Actualizacion a nginx:1.91
```

NOTE: From the output, you can see the commands that are run previously. 
If you notice, Image tag we used is 1.91 instead of 1.19. Let's rollback!

You can confirm the same from by running

```bash
kubectl get deploy nginx-deploy -o wide
NAME           READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES       SELECTOR
nginx-deploy   3/4     2            3           15m   nginx        nginx:1.91   app=nginx-deploy
```

5. Doing previous rollout "undo":

kubectl rollout undo deployment/[DEPLOYMENT-NAME]

```bash
kubectl rollout undo deployment/nginx-deploy
deployment.apps/nginx-deploy rolled back
```

(OR)

kubectl rollout undo deployment [DEPLOYMENT-NAME] --to-revision=[DESIRED-REVISION-NUMBER]

```bash
kubectl rollout undo deployment nginx-deploy --to-revision=0
deployment.apps/nginx-deploy rolled back
```

kubectl rollout status deployment/[DEPLOYMENT-NAME]

```bash
kubectl rollout status deployment/nginx-deploy
deployment "nginx-deploy" successfully rolled out
```

kubectl get deploy [DEPLOYMENT-NAME] -o wide


```bash
kubectl get deploy nginx-deploy -o wide
NAME           READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES       SELECTOR
nginx-deploy   4/4     4            4           30m   nginx        nginx:1.18   app=nginx-deploy
```

```bash
kubectl get deploy nginx-deploy -o json | grep -i image
                        "image": "nginx:1.18",
                        "imagePullPolicy": "IfNotPresent",
```


## Reference

* https://cloud.google.com/kubernetes-engine/docs/how-to/updating-apps
* https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#rolling-update-deployment
* https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#checking-rollout-history-of-a-deployment
* https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#rolling-back-to-a-previous-revision
* https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#rolling-back-a-deployment
