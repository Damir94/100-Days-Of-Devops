# Creating and Transferring a Compressed Archive for Developer Data

## Overview
The Nautilus storage server within the Stratos Datacenter hosts shared developer data in /data.
As part of a developer request, we need to create a compressed archive of user javed’s data and transfer it to the /home directory for easy access.

This procedure helps maintain organized data handling practices and minimizes the need for direct access to production directories.

## Objective
Perform the following actions on the Storage Server:
  - Create a compressed archive named javed.tar.gz of the directory /data/javed
  - Copy or move the archive to the /home directory

## Steps to Implement
1. Connect to the Storage Server
From the jump host, connect to the Storage Server:
```bash
ssh natasha@ststor01
```
Replace natasha with the correct storage server user if required.

2. Create the Compressed Archive
Use the tar command to create a gzip-compressed archive of /data/javed:
```bash
sudo tar -czf /tmp/javed.tar.gz -C /data javed
```
### Explanation of parameters:
  - tar → archive utility
  - -c → create a new archive
  - -z → compress using gzip
  - -f /tmp/javed.tar.gz → specify the output file name and location
  - -C /data javed → change to /data before archiving the javed directory
The archive javed.tar.gz will be created under /tmp in this example (you can create it directly in /home if preferred).

3. Transfer the Archive to the /home Directory
Now move or copy the archive file to /home:
```bash
sudo mv /tmp/javed.tar.gz /home/
```
or
```bash
sudo cp /tmp/javed.tar.gz /home/
```
Either command is acceptable. Use mv if you want to move the archive, or cp if you want to keep a copy in /tmp.

4. Verify the Archive Transfer
Confirm that the archive exists in /home:
```bash
ls -l /home/javed.tar.gz
```
You should see output similar to:
```ini
-rw-r--r-- 1 root root 12400 Oct 28 12:34 /home/javed.tar.gz
```
(Optional) Verify archive integrity:
```bash
tar -tzf /home/javed.tar.gz
```
This lists the files inside without extracting them.

## Verification Checklist
| Check Item              | Expected Result                                   |
| ----------------------- | ------------------------------------------------- |
| Archive created         | `/tmp/javed.tar.gz` (or similar path) exists      |
| Archive compressed      | File extension `.tar.gz`                          |
| Archive copied to /home | `/home/javed.tar.gz` present                      |
| Content verification    | `tar -tzf` lists `/data/javed` contents correctly |

## Conclusion
By completing these steps, we have:
  - Created a compressed archive javed.tar.gz containing all data from /data/javed
  - Successfully transferred it to the /home directory
  - Ensured compliance with data handling best practices in the Stratos Datacenter
This setup allows developer javed to securely access their data without modifying the live /data structure.
