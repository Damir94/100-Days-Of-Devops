# Provisioning an AWS VPC Using Terraform

This guide walks you through provisioning a Virtual Private Cloud (VPC) in AWS us-east-1 using Terraform.
We will create a VPC named datacenter-vpc with an IPv4 CIDR block.

## Project Overview
This Terraform project provisions the foundational networking layer for your AWS infrastructure—a dedicated VPC environment 
where you can later add subnets, route tables, gateways, EC2 instances, and more.

The Terraform configuration lives in the working directory:
```bash
/home/bob/terraform
```
### What This Setup Creates
  - The Terraform script provisions:
  - VPC Name: datacenter-vpc\
  - Region: us-east-1
  - IPv4 CIDR Block: 10.0.0.0/16
  - AWS Provider Configuration
This forms a secure and isolated virtual network in AWS.

### Terraform Configuration
Below is the exact main.tf configuration used:
```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_vpc" "datacenter_vpc" {
  cidr_block = "10.0.0.0/16"

  tags = {
    Name = "datacenter-vpc"
  }
}
```
### Deployment Instructions
Make sure you are inside your Terraform working directory:
```bash
cd /home/bob/terraform
```
1. Initialize Terraform
This downloads the required AWS provider plugin.
```bash
terraform init
```
2. Review the Plan
This previews the changes before applying them.
```bash
terraform plan
```
3. Apply the Configuration
This creates the VPC in AWS.
```bash
terraform apply -auto-approve
```
4. Cleanup (Destroy the VPC)
If you want to remove everything created by this configuration:
```bash
terraform destroy -auto-approve
```
### Notes
  - The CIDR block 10.0.0.0/16 allows for up to 65,536 IP addresses, suitable for multi-tier architectures.
  - Tagging the VPC helps organization and simplifies AWS resource tracking.
  - This is a basic setup—future enhancements can include:
      - Public & private subnets
      - Route tables
      - NAT Gateways
      - Internet Gateways
      - Security groups
     
## Conclusion
This simple Terraform configuration helps you quickly stand up a clean, isolated VPC environment in AWS.
From here, you can expand the infrastructure to support real-world applications and services.

