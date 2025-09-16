# Day 23 - Forking a Git Repository in Gitea (KodeKloud Lab)
## Scenario:
The Nautilus project uses a Gitea Git server for collaboration. A new developer, Jon, needs to start working on a project. To contribute safely without affecting the main repository, he must fork an existing repository.

This lab demonstrates how to navigate a Git web UI, fork a repository, and set up proper remotes; these are essential skills for DevOps engineers working in collaborative, team-based environments.

## Lab Environment & Credentials
•	Platform: KodeKloud Lab

•	Gitea access: Click the Gitea UI button in the lab top bar

•	Login Credentials:

o	Username: jon

o	Password: Jon_pass123

**Business Value: Forking ensures that new contributors can work independently, preserving the integrity of the main project while allowing collaborative development.**

## Step-by-Step Implementation
1️. Open Gitea UI

•	Click the Gitea UI button at the top of the lab page.

2️. Log in as Jon

•	Username: jon

•	Password: Jon_pass123

•	Confirm successful login by viewing the Jon dashboard/avatar.

3. Locate the repository

•	Go to Explore → Repositories

•	Search for sarah/story-blog

•	Click to open the repository page.

<img width="1898" height="409" alt="Screenshot 2025-09-16 at 10 35 15 AM" src="https://github.com/user-attachments/assets/76d74153-4000-42f4-a627-cd7cd3e91867" />


4️. Fork the repository

•	Click the Fork button (usually top-right).

•	In the dialog, select jon as the destination/owner.

•	Confirm the fork.

<img width="1902" height="465" alt="Screenshot 2025-09-16 at 10 37 10 AM" src="https://github.com/user-attachments/assets/b65ecdd6-b820-47e7-ad27-4b24b318003b" />

<img width="1871" height="531" alt="Screenshot 2025-09-16 at 10 37 49 AM" src="https://github.com/user-attachments/assets/21427c3e-ef57-4e48-9ce4-8e8eec7557ad" />


**Business Benefit: Forking enables isolated development, preventing accidental changes to the main repo.**

5️. Verify the fork

•	The forked repository should now be under jon/story-blog.

•	Confirm the repository URL and contents.

<img width="1905" height="515" alt="Screenshot 2025-09-16 at 10 38 08 AM" src="https://github.com/user-attachments/assets/cae712e7-7ef5-485f-adb1-f531a8792e8c" />

## Key Learnings
• Navigated Gitea UI and performed repository forking.

•	Set up origin and upstream remotes.

•	Practiced safe contribution workflows to avoid impacting main code.

## Business & DevOps Relevance
•	Business Needs: Enables new team members to contribute safely without disrupting production code.

•	DevOps Impact:

1. Supports collaborative development.

2. Ensures traceable, auditable code changes.

3. Prepares developers for CI/CD pipelines and code reviews.
