# Enable High Availability & Auto Scaling for FastAPI Application on AWS EC2

## Project Overview

This project demonstrates how to deploy a highly available FastAPI application using the following AWS services:

- Amazon EC2
- Amazon Machine Image (AMI)
- Launch Template
- Application Load Balancer (ALB)
- Target Group
- Auto Scaling Group (ASG)

The goal is to ensure that the application remains available even if one instance fails and automatically scales based on demand.

---

# Architecture

```
                    Internet
                        │
                        ▼
            Application Load Balancer
                        │
                        ▼
               Target Group (HTTP:3000)
                        │
                        ▼
              Auto Scaling Group (ASG)
                        │
          ┌─────────────┴─────────────┐
          ▼                           ▼
   EC2 Instance (AZ-1)         EC2 Instance (AZ-2)
        FastAPI                    FastAPI
        Port 3000                  Port 3000
```

---

# Prerequisites

Before starting this project, ensure you have:

- AWS Account
- Existing VPC
- Two Private Subnets in different Availability Zones
- Internet-facing Application Load Balancer
- FastAPI application running on EC2
- Security Group configured
- IAM Role (if required)

---

# Existing Environment

Backend Server

- Ubuntu 24.04
- FastAPI
- Uvicorn
- Application running on Port **3000**

Verify:

```bash
curl http://localhost:3000/
```

Output

```json
{
    "message":"Hello from Backend Server",
    "server":"Private Server"
}
```

Verify service

```bash
systemctl status backend
```

Verify listening ports

```bash
sudo ss -tulnp
```

Expected Output

```text
LISTEN 0 2048 0.0.0.0:3000
```

---

# Step 1 - Create an AMI

## Why?

The Auto Scaling Group launches new EC2 instances from an AMI.

The AMI contains

- Operating System
- FastAPI Application
- Python Packages
- Uvicorn Service
- Configuration Files

---

## AWS Console

```
EC2
→ Instances
→ Select Backend Server
→ Actions
→ Image and Templates
→ Create Image
```

### Configuration

| Field | Value |
|---------|------|
| Image Name | backend-fastapi-ami |
| Description | FastAPI Backend AMI |
| Reboot Instance | Yes |

Click

```
Create Image
```

Wait until

```
AMI Status = Available
```

---

# Step 2 - Create Launch Template

Navigate

```
EC2
→ Launch Templates
→ Create Launch Template
```

## Configuration

### Launch Template Name

```
backend-template
```

### Version Description

```
FastAPI Backend Template
```

---

## AMI

Select

```
My AMIs

↓

backend-fastapi-ami
```

---

## Instance Type

```
t2.micro
```

---

## Key Pair

Select

```
Existing Key Pair
```

---

## Network

Do not select any subnet.

Leave

```
Don't include in launch template
```

The Auto Scaling Group will decide where to launch instances.

---

## Security Group

Choose

```
Backend Security Group
```

Required Rules

| Port | Purpose |
|------|----------|
|22|SSH|
|3000|FastAPI|
|80|Optional|
|443|Optional|

---

## Storage

Default

```
8 GB gp3
```

---

Click

```
Create Launch Template
```

---

# Step 3 - Create Target Group

Navigate

```
EC2
→ Target Groups
→ Create Target Group
```

---

## Configuration

Target Type

```
Instance
```

Protocol

```
HTTP
```

Port

```
3000
```

VPC

```
Your Existing VPC
```

Health Check Protocol

```
HTTP
```

Health Check Path

```
/
```

Success Codes

```
200
```

Click

```
Next
```

Register Targets

Select

```
Backend EC2
```

Port

```
3000
```

Click

```
Include as Pending
```

Create Target Group

---

# Step 4 - Configure Application Load Balancer

Navigate

```
EC2
→ Load Balancers
```

Use the existing ALB.

---

## Listener

```
HTTP : 80
```

Forward To

```
FastAPI Target Group
```

