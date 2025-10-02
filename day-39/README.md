# 100 Days of DevOps - Day 39: Creating Docker Image from Running Container

## Task Overview

The Nautilus developers requested the DevOps team to create a new Docker image from an existing running container. This ensures that the developer’s changes inside the container are preserved as a reusable image.

## Task Details

 - Source Container: ubuntu_latest (running on Application Server 1)

 - Target Image: news:nautilus

## Steps Performed

### 1. Verified Running Container
Checked the running containers to confirm that ubuntu_latest was active:
```bash
  docker ps
```

### 2. Created New Image from Container
Used the docker commit command to create a new image from the container:
```bash
  docker commit ubuntu_latest news:nautilus
```

### 3. Verified the New Image
Listed Docker images to ensure the new image was created successfully:
```bash
  docker images
```

## Result

A new Docker image named news:nautilus was successfully created from the running container ubuntu_latest on Application Server 2. 
This image can now be used to run new containers with the developer’s changes preserved.


