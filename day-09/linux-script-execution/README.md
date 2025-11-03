# Granting Executable Permissions to a Backup Script

## Overview
The xFusionCorp Industries system administration team has deployed a new automation script named xfusioncorp.sh to streamline backup processes across the environment.
However, the script currently lacks executable permissions on App Server 3 in the Stratos Datacenter.

This document explains how to grant execution rights so that all users can run the script when needed.

## Objective
  - Locate the script: /tmp/xfusioncorp.sh
  - Grant execute permissions to all users (owner, group, others)
  - Apply changes on App Server 3

## Steps to Implement
1. Connect to App Server 3
Access App Server 3 via SSH from the jump host:
```bash
ssh tony@stapp03
```
Replace tony with the correct server login user if required.

2. Verify the Script’s Existence and Current Permissions
Check if the script exists and list its current permissions:
```bash
ls -l /tmp/xfusioncorp.sh
```
Expected output (before change):
```ini
-rw-r--r-- 1 root root 1024 Oct 28 10:20 /tmp/xfusioncorp.sh
```
The absence of an x (execute) flag indicates that the script is not currently executable.

3. Grant Execute Permissions to All Users
Run the following command to make the script executable for everyone:
```bash
sudo chmod a+x /tmp/xfusioncorp.sh
```
Explanation:
  - chmod → change file mode (permissions)
  - a+x → give execute permission to all users (owner, group, others)

4. Verify the New Permissions
Recheck the file permissions:
```bash
ls -l /tmp/xfusioncorp.sh
```
Expected output (after change):
```ini
-rwxr-xr-x 1 root root 1024 Oct 28 10:20 /tmp/xfusioncorp.sh
```
The presence of x under each permission group confirms that all users can execute the script.

5. (Optional) Test Execution
Confirm that the script can be executed:
```bash
bash /tmp/xfusioncorp.sh
```
or simply:
```bash
/tmp/xfusioncorp.sh
```
If the script runs successfully (or shows expected output/errors), the permissions have been applied correctly.

## Verification Checklist
| Check Item              | Expected Result                          |
| ----------------------- | ---------------------------------------- |
| File exists             | `/tmp/xfusioncorp.sh` present            |
| Permissions updated     | `-rwxr-xr-x` visible                     |
| Executable by all users | Anyone can run the script                |
| Tested successfully     | Script executes without permission error |

## Conclusion
By completing these steps, we have:
  - Enabled executable permissions on the /tmp/xfusioncorp.sh script
  - Ensured that all users on App Server 3 can execute the backup automation script
  - Aligned with xFusionCorp’s automation and access readiness policy
This configuration allows smoother operation of automated backup workflows across the Stratos Datacenter.ssfully 
