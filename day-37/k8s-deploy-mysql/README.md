# MySQL Deployment on Kubernetes
This project sets up a MySQL server on a Kubernetes cluster with persistent storage, secrets for credentials, and a NodePort service for external access.
It follows the requirements defined by the Nautilus DevOps team.

## Overview of Tasks
1. Create a PersistentVolume (PV) — provides 250Mi of storage for MySQL data.
2. Create a PersistentVolumeClaim (PVC) — allows the pod to claim the PV storage.
3. Create a Deployment — runs the MySQL container and mounts the persistent volume.
4. Create a Service — exposes MySQL externally on port 30007.
5. Create Secrets — store database credentials securely.
6. Set Environment Variables — configure MySQL container using secret values.

### Step-by-Step YAML Breakdown
1. Persistent Volume (PV)
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: mysql-pv
spec:
  capacity:
    storage: 250Mi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  hostPath:
    path: /mnt/data/mysql
```

### Explanation:
  - PersistentVolume defines physical storage on the node.
  - capacity.storage sets the size to 250Mi.
  - accessModes: ReadWriteOnce means the volume can be mounted by one node at a time.
  - hostPath uses a local path /mnt/data/mysql to simulate persistent storage.
  - persistentVolumeReclaimPolicy: Retain ensures data isn’t deleted when PVC is released.

2. Persistent Volume Claim (PVC)
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pv-claim
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 250Mi
```
### Explanation:
  - The PVC requests 250Mi of storage from available PVs.
  - Once bound, it ensures MySQL data persists even after pod restarts.

3. Secrets for Credentials
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysql-root-pass
type: Opaque
data:
  password: WVVJaWRoYjY2Nw==   # Base64 for 'YUIidhb667'
---
apiVersion: v1
kind: Secret
metadata:
  name: mysql-user-pass
type: Opaque
data:
  username: a29kZWtsb3VkX3BvcA==   # Base64 for 'kodekloud_pop'
  password: R3lRa0ZSVk5yMw==       # Base64 for 'GyQkFRVNr3'
---
apiVersion: v1
kind: Secret
metadata:
  name: mysql-db-url
type: Opaque
data:
  database: a29kZWtsb3VkX2RiMg==   # Base64 for 'kodekloud_db2'
```
### Explanation:
  - Secrets store sensitive data in Base64 encoded format.
  - Each secret contains credentials required by MySQL (root password, username, and database name).
  - Using secrets prevents exposing credentials in plain text in YAML.

4. MySQL Deployment
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - name: mysql
        image: mysql:5.7
        ports:
        - containerPort: 3306
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
        volumeMounts:
        - name: mysql-storage
          mountPath: /var/lib/mysql
      volumes:
      - name: mysql-storage
        persistentVolumeClaim:
          claimName: mysql-pv-claim
```
### Explanation:
  - Deployment ensures MySQL runs with 1 replica for simplicity.
  - env variables configure MySQL from the secrets.
  - volumeMounts mount the claimed storage at /var/lib/mysql (default MySQL data directory).
  - Using mysql:5.7 as a stable version of MySQL image.

5. MySQL Service
```yaml
apiVersion: v1
kind: Service
metadata:
  name: mysql
spec:
  type: NodePort
  ports:
    - port: 3306
      targetPort: 3306
      nodePort: 30007
  selector:
    app: mysql
````
### Explanation:
  - NodePort exposes MySQL on all Kubernetes nodes at port 30007.
  - port/targetPort map the service to MySQL’s container port 3306.
  - Allows access from outside the cluster via any node’s IP.

## Verification Steps
1. Check PV and PVC Binding
```bash
kubectl get pv,pvc
```
Ensure STATUS is Bound.

2. Check Pod Status
```bash
kubectl get pods
```
Pod should show Running.

3. Access Service
```bash
kubectl get svc
```
Verify service mysql is exposed on NodePort 30007.

4. Optional: Test Connection
```bash
mysql -h <NodeIP> -P 30007 -u kodekloud_pop -p
```
Enter password GyQkFRVNr3 and confirm connection to database kodekloud_db2.

## Summary
This setup demonstrates:
  - Persistent storage management with PV/PVC
  - Secure handling of credentials with Kubernetes Secrets
  - Controlled external access via NodePort
  - Automated environment configuration through secret references
With this, MySQL is fully functional, persistent, and production-ready for internal testing on the cluster.
