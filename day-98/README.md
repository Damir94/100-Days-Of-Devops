# 100 Days of Devops - Day 98: Private VPC Infrastructure Deployment – Terraform Documentation

This project provisions a fully private AWS network environment using Terraform. The goal is to help the Nautilus DevOps team deploy isolated compute resources inside a secure Virtual Private Cloud (VPC). The environment includes a private VPC, a private subnet, and an EC2 instance restricted to internal-only access.

Terraform Scripts

Below are the exact Terraform files required for the task.

### main.tf
```hcl
provider "aws" {
  region = "us-east-1"
}

# -------------------------------
# VPC
# -------------------------------
resource "aws_vpc" "datacenter_priv_vpc" {
  cidr_block           = var.KKE_VPC_CIDR
  enable_dns_support   = true
  enable_dns_hostnames = true
  tags = {
    Name = "datacenter-priv-vpc"
  }
}

# -------------------------------
# Subnet
# -------------------------------
resource "aws_subnet" "datacenter_priv_subnet" {
  vpc_id                  = aws_vpc.datacenter_priv_vpc.id
  cidr_block              = var.KKE_SUBNET_CIDR
  map_public_ip_on_launch = false
  tags = {
    Name = "datacenter-priv-subnet"
  }
}

# -------------------------------
# Security Group (Allow only VPC traffic)
# -------------------------------
resource "aws_security_group" "priv_sg" {
  name        = "datacenter-priv-sg"
  description = "Allow traffic only from inside VPC"
  vpc_id      = aws_vpc.datacenter_priv_vpc.id

  ingress {
    description = "Allow all internal traffic from VPC"
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = [var.KKE_VPC_CIDR]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = [var.KKE_VPC_CIDR]
  }

  tags = {
    Name = "datacenter-priv-sg"
  }
}

# -------------------------------
# EC2 Instance (Private)
# -------------------------------
resource "aws_instance" "datacenter_priv_ec2" {
  ami                    = "ami-0c55b159cbfafe1f0" # Amazon Linux 2 (us-east-1 example)
  instance_type          = "t2.micro"
  subnet_id              = aws_subnet.datacenter_priv_subnet.id
  vpc_security_group_ids = [aws_security_group.priv_sg.id]

  tags = {
    Name = "datacenter-priv-ec2"
  }
}
```

### variables.tf
```hcl
variable "KKE_VPC_CIDR" {
  type        = string
  description = "CIDR block for the private VPC"
}

variable "KKE_SUBNET_CIDR" {
  type        = string
  description = "CIDR block for the private subnet"
}
```

### outputs.tf
```hcl
output "KKE_vpc_name" {
  value = aws_vpc.datacenter_priv_vpc.tags["Name"]
}

output "KKE_subnet_name" {
  value = aws_subnet.datacenter_priv_subnet.tags["Name"]
}

output "KKE_ec2_private" {
  value = aws_instance.datacenter_priv_ec2.tags["Name"]
}
```

### How to Deploy

1. Initialize Terraform
```bash
terraform init
```
2. Validate configuration
```bash
terraform validate
```
3. Preview resources Terraform will create
```bash
terraform plan
```
4. Apply changes
```bash
terraform apply
```
### Outputs After Deployment

Terraform will return:

KKE_vpc_name

KKE_subnet_name

KKE_ec2_private

These help verify the infrastructure was created successfully.

### Security Notes

No public IPs are created.

EC2 instance is not internet accessible.

All traffic is restricted to 10.0.0.0/16.

Access requires internal routing, SSM Session Manager, or a bastion host.

### Conclusion
This Terraform configuration gives the Nautilus DevOps team a fully private VPC environment suitable for secure internal workloads. It provides a strong foundation for future cloud expansion with isolated networking and controlled access.
