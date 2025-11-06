# Configuring Process Limits for nfsuser – Storage Server (Stratos Datacenter)

## Overview
The Storage Server within the Stratos Datacenter has been experiencing performance degradation due to a high number of active processes initiated by the nfsuser account.
To prevent system resource exhaustion and maintain stability, process limits must be applied to this user.

## Objective
Restrict the number of processes that the nfsuser can spawn by configuring both soft and hard limits.

| Limit Type | Value |
| ---------- | ----- |
| Soft Limit | 1024  |
| Hard Limit | 2024  |

## Implementation Steps
1. Verify Current Limits

Before applying new limits, check the current settings for nfsuser:
```bash
su - nfsuser -c "ulimit -u"
```
This will show the current maximum number of user processes (if any limit is set).

2. Configure User Limits

Edit the limits configuration file:
```bash
sudo vi /etc/security/limits.conf
```
Add the following lines at the bottom of the file:
```bash
nfsuser   soft   nproc   1024
nfsuser   hard   nproc   2024
```
Alternatively, you can create a separate file for cleaner management:
```bash
sudo vi /etc/security/limits.d/nfsuser.conf
```
Add the same lines:
```bash
nfsuser   soft   nproc   1024
nfsuser   hard   nproc   2024
```

Recommended: Use /etc/security/limits.d/nfsuser.conf to avoid overwriting global configurations.

3. Apply and Verify the Limits

After saving the configuration, log in as nfsuser:
```bash
su - nfsuser
ulimit -u
```
Expected output:
```bash
1024
```
4. (Optional) Verify with PAM Configuration

Ensure PAM is configured to apply user limits:
```bash
grep pam_limits /etc/pam.d/*
```
You should see lines similar to:
```swift
session required pam_limits.so
```
If missing, add it under /etc/pam.d/common-session and /etc/pam.d/common-session-noninteractive.

## Outcome
  - The soft limit for nfsuser processes is now set to 1024.
  - The hard limit for nfsuser processes is now set to 2024.
  - This configuration will automatically take effect for all new sessions of nfsuser.
  - The change helps stabilize system performance by preventing excessive process creation.
