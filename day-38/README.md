# 100 Days of DevOps - Day 38: # Nautilus Project - Containerized Environment Testing

## Overview
As part of the Nautilus project testing phase, the DevOps team was tasked with preparing a containerized environment.  
The initial requirement was to work with the **BusyBox** image and create a local version for further testing.

## Tasks Completed

### 1. Pulled `busybox:musl` image
We pulled the official BusyBox image (`musl` variant) from Docker Hub to the local App Server 2 in Stratos DC.
```bash
docker pull busybox:musl
```

### 2. Re-tagged image as busybox:local

We created a new tag local for the pulled image so that the same image can be referenced locally with a simplified tag.
```bash
docker tag busybox:musl busybox:local
```

### 3. Verified image tags

We confirmed that both musl and local tags point to the same image ID.
```bash
docker images busybox
```

### Example output:

 - REPOSITORY   TAG     IMAGE ID       CREATED       SIZE
 - busybox      musl    <image_id>     <time>        <size>
 - busybox      local   <image_id>     <time>        <size>

### Notes
 - The busybox:local tag will be used in upcoming testing tasks.
 - No additional configuration was required on App Server 2 beyond Docker being installed and running.

