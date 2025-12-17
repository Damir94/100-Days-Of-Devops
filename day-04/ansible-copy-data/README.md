
# Ansible File Creation Playbook

## Overview
This playbook is designed for the Nautilus DevOps team to test Ansible's `file` module by creating a blank file on all application servers in Stratos DC.  
Each file will have specific ownership and permission settings based on the server it is deployed to.

---

## Files
- **inventory** → Contains the list of target app servers.
- **playbook.yml** → Ansible playbook to create and configure the file `/opt/data.txt`.

---

## Inventory Structure
The inventory file defines three app servers and their corresponding Ansible users:

```ini
[app_servers]
stapp01 ansible_host=<IP_of_app_server_1> ansible_user=tony
stapp02 ansible_host=<IP_of_app_server_2> ansible_user=steve
stapp03 ansible_host=<IP_of_app_server_3> ansible_user=banner
```
Replace <IP_of_app_server_X> with the actual IP addresses of the servers.

### Playbook Details
The playbook performs the following tasks:
- Creates the file /opt/data.txt on each app server.
- Sets file permissions to 0755.
- Assigns ownership dynamically based on the SSH user for each server.
```yaml
- name: Create /opt/data.txt on all app servers
  hosts: app_servers
  become: yes
  tasks:
    - name: Create /opt/data.txt with correct permissions and ownership
      file:
        path: /opt/data.txt
        state: touch
        mode: '0755'
        owner: "{{ ansible_user }}"
        group: "{{ ansible_user }}"
```
Running the Playbook
To execute the playbook:
```bash
ansible-playbook -i inventory playbook.yml
```
### Expected Results
After successful execution:
  - On App Server 1 → /opt/data.txt owned by tony
  - On App Server 2 → /opt/data.txt owned by steve
  - On App Server 3 → /opt/data.txt owned by banner
  - Permissions set to 0755

### Verification
You can verify the results by running the following command on each app server:
```bash
ls -l /opt/data.txt
```
<img width="1706" height="625" alt="Screenshot 2025-10-26 at 11 07 01 AM" src="https://github.com/user-attachments/assets/e65f0efa-5cf3-4116-8a1f-c9eccdc25815" />




