# JSON PATH Kubernetes

You can’t go back and change the beginning, but you can start where you are and change the ending.

– C.S. Lewis

In this lab, we provide you with a Kubernetes pod information file in JSON format. Try to build JSON path queries with CLI to extract information from JSON.

#### Reference

https://gist.github.com/noseka1/231105ca1e39b304c7e737323378825a


1. In the given data, what is the data type of the root element?

**dictionary**

```json
{
  "apiVersion": "v1",
  "kind": "Pod",
  "metadata": {
    "name": "nginx-pod",
    "namespace": "default"
  },
  "spec": {
    "containers": [
      {
        "image": "nginx:alpine",
        "name": "nginx"
      }
    ],
    "nodeName": "node01"
  }
}
```

**Hint**
The root element is the top most object in a JSON document. Look at the encapsulation. Is it square brackets [] or curly brackets {} ? If it's square brackets its an array/list. If its curly brackets it is a **dictionary**.

2. How many properties/fields does the root object(dictionary) have?

**four**

```json
"apiVersion": "v1",
  "kind": "Pod",
  "metadata": {
"spec": {
```

```json
{
  "apiVersion": "v1",
  "kind": "Pod",
  "metadata": {
    "name": "nginx-pod",
    "namespace": "default"
  },
  "spec": {
    "containers": [
      {
        "image": "nginx:alpine",
        "name": "nginx"
      }
    ],
    "nodeName": "node01"
  }
}
```

3. In the given data, what is the data type of the root element?

The data type of the root element is **List**

```json
[
  {
    "apiVersion": "v1",
    "kind": "Pod",
    "metadata": {
      "name": "web-pod-1",
      "namespace": "default"
    },
    "spec": {
      "containers": [
        {
          "image": "nginx:alpine",
          "name": "nginx"
        }
      ],
      "nodeName": "node01"
    }
  },
  {
    "apiVersion": "v1",
    "kind": "Pod",
    "metadata": {
      "name": "web-pod-2",
      "namespace": "default"
    },
    "spec": {
      "containers": [
        {
          "image": "nginx:alpine",
          "name": "nginx"
        }
      ],
      "nodeName": "node02"
    }
  }
]
```

4. How many items do the list have?

Within the array/list each item is a dictionary as its encapsulated in curly brackets. So it's a List of Dictionaries. So count each item of that list. 

**Answer is 2**

```json
[
  {
    "apiVersion": "v1",
    "kind": "Pod",
    "metadata": {
      "name": "web-pod-1",
      "namespace": "default"
    },
    "spec": {
      "containers": [
        {
          "image": "nginx:alpine",
          "name": "nginx"
        }
      ],
      "nodeName": "node01"
    }
  },
  {
    "apiVersion": "v1",
    "kind": "Pod",
    "metadata": {
      "name": "web-pod-2",
      "namespace": "default"
    },
    "spec": {
      "containers": [
        {
          "image": "nginx:alpine",
          "name": "nginx"
        }
      ],
      "nodeName": "node02"
    }
  }
]
```

5. What is the data type of the apiVersion field?

The apiVersion has a value v1. So its data type should be **string**

```json
{
  "apiVersion": "v1",
  "kind": "Pod",
  "metadata": {
    "name": "nginx-pod",
    "namespace": "default"
  },
  "spec": {
    "containers": [
      {
        "image": "nginx:alpine",
        "name": "nginx"
      }
    ],
    "nodeName": "node01"
  }
}
```

6. What is the data type of the metadata field?

The metadata field has a value that's encapsulated in curly braces. So it should be a **dictionary**.

```json
{
  "apiVersion": "v1",
  "kind": "Pod",
  "metadata": {
    "name": "nginx-pod",
    "namespace": "default"
  },
  "spec": {
    "containers": [
      {
        "image": "nginx:alpine",
        "name": "nginx"
      }
    ],
    "nodeName": "node01"
  }
}
```

7. Which of the below is the best description of the type of data in the containers field?

The containers field is an array as its values are encapsulated in square brackets. But within the array each item is a dictionary as its encapsulated in curly brackets. So its a **List of Dictionaries**

