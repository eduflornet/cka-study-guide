# Deployments

## Reference                                                        
* https://www.redhat.com/en/topics/containers/what-is-kubernetes-deployment                                      
* https://cloud.google.com/kubernetes-engine/docs/concepts/deployment                             
* https://kubernetes.io/docs/concepts/workloads/controllers/deployment/                           
* https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#updating-a-deployment     
* https://kubernetes.io/docs/concepts/cluster-administration/manage-deployment/                   
* https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#pausing-and-resuming-a-deployment


For **dry-run**: It tests to ensure were there any issues. Will NOT create the Object:

kubectl create deployment NAME --image=[IMAGE-NAME] --replicas=[NUMBER] --dry-run=client

Due to time sensitive in the Exam, It is better to generate to Deployment YAML using above command.
Instead of writing complete Deployment YAML. It saves lot of time.

```bash
kubectl create deployment redis-deploy --image=redis --replicas=3 --dry-run=client
```

1. **Displaying Deployment**

kubectl get deploy <NAME>
kubectl get deploy <NAME> -o wide
kubectl get deploy <NAME> -o yaml

kubectl describe deploy <NAME>

2. **Print Details of Pod Created by this Deployment**


kubectl get pods --show-labels
kubectl get pods -l [LABEL]

EX: 

```bash
kubectl get pods -l app=nginx-app
```


3. **Print Details of ReplicaSet Created by this Deployment**

kubectl get rs --show-labels
kubectl get rs -l [LABEL]

EX: 

```bash
kubectl get rs -l app=nginx-app
```

4. **Scaling Applications**

kubectl scale deploy [DEPLOYMENT-NAME] --replicas=[COUNT]     
Update the replica-count to 5


5. **Edit the Deployment**

kubectl edit deploy [DEPLOYMENT-NAME]


6. **Running operations directly on the YAML file**

SYNTAX: kubectl [OPERATION] –f [FILE-NAME.yaml]

kubectl get –f [FILE-NAME.yaml]
kubectl describe –f [FILE-NAME.yaml]
kubectl edit –f [FILE-NAME.yaml]
kubectl delete –f [FILE-NAME.yaml]
kubectl create –f [FILE-NAME.yaml]


7. **Delete the Deployment**

kubectl delete deploy <NAME>

```bash
kubectl get deploy
kubectl get rs
kubectl get pods
```

