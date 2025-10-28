# 100 Days of Devops - Day 65: Deploying Redis on Kubernetes — Setup and Configuration Guide

## Overview
The Nautilus application development team observed performance issues due to frequent database queries. 
To improve performance, the team decided to introduce an in-memory caching layer using Redis.
This guide documents the steps we followed to deploy a Redis instance on a Kubernetes cluster for testing purposes.
The configuration ensures Redis runs efficiently with minimal resource usage, proper volume management, and Kubernetes best practices.

## Objectives
We were tasked to:
1. Create a ConfigMap containing Redis configuration.
2. Deploy a Redis Deployment using the redis:alpine image.
3. Configure CPU requests and volume mounts.
4. Expose the Redis default port 6379.
5. Verify that the Redis pod is running successfully.

### Step 1: Create the ConfigMap
We first created a ConfigMap to define Redis memory limits.
🧾 YAML Manifest — redis-configmap.yaml
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-redis-config
data:
  redis-config: |
    maxmemory 2mb
```
#### Explanation
  - apiVersion & kind: Defines that this is a Kubernetes ConfigMap resource.
  - metadata.name: Gives the ConfigMap a name (my-redis-config) to reference later.
  - data: Contains key-value pairs. Here, the key redis-config holds Redis configuration content.
  - The command maxmemory 2mb tells Redis to use a maximum of 2MB memory for data caching.
  - This ConfigMap will later be mounted into the Redis container, so Redis reads it as its configuration file.

### Step 2: Create the Redis Deployment
The next step was to create a Deployment to run Redis inside the cluster.
🧾 YAML Manifest — redis-deployment.yaml
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      labels:
        app: redis
    spec:
      containers:
      - name: redis-container
        image: redis:alpine
        ports:
        - containerPort: 6379
        resources:
          requests:
            cpu: "1"
        volumeMounts:
        - name: data
          mountPath: /redis-master-data
        - name: redis-config
          mountPath: /redis-master
      volumes:
      - name: data
        emptyDir: {}
      - name: redis-config
        configMap:
          name: my-redis-config
          items:
            - key: redis-config
              path: redis.conf
```
### YAML Breakdown and Explanation
#### Deployment Structure
  - apiVersion & kind: This is an apps/v1 Deployment.
  - metadata.name: Sets the name of the Deployment as redis-deployment.
  - spec.replicas: Runs only one replica of Redis (for testing phase).

#### Selector and Labels
  - selector.matchLabels: Matches pods with label app: redis.
  - template.metadata.labels: Defines the same label in the pod template, ensuring that the Deployment manages these pods.

#### Container Section
  - name: Container name is redis-container.
  - image: Uses lightweight redis:alpine image.
  - ports: Exposes Redis’s default port 6379 for internal communication.
  - resources.requests.cpu: Requests 1 CPU to ensure consistent performance.
  - volumeMounts:
    - Mounts two volumes:
      - /redis-master-data — for temporary Redis data storage.
      - /redis-master — where the Redis config file from ConfigMap will be placed.

#### Volumes Section
  - data (EmptyDir): Provides an ephemeral storage volume for Redis runtime data. This is automatically wiped when the pod stops.
  - redis-config (ConfigMap):
      - Mounts the content of my-redis-config ConfigMap.
      - The items field maps the key redis-config to a file called redis.conf inside /redis-master.
Redis will read this redis.conf file automatically when it starts.

### Step 3: Apply and Verify
Run the following commands to deploy everything:
```bash
kubectl apply -f redis-configmap.yaml
kubectl apply -f redis-deployment.yaml
```
#### Verification
Check the pod status:
```bash
kubectl get pods
```
You should see something like:
```sql
NAME                                 READY   STATUS    RESTARTS   AGE
redis-deployment-7f77fbb48d-kskpt    1/1     Running   0          30s
```
To verify Redis is working correctly:
```bash
kubectl exec -it <redis-pod-name> -- redis-cli ping
```
Expected output:
```nginx
PONG
```
<img width="1615" height="544" alt="Screenshot 2025-10-24 at 8 02 49 AM" src="https://github.com/user-attachments/assets/f00a1979-1f11-4ad7-a2ba-16e2efe62986" />

###(Optional) Expose Redis with a Service
If other pods (like your application) need to access Redis, create a Service:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: redis-service
spec:
  selector:
    app: redis
  ports:
  - port: 6379
    targetPort: 6379
```
Apply with:
```bash
kubectl apply -f redis-service.yaml
```
### Summary
| Resource Type      | Name               | Description                                   |
| ------------------ | ------------------ | --------------------------------------------- |
| ConfigMap          | `my-redis-config`  | Stores Redis memory configuration             |
| Deployment         | `redis-deployment` | Runs Redis container using redis:alpine image |
| Volume (EmptyDir)  | `data`             | Provides temporary storage                    |
| Volume (ConfigMap) | `redis-config`     | Mounts configuration file into container      |
| Port               | `6379`             | Exposes Redis service port                    |


## Key Takeaways
  - ConfigMaps are ideal for externalizing configuration.
  - EmptyDir volumes are useful for temporary, per-pod storage.
  - Using resource requests ensures pods get the required CPU share
  - Redis as an in-memory cache can drastically improve read performance by reducing database load.
