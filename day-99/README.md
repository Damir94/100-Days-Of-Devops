# 100 Day of Devops - Day 99: Secure DynamoDB + IAM Using Terraform
Overview

This Terraform project creates a secure DynamoDB table and configures fine-grained IAM access so only trusted AWS services can read data from the table. 
It automates the provisioning of the table, IAM role, and read-only policy.

## What It Creates
  - DynamoDB Table – xfusion-table with minimal configuration
  - IAM Role – xfusion-role for trusted AWS services
  - IAM Policy – xfusion-readonly-policy granting:
      - dynamodb:GetItem
      - dynamodb:Query
      - dynamodb:Scan
## main.tf
```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }

  required_version = ">= 1.3.0"
}

provider "aws" {
  region = "us-east-1"
}

# -------------------------------
# DynamoDB Table
# -------------------------------
resource "aws_dynamodb_table" "kke_table" {
  name         = var.KKE_TABLE_NAME
  billing_mode = "PAY_PER_REQUEST"
  hash_key     = "id"

  attribute {
    name = "id"
    type = "S"
  }
}

# -------------------------------
# IAM Role
# -------------------------------
resource "aws_iam_role" "kke_role" {
  name = var.KKE_ROLE_NAME

  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Effect = "Allow"
        Principal = {
          Service = "lambda.amazonaws.com" # Example trusted AWS service
        }
        Action = "sts:AssumeRole"
      }
    ]
  })
}

# -------------------------------
# IAM Policy (Read-Only to this table)
# -------------------------------
resource "aws_iam_policy" "kke_readonly_policy" {
  name        = var.KKE_POLICY_NAME
  description = "Read-only access to a specific DynamoDB table"

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Effect   = "Allow"
        Action   = [
          "dynamodb:GetItem",
          "dynamodb:Query",
          "dynamodb:Scan"
        ]
        Resource = aws_dynamodb_table.kke_table.arn
      }
    ]
  })
}

# Attach policy to role
resource "aws_iam_role_policy_attachment" "kke_policy_attach" {
  role       = aws_iam_role.kke_role.name
  policy_arn = aws_iam_policy.kke_readonly_policy.arn
}
```

### variables.tf
```hcl
variable "KKE_TABLE_NAME" {
  description = "Name of the DynamoDB table"
  type        = string
}

variable "KKE_ROLE_NAME" {
  description = "Name of the IAM role"
  type        = string
}

variable "KKE_POLICY_NAME" {
  description = "Name of the IAM policy"
  type        = string
}
```

### outputs.tf
```hcl
output "kke_dynamodb_table" {
  description = "DynamoDB table name"
  value       = aws_dynamodb_table.kke_table.name
}

output "kke_iam_role_name" {
  description = "IAM role name"
  value       = aws_iam_role.kke_role.name
}

output "kke_iam_policy_name" {
  description = "IAM policy name"
  value       = aws_iam_policy.kke_readonly_policy.name
}
```

### terraform.tfvars
```hcl
KKE_TABLE_NAME  = "xfusion-table"
KKE_ROLE_NAME   = "xfusion-role"
KKE_POLICY_NAME = "xfusion-readonly-policy"
```

### How to Deploy
```bash
terraform init
terraform plan
terraform apply
```
