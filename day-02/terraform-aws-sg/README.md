# Creating a Security Group in AWS Using Terraform

## Overview

This project demonstrates how to use Terraform — an Infrastructure as Code (IaC) tool — to provision a Security Group in AWS under the default VPC in the us-east-1 region.
The goal was to configure a secure and reusable network access control layer that allows only necessary inbound traffic (HTTP and SSH) while permitting all outbound traffic.

## Task Details

Objective
Create a security group named nautilus-sg with the following rules:

 - Inbound Rules:

   - Allow HTTP (port 80) access from anywhere (0.0.0.0/0).
   - Allow SSH (port 22) access from anywhere (0.0.0.0/0).

 - Outbound Rules:
   - Allow all traffic to any destination.

## Understanding the Configuration

### 1. Provider Configuration
We configure Terraform to use AWS as the provider and specify the region.
```hcl
provider "aws" {
  region = "us-east-1"
}
```

### 2. Fetching the Default VPC
Since the task specifies using the default VPC, we use a data source to fetch its ID dynamically:
```hcl
data "aws_vpc" "default" {
  default = true
}
```

### 3. Creating the Security Group
The core resource — aws_security_group — defines our inbound and outbound traffic rules.
```hcl
resource "aws_security_group" "nautilus_sg" {
  name        = "nautilus-sg"
  description = "Security group allowing HTTP and SSH access"
  vpc_id      = data.aws_vpc.default.id
```

### 4. Defining Ingress Rules
Two separate ingress blocks allow web and SSH access:
```hcl
  ingress {
    description = "Allow HTTP traffic"
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    description = "Allow SSH traffic"
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
```

### 5. Defining Egress Rules
Allowing all outbound traffic ensures that instances in this group can communicate externally:
```hcl
  egress {
    description = "Allow all outbound traffic"
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
```

## How to Deploy

### 1. Initialize Terraform
```bash
terraform init
```
This downloads the required provider plugins.

### 2. Validate Configuration
```bash
terraform validate
```
Ensures the syntax and logic are correct.

### 3. Apply Configuration
```bash
terraform apply -auto-approve
```

### 4. Verify in AWS Console
 - Navigate to VPC → Security Groups.
 - Confirm the creation of nautilus-sg with the correct rules.

## Cleanup
To delete all resources created by this configuration:
```bash
terraform destroy -auto-approve
```

## Summary

| Resource Type      | Name        | Description                             |
| ------------------ | ----------- | --------------------------------------- |
| AWS Security Group | nautilus-sg | Allows HTTP & SSH access in default VPC |
| Region             | us-east-1   | AWS region for deployment               |
| Outbound Rule      | All traffic | Allows any outgoing connections         |


## Key Takeaways
 - Terraform allows declarative infrastructure management — define once, deploy repeatedly.
 - Using data sources ensures infrastructure remains dynamic and reusable.
 - Always limit SSH access to specific IPs in production environments for better security.
