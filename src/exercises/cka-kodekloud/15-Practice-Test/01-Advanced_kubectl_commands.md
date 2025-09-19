# Advamced Kubectl Commands

Success is not final, failure is not fatal: it is the courage to continue that counts.

– Winston Churchill

An investment in knowledge pays the best interest.

– Benjamin Franklin

You never know what you can do until you try.

– William Cobbett

The most important thing to do in solving a problem is to begin.

– Frank Tyger

#### Reference

[JSON PATH in Kubectl](https://gist.github.com/noseka1/231105ca1e39b304c7e737323378825a)

1. Get the list of nodes in JSON format and store it in a file at /opt/outputs/nodes.json.

```bash
k get nodes -o json > /opt/outputs/nodes.json
```

2. Get the details of the node node01 in json format and store it in the file /opt/outputs/node01.json.

```bash
kubectl get node node01 -o json > /opt/outputs/node01.json
```

3. Use JSON PATH query to fetch node names and store them in /opt/outputs/node_names.txt.

Remember the file should only have node names.

```bash
kubectl get nodes -o=jsonpath='{.items[*].metadata.name}' > /opt/outputs//node_names.txt 
```

4. Use JSON PATH query to retrieve the osImages of all the nodes and store it in a file /opt/outputs/nodes_os.txt.


The osImage is under the nodeInfo section under status of each node.

```bash
kubectl get nodes -o=jsonpath='{.items[*].status.nodeInfo.osImage}' > /opt/outputs/nodes_os.txt
```

5. A **kube-config** file is present at ``` /root/my-kube-config ```. Get the user names from it and store it in a file ``` /opt/outputs/users.txt ```.


Use the command ``` kubectl config view --kubeconfig=/root/my-kube-config ``` to view the custom kube-config.

**answer**

```bash
kubectl config view --kubeconfig=/root/my-kube-config -o=jsonpath='{.users[*].name}' > /opt/outputs/users.txt
```

6. A set of Persistent Volumes are available. Sort them based on their capacity and store the result in the file ``` /opt/outputs/storage-capacity-sorted.txt ```.

```bash
kubectl get pv --sort-by=.spec.capacity.storage > /opt/outputs/storage-capacity-sorted.txt
```

7. That was good, but we don't need all the extra details. Retrieve just the first 2 columns of output and store it in ``` /opt/outputs/pv-and-capacity-sorted.txt ```.


The columns should be named NAME and CAPACITY. Use the custom-columns option and remember, it should still be sorted as in the previous question.

**answer**

```bash
kubectl get pv --sort-by=.spec.capacity.storage -o=custom-columns=NAME:.metadata.name,CAPACITY:.spec.capacity.storage > /opt/outputs/pv-and-capacity-sorted.txt
```

8. Use a JSON PATH query to identify the context configured for the aws-user in the my-kube-config context file and store the result in /opt/outputs/aws-context-name.

**answer**

```bash
kubectl config view --kubeconfig=/root/my-kube-config -o=jsonpath="{.contexts[?(@.context.user=='aws-user')].name}"> /opt/outputs/aws-context-name
```








