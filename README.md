# CKA Study Guide

## CKA Exam Overview
Welcome to the CKA (Certified Kubernetes Administrator) Study Guide! This project is designed to help you prepare for the CKA exam by providing structured documentation, practices, notes, commands, and exercises related to key topics about [CKA Curriculum CNCF official](https://github.com/cncf/curriculum).  


#### CKA Curriculum 1.33 (effective August 2025)

## 25% - Cluster Architecture, Installation and Conﬁguration

- [Manage role based access control (RBAC)](src/index.md)
- Prepare underlying infrastructure for installing a Kubernetes cluster
- Create and manage Kubernetes clusters using kubeadm
- Manage the lifecycle of Kubernetes clusters
- Implement and conﬁgure a highly-available control plane
- Use Helm and Kustomize to install cluster components
- Understand extension interfaces (CNI, CSI, CRI, etc.)
- Understand CRDs, install and conﬁgure operators

## 15% - Workloads and Scheduling
- Understand application deployments and how to perform rolling update and rollbacks
- Use ConﬁgMaps and Secrets to conﬁgure applications
- Conﬁgure workload autoscaling
- Understand the primitives used to create robust, self-healing, application deployments
- Conﬁgure Pod admission and scheduling (limits, node afﬁnity, etc.)

## 20% - Servicing and Networking
- Understand connectivity between Pods
- Deﬁne and enforce Network Policies
- Use ClusterIP, NodePort, LoadBalancer service types and endpoints
- Use the Gateway API to manage Ingress trafﬁc
- Know how to use Ingress controllers and Ingress resources
- Understand and use CoreDNS

## 10% - Storage
- Implement storage classes and dynamic volume provisioning
- Conﬁgure volume types, access modes and reclaim policies
- Manage persistent volumes and persistent volume claims

## 30% - Troubleshooting
- Troubleshoot clusters and nodes
- Troubleshoot cluster components
- Monitor cluster and application resource usage
- Manage and evaluate container output streams
- Troubleshoot services and networking

## Additional Resources

- Here I leave [exercises](https://github.com/eduflornet/cka-study-guide/blob/main/src/exercises/) that I have been creating throughout this guide, which are practices from some courses.

- [Tips for success](https://github.com/eduflornet/cka-study-guide/blob/main/docs/cka_tips.md): Detailed information about tips for success.

- [A quick note on editing Pods and Deployments](https://github.com/eduflornet/cka-study-guide/blob/main/docs/edit_pods_deployments.md)

- [Additional tools](https://github.com/eduflornet/cka-study-guide/blob/main/docs/additional_tools.md)

For more information and resources, refer to the official Kubernetes documentation and other CKA study materials available online.

Good luck with your studies and on your journey to becoming a Certified Kubernetes Administrator!
