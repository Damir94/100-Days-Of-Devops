# Secure File Transfer: Jump Host to App Server 3

## Overview
This document describes the secure process of transferring confidential data from the **Jump Host** to **App Server 3** within the **Stratos DC** infrastructure.  

The transfer ensures that sensitive information remains secure while meeting internal compliance and access control requirements.  
Since developers do **not** have direct access to application servers, the **System Administrator** team performs the file movement.

---

## Environment Details
| Server Role     | Hostname / Alias | Username | Purpose |
|------------------|------------------|-----------|----------|
| Jump Host        | `jump_host`      | `banner`  | Central gateway to access internal servers |
| App Server 3     | `stapp03` / `app3` | `tony` | Target application server for storing confidential files |

---

## File Information
- **Source File:** `/tmp/nautilus.txt.gpg`
- **Destination Path:** `/home/app/`
- **Transfer Type:** Secure Copy (`scp`)
- **Encryption:** The file is GPG-encrypted to protect its contents during and after transfer.

---

## Transfer Steps

### Step 1: Access the Jump Host
Connect to the Jump Host from your local workstation:
```bash
ssh banner@jump_host
```
## Step 2: Copy the File to App Server 3

From the Jump Host, securely copy the file using the scp command:
```bash
scp /tmp/nautilus.txt.gpg tony@stapp03:/home/app/
```
Command Explanation:
  - /tmp/nautilus.txt.gpg: Path of the file on the Jump Host.
  - tony@stapp03: Destination user and host for App Server 3.
  - /home/app/: Target directory on App Server 3.
  - You may be prompted to enter tony’s password to authenticate the transfer.

## Verification
After the transfer, confirm that the file exists on App Server 3:
```bash
ssh tony@stapp03
ls -l /home/app/
```
You should see:
```ini
-rw-r--r-- 1 tony tony  <size>  nautilus.txt.gpg
```
## Security Notes
  - The .gpg file format ensures the data remains encrypted at rest and in transit.
  - Access to both servers should be limited to authorized system administrators only.
  - Never move or decrypt sensitive files on shared or insecure systems.
  - Verify permissions on /home/app/ to prevent unauthorized access:
```bash
chmod 700 /home/app/
```
## Troubleshooting
| Issue                                        | Possible Cause                   | Solution                                             |
| -------------------------------------------- | -------------------------------- | ---------------------------------------------------- |
| `Permission denied`                          | Incorrect SSH credentials        | Verify user and permissions                          |
| `Host not found`                             | Incorrect hostname               | Use full FQDN like `stapp03.stratos.xfusioncorp.com` |
| `scp: /home/app/: No such file or directory` | Directory missing on destination | Create it: `mkdir -p /home/app`                      |
| `Connection timed out`                       | Firewall restrictions            | Ensure SSH (port 22) access is open from Jump Host   |
