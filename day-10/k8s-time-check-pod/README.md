# Kubernetes Time Check Pod — Logging Setup (Nautilus DevOps Project)

## Overview
The Nautilus DevOps team set up a Kubernetes pod for time-based logging. This setup helps track timestamps continuously, 
which is useful for system testing, monitoring intervals, and ensuring consistent logging behavior.
The solution was implemented in the datacenter namespace using a BusyBox container.

## Requirements
 - Pod Name: time-check
 - Namespace: datacenter
 - Container Name: time-check
 - Image: busybox:latest
 - ConfigMap: time-config with data TIME_FREQ=8
 - Command:
   ```bash
   while true; do date; sleep $TIME_FREQ; done
   ``` 
   → Logs should be written to /opt/data/time/time-check.log.
 - Volume: log-volume mounted at /opt/data/time
 - Environment Variable: TIME_FREQ (loaded from ConfigMap)

## Step-by-Step Implementation
1. Create the Namespace
```bash
kubectl create namespace datacenter
```

2. Create the ConfigMap
```bash
kubectl create configmap time-config \
  --from-literal=TIME_FREQ=8 \
  -n datacenter
```

3. Create the Pod Manifest (time-check-pod.yaml)
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: time-check
  namespace: datacenter
  labels:
    app: time-check
spec:
  containers:
    - name: time-check
      image: busybox:latest
      command: ["/bin/sh", "-c"]
      args:
        - while true; do date >> /opt/data/time/time-check.log; sleep $TIME_FREQ; done
      env:
        - name: TIME_FREQ
          valueFrom:
            configMapKeyRef:
              name: time-config
              key: TIME_FREQ
      volumeMounts:
        - name: log-volume
          mountPath: /opt/data/time
  volumes:
    - name: log-volume
      emptyDir: {}
```

4. Deploy the Pod
```bash
kubectl apply -f time-check-pod.yaml
```

6. Verify the Setup
Check if the pod is running:
```bash
kubectl get pods -n datacenter
```
Inspect the logs:
```bash
kubectl exec -n datacenter time-check -- tail -f /opt/data/time/time-check.log
```

## Result
 - The time-check pod continuously logs the current date and time every 8 seconds.
 - Logs are stored under /opt/data/time/time-check.log.
 - Configuration flexibility: update the logging interval via the ConfigMap without modifying the container image.

## Future Integration
This setup can be easily extended for:
- Centralized log aggregation.
- Cron-based log cleanup.
- Integration into production monitoring systems within the Kubernetes cluster.
