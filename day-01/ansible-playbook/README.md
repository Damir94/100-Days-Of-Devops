# Ansible Playbook: Create File on App Server 3

## Overview
This project demonstrates how to configure an Ansible inventory and run a simple playbook targeting a specific remote host (App Server 3) in the Stratos DC environment.
The playbook’s goal is to create an empty file named /tmp/file.txt on App Server 3.

Files and Structure
```bash
/home/thor/ansible/
├── inventory
├── playbook.yml
└── README.md
```
 - inventory → Defines the list of managed hosts (App Server 3 in this case).
 - playbook.yml → Contains the automation logic to create the file on the target host.
 - README.md → Documentation describing setup and purpose.

1. Inventory Configuration
The inventory file defines the target host (App Server 3).
An example configuration is shown below:
```ini
[appservers]
stapp03 ansible_host=172.16.238.12 ansible_user=tony ansible_ssh_pass=Ir0nM@n
```
Explanation:
  - stapp03 — Host alias for App Server 3
  - ansible_host — IP address of the target server
  - ansible_user — SSH user for connection
  - ansible_ssh_pass — SSH password for authentication
This ensures the playbook runs only on App Server 3 in the Stratos DC setup.

2. Playbook Details
The playbook.yml file automates file creation on the target system.
```yaml
---
- name: Create file on App Server 3
  hosts: stapp03
  become: yes
  tasks:
    - name: Create empty file /tmp/file.txt
      file:
        path: /tmp/file.txt
        state: touch
```
Key Points:
  - hosts: Defines which host to execute the task on (stapp03).
  - become: yes: Runs the task with elevated privileges (sudo).
  - file module: Used to manage files and directories.
  - path specifies where the file will be created.
  - state: touch ensures the file exists (creates an empty one if missing).

3. Running the Playbook
Execute the following command from the jump host:
```bash
ansible-playbook -i /home/thor/ansible/inventory /home/thor/ansible/playbook.yml
```
Expected Output:

<img width="1787" height="553" alt="Screenshot 2025-10-26 at 10 43 01 AM" src="https://github.com/user-attachments/assets/d7a37384-5e32-4bff-9ae0-6046f4f72856" />

4. Verification
To confirm that the file was created, SSH into App Server 3 and check:
```bash
ssh tony@172.16.238.12
ls -l /tmp/file.txt
```
You should see an empty file named file.txt in the /tmp directory.

Conclusion. This task demonstrates:
  - How to configure a host-specific Ansible inventory.
  - How to create and execute a basic playbook.
  - How to validate automation results across remote servers.
This forms the foundation for more advanced automation workflows such as file management, configuration deployment, and service provisioning in multi-server environments.

