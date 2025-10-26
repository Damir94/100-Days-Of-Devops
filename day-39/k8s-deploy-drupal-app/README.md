# Deploying Drupal with MySQL on Kubernetes (with Separate YAML Files)
This guide explains how to deploy a Drupal application connected to a MySQL database on a Kubernetes cluster using Persistent Volumes, Secrets, and Services.

### File 1: mysql-pv-pvc.yaml
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: drupal-mysql-pv
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /drupal-mysql-data
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: drupal-mysql-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 3Gi
```
### Explanation
  - PersistentVolume (PV): Defines a 5Gi storage space on the worker node using hostPath (/drupal-mysql-data), which already exists on the node.
  - PersistentVolumeClaim (PVC): Requests 3Gi of that space for the MySQL pod.AccessMode ReadWriteOnce: Ensures only one node can write to it at a time, which is perfect for databases.

### File 2: mysql-secret.yaml
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: drupal-mysql-secret
type: Opaque
stringData:
  mysql-root-password: root
  mysql-database: drupal
  mysql-user: drupal
  mysql-password: drupal
```
### Explanation
  - Secret stores MySQL credentials securely.
  - Using stringData means you can write readable values (Kubernetes encodes them automatically in base64).
  - These secrets will be injected into the MySQL and Drupal pods as environment variables.

### File 3: mysql-deployment.yaml
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: drupal-mysql
spec:
  replicas: 1
  selector:
    matchLabels:
      app: drupal-mysql
  template:
    metadata:
      labels:
        app: drupal-mysql
    spec:
      containers:
        - name: drupal-mysql
          image: mysql:5.7
          ports:
            - containerPort: 3306
          env:
            - name: MYSQL_ROOT_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: drupal-mysql-secret
                  key: mysql-root-password
            - name: MYSQL_DATABASE
              valueFrom:
                secretKeyRef:
                  name: drupal-mysql-secret
                  key: mysql-database
            - name: MYSQL_USER
              valueFrom:
                secretKeyRef:
                  name: drupal-mysql-secret
                  key: mysql-user
            - name: MYSQL_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: drupal-mysql-secret
                  key: mysql-password
          volumeMounts:
            - name: mysql-storage
              mountPath: /var/lib/mysql
      volumes:
        - name: mysql-storage
          persistentVolumeClaim:
            claimName: drupal-mysql-pvc
```
### Explanation
  - Deployment ensures the MySQL pod runs continuously, restarting if it crashes.
  - The container uses the mysql:5.7 image.
  - Environment variables pull values from the secret created earlier.
  - The volume mount /var/lib/mysql stores data in the PVC, so data persists across restarts.

### File 4: mysql-service.yaml
```yaml
apiVersion: v1
kind: Service
metadata:
  name: drupal-mysql-service
spec:
  selector:
    app: drupal-mysql
  ports:
    - port: 3306
      targetPort: 3306
```
### Explanation
  - This service exposes the MySQL pod internally within the Kubernetes cluster on port 3306.
  - Drupal will use the service name drupal-mysql-service as the database host (DRUPAL_DB_HOST).
  - It doesn’t need to be externally accessible since only Drupal connects to it.

### File 5: drupal-deployment.yaml
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: drupal
spec:
  replicas: 1
  selector:
    matchLabels:
      app: drupal
  template:
    metadata:
      labels:
        app: drupal
    spec:
      containers:
        - name: drupal
          image: drupal:8.6
          ports:
            - containerPort: 80
          env:
            - name: DRUPAL_DB_HOST
              value: drupal-mysql-service
            - name: DRUPAL_DB_NAME
              valueFrom:
                secretKeyRef:
                  name: drupal-mysql-secret
                  key: mysql-database
            - name: DRUPAL_DB_USER
              valueFrom:
                secretKeyRef:
                  name: drupal-mysql-secret
                  key: mysql-user
            - name: DRUPAL_DB_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: drupal-mysql-secret
                  key: mysql-password
```
### Explanation
  - Runs the official Drupal 8.6 image.
  - Connects to MySQL using credentials from the same secret.
  - Uses environment variables for database connectivity.
  - No volume mount is needed since Drupal content is stored in the database.

### File 6: drupal-service.yaml
```yaml
apiVersion: v1
kind: Service
metadata:
  name: drupal-service
spec:
  type: NodePort
  selector:
    app: drupal
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30095
```
### Explanation
  - Exposes Drupal externally using NodePort 30095.
  - You can access the Drupal setup page using your node’s IP:
```cpp
http://<Node_IP>:30095
```
  - The service routes external traffic to port 80 of the Drupal container.

### Deployment Steps
Run each file in order:
```bash
kubectl apply -f mysql-pv-pvc.yaml
kubectl apply -f mysql-secret.yaml
kubectl apply -f mysql-deployment.yaml
kubectl apply -f mysql-service.yaml
kubectl apply -f drupal-deployment.yaml
kubectl apply -f drupal-service.yaml
```

### Verify Setup
Check everything is running properly:
```bash
kubectl get pods
kubectl get svc
kubectl get pvc
```

### Expected output:
  - drupal and drupal-mysql pods in Running state
  - drupal-service showing NodePort 30095
  - drupal-mysql-service available on port 3306
  - PVC bound to PV

### Access the Application
Once all pods are running:
```cpp
http://<Node_IP>:30095
```
<img width="1563" height="816" alt="Screenshot 2025-10-25 at 8 20 34 PM" src="https://github.com/user-attachments/assets/f54a9eed-558c-4c03-9819-df4cbaca79f4" />
