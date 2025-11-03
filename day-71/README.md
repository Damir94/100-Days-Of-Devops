# 100 Days of Devops - Day 71: Jenkins Automation: Remote Package Installation via Parameterized Job

## Overview
This Jenkins job automates the installation of packages on a remote Storage Server within the Nautilus Stratos Datacenter.
It enables DevOps engineers to install any Linux package remotely by providing the package name as a build parameter — making it reusable and consistent across environments.

## Objective
  - The goal was to create a Jenkins job that:
  - Accepts a package name as an input parameter.
  - Connects to a remote storage server via SSH.
  - Installs the specified package using yum.
  - Handles authentication and sudo permissions automatically.
  - Runs reliably on repeated executions.

### Jenkins Configuration Steps
1. Login
Access Jenkins using:
```makefile
Username: admin
Password: Adm!n321
```
2. Create the Job
1. From the Jenkins dashboard, click “New Item” → Freestyle Project.
2. Name it install-packages.
3. Check This project is parameterized.
4. Add a String Parameter:
  - Name: PACKAGE
  - Default Value: wget (optional)
  - Description: “Enter the package name to install on the storage server.”

3. Add Build Step
In the Build section, choose Execute Shell and add this script:
```bash
sshpass -p 'Bl@kW' ssh -o StrictHostKeyChecking=no natasha@172.16.238.15 "echo 'Bl@kW' | sudo -S yum install -y ${PACKAGE}"
```
## Explanation:
  - sshpass: Provides the password automatically for non-interactive SSH sessions.
  - ssh -o StrictHostKeyChecking=no: Bypasses the host key confirmation prompt.
  - natasha@172.16.238.15: Remote storage server user and IP.
  - echo 'Bl@kW' | sudo -S: Sends the password to sudo via standard input.
  - $PACKAGE: Jenkins parameter representing the package name.
  - yum install -y: Installs the package without prompting for confirmation.

4. Save and Run
  - Click Build with Parameters.
  - Enter a package name (e.g., httpd).
  - Click Build.
The console output should display:
```makefile
Complete!
Finished: SUCCESS
```
## Verification
After successful execution:

Log into the remote server:
```bash
ssh natasha@172.16.238.15
```
Verify the package installation:
```bash
rpm -q httpd
```
Output:
```ini
httpd-2.4.x86_64
```

## Troubleshooting Guide
1. Permission Denied (SSH)
Error:
```cpp
Permission denied, please try again.
```
Cause: Wrong password or SSH key missing.
Fix:
  - Verify the correct credentials.
  - Use key-based authentication:
```bash
ssh-keygen -t rsa
ssh-copy-id natasha@172.16.238.15
```
2. Sudo Requires a Password
Error:
```swift
sudo: a password is required
```
Cause: Jenkins session cannot provide an interactive password.
Fix Options:
(Best) Configure passwordless sudo:
```sql
natasha ALL=(ALL) NOPASSWD: ALL
```
in /etc/sudoers

OR use:
```bash
echo 'Bl@kW' | sudo -S
```
3. Empty $PACKAGE Parameter

Error:
```nginx
yum install: error: the following arguments are required: PACKAGE
```
Fix:
  - Ensure This project is parameterized is checked.
  - Add String Parameter named PACKAGE.
  - Use Build with Parameters, not Build Now.

4. Jenkins Restart Required
If you install plugins (like SSH Agent), and Jenkins asks to restart:
  - Choose Restart Jenkins when installation is complete and no jobs are running.
  - Wait for Jenkins to come back before proceeding.

### Security Recommendations
  - Use Jenkins Credentials Manager instead of hardcoding passwords.
  - Use SSH key-based authentication where possible.
  - Limit user privileges — use non-root accounts with passwordless sudo only for specific commands.


### Example Output (Success)
```perl
[install-packages] $ /bin/sh -xe /tmp/jenkins...
+ sshpass -p Bl@kW ssh -o StrictHostKeyChecking=no natasha@172.16.238.15 "echo 'Bl@kW' | sudo -S yum install -y httpd"
Loaded plugins: fastestmirror
Resolving Dependencies
--> Installing:
httpd.x86_64 0:2.4.57-1.el9
Complete!
Finished: SUCCESS
```
