# PHP + Nginx Deployment on Kubernetes
With PHP-FPM, ConfigMap, Shared Volume, and NodePort Service

## Overview
This project demonstrates how to deploy a PHP-based application using Nginx and PHP-FPM in a Kubernetes cluster.
It uses:
  - A ConfigMap for custom Nginx configuration
  - A shared emptyDir volume for both containers
  - A NodePort service to expose the application externally

### Architecture Diagram
```yaml
             ┌──────────────────────┐
             │   NodePort Service   │
             │ Port: 30012 -> 8091  │
             └──────────┬───────────┘
                        │
             ┌──────────▼───────────┐
             │     Pod: nginx-phpfpm│
             │ ┌──────────────────┐ │
             │ │ nginx-container  │ │
             │ │ - serves traffic │ │
             │ │ - port 8091      │ │
             │ └──────────────────┘ │
             │ ┌──────────────────┐ │
             │ │ php-fpm-container│ │
             │ │ - runs PHP code  │ │
             │ │ - port 9000      │ │
             │ └──────────────────┘ │
             │ Shared Volume: /var/www/html │
             └──────────────────────────────┘
```
### Step 1: Create the ConfigMap
Create a file named nginx.conf under /opt/nginx.conf with the following content:
```nginx
events {}

http {
    server {
        listen 0.0.0.0:8091;
        root /var/www/html;
        index index.html index.htm index.php;

        location / {
            try_files $uri $uri/ =404;
        }

        location ~ \.php$ {
            include fastcgi_params;
            fastcgi_pass 127.0.0.1:9000;
            fastcgi_index index.php;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        }
    }
}
```
Create the ConfigMap:
```bash
kubectl create configmap nginx-config --from-file=nginx.conf=/opt/nginx.conf
```
### Step 2: Create the Pod (nginx + php-fpm)
nginx-phpfpm.yaml
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-phpfpm
  labels:
    app: nginx-phpfpm
spec:
  containers:
    - name: nginx-container
      image: nginx:latest
      ports:
        - containerPort: 8091
      volumeMounts:
        - name: shared-files
          mountPath: /var/www/html
        - name: nginx-config-volume
          mountPath: /etc/nginx/nginx.conf
          subPath: nginx.conf

    - name: php-fpm-container
      image: php:8.2-fpm-alpine
      volumeMounts:
        - name: shared-files
          mountPath: /var/www/html

  volumes:
    - name: shared-files
      emptyDir: {}
    - name: nginx-config-volume
      configMap:
        name: nginx-config
        items:
          - key: nginx.conf
            path: nginx.conf
```
Apply:
```bash
kubectl apply -f nginx-phpfpm.yaml
```
### Step 3: Expose the Application via NodePort
nginx-service.yaml
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: NodePort
  selector:
    app: nginx-phpfpm
  ports:
    - port: 8091
      targetPort: 8091
      nodePort: 30012
```
Apply:
```bash
kubectl apply -f nginx-service.yaml
```
### Step 4: Copy the PHP File
Copy your application file into the container:
```bash
kubectl cp /opt/index.php nginx-phpfpm:/var/www/html/index.php -c nginx-container
```
Then verify inside the container:
```bash
kubectl exec -it nginx-phpfpm -c nginx-container -- ls /var/www/html
```
### Step 5: Verify Setup
Check resources:
```bash
kubectl get pods,svc
```
Access in browser or lab “App” button:
```bash
http://<NodeIP>:30012
```
<img width="596" height="186" alt="Screenshot 2025-10-25 at 8 56 14 AM" src="https://github.com/user-attachments/assets/e0ec19bc-3cd7-413d-bfe8-ee27bf2ee315" />

### Troubleshooting Guide
#### 502 Bad Gateway - Meaning: nginx can’t reach php-fpm.

#### Fix Steps:
1. Ensure both containers are in the same Pod.
2. Check php-fpm is listening:
```bash
kubectl exec -it nginx-phpfpm -c php-fpm-container -- netstat -tlnp
```
You should see port 9000 listening.

3. Ensure nginx.conf includes:
```nginx
fastcgi_pass 127.0.0.1:9000;
```
4. Restart nginx:
```bash
kubectl exec -it nginx-phpfpm -c nginx-container -- nginx -s reload
```
#### 403 Forbidden - Meaning: nginx can reach php-fpm, but access is denied due to file permissions or missing file.

#### Fix Steps:
1. Check file exists:
```bash
kubectl exec -it nginx-phpfpm -c nginx-container -- ls -l /var/www/html
```
2. Fix permissions:
```bash
kubectl exec -it nginx-phpfpm -c nginx-container -- chmod 644 /var/www/html/index.php
kubectl exec -it nginx-phpfpm -c nginx-container -- chown -R nginx:nginx /var/www/html
```
3. Ensure index.php is listed in nginx config:
```nginx
index index.html index.htm index.php;
```
#### Service not connecting (No response / timeout) - Meaning: The Service cannot find the Pod.

#### Fix Steps:

1. Check labels:
```bash
kubectl get pods --show-labels
```
You should see: app=nginx-phpfpm
2. Check Service selector:
```yaml
selector:
  app: nginx-phpfpm
```
3. Verify endpoints:
```bash
kubectl get endpoints nginx-service
```
Should show the Pod IP and port 8091.

### Verification Checklist
| Task                                      | Status |
| ----------------------------------------- | ------ |
| ConfigMap created (`nginx-config`)        | ✅      |
| Pod running (`nginx-phpfpm`)              | ✅      |
| Both containers running (nginx + php-fpm) | ✅      |
| NodePort service exposed on `30012`       | ✅      |
| App accessible via browser                | ✅      |


## Summary
This setup demonstrates how to:
  - Use multi-container Pods to connect web and app layers.
  - Use ConfigMaps for custom nginx configuration.
  - Use emptyDir volumes for shared file systems.
  - Expose apps using NodePort in Kubernetes.
  - Troubleshoot common nginx/php-fpm issues like 502 and 403.
