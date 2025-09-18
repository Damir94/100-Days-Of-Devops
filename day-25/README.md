# Nautilus Blog Repo – A Little Git Adventure 🚀

This week, the Nautilus DevOps team rolled up their sleeves to make some changes to the blog repository. 
Nothing too fancy — just a small but important exercise in branch management, adding new content, 
and making sure everything flows smoothly back into master.

*The Challenge*
Here’s what we were asked to do:
 - Spin up a new branch called xfusion off of master.
 - Drop in a shiny new file: /tmp/index.html.
 - Commit that change and send it up to the remote repo.
 - Finally, merge it all back into master and keep both branches in sync with origin.

*Step 1: Starting on master*
we hopped into the repo and made sure master was fresh:
 - $ cd /usr/src/kodekloudrepos/blog
 - $ git checkout master
 - $ git pull origin master

*Step 2: Create and switch to new branch xfusion*
 - git checkout -b xfusion

*Step 3: Copy the index.html file into the repo*
 - cp /tmp/index.html .

*Step 4: Stage and commit the file*
 - git add index.html
 - git commit -m "Add index.html file to xfusion branch"
 - git push origin xfusion

 *Step 5: Switch back to master and merge*
  - git checkout master
  - git merge xfusion
  - git push origin master

<img width="1515" height="754" alt="Screenshot 2025-09-18 at 6 00 45 AM" src="https://github.com/user-attachments/assets/29f9b17f-415d-468f-9f90-82befc594e56" />

# The Outcome 
At the end of this little adventure:
 - index.html is now part of the repo.
 - Both xfusion and master branches exist and are pushed to origin.
 - We got some hands-on practice with a clean branch → commit → merge workflow.