```json
{
  "apiVersion": "v1",
  "kind": "Pod",
  "metadata": {
    "name": "nginx-pod",
    "namespace": "default"
  },
  "spec": {
    "containers": [
      {
        "image": "nginx:alpine",
        "name": "nginx"
      }
    ],
    "nodeName": "node01"
  }
}
```

8. Now let's get into some action with JSON PATH.

Develop a JSON PATH query to extract the kind of object. A file named input.json is provided in the terminal. Provide the file as input to the cat command
for example, the command should be like this cat filename | jpath $.query

Expected output should be like this

```json
[
  "Pod"
]
```

save query command to filename answer9.sh under root directory.

```bash

```

**answer9.sh**
```sh
cat input.json | jpath '$.kind'
[
  "Pod"
]
```

10. Develop a JSON PATH query to get the name of the POD.A file named input.json is provided in the terminal. Provide the file as input to the cat command
for example, the command should be like this cat filename | jpath $.query

Expected output should be like this

[
  "nginx-pod"
]


save query command to filename answer10.sh under root directory.

```bash
 echo "cat input.json | jpath '$metadata.name'" > answer10.sh
 cat input.json | jpath '$.metadata.name' >> answer10.sh 
```

**answer10.sh**

```sh
cat input.json | jpath $.metadata.name
[
  "nginx-pod"
]
```

11. Develop a JSON PATH query to get the name of the POD. A file named input.json is provided in the terminal. Provide the file as input to the cat command
for example, the command should be like this cat filename | jpath $.query

Expected output should be like this

[
  "node01"
]


Save query command to filename answer11.sh under root directo

cat filename | jpath $.query

Filename - input.json, It is an input for jpath query.
It is the query to get the required output $.spec.nodeName


So the final command would be like this to get output try it yourself.
cat input.json | jpath $.spec.nodeName
save the command used for the query to filename answer11.sh under root directory.

**answer11.sh**

```sh
cat input.json | jpath $.spec.nodeName
```

12. Develop a JSON PATH query to get the name of the POD. A file named input.json is provided in the terminal. Provide the file as input to the cat command
for example, the command should be like this cat filename | jpath $.query

```json
# input.json
{
  "apiVersion": "v1",
  "kind": "Pod",
  "metadata": {
    "name": "nginx-pod",
    "namespace": "default"
  },
  "spec": {
    "containers": [
      {
        "image": "nginx:alpine",
        "name": "nginx"
      }
    ],
    "nodeName": "node01"
  }
}
```

Expected output should be like this

[
  {
    "image": "nginx:alpine",
    "name": "nginx"
  }
]


Save query command to filename answer12.sh under root directory.

**answer12.sh**

```sh
cat input.json | jpath $.spec.containers[0]
```

13. Develop a JSON PATH query to get the name of the POD. A file named input.json is provided in the terminal. Provide the file as input to the cat command
for example, the command should be like this cat filename | jpath $.query

Expected output should be like this

[
  {
    "image": "nginx:alpine",
    "name": "nginx"
  }
]


Save query command to filename answer12.sh under root directory.

```sh
cat input.json | jpath $.spec.containers[0]
```

13. Develop a JSON PATH query to get the image name under the containers section.

A file named input.json is provided in the terminal. Provide the file as input to the cat command
for example, the command should be like this cat filename | jpath $.query

Expected output should be like this

[
  "nginx:alpine"
]

```sh
cat input.json | jpath $.spec.containers[0].image
```

14. We now have POD status too. Develop a JSON PATH query to get the phase of the pod under the status section.


A file named k8status.json is provided in the terminal. Provide the file as input to the cat command
for example, the command should be like this cat filename | jpath $.query

