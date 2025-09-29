# 100 Days of DevOps - Day 36: Deploying Nginx Container on Application Server 2

## Overview

The Nautilus DevOps team is conducting application deployment tests across selected servers. As part of this process, we are tasked with deploying an Nginx container on Application Server 2.

This guide documents the steps taken to complete the deployment using the nginx:alpine image.

## Steps Performed

1. Connect to Application Server 2
   First, log in to the target server
   ```bash
       ssh user@app_server_2
   ```

2. Deploy the Nginx Container

Run the container with the following parameters:

 - Image: nginx:alpine
 - Container Name: nginx_2
 - Mode: Detached (-d)
```bash
    docker run -d --name nginx_2 nginx:alpine
```

3. Verify Container Status

Check that the container is running:
```bash
    docker ps | grep nginx_2
```

## Results
 - A container named nginx_2 is successfully created and running.
 - The container uses the nginx:alpine image for lightweight and efficient deployment.
 - Nginx is running inside the container and listening on port 80/tcp by default.

## Notes

For external access, you may map container port 80 to a host port using:
```bash
    docker run -d --name nginx_2 -p 8080:80 nginx:alpine
```

Then access via: http://<server_ip>:8080
This deployment ensures a clean, lightweight Nginx setup suitable for testing.
