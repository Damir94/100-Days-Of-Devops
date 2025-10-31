# Repository Cloning Task — Nautilus Project

## Overview
As part of the Nautilus Application Development project, the DevOps team at xFusionCorp Industries 
initialized a new Git repository to support upcoming application development. To facilitate access 
for the development team, this repository needs to be cloned onto the Storage Server within the Stratos Datacenter.
This document provides a detailed explanation of how the repository cloning process was executed.

## Objective
Clone the existing Git repository from /opt/cluster.git to the directory /usr/src/kodekloudrepos using the natasha user account.

Important conditions:
  - Do not modify repository contents or permissions.
  - Ensure all operations are performed as the natasha user.
  - Maintain the original structure and access integrity.

## Environment Details
  - Repository Source: /opt/cluster.git
  - Destination Directory: /usr/src/kodekloudrepos
  - User Account Used: natasha
  - Server: Storage Server (Stratos DC)
  - Version Control System: Git

## Steps Performed
1. Switch to the natasha User

To ensure correct ownership and permissions, all operations were carried out under the natasha user.
```bash
sudo su - natasha
```
2. Create the Target Directory

The destination directory /usr/src/kodekloudrepos was verified and created if it did not already exist.
```bash
mkdir -p /usr/src/kodekloudrepos
```
3. Navigate to the Target Directory
```bash
cd /usr/src/kodekloudrepos
```
5. Clone the Repository

The repository located at /opt/cluster.git was cloned into the working directory.
```bash
git clone /opt/cluster.git
```
This command created a new folder named cluster inside /usr/src/kodekloudrepos.

5. Verify the Clone

To confirm that the repository was cloned successfully and remains unmodified:
```bash
cd /usr/src/kodekloudrepos/cluster
git status
```

Output should indicate a clean working tree:
```pgsql
On branch master
nothing to commit, working tree clean
```
## Verification Checklist
| Checkpoint                                 | Description                                 | Status |
| ------------------------------------------ | ------------------------------------------- | ------ |
| Repository cloned by `natasha`             | Ensures correct user ownership              | ✅      |
| Repository source path `/opt/cluster.git`  | Matches provided location                   | ✅      |
| Destination path `/usr/src/kodekloudrepos` | Successfully created and contains the clone | ✅      |
| No file or permission changes              | Verified post-cloning                       | ✅      |
| Git repository status clean                | Confirmed with `git status`                 | ✅      |

## Notes
  - The cloning process did not alter file permissions or repository content.
  - The cloned repository maintains full commit history and remote origin references.
  - Any future commits or pushes must be performed under authorized credentials following the organization’s Git workflow policies.

## Summary
  - This task successfully cloned the cluster.git repository to the /usr/src/kodekloudrepos directory using the natasha user.
  - The setup now enables the Nautilus development team to begin active development while ensuring compliance with security and access control standards.
