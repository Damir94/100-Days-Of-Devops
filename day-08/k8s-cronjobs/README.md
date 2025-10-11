# Kubernetes CronJob: Nautilus Task

## Overview

The Nautilus DevOps team is implementing recurring automation within their Kubernetes cluster. 
To start, the team created a CronJob resource that executes a simple placeholder command on a scheduled interval. 
This setup lays the groundwork for future automated scripts that will handle real operational tasks.

## Objective
Create a Kubernetes CronJob named nautilus that runs periodically and executes a dummy command using an Nginx container.

## Configuration Details
| Field                            | Description                                      |
| -------------------------------- | ------------------------------------------------ |
| `apiVersion: batch/v1`           | Defines this as a CronJob resource.              |
| `metadata.name: nautilus`        | The name of the CronJob.                         |
| `schedule: "*/4 * * * *"`        | Runs the job every 4 minutes.                    |
| `containers.name: cron-nautilus` | Name of the container running in each job pod.   |
| `image: nginx:latest`            | Uses the latest Nginx image.                     |
| `command`                        | Executes a simple echo command as a placeholder. |
| `restartPolicy: OnFailure`       | Pods will restart only if they fail.             |



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

