# Deploying Grafana on Kubernetes
## Overview
The Nautilus DevOps team planned to set up Grafana, an open-source analytics 
and monitoring platform, on a Kubernetes cluster to collect and visualize metrics from various applications.
This document describes the steps followed to deploy Grafana and expose it externally for access via a web browser.

## Objective
 - Deploy a Grafana application on Kubernetes.
 - Expose it through a NodePort service on port 32000.
 - Verify that the Grafana login page is accessible.

## Deployment Details
1. Grafana Deployment
A Kubernetes Deployment named grafana-deployment-xfusion was created using the official Grafana image from Docker Hub.
This ensures that the Grafana pod runs continuously, and if it crashes, Kubernetes automatically recreates it.

Deployment manifest:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: grafana-deployment-xfusion
  labels:
    app: grafana
spec:
  replicas: 1
  selector:
    matchLabels:
      app: grafana
  template:
    metadata:
      labels:
        app: grafana
    spec:
      containers:
        - name: grafana
          image: grafana/grafana:latest
          ports:
            - containerPort: 3000
```
2. NodePort Service
To make Grafana accessible externally, a Service of type NodePort was created.
This service exposes port 3000 (the default Grafana port) and maps it to nodePort 32000 on the Kubernetes node.

Service manifest:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: grafana-service
  labels:
    app: grafana
spec:
  type: NodePort
  selector:
    app: grafana
  ports:
    - port: 3000
      targetPort: 3000
      nodePort: 32000
```
## Deployment Steps
1. Apply Deployment and Service manifests
```bash
kubectl apply -f grafana-deployment.yaml
kubectl apply -f grafana-service.yaml
```

2. Verify the deployment
```bash
kubectl get pods
kubectl get svc
```

3. Access Grafana UI
Open a browser and visit:
```bash
http://<Node-IP>:32000
```
<img width="1609" height="818" alt="Screenshot 2025-10-16 at 9 25 44 PM" src="https://github.com/user-attachments/assets/2f8c5c44-5836-4f03-bee7-615e30a957ed" />

You should see the Grafana login page (default credentials: admin/admin).

## Validation
After applying the manifests:
 - The pod was successfully created and running.
 - The NodePort service exposed Grafana externally.
 - The Grafana login page was accessible in a browser.

## Summary
| Component  | Type     | Name                       | Port(s) |
| ---------- | -------- | -------------------------- | ------- |
| Deployment | apps/v1  | grafana-deployment-xfusion | 3000    |
| Service    | NodePort | grafana-service            | 32000   |
