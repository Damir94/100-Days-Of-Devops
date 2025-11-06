# Enabling GUI Boot by Default on App Servers – Stratos Datacenter

## Overview
With the deployment of new tools on the App Servers within the Stratos Datacenter, 
certain applications now require Graphical User Interface (GUI) access. To support this, 
we must modify the default system runlevel so that the servers boot into graphical mode by default.

Note: Do not reboot the servers after making this change.

## Task Summary

Goal:
Enable GUI boot mode on all App Servers in the Stratos Datacenter without restarting them.

Affected Hosts:
  - App Server 1
  - App Server 2
  - App Server 3

## Steps Performed
1. Check the Current Default Target

Run the following command to confirm the current default runlevel:
```bash
systemctl get-default
```
You’ll likely see:
```pgsql
multi-user.target
```
This indicates the system boots into non-GUI (command-line) mode by default.

2. Set GUI Mode as Default

To make GUI mode the default for all future boots:
```bash
sudo systemctl set-default graphical.target
```
This command creates a symbolic link to the graphical.target, ensuring the system enters the graphical (desktop) environment upon next boot.

3. Verify the Change

Check again to confirm the new default target has been set:
```bash
systemctl get-default
```
Expected output:
```pgsql
graphical.target
```
4. Do Not Reboot

As per the task requirement, do not reboot the servers.
The change will take effect automatically on the next system restart.

## Outcome
All App Servers in the Stratos Datacenter are now configured to boot into GUI mode by default without requiring an immediate reboot.
