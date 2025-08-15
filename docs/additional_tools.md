# Helm charts


[Traefik](https://doc.traefik.io/traefik/getting-started/install-traefik/#use-the-helm-chart): Is a modern HTTP reverse proxy and load balancer made to deploy microservices with ease. The Traefik Helm chart is focused on Traefik deployment configuration, visit [Github](https://github.com/traefik/traefik-helm-chart). 

Example of traefik in a cluster:
```shell
kubectl get pods -A
NAMESPACE     NAME                                      READY   STATUS      RESTARTS   AGE
kube-system   coredns-697968c856-869dn                  1/1     Running     0          11m
kube-system   helm-install-traefik-crd-v9c7c            0/1     Completed   0          11m
kube-system   helm-install-traefik-nrqpn                0/1     Completed   2          11m
kube-system   local-path-provisioner-774c6665dc-hwnnr   1/1     Running     0          11m
kube-system   metrics-server-6f4c6675d5-vg2mr           1/1     Running     0          11m
kube-system   svclb-traefik-dd23f229-r29vj              2/2     Running     0          10m
kube-system   traefik-c98fdf6fb-kfx9x 
```