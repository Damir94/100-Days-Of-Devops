# Creating a Temporary User Account with Expiry Date
## Overview
To manage temporary access securely, the system admin team at xFusionCorp Industries enforces strict user account lifecycle policies.
This ensures that temporary users automatically lose access after a defined period, without manual intervention.

This document explains how to create a temporary user account for the developer anita on App Server 1 in the Stratos Datacenter, with an expiry date of 2023-12-07.

## Objective
  - Create a user named anita (in lowercase)
  - Set the account expiry date to December 7, 2023
  - Perform this configuration on App Server 1

## Steps to Implement
1. Connect to App Server 1
Access App Server 1 from the jump host:
```bash
ssh tony@stapp01
```
Replace tony with the correct user if necessary.
You should now be operating on App Server 1.

2. Create the User with an Expiry Date
Run the following command to create the user:
```bash
sudo useradd -e 2023-12-07 anita
```
Explanation of parameters:
  - -e 2023-12-07 → sets the account expiry date
  - anita → username for the temporary user
This ensures that the account will automatically expire at midnight on the specified date.

3. Verify the User Creation
Check the user details:
```bash
sudo chage -l anita
```
You should see output similar to:
```pgsql
Last password change                                    : never
Account expires                                         : Dec 07, 2023
Password inactive                                       : never
Minimum number of days between password change          : 0
Maximum number of days between password change          : 99999
Number of days of warning before password expires       : 7
```
4. (Optional) Set a Password
If the developer requires a password for login:
```bash
sudo passwd anita
```
Enter a secure temporary password as required by policy.

## Verification Checklist
| Check Item                   | Expected Result                     |
| ---------------------------- | ----------------------------------- |
| User exists                  | `id anita` shows UID and GID        |
| Expiry date                  | `chage -l anita` shows Dec 07, 2023 |
| Username format              | all lowercase                       |
| Account active before expiry | User can log in                     |
| Account auto-expires         | Login disabled after expiry date    |

## Conclusion
By completing these steps, we have:
  - Created a temporary user account named anita
  - Configured it to automatically expire on 2023-12-07
  - Ensured compliance with xFusionCorp Industries’ access management policy for temporary users
This approach prevents lingering inactive accounts and strengthens overall security and compliance across the environment.
