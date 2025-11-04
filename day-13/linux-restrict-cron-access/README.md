# Crontab Access Control Configuration – App Server 1

## Overview
In compliance with the **Nautilus Project security standards**, crontab access has been restricted on **App Server 1**.  
Only authorized users are permitted to create or modify cron jobs, ensuring better control, accountability, and reduced risk of unauthorized scheduled tasks.

This configuration explicitly:
- **Allows crontab access** to the user **`javed`**
- **Denies crontab access** to the user **`jerome`**

---

## Environment Details

| Component | Hostname / Alias | User | Purpose |
|------------|------------------|------|----------|
| App Server 1 | `stapp01` / `app1` | `tony` (default sudo user) | Target server where cron restrictions are enforced |

---

## Security Policy Rationale
Cron jobs are a powerful feature in Linux, enabling task automation. However, unrestricted access can:
- Introduce unauthorized background processes
- Interfere with system performance
- Compromise sensitive data or compliance requirements

To align with **Nautilus DC Security Guidelines**, access is granted only to designated users.

---

## Implementation Steps

### Step 1: SSH into App Server 1
Access the target server from the jump host:
```bash
ssh tony@stapp01
```

## Step 2: Configure cron.allow
Create or update the file /etc/cron.allow with the authorized username:
```bash
echo "javed" | sudo tee /etc/cron.allow
```
This explicitly grants the javed user permission to create or edit cron jobs.

### Step 3: Configure cron.deny
Create or update /etc/cron.deny to list users who are denied access:
```bash
echo "jerome" | sudo tee /etc/cron.deny
```
Although /etc/cron.allow takes precedence, including jerome in /etc/cron.deny ensures clarity and consistency with compliance documentation.

### Step 4: Secure File Permissions
Set proper ownership and permissions to prevent unauthorized edits:
```bash
sudo chmod 644 /etc/cron.allow /etc/cron.deny
sudo chown root:root /etc/cron.allow /etc/cron.deny
```
### Verification
Allow Test (User: javed)

Switch to the javed account and attempt to edit crontab:
```bash
su - javed
crontab -e
```
You should see the default crontab editor open.

❌ Deny Test (User: jerome)

Switch to the jerome account:
```bash
su - jerome
crontab -e
```

Expected output:
```java
You (jerome) are not allowed to use this program (crontab)
```
### Summary of Configuration
| File              | Purpose                         | Content  |
| ----------------- | ------------------------------- | -------- |
| `/etc/cron.allow` | Lists users allowed to use cron | `javed`  |
| `/etc/cron.deny`  | Lists users denied access       | `jerome` |

## Notes & Best Practices
  - If /etc/cron.allow exists, it overrides /etc/cron.deny — only listed users may use cron.
  - Always keep these files owned by root to prevent tampering.
  - Use version control (like Git) for tracking configuration changes in /etc/security/ or cron-related settings.
