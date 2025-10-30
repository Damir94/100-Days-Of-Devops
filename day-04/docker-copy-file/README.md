# Secure File Transfer to ubuntu_latest Container on Application Server 3

## Overview
The Nautilus DevOps team stores confidential data on Application Server 3 in the Stratos Datacenter.
A running container named ubuntu_latest required access to an encrypted file for internal testing.
To ensure security, the encrypted file /tmp/nautilus.txt.gpg was securely copied from the Docker host to the container without modification.

## Objective
  - Copy the encrypted file /tmp/nautilus.txt.gpg from the host to the ubuntu_latest container.
  - The destination path inside the container should be /tmp/.
  - Ensure that the file remains unaltered during transfer.

## Environment Details
  - Server: Application Server 3
  - Container Engine: Docker
  - Container Name: ubuntu_latest
  - Source File (on host): /tmp/nautilus.txt.gpg
  - Destination (inside container): /tmp/nautilus.txt.gpg

## Implementation Steps
1. Connect to Application Server 3
Access the target system from the jump host:
```bash
ssh tony@stapp03
```
2. Verify the Container is Running
Confirm that the ubuntu_latest container is active:
```bash
sudo docker ps
```
Example:
```mathrmatica
CONTAINER ID   IMAGE          COMMAND       STATUS         NAMES
a1b2c3d4e5f6   ubuntu:latest  "/bin/bash"   Up 10 minutes  ubuntu_latest
```
3. Confirm the Encrypted File Exists on Host
Check for the presence of the file before copying:
```bash
ls -l /tmp/nautilus.txt.gpg
```
You should see a valid file path and size output.

4. Copy the File into the Container
Use the docker cp command to copy the file securely:
```bash
sudo docker cp /tmp/nautilus.txt.gpg ubuntu_latest:/tmp/
```
This method ensures the file is transferred as-is, without changing its contents or timestamps.

5. Verify the File Inside the Container
Confirm successful transfer:
```bash
sudo docker exec ubuntu_latest ls -l /tmp/nautilus.txt.gpg
```
Output should show the file present inside /tmp/.

## Validation of File Integrity
To confirm the file was not modified:
On the host:
```bash
sha256sum /tmp/nautilus.txt.gpg
```
Inside the container:
```bash
sudo docker exec ubuntu_latest sha256sum /tmp/nautilus.txt.gpg
```
Both checksums should match ✅ — confirming that the file remained unchanged.

## Conclusion
The encrypted file nautilus.txt.gpg was securely copied from the host system into the ubuntu_latest container’s /tmp/ directory on Application Server 3.
This process ensured data integrity, no file modification, and successful secure transfer within the Docker environment.
