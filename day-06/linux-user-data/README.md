# Filtering and Relocating User Data on App Server 2

## Overview
Due to a data mix-up on Nautilus App Server 2, files belonging to multiple users were unintentionally placed together under /home/usersdata.
To fix this issue, we need to identify and copy only the files owned by a specific user (kirsty) to the designated directory /blog.

This document explains the steps to locate, filter, and copy files owned by kirsty while preserving the directory structure.

## Objective
  - Work on App Server 2 in the Stratos Datacenter.
  - Locate all files (not directories) owned by user kirsty under /home/usersdata.
  - Copy those files to the /blog directory.
  - Maintain the original directory structure during the copy process.

## Steps to Implement
1. Connect to App Server 2
From the jump host, connect to App Server 2:
```bash
ssh tony@stapp02
```
Replace tony with the correct login user if necessary.
You should now be operating on App Server 2.

2. Locate Files Owned by kirsty
Use the find command to identify files (excluding directories) owned by user kirsty:
```bash
sudo find /home/usersdata -type f -user kirsty
```
Explanation:
  - /home/usersdata → the target directory to search
  - -type f → limits results to files only
  - -user kirsty → filters files owned by the user kirsty
This ensures that only kirsty’s files are located, excluding directories and other users’ data.

3. Copy Files to /blog While Preserving Structure
Use the following command:
```bash
sudo find /home/usersdata -type f -user kirsty -exec cp --parents {} /blog \;
```
Explanation:
 - -exec cp --parents {} /blog \; → copies each file to /blog while keeping its directory structure intact relative to /home/usersdata.

Example:
  - If a file is /home/usersdata/reports/2023/file.txt
  - it will be copied as /blog/home/usersdata/reports/2023/file.txt.
The --parents flag is key to preserving the full directory tree.

4. Verify the Copied Files
After copying, verify that the files exist in /blog:
```bash
sudo find /blog -type f -user kirsty
```
You should see similar file paths as under /home/usersdata.

5. (Optional) Check Ownership and Permissions
If needed, confirm file ownership:
```bash
sudo ls -lR /blog | grep kirsty
```

Or adjust permissions to match original:
```bash
sudo chown -R kirsty:kirsty /blog
```
## Verification Checklist
| Check Item           | Expected Result                                                |
| -------------------- | -------------------------------------------------------------- |
| Files found          | `find /home/usersdata -type f -user kirsty` lists user’s files |
| Directories excluded | Only files copied, no directories                              |
| Copy complete        | Files exist under `/blog`                                      |
| Structure preserved  | Directory hierarchy under `/blog` matches source               |
| Ownership intact     | Files owned by `kirsty`                                        |

## Conclusion
By completing these steps, we have:
  - Isolated user-specific data for kirsty
  - Copied files to /blog while preserving directory structure
  - Prevented further mix-ups and improved data segregation and security on App Server 2
This action aligns with xFusionCorp Industries’ standards for data hygiene and structured recovery.
