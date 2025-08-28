# Custom Resources Definition

People who are really serious about software should make their own hardware.

– Alan Kay

Excellence happens not by accident. It is a process.

– Dr. APJ. Abdul Kalam


1. CRD Object can be either namespaced or cluster scoped.

Is this statement true or false?

**true**

A CRD can be either namespaced or cluster-scoped. When you create a CRD, you can specify its scope in the spec.scope field of the CRD manifest.

2. What is a custom resource?

It is an extension of the Kubernetes API that is not necessarily available in a default Kubernetes installation.

3. We have provided an incomplete Custom Resource Definition (CRD) manifest located at /root/crd.yaml.

Your task is to complete this file to define a namespaced CRD named internals.datasets.kodekloud.com.

Please ensure you adhere to the following specifications:

The CRD must belong to the group **datasets.kodekloud.com**.
The scope of the CRD should be set to **Namespaced**.
The version must be v1, and it should be marked as both served: true and storage: true.
Additionally, include a basic OpenAPI v3 schema for the CRD under the spec section with the following fields:

- internalLoad (string)
- range (integer)
- percentage (string)

Once you have created the CRD, utilize the provided ``` /root/custom.yaml ``` file to create a corresponding custom resource.


- CRD created?

- Corrected the version?

- Enable the version?

- Deployed custom resource?

The solution file for crd.yaml is pasted below:

```yaml
---
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata:
  name: internals.datasets.kodekloud.com 
spec:
  group: datasets.kodekloud.com
  versions:
    - name: v1
      served: true
      storage: true
      schema:
        openAPIV3Schema:
          type: object
          properties:
            spec:
              type: object
              properties:
                internalLoad:
                  type: string
                range:
                  type: integer
                percentage:
                  type: string
  scope: Namespaced 
  names:
    plural: internals
    singular: internal
    kind: Internal
    shortNames:
    - int
```

Also make sure to create custom resource using kubectl create -f custom.yaml after correcting and creating CRD.

```yaml
# custom.yaml
kind: Internal
apiVersion: datasets.kodekloud.com/v1
metadata:
  name: internal-space
  namespace: default
spec:
  internalLoad: "high"
  range: 80
  percentage: "50"
```

```bash
k get crd -o wide
NAME                               CREATED AT
collectors.monitoring.controller   2025-08-28T08:02:40Z
globals.traffic.controller         2025-08-28T08:02:40Z
internals.datasets.kodekloud.com   2025-08-28T08:12:03Z
```

4. What are the properties given to the CRD’s called collectors.monitoring.controller?

**image, replicas, name**

```bash
kubectl describe crd collectors.monitoring.controller
```

```yaml
# collectors.monitoring.controller
Versions:
    Name:  v1
    Schema:
      openAPIV3Schema:
        Properties:
          Spec:
            Properties:
              Image:
                Type:  string
              Name:
                Type:  string
              Replicas:
                Type:  integer
            Type:      object
        Type:          object
    Served:            true
    Storage:           true
```

5. Please create a custom resource instance named datacenter utilizing the existing Custom Resource Definition (CRD) with the following specifications:

- apiVersion: traffic.controller/v1
- kind: Global

Set the following fields under spec:

- dataField: 2
- access: true

To create a custom resource called datacenter:

```yaml
kind: Global
apiVersion: traffic.controller/v1
metadata:
  name: datacenter
spec:
  dataField: 2
  access: true
```

```bash
kubectl get crd
NAME                               CREATED AT
collectors.monitoring.controller   2025-08-28T08:15:56Z
globals.traffic.controller         2025-08-28T08:15:56Z
internals.datasets.kodekloud.com   2025-08-28T08:19:28Z
```

6. What is the short name given to the CRD globals.traffic.controller ?

**gb**

Inspect the Custom Resource Definition (CRD) globals.traffic.controller and identify the short names by executing the following command:

```bash
kubectl describe crd globals.traffic.controller
```

```yaml
 Accepted Names:
    Kind:       Global
    List Kind:  GlobalList
    Plural:     globals
    Short Names:
      gb
    Singular:  global
```

