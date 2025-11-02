# Creating a Custom Apache User for Web Application Security

## Overview
As part of enhanced security practices at xFusionCorp Industries, each web application on the servers must run under a dedicated Apache user account.
This limits permissions to each application's context, reducing risk in case of compromise.

This document outlines the procedure to create a custom Apache user named john on App Server 2 within the Stratos Datacenter.

## Objective
  - Create a secure and isolated Apache user with:
  - Username: john
  - User ID (UID): 1991
  - Home Directory: /var/www/john
This ensures that the user john can be associated with a specific web application while maintaining proper isolation and ownership of web resources.

## Steps to Implement
1. Connect to App Server 2

Access App Server 2 using SSH from the jump host:
```bash
ssh tony@stapp02
```
(Replace tony with the correct server login user if needed.)

2. Create the Custom User
Run the following command to create the Apache user:
```bash
sudo useradd -u 1991 -d /var/www/john -m john
```
Explanation of parameters:
  - -u 1991 → assigns a unique user ID (UID)
  - -d /var/www/john → sets the user’s home directory
  - -m → automatically creates the home directory if it does not exist

3. Verify User Creation
Check if the user was created successfully:
```bash
id john
```
Expected output:
```ini
uid=1991(john) gid=1991(john) groups=1991(john)
```
Verify the home directory:
```bash
ls -ld /var/www/john
```
You should see that /var/www/john exists and is owned by the user john.

4. (Optional) Assign a Password
If password login is required:
```bash
sudo passwd john
```
For service users, this step is typically skipped to enhance security.

## Security Recommendation
For application service accounts that do not require shell access, restrict login by assigning a non-interactive shell:
```bash
sudo usermod -s /sbin/nologin john
```
This prevents direct user login while allowing Apache or other services to run under the user context.

## Verification Checklist
| Item                         | Expected Result          |
| ---------------------------- | ------------------------ |
| User exists                  | `id john` shows UID 1991 |
| Home directory               | `/var/www/john` exists   |
| Correct ownership            | Owned by `john`          |
| Shell restriction (optional) | `/sbin/nologin` assigned |

## Conclusion
By following the above steps, we have:
  - Created a custom Apache user john
  - Ensured UID consistency and secure directory ownership
  - Optionally applied non-interactive shell restriction for improved security
This aligns with xFusionCorp’s security policy of application-specific user isolation for web services.
