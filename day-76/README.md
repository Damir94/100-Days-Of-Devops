# 100 Days 0f Devops - Day 76: Jenkins Job Permissions Configuration
Project: xFusionCorp Industries
Task: Grant job-level access permissions for developers on Jenkins

## Overview
The DevOps team at xFusionCorp Industries was tasked with providing 
two newly onboarded developers access to an existing Jenkins job named Packages. 
Each developer requires specific permissions as outlined below.

## User Access Requirements
User	Password	Permissions
sam	sam@pass12345	Build, Configure, Read
rohan	rohan@pass12345	Build, Cancel, Configure, Read, Update, Tag

## Step-by-Step Implementation
1. Access Jenkins Dashboard

Click the Jenkins button on the top bar.

Login using:

Username: admin
Password: Adm!n321

2. Verify or Install Required Plugin

To enable job-level access control, ensure the Role-based Authorization Strategy or Project-based Matrix Authorization Strategy plugin is installed.

Steps:

Go to Manage Jenkins → Plugins → Available plugins

Search for and install:

  - Role-based Authorization Strategy or
  - Matrix Authorization Strategy

After installation, select Restart Jenkins when installation is complete and no jobs are running.

If the UI becomes unresponsive after restart, refresh your browser page.

3. Configure Global Security

Navigate to Manage Jenkins → Configure Global Security.

Under Authorization, choose:

Project-based Matrix Authorization Strategy

Ensure the admin user retains all permissions.

Save the configuration.

4. Enable Project-Based Security for the Job

Go to the Jenkins Dashboard → open Packages job.

Click Configure on the left sidebar.

Scroll to the Enable project-based security checkbox and enable it.

Under the security matrix, check:

Inherit permissions from parent ACL

5. Assign User Permissions
For user sam

Grant the following:

Build

Configure

Read

For user rohan

Grant the following:

Build

Cancel

Configure

Read

Update

Tag

6. Save and Verify Access

Click Save to apply the configuration.

Log out from the admin account.

Log in as:

sam → Verify limited permissions.

rohan → Verify extended permissions.

## Verification Notes

Ensure inheritance is enabled under job security settings.

Confirm each user can only perform actions based on assigned permissions.

Avoid modifying any other job configurations.

Document your work using:

Screenshots of Jenkins UI steps, or

A Loom screen recording (https://loom.com
).

## Outcome
Both developers now have secure, job-specific access to the Packages job, following best practices for least privilege and project-based authorization.

