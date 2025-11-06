# 100 Days of Devops - Day 73: Centralized Apache Log Collection via Jenkins

## Overview
The DevOps team of xFusionCorp Industries is in the process of setting up a centralized logging management system to simplify log monitoring and troubleshooting across multiple servers.
While the centralized solution is being deployed, this Jenkins automation provides an interim way to collect Apache logs periodically from one of the App Servers and store them on the Storage Server for analysis.

This setup ensures important logs are consistently backed up and available for troubleshooting Apache issues, especially on App Server 2.

## Task Summary

Goal:
Automate copying of Apache log files (access_log and error_log) from App Server 2 to the Storage Server every 7 minutes using Jenkins.

Jenkins Job Name: copy-logs
Schedule: Every 7 minutes (*/7 * * * *)
Source (App Server 2): /var/log/httpd/
Destination (Storage Server): /usr/src/data/

## Prerequisites

1. Jenkins Installed and Running
 - Jenkins UI accessible via browser
 - Credentials:
```makefile
Username: admin
Password: Adm!n321
```

2. SSH Access Between Servers
  - Jenkins host must be able to SSH into both:
      - App Server 2 (to read logs)
      - Storage Server (to copy logs)
  - Example user: natasha

3. Apache Installed and Running on App Server 2
Default log paths:
```bash
/var/log/httpd/access_log
/var/log/httpd/error_log
```
4. Storage Directory Exists and Has Proper Permissions
On the Storage Server, run:
```bash
sudo mkdir -p /usr/src/data
sudo chown natasha:natasha /usr/src/data
sudo chmod 755 /usr/src/data
```
## Jenkins Job Configuration Steps
1. Create the Jenkins Job
  - Log in to Jenkins.
  - Click “New Item” → “Freestyle project”.
  - Enter name copy-logs and click OK.

2. Set Up Periodic Build Schedule
  - Go to Build Triggers.
  - Enable “Build periodically”.
  - Enter the following cron expression:
```markdown
*/7 * * * *
```
This runs the job every 7 minutes.

3. Configure SSH Plugin
 - Navigate to Manage Jenkins → Plugins → Available.
 - Install the plugin: Publish Over SSH.
 - Restart Jenkins when installation completes.

4. Add Remote Host Configuration
  - Go to Manage Jenkins → Configure System.
  - Scroll to Publish Over SSH section.
  - Add a new SSH server:
  - Name: storage-server
  - Hostname: ststor01
  - Username: natasha
  - Remote Directory: /usr/src/data
  - Test Configuration to verify connection
  - Save.

5. Define Build Step

Open copy-logs job → Configure.

Under Build, choose:
```sql
Add build step → Execute shell
```
Paste the following script:
```bash
#!/bin/bash
echo "Copying Apache logs from App Server 2 to Storage Server..."

# Variables
SRC_LOG_PATH="/var/log/httpd"
DST_SERVER="natasha@ststor01"
DST_PATH="/usr/src/data"

# Copy access and error logs
scp ${SRC_LOG_PATH}/access_log ${SRC_LOG_PATH}/error_log ${DST_SERVER}:${DST_PATH}/

echo "Log copy completed successfully."
```
Save and click Build Now to test.

## Verification
After a successful run:

SSH into the Storage Server:
```bash
ssh natasha@ststor01
```
List the destination directory:
```bash
ls -l /usr/src/data/
```
You should see:
```nginx
access_log
error_log
```

Check Jenkins console logs for:

Finished: SUCCESS

## Troubleshooting
| Issue                       | Cause                                             | Fix                                                                 |
| --------------------------- | ------------------------------------------------- | ------------------------------------------------------------------- |
| `Permission denied`         | User doesn’t have write access to `/usr/src/data` | Run `sudo chown natasha:natasha /usr/src/data`                      |
| `No such file or directory` | Destination directory missing                     | Run `sudo mkdir -p /usr/src/data`                                   |
| `scp: not found`            | SCP command missing on remote                     | Install with `sudo yum install openssh-clients -y`                  |
| Logs not updating           | Wrong Apache log path                             | Check whether logs are under `/var/log/httpd` or `/var/log/apache2` |

## Maintenance
  - Logs will be copied every 7 minutes automatically.
  - You can view the job’s history in Jenkins → copy-logs → Build History.
  - Periodically verify the Storage Server has enough disk space and clean old logs if necessary.

## Summary
This Jenkins automation ensures continuous Apache log collection across servers
It simplifies troubleshooting and provides a reliable foundation for future integration with a centralized logging system like ELK or OpenSearch.
