# Copying and Committing index.html to Games Repository

## Project Background
The Nautilus Development Team has initiated a new project under the repository /opt/games.git, which manages the source code for one of their web-based applications.
A sample index.html file was provided on the Jump Host under the /tmp directory for initial setup and commit testing.

This document explains how to copy, add, commit, and push that file to the cloned repository on the Storage Server.

## Environment Details
| Component               | Location / Description                           |
| ----------------------- | ------------------------------------------------ |
| Repository (bare)       | `/opt/games.git`                                 |
| Cloned Repository       | `/usr/src/kodekloudrepos/games` (Storage Server) |
| Sample File             | `/tmp/index.html` (Jump Host)                    |
| User for Git Operations | `natasha`                                        |
| Branch                  | `master`                                         |

## Step-by-Step Procedure
1. Copy the File from Jump Host to Storage Server
Since the repository directory on the storage server is restricted, first copy the file to a temporary location, then move it into the repo.
```bash
# From Jump Host
scp /tmp/index.html natasha@<storage_server>:/tmp/
```
Then, SSH into the storage server:
```bash
ssh natasha@<storage_server>
```
Move the file into the cloned repository:
```bash
sudo mv /tmp/index.html /usr/src/kodekloudrepos/games/
```
Verify that the file exists:
```bash
ls -l /usr/src/kodekloudrepos/games/
```
2. Add the File to the Repository
Switch to the repository directory and ensure you’re on the correct branch:
```bash
cd /usr/src/kodekloudrepos/games
git branch
```
Add the new file to the Git staging area:
```bash
git add index.html
```
3. Commit the File
Create a meaningful commit message:
```bash
git commit -m "Add sample index.html file"
```
4. Push Changes to Remote Repository
Push the changes to the master branch:
```bash
git push origin master
```
## Verification
Run the following commands to confirm that your changes were successfully committed and pushed:
```bash
git log --oneline
```
You should see the latest commit message:

Add sample index.html file

Also, you can verify by checking the remote repository or running:
```bash
git status
```

Expected output:

nothing to commit, working tree clean

## Troubleshooting
  - Error: Permission denied while copying to /usr/src/kodekloudrepos/game
  - Cause: User natasha lacks write permissions to that directory
  - Fix: Copy the file to /tmp first, then move it with sudo mv.

## Summary
  - This task successfully:
  - Copied the sample index.html file from the jump host
  - Added it into the cloned games repository on the storage server
  - Committed and pushed the change to the master branch
The repository is now updated with the initial web page file, completing the setup phase of the Nautilus Games Project.
