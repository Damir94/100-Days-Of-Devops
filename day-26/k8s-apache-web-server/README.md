# Apache HTTPD Deployment on Kubernetes (Nautilus Project)
## Overview
This project deploys the Apache HTTP Server (httpd) application on a Kubernetes cluster for the Nautilus development team.
The setup ensures the application is highly available using multiple replicas and can be accessed externally using a NodePort service.

## Deployment Architecture
Components created:

1. Namespace: httpd-namespace-nautilus
 - Provides logical isolation for application resources.

2. Deployment: httpd-deployment-nautilus
 - Runs 2 replicas of the Apache web server.
 - Uses the latest httpd:latest image.
 - Ensures high availability and automatic pod management.

3. Service: httpd-service-nautilus
 - Exposes the httpd deployment externally.
 - Uses NodePort type with port 30004 for external access.

## Kubernetes Manifest
File: httpd-deployment.yaml
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: httpd-namespace-nautilus
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpd-deployment-nautilus
  namespace: httpd-namespace-nautilus
spec:
  replicas: 2
  selector:
    matchLabels:
      app: httpd-app
  template:
    metadata:
      labels:
        app: httpd-app
    spec:
      containers:
        - name: httpd-container
          image: httpd:latest
          ports:
            - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: httpd-service-nautilus
  namespace: httpd-namespace-nautilus
spec:
  type: NodePort
  selector:
    app: httpd-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 30004
```
## Deployment Steps
1. Apply the configuration:
```bash
kubectl apply -f httpd-deployment.yaml
```
2. Verify resources:
```bash
kubectl get all -n httpd-namespace-nautilus
```
3. Access the application:
 - Open your browser and navigate to:
```bash
http://<NodeIP>:30004
```
 - Replace <NodeIP> with the IP of any Kubernetes node.
   
<img width="1734" height="728" alt="Screenshot 2025-10-19 at 9 49 09 AM" src="https://github.com/user-attachments/assets/73196dec-077a-4f41-8c43-56db5aa9a538" />
   
<img width="579" height="203" alt="Screenshot 2025-10-19 at 9 48 54 AM" src="https://github.com/user-attachments/assets/4b602bb3-c050-4b03-8edb-44577654e8a6" />

## Notes
 - replicas: 2 ensures that even if one pod fails, another remains available.
 - The NodePort service allows external traffic to access the app via port 30004.
 - Using a dedicated namespace keeps resources isolated and easy to manage.
