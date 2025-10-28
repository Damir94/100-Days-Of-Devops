# Docker and Docker Compose Setup on App Server 3

## Overview
The Nautilus DevOps team recently initiated a plan to containerize multiple applications to improve deployment speed, scalability, and consistency across environments.
As a first step in this process, Docker and Docker Compose were installed and configured on App Server 3 to serve as the base environment for containerization testing.

## Objectives
  - The goal of this setup was to:
  - Install and configure Docker CE (Community Edition) on App Server 3.
  - Install Docker Compose for defining and managing multi-container applications.
  - Start and enable the Docker service to ensure containers can run persistently.
  - Verify the installation by running a test container.

## Environment Details
  - Server: App Server 3
  - OS: CentOS (Linux-based)
  - Tools Installed:
    - Docker CE
    - Docker Compose

## Installation Steps
1. Update Packages and Install Prerequisites
Before installing Docker, we ensured all necessary dependencies were in place:
```bash
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```
2. Add Docker Repository
We added Docker’s official repository to the YUM package manager:
```bash
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```
3. Install Docker CE
Using YUM, we installed the Docker engine, CLI, and container runtime:
```bash
sudo yum install -y docker-ce docker-ce-cli containerd.io
```
4. Install Docker Compose
Docker Compose simplifies running multi-container applications. We downloaded the latest binary and made it executable:
```bash
sudo curl -L "https://github.com/docker/compose/releases/download/2.27.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```
Verify installation:
```bash
docker-compose version
```      
5. Start and Enable Docker Service
We started the Docker service and configured it to start automatically on system boot:
```bash
sudo systemctl start docker
sudo systemctl enable docker
```
To confirm Docker is running:
```bash
sudo systemctl status docker
```
6. Verify Docker Installation
To ensure everything works correctly, we ran the hello-world test container:
```bash
sudo docker run hello-world
```
If the output shows a “Hello from Docker!” message, the setup is successful.

## Outcome
Docker and Docker Compose were successfully installed and configured on App Server 3.
This setup provides a solid foundation for the Nautilus DevOps team to begin containerizing their application stack and conducting integration tests.