Remove the old Target Group if it was using Port 80.

---

# Step 5 - Create Auto Scaling Group

Navigate

```
EC2
→ Auto Scaling Groups
→ Create Auto Scaling Group
```

---

## Basic Details

Group Name

```
backend-asg
```

Launch Template

```
backend-template
```

Version

```
Latest
```

---

## Network

VPC

```
Existing VPC
```

Availability Zones

Select

```
Private Subnet 1
Private Subnet 2
```

Balanced Distribution

```
Balanced Best Effort
```

---

# Step 6 - Attach Load Balancer

Choose

```
Attach Existing Load Balancer
```

Select

```
Target Group
```

Choose

```
fastapi-target-group
```

---

## Health Checks

Enable

```
Elastic Load Balancer Health Checks
```

Grace Period

```
300 Seconds
```

---

# Step 7 - Configure Scaling

Desired Capacity

```
2
```

Minimum Capacity

```
2
```

Maximum Capacity

```
4
```

---

## Scaling Policy

Choose

```
Target Tracking Scaling Policy
```

Metric

```
Average CPU Utilization
```

Target Value

```
70%
```

Cooldown

```
300 Seconds
```

---

## Instance Maintenance Policy

Select

```
No Policy
```

---

## Monitoring

Leave Default

---

Click

```
Create Auto Scaling Group
```

---

# Step 8 - Verify Target Group

Navigate

```
EC2
→ Target Groups
```

Expected

```
Healthy
Healthy
```

No instances should be

```
Unhealthy
```

---

# Step 9 - Verify Auto Scaling

Navigate

```
EC2
→ Auto Scaling Groups
```

Verify

Desired

```
2
```

Current

```
2
```

Healthy

```
2
```

---

# Step 10 - Verify Load Balancer

Open

```
Load Balancer DNS
```

Example

```
http://test-alb-xxxxxxxx.ap-south-1.elb.amazonaws.com
```

Expected Output

```json
{
    "message":"Hello from Backend Server",
    "server":"Private Server"
}
```

---

# Testing High Availability

Terminate one EC2 instance manually.

Navigate

```
EC2
→ Instances
→ Select Backend Instance
→ Terminate
```

Expected Result

- Target becomes unhealthy
- Auto Scaling launches a new EC2 instance
- New instance joins Target Group
- ALB routes traffic to healthy instances
- Application remains accessible

---

# Common Troubleshooting

## Target Group Unhealthy

Possible Reasons

- Wrong Target Group Port
- FastAPI not running
- Security Group blocking traffic
- Wrong Health Check Path

Solution

Verify FastAPI

```bash
curl http://localhost:3000/
```

---

## Check Listening Port

```bash
sudo ss -tulnp
```

Expected

```
0.0.0.0:3000
```

---

## Check Backend Service

```bash
systemctl status backend
```

Expected

```
active (running)
```

---

## Check Health Endpoint

```bash
curl http://localhost:3000/
```

Should return

HTTP Status

```
200 OK
```

---

# Best Practices

- Use Private Subnets for backend servers.
- Place ALB in Public Subnets.
- Keep the application behind the Load Balancer.
- Enable ALB Health Checks.
- Use Launch Templates instead of Launch Configurations.
- Create AMIs only after verifying the application.
- Keep the desired capacity at least **2** for High Availability.
- Enable Auto Scaling policies for production workloads.

---

# AWS Services Used

- Amazon EC2
- Amazon Machine Image (AMI)
- Launch Template
- Application Load Balancer (ALB)
- Target Group
- Auto Scaling Group (ASG)
- Security Groups
- Amazon VPC

---

# Outcome

Successfully implemented:

- High Availability
- Multi-AZ Deployment
- Application Load Balancer
- Launch Template
- Auto Scaling Group
- Target Group
- Automatic Health Checks
- Automatic Instance Replacement
- Automatic Horizontal Scaling
