# 100 Days of DevOps - Day 37: Secure File Transfer from Docker Host to Container

## Overview

As part of the Nautilus DevOps team’s operations, we needed to securely transfer an encrypted file 
from the Docker host to a running container without altering its contents. 
This document outlines the steps performed to copy the file and validate its integrity.

## Task

 - Source File: /tmp/nautilus.txt.gpg (encrypted)

 - Docker Host: App Server 3 (Stratos Datacenter)

 - Target Container: ubuntu_latest

 - Destination Path: /opt/ inside the container

The requirement was to ensure the file was copied as-is, without any modification.

## Steps Performed

1. Confirm Container Status

First, verify that the target container is running:
```bash
  docker ps | grep ubuntu_latest
```
2. Copy File into Container

Use the docker cp command to transfer the encrypted file:
```bash
  docker cp /tmp/nautilus.txt.gpg ubuntu_latest:/opt/
```

This command securely copies the file from the host into the container’s /opt/ directory.

3. Verify Inside Container

Access the container:
```bash
  docker exec -it ubuntu_latest bash
```

Check the file presence:
```bash
  ls -l /opt/nautilus.txt.gpg
```
The file should now be available at the target location.

## Outcome

The encrypted file was successfully copied from the Docker host to the ubuntu_latest container at /opt/ with no modification. Integrity was verified using checksum validation.

## Key Takeaways

 - docker cp provides a simple and reliable way to copy files between a host and a container.

 - Using checksum validation ensures the file remains unaltered during the transfer.

 - Following these steps maintains the confidentiality and integrity of sensitive files in containerized environments.
