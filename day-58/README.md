# 100 Days Devops - Day 58: Grafana Deployment on Kubernetes

## Overview
This project sets up Grafana, an open-source visualization and analytics platform, on a Kubernetes cluster using a Deployment and Service. 
Grafana will be accessible externally through a NodePort (32000) so you can open its web dashboard in a browser.

## Resources Created
This setup includes two Kubernetes resources:

1. Deployment — grafana-deployment-nautilus
  - Purpose: Deploys the Grafana application in a pod.
  - Image Used: grafana/grafana:latest
  - Replicas: 1 (you can scale later if needed)
  - Port: 3000 (default Grafana web UI port)
  - File: grafana-deployment.yaml
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: grafana-deployment-nautilus
  labels:
    app: grafana
spec:
  replicas: 1
  selector:
    matchLabels:
      app: grafana
  template:
    metadata:
      labels:
        app: grafana
    spec:
      containers:
        - name: grafana
          image: grafana/grafana:latest
          ports:
            - containerPort: 3000
```

2. Service — grafana-service
  - Purpose: Exposes the Grafana web interface externally.
  - Type: NodePort
  - NodePort: 32000 (to access from browser)
  - TargetPort: 3000 (Grafana container port)
  - File: grafana-service.yaml
```yaml
apiVersion: v1
kind: Service
metadata:
  name: grafana-service
spec:
  type: NodePort
  selector:
    app: grafana
  ports:
    - protocol: TCP
      port: 3000
      targetPort: 3000
      nodePort: 32000
```
## Deployment Instructions
Step 1: Apply the YAML manifests
Run the following commands:
```bash
kubectl apply -f grafana-deployment.yaml
kubectl apply -f grafana-service.yaml
```
Step 2: Verify the deployment
Check the running pods and services:
```bash
kubectl get pods
kubectl get svc
```
You should see:
```sql
NAME                         READY   STATUS    RESTARTS   AGE
grafana-deployment-nautilus   1/1     Running   0          1m
```
and
```pgsql
NAME               TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
grafana-service    NodePort   10.96.77.12     <none>        3000:32000/TCP   1m
```
## Access Grafana
Once the pod and service are running, open Grafana in your browser:
```bash
http://<NodeIP>:32000
```
Replace <NodeIP> with the external IP or hostname of your Kubernetes node.

<img width="1655" height="838" alt="Screenshot 2025-10-20 at 10 14 20 PM" src="https://github.com/user-attachments/assets/90623663-dd83-4842-8589-be869f9cf9b2" />

<img width="1618" height="647" alt="Screenshot 2025-10-20 at 10 14 47 PM" src="https://github.com/user-attachments/assets/bafebda6-b6d2-4d99-a87d-8f31f1431e90" />

## Default Login
Username: admin
Password: admin
(You’ll be prompted to change the password on first login.)

## Cleanup
To remove all created resources:
```bash
kubectl delete -f grafana-deployment.yaml
kubectl delete -f grafana-service.yaml
```

## Notes
  - This setup uses default Grafana configuration — no data sources or dashboards are preconfigured.
  - You can later use a ConfigMap or PersistentVolume if you want to store custom dashboards and settings.
  - Ensure the node’s firewall allows access to port 32000.
