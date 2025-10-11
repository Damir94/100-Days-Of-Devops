# Kubernetes Job Template — countdown-xfusion

## Overview

The Nautilus DevOps team is experimenting with Kubernetes Jobs to test task automation in their cluster. 
As part of this setup, we created a simple Job template that runs a dummy command using an Ubuntu container. 
This helps ensure that our Job configurations and 
cluster behavior work correctly before adding real workloads.

## Task Details
We created a Kubernetes Job with the following specifications:
 - Job Name: countdown-xfusion
 - Pod Template Name: countdown-xfusion
 - Container Name: container-countdown-xfusion
 - Image Used: ubuntu:latest
 - Command: sleep 5
 - Restart Policy: Never

This job simply runs a short command (sleep 5) and exits successfully — serving as a placeholder for future automated scripts.

## YAML Manifest
Below is the manifest used to create the Job:
```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: countdown-xfusion
spec:
  template:
    metadata:
      name: countdown-xfusion
    spec:
      containers:
        - name: container-countdown-xfusion
          image: ubuntu:latest
          command: ["sleep", "5"]
      restartPolicy: Never
```

## Deployment Steps

1. Create the Job
Save the YAML file as countdown-xfusion-job.yaml and apply it:
```bash
kubectl apply -f countdown-xfusion-job.yaml
```

2. Verify the Job
```bash
kubectl get jobs
kubectl get pods
```

3. Check Logs
Once the pod completes, you can view its logs:
```bash
kubectl logs <pod-name>
```

## Expected Outcome
The Job will create a pod that runs the command sleep 5, waits for 5 seconds, and then completes successfully. 
This confirms that the Job setup and execution flow in the cluster are functioning as expected.


