## Day 28 of 100 Days of DevOps - Pull Request Workflow: Adding The Fox and Grapes Story
 - In this task, we practiced a proper Git workflow to ensure that code is never pushed directly to the master branch. Instead, all new changes are reviewed and approved before being merged.

# Steps Completed
1. Verified Repository and Branches
   - Logged into the storage server as max.
   - Checked the cloned repository in Max’s home directory.
   - Ran git log to review commit history and confirmed:
     - Sarah’s story commits were present.
     - Max’s new story (Fox and Grapes) was committed on the branch story/fox-and-grapes.
       
2. Created Pull Request (PR)
   - Logged into Gitea as max.
   - Opened the repository and created a Pull Request with:
     - Title: Added fox-and-grapes story
     - Source branch: story/fox-and-grapes
     - Destination branch: master
   - This PR ensures the story won’t directly be merged into master without review.

 3. Assigned Reviewer
    - From the PR page, assigned tom as the reviewer to check the story.

4. Code Review and Approval
   - Logged out as max and logged in as tom.
   - Reviewed the PR contents.
   - Approved and merged the PR into master.

Final Outcome
  - The master branch now includes Max’s Fox and Grapes story.
  - Direct pushes to master were prevented.
  - A proper review and approval workflow was enforced.

Key Takeaways
  - Always create a branch for new work.
  - Use Pull Requests to propose merging changes.
  - Assign reviewers for quality assurance.
  - Only after approval, merge changes into master.
  - This workflow keeps the master branch stable, clean, and production-ready

<img width="1163" height="856" alt="Screenshot 2025-09-21 at 9 25 47 PM" src="https://github.com/user-attachments/assets/9a9b0db0-7827-4c64-a367-dfc88ec425ca" />

<img width="1671" height="560" alt="Screenshot 2025-09-21 at 9 25 30 PM" src="https://github.com/user-attachments/assets/b8e264ce-0723-4a0d-bd8a-c7bd0c979294" />
