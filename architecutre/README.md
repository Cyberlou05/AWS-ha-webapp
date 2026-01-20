# Architecture Overview

This project demonstrates a production-style, highly available web application on AWS aligned with SAA best practices.

## Traffic Flow
User → Route 53 (optional) → ALB (public subnets) → EC2 Auto Scaling Group (private subnets) → RDS (private subnets)

## Core Services
- VPC across 2 Availability Zones
- Public subnets: Application Load Balancer (ALB)
- Private subnets: EC2 instances in an Auto Scaling Group (ASG)
- NAT Gateway for private subnet outbound access
- RDS (Multi-AZ) for relational data
- CloudWatch for metrics/logs/alarms
- IAM least privilege + Security Groups

## High Availability / Resiliency
- Multi-AZ design (subnets, ALB, ASG)
- Health checks + auto-replacement via ASG
- Database high availability via Multi-AZ

## Security
- No SSH from the internet (use SSM Session Manager)
- ALB Security Group allows 80/443 from the internet
- EC2 Security Group allows inbound only from ALB SG
- DB Security Group allows inbound only from EC2 SG
