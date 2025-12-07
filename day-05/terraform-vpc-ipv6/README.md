# VPC With IPv6 Using Terraform
This document explains how to create an AWS VPC with full IPv6 support using Terraform. 
It is suitable for inclusion in a README.md or technical blog. 
The goal is to provide clear reference documentation for engineers provisioning dual-stack (IPv4 + IPv6) infrastructure on AWS.

## Overview
This configuration provisions an AWS VPC that supports both IPv4 and IPv6 addressing. It includes:
  - A VPC with an auto-assigned IPv6 CIDR block
  - Two public subnets configured with IPv4 and IPv6 CIDRs
  - An Internet Gateway
  - Optional Egress-Only Internet Gateway for IPv6 outbound traffic
  - Public route tables supporting IPv4 and IPv6
  - Subnet associations
This setup enables resources such as EC2 instances to receive public IPv4 and IPv6 addresses and be reachable over both protocols.

## Why Use IPv6 in AWS
AWS supports IPv6 natively across most services. Choosing to enable IPv6 provides:
  - A globally unique, massively scalable IP address space
  - Direct public access without NAT (when desired)
  - Reduced reliance on IPv4 exhaustion
  - Simplified connectivity for global and distributed workloads
  - Better performance in dual-stack networks where clients prefer IPv6
For modern cloud architectures, enabling IPv6 is becoming a best practice, especially for internet-facing applications.

## Architecture Summary
The Terraform code provisions the following components:

1. VPC

A single VPC with a /16 IPv4 CIDR and an auto-allocated /56 IPv6 range.

2. Subnets

Two public subnets using /24 IPv4 CIDRs and /64 IPv6 CIDRs.
Each subnet is automatically assigned IPv6 addresses for launched instances.

3. Internet Gateway

Allows outbound and inbound connectivity over both IPv4 and IPv6.

4. Egress-Only Internet Gateway (optional)

Provides outbound-only IPv6 traffic for private subnets (included for completeness, but not actively used in this minimal example).

5. Routes

A public route table directs:

0.0.0.0/0 → Internet Gateway (IPv4)

::/0 → Internet Gateway (IPv6)

6. Associations

Both public subnets are connected to the public route table.

## Terraform Code
Below is the complete Terraform configuration:
```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_vpc" "main" {
  cidr_block                       = "10.0.0.0/16"
  assign_generated_ipv6_cidr_block = true

  tags = {
    Name = "ipv6-vpc"
  }
}

resource "aws_internet_gateway" "igw" {
  vpc_id = aws_vpc.main.id

  tags = {
    Name = "vpc-igw"
  }
}

resource "aws_egress_only_internet_gateway" "egress_igw" {
  vpc_id = aws_vpc.main.id
}

resource "aws_subnet" "public_a" {
  vpc_id                  = aws_vpc.main.id
  cidr_block              = "10.0.1.0/24"
  ipv6_cidr_block         = cidrsubnet(aws_vpc.main.ipv6_cidr_block, 8, 0)
  map_public_ip_on_launch = true

  tags = {
    Name = "public-a"
  }
}

resource "aws_subnet" "public_b" {
  vpc_id                  = aws_vpc.main.id
  cidr_block              = "10.0.2.0/24"
  ipv6_cidr_block         = cidrsubnet(aws_vpc.main.ipv6_cidr_block, 8, 1)
  map_public_ip_on_launch = true

  tags = {
    Name = "public-b"
  }
}

resource "aws_route_table" "public" {
  vpc_id = aws_vpc.main.id

  tags = {
    Name = "public-rt"
  }
}

resource "aws_route" "ipv4_internet" {
  route_table_id         = aws_route_table.public.id
  destination_cidr_block = "0.0.0.0/0"
  gateway_id             = aws_internet_gateway.igw.id
}

resource "aws_route" "ipv6_internet" {
  route_table_id              = aws_route_table.public.id
  destination_ipv6_cidr_block = "::/0"
  gateway_id                  = aws_internet_gateway.igw.id
}

resource "aws_route_table_association" "public_a" {
  subnet_id      = aws_subnet.public_a.id
  route_table_id = aws_route_table.public.id
}

resource "aws_route_table_association" "public_b" {
  subnet_id      = aws_subnet.public_b.id
  route_table_id = aws_route_table.public.id
}
```
