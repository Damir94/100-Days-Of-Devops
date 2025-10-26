# 100 Days of Devops - Day 63: Iron Gallery App Deployment — Nautilus DevOps Project

## Overview
The Nautilus DevOps team has been developing the Iron Gallery web application and its companion Iron DB database.
In this phase, we customized and deployed both components into a Kubernetes cluster within a dedicated namespace.

This documentation outlines how we created:
  - A custom namespace
  - Two deployments (Iron Gallery & Iron DB)
  - Two services (internal for DB and external for the web app)
  - Proper labels, resource limits, and persistent storage using emptyDir volumes

### Step 1: Create Namespace
To logically separate application components, we created a dedicated namespace:
```bash
kubectl create namespace iron-namespace-xfusion
```
Namespaces help isolate resources between projects or teams and simplify management.

### Step 2: Create Iron Gallery Deployment
We deployed the main Iron Gallery frontend application using the official image kodekloud/irongallery:2.0.
Deployment Specification
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: iron-gallery-deployment-xfusion
  namespace: iron-namespace-xfusion
  labels:
    run: iron-gallery
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
        - name: iron-gallery-container-xfusion
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
### Key Points
  - Labels: Used run: iron-gallery for selectors and service mapping.
  - Resource Limits: Ensures the container uses only limited CPU (50m) and memory (100Mi).
  - Volumes: Two emptyDir volumes simulate ephemeral storage for uploaded files and configuration data.
  - Container Name: iron-gallery-container-xfusion for clarity and consistency.
Apply it using:
```bash
kubectl apply -f iron-gallery-deployment.yaml
```

### Step 3: Create Iron DB Deployment
We then deployed the database using the image kodekloud/irondb:2.0.
This acts as the backend data store for the Iron Gallery app.
Deployment Specification
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: iron-db-deployment-xfusion
  namespace: iron-namespace-xfusion
  labels:
    db: mariadb
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
        - name: iron-db-container-xfusion
          image: kodekloud/irondb:2.0
          env:
            - name: MYSQL_DATABASE
              value: database_apache
            - name: MYSQL_ROOT_PASSWORD
              value: "Root@12345!"
            - name: MYSQL_USER
              value: "ironuser"
            - name: MYSQL_PASSWORD
              value: "Iron@56789!"
          volumeMounts:
            - name: db
              mountPath: /var/lib/mysql
      volumes:
        - name: db
          emptyDir: {}
```
### Key Points
  - Database: Uses MariaDB image customized for Iron Gallery.
  - Environment Variables:
  - MYSQL_DATABASE → database name
  - MYSQL_ROOT_PASSWORD, MYSQL_USER, MYSQL_PASSWORD → DB authentication
  - Volume: Mounted at /var/lib/mysql to simulate storage.
  - Labels: db: mariadb ensures correct service selection.
Apply with:
```bash
kubectl apply -f iron-db-deployment.yaml
```

### Step 4: Create Iron DB Service
To expose the database internally within the cluster, we created a ClusterIP service:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: iron-db-service-xfusion
  namespace: iron-namespace-xfusion
spec:
  selector:
    db: mariadb
  ports:
    - protocol: TCP
      port: 3306
      targetPort: 3306
  type: ClusterIP
```
### Explanation
- This allows the Iron Gallery pod to communicate securely with the DB using the internal DNS name:
   ```pgsql
     iron-db-service-xfusion.iron-namespace-xfusion.svc.cluster.local
    ```
- Port 3306 is the standard MariaDB/MySQL port.

### Step 5: Create Iron Gallery Service
To make the Iron Gallery application accessible from outside the cluster, we created a NodePort service:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: iron-gallery-service-xfusion
  namespace: iron-namespace-xfusion
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
### Explanation
  - Type: NodePort exposes the app externally on port 32678.
  - Selector: Matches the run: iron-gallery label in the deployment.
  - Protocol: Uses standard HTTP (TCP).
Apply with:
```bash
kubectl apply -f iron-gallery-service.yaml
```
### Step 6: Verification
After deployment, verify everything is running correctly:
```bash
kubectl get all -n iron-namespace-xfusion
```

Expected output:
```pgsql
NAME                                             READY   STATUS    RESTARTS   AGE
pod/iron-db-deployment-xfusion-xxxxx             1/1     Running   0          2m
pod/iron-gallery-deployment-xfusion-xxxxx        1/1     Running   0          2m

NAME                                TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/iron-db-service-xfusion     ClusterIP   10.0.0.20      <none>        3306/TCP         2m
service/iron-gallery-service-xfusion NodePort   10.0.0.35      <none>        80:32678/TCP     2m

NAME                                         READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/iron-db-deployment-xfusion   1/1     1            1           2m
deployment.apps/iron-gallery-deployment-xfusion 1/1  1            1           2m
```
You can test the Iron Gallery web app from your browser using:
```cpp
http://<NodeIP>:32678
```
<img width="1466" height="956" alt="Screenshot 2025-10-25 at 8 28 50 PM" src="https://github.com/user-attachments/assets/44289437-af6d-422f-afae-d4f2393281f6" />

### Summary

| Component            | Type       | Name                            | Purpose                |
| -------------------- | ---------- | ------------------------------- | ---------------------- |
| Namespace            | Namespace  | iron-namespace-xfusion          | Logical isolation      |
| Iron Gallery         | Deployment | iron-gallery-deployment-xfusion | Frontend web app       |
| Iron Gallery Service | NodePort   | iron-gallery-service-xfusion    | Exposes app externally |
| Iron DB              | Deployment | iron-db-deployment-xfusion      | Database backend       |
| Iron DB Service      | ClusterIP  | iron-db-service-xfusion         | Internal DB access     |

### Key Learnings
  - Namespaces isolate resources for easier management.
  - Labels and selectors connect Deployments and Services.
  - emptyDir volumes provide temporary storage useful for testing.
  - Resource limits protect nodes from overconsumption.
  - NodePort services make applications reachable externally for testing/demo.
