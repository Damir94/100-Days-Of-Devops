# 100 Days of Devops - Day 46:  Docker Compose Setup for PHP + MariaDB Application

## Overview
The Nautilus Application Development and DevOps teams are testing their new application in a containerized environment before going live. 
This setup uses Docker Compose to deploy a two-service stack — one for the web (PHP + Apache) server and another for the database (MariaDB).

All configuration is defined in a single Compose file located at:
```bash
/opt/devops/docker-compose.yml
```

## Services Overview

1. Web Service (web)
This service hosts the PHP-based application using the php:apache image.

Configuration:
 - Container name: php_host
 - Image: php:apache (or any valid PHP + Apache tag)
 - Ports: Host port 6000 → Container port 80
 - Volumes: Host path /var/www/html → Container path /var/www/html
Purpose:
This container serves the application files over HTTP on port 6000.

2. Database Service (db)
This service provides the backend database using the MariaDB image.

Configuration:

 - Container name: mysql_host
 - Image: mariadb:latest
 - Ports: Host port 3306 → Container port 3306
 - Volumes: Host path /var/lib/mysql → Container path /var/lib/mysql
 - Environment Variables:
    - MYSQL_DATABASE: database_host
    - MYSQL_USER: dev_user
    - MYSQL_PASSWORD: StrongP@ss123
    - MYSQL_ROOT_PASSWORD: Root@123
Purpose:
This container provides a persistent and isolated MariaDB database for the web application.

## Docker Compose File
Path: /opt/devops/docker-compose.yml
```yaml
version: '3.8'

services:
  web:
    container_name: php_host
    image: php:apache
    ports:
      - "6000:80"
    volumes:
      - /var/www/html:/var/www/html
    depends_on:
      - db

  db:
    container_name: mysql_host
    image: mariadb:latest
    environment:
      MYSQL_DATABASE: database_host
      MYSQL_USER: dev_user
      MYSQL_PASSWORD: StrongP@ss123
      MYSQL_ROOT_PASSWORD: Root@123
    ports:
      - "3306:3306"
    volumes:
      - /var/lib/mysql:/var/lib/mysql
```

## How to Deploy
Navigate to the compose directory:
```bash
cd /opt/devops
```
Run the containers:
```bash
docker-compose up -d
```
Verify running containers:
```bash
docker ps
```
Test the application:
```bash
curl http://localhost:6000/
```

<img width="1771" height="885" alt="Screenshot 2025-10-08 at 8 12 57 PM" src="https://github.com/user-attachments/assets/b5eaa007-d4af-4237-b136-ed892d82a898" />

You should receive the default PHP/Apache response or your app’s homepage.

## To Stop and Clean Up
To stop the containers:
```bash
docker-compose down
```
To stop and remove all containers, networks, and volumes:
```bash
docker-compose down -v
```

## Summary

This setup provides a simple and reproducible way to run a PHP + MariaDB stack using Docker Compose.
It enables local testing and development in an environment that mirrors production deployment.
