# Day 22 of 100 Days of DevOps - Cloning Git Repositories on Storage Servers

**The DevOps team established a new Git repository last week, which remains unused at present. 
However, the Nautilus application development team now requires a copy of this repository on the Storage Server in the Stratos DC. 
Follow the provided details to clone the repository:**

 - The repository to be cloned is located at /opt/games.git
 - Clone this Git repository to the /usr/src/kodekloudrepos directory.
   Perform this task using the natasha user, and ensure that no modifications are made to the repository or existing directories,
   such as changing permissions or making unauthorized alterations.

# Steps:

1. Switch to natasha user:
   - sudo su - natasha

2. Clone the repository from /opt/games.git to /usr/src/kodekloudrepos:
   - cd /usr/src/kodekloudrepos
   - git clone /opt/games.git

 <img width="1491" height="533" alt="Screenshot 2025-09-14 at 8 11 25 PM" src="https://github.com/user-attachments/assets/15651aec-e8fb-4120-b90a-41888a3bf709" />

✅ This will create a working copy of the games.git repository inside /usr/src/kodekloudrepos.
