# Deploying a Static Website on Kubernetes using Nginx

## Overview
The Nautilus DevOps team set up a highly available and scalable deployment for a static website using Kubernetes and Nginx.
This guide explains how we deployed the website with multiple replicas and exposed it externally through a NodePort service.

## Objective
We needed to:
 - Deploy an Nginx-based static website using Kubernetes Deployment.
 - Ensure high availability by running multiple replicas (3 pods).
 - Expose the application externally using a NodePort Service on port 30011.

## Kubernetes Resources Created
1. Deployment: nginx-deployment
This resource manages the desired number of Nginx pods (replicas) and ensures that the website stays up even if some pods fail.
Deployment Manifest:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx-container
          image: nginx:latest
          ports:
            - containerPort: 80
```

Explanation:
 - replicas: 3 → Runs three identical Nginx pods for high availability.
 - selector and labels → Used to link the deployment with the service.
 - containerPort: 80 → Nginx listens on port 80 inside the container.
 - nginx:latest → Uses the most recent Nginx image.

2. Service: nginx-service

This service exposes the Nginx application outside the Kubernetes cluster, allowing external users to access the website through a fixed node port.
Service Manifest:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30011

```
Explanation:
 - type: NodePort → Makes the service accessible externally using a node IP and port.
 - port → The internal cluster port (80).
 - targetPort → The port on the Nginx container.
 - nodePort: 30011 → The external port that users can access from outside the cluster.
 - selector: app: nginx → Connects the service to pods managed by the deployment.

## Deployment Steps
1. Apply the Deployment
```bash
kubectl apply -f nginx-deployment.yaml
```
2. Apply the Service
```bash
kubectl apply -f nginx-service.yaml
```
3. Verify the Resources
```bash
kubectl get deployments
kubectl get pods
kubectl get svc
```
4. Access the Website
Open your browser and visit:
```cpp
http://<node-ip>:30011
```
Replace <node-ip> with the IP address of any worker node in your cluster.

<img width="1051" height="418" alt="Screenshot 2025-10-14 at 11 47 26 AM" src="https://github.com/user-attachments/assets/d76cd323-8726-4a5e-a949-26ac5a64ff72" />

<img width="1376" height="416" alt="Screenshot 2025-10-14 at 11 47 17 AM" src="https://github.com/user-attachments/assets/b14e2a5a-a09d-4d2d-993e-1eae0f61ffda" />

## Key Takeaways
 - Deployments ensure reliability and scalability for your applications by managing replicas.
 - Services enable stable networking between pods and provide external access when needed.
 - Using NodePort, we can easily test or expose apps without requiring an ingress controller or load balancer.

## Summary
By following this setup, the Nautilus DevOps team successfully:
 - Deployed an Nginx-based static website on Kubernetes.
 - Ensured high availability using 3 replicas.
 - Made the website accessible externally on port 30011 via NodePort service.
