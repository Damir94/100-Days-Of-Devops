# 100 Days of DevOps - Day 42: Docker Network Setup — macvlan Configuration

## Overview
This document explains how the Nautilus DevOps team created a dedicated Docker macvlan network named beta on App Server 2.
The purpose of this setup is to provide isolated network environments for various application containers that will be deployed later.

## Task Objectives
The following requirements were provided by the project ticket:

1. Create a Docker network named beta.
2. Use the macvlan driver for the network.
3. Assign the subnet: 172.28.0.0/24
4. Assign the IP range: 172.28.0.0/24
5. Perform all tasks on App Server 2 within the Stratos Data Center (DC).

## Background: Why macvlan?
The macvlan driver allows containers to appear as physical devices on the same network as the host.
Each container connected to a macvlan network gets its own unique MAC address and can communicate directly with other devices on the local network, bypassing the Docker bridge.

Benefits include:

 - Improved network isolation and performance.
 - Containers get IPs directly reachable on the physical network.
 - Ideal for scenarios requiring direct L2 network connectivity (e.g., legacy apps, monitoring, or security appliances).

## Steps Performed
### 1. Connect to App Server 2
```bash
  ssh user@appserver2
```
(Replace user with the appropriate username for your environment.)

### 2. Create the Docker Network

The following command was used to create the beta network using the macvlan driver:
```bash
  sudo docker network create -d macvlan \
  --subnet=172.28.0.0/24 \
  --ip-range=172.28.0.0/24 \
  beta
```

### Command Explanation:

-d macvlan → specifies the driver type.

--subnet → defines the network’s IP address range.

--ip-range → sets the range of assignable container IPs.

beta → name of the network.

### 3. Verify Network Creation

List all networks:
```bash
  sudo docker network ls
```

### 4. Inspect Network Details

To confirm the configuration:
```bash
sudo docker network inspect beta
```

<img width="1541" height="891" alt="Screenshot 2025-10-05 at 11 28 28 AM" src="https://github.com/user-attachments/assets/ab036e60-c0bc-4bf7-bc52-5cc889683e4a" />

### Notes

 - macvlan networks can only communicate externally or with other containers on the same macvlan, not with containers on the default bridge network.
 - When deploying containers on this network, ensure your host NIC supports promiscuous mode if external communication is needed.
 - Future containers using this network should include:
```bash
   docker run -d --network beta <image_name>
```

### Conclusion

The beta Docker macvlan network has been successfully configured on App Server 2 as per the project requirements.
This network will be used as a base layer for future application deployments that require isolated, high-performance networking.
