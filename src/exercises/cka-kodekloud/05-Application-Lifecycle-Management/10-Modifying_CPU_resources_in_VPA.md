# Modifying CPU resources in VPA

Learning never exhausts the mind.

– Leonardo da Vinci

## VPA CPU Optimization Lab

In this lab, you will deploy a sample application on Kubernetes, monitor its CPU resource usage, and utilize a Vertical Pod Autoscaler (VPA) to manage and adjust the resource allocation for the pods. The goal is to observe how the VPA automatically recommends and adjusts memory resource limits, particularly when the application experiences increased load.

**Objectives:**
Deploy a sample application and verify that the pods are running correctly.
Monitor pod resource usage using the kubectl top command to assess CPU and memory consumption.
Deploy a Vertical Pod Autoscaler (VPA) to observe how it adjusts CPU and memory resource recommendations based on the application’s current usage.
Introduce load to the application and monitor how VPA dynamically updates its recommendations in response to increased demand.
Interpret VPA recommendations by understanding key parameters like lowerBound, upperBound, and uncappedTarget for resource management.
By the end of this lab, you will have a clear understanding of how VPA optimizes CPU resource usage in a Kubernetes environment, improving application performance and efficiency under varying workloads.

