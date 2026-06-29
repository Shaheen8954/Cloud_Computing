3. HANDS_ON.md

This file contains only the practical implementation.

Start like this

# AWS Transit Gateway Hands-on Lab

## Objective

Connect three VPCs using AWS Transit Gateway and verify communication between all EC2 instances.

---

## Lab Topology

(Add architecture diagram)

---

## Prerequisites

- AWS Account
- Three VPCs
- Four EC2 Instances
- VPN Server

Then

Step 1
## Step 1 — Create Transit Gateway

Navigate to

VPC

↓

Transit Gateways

↓

Create Transit Gateway

Screenshot

/screenshots/01-create-tgw.png

Then explain

Why?
Transit Gateway acts as the central router that connects all VPCs.
Step 2

Create TGW Route Table

Explain

Why?

Step 3

Create Attachments

Screenshot

Explain

Why?

Step 4

Update VPC Route Tables

Show route tables

Explain

Why?

Step 5

Security Groups

Show screenshots

Explain

Why?

Step 6

Associate Route Tables

Explain

Step 7

Ping Test

Private EC2-1

↓

Private EC2-2

↓

Private EC2-3

Include terminal output.

Step 8

VPN Verification

SSH

Ping

Traceroute

Explain the traffic flow.

Troubleshooting

This section is very important.

Example

## Troubleshooting

### Problem

Ping not working

### Cause

Transit Gateway route missing

### Solution

Added VPC CIDR in the route table.

Another

Problem

SSH timeout

Cause

Security Group blocked port 22

Solution

Allowed VPN CIDR.

Recruiters love this section because it shows debugging skills.
