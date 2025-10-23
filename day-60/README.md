# 100 Days of Devops - Day 60: Web Application Deployment on Kubernetes

## Overview
This project demonstrates how to deploy a simple web application (using Apache HTTPD) on a Kubernetes cluster with persistent storage.
The deployment includes:
  - A PersistentVolume (PV) for storing web content.
  - A PersistentVolumeClaim (PVC) for accessing storage.
  - A Pod running the Apache web server.
  - A NodePort Service for external access.

## Requirements
| Resource Type         | Name         | Key Configuration                                    |
| --------------------- | ------------ | ---------------------------------------------------- |
| PersistentVolume      | `pv-devops`  | 5Gi storage, `manual` class, `/mnt/finance` hostPath |
| PersistentVolumeClaim | `pvc-devops` | 2Gi request, `manual` class                          |
| Pod                   | `pod-devops` | Uses `httpd:latest`, mounts PVC at document root     |
| Service               | `web-devops` | Type `NodePort`, exposed on port `30008`             |

## Kubernetes Manifests
1. PersistentVolume: pv-devops
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-devops
spec:
  storageClassName: manual
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /mnt/finance
```

2. PersistentVolumeClaim: pvc-devops
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-devops
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 2Gi
```

3. Pod: pod-devops
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-devops
  labels:
    app: httpd
spec:
  containers:
    - name: container-devops
      image: httpd:latest
      ports:
        - containerPort: 80
      volumeMounts:
        - name: devops-storage
          mountPath: /usr/local/apache2/htdocs
  volumes:
    - name: devops-storage
      persistentVolumeClaim:
        claimName: pvc-devops
```
4. Service: web-devops
```yaml
apiVersion: v1
kind: Service
metadata:
  name: web-devops
spec:
  type: NodePort
  selector:
    app: httpd
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30008
```
## Deployment Steps
Step 1: Apply all manifests

Save all the YAML content in a single file (for example, web-deployment.yaml) or multiple files, and run:
```bash
kubectl apply -f web-deployment.yaml
```
Step 2: Verify the resources
```bash
kubectl get pv
kubectl get pvc
kubectl get pods
kubectl get svc
```

You should see:
  - PV and PVC in Bound state.
  - Pod Running.
  - Service NodePort (30008) exposed.

Example output:
```pgsql
NAME         STATUS   VOLUME     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
pvc-devops   Bound    pv-devops  5Gi        RWO            manual         2m

NAME           TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
web-devops     NodePort   10.96.11.123   <none>        80:30008/TCP   1m
```
## Access the Web Application
Once the pod is running, open Grafana in your browser using:
```cpp
http://<NodeIP>:30008
```

<img width="548" height="183" alt="Screenshot 2025-10-22 at 10 28 26 PM" src="https://github.com/user-attachments/assets/5657f11f-8ce1-4153-bf43-02fc2f76b328" />

You should see the Apache HTTPD default page.
Tip: Replace <NodeIP> with your Kubernetes node’s IP address (run kubectl get nodes -o wide to find it).

## Cleanup
To remove all resources created:
```bash
kubectl delete -f web-deployment.yaml
```
## Notes
  - The directory /mnt/finance already exists on the cluster node — no need to create or access it manually.
  - Storage is persistent — even if the pod restarts, your web files remain.
  - You can later use ConfigMaps or PersistentVolumes with NFS for shared storage.
