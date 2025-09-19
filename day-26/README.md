## Project Update – Remote Setup and File Commit

As part of the recent updates, we aligned the `media` repository with the new Git server setup in Stratos DC. Here’s a quick walkthrough of what we accomplished.

### 1. Adding a New Remote
The DevOps team introduced a new remote, so we added it to our local repository.  
We named the remote **dev_media** and pointed it to /opt/xfusioncorp_media.git:

 - cd /usr/src/kodekloudrepos/media
 - git remote add dev_media /opt/xfusioncorp_media.git

### 2. Adding a New File
There was a file index.html located in /tmp. We copied it into the repository and prepared it for commit:
 - cp /tmp/index.html .
 - git add index.html
 - git commit -m "Add index.html file"

### 3. Pushing Changes to Remote
Finally, we pushed the updated master branch to the new remote:
 - git push dev_media master

<img width="1523" height="820" alt="Screenshot 2025-09-19 at 5 46 38 AM" src="https://github.com/user-attachments/assets/3472cbb7-c13c-439e-8404-e6b15c7729a8" />

With these steps completed, the repository is now fully connected to the new remote, and the latest changes are safely versioned in dev_media. This ensures consistency across environments and keeps our workflow in sync with the Git server updates.
