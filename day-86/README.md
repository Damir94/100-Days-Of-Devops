# 100 Days of Devops - Day 86: Setting Up Passwordless SSH and Testing Ansible Connectivity (Stratos DC – App Server 3)

This document explains how to configure passwordless SSH from the Ansible controller (jump host) to App Server 3 (stapp03) and verify connectivity using the Ansible ping module.
This setup is required before running any Ansible playbooks in the Stratos Data Center environment.

## Overview

The Nautilus DevOps team needs to test multiple Ansible playbooks across different application servers. Before doing so, the team must ensure that the Ansible controller can connect to the managed nodes without requiring a password.

This guide walks through:
 - Setting up passwordless SSH from the thor user (Ansible controller)
 - Connecting to banner user on App Server 3
 - Testing connectivity using the ansible ping module
 - Ensuring the inventory file is used properly

## Inventory File Location
The Ansible inventory file is already provided on the jump host:
```swift
/home/thor/ansible/inventory
```
This inventory contains the list of managed servers including stapp03, which represents App Server 3.

## Step 1: Generate SSH Keys on the Jump Host (Ansible Controller)
Log in as the thor user:
```bash
sudo su - thor
```
Check if an existing SSH key already exists:
```bash
ls ~/.ssh/id_rsa
```
If not present, generate a new key pair:
```bash
ssh-keygen -t rsa -N "" -f ~/.ssh/id_rsa
```
Press Enter to accept defaults.

## Step 2: Copy the SSH Public Key to App Server 3

In Stratos DC labs, the login user for App Server 3 is:
  - Host: stapp03
  - User: banner
  - Copy the key:
```bash
ssh-copy-id banner@stapp03
```
When prompted, enter the banner user’s password from the lab.
Successful output will look like:
```scss
Number of key(s) added: 1
```
## Step 3: Verify Passwordless SSH Manually
Before testing with Ansible, verify direct SSH access:
```bash
ssh banner@stapp03
```
If login works without asking for a password, SSH authentication is correctly set up.
Exit the session:

exit

## Step 4: Test Ansible Ping to App Server 3
Use the provided inventory file:
```bash
ansible -i /home/thor/ansible/inventory stapp03 -m ping
```
```javascript
✔ Expected Output
stapp03 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```
If you receive the above output, the connection is successful, and the node is ready for Ansible automation.

## Troubleshooting
Permission Denied / SSH Failures

If you see:

UNREACHABLE! Permission denied

It means passwordless SSH is not set up. Re-run:
```bash
ssh-copy-id banner@stapp03
```
Verbose SSH Debugging
```bash
ssh -vvv banner@stapp03
```
Review authentication logs—helpful for diagnosing key or permission issues.

## Conclusion
By completing the steps above, passwordless SSH is now configured between the Ansible controller (jump host) and App Server 3 (stapp03). 
You have also validated connectivity using the Ansible ping module, which confirms that the server is ready for running Ansible playbooks.
This setup is a foundational requirement for consistent and automated deployments across Stratos DC servers.
