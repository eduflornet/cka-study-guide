# Gateway API

You are exactly where you need to be. You are not behind.

For success attitude is equally important as ability.

– Harry F.Bank

## Resource

[https://gateway-api.sigs.k8s.io/](https://gateway-api.sigs.k8s.io/)


1. Which API resource is used to define a Gateway in Kubernetes?

**Gateway**

The Gateway resource is used to define an instance of a Gateway API implementation in Kubernetes.
It acts as an entry point for external traffic into the cluster.

The GatewayClass resource defines a type of Gateway (i.e., the controller and its capabilities),
but does not create an actual Gateway instance.
To actually deploy a Gateway that routes traffic, you must create a Gateway resource.

2. What is the purpose of the allowedRoutes field in a Gateway?

especificar que namespaces pueden attach al gateway

The allowedRoutes field in a Gateway listener determines which namespaces and types of Routes (like HTTPRoute, TCPRoute, etc.) are allowed to attach to that Gateway.

By default, only Routes in the same namespace as the Gateway can attach. Setting namespaces.from: All allows Routes from all namespaces to attach. You can also restrict which kinds of Routes can attach using the kinds field. This enables fine-grained access control and safe multi-tenant routing.

Example:

```yaml

spec:
  listeners:
    - name: http
      port: 80
      protocol: HTTP
      allowedRoutes:
        namespaces:
          from: All  # Allows Routes from all namespaces to attach
        kinds:
          - group: gateway.networking.k8s.io
            kind: HTTPRoute
```

3. Which of the following protocols is NOT supported by the Kubernetes Gateway API?

**ICMP**

4. How does a GatewayClass differ from a Gateway?

?

5. What is the primary advantage of using Gateway API over Ingress?

The Gateway API provides more advanced routing capabilities than Ingress, including:

Multi-protocol support (HTTP, TCP, UDP, etc.)
Better extensibility with Routes and Filters
More granular access control using AllowedRoutes
Unlike Ingress, which is primarily HTTP-based, Gateway API is designed for flexibility across multiple protocols.

6. To use the Gateway API, a controller is required. In this lab, we will install NGINX Gateway Fabric as the controller. Follow these steps to complete the installation:

- Install the Gateway API resources

```bash
kubectl kustomize "https://github.com/nginx/nginx-gateway-fabric/config/crd/gateway-api/standard?ref=v1.5.1" | kubectl apply -f -
```

- Deploy the NGINX Gateway Fabric CRDs

```bash
kubectl apply -f https://raw.githubusercontent.com/nginx/nginx-gateway-fabric/v1.6.1/deploy/crds.yaml
```

Deploy NGINX Gateway Fabric

```bash
kubectl apply -f https://raw.githubusercontent.com/nginx/nginx-gateway-fabric/v1.6.1/deploy/nodeport/deploy.yaml
```

- Verify the Deployment

```bash
kubectl get pods -n nginx-gateway
```

- View the nginx-gateway service

```bash
kubectl get svc -n nginx-gateway nginx-gateway -o yaml
```

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"v1","kind":"Service","metadata":{"annotations":{},"labels":{"app.kubernetes.io/instance":"nginx-gateway","app.kubernetes.io/name":"nginx-gateway","app.kubernetes.io/version":"1.6.1"},"name":"nginx-gateway","namespace":"nginx-gateway"},"spec":{"externalTrafficPolicy":"Local","ports":[{"name":"http","port":80,"protocol":"TCP","targetPort":80},{"name":"https","port":443,"protocol":"TCP","targetPort":443}],"selector":{"app.kubernetes.io/instance":"nginx-gateway","app.kubernetes.io/name":"nginx-gateway"},"type":"NodePort"}}
  creationTimestamp: "2025-09-05T10:51:43Z"
  labels:
    app.kubernetes.io/instance: nginx-gateway
    app.kubernetes.io/name: nginx-gateway
    app.kubernetes.io/version: 1.6.1
  name: nginx-gateway
  namespace: nginx-gateway
  resourceVersion: "2639"
  uid: ce066d23-e536-421b-9e16-ebb6b2400e7d
spec:
  clusterIP: 172.20.26.153
  clusterIPs:
  - 172.20.26.153
  externalTrafficPolicy: Local
  internalTrafficPolicy: Cluster
  ipFamilies:
  - IPv4
  ipFamilyPolicy: SingleStack
  ports:
  - name: http
    nodePort: 31720
    port: 80
    protocol: TCP
    targetPort: 80
  - name: https
    nodePort: 31444
    port: 443
    protocol: TCP
    targetPort: 443
  selector:
    app.kubernetes.io/instance: nginx-gateway
    app.kubernetes.io/name: nginx-gateway
  sessionAffinity: None
  type: NodePort
status:
  loadBalancer: {}
```

- Update the nginx-gateway service to expose ports 30080 for HTTP and 30081 for HTTPS

```bash
kubectl patch svc nginx-gateway -n nginx-gateway --type='json' -p='[
  {"op": "replace", "path": "/spec/ports/0/nodePort", "value": 30080},
  {"op": "replace", "path": "/spec/ports/1/nodePort", "value": 30081}
]'
```
- Is the Gateway API resource installed?
- Is the NGINX Gateway Fabric CRDs deployed?
- Is the NGINX Gateway Fabric pod up and running?
- Is the nginx-gateway service updated?



7. Create a Kubernetes Gateway resource with the following specifications:

- Name: nginx-gateway
- Namespace: nginx-gateway
- Gateway Class Name: nginx
- Listeners:
    - Protocol: HTTP
    - Port: 80
    - Name: http
- Allowed Routes: All namespaces

Is the nginx-gateway deployed?

Prerequisite: Make sure the Gateway API CRDs are installed and a compatible controller (like NGINX Gateway Fabric) is running.

- Create the Gateway manifest (gateway.yaml):

```yaml
# gateway.yaml
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  name: nginx-gateway
  namespace: nginx-gateway
