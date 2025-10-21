# Kubernetes Web Application with Persistent Volume — datacenter 

## Setup
This project demonstrates how to deploy a web application on a Kubernetes cluster using Persistent Volumes (PV) and Persistent Volume Claims (PVC) for storage. 
The setup ensures that the web server’s files remain persistent even if the pod restarts or gets rescheduled.

## Project Overview
The goal of this setup is to:
  - Deploy an Apache HTTP web server using httpd:latest.
  - Use a Persistent Volume (pv-datacenter) to store application code under /mnt/finance.
  - Bind the volume to a Persistent Volume Claim (pvc-datacenter) requesting 3Gi of storage.
  - Run a Pod (pod-datacenter) that mounts this persistent storage.
  - Expose the web application externally using a NodePort Service (web-datacenter) on port 30008.

## Directory Structure
```scss
kubernetes-datacenter/
├── pv-datacenter.yaml
├── pvc-datacenter.yaml
├── pod-datacenter.yaml
└── web-datacenter-service.yaml
```
Each file defines a specific Kubernetes resource needed to deploy and expose the web application.

## Resource Explanations
1. Persistent Volume (PV) — pv-datacenter.yaml

A PersistentVolume (PV) represents physical storage in the cluster.
It is manually created by the admin and can be claimed by pods through PVCs.
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-datacenter
spec:
  storageClassName: manual
  capacity:
    storage: 4Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /mnt/finance
```
### Key Points:
  - storageClassName: manual — enables manual binding.
  - capacity: 4Gi — defines total available storage.
  - accessModes: ReadWriteOnce — allows one node to read/write at a time.
  - hostPath: /mnt/finance — uses the host node’s directory for persistence.

2. Persistent Volume Claim (PVC) — pvc-datacenter.yaml

A PersistentVolumeClaim (PVC) requests storage from the PV.
When the specs (storage class, access mode, capacity) match, the claim binds automatically.
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-datacenter
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 3Gi
```
### Key Points:
  - Requests: 3Gi from the PV’s 4Gi total.
  - Ensures pods get reliable and persistent storage access.

3. Pod — pod-datacenter.yaml

The Pod runs a single container using the httpd:latest image.
It mounts the PVC to Apache’s document root, so files stored persist across pod restarts.
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-datacenter
  labels:
    app: datacenter
spec:
  containers:
    - name: container-datacenter
      image: httpd:latest
      ports:
        - containerPort: 80
      volumeMounts:
        - mountPath: /usr/local/apache2/htdocs
          name: datacenter-storage
  volumes:
    - name: datacenter-storage
      persistentVolumeClaim:
        claimName: pvc-datacenter
```
### Key Points:
  - Container Image: httpd:latest (Apache Web Server).
  - Mount Path: /usr/local/apache2/htdocs — the default web root for Apache.
  - Volume: Uses the pvc-datacenter claim for persistent storage.
  - Labels: The pod is labeled app: datacenter for the service selector.

4. Service — web-datacenter-service.yaml

A Service exposes the pod to external users.
Here, a NodePort type service opens port 30008 on each cluster node.
```yaml
apiVersion: v1
kind: Service
metadata:
  name: web-datacenter
spec:
  type: NodePort
  selector:
    app: datacenter
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30008
```
### Key Points:
  - Type: NodePort — makes the app accessible from outside the cluster.
  - Selector: Matches pods labeled app: datacenter.
  - nodePort: 30008 — accessible via <NodeIP>:30008.

## Deployment Steps
1. Apply all configurations
```bash
kubectl apply -f pv-datacenter.yaml
kubectl apply -f pvc-datacenter.yaml
kubectl apply -f pod-datacenter.yaml
kubectl apply -f web-datacenter-service.yaml
```

2. Check resource status
```bash
kubectl get pv,pvc,pod,svc
```

3. Verify bindings
  - Ensure pvc-datacenter is Bound to pv-datacenter.
  - Pod should show STATUS: Running.

4. Access the web app
  -  Get the Node IP:
```bash
kubectl get nodes -o wide
```
Open the app in your browser:
```bash
http://<NodeIP>:30008
```
<img width="615" height="228" alt="Screenshot 2025-10-20 at 10 08 38 PM" src="https://github.com/user-attachments/assets/3a67cbbb-668a-4e1a-bb51-ae42fd5808eb" />

<img width="1447" height="685" alt="Screenshot 2025-10-20 at 10 08 54 PM" src="https://github.com/user-attachments/assets/35cefa6b-3473-42c6-99db-0aacd825cc81" />

### How It Works (Flow Diagram)
```scss
User (Browser)
      │
      ▼
Service (web-datacenter, NodePort 30008)
      │
      ▼
Pod (pod-datacenter running httpd)
      │
      ▼
PVC (pvc-datacenter)
      │
      ▼
PV (pv-datacenter at /mnt/finance)
```
This flow ensures your web server data is stored persistently and accessible externally.

## Cleanup
To remove all resources created:
```bash
kubectl delete -f web-datacenter-service.yaml
kubectl delete -f pod-datacenter.yaml
kubectl delete -f pvc-datacenter.yaml
kubectl delete -f pv-datacenter.yaml
```
## Conclusion
This setup demonstrates how to:
  - Use Persistent Volumes and Claims for reliable storage.
  - Deploy a Pod running a web server using persistent data.
  - Expose the application externally via a NodePort Service.

You can easily extend this configuration for more complex deployments — such as using Deployments, ConfigMaps, or Ingress for production environments.

