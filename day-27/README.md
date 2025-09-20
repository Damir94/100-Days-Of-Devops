## Day 26 of 100 Days of DevOps - Git Commit Revert Task

# Task Overview
The Nautilus application development team reported issues with the latest changes pushed to the repository located at:
 - /usr/src/kodekloudrepos/demo
They requested the DevOps team to revert the latest commit (HEAD) and roll the repository back to the previous commit. The previous commit was the initial commit, so the goal was to undo the latest changes while keeping the repo clean

*Steps Performed*
# 1. Navigated to the repository:
 - cd /usr/src/kodekloudrepos/demo
   
# 2. Reverted the latest commit (HEAD):
Instead of deleting history, we used git revert to create a new commit that undoes the changes introduced by the last commit.
 - git revert -n HEAD
 - git commit -m "revert demo message"
 - 
*-n (no-commit) stages the changes without committing immediately.*
*Then, we committed with the required lowercase message: revert demo message*

# 3. Verified the commit history:
 - git log --oneline -2
 - 
Output showed:
 - Top commit → revert demo message
 - Below it → the original initial commit

# Outcome
 - The repository was successfully reverted to the initial commit state.
 - A new commit with the message "revert demo message" was created to clearly document the action.
 - The commit history remains clean, and no commits were deleted or lost.

# Key Takeaway
Using git revert is safer than git reset for production and team workflows because:
 - It preserves commit history.
 - It provides a clear, trackable commit showing when and why a change was undone.

<img width="1520" height="715" alt="Screenshot 2025-09-20 at 2 46 02 PM" src="https://github.com/user-attachments/assets/4ae7c3ae-7192-4cfd-9e42-d73694c27613" />
