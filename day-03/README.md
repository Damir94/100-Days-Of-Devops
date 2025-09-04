# Day 03- Securing SSH Access: Disabling Root Login on Linux Servers

## Objective

As part of the **100 Days of Kloud** challenge and enterprise security hardening initiative, I completed a project to **disable direct root SSH login** across all app servers in the **Stratos Datacenter**. This was based on updated security policies after an internal audit by the xFusionCorp security team.

## Business Value

Disabling direct root SSH access significantly improves server security by:

- Reducing the attack surface for brute-force and credential-stuffing attacks
- Enforcing the principle of least privilege (users log in with limited accounts and escalate with `sudo` only when needed)
- Making all administrative activity traceable to specific user accounts, enhancing auditability

This is a standard best practice in securing Linux servers in production environments.

## Target Systems

The following application servers were updated:

| Hostname   | IP Address      | User         |
|------------|------------------|--------------|
| stapp01    | 172.16.238.10    | `tony`       |
| stapp02    | 172.16.238.11    | `steve`      |
| stapp03    | 172.16.238.12    | `banner`     |

## 🔧 Implementation Walkthrough

### Step 1: SSH into Each App Server via the Jump Host

ssh tony@stapp01    # then repeat for steve@stapp02 and banner@stapp03

### Step 2: Verify Current Root SSH Setting
sudo grep PermitRootLogin /etc/ssh/sshd_config

<img width="1118" height="730" alt="Screenshot 2025-09-04 at 2 09 07 PM" src="https://github.com/user-attachments/assets/b184baf6-0488-45a3-a565-713386b14755" />


### Step 3: Edit SSH Configuration
**-Used vi to edit the SSH daemon config file**: sudo vi /etc/ssh/sshd_config

**-Updated (or added) the line:** PermitRootLogin no

<img width="517" height="243" alt="Screenshot 2025-09-04 at 2 10 11 PM" src="https://github.com/user-attachments/assets/b97e64f5-50ad-4b9b-ae30-f815e99291ee" />


### Step 4: Restart SSH Service
sudo systemctl restart sshd

sudo systemctl status sshd

<img width="976" height="635" alt="Screenshot 2025-09-04 at 2 10 49 PM" src="https://github.com/user-attachments/assets/dc04d2d8-8162-4e09-80ec-f4d7d36d8a0e" />

### Step 5: Confirm Root SSH Login Is Denied
From the jump host or app server:

ssh root@stapp01

Observed behavior:

root@stapp01's password:
Connection closed by 172.16.238.10 port 22

<img width="1158" height="656" alt="Screenshot 2025-09-04 at 2 12 36 PM" src="https://github.com/user-attachments/assets/721b267e-0526-4d48-bc03-3b557904c328" />


### Verification Completed On:

| Hostname   | PermitRootLogin  | SSH Restarted| Root Login Blocked
|------------|------------------|--------------|--------------
| stapp01    | ✅               | ✅          | ✅
| stapp02    | ✅               | ✅          | ✅
| stapp03    | ✅               | ✅          | ✅

### Evidence
All key steps were documented with screenshots and saved in the /screenshots directory:

stapp01-before-grep.png

stapp01-edited-config.png

stapp01-ssh-restarted.png

... and the same for stapp02 and stapp03, with a single screenshot showing that all servers had their root logins denied.

You can view these in the /screenshots folder.

### Outcome
Successfully disabled direct root SSH login on all app servers in compliance with xFusionCorp security policies. This enhanced the security posture of the environment by enforcing named user access and reducing privilege escalation vectors.

### Skills Demonstrated
Linux system administration

SSH and server hardening

Editing and managing daemon config files

Systemd service management

Verifying real-world security controls

Professional documentation and screenshot capture

### Final Note
This task was completed as part of the 100 Days of Kloud challenge on KodeKloud's DevOps learning platform, focused on mastering hands-on DevOps and SRE skills through real-world projects.

---
