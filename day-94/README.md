# 100 Days of Devops - Day 94: # Nautilus DevOps AWS Migration Documentation

## Overview
The Nautilus DevOps team is embarking on a strategic migration of a portion of its on-premises infrastructure to the AWS cloud. Given the complexity and size of the environment, the team has adopted an incremental migration strategy. Instead of moving all resources at once, they are breaking the process into smaller, manageable tasks that can be tackled systematically.
This phased approach reduces risk, minimizes service interruptions, and enables better oversight across each step of the migration. By segmenting the work, the team can optimize resources, validate each component, and maintain operational stability throughout the transition.

---

## Task Completed: Creating a VPC in AWS Using Terraform

As part of the initial phase of the migration, the team provisioned foundational networking infrastructure in AWS. This serves as the baseline environment for future deployments.

### **Objective**

Create a Virtual Private Cloud (VPC) named **`devops-vpc`** in the **us-east-1** region using Terraform. The Terraform working directory for this task is `/home/bob/terraform`, and all configuration is contained within **one file**, `main.tf`.

### **Why This Task Matters**

A VPC is the core building block of your AWS infrastructure. It allows you to define your own virtual network, including IP addressing, routing, and security. By creating the VPC first, the team establishes a secure, isolated environment in which all cloud resources will reside.

---

## Terraform Configuration

The following Terraform configuration was implemented in the `main.tf` file:

```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_vpc" "devops_vpc" {
  cidr_block = "10.0.0.0/16"
  tags = {
    Name = "devops-vpc"
  }
}
```

### **Key Points**

* **Provider Block**: Specifies AWS as the cloud provider and sets the region.
* **VPC Resource**: Creates a VPC with CIDR block `10.0.0.0/16`, suitable for subdividing into multiple subnets later.
* **Tagging**: Adds an identifying tag to the VPC, which is useful for filtering and cost allocation.

---

## Next Steps

With the VPC in place, the team can proceed to build additional AWS components, such as:

* Public and private subnets
* Internet Gateways and NAT Gateways
* Route tables
* EC2 instances, load balancers, and security groups
* CI/CD integrations for automated provisioning

Each of these stages will follow the same incremental, well-documented approach to ensure a controlled and efficient migration.

---

## Conclusion

This task marks an important first step in the Nautilus DevOps team's journey toward cloud adoption. By carefully planning and executing foundational infrastructure setup, the team ensures a stable and scalable future cloud environment.

Further tasks will continue to build upon this foundation, driving the organization toward a fully operational and optimized AWS-based architecture.
