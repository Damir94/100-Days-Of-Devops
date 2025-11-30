# 100 Day of Devops - Day 96: Nautilus DevOps – Incremental AWS Migration
Task: Provision an EC2 Instance Using Terraform

As part of the Nautilus DevOps team’s phased migration to AWS, this task focuses on provisioning core infrastructure using Infrastructure as Code (IaC). By breaking down migration efforts into smaller, manageable units, the team ensures reliability, repeatability, and reduced operational risk during the transition.

This module covers the creation of an Amazon EC2 instance using Terraform, following the standards and naming conventions defined by the team.

## Objective
Provision a single Amazon EC2 instance in AWS using Terraform with the following requirements:
  - Instance Name: xfusion-ec2
  - AMI: ami-0c101f26f147fa7fd (Amazon Linux 2)
  - Instance Type: t2.micro
  - Create a new RSA Key Pair named: xfusion-k
  - Attach the default security group (automatically provided by the VPC)

## Terraform Configuration
Below is the Terraform code that fulfills all requirements.

1. Provider Configuration
```hcl
provider "aws" {
  region = "us-east-1"
}
```
2. Create an RSA Key Pair
This generates an RSA key and stores the private key locally, while uploading the public key to AWS.
```hcl
resource "tls_private_key" "xfusion_key" {
  algorithm = "RSA"
  rsa_bits  = 4096
}

resource "aws_key_pair" "xfusion_kp" {
  key_name   = "xfusion-kp"
  public_key = tls_private_key.xfusion_key.public_key_openssh
}

# Save the private key locally
resource "local_file" "private_key" {
  content  = tls_private_key.xfusion_key.private_key_pem
  filename = "xfusion-kp.pem"
}
```
3. Launch EC2 Instance
```hcl
resource "aws_instance" "xfusion_ec2" {
  ami           = "ami-0c101f26f147fa7fd"
  instance_type = "t2.micro"
  key_name      = aws_key_pair.xfusion_kp.key_name

  # Attach the default security group
  vpc_security_group_ids = ["default"]

  tags = {
    Name = "xfusion-ec2"
  }
}
```
## How to Deploy

Initialize Terraform:
```bash
terraform init
```
Review the execution plan:
```bash
terraform plan
```
Apply the configuration:
```bash
terraform apply -auto-approve
```
The EC2 instance, RSA key pair, and required resources will now be provisioned automatically in AWS.

## Outcome
By completing this task, the Nautilus DevOps team successfully:
  - Demonstrated IaC-based provisioning using Terraform
  - Ensured consistent instance creation using reusable scripts
  - Followed AWS best practices for key management and tagging
  - Progressed one step further in their cloud migration strategy
