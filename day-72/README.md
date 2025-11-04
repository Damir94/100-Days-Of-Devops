# Understanding Jenkins Parameterized Jobs — A Hands-On Walkthrough

As part of onboarding a new DevOps Engineer, the team decided to perform a simple but powerful exercise in Jenkins — creating and running a parameterized job.
This activity helps engineers understand how Jenkins handles dynamic input values during builds, a concept that becomes essential in real CI/CD pipelines.

## Objective
The goal was to:
  - Create a parameterized Jenkins job named parameterized-job
  - Configure it to accept user inputs (parameters)
  - Execute a simple shell command that prints these values
  - Validate the job by running it successfully using one of the provided parameters

### Step 1: Accessing Jenkins
We began by logging into the Jenkins dashboard using administrative credentials:

Username: admin
Password: Adm!n321

Once logged in, the Jenkins interface was ready for us to create a new job.

### Step 2: Creating the Job
From the Jenkins dashboard:

We clicked on “New Item.”

Named the job parameterized-job.

Chose the Freestyle project option.

Clicked OK to continue to configuration.

This created the base structure for our Jenkins job.

### Step 3: Adding Parameters

Next, we enabled This project is parameterized to allow input variables during build time.

We then added two parameters:

### String Parameter

Name: Stage

Default Value: Build

This acts as a simple text-based input that can hold any string value (e.g., “Build,” “Test,” “Deploy”).

### Choice Parameter

Name: env

Choices:

Development
Staging
Production


This dropdown-style parameter is ideal for selecting environments or deployment targets.

### Step 4: Adding the Build Script

To make the job do something meaningful with these parameters, we added a Build Step → Execute Shell and inserted the following commands:
```bash
echo "Stage Parameter Value: $Stage"
echo "Environment Parameter Value: $env"
```
This script echoes both parameters, letting us verify that Jenkins is passing them correctly to the build environment.

### Step 5: Running the Job

After saving the configuration, we clicked Build with Parameters from the left sidebar.
We selected:

env = Staging

Left Stage = Build (default)

Then we clicked Build.

### Step 6: Verifying Results

Once the job completed, we opened the Console Output of the build.
It displayed:

Stage Parameter Value: Build
Environment Parameter Value: Staging
Finished: SUCCESS


This confirmed the job worked perfectly — Jenkins had successfully injected and echoed both parameters.

### Troubleshooting Notes
  - If Jenkins required any plugins for parameterized builds, they were installed from Manage Jenkins → Plugins → Available Plugins.
  - After installation, Jenkins was restarted using the Restart Jenkins when installation is complete option.
  - If the Jenkins UI appeared unresponsive during restart, refreshing the page restored functionality.

## Outcome
By the end of this exercise, we had:
  - Created a functional parameterized Jenkins job
  - Defined string and choice parameters
  - Executed a shell build step that used those parameters
  - Successfully verified output for env = Staging
This small exercise laid the groundwork for more advanced Jenkins tasks, such as:
  - Dynamic environment deployments
  - Parameterized build pipelines using Jenkinsfile
  - Automated testing across multiple stages (Build, Test, Deploy)

### Key Takeaway
This simple project demonstrated how parameterized jobs bring flexibility to Jenkins.
They allow teams to reuse the same job definition for multiple environments, configurations, and scenarios — saving time and ensuring consistency across deployments.
