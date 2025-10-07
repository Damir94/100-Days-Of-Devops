# 100 Days of DevOps - Day 44: Hosting Static Website with Apache HTTPD using Docker Compose

## Overview
This project demonstrates how to host a static website using the Apache HTTPD web server inside a Docker container, managed through Docker Compose.
The static content is shared by the Nautilus application development team and stored on the host system.

We deployed this setup on App Server 3 in Stratos DC following the given infrastructure guidelines.

## Objectives
 - Create a containerized HTTPD web server using Docker Compose.
 - Serve static website files stored in /opt/finance directory.
 - Expose the web server on host port 8084.
 - Ensure persistent website data using Docker volume mapping.

## Directory Structure
```bash
/opt/
├── docker/
│   └── docker-compose.yml      # Docker Compose configuration file
└── finance/                    # Static website files shared by the dev team
```

## Docker Compose Configuration
File: /opt/docker/docker-compose.yml
```bash
version: '3'
services:
  web:
    image: httpd:latest
    container_name: httpd
    ports:
      - "8084:80"
    volumes:
      - /opt/finance:/usr/local/apache2/htdocs

```

## Explanation

| Directive          | Description                                                                                                                             |
| ------------------ | --------------------------------------------------------------------------------------------------------------------------------------- |
| **image**          | Uses the latest official `httpd` (Apache) image from Docker Hub.                                                                        |
| **container_name** | Names the container as `httpd` for easy identification.                                                                                 |
| **ports**          | Maps host port `8084` to container port `80`, allowing access via `http://<host_ip>:8084`.                                              |
| **volumes**        | Mounts the host directory `/opt/finance` into the container’s web root `/usr/local/apache2/htdocs`, serving static site files directly. |


## Deployment Steps

### 1. Create the Docker directory
```bash
sudo mkdir -p /opt/docker
cd /opt/docker
```

### 2. Create the Docker Compose file
```bash
sudo vi docker-compose.yml
```
Paste the YAML content shown above.

### 3.Launch the container
If Docker Compose is built into Docker (recommended):
```bash
sudo docker compose -f /opt/docker/docker-compose.yml up -d
```
If using standalone Compose:
```bash
sudo docker-compose -f /opt/docker/docker-compose.yml up -d
```
### 4.Verify the running container
```bash
sudo docker ps
```

### 5.Test the setup
Access the static website via:
```bash
http://<server-ip>:8084
```
or test locally:
```bash
curl http://localhost:8084
```

<img width="1774" height="580" alt="Screenshot 2025-10-07 at 9 37 40 AM" src="https://github.com/user-attachments/assets/2639ba76-2f0b-4563-9d74-6395733e96f3" />


## Auto Start on System Reboot

To ensure the container starts automatically after a reboot, run:
```bash
sudo docker update --restart unless-stopped httpd
```

## Verification Checklist
| Task                                                | Status |
| --------------------------------------------------- | ------ |
| Created `/opt/docker/docker-compose.yml`            | ✅      |
| Used `httpd:latest` image                           | ✅      |
| Container named `httpd`                             | ✅      |
| Port `8084` → `80` mapping                          | ✅      |
| Volume `/opt/finance` → `/usr/local/apache2/htdocs` | ✅      |
| Verified container runs successfully                | ✅      |


## Summary

We successfully deployed an Apache HTTPD container using Docker Compose that serves the Nautilus team's static website content.
The setup ensures:

 - Port mapping for external access (8084 → 80)
 - Persistent storage through volume mapping
 - Simple one-command deployment and management via Docker Compose


