# AWS Transit Gateway Lab

## Overview

This project demonstrates how to connect multiple Amazon VPCs using **AWS Transit Gateway** to enable private communication between workloads deployed across different networks.

The environment consists of three VPCs connected through a central Transit Gateway. Each VPC contains EC2 instances, and all private instances are reachable through a VPN server deployed in the first VPC.

The objective of this lab is to simulate an enterprise network architecture where multiple isolated VPCs communicate securely without requiring complex VPC peering connections.

---

# Architecture

```
                           Internet
                               |
                        Internet Gateway
                               |
                        Public EC2 (VPN Server)
                               |
                     +----------------------+
                     |      VPC-1           |
                     | 10.10.0.0/16         |
                     |                      |
                     | Public Subnet        |
                     |  VPN Server          |
                     |                      |
                     | Private Subnet       |
                     | Private EC2-1        |
                     +----------+-----------+
                                |
                                |
                   AWS Transit Gateway
               +----------------+----------------+
               |                                 |
               |                                 |
      +--------+--------+               +--------+--------+
      |      VPC-2      |               |      VPC-3      |
      | 10.20.0.0/16    |               | 10.30.0.0/16    |
      |                 |               |                 |
      | Private EC2-2   |               | Private EC2-3   |
      +-----------------+               +-----------------+
```

---

# Objectives

* Create a central AWS Transit Gateway
* Connect three VPCs using Transit Gateway Attachments
* Configure routing between all VPCs
* Allow private communication between EC2 instances
* Enable VPN access to all private instances
* Validate end-to-end connectivity using ICMP (Ping)

---

# Infrastructure

| Resource                    | Quantity |
| --------------------------- | -------: |
| VPCs                        |        3 |
| Transit Gateway             |        1 |
| Transit Gateway Route Table |        1 |
| Transit Gateway Attachments |        3 |
| Public EC2 Instances        |        1 |
| Private EC2 Instances       |        3 |
| Internet Gateway            |        1 |
| VPN Server                  |        1 |

---

# VPC Layout

## VPC-1

* Public Subnet

  * VPN Server (Public EC2)

* Private Subnet

  * Private EC2-1

---

## VPC-2

* Private Subnet

  * Private EC2-2

---

## VPC-3

* Private Subnet

  * Private EC2-3

---

# Implementation

## Step 1 – Create Transit Gateway

A Transit Gateway was created to act as the central networking hub between all VPCs.

### Components

* Transit Gateway
* Transit Gateway Route Table

All VPC attachments were associated with the Transit Gateway Route Table.

---

## Step 2 – Create Transit Gateway Attachments

Created one attachment for each VPC.

| Attachment   | Connected VPC |
| ------------ | ------------- |
| Attachment-1 | VPC-1         |
| Attachment-2 | VPC-2         |
| Attachment-3 | VPC-3         |

Once attached, each VPC became part of the central routing domain.

---

## Step 3 – Configure VPC Route Tables

Each VPC Route Table was updated to forward traffic destined for other VPC CIDR blocks through the Transit Gateway.

### VPC-1 Public Route Table

| Destination | Target           |
| ----------- | ---------------- |
| VPC-2 CIDR  | Transit Gateway  |
| VPC-3 CIDR  | Transit Gateway  |
| 0.0.0.0/0   | Internet Gateway |

---

### VPC-1 Private Route Table

| Destination | Target          |
| ----------- | --------------- |
| VPC-2 CIDR  | Transit Gateway |
| VPC-3 CIDR  | Transit Gateway |

---

### VPC-2 Private Route Table

| Destination | Target          |
| ----------- | --------------- |
| VPC-1 CIDR  | Transit Gateway |
| VPC-3 CIDR  | Transit Gateway |

---

### VPC-3 Private Route Table

| Destination | Target          |
| ----------- | --------------- |
| VPC-1 CIDR  | Transit Gateway |
| VPC-2 CIDR  | Transit Gateway |

---

## Step 4 – Configure Security Groups

To allow connectivity testing, ICMP traffic was enabled between all VPC CIDR ranges.

Inbound Rule

```
Type:
    All ICMP - IPv4

Source:
    VPC-1 CIDR
    VPC-2 CIDR
    VPC-3 CIDR
```

This rule was applied to:

* VPN Server
* Private EC2-1
* Private EC2-2
* Private EC2-3

---

## Step 5 – Verify Subnet Associations

Verified that every subnet was associated with the correct route table.

| Subnet                 | Route Table         |
| ---------------------- | ------------------- |
| Public Subnet (VPC-1)  | Public Route Table  |
| Private Subnet (VPC-1) | Private Route Table |
| Private Subnet (VPC-2) | Private Route Table |
| Private Subnet (VPC-3) | Private Route Table |

---

# Connectivity Validation

After configuration:

* VPC-1 → VPC-2 communication successful
* VPC-1 → VPC-3 communication successful
* VPC-2 → VPC-3 communication successful

Ping tests were performed between all private EC2 instances.

Additionally,

* Connected to the VPN Server
* Accessed every private EC2 through private IP addresses
* Verified end-to-end connectivity

---

# Network Flow

```
Client
   │
   ▼
VPN Server
   │
   ▼
VPC-1
   │
   ▼
Transit Gateway
   │
   ├────────► VPC-2
   │             │
   │             ▼
   │         Private EC2
   │
   └────────► VPC-3
                 │
                 ▼
             Private EC2
```

---

# Key Learnings

* Centralized VPC connectivity using Transit Gateway
* Transit Gateway Attachments
* VPC Route Table configuration
* Transit Gateway Route Table Association
* Security Group configuration for inter-VPC communication
* Private networking between isolated VPCs
* VPN access to private infrastructure
* End-to-end connectivity troubleshooting

---

# Benefits of AWS Transit Gateway

* Centralized network architecture
* Simplified routing management
* Scalable for large environments
* Eliminates full-mesh VPC Peering complexity
* Supports thousands of VPC attachments
* Reduces operational overhead
* Enterprise-grade network design

---

# Result

Successfully built a hub-and-spoke AWS networking architecture using AWS Transit Gateway.

The final environment provides:

* Secure communication between all VPCs
* Centralized routing
* VPN access to private workloads
* End-to-end connectivity across multiple VPCs
* Scalable architecture suitable for enterprise deployments
