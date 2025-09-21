# Git Cherry-Pick: Selective Commit Merge
 - In this task, I worked on the Nautilus eCommerce project repository (/opt/ecommerce.git, cloned at /usr/src/kodekloudrepos). The repository had two branches:
    - master – stable production-ready branch
    - feature – where developers are actively working on new updates
 - The requirement was to merge only one specific commit from the feature branch into master, without merging all the in-progress work. The commit message we needed was:
    - Update info.txt

# ⚙️ Steps I Followed

## 1. Go to the repo directory:
 - cd /usr/src/kodekloudrepos/ecommerce

## 2. Check the current branch:
 - git status
 - git checkout master

## 3. Find the commit ID in the feature branch:
 - git log feature --oneline
 - Look for the commit with the message: <commit_id> Update info.txt

## 4. Cherry-pick that commit into master:
 - git cherry-pick <commit_id>

## 5. Resolved conflicts (if any)
 - git add <resolved_files>
 - git cherry-pick --continue

## 6. Pushed changes to remote master
 - git push origin master

# Outcome
 - The commit Update info.txt from the feature branch is now part of the master branch.
 - Other ongoing development work in feature remains untouched.
 - The repository history is clean, and the stable branch only received the needed update.

# Key Takeaway
 - This task highlighted the power of git cherry-pick, which is an essential tool for DevOps engineers and developers.
   It allows you to merge just the changes you need without bringing in the entire branch.
   Perfect for when you want to keep production stable while still applying selective updates.
   
<img width="538" height="311" alt="Screenshot 2025-09-21 at 6 21 08 AM" src="https://github.com/user-attachments/assets/ba14f134-7c4f-4260-8a7e-96c1268ad1b1" />

<img width="1340" height="546" alt="Screenshot 2025-09-21 at 6 09 01 AM" src="https://github.com/user-attachments/assets/7735c370-977a-46c3-abac-3e7d2b0dc476" />

