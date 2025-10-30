# Static Website Issue Investigation — nautilus Container on App Server 1

## Overview
The Nautilus DevOps team reported an issue with a static website running inside a container named nautilus on Application Server 1 in the Stratos Datacenter.
The goal of this task was to verify the container’s volume mapping and ensure that the website was correctly accessible through port 8080 on the host.

## Objective
  - Confirm that the container volume /usr/local/apache2/htdocs is correctly mapped to the host directory /var/www/html.
  - Verify that the website is reachable on host port 8080 and that the following command works on App Server 1:
  - curl http://localhost:8080/

## Environment Details
  - Server: Application Server 1
  - Container Engine: Docker
  - Container Name: nautilus
  - Image Used: httpd:latest
  - Host Volume: /var/www/html
  - Container Volume: /usr/local/apache2/htdocs
  - Host Port: 8080
  - Container Port: 80

## Investigation Steps
1. Connect to Application Server 1
Access the target system via the jump host:
```bash
ssh tony@stapp01
```
2. Check if the Container is Running
List all active containers:
```bash
sudo docker ps
```
Example output:
```nginx
CONTAINER ID   IMAGE          COMMAND              STATUS         PORTS                  NAMES
a1b2c3d4e5f6   httpd:latest   "httpd-foreground"   Up 8 minutes   0.0.0.0:8080->80/tcp   nautilus
```
Observation:
  - The container nautilus is running, and port 8080 is correctly mapped to container port 80.

3. Verify Volume Mapping
Inspect container details:
```bash
sudo docker inspect nautilus | grep -A 5 "Mounts"
```

Expected output:
```json
"Mounts": [
    {
        "Type": "bind",
        "Source": "/var/www/html",
        "Destination": "/usr/local/apache2/htdocs",
        "Mode": "",
        "RW": true
    }
]
```

Result:
  - Host directory /var/www/html is correctly bound to container path /usr/local/apache2/htdocs.

3. Test Website Accessibility
Check if the static site is accessible via port 8080:
```bash
curl http://localhost:8080/
```
Expected response:
```html
<html><body><h1>It works!</h1></body></html>
```
Result:
  - The website responds successfully, confirming that the containerized Apache server is serving content correctly through port 8080.

5. (Optional) Verify File Sync
Compare files between host and container:
```bash
ls /var/www/html
sudo docker exec nautilus ls /usr/local/apache2/htdocs
```
Both directories contain identical files, confirming proper volume synchronization.

## Validation Summary
| Check                                                          | Result    |
| -------------------------------------------------------------- | --------- |
| Container running                                              | ✅ Yes     |
| Port mapping (8080 → 80)                                       | ✅ Correct |
| Volume mapping (`/var/www/html` → `/usr/local/apache2/htdocs`) | ✅ Correct |
| Website accessible via `curl`                                  | ✅ Working |

## Conclusion
The nautilus container on Application Server 1 is properly configured and serving the static website as expected.
Both the volume mapping and port binding were verified to be correct, and the website is accessible on port 8080.
No further action is required.
