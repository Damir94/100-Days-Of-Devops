# Jenkins User Access Configuration – Nautilus Project

## Overview
This document outlines the steps performed to configure user access on the newly set up Jenkins server for the Nautilus CI/CD environment.
The goal is to ensure secure and role-based access using the Project-based Matrix Authorization Strategy.

### Login Details
Access the Jenkins UI:
  - URL: http://<jenkins-server-ip>:8080
  - Username: admin
  - Password: Adm!n321

### Step 1: Create User anita
1. Navigate to Manage Jenkins → Security → Manage Users → Create User.
2. Fill in the following details:
   - Username: anita
   - Password: Rc5C9EyvbU
   - Full Name: Anita
3. Click Create User.

### Step 2: Enable Matrix Authorization
1. Go to Manage Jenkins → Configure Global Security.
2. Under Authorization, select Project-based Matrix Authorization Strategy.
3. Add the following users:
  - admin
  - anita
4. Remove Anonymous users (if listed).

### Step 3: Assign Permissions
| User          | Permissions          |
| ------------- | -------------------- |
| **admin**     | Overall → Administer |
| **anita**     | Overall → Read       |
| **Anonymous** | No permissions       |
Click Save to apply.

### Step 4: Configure Job-Level Access
1. Open the existing Jenkins job.
2. Click Configure → Enable project-based security.
3. Add the user anita with Read permission only.
4. Ensure the admin user has full permissions for the job.
5. Save the configuration.

### Step 5: Plugin Installation (if required)
If the Matrix Authorization Strategy option is unavailable:
1. Go to Manage Jenkins → Plugins → Available.
2. Search for Matrix Authorization Strategy Plugin.
3. Install the plugin and select
4. Restart Jenkins when installation is complete and no jobs are running.
5. Wait for Jenkins to fully restart before proceeding.

### Step 6: Documentation & Review
For review purposes:
  - Capture screenshots of:
    - User creation page
    - Matrix permission configuration
    - Job-level access control

## Summary
| Item                  | Status              |
| --------------------- | ------------------- |
| Jenkins Admin Account | Configured          |
| User `anita`          | Created             |
| Matrix Authorization  | Enabled             |
| Anonymous Access      | Disabled            |
| Job Access            | Read-only for Anita |

