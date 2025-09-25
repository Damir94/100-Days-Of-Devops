# Day 32 of 100 Days of DevOps

# Rebasing Feature Branch with Master in Git

Recently, we had a scenario in the **Nautilus application development project** where one of our developers was working on a `feature` branch while the `master` branch had already received new commits. The requirement was clear:

- Keep the `feature` branch updated with `master`.  
- Do **not** lose any in-progress work.  
- Avoid extra merge commits (i.e., no `git merge`).  

The solution was to use **Git Rebase**.

---

## Steps Taken

1. Navigated to the repository:
   ```bash
   cd /usr/src/kodekloudrepos/news
2. Switched to the feature branch:
    ```bash
    git checkout feature
3. Fetched the latest changes from the remote:
   ```bash
   git fetch origin
4. Rebasing the feature branch on top of master:
   ```bash
   git rebase origin/master
5. Once rebase was successful, we force pushed the updated branch:
    ```bash
    git push origin feature --force

Outcome:
 - The feature branch now includes all latest updates from master.
 - The commit history remains linear and clean with no extra merge commits.
 - Developer progress is preserved without losing any work.
 - This is a classic use case of Git Rebase for keeping branches tidy and up to date.

<img width="1405" height="541" alt="Screenshot 2025-09-24 at 9 16 57 PM" src="https://github.com/user-attachments/assets/ac3e10d2-fb89-403a-8584-6d780a03370c" />
