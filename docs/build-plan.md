# Build Plan — AWS Highly Available Web App (SAA-ready)

## Goal
Deploy a production-style, highly available web app reference architecture on AWS using core services and best practices.

## Target Architecture (2 AZs)
**User -> (Route 53 optional) -> ALB (public subnets) -> EC2 Auto Scaling Group (private subnets) -> RDS (private subnets)**  
Supporting: NAT Gateway, Security Groups, IAM, CloudWatch, VPC endpoints (optional).

---

## Phase 0 — Naming + Region + AZs
- Pick 1 region (ex: us-east-1 or us-west-2)
- Use **2 Availability Zones** (AZ-a, AZ-b)
- Tag everything:
  - `Project=AWS-ha-webapp`
  - `Env=dev`
  - `Owner=Lou`

---

## Phase 1 — VPC + Networking
### 1. VPC
- Create VPC: `10.0.0.0/16`
- Enable DNS hostnames + DNS resolution

### 2. Subnets (2 AZs)
Create 4 subnets:
- **Public Subnet A** (ALB + NAT A) — ex `10.0.1.0/24`
- **Public Subnet B** (ALB + NAT B) — ex `10.0.2.0/24`
- **Private App Subnet A** (EC2) — ex `10.0.11.0/24`
- **Private App Subnet B** (EC2) — ex `10.0.12.0/24`

(Optional later: separate private DB subnets.)

### 3. Internet Gateway + Public Route Table
- Attach **Internet Gateway (IGW)** to VPC
- Public route table:
  - `0.0.0.0/0 -> IGW`
- Associate public subnets to this route table

### 4. NAT Gateway + Private Route Table
- Create **NAT Gateway** in each public subnet (best practice), or 1 NAT to save cost
- Private route table:
  - `0.0.0.0/0 -> NAT`
- Associate private app subnets to this private route table

---

## Phase 2 — Security (SAA must-know)
### Security Groups (SGs)
1) **ALB-SG**
- Inbound: `80/443` from `0.0.0.0/0`
- Outbound: to **App-SG** on app port (ex `80`)

2) **App-SG (EC2)**
- Inbound: app port (ex `80`) **ONLY from ALB-SG**
- Outbound: allow to DB-SG (ex `3306/5432`) + outbound internet via NAT

3) **DB-SG (RDS)**
- Inbound: DB port **ONLY from App-SG**
- Outbound: default (fine)

### No SSH from the internet
- Preferred: **SSM Session Manager**
- If SSH is required (not preferred), lock it down to your IP and use a bastion (extra).

---

## Phase 3 — Compute Layer (EC2 + Auto Scaling)
### Launch Template
- Amazon Linux 2023 (or AL2)
- Instance type: t3.micro (dev)
- IAM role: allow SSM + CloudWatch agent (minimal)
- User Data:
  - Install web server (nginx/httpd)
  - Serve a simple page showing instance ID + AZ (proves HA)

### Auto Scaling Group (ASG)
- Subnets: **private app subnets (A + B)**
- Desired: 2, Min: 2, Max: 4
- Health checks: EC2 + ELB
- Scaling policy: CPU target tracking (ex 50%)

---

## Phase 4 — Load Balancer (ALB)
- Create ALB in **public subnets (A + B)**
- Listener: HTTP :80 (optional HTTPS later)
- Target group:
  - Type: instance
  - Port: 80
  - Health check path: `/`
- Attach ASG to target group

---

## Phase 5 — Database Layer (RDS)
- Engine: PostgreSQL or MySQL (pick one)
- **Multi-AZ enabled**
- Subnet group: private subnets (prefer dedicated DB subnets, but OK for dev)
- Public access: **No**
- Backup retention: 7 days (dev can be lower)
- Storage: gp3
- Use Secrets Manager (optional) or SSM Parameter Store for creds

---

## Phase 6 — Observability + Reliability
- CloudWatch:
  - ALB metrics (5xx, target response time)
  - ASG metrics (CPU, desired vs in-service)
  - RDS alarms (CPU, free storage, connections)
- Enable ALB access logs to S3 (optional)
- Create a couple alarms + dashboard (optional)

---

## Phase 7 — Optional “SAA flex” extras
- Route 53 custom domain -> ALB
- ACM certificate + HTTPS listener
- VPC Endpoints (S3, SSM) to reduce NAT usage
- WAF on ALB
- CloudFront in front of ALB (if you want “global edge” story)

---

## Deliverables (what the repo will include)
- `architecture/README.md` (already started)
- `docs/build-plan.md` (this file)
- `diagrams/` (architecture diagram export)
- `terraform/` (IaC implementation)
- `notes/` (gotchas + exam-relevant callouts)

## Definition of Done
- ALB DNS works and returns the web page
- Two instances across two AZs
- If one instance is terminated, ASG replaces it and app stays up
- RDS is private + Multi-AZ
- Repo reads clean and tells the story end-to-end
