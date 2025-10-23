# MySQL Deployment Troubleshooting and Fix Documentation

## Overview
This documentation describes the steps, issues, and solutions involved in fixing and successfully deploying a MySQL application on a Kubernetes cluster for the Nautilus DevOps team.
The task involved debugging and correcting a broken Kubernetes template (mysql_deployment.yml) that initially failed to apply due to multiple YAML syntax and configuration issues.

## Objective
  - Fix and validate the existing MySQL Kubernetes deployment template.
  - Ensure all resources (PersistentVolume, PersistentVolumeClaim, Deployment, and Service) are correctly defined.
  - Verify that the manifest applies cleanly and that all pods start successfully without removing any resource components.

## Kubernetes Components Used
| Resource                        | Purpose                                                           |
| ------------------------------- | ----------------------------------------------------------------- |
| **PersistentVolume (PV)**       | Provides persistent local storage for MySQL data using hostPath.  |
| **PersistentVolumeClaim (PVC)** | Requests storage from the PV with a defined size and access mode. |
| **Deployment**                  | Manages the MySQL pod lifecycle and ensures self-healing.         |
| **Service (NodePort)**          | Exposes the MySQL service externally on port `30011`.             |


## Final Directory Structure
```arduino
/home/thor/
└── mysql_deployment.yml
```

### Common Issues Found and Fixed
1. ❌ Wrong API Versions
Error:
```pgsql
no matches for kind "PersistentVolume" in version "apps/v1"
```
Fix:
PersistentVolume and PersistentVolumeClaim belong to apiVersion: v1, not apps/v1.

2. ❌ Indentation and Formatting Errors
Error:
```pgsql
error converting YAML to JSON: yaml: line 28: mapping values are not allowed in this context
```
Cause:
Misaligned indentation and misplaced colons (:) in accessModes, resources, and volumeMounts.

Fix:
All YAML keys were properly aligned using consistent 2-space indentation. Lists were prefixed with - under parent keys.

3. ❌ Invalid Field Names
Error:
```lua
unknown field "images" in io.k8s.api.core.v1.Container
```
Fix:
Changed images: → image: under the container definition.

4. ❌ Incorrect Resource Units
Error:
```yaml
invalid resource quantity: 250MB
```
Fix:
Changed 250MB → 250Mi (Kubernetes expects binary units like Mi, Gi).

5. ❌ Invalid List Usage
Error:
```makefile
persistentVolumeReclaimPolicy: - Retain
```
Fix:
Removed the dash -. This field accepts a single string value, not a list.

6. ⚠️ Selector and Label Mismatch
Cause:
Deployment and Service labels didn’t match properly (app: mysql vs app: mysql-app).
Fix:
Standardized all labels and selectors to:
```yaml
app: mysql-app
tier: mysql
```

## Fixed version
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: mysql-pv
  labels:
    type: local
spec:
  storageClassName: standard
  capacity:
    storage: 250Mi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /mnt/data
  persistentVolumeReclaimPolicy: Retain
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pv-claim
  labels:
    app: mysql-app
spec:
  storageClassName: standard
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 250Mi
---
apiVersion: v1
kind: Service
metadata:
  name: mysql
  labels:
    app: mysql-app
spec:
  type: NodePort
  ports:
    - port: 3306
      targetPort: 3306
      nodePort: 30011
  selector:
    app: mysql-app
    tier: mysql
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql-deployment
  labels:
    app: mysql-app
spec:
  selector:
    matchLabels:
      app: mysql-app
      tier: mysql
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: mysql-app
        tier: mysql
    spec:
      containers:
        - name: mysql
          image: mysql:5.6
          env:
            - name: MYSQL_ROOT_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysql-root-pass
                  key: password
            - name: MYSQL_DATABASE
              valueFrom:
                secretKeyRef:
                  name: mysql-db-url
                  key: database
            - name: MYSQL_USER
              valueFrom:
                secretKeyRef:
                  name: mysql-user-pass
                  key: username
            - name: MYSQL_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysql-user-pass
                  key: password
          ports:
            - containerPort: 3306
              name: mysql
          volumeMounts:
            - name: mysql-persistent-storage
              mountPath: /var/lib/mysql
      volumes:
        - name: mysql-persistent-storage
          persistentVolumeClaim:
            claimName: mysql-pv-claim
```

## Final Working Configuration
The final version includes the following resources in one YAML file:
  - PersistentVolume
  - PersistentVolumeClaim
  - Service (NodePort 30011)
  - Deployment (MySQL 5.6)

Each section was validated using:
```bash
kubectl apply -f mysql_deployment.yml --dry-run=client
```
and successfully deployed using:
```bash
kubectl apply -f mysql_deployment.yml
```
## Verification Steps
Check all resources
```bash
kubectl get all
```
Inspect PVC binding
```bash
kubectl get pv,pvc
```

Cofirm Pod is running
```bash
kubectl get pods
```
View logs
```bash
kubectl logs <mysql-pod-name>
```

## Troubleshooting Tips
| Issue                           | Possible Cause                           | Solution                                              |
| ------------------------------- | ---------------------------------------- | ----------------------------------------------------- |
| Pod stuck in `CrashLoopBackOff` | Secrets missing for MySQL credentials    | Create secrets using `kubectl create secret generic`  |
| PVC pending                     | PV and PVC `storageClassName` mismatch   | Ensure both use the same `storageClassName`           |
| NodePort not reachable          | Firewall or security group blocking port | Open NodePort range (30000–32767) in cluster firewall |
| YAML fails to parse             | Misaligned indentation                   | Validate YAML using `kubectl apply --dry-run=client`  |


## Conclusion
After fixing API versions, indentation, and resource misconfigurations, the MySQL Kubernetes deployment applied successfully.
The pod started correctly, persistent storage was attached, and the service was exposed on NodePort 30011.

This document can serve as a reference guide for future troubleshooting of Kubernetes YAML configurations.

