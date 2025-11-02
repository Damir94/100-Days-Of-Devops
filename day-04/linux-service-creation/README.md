# Creating a Service User Without a Home Directory

## Overview
As part of the new tool implementation at xFusionCorp Industries, the system administrators need a dedicated service user to run background processes securely.
Since service accounts don’t require interactive login or persistent storage, they are typically created without home directories to minimize unnecessary system usage.

This document explains how to create a service user named rose on App Server 3 in the Stratos Datacenter.

## Objective
  - Create a user named rose
  - Do not create a home directory for the user

## Steps to Implement
1. Connect to App Server 3
Access App Server 3 via SSH from the jump host:
```bash
ssh tony@stapp03
```
Replace tony with the correct user if needed.

2. Create the Service User Without a Home Directory
Use the useradd command with the -M flag:
```bash
sudo useradd -M rose
```
Explanation:
   - -M → ensures no home directory is created for the user
   - rose → the username for the service account
This creates a lightweight system user suitable for running services or automated tools.

3. Verify User Creation
Confirm the user was created successfully:
```bash
id rose
```
Expected output:
```ini
uid=1004(rose) gid=1004(rose) groups=1004(rose)
```
Check that no home directory exists:
```bash
grep rose /etc/passwd
```
Output example:
```ruby
rose:x:1004:1004::/home/rose:/bin/bash
```
Then confirm the directory doesn’t actually exist:
```bash
ls -ld /home/rose
```
Expected output:
```pgsql
ls: cannot access '/home/rose': No such file or directory
```
4. (Optional) Assign a Non-Interactive Shell
For additional security (to prevent direct login):
```bash
sudo usermod -s /sbin/nologin rose
```
Now verify:
```bash
grep rose /etc/passwd
```
Example output:
```ruby
rose:x:1004:1004::/home/rose:/sbin/nologin
```
## Verification Checklist
| Item                       | Expected Result                          |
| -------------------------- | ---------------------------------------- |
| User exists                | `id rose` shows UID and GID              |
| Home directory             | No directory `/home/rose` created        |
| Optional shell restriction | `/sbin/nologin` assigned (if configured) |

## Conclusion
By following these steps, we have:
  - Created a service user named rose
  - Ensured no home directory was created, minimizing resource usage
  - Optionally enforced non-interactive access for security
This configuration aligns with xFusionCorp Industries’ policy for secure service account management.