```json
{
  "apiVersion": "v1",
  "kind": "Pod",
  "metadata": {
    "name": "nginx-pod",
    "namespace": "default"
  },
  "spec": {
    "containers": [
      {
        "image": "nginx:alpine",
        "name": "nginx"
      },
      {
        "image": "redis:alpine",
        "name": "redis-container"
      }
    ],
    "nodeName": "node01"
  },
  "status": {
    "conditions": [
      {
        "lastProbeTime": null,
        "lastTransitionTime": "2019-06-13T05:34:09Z",
        "status": "True",
        "type": "Initialized"
      },
      {
        "lastProbeTime": null,
        "lastTransitionTime": "2019-06-13T05:34:09Z",
        "status": "True",
        "type": "PodScheduled"
      }
    ],
    "containerStatuses": [
      {
        "image": "nginx:alpine",
        "name": "nginx",
        "ready": false,
        "restartCount": 4,
        "state": {
          "waiting": {
            "reason": "ContainerCreating"
          }
        }
      },
      {
        "image": "redis:alpine",
        "name": "redis-container",
        "ready": false,
        "restartCount": 2,
        "state": {
          "waiting": {
            "reason": "ContainerCreating"
          }
        }
      }
    ],
    "hostIP": "172.17.0.75",
    "phase": "Pending",
    "qosClass": "BestEffort",
    "startTime": "2019-06-13T05:34:09Z"
  }
}
```

Expected output should be like this

```json
[
  "Pending"
]
```

**answer14.sh**

```sh
cat input.json | jpath $.spec.containers[0].image
```

Save query command to filename answer14.sh under root directory.

15. Develop a JSON PATH query to get the reason for the state of the container under the status section.

A file named k8status.json is provided in the terminal. Provide the file as input to the cat command
for example, the command should be like this cat filename | jpath $.query

Expected output should be like this

[
  "ContainerCreating"
]

**answer15.sh**

```sh
cat k8status.json | jpath $.status.containerStatuses[0].state.waiting.reason
```
16. Develop a JSON PATH query to get the restart count of the redis-container under the status section.

A file named k8status.json is provided in the terminal. Provide the file as input to the cat command
for example, the command should be like this cat filename | jpath $.query

Expected output should be like this

[
  2
]


Save query command to filename answer16.sh under root directory.


**answer16.sh**

```sh
cat k8status.json | jpath $.status.containerStatuses[1].restartCount
```

17. Develop a JSON PATH query to get all pod names.

```json
[
  {
    "apiVersion": "v1",
    "kind": "Pod",
    "metadata": {
      "name": "web-pod-1",
      "namespace": "default"
    },
    "spec": {
      "containers": [
        {
          "image": "nginx:alpine",
          "name": "nginx"
        }
      ],
      "nodeName": "node01"
    }
  },
  {
    "apiVersion": "v1",
    "kind": "Pod",
    "metadata": {
      "name": "web-pod-2",
      "namespace": "default"
    },
    "spec": {
      "containers": [
        {
          "image": "nginx:alpine",
          "name": "nginx"
        }
      ],
      "nodeName": "node02"
    }
  },
  {
    "apiVersion": "v1",
    "kind": "Pod",
    "metadata": {
      "name": "web-pod-3",
      "namespace": "default"
    },
    "spec": {
      "containers": [
        {
          "image": "nginx:alpine",
          "name": "nginx"
        }
      ],
      "nodeName": "node01"
    }
  },
  {
    "apiVersion": "v1",
    "kind": "Pod",
    "metadata": {
      "name": "web-pod-4",
      "namespace": "default"
    },
    "spec": {
      "containers": [
        {
          "image": "nginx:alpine",
          "name": "nginx"
        }
      ],
      "nodeName": "node01"
    }
  },
  {
    "apiVersion": "v1",
    "kind": "Pod",
    "metadata": {
      "name": "db-pod-1",
      "namespace": "default"
    },
    "spec": {
      "containers": [
        {
          "image": "mysql",
          "name": "mysql"
        }
      ],
      "nodeName": "node01"
    }
  }
]
```

A file podslist.json file is provided in the terminal.
The expected output should be like this.

[
  "web-pod-1",
  "web-pod-2",
  "web-pod-3",
  "web-pod-4",
  "db-pod-1"
]


save query command to filename answer17.sh under root directory.

**answer17.sh**

```sh
cat podslist.json | jpath '$[*].metadata.name'
```









