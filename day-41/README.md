# 100 Days of DevOps - Day 41: Custom Apache Docker Image Setup for Nautilus Application

## Overview
As per the recent requirements shared by the Nautilus Application Development Team, the DevOps Team was tasked with creating a custom Docker image to support initial testing.
This image is designed to run Apache2 on port 8082, using Ubuntu 24.04 as the base image.

The following documentation explains how this Docker image was created, what it includes, and how to use it.

### Project Details

Location of Dockerfile:
 - /opt/docker/Dockerfile

Base Image:
 - ubuntu:24.04

Installed Packages:
 - apache2

Configured Port:
 - Apache runs on port 8082 (instead of the default 80)

### Dockerfile Breakdown

Below is the explanation of each step in the Dockerfile:
```bash
  # Use ubuntu:24.04 as the base image
  FROM ubuntu:24.04
```
This line specifies the base image for our container — the latest stable Ubuntu version.
```bash
# Install apache2
RUN apt-get update && \
    apt-get install -y apache2 && \
    apt-get clean
```
We update the package list, install Apache2, and clean up unnecessary cached files to reduce the image size.
```bash
# Change Apache to listen on port 8082
RUN sed -i 's/80/8082/' /etc/apache2/ports.conf && \
    sed -i 's/:80/:8082/' /etc/apache2/sites-enabled/000-default.conf
```
The default Apache port (80) is replaced with port 8082 to meet the project’s requirement.
```bash
# Expose port 8082
EXPOSE 8082
```
This tells Docker that the container will listen on port 8082.

```bash
# Start Apache in the foreground
CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]
```
This ensures that Apache runs in the foreground when the container starts (keeping the container alive).

### How to Build the Image

Navigate to the Dockerfile directory:
```bash
cd /opt/docker
```

### Build the image:
```bash
sudo docker build -t custom-apache:latest .
```
### How to Run the Container

Run the container using:
```bash
sudo docker run -d -p 8082:8082 custom-apache:latest
```

This maps port 8082 on the host to port 8082 in the container.

### Testing the Setup

To confirm Apache is running correctly, open your browser or use curl:
```bash
curl http://localhost:8082
```

<img width="1801" height="862" alt="Screenshot 2025-10-04 at 8 36 35 AM" src="https://github.com/user-attachments/assets/9cb33515-2c39-418a-a907-2b7f2ba3f11a" />

You should see the default Apache2 Ubuntu page.

### Notes

The document root and other Apache settings remain unchanged.

Only the listening port has been modified from 80 to 8082.

This image can be used as a base for further application testing by the Nautilus team.

