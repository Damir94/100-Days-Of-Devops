# Implementing Group-Based Access Control for App Servers

## Overview
To simplify and secure access management, the xFusionCorp Industries system administration team has introduced group-based access control.
This approach ensures that permissions and administrative roles are managed at the group level, rather than per individual user.

This document describes the process of creating a dedicated administrative group and assigning users accordingly across all App Servers in the Stratos Datacenter.

## Objective
Perform the following tasks on all App Servers (App Server 1, 2, and 3):
  - Create a new group named nautilus_admin_users
  - Add the user sonya to that group
  - If the user sonya does not already exist, create it first

## Steps to Implement
1. Connect to Each App Server
Access each App Server individually from the jump host using SSH:
```bash
ssh tony@stapp01     # App Server 1
ssh tony@stapp02     # App Server 2
ssh tony@stapp03     # App Server 3
```

Tip: The default user for app servers in the Stratos DC is usually tony.
Replace the username as needed based on your environment setup.

2. Create the Group
Run the following command on each App Server:
```bash
sudo groupadd nautilus_admin_users
```
This command creates a new group named nautilus_admin_users.
If the group already exists, you’ll receive a harmless message indicating so.

3. Check if the User Exists
Verify whether the user sonya exists:
```bash
id sonya
```
If the output shows “no such user”, create the user:
```bash
sudo useradd sonya
```
4. Add the User to the Group
Now add sonya to the nautilus_admin_users group:
```bash
sudo usermod -aG nautilus_admin_users sonya
```
The -aG flag ensures the user is appended to the group without losing other group memberships.

Always double-check group membership after running this command.

5. Verify the Group Membership

Confirm that sonya was successfully added to the group:
```bash
id sonya
```
Expected output example:
```ini
uid=1002(sonya) gid=1002(sonya) groups=1002(sonya),1003(nautilus_admin_users)
```
6. Repeat on All App Servers
Repeat steps 2 through 5 on:
  - App Server 1
  - App Server 2
  - App Server 3
This ensures uniform access control across all environments.

## Verification Checklist
| Check Item                    | Expected Result                                        |
| ----------------------------- | ------------------------------------------------------ |
| Group exists                  | `grep nautilus_admin_users /etc/group` shows the group |
| User exists                   | `id sonya` displays UID and groups                     |
| User in group                 | `nautilus_admin_users` listed under sonya’s groups     |
| Config applied to all servers | Same verification passes on App1, App2, App3           |

## Conclusion
By completing these steps, we have:
  - Created a centralized admin group: nautilus_admin_users
  - Ensured consistent user-to-group assignment across all App servers
  - Streamlined the access control model for administrative users in the Stratos Datacenter
This setup supports better security, scalability, and simplified management across environments.
