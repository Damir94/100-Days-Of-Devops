# 100 Days of Devops - Day 74: Jenkins Database Backup Automation

This project automates the process of taking periodic database backups using Jenkins. 
The job is configured to dump the database from the Database Server and securely transfer it to the Backup Server every 10 minutes.

## Overview
## Objective
The goal of this Jenkins job is to:
  - Automatically back up the kodekloud_db01 database.
  - Save the dump file in the format db_YYYY-MM-DD.sql (e.g., db_2025-11-06.sql).
  - Copy the backup to the Backup Server under /home/clint/db_backups.
  - Run every 10 minutes using a Jenkins cron schedule.

## Jenkins Job Details
| **Item**               | **Description**                           |
| ---------------------- | ----------------------------------------- |
| **Job Name**           | `database-backup`                         |
| **Job Type**           | Freestyle Project                         |
| **Schedule**           | `*/10 * * * *` (every 10 minutes)         |
| **Database Name**      | `kodekloud_db01`                          |
| **Database User**      | `kodekloud_roy`                           |
| **Password**           | `asdfgdsd`                                |
| **Backup Destination** | `/home/clint/db_backups` on Backup Server |



## Step-by-Step Setup
1. Login to Jenkins

Go to the Jenkins dashboard.

Use the following credentials:
```bash
Username: admin
Password: Adm!n321
```
2. Create a New Job

Click “New Item” → Freestyle project.

Name it database-backup.

Click OK.

3. Configure the Build Step

Under the Build section, add a Shell Command and paste the script below:
```bash
#!/bin/bash

# Variables
DB_NAME="kodekloud_db01"
DB_USER="kodekloud_roy"
DB_PASS="asdfgdsd"
BACKUP_FILE="db_$(date +%F).sql"
DB_SERVER="stl-db01"         # Replace with actual DB server name or IP
BACKUP_SERVER="stl-backup01" # Replace with actual backup server name or IP

# Step 1: Take a database dump on the Database Server
ssh $DB_SERVER "mysqldump -u${DB_USER} -p${DB_PASS} ${DB_NAME} > /tmp/${BACKUP_FILE}"

# Step 2: Copy the dump to the Backup Server
scp $DB_SERVER:/tmp/${BACKUP_FILE} ${BACKUP_SERVER}:/home/clint/db_backups/

# Step 3: Remove temporary dump file from the Database Server
ssh $DB_SERVER "rm -f /tmp/${BACKUP_FILE}"
```

## Schedule the Backup Job
Go to the Build Triggers section and check:

Build periodically

Then enter the schedule:

*/10 * * * *

This ensures the job runs every 10 minutes, performing automatic backups without manual intervention.

## Testing the Job

Click “Build Now” from the Jenkins job page.

Check the Console Output to confirm:

Database dump was successfully created.

File was copied to /home/clint/db_backups on the Backup Server.

Verify the file exists in the destination folder:
```bash
ls /home/clint/db_backups/db_$(date +%F).sql
```
## Security Considerations

For production environments:

Avoid hardcoding passwords in scripts.

Use Jenkins Credentials Manager to securely store sensitive information.

Replace the plain password with a credentials variable in your script, e.g.:
```bash
DB_PASS=$(cat $DB_PASS_FILE)
```
or use Jenkins’ built-in environment variables like ${DB_PASS}.

## Maintenance

Old backups can be rotated using a cron-based cleanup script or a retention policy.

Jenkins logs and workspace should be periodically cleaned to save space.

## Example Output
```bash
+ mysqldump -ukodekloud_roy -pasdfgdsd kodekloud_db01 > /tmp/db_2025-11-06.sql
+ scp /tmp/db_2025-11-06.sql backup_server:/home/clint/db_backups/
+ rm -f /tmp/db_2025-11-06.sql
[database-backup] SUCCESS
```
## Summary
This Jenkins job ensures:

Automated backups every 10 minutes

Secure file transfer to the backup server

Consistent naming and easy tracking of backup history

With this setup, database backups are reliable, frequent, and fully automated — reducing the risk of data loss.
