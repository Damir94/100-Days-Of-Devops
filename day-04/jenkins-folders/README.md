# Organizing Jenkins Jobs into Folders – xFusionCorp Industries

## Overview
This document outlines the steps taken by the xFusionCorp DevOps team to organize existing Jenkins jobs into structured folders. 
The goal of this task was to improve job management and maintainability by grouping related Jenkins jobs according to their purpose.

## Objective
  - Create a dedicated folder structure within Jenkins to organize jobs by type.
  - Move existing jobs (httpd-php and services) into the new Apache folder.
  - Ensure all required plugins are installed to support folder-based job organization.

## Prerequisites

Access to the Jenkins UI.

Admin credentials:

Username: admin
Password: Adm!n321


Active Jenkins service running on the server.

## Implementation Steps
Step 1: Log in to Jenkins

Access the Jenkins UI via the web interface.

Enter the provided admin credentials.

Step 2: Install Required Plugin

To enable folder creation, the CloudBees Folder Plugin must be installed.

Actions:

Navigate to Manage Jenkins → Plugins → Available Plugins.

Search for CloudBees Folder Plugin.

Install the plugin and select:

Restart Jenkins when installation is complete and no jobs are running


Wait for Jenkins to restart, then log back in.

Step 3: Create the "Apache" Folder

From the Jenkins dashboard, click New Item.

Enter the name:

Apache


Select Folder as the item type.

Click OK → Save to finalize creation.

Step 4: Move Existing Jobs

Two existing jobs, httpd-php and services, needed to be relocated to the Apache folder.

Actions:

From the Jenkins dashboard, click on each job (httpd-php, services).

In the left sidebar, select Move.

Choose the destination folder:

Apache


Click Move.

Verify that both jobs now appear under the Apache folder.

Note: If the Move option is not visible, install the Job Import Plugin or Job Copy Builder Plugin to enable job migration between folders.

Step 5: Verify Setup

Open the Apache folder in Jenkins.

Confirm that both httpd-php and services jobs are listed inside.

Optionally, run one of the jobs to ensure successful migration.

## Key Learnings
  - CloudBees Folder Plugin enables logical organization of Jenkins jobs into folders.
  - Organizing jobs improves scalability and readability in CI/CD environments.
  - Always restart Jenkins after installing new plugins to ensure stability.

## Documentation & Review
  - Screenshots and/or a Loom screen recording were captured showing
  - Plugin installation and restart confirmation.
  - Creation of the Apache folder.
  - Migration of both jobs into the new folder.
  - These materials are stored for internal documentation and review.

## Result
  - After completing the above steps:
  - A new Apache folder exists in Jenkins.
  - Both httpd-php and services jobs are organized under it.
  - Jenkins job management is now more structured and easier to maintain.
