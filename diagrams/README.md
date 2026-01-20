# Diagrams
Architecture diagrams for the project will live here (PNG/SVG).
# Diagrams

This folder contains exports of the architecture diagram used in this project.

## Files
- `ha-webapp.png` — primary diagram (exported)
- `ha-webapp.drawio` — editable source (optional)

## Diagram Notes
- 2 AZs
- Public subnets: ALB + NAT
- Private subnets: EC2 ASG + RDS
- SG flow: Internet -> ALB -> EC2 -> RDS
