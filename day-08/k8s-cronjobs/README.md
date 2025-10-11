# Kubernetes CronJob: Nautilus Task

## Overview

The Nautilus DevOps team is implementing recurring automation within their Kubernetes cluster. 
To start, the team created a CronJob resource that executes a simple placeholder command on a scheduled interval. 
This setup lays the groundwork for future automated scripts that will handle real operational tasks.

## Objective
Create a Kubernetes CronJob named nautilus that runs periodically and executes a dummy command using an Nginx container.

## Configuration Details
| Parameter          | Description                          |
| ------------------ | ------------------------------------ |
| **Resource Type**  | CronJob                              |
| **Name**           | `nautilus`                           |
| **Container Name** | `cron-nautilus`                      |
| **Image**          | `nginx:latest`                       |
| **Schedule**       | `*/4 * * * *` (runs every 4 minutes) |
| **Command**        | `echo Welcome to xfusioncorp!`       |
| **Restart Policy** | `OnFailure`                          |


## YAML Manifest
```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: nautilus
spec:
  schedule: "*/4 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: cron-nautilus
            image: nginx:latest
            command: ["echo", "Welcome to xfusioncorp!"]
          restartPolicy: OnFailure
```

## Deployment Steps

1. Save the manifest file
```bash
vi cronjob-nautilus.yaml
```
Paste the above YAML content and save the file.

2. Apply the CronJob
```bash
kubectl apply -f cronjob-nautilus.yaml
```

3. Verify CronJob creation
```bash
kubectl get cronjob
```
4. Check if jobs are scheduled
After a few minutes, you can check:
```bash
kubectl get jobs
kubectl get pods
```

5. View logs
Once a pod runs, check the output:
```bash
kubectl logs <pod-name>
```

You should see:
```css
Welcome to xfusioncorp!
```
## Notes
 - The schedule */4 * * * * triggers the job every 4 minutes.
 - The OnFailure restart policy ensures pods only restart if they fail.
 - This CronJob currently uses a placeholder command but can easily be extended to run real scripts or maintenance tasks in the future.

## Summary
This task demonstrates how to configure a Kubernetes CronJob to execute recurring workloads. 
The nautilus CronJob will automatically create and run pods every 4 minutes, ensuring reliable and 
repeatable execution of commands — a foundational step for future automation in the Nautilus infrastructure.
