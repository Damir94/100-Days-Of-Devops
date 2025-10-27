# Ansible Inventory Setup for App Server 2 – Stratos DC

## Overview
The Nautilus DevOps team is testing Ansible playbooks across multiple servers in the Stratos DC environment.
To enable Ansible connectivity with App Server 2, an inventory file was created and configured on the jump host.

#### File Location
```swift
/home/thor/playbook/inventory
```
#### Inventory File Format
The inventory file follows the INI format used by Ansible.
```ini
[app_servers]
stapp02 ansible_host=172.16.238.11 ansible_user=tony ansible_ssh_pass=Ir0nM@n
```
### Explanation of Parameters
| Parameter            | Description                                                       |
| -------------------- | ----------------------------------------------------------------- |
| **[app_servers]**    | Defines the group name for application servers.                   |
| **stapp02**          | The hostname of App Server 2 as per Stratos DC naming convention. |
| **ansible_host**     | The actual IP address of the server.                              |
| **ansible_user**     | Username used for SSH connection.                                 |
| **ansible_ssh_pass** | Password used for authentication.                                 |

### Verification
To verify connectivity with App Server 2:
```bash
ansible -i /home/thor/playbook/inventory all -m ping
```
### Expected Output:

<img width="1609" height="553" alt="Screenshot 2025-10-26 at 10 49 55 AM" src="https://github.com/user-attachments/assets/f615cdd2-3f19-4e41-baf2-e7408f7ab013" />

### Notes
  - This inventory file enables Ansible to run playbooks located under /home/thor/playbook/ directly on App Server 2.
  - Ensure that SSH access and firewall settings allow communication between the jump host and the target server.
  - Any future servers can be added under the same [app_servers] group for centralized management.
