# Iron Gallery Deployment on Kubernetes

## Overview
The Nautilus DevOps Team has deployed a customized version of the Iron Gallery application on a Kubernetes cluster.
This setup includes two main components:
  - Iron Gallery (Frontend Web App)
  - Iron DB (MariaDB Database)
Both components are containerized and deployed as Kubernetes Deployments, each with its own Service for internal and external access.

## Project Structure
We organized the manifests into separate YAML files for clarity and modular management:
```matlab
.
├── namespace.yaml
├── iron-gallery-deployment.yaml
├── iron-gallery-service.yaml
├── iron-db-deployment.yaml
└── iron-db-service.yaml
```
Each file serves a specific purpose, keeping the configuration simple and maintainable.

## Step-by-Step Explanation
#### 1. Namespace Creation — namespace.yaml
We first created a namespace called iron-namespace-nautilus.
Namespaces logically isolate resources in Kubernetes, making it easier to manage and avoid naming conflicts.
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: iron-namespace-nautilus
```
This ensures all Iron Gallery resources are grouped under one environment.

#### 2. Iron Gallery Deployment — iron-gallery-deployment.yaml
This deployment runs the Iron Gallery frontend, which is a web application served by NGINX.
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: iron-gallery-deployment-nautilus
  namespace: iron-namespace-nautilus
spec:
  replicas: 1
  selector:
    matchLabels:
      run: iron-gallery
  template:
    metadata:
      labels:
        run: iron-gallery
    spec:
      containers:
        - name: iron-gallery-container-nautilus
          image: kodekloud/irongallery:2.0
          resources:
            limits:
              memory: "100Mi"
              cpu: "50m"
          volumeMounts:
            - name: config
              mountPath: /usr/share/nginx/html/data
            - name: images
              mountPath: /usr/share/nginx/html/uploads
      volumes:
        - name: config
          emptyDir: {}
        - name: images
          emptyDir: {}
```
Key Details:
  - Deployment Name: iron-gallery-deployment-nautilus
  - Container Image: kodekloud/irongallery:2.0
  - Container Name: iron-gallery-container-nautilus
  - Namespace: iron-namespace-nautilus
  - Replicas: 1
  - Labels: run: iron-gallery
  - Resource Limits:
    - CPU: 50m
    - Memory: 100Mi
  - Volumes:
    - config → /usr/share/nginx/html/data
    - images → /usr/share/nginx/html/uploads
The volumes use emptyDir, which means they are ephemeral (cleared when the pod restarts).

This setup allows NGINX to serve static files and uploaded images properly.

#### 3. Iron DB Deployment — iron-db-deployment.yaml
This deployment runs the MariaDB database used by the Iron Gallery app.
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: iron-db-deployment-nautilus
  namespace: iron-namespace-nautilus
spec:
  replicas: 1
  selector:
    matchLabels:
      db: mariadb
  template:
    metadata:
      labels:
        db: mariadb
    spec:
      containers:
        - name: iron-db-container-nautilus
          image: kodekloud/irondb:2.0
          env:
            - name: MYSQL_DATABASE
              value: database_host
            - name: MYSQL_ROOT_PASSWORD
              value: "RootP@ssw0rd!"
            - name: MYSQL_PASSWORD
              value: "UserP@ssw0rd123"
            - name: MYSQL_USER
              value: customuser
          volumeMounts:
            - name: db
              mountPath: /var/lib/mysql
      volumes:
        - name: db
          emptyDir: {}
```
Key Details:
  - Deployment Name: iron-db-deployment-nautilus
  - Container Image: kodekloud/irondb:2.0
  - Container Name: iron-db-container-nautilus
  - Namespace: iron-namespace-nautilus
  - Replicas: 1
  - Labels: db: mariadb
  - Environment Variables:
      - MYSQL_DATABASE: database_host
      - MYSQL_ROOT_PASSWORD: RootP@ssw0rd!
      - MYSQL_USER: customuser
      - MYSQL_PASSWORD: UserP@ssw0rd123
  - Volume Mount: /var/lib/mysql → Persistent data directory (using emptyDir)
⚠️ Note: In production, passwords should be stored securely using Kubernetes Secrets.

#### 4. Iron DB Service — iron-db-service.yaml
This Service exposes the database internally within the cluster so that only other pods (like Iron Gallery) can reach it.
```yaml
apiVersion: v1
kind: Service
metadata:
  name: iron-db-service-nautilus
  namespace: iron-namespace-nautilus
spec:
  selector:
    db: mariadb
  ports:
    - protocol: TCP
      port: 3306
      targetPort: 3306
  type: ClusterIP
```
Key Details:
  - Service Name: iron-db-service-nautilus
  - Type: ClusterIP
  - Port: 3306 (MySQL default)
  - Selector: db: mariadb
This internal service enables the Iron Gallery frontend to communicate with the database using a stable DNS name like:
```pgsql
iron-db-service-nautilus.iron-namespace-nautilus.svc.cluster.local
```
#### 5. Iron Gallery Service — iron-gallery-service.yaml
This Service exposes the frontend to users outside the cluster.
```yaml
apiVersion: v1
kind: Service
metadata:
  name: iron-gallery-service-nautilus
  namespace: iron-namespace-nautilus
spec:
  selector:
    run: iron-gallery
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 32678
  type: NodePort
```
Key Details:
  - Service Name: iron-gallery-service-nautilus
  - Type: NodePort
  - Port: 80
  - NodePort: 32678
  - Selector: run: iron-gallery
Users can access the Iron Gallery web app through any cluster node’s IP and the assigned NodePort:
```cpp
http://<NodeIP>:32678
```
### Why We Used Separate Files
  - Easier to read, manage, and update specific components.
  - Ideal for CI/CD pipelines, where deployments and services can be managed independently.
  - Follows Kubernetes best practices for resource organization.

## How to Deploy
Apply all manifests with a single command:
```bash
kubectl apply -f .
```
Or apply each file individually:
```bash
kubectl apply -f namespace.yaml
kubectl apply -f iron-db-deployment.yaml
kubectl apply -f iron-gallery-deployment.yaml
kubectl apply -f iron-db-service.yaml
kubectl apply -f iron-gallery-service.yaml
```
## Verification Commands
Check if all resources are created:
```bash
kubectl get all -n iron-namespace-nautilus
```
Check services:
```bash
kubectl get svc -n iron-namespace-nautilus
```
Test access (from your browser or curl):
```bash
curl http://<NodeIP>:32678
```
<img width="1418" height="826" alt="Screenshot 2025-10-23 at 9 33 44 PM" src="https://github.com/user-attachments/assets/d13702fc-ca9a-4b57-99f8-47d2d6929de0" />

## Future Improvements
  - Use PersistentVolumeClaims (PVC) for database and image storage.
  - Store passwords in Kubernetes Secrets instead of plain text.
  - Add ConfigMaps for environment-based configurations.
  - Integrate Helm charts for templated deployment management.

## Summary
This project demonstrates how to:
  - Deploy multi-tier applications (frontend + database) on Kubernetes.
  - Use Deployments, Services, and Namespaces efficiently.
  - Follow DevOps and Kubernetes best practices for modular and scalable configurations.

