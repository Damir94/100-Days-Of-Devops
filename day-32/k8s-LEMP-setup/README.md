## Deploying a LEMP (Nginx + PHP-FPM + MySQL) Stack on Kubernetes
This project demonstrates how the Nautilus DevOps team deployed a static PHP website 
on a Kubernetes cluster using Nginx, PHP-FPM, and MySQL, along with Kubernetes Secrets, ConfigMaps, and NodePort Services.

## 1. Overview
The goal was to deploy a web application that connects to a MySQL database securely using environment variables stored in Kubernetes Secrets. The setup consists of:
  - Deployment: lemp-wp containing two containers
      - nginx-php-container → serves the PHP application
      - mysql-container → hosts the MySQL database
  - Secrets: Store database credentials and configuration
  - ConfigMap: Store PHP configuration (php.ini)
  - Services:
      - lemp-service (NodePort 30008) for web access
      - mysql-service (ClusterIP 3306) for internal DB connectivity

## 2. Step-by-Step Setup
A. Create Secrets
```bash
kubectl create secret generic mysql-root-pass --from-literal=password=R00t
kubectl create secret generic mysql-user-pass \
  --from-literal=username=kodekloud_rin \
  --from-literal=password=TmPcZjtRQx
kubectl create secret generic mysql-db-url --from-literal=database=kodekloud_db9
kubectl create secret generic mysql-host --from-literal=host=mysql-service
```
B. Create ConfigMap
```bash
kubectl create configmap php-config \
  --from-literal=php.ini='variables_order = "EGPCS"'
```
C. Create Deployment

The deployment lemp-wp contains two containers: Nginx-PHP and MySQL.
Both use environment variables from Secrets, and the PHP container mounts the php-config ConfigMap.
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: lemp-wp
spec:
  replicas: 1
  selector:
    matchLabels:
      app: lemp-wp
  template:
    metadata:
      labels:
        app: lemp-wp
    spec:
      containers:
        - name: nginx-php-container
          image: webdevops/php-nginx:alpine-3-php7
          ports:
            - containerPort: 80
          volumeMounts:
            - name: php-config-volume
              mountPath: /opt/docker/etc/php/php.ini
              subPath: php.ini
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
            - name: MYSQL_HOST
              valueFrom:
                secretKeyRef:
                  name: mysql-host
                  key: host

        - name: mysql-container
          image: mysql:5.6
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

      volumes:
        - name: php-config-volume
          configMap:
            name: php-config
```
Apply:
```bash
kubectl apply -f lemp-wp.yaml
```
D. Create Services
MySQL Service
```yaml
apiVersion: v1
kind: Service
metadata:
  name: mysql-service
spec:
  selector:
    app: lemp-wp
  ports:
    - port: 3306
      targetPort: 3306
```
Web Service (NodePort)
```yaml
apiVersion: v1
kind: Service
metadata:
  name: lemp-service
spec:
  type: NodePort
  selector:
    app: lemp-wp
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30008
```
Apply both:
```bash
kubectl apply -f mysql-service.yaml
kubectl apply -f lemp-service.yaml
```
E. Add Application File

The /tmp/index.php file was copied into the running Nginx container under /app:
```bash
kubectl cp /tmp/index.php <pod-name>:/app -c nginx-php-container
```
Correct index.php content:
```php
<?php
$dbname = getenv('MYSQL_DATABASE');
$dbuser = getenv('MYSQL_USER');
$dbpass = getenv('MYSQL_PASSWORD');
$dbhost = getenv('MYSQL_HOST');

$connect = mysqli_connect($dbhost, $dbuser, $dbpass, $dbname);

if (!$connect) {
    die("Connection failed: " . mysqli_connect_error());
}
echo "Connected successfully";
?>
```
## 3. Troubleshooting Journey
Issue 1: 403 Forbidden
  - Cause: The index.php file was missing or had wrong permissions.
  - Fix: Copied index.php to /app, set correct permissions:
```bash
kubectl cp /tmp/index.php <pod>:/app -c nginx-php-container
kubectl exec -it <pod> -c nginx-php-container -- chmod 644 /app/index.php
```
Issue 2: Unable to Connect to 'dbhost'
  - Cause: PHP file had hard-coded credentials ('dbhost', 'dbuser', 'dbpass').
  - Fix: Rewrote PHP to use environment variables via getenv().

Issue 3: RSA public key file not found
  - Cause: MySQL 5.6 prints this warning if RSA keys aren’t generated.
  - Impact: Harmless — doesn’t affect normal authentication.
  - Optional Fix: Run inside MySQL container:
```bash
mysql_ssl_rsa_setup
```
Issue 4: DNS or Service Resolution
Verified with:
```bash
kubectl exec -it <nginx-pod> -c nginx-php-container -- ping -c 2 mysql-service
```
Ensured mysql-service and nginx pods were in the same namespace.

4. Verification
Check Pods and Services:
```bash
kubectl get pods
kubectl get svc
```
Access the site:
```bash
http://<node-ip>:30008
```
Expected output:

<img width="660" height="202" alt="Screenshot 2025-10-22 at 10 18 04 PM" src="https://github.com/user-attachments/assets/30907e5a-708e-4cd1-992b-db4db091cff3" />

Connected successfully

## 5. Lessons Learned
  - Always use environment variables from Secrets, not hard-coded DB credentials.
  - The kubectl expose command doesn’t accept --node-port; use YAML manifests instead.
  - MySQL readiness must be confirmed before PHP tries to connect.
  - Warnings like RSA key messages are not fatal for basic MySQL setups.

## Conclusion
This exercise demonstrated how to:
  - Deploy a multi-container workload in a single pod (LEMP stack)
  - Manage secure credentials via Kubernetes Secrets
  - Configure PHP using ConfigMaps
  - Expose the application externally with a NodePort service
  - Diagnose and fix connectivity, permission, and configuration issues
  - Once everything was configured, the application displayed:
  - Connected successfully
  - indicating the database connection and deployment were working as intended.
