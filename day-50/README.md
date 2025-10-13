# 100 Days 0f Devops - Day 50:  Kubernetes Pod Resource Limits — HTTPD Pod

## Overview

The Nautilus DevOps Team identified performance issues in several Kubernetes-hosted applications caused by resource contention. 
To prevent any single application from over-consuming cluster resources, the team decided to apply resource requests and limits on containers.
This document describes the creation of a Pod running an httpd (Apache HTTP Server) container with specific CPU and memory constraints.

## Objective
Create a Kubernetes Pod named httpd-pod using the httpd:latest image and 
define resource requests and limits to control CPU and memory utilization.

## Specifications
| Setting      | Type                         | Memory | CPU  |
| ------------ | ---------------------------- | ------ | ---- |
| **Requests** | Minimum guaranteed resources | 15Mi   | 100m |
| **Limits**   | Maximum allowed resources    | 20Mi   | 100m |

## Implementation Steps
1. Create the Pod Definition File
Create a file named httpd-pod.yaml and add the following content:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: httpd-pod
spec:
  containers:
  - name: httpd-container
    image: httpd:latest
    resources:
      requests:
        memory: "15Mi"
        cpu: "100m"
      limits:
        memory: "20Mi"
        cpu: "100m"
```

2. Apply the Configuration
Run the following command to create the pod in your Kubernetes cluster:
```bash
kubectl apply -f httpd-pod.yaml
```

3. Verify the Pod
To check the pod’s status:
```bash
kubectl get pods
```

To view detailed resource configurations:
```bash
kubectl describe pod httpd-pod
```
## Understanding Resource Requests & Limits

### Requests:
Define the minimum amount of CPU and memory guaranteed for a container. The Kubernetes scheduler uses these values to decide on which node to place the pod.

### Limits:
Define the maximum amount of CPU and memory a container can consume. If the container exceeds its memory limit, it will be terminated. If it exceeds its CPU limit, it will be throttled.

## Outcome
By setting resource requests and limits:
 - The httpd-pod runs within predictable CPU and memory boundaries.
 - Cluster stability improves by avoiding resource starvation.
 - Applications can coexist efficiently without impacting each other’s performance.

## Example Output
```bash
$ kubectl get pod httpd-pod
NAME         READY   STATUS    RESTARTS   AGE
httpd-pod    1/1     Running   0          1m
```

## Summary

This exercise demonstrates the importance of defining resource constraints in Kubernetes workloads. Implementing CPU and memory limits ensures:
 - Consistent performance across pods
 - Efficient utilization of cluster resources
 - Protection from resource-hogging applications

## Next Steps
 - Apply similar limits across other application pods.
 - Use LimitRange and ResourceQuota at the namespace level for broader control.
 - Integrate resource monitoring with Prometheus or Lens to visualize resource usage trends.
