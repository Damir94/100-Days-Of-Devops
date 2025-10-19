# Deploying a PHP + MySQL (LAMP) Stack on Kubernetes

This project demonstrates how to deploy a PHP website with Apache and MySQL on a Kubernetes cluster using ConfigMaps, Secrets, Deployments, and Services.
It follows best practices like environment variable injection, configuration separation, and containerized deployment.

## Overview
The Nautilus DevOps team wanted to host a PHP-based web application using Apache as the web server and MySQL as the database.
The goal was to create a scalable, secure, and easily maintainable setup on Kubernetes.

The deployment includes:
 - A ConfigMap for PHP configuration (php.ini)
 - A Secret for sensitive MySQL credentials
 - A Deployment with two containers (PHP-Apache + MySQL)
 - Two Services (NodePort for web, ClusterIP for MySQL)
 - A PHP test page (index.php) that confirms database connectivity


1. ConfigMap for php.ini
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: php-config
data:
  php.ini: |
    variables_order = "EGPCS"
```
2. Secret for MySQL credentials
(You can customize the values)
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysql-secret
type: Opaque
stringData:
  mysql-root-password: rootpass123
  mysql-user: dbuser
  mysql-password: dbpass123
  mysql-database: mydb
  mysql-host: mysql-service
```

3. Deployment for Apache (PHP) + MySQL
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: lamp-wp
spec:
  replicas: 1
  selector:
    matchLabels:
      app: lamp-app
  template:
    metadata:
      labels:
        app: lamp-app
    spec:
      containers:
        - name: httpd-php-container
          image: webdevops/php-apache:alpine-3-php7
          ports:
            - containerPort: 80
          volumeMounts:
            - name: php-config-volume
              mountPath: /opt/docker/etc/php/php.ini
              subPath: php.ini
            - name: app-code
              mountPath: /app
          env:
            - name: MYSQL_ROOT_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysql-secret
                  key: mysql-root-password
            - name: MYSQL_DATABASE
              valueFrom:
                secretKeyRef:
                  name: mysql-secret
                  key: mysql-database
            - name: MYSQL_USER
              valueFrom:
                secretKeyRef:
                  name: mysql-secret
                  key: mysql-user
            - name: MYSQL_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysql-secret
                  key: mysql-password
            - name: MYSQL_HOST
              valueFrom:
                secretKeyRef:
                  name: mysql-secret
                  key: mysql-host

        - name: mysql-container
          image: mysql:5.6
          ports:
            - containerPort: 3306
          env:
            - name: MYSQL_ROOT_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysql-secret
                  key: mysql-root-password
            - name: MYSQL_DATABASE
              valueFrom:
                secretKeyRef:
                  name: mysql-secret
                  key: mysql-database
            - name: MYSQL_USER
              valueFrom:
                secretKeyRef:
                  name: mysql-secret
                  key: mysql-user
            - name: MYSQL_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysql-secret
                  key: mysql-password
      volumes:
        - name: php-config-volume
          configMap:
            name: php-config
        - name: app-code
          emptyDir: {}
```
4. Services

Web (Apache) Service
```yaml
apiVersion: v1
kind: Service
metadata:
  name: lamp-service
spec:
  type: NodePort
  selector:
    app: lamp-app
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30008
```
MySQL Service
```yaml
apiVersion: v1
kind: Service
metadata:
  name: mysql-service
spec:
  selector:
    app: lamp-app
  ports:
    - port: 3306
      targetPort: 3306
```

5. Inside your index.php file
Make sure MySQL connection variables use environment variables (not hard-coded):

```php
<?php
// Use environment variables instead of hardcoding
$dbname = getenv('MYSQL_DATABASE');
$dbuser = getenv('MYSQL_USER');
$dbpass = getenv('MYSQL_PASSWORD');
$dbhost = getenv('MYSQL_HOST');

// Connect to MySQL
$connect = mysqli_connect($dbhost, $dbuser, $dbpass, $dbname);

if (!$connect) {
    die("Connection failed: " . mysqli_connect_error());
}

// Run a test query
$test_query = "SHOW TABLES";
$result = mysqli_query($connect, $test_query);

if ($result) {
    echo "Connected successfully";
} else {
    echo "Error: " . mysqli_error($connect);
}

mysqli_close($connect);
?>
```
## Explanation
- getenv() retrieves environment variables set in your Kubernetes Deployment (e.g., MYSQL_USER, MYSQL_PASSWORD).
- mysqli_connect($dbhost, $dbuser, $dbpass, $dbname) connects directly using those variables.
- Always check for connection errors using mysqli_connect_error().
- Running a simple query like SHOW TABLES verifies the connection works.nside your index.php file

## Steps to Deploy
Create the ConfigMap
```bash
kubectl apply -f php-config.yaml
```
Create the Secret
```bash
kubectl apply -f mysql-secret.yaml
```
Deploy the Application
```bash
kubectl apply -f lamp-deployment.yaml
```
Expose Services
```bash
kubectl apply -f lamp-service.yaml
kubectl apply -f mysql-service.yaml
```
Copy PHP File to Container
```bash
kubectl cp /tmp/index.php $(kubectl get pod -l app=lamp-app -o jsonpath='{.items[0].metadata.name}'):/app -c httpd-php-container
```
Access the Application
Visit:
```bash
http://<NodeIP>:30008
```
<img width="653" height="168" alt="Screenshot 2025-10-19 at 10 04 51 AM" src="https://github.com/user-attachments/assets/05e1eb84-14d0-4fd1-8590-c77ef9a60966" />

You should see:
```nginx
Connected successfully
```

## Outcome
 - The LAMP stack runs inside Kubernetes using containers.
 - PHP and MySQL communicate securely via environment variables.
 - Application is accessible externally via NodePort.
 - No sensitive data is hardcoded — Secrets handle all credentials.
 - Configurations and app code are fully decoupled for easy updates.
