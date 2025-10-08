# 100 Days of DevOps - Day 45: Building a Custom Apache HTTPD Docker Image with SSL Support

## Overview

This project documents the steps taken by the Nautilus DevOps team to fix and successfully build a custom Apache HTTPD Docker image as per the development team’s requirements.
The image enables HTTP traffic on a custom port and supports SSL (HTTPS) by enabling required modules and copying SSL certificates into the container.
All work was performed on App Server 3 in Stratos DC.

## Objectives

 - Fix Dockerfile build errors to ensure a successful image build.
 - Preserve the original base image (httpd:2.4.43) and configuration logic.
 - Enable HTTPS support by activating required Apache modules.
 - Configure the Apache web server to listen on port 8080 instead of 80.
 - Copy the provided SSL certificates (server.crt and server.key) into the image.

## Project Structure
```pgsql
/opt/docker/
├── Dockerfile
├── certs/
│   ├── server.crt
│   └── server.key
└── index.html   # (if provided for testing)
```

## Fixed Dockerfile
File: /opt/docker/Dockerfile
```bash
FROM httpd:2.4.43

# Change Listen port from 80 to 8080
RUN sed -i "s/Listen 80/Listen 8080/g" /usr/local/apache2/conf/httpd.conf

# Enable SSL modules
RUN sed -i '/LoadModule ssl_module modules\/mod_ssl.so/s/^#//g' /usr/local/apache2/conf/httpd.conf
RUN sed -i '/LoadModule socache_shmcb_module modules\/mod_socache_shmcb.so/s/^#//g' /usr/local/apache2/conf/httpd.conf

# Include SSL configuration
RUN sed -i '/Include conf\/extra\/httpd-ssl.conf/s/^#//g' /usr/local/apache2/conf/httpd.conf

# Copy SSL certificates into container
COPY certs/server.crt /usr/local/apache2/conf/server.crt
COPY certs/server.key /usr/local/apache2/conf/server.key
```
## Key Fixes Made

| Issue                                               | Root Cause                                                                    | Fix                                                             |
| --------------------------------------------------- | ----------------------------------------------------------------------------- | --------------------------------------------------------------- |
| ❌ Build failed due to `unexpected end of statement` | `ADD` was incorrectly used to run shell commands                              | ✅ Replaced `ADD` with `RUN` for all shell operations            |
| ❌ Config file path invalid (`conf.d/httpd.conf`)    | The correct path in the `httpd` image is `/usr/local/apache2/conf/httpd.conf` | ✅ Updated all `sed` commands to reference the correct file path |
| ❌ SSL modules not loading                           | Lines were commented out in default config                                    | ✅ Used `sed` to uncomment SSL-related modules                   |
| ✅ Final build verification                          | Successfully built and tagged as `custom-httpd:latest`                        | ✅ Image built and verified using `docker build`                 |

## Build Instructions

### 1. Navigate to the project directory
```bash
  cd /opt/docker
```

### 2. Build the image
```bash
sudo docker build -t custom-httpd:latest .
```

### 3. Run the container
```bash
sudo docker run -d -p 8080:8080 --name httpd-ssl custom-httpd:latest
```

### 4. Verify it’s running
```bash
sduo docker ps
```

### 5. Test HTTP access
```bash
curl http://localhost:8080
```

## Summary

The Nautilus DevOps team successfully debugged and fixed a Dockerfile that was previously failing to build due to incorrect syntax and invalid file paths.
The final image:

 - Uses httpd:2.4.43 as its base
 - Listens on port 8080
 - Enables SSL (HTTPS) support
 - Copies and configures valid SSL certificates

This configuration ensures a secure and flexible Apache environment suitable for both HTTP and HTTPS access.

