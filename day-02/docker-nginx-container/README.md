
# Nginx Container Deployment on Application Server 3
## Overview
The Nautilus DevOps team is conducting application deployment tests on selected application servers.
As part of this testing phase, we deployed an Nginx container on Application Server 3 using the lightweight Alpine-based image.
This document explains the steps taken to create, run, and verify the Nginx container.

## Objective
Deploy and run an Nginx container on Application Server 3 with the following requirements:
  - Container name: nginx_3
  - Image: nginx:alpine
  - Container state: Running

## Environment Details
  - Server: Application Server 3
  - Base OS: Linux
  - Container Engine: Docker
  - Network: Connected through jump host (jump_host → App Server 3)

## Deployment Steps
1. Connect to the Target Server
From the jump host, access Application Server 3:
```bash
ssh tony@stapp03
```
(Username may vary depending on environment setup.)

2. Pull the Nginx Image
Fetch the nginx:alpine image from Docker Hub:
```bash
sudo docker pull nginx:alpine
```
This downloads the lightweight Nginx web server image optimized for Alpine Linux.

3. Create and Run the Container
Start the container in detached mode:
```bash
sudo docker run -d --name nginx_3 nginx:alpine
```
  - -d → Runs the container in background (detached mode).
  - --name nginx_3 → Assigns a specific name for easy management.
  - nginx:alpine → Uses the official lightweight image.

4. Verify Container Status
Confirm that the container is running:
```bash
sudo docker ps
```
Expected output:
```mathematica
CONTAINER ID   IMAGE          COMMAND                  STATUS         NAMES
a1b2c3d4e5f6   nginx:alpine   "/docker-entrypoint.…"   Up X minutes   nginx_3
```
## Validation
To verify the Nginx web server is working:
```bash
sudo docker exec -it nginx_3 nginx -v
```
You should see the installed version of Nginx.

