# Terraform
Terraform code to provision the AWS infrastructure for this project will live here.

This folder will deploy the AWS HA Web App stack.

## What it builds
- VPC with 2 AZs
- Public + private subnets
- IGW + NAT
- ALB
- EC2 Auto Scaling Group
- RDS (Multi-AZ)

## Coming next
- `versions.tf`, `providers.tf`
- networking module
- compute module
- db module
