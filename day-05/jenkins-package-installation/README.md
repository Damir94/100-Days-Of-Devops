# Jenkins Job: install-packages

## Overview
The install-packages Jenkins job was created to automate the installation of software packages on the storage server within the Stratos Datacenter.
This job allows DevOps engineers to trigger remote package installations by simply providing the package name as a parameter — no manual SSH needed.

## Configuration Details
1. Jenkins Access

Server: Jenkins (Nautilus Infrastructure under Stratos Datacenter)

Login Credentials:

Username: admin

Password: Adm!n321

2. Job Creation Steps

Log in to Jenkins and click on “New Item”.

Enter the job name install-packages.

Select Freestyle project and click OK.

3. Add Parameter

Enable job parameterization to make the job dynamic.

Check “This project is parameterized”

Click Add Parameter → String Parameter

Name: PACKAGE

Default Value (optional): httpd

Description: Enter the package name to install on the storage server.

4. Add Build Step

Add a build step to execute a remote installation command on the storage server.

Build Step: Execute Shell
```bash
#!/bin/bash
echo "Installing package: $PACKAGE on storage server..."

sshpass -p 'Storag3_pass' ssh -o StrictHostKeyChecking=no natasha@ststor01 \
"sudo yum install -y $PACKAGE"
```

## Notes:

If using an Ubuntu/Debian-based server, replace yum with apt-get.

Replace the username and password as per your infrastructure.

sshpass is used for non-interactive authentication; ensure it’s installed on the Jenkins host.

5. Save and Build

Click Save.

Choose Build with Parameters.

Enter a package name (for example, tree, nginx, or git).

Click Build.

Monitor Console Output for real-time installation logs.

Example Output

When building with parameter nginx, the console output should look similar to:

Installing package: nginx on storage server...
Loaded plugins: fastestmirror
Resolving Dependencies
--> Running transaction check
---> Package nginx.x86_64 1:1.20.1-2.el7 will be installed
...
Complete!

Security Notes

Ensure SSH access from the Jenkins server to the storage server is secure.

Consider switching from sshpass to an SSH key-based authentication for production setups.

Use Credentials Manager in Jenkins to securely store passwords instead of hardcoding.

Benefits

Reduces manual SSH operations for package management.

Ensures consistency across environments.

Easily extensible for batch or conditional installations.
