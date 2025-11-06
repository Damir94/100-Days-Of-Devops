# Synchronizing Timezone Across Nautilus Application Servers – Stratos Datacenter

## Overview
During the daily standup, it was observed that the timezone configuration across the Nautilus Application Servers in the Stratos Datacenter was inconsistent.
To maintain uniformity for log timestamps, cron jobs, and monitoring systems, all application servers must be synchronized to match the local datacenter timezone.

## Target Timezone

Datacenter Timezone: Asia/Khandyga

## Task Objective

Update and synchronize the timezone settings across all Nautilus Application Servers to Asia/Khandyga.

Affected Servers:
  - App Server 1
  - App Server 2
  - App Server 3

## Implementation Steps
1. Verify Current Timezone

Check the current timezone setting on each server:
```bash
timedatectl
```
The output may show an incorrect timezone such as:
```pgsql
Time zone: UTC (UTC, +0000)
```
2. List Available Timezones (Optional)

To confirm the desired timezone exists:
```bash
timedatectl list-timezones | grep Khandyga
```

You should see:

Asia/Khandyga

3. Set the Correct Timezone

Run the following command on each App Server:

sudo timedatectl set-timezone Asia/Khandyga

4. Confirm the Change

After updating, verify the configuration:
```bash
timedatectl
```
Expected output:

Time zone: Asia/Khandyga (YAKT, +0900)

5. No Reboot Required

The timezone change takes effect immediately and does not require a server restart.

✅ Outcome

All Nautilus Application Servers in the Stratos Datacenter are now synchronized to the Asia/Khandyga timezone, ensuring consistency across logs, applications, and system operations.
