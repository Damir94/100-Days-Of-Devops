# Creating a Non-Interactive User for Backup Agent Tool
## Overview
To meet the requirements of the backup agent tool used by xFusionCorp Industries, certain system users must be configured with non-interactive shells.
This ensures that such users can perform automated tasks (like backups) without allowing direct login access — improving system security.

This document explains how to create a non-interactive user named javed on App Server 2 in the Stratos Datacenter.

## Objective
  - Create a user named javed
  - Configure the account with a non-interactive shell (so the user cannot log in directly)

## Steps to Implement
1. Connect to App Server 2
Use SSH from the jump host to connect:
```bash
ssh tony@stapp02
```
Replace tony with the correct user if necessary.

2. Create the User with a Non-Interactive Shell
Run the following command:
```bash
sudo useradd -s /sbin/nologin javed
```
Explanation:
  - -s /sbin/nologin → sets the login shell to nologin, preventing shell access
  - javed → the username of the backup user
This ensures the user cannot log in interactively but can still be used by background services or automated tools.

3. Verify User Creation
Check that the user was created correctly:
```bash
id javed
```
Expected output:
```ini
uid=1003(javed) gid=1003(javed) groups=1003(javed)
```
Check the assigned shell:
```bash
grep javed /etc/passwd
```
You should see something like:
```ruby
javed:x:1003:1003::/home/javed:/sbin/nologin
```
4. (Optional) Create Home Directory
If the backup agent requires a specific home directory:
```bash
sudo mkdir -p /home/javed
sudo chown javed:javed /home/javed
```
This step is optional — non-interactive users often don’t need one.

## Verification Checklist
| Item             | Expected Result                                                           |
| ---------------- | ------------------------------------------------------------------------- |
| User exists      | `id javed` shows UID and groups                                           |
| Shell configured | `/sbin/nologin` assigned                                                  |
| Login restricted | Attempting `su - javed` returns “This account is currently not available” |

## Conclusion
By following the above steps, we have:
  - Created a secure, non-interactive system user named javed
  - Configured it in compliance with the backup agent tool’s security requirements
  - Ensured the user cannot log in interactively but can be used by automated processes
This aligns with xFusionCorp’s best practices for principle of least privilege and system hardening.
