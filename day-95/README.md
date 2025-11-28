# 100 Days of Devops - Day 95: Nautilus DevOps AWS Migration – Terraform Security Group Setup

As part of the Nautilus DevOps team's ongoing cloud migration strategy, we are transitioning key infrastructure components to AWS using an incremental approach. 
Instead of performing a massive one-time migration, the team has broken down the process into smaller, manageable phases. This method ensures minimal disruption, 
better control, and improved visibility across each stage of the migration.
One of the foundational steps in preparing AWS resources for application deployment is the configuration of secure and consistent networking controls. 
In this phase, we focus on creating a Security Group that will be associated with Nautilus application servers.

## Objective
Create an AWS Security Group using Terraform within the default VPC, following the team’s cloud migration standards. 
The Security Group will define controlled access for both application and administrative traffic.

## What We Built
Using Terraform, we provisioned a Security Group named devops-sg that will be used by Nautilus application servers. 
This Security Group includes both application-level access and remote administrative access, ensuring that systems are reachable yet protected.

### Key Requirements Implemented
| Requirement             | Description                               |
| ----------------------- | ----------------------------------------- |
| **Security Group Name** | `devops-sg`                               |
| **Description**         | "Security group for Nautilus App Servers" |
| **VPC**                 | Default VPC                               |
| **Inbound Rule 1**      | HTTP (port 80) from `0.0.0.0/0`           |
| **Inbound Rule 2**      | SSH (port 22) from `0.0.0.0/0`            |
| **Outbound Rules**      | Allow all traffic (default behavior)      |

### Terraform Configuration
Below is the exact Terraform configuration used to create the security group:

```hcl
provider "aws" {
  region = "us-east-1"
}

# Fetch the default VPC
data "aws_vpc" "default" {
  default = true
}

# Create the security group
resource "aws_security_group" "devops_sg" {
  name        = "devops-sg"
  description = "Security group for Nautilus App Servers"
  vpc_id      = data.aws_vpc.default.id

  # Inbound Rule: HTTP (port 80)
  ingress {
    description = "HTTP access"
    protocol    = "tcp"
    from_port   = 80
    to_port     = 80
    cidr_blocks = ["0.0.0.0/0"]
  }

  # Inbound Rule: SSH (port 22)
  ingress {
    description = "SSH access"
    protocol    = "tcp"
    from_port   = 22
    to_port     = 22
    cidr_blocks = ["0.0.0.0/0"]
  }

  # Outbound: Allow all (default)
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = {
    Name = "devops-sg"
  }
}
```

### How to Deploy

Initialize Terraform
```bash
terraform init
```
Preview the resources
```bash
terraform plan
```
Apply the configuration
```bash
terraform apply -auto-approve
```
Once applied, the Security Group will appear in the AWS Console under EC2 → Security Groups.

## Conclusion
This task lays the groundwork for deploying application workloads in AWS as part of the Nautilus DevOps migration initiative. 
By defining networking boundaries and access rules using Terraform, we ensure:
  - Consistency across environments
  - Infrastructure-as-Code compliance
  - Improved security posture
  - Repeatable and automated deployments
As the migration progresses, additional Terraform modules and AWS resources will be integrated to support compute, storage, networking, and deployment pipelines.
