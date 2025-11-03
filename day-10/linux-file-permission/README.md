# Correcting ACL Permissions for /etc/resolv.conf

## Overview
During a recent security audit in the Stratos Datacenter, the Nautilus security team discovered that some critical system files had incorrect permission settings.
One of these files — /etc/resolv.conf — on Nautilus App Server 1 needs its ownership and ACLs corrected to comply with company security standards.

This document outlines the exact steps to properly secure the file.

## Objective
  - On App Server 1, perform the following actions for /etc/resolv.conf:
  - Set user owner → root
  - Set group owner → root
  - Ensure others have read-only permission
  - Revoke all permissions for user kareem
  - Grant read-only permission to user rod

## Steps to Implement
1. Connect to App Server 1
From the jump host, connect to App Server 1:
```bash
ssh tony@stapp01
```
Replace tony with the correct SSH user if needed.

2. Verify the Current Ownership and Permissions
Check the current ownership and ACLs:
```bash
ls -l /etc/resolv.conf
getfacl /etc/resolv.conf
```
This helps confirm the current setup before making changes.

3. Set User and Group Ownership to root
Run the following command:
```bash
sudo chown root:root /etc/resolv.conf
```
This ensures both the file’s owner and group are root.

Verify:
```bash
ls -l /etc/resolv.conf
```
Expected result:
```ini
-rw-r--r-- 1 root root ... /etc/resolv.conf
```
4. Set Base File Permissions (Others Read-Only)
Reset standard permissions so that others have read-only access:
```bash
sudo chmod 644 /etc/resolv.conf
```
Explanation:
  - 6 = read/write for owner (root)
  - 4 = read-only for group
  - 4 = read-only for others
Verify:
```bash
ls -l /etc/resolv.conf
```
Expected:
```ini
-rw-r--r-- 1 root root ... /etc/resolv.conf
```
5. Remove All Permissions for User kareem
   Use setfacl to explicitly remove permissions:
```bash
sudo setfacl -x u:kareem /etc/resolv.conf
```
If the user previously had permissions set via ACLs, this will delete them.

Alternatively (if no ACL existed before):
```bash
sudo setfacl -m u:kareem:--- /etc/resolv.conf
```
6. Grant Read-Only Permission to User rod
Use the following command:
```bash
sudo setfacl -m u:rod:r-- /etc/resolv.conf
```
This allows rod to read the file but not modify it.

7. Verify All Changes
Check the final ACL configuration:
```bash
getfacl /etc/resolv.conf
```
Expected output:
```pgsql
# file: /etc/resolv.conf
# owner: root
# group: root
user::rw-
user:rod:r--
user:kareem:---
group::r--
mask::r--
other::r--
```
## Verification Checklist
| Check Item | Expected Result   |
| ---------- | ----------------- |
| Owner      | `root`            |
| Group      | `root`            |
| Others     | read-only (`r--`) |
| kareem     | no access (`---`) |
| rod        | read-only (`r--`) |

## Conclusion
By completing these steps, we have:
  - Corrected ownership and permissions for /etc/resolv.conf
  - Removed unauthorized access for kareem
  - Granted controlled read-only access for rod
  - Ensured compliance with xFusionCorp Industries and Nautilus project security protocols
This strengthens the system’s protection for critical network configuration files and ensures proper principle of least privilege access.
