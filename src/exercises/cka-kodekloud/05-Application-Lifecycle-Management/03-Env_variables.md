# Env Variables

You are never too old to set another goal or to dream a new dream.

– Malala Yousafzai

Change is the end result of all true learning.

– Leo Buscaglia


01. How many PODs exist on the system? 
in the current(default) namespace

```bash
k get pods -n default
NAME           READY   STATUS    RESTARTS   AGE
webapp-color   1/1     Running   0          43s
```

2. What is the environment variable name set on the container in the pod?

**APP_COLOR**

``` k get pod webapp-color -o yaml ```

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: "2025-08-22T19:58:20Z"
  generation: 1
  labels:
    name: webapp-color
  name: webapp-color
  namespace: default
  resourceVersion: "892"
  uid: 3bbfa863-92ef-4cca-9de7-b3df14a549ab
spec:
  containers:
  - env:
    - name: APP_COLOR
      value: pink
...
```
3. What is the value set on the environment variable APP_COLOR on the container in the pod?

**pink**

4. View the web application UI by clicking on the Webapp Color Tab above your terminal.

This is located on the right side.
https://30080-port-upyqvps2emby4kej.labs.kodekloud.com/

5. Update the environment variable on the POD to display a green background.

Note: Delete and recreate the POD. Only make the necessary changes. Do not modify the name of the Pod.

- Pod Name: webapp-color

- Label Name: webapp-color

- Env: APP_COLOR=green

```yaml
  containers:
  - env:
    - name: APP_COLOR
      value: green
```

6. View the changes to the web application UI by clicking on the Webapp Color Tab above your terminal.

If you already have it open, simply refresh the browser.
https://30080-port-upyqvps2emby4kej.labs.kodekloud.com/

7. How many ConfigMaps exists in the default namespace?

**2**

```bash
k get configMaps
NAME               DATA   AGE
db-config          3      2m37s
kube-root-ca.crt   1      26m
```


```bash
k get configMap db-config -o yaml
```

```yaml
apiVersion: v1
data:
  DB_HOST: SQL01.example.com
  DB_NAME: SQL01
  DB_PORT: "3306"
kind: ConfigMap
metadata:
  creationTimestamp: "2025-08-22T20:13:36Z"
  name: db-config
  namespace: default
  resourceVersion: "1184"
  uid: ea9736ce-68af-48b2-a975-da87897bae69
```

8. Identify the database host from the config map db-config.

**SQL01.example.com**

9. Create a new ConfigMap for the webapp-color POD. Use the spec given below.

- ConfigMap Name: webapp-config-map

- Data: APP_COLOR=darkblue

- Data: APP_OTHER=disregard

Solution

**Run** the command ``` kubectl create configmap  webapp-config-map --from-literal=APP_COLOR=darkblue --from-literal=APP_OTHER=disregard ```

10. Update the environment variable on the POD to use only the APP_COLOR key from the newly created ConfigMap.

Note: Delete and recreate the POD. Only make the necessary changes. Do not modify the name of the Pod.

- Pod Name: webapp-color

- ConfigMap Name: webapp-config-map

**Hint**

Set the environment option of env from value to valueFrom and use the configMapKeyRef to further specify the specs such as name and key for webapp-config-map configmap.

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    name: webapp-color
  name: webapp-color
  namespace: default
spec:
  containers:
  - name: webapp-color
    image: kodekloud/webapp-color
    imagePullPolicy: Always
    env:
    - name: APP_COLOR
      valueFrom:
        configMapKeyRef:
          name: webapp-config-map
          key: APP_COLOR
    resources: {}
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-c7g95
      readOnly: true
  dnsPolicy: ClusterFirst
  restartPolicy: Always
  serviceAccountName: default
  terminationGracePeriodSeconds: 30 
  ...
```

11. View the changes to the web application UI by clicking on the Webapp Color Tab above your terminal.

If you already have it open, simply refresh the browser.

https://30080-port-upyqvps2emby4kej.labs.kodekloud.com/









