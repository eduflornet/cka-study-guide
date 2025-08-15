# A quick note on editing Pods and Deployments

### Edit a Pod
Remember, you CANNOT edit specifications of an existing Pod other than the below.

```yaml
spec.containers[*].image

spec.initContainers[*].image

spec.activeDeadlineSeconds

spec.tolerations
```

For example you cannot edit the environment variables, service accounts, resource limits of a running pod. But if you really want to, you have 2 options:

1. Run the ``` kubectl edit pod <pod name> ``` command.  This will open the pod specification in an editor (vi editor). Then edit the required properties. When you try to save it, you will be denied. This is because you are attempting to edit a field on the pod that is not editable.

A copy of the file with your changes is saved in a temporary location as shown above.

You can then delete the existing pod by running the command:

```shell
kubectl delete pod webapp
```

Then create a new pod with your changes using the temporary file

```shell
kubectl create -f /tmp/kubectl-edit-ccvrq.yaml
```yaml

2. The second option is to extract the pod definition in YAML format to a file using the command
```shell
kubectl get pod webapp -o yaml > my-new-pod.yaml
```

Then make the changes to the exported file using an editor (nano editor). Save the changes
```shell
nano my-new-pod.yaml
```

Then delete the existing pod
```shell
kubectl delete pod webapp
```

Then create a new pod with the edited file
```shell
kubectl create -f my-new-pod.yaml
```

### Edit Deployments

With Deployments you can easily edit any field/property of the POD template. Since the pod template is a child of the deployment specification,  with every change the deployment will automatically delete and create a new pod with the new changes. So if you are asked to edit a property of a POD part of a deployment you may do that simply by running the command

```shell
kubectl edit deployment my-deployment
```