# 100 Days of DevOps - Day 40: Apache Setup in kkloud Container

## Overview
This document describes the steps performed to configure and run Apache2 inside the kkloud container on App Server 3 in the Stratos Datacenter.
The main requirements were:

 - Install Apache2 inside the container.
 - Configure Apache to listen on port 8088 instead of the default port 80.
 - Ensure the service runs successfully and the container remains up.

## Steps Completed

### 1. Accessing the Container

Logged into App Server 3 and attached to the kkloud container:
```bash
  ssh tony@stapp03
  docker exec -it kkloud bash
```

### 2. Installing Apache2

Inside the container, updated package lists and installed Apache2:
```bash
  apt-get update -y
  apt-get install apache2 -y
```

## 3. Configuring Apache to Listen on Port 8088

Updated Apache configuration files to use port 8088.

Modified ports.conf:
```bash
  sed -i 's/Listen 80/Listen 8088/' /etc/apache2/ports.conf
```
Modified 000-default.conf:
```bash
  sed -i 's/<VirtualHost \*:80>/<VirtualHost *:8088>/' /etc/apache2/sites-available/000-default.conf
```

### 4. Restarting and Verifying Apache

Restarted the Apache service:
```bash
  service apache2 restart
  service apache2 status
```

5. Verification

Confirmed that Apache responds on port 8088:
```bash
  curl http://localhost:8088
```
Output displayed the Apache2 Default Page, confirming success. 

## Results

 - Apache2 is installed and configured.
 - Service is running on port 8088.
 - Container remains in the running state.

## Notes

- We used sed instead of vi since the container didn’t have text editors pre-installed.
- netstat was unavailable, so we verified using curl.
- The container will serve HTTP requests on port 8088 across all interfaces (localhost, 127.0.0.1, container IP).
