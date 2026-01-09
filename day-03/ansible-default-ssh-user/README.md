
# Configuring Default SSH User in Ansible

## Purpose
To simplify Ansible operations across all servers in the Nautilus stack, we configured a default SSH user. 
This ensures that Ansible automatically uses the same user for connecting to all managed nodes without needing to specify it every time in playbooks or inventory files.

### Configuration Details
  - File modified: /etc/ansible/ansible.cfg
  - Section: [defaults]
  - Parameter added/updated:
```ini
remote_user = javed
```
### Why This Is Important
1. Consistency: Ensures all servers are managed using the same non-root user (javed), improving uniformity.
2. Security: Avoids direct use of root, following best practices for privilege management.
3. Efficiency: Eliminates the need to repeatedly specify -u javed in Ansible commands or define the user in multiple inventories.
4. Scalability: Simplifies future expansion since new hosts automatically inherit the same SSH user configuration.

### Verification
Run the following to confirm the change:
```bash
ansible all -i /etc/ansible/hosts -m ping
```
If the connection is successful, Ansible is now using javed by default.







