# Scaling Application Instance

## Reference

- https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#scaling-a-deployment

- https://kubernetes.io/docs/concepts/cluster-administration/manage-deployment/#scaling-your-application

In this Demo:

We will deploy sample application, then we will increase the application instances using kubectl scale command. Finally, we will validate the same.

1. Creating Deployment "Imperatively" (from command line):

kubectl create deployment NAME --image=[IMAGE-NAME] --replicas=[NUMBER]

Ex:

```bash
kubectl create deployment nginx-deploy --image=nginx --replicas=3
```

2. Scaling Deployment using "kubectl scale" command:

kubectl scale deployment nginx-deploy --replicas=[NEW-REPLICA-COUNT]

```bash
kubectl scale deployment nginx-deploy --replicas 4
deployment.apps/nginx-deploy scaled
```

3. Validate the Replica Count:

```bash
# k get deploy
kubectl get deploy nginx-deploy 
NAME           READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deploy   4/4     4            4           2m7s

# k get rs
kubectl get rs | grep nginx-deploy
nginx-deploy-c9d9f6c6c   4         4         4       4m47s

# k get pods
kubectl get pods | grep nginx-deploy
nginx-deploy-c9d9f6c6c-6bd6m   1/1     Running   0          5m15s
nginx-deploy-c9d9f6c6c-8c6j9   1/1     Running   0          5m15s
nginx-deploy-c9d9f6c6c-mb7sq   1/1     Running   0          4m7s
nginx-deploy-c9d9f6c6c-pxk85   1/1     Running   0          5m15s
```



