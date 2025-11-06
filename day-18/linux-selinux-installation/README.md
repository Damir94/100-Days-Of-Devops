# SELinux Configuration for App Server 1 – Stratos Datacenter

## Overview
Following a recent security audit, the xFusionCorp Industries security team decided to strengthen system and application security by implementing SELinux (Security-Enhanced Linux).
As part of the initial rollout, App Server 1 in the Stratos Datacenter has been selected for configuration and testing.

## Objective
  - Perform the following tasks on App Server 1:
  - Install required SELinux packages.
  - Permanently disable SELinux (temporary measure).
  - No reboot required immediately — the server will reboot during the scheduled maintenance window.
  - Ignore the current runtime status; after reboot, SELinux must remain disabled.

## Implementation Steps
1. Install SELinux Packages

Ensure SELinux core packages are installed. Run:
```bash
sudo yum install selinux-policy selinux-policy-targeted policycoreutils -y
```
Verify the installation:
```bash
rpm -qa | grep selinux
```
You should see output confirming installation of SELinux-related packages such as:
```pgsql
selinux-policy
selinux-policy-targeted
policycoreutils
```
2. Check Current SELinux Status

To confirm the current runtime mode:
```bash
sestatus
```
You might see:
```yaml
SELinux status:                 enabled
Current mode:                   enforcing
```
Ignore this value for now, as runtime status will remain unchanged until the next reboot.

3. Permanently Disable SELinux

Edit the SELinux configuration file:
```bash
sudo vi /etc/selinux/config
```
Locate the line:
```ini
SELINUX=enforcing
```
Change it to:
```ini
SELINUX=disabled
```
Save and exit the file.

4. Verify the Configuration Change

Check the updated configuration setting without rebooting:
```bash
grep SELINUX= /etc/selinux/config
```
Expected output:
```ini
SELINUX=disabled
```
5. No Immediate Reboot Required

Do not reboot the server now.
The change will take effect automatically during the scheduled maintenance reboot tonight.

## Outcome
  - All necessary SELinux packages have been successfully installed.
  - SELinux has been permanently disabled in the configuration file.
  - The runtime status may still show "enabled" until the next reboot — this is expected behavior.
  - After reboot, SELinux will remain disabled as per configuration.
