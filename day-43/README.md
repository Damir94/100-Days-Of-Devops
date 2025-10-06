# 100 Days of DevOps - Day 43: Nginx Container Setup — Application Server 2

## Overview
This document describes the steps performed by the Nautilus DevOps team to deploy an Nginx-based container on Application Server 2 in the Stratos Datacenter.
The goal was to host a simple application using the nginx:stable Docker image, ensuring that the container remains running and accessible through a mapped host port.

## Task Objectives
 - Pull the nginx:stable Docker image.
 - Create a container named blog using the pulled image.
 - Map host port 8083 to container port 80.
 - Keep the container in a running state and verify accessibility.

## Steps Performed

### 1. Connect to Application Server 2
```bash
  ssh tony@stapp02
```
Replace the username if different in your environment.

### 2. Pull Nginx Stable Image
```bash
  sudo docker pull nginx:stable
```
This downloads the latest stable version of the Nginx image from Docker Hub.
To confirm:
```bash
 sudo docker images
```

### 3. Create and Run the Container
```bash
  sudo docker run -d --name blog -p 8083:80 nginx:stable
```

Command breakdown:

-d → Runs container in detached (background) mode

--name blog → Assigns the container name blog

-p 8083:80 → Maps host port 8083 to container port 80

nginx:stable → Specifies the image version

### 4. Verify Container Status
```bash
  sudo docker ps
```

### 5. Test Application Accessibility
```bash
  curl http://localhost:8083
```

<img width="1758" height="861" alt="Screenshot 2025-10-06 at 9 02 06 AM" src="https://github.com/user-attachments/assets/a88f9ef6-6948-4298-885a-afa11a07b457" />