spec:
  gatewayClassName: nginx
  listeners:
    - name: http
      port: 80
      protocol: HTTP
      allowedRoutes: 
        namespaces: 
          from: All
```

- Deploy the manifest:

```bash
kubectl apply -f gateway.yaml
```

- To verify the succesfull deployment, run the commands below:

```bash
kubectl get gateways -n nginx-gateway
kubectl describe gateway nginx-gateway -n nginx-gateway
```

8. A new pod named frontend-app and a service called frontend-svc have been deployed in the default namespace.
Expose the service on the / path by creating an HTTPRoute named frontend-route.

- Confirm the pod and service exist:

```bash
kubectl get pod,svc -n default
```

- Create the HTTPRoute manifest:

- Create a file named frontend-route.yaml with the following content:

```yaml
# frontend-route.yaml
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: frontend-route
  namespace: default
spec:
  parentRefs:
    - name: nginx-gateway           # Name of the Gateway
      namespace: nginx-gateway      # Namespace where the Gateway is deployed
      sectionName: http             # Attach to the 'http' listener
  rules:
    - matches:
        - path:
            type: PathPrefix
            value: /
      backendRefs:
        - name: frontend-svc
          port: 80
```

parentRefs attaches the route to the nginx-gateway Gateway in the nginx-gateway namespace, specifically to the http listener.
The rule matches all requests with a path prefix of / and forwards them to the frontend-svc service on port 80.

- Apply the manifest:

```bash
kubectl apply -f frontend-route.yaml
```

- Verify the HTTPRoute:

```bash
kubectl get httproute frontend-route

Name:         frontend-route
Namespace:    default
Labels:       <none>
Annotations:  <none>
API Version:  gateway.networking.k8s.io/v1
Kind:         HTTPRoute
Metadata:
  Creation Timestamp:  2025-09-05T11:06:58Z
  Generation:          1
  Resource Version:    4352
  UID:                 abcd9e12-1246-4517-bed6-5cd330323e77
Spec:
  Parent Refs:
    Group:         gateway.networking.k8s.io
    Kind:          Gateway
    Name:          nginx-gateway
    Namespace:     nginx-gateway
    Section Name:  http
  Rules:
    Backend Refs:
      Group:   
      Kind:    Service
      Name:    frontend-svc
      Port:    80
      Weight:  1
    Matches:
      Path:
        Type:   PathPrefix
        Value:  /
Status:
  Parents:
    Conditions:
      Last Transition Time:  2025-09-05T11:06:58Z
      Message:               The route is accepted
      Observed Generation:   1
      Reason:                Accepted
      Status:                True
      Type:                  Accepted
      Last Transition Time:  2025-09-05T11:06:58Z
      Message:               All references are resolved
      Observed Generation:   1
      Reason:                ResolvedRefs
      Status:                True
      Type:                  ResolvedRefs
    Controller Name:         gateway.nginx.org/nginx-gateway-controller
    Parent Ref:
      Group:         gateway.networking.k8s.io
      Kind:          Gateway
      Name:          nginx-gateway
      Namespace:     nginx-gateway
      Section Name:  http
Events:              <none>
```

```bash
kubectl describe httproute frontend-route 
```

```yaml
Name:         frontend-route
Namespace:    default
Labels:       <none>
Annotations:  <none>
API Version:  gateway.networking.k8s.io/v1
Kind:         HTTPRoute
Metadata:
  Creation Timestamp:  2025-09-05T11:06:58Z
  Generation:          1
  Resource Version:    4352
  UID:                 abcd9e12-1246-4517-bed6-5cd330323e77
Spec:
  Parent Refs:
    Group:         gateway.networking.k8s.io
    Kind:          Gateway
    Name:          nginx-gateway
    Namespace:     nginx-gateway
    Section Name:  http
  Rules:
    Backend Refs:
      Group:   
      Kind:    Service
      Name:    frontend-svc
      Port:    80
      Weight:  1
    Matches:
      Path:
        Type:   PathPrefix
        Value:  /
Status:
  Parents:
    Conditions:
      Last Transition Time:  2025-09-05T11:06:58Z
      Message:               The route is accepted
      Observed Generation:   1
      Reason:                Accepted
      Status:                True
      Type:                  Accepted
      Last Transition Time:  2025-09-05T11:06:58Z
      Message:               All references are resolved
      Observed Generation:   1
      Reason:                ResolvedRefs
      Status:                True
      Type:                  ResolvedRefs
    Controller Name:         gateway.nginx.org/nginx-gateway-controller
    Parent Ref:
      Group:         gateway.networking.k8s.io
      Kind:          Gateway
      Name:          nginx-gateway
      Namespace:     nginx-gateway
      Section Name:  http
Events:              <none>
```

https://30080-port-wllbrz5elrprwav6.labs.kodekloud.com/

