# Terraform AWS VPC Setup
This project demonstrates how to create a VPC with a custom CIDR, a public subnet, and internet connectivity using Terraform on AWS.

## Project Overview
The purpose of this project is to:
 - Provision a Virtual Private Cloud (VPC) in AWS with a custom CIDR block.
 - Create a public subnet within the VPC.
 - Configure an Internet Gateway for outbound internet access.
 - Set up a route table and associate it with the subnet.
This setup is suitable for deploying public-facing resources like web servers.

### Infrastructure Architecture
```nginx
VPC (10.0.0.0/16)
└── Public Subnet (10.0.1.0/24)
    └── Internet Gateway
        └── Route Table -> 0.0.0.0/0 -> IGW
```
Terraform Resources Used

1. Provider
 - aws – to interact with AWS resources.
2. VPC
 - aws_vpc – creates the VPC with CIDR block 10.0.0.0/16.
3. Subnet
 - aws_subnet – creates a public subnet with CIDR block 10.0.1.0/24.
4. Internet Gateway
 - aws_internet_gateway – allows resources in the public subnet to access the internet.
5. Route Table
 - aws_route_table – defines routing rules to send traffic to the Internet Gateway.
 - aws_route_table_association – links the route table to the public subnet.

### Terraform Code to Create a VPC
```hcl
provider "aws" {
  region = "us-east-1"  # Change to your desired region
}

# Create VPC
resource "aws_vpc" "my_vpc" {
  cidr_block           = "10.0.0.0/16"   # Change CIDR as needed
  enable_dns_support   = true
  enable_dns_hostnames = true

  tags = {
    Name = "my-vpc"
  }
}

# Create a public subnet
resource "aws_subnet" "public_subnet" {
  vpc_id                  = aws_vpc.my_vpc.id
  cidr_block              = "10.0.1.0/24"
  map_public_ip_on_launch = true
  availability_zone       = "us-east-1a"

  tags = {
    Name = "my-public-subnet"
  }
}

# Create an Internet Gateway
resource "aws_internet_gateway" "igw" {
  vpc_id = aws_vpc.my_vpc.id

  tags = {
    Name = "my-igw"
  }
}

# Create a route table
resource "aws_route_table" "public_rt" {
  vpc_id = aws_vpc.my_vpc.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.igw.id
  }

  tags = {
    Name = "public-rt"
  }
}

# Associate route table with public subnet
resource "aws_route_table_association" "public_assoc" {
  subnet_id      = aws_subnet.public_subnet.id
  route_table_id = aws_route_table.public_rt.id
}
```

## Explanation

1. aws_vpc → Creates the VPC with the given CIDR.
2. aws_subnet → Creates a subnet inside the VPC.
3. aws_internet_gateway → Allows Internet access.
4. aws_route_table & aws_route_table_association → Routes traffic to the Internet Gateway.

## Steps to Deploy
1. Initialize Terraform:
```bash
terraform init
```
2. Preview what Terraform will create:
```bash
terraform plan
```
3. Apply the configuration:
```bash
terraform apply
```
4. Verify in AWS Console:
 - Check the VPC, Subnet, and Internet Gateway creation.
