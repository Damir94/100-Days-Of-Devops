# Creating an Elastic IP (EIP) Using Terraform

This guide explains how to create and optionally associate an Elastic IP (EIP) with an EC2 instance using Terraform. 
An Elastic IP is a static, public IPv4 address that you can attach to resources in AWS. 
It remains constant even if you restart or recreate your EC2 instance.

## main.tf
```hcl
provider "aws" {
  region = "us-east-1"
}

# Create an Elastic IP
resource "aws_eip" "my_eip" {
  vpc = true
}

# (Optional) Output the EIP address
output "eip_public_ip" {
  value = aws_eip.my_eip.public_ip
}
```
## Running the Terraform Deployment

Run the following commands
```bash
terraform init     # Downloads provider plugins
terraform plan     # Shows what will be created
terraform apply    # Deploys the resources
```
