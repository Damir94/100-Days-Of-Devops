# Implementing the Sidecar Pattern for Nginx Log Shipping in Kubernetes

## Overview
This project demonstrates how to use the Sidecar Container Pattern in Kubernetes to separate concerns 
within a Pod — one container serves web content using Nginx, and another container continuously ships 
the access and error logs from Nginx to a logging system (simulated here by printing logs every 30 seconds).
This approach follows the DevOps best practice of “one container, one responsibility.”

## Use Case
The Nautilus DevOps team runs a web server container using the official nginx image.
They need the last 24 hours of web server logs (both access.log and error.log) available 
for developers to debug issues. However, logs are not critical enough to require persistent storage.

To achieve this:
 - Nginx serves web pages and writes logs to a shared directory.
 - A sidecar container periodically reads and ships those logs.
 - Both containers share a temporary in-memory volume (emptyDir).

## Kubernetes Design
We will create a single Pod named webserver with:
 - Two containers:
     - nginx-container → runs the Nginx web server
     - sidecar-container → continuously reads and prints Nginx logs

 - A shared emptyDir volume called shared-logs mounted on both containers at /var/log/nginx.

This setup ensures:
 - Both containers can access the same log files.
 - Logs remain available while the Pod is running.
 - Containers remain isolated by purpose (Nginx serves; Sidecar ships).

## Step-by-Step Implementation
1. Create a Pod Definition File
Create a YAML file named webserver.yaml and add the following configuration:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: webserver
spec:
  volumes:
    - name: shared-logs
      emptyDir: {}

  containers:
    - name: nginx-container
      image: nginx:latest
      volumeMounts:
        - name: shared-logs
          mountPath: /var/log/nginx

    - name: sidecar-container
      image: ubuntu:latest
      command: ["sh", "-c", "while true; do cat /var/log/nginx/access.log /var/log/nginx/error.log; sleep 30; done"]
      volumeMounts:
        - name: shared-logs
          mountPath: /var/log/nginx
```
2. Apply the Pod Manifest
Run the following command to create the Pod:
```bash
kubectl apply -f webserver.yaml
```
Check that the Pod is running:
```bash
kubectl get pods
```
Expected output:
```sql
NAME         READY   STATUS    RESTARTS   AGE
webserver    2/2     Running   0          1m
```
3. Verify Both Containers Are Running
To check the containers inside the Pod:
```bash
kubectl describe pod webserver
```
You should see two containers:
nginx-container and sidecar-container.

4. View the Logs from the Sidecar
Since the sidecar container is continuously reading the Nginx log files and printing them, you can view the output by running:
```bash
kubectl logs webserver -c sidecar-container
```
You’ll see combined logs from both:
 - /var/log/nginx/access.log
 - /var/log/nginx/error.log
The logs will appear every 30 seconds as the sidecar prints them.

<img width="1479" height="810" alt="Screenshot 2025-10-14 at 10 27 52 AM" src="https://github.com/user-attachments/assets/f0453e05-ad22-448e-a039-df7027971b6b" />

## Why the Sidecar Pattern?
The Sidecar Pattern is a core design pattern in containerized environments like Kubernetes.
It allows two or more containers to run in the same Pod and share the same network and storage space.

Benefits:
 - Separation of concerns: Each container focuses on a single responsibility.
 - Reusability: The log shipper container can be reused with other web servers.
 - Scalability: The web server and log shipper can be scaled or modified independently.
 - Simplified architecture: No external dependencies are needed to collect logs.

## Understanding the Components
| Component             | Type              | Description                                                 |
| --------------------- | ----------------- | ----------------------------------------------------------- |
| **Pod**               | Kubernetes Object | Smallest deployable unit in Kubernetes.                     |
| **nginx-container**   | Container         | Serves web content and generates logs.                      |
| **sidecar-container** | Container         | Reads and ships logs from Nginx every 30 seconds.           |
| **emptyDir Volume**   | Temporary Storage | Shared directory that exists as long as the Pod is running. |


## Cleanup
When you’re done testing, delete the Pod to free up resources:
```bash
kubectl delete pod webserver
```

## Summary
In this project, we:
 - Deployed a Pod with two containers — Nginx and Ubuntu.
 - Implemented the Sidecar Pattern to handle log shipping.
 - Used a shared emptyDir volume to store and access Nginx logs.
 - Verified the setup by observing logs via the sidecar container.
This simple but powerful setup demonstrates how Kubernetes allows multiple
containers to collaborate efficiently inside a Pod while keeping their responsibilities clearly separated.
