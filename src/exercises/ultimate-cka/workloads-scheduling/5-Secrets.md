# Secrets

## Reference

* https://kubernetes.io/docs/tasks/configmap-secret/
* https://kubernetes.io/docs/concepts/configuration/secret/
* https://kubernetes.io/docs/tasks/inject-data-application/distribute-credentials-secure/
* https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-secret-em-
* https://cloud.google.com/kubernetes-engine/docs/concepts/secret
* https://blog.newrelic.com/engineering/how-to-use-kubernetes-secrets/

In this Demo:

We will create the sample Secret using both Declaratively and Imperatively.
Then we will consume these Secrets inside Pod as Environment Variables and Files inside the volume.

1. Creating Secrets Declaratively (Using YAML):

a. Base-64 Encoding:

```bash
echo -n 'admin' | base64
echo -n '1f2d1e2e67df' | base64
```

b. Using Base64 Encoding in creating Secret:

```yaml
# secret-db-user-pass.yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-user-pass
  namespace: default
data:
  username: YWRtaW4=
  password: MWYyZDFlMmU2N2Rm
```

Deploy:

```bash
kubectl apply –f secret-db-user-pass.yaml 
```

2. Creating Secrets Imperatively (From Command line):

If you want to skip the Base64 encoding step, you can create the same Secret using the kubectl create secret command. This is more convinient.


Example:

```bash
kubectl create secret generic test-secret --from-literal='username=my-app' --from-literal='password=39528$vdg7Jb'
```

Example:

```bash
echo -n 'admin' > ./username.txt
echo -n '1f2d1e2e67df' > ./password.txt
```

kubectl create secret [TYPE] [NAME] [DATA]

Example

```bash
kubectl create secret generic db-user-pass-from-file --from-file=./username.txt --from-file=./password.txt
```

Example:

```bash
kubectl get secrets db-user-pass –o yaml
```

3. Injecting "Secrets" into Pod As Environmental Variables:

```yaml
# my-secrets-pod-env.yaml

apiVersion: v1
kind: Pod
metadata:
  name: secret-env-pod
spec:
  containers:
  - name: mycontainer
    image: redis
    env:
      - name: SECRET_USERNAME
        valueFrom:
          secretKeyRef:
            name: db-user-pass
            key: username
      - name: SECRET_PASSWORD
        valueFrom:
          secretKeyRef:
            name: db-user-pass
            key: password
  restartPolicy: Never
```

Validate:

```bash
kubectl exec secret-env-pod -- env
kubectl exec secret-env-pod -- env | grep SECRET
```

4. Injecting "Secrets" into Pod As Files inside the Volume:

```yaml
# my-secrets-vol-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: secret-vol-pod
spec:
  containers:
    - name: test-container
      image: nginx
      volumeMounts:
        - name: secret-volume
          mountPath: /etc/secret-volume
  volumes:
    - name: secret-volume
      secret:
        secretName: test-secret
```

Validate:

```bash
kubectl exec secret-vol-pod -- ls /etc/secret-volume
kubectl exec secret-vol-pod -- cat /etc/secret-volume/username
kubectl exec secret-vol-pod -- cat /etc/secret-volume/password
```

5. Displaying Secret:

kubectl get secret <NAME>
kubectl get secret <NAME> -o wide
kubectl get secret <NAME> -o yaml
kubectl get secret <NAME> -o json

kubectl describe secret <NAME>

6. Running operations directly on the YAML file:

kubectl [OPERATION] –f [FILE-NAME.yaml]

kubectl get –f [FILE-NAME.yaml]
kubectl delete –f [FILE-NAME.yaml]
kubectl get -f [FILE-NAME.yaml]
kubectl create -f [FILE-NAME.yaml]

7. Delete Secret: 

kubectl delete secret <NAME>



