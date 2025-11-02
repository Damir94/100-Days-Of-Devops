# Disabling Direct Root SSH Login on All App Servers

## Overview
After a recent security audit, the xFusionCorp Industries Security Team has mandated stricter SSH access controls.
One of the key requirements is to disable direct root SSH login on all App Servers within the Stratos Datacenter.

This change minimizes the risk of unauthorized privileged access and enforces proper access management through non-root users.

## Objective
  - Disable direct SSH login for the root user
  - Apply the change on all App Servers:
    - App Server 1 (stapp01)
    - App Server 2 (stapp02)
    - App Server 3 (stapp03)

## Steps to Implement
1. Connect to Each App Server
From the jump host, connect to each App Server one at a time:
```bash
ssh tony@stapp01
ssh tony@stapp02
ssh tony@stapp03
```
Replace tony with the correct non-root user (usually provided for app servers).

2. Edit the SSH Configuration File
Open the SSH daemon configuration file /etc/ssh/sshd_config:
```bash
sudo vi /etc/ssh/sshd_config
```
Locate the line that controls root login:
```shell
#PermitRootLogin yes
```
Uncomment it (remove #) and set its value to no:
```nginx
PermitRootLogin no
```
This explicitly disables SSH logins by the root user.

3. Save and Exit

4. Restart the SSH Service
Apply the new configuration by restarting the SSH daemon:
```bash
sudo systemctl restart sshd
```
Verify that it’s active and running:
```bash
sudo systemctl status sshd
```
You should see:
```arduino
active (running)
```
5. Verify the Change
Confirm that root SSH login is disabled:
```bash
sudo grep PermitRootLogin /etc/ssh/sshd_config
```
Expected output:
```nginx
PermitRootLogin no
```
You should see:
```nginx
Permission denied
```
## Verification Checklist
| Check Item                 | Expected Result                                |
| -------------------------- | ---------------------------------------------- |
| SSH config updated         | `PermitRootLogin no` in `/etc/ssh/sshd_config` |
| SSH service restarted      | `active (running)`                             |
| Root login attempt         | Denied                                         |
| Applied on all app servers | ✅ App Server 1, 2, 3 verified                  |

## Conclusion
By implementing these steps, we have:
  - Disabled direct SSH login for the root user
  - Enforced least-privilege access policies
  - Strengthened overall security posture of all application servers in the Stratos Datacenter
Developers and admins must now log in using non-root accounts (e.g., tony, steve, etc.) and use sudo for privileged operations.
