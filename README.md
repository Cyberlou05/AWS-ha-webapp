# AWS-ha-webapp
# AWS Highly Available Web App (Reference Architecture)

## Goal
Demonstrate a production-style, highly available AWS architecture using core services and best practices (aligned with AWS SAA).

## Architecture (What this includes)
- VPC across 2 Availability Zones
- Public subnets: Application Load Balancer
- Private subnets: EC2 Auto Scaling Group
- Multi-AZ RDS (database)
- S3 for static assets / backups
- IAM least-privilege roles
- CloudWatch metrics + logs

## What this proves
- You understand VPC design (public vs private subnets, routing)
- You can build scalable, fault-tolerant compute (ALB + ASG)
- You can place data services correctly (RDS in private subnets, Multi-AZ)
- You think about security (SGs, IAM) and observability (CloudWatch)

## Deliverables
- Architecture diagram
- Step-by-step build notes
- Cleanup/teardown checklist
