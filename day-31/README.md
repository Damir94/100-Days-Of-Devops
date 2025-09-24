## Day 31 of 100 Days of DevOps - Restoring Git Stash in a Production-like Environment

During our recent development work in the Nautilus project, one of the developers had stashed some in-progress changes inside the Git repository located at /usr/src/kodekloudrepos/official. 
Later, we needed to bring back those changes and commit them to the remote repository.

# Here’s how the task was completed:

1. Navigated to the repository:
   - *cd /usr/src/kodekloudrepos/official*

2. Verified the available stashes:
   - *git stash list*

3. Applied the stash:
   - *git stash apply stash@{1}*
     
4. Staged the changes:
   - *git add .*

5. Committed the changes:
   - *git commit -m "Restored changes from stash@{1}"*

6. Finally, pushed the updates to the origin:
   - *git push origin HEAD*

# This ensured that the previously stashed work was safely restored, committed, and made available for the team in the remote repository.

<img width="1476" height="864" alt="Screenshot 2025-09-24 at 6 05 20 AM" src="https://github.com/user-attachments/assets/7f6009d6-b8a6-42e4-aee2-fd9d9e9f44fb" />
