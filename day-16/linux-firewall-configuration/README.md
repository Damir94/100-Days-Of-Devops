# Configuring Firewall Rules for Backup Utility Web UI – Nautilus Backup Server

## Overview
The Nautilus system admins team recently deployed a web UI application for their backup utility on the Nautilus Backup Server in the Stratos Datacenter.
This web application operates on port 3004 (TCP) and needs to be accessible externally.
Since firewalld is active on the server, appropriate firewall rules must be configured to allow inbound traffic to this port.

## Task Objective
  - Allow all incoming TCP connections on port 3004.
  - Ensure the firewalld zone is set to public.
  - Apply the configuration without restarting the system.

## Implementation Steps
1. Verify Firewall Status

Check if firewalld is running:
```bash
sudo systemctl status firewalld
```
Expected output should show the service as active (running).

2. Confirm Active Zone

List all active zones:
```bash
sudo firewall-cmd --get-active-zones
```

Typical output:
```cpp
public
  interfaces: eth0
```
If the active zone isn’t public, you can specify it manually using --zone=public in subsequent commands.

3. Add Rule to Allow Port 3004/TCP

Run the following command to permanently allow incoming traffic on port 3004/tcp in the public zone:
```bash
sudo firewall-cmd --zone=public --add-port=3004/tcp --permanent
```
4. Reload the Firewall to Apply Changes

Reload firewalld for the new rule to take effect:
```bash
sudo firewall-cmd --reload
```
5. Verify the Rule

Confirm that the port has been successfully added:
```bash
sudo firewall-cmd --zone=public --list-ports
```
Expected output:
```bash
3004/tcp
```
## Outcome
  - The backup utility web UI running on port 3004/tcp is now accessible externally.
  - The configuration was applied under the public zone of firewalld.
  - The system continues operating securely without requiring a reboot.
