# 100 Days of Devops - Day 98: Private VPC Infrastructure Deployment – Terraform Documentation

This project provisions a fully private AWS network environment using Terraform. The goal is to help the Nautilus DevOps team deploy isolated compute resources inside a secure Virtual Private Cloud (VPC). The VPC, subnet, and EC2 instance created through this configuration are designed to be fully private, meaning they are not exposed to the public internet and can only communicate within the internal network.

##  Overview

This Terraform configuration builds three main components:

A private VPC

Ensures complete isolation from external networks

CIDR block: 10.0.0.0/16

A private subnet

No auto-assigned public IPs

CIDR block: 10.0.1.0/24

A private EC2 instance

Instance type: t2.micro

Located inside the private subnet

Accessible only from within the VPC

All access to the instance is restricted using a security group that allows traffic only from the VPC CIDR block.

##  Architecture Summary

The created infrastructure includes:

datacenter-priv-vpc

A private VPC with internal-only routing.

datacenter-priv-subnet

A subnet that does not assign public IPs on launch.

datacenter-priv-ec2

A compute instance operating fully inside the private network.

Security Group Rules

Inbound and outbound traffic are only allowed from/to the VPC CIDR

No external IP or outside access is permitted

This architecture is ideal for workloads requiring strong isolation, internal-only tools, or backend computation layers.

##  File Structure
.
├── main.tf          # Contains VPC, Subnet, Security Group, EC2 instance resources
├── variables.tf     # Input variables for VPC and Subnet CIDR blocks
└── outputs.tf       # Useful output values for referencing deployed resources

##  Terraform Variables

You must define two variables in variables.tf:

KKE_VPC_CIDR – CIDR block for the VPC

KKE_SUBNET_CIDR – CIDR block for the subnet

Example usage in terraform.tfvars:

KKE_VPC_CIDR   = "10.0.0.0/16"
KKE_SUBNET_CIDR = "10.0.1.0/24"

##  How to Deploy

Follow these steps to provision the infrastructure:

1️⃣ Initialize Terraform
```bash
terraform init
```
2️⃣ Validate configuration
```bash
terraform validate
```
3️⃣ Review the plan
```bash
terraform plan
```
4️⃣ Apply the changes
```bash
terraform apply
```

Terraform will prompt for approval before creating the resources.

##  Terraform Outputs

After deployment, Terraform will display:

KKE_vpc_name – Name of the VPC

KKE_subnet_name – Name of the Subnet

KKE_ec2_private – Name of the EC2 Instance

These outputs help confirm creation and can be referenced in future modules or automation scripts.

## Security Notes

The EC2 instance is fully private and cannot be accessed from the internet.

No public IPs are assigned at any point.

All inbound and outbound traffic is restricted to the 10.0.0.0/16 range.

To access the instance, you must use internal networking (e.g., Bastion host, VPN, SSM Session Manager).

##  Purpose of This Setup

This configuration helps teams:

Build secure internal-only environments

Test private workloads

Deploy backend services

Create isolated environments for sensitive data, internal dashboards, or microservices not intended for public exposure

## Conclusion

This Terraform module demonstrates how to deploy a private VPC, subnet, and EC2 instance with strict internal-only access policies. It serves as a secure foundation for future AWS infrastructure expansion, ensuring proper isolation and network control.
