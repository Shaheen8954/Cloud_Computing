# Why start with Inspector?

AWS Inspector is the easiest to understand because it scans your EC2 instances for vulnerabilities. Once it's enabled, GuardDuty and Security Hub become easier to understand because you'll already be familiar with AWS security findings.

# Monitor Amazon EC2 Security using Amazon Inspector, Amazon GuardDuty & AWS Security Hub

---

# Project Overview

This project demonstrates how to secure Amazon EC2 workloads using AWS native security services.

Three AWS security services are configured:

- Amazon Inspector
- Amazon GuardDuty
- AWS Security Hub

These services work together to provide:

- Vulnerability Assessment
- Threat Detection
- Centralized Security Monitoring

---

# Project Objectives

- Configure Amazon Inspector
- Configure Amazon GuardDuty
- Configure AWS Security Hub
- Scan EC2 instances
- Detect vulnerabilities
- Detect malicious activities
- Aggregate security findings
- Monitor AWS Security Posture

---

# AWS Services Used

- Amazon EC2
- IAM
- Amazon Inspector
- Amazon GuardDuty
- AWS Security Hub
- AWS Systems Manager (SSM)

---

# Architecture Diagram

```
                    AWS Account

                    EC2 Instance
                          │
          ┌───────────────┴────────────────┐
          │                                │
          ▼                                ▼
 Amazon Inspector                 Amazon GuardDuty
 Vulnerability Scan              Threat Detection
          │                                │
          └───────────────┬────────────────┘
                          │
                          ▼
                  AWS Security Hub
                          │
              Central Security Dashboard
```

---

# Prerequisites

- AWS Account
- Ubuntu EC2 Instance
- IAM Role attached to EC2

Required IAM Policies

```
AmazonSSMManagedInstanceCore

CloudWatchAgentServerPolicy
```

SSM Agent Installed

CloudWatch Agent Installed

---

# Security Overview

Traditional Security

```
Administrator

↓

Logs into EC2

↓

Runs Updates

↓

Checks Vulnerabilities
```

AWS Native Security

```
EC2

↓

Inspector

↓

GuardDuty

↓

Security Hub

↓

Security Findings
```

---

# Understanding Amazon Inspector

## What is Amazon Inspector?

Amazon Inspector is an automated vulnerability management service.

It continuously scans AWS resources for:

- Software vulnerabilities
- Missing patches
- CVEs
- Network exposure

---

# How Inspector Works

```
EC2

↓

Installed Packages

↓

Amazon Inspector

↓

AWS Vulnerability Database

↓

Findings
```

---

# Inspector Configuration

## Step 1

Open

```
Amazon Inspector
```

Enable Inspector.

---

## Step 2

Enable

- EC2 Scanning
- ECR Scanning (Optional)

---

## Step 3

Inspector automatically discovers EC2 instances.

Dashboard Example

```
Environment Coverage

75%

3/4 Resources
```

---

# Inspector Dashboard Explained

## Environment Coverage

Meaning

```
Total EC2

↓

4

↓

Scanning

↓

3
```

---

## Critical Findings

Shows Critical vulnerabilities.

Example

```
Critical

0
```

---

## Total Findings

Example

```
23 Findings
```

These include:

- Low
- Medium
- High

---

## Findings with Public Exploit

Example

```
7 Findings
```

Meaning

Public exploit code already exists.

---

## Findings with Fix Available

Example

```
17 Findings
```

Meaning

AWS already knows how to remediate these vulnerabilities.

---

## Risk-Based Remediation

Inspector recommends which package should be updated first.

Example

```
python3-pip

↓

8 Vulnerabilities
```

---

# Understanding CVE

CVE

```
Common Vulnerabilities and Exposures
```

Example

```
CVE-2025-XXXX
```

Every vulnerability receives a unique CVE ID.

---

# Amazon GuardDuty

## What is GuardDuty?

GuardDuty is an intelligent threat detection service.

Unlike Inspector,

it detects active malicious activity.

---

# Data Sources

GuardDuty analyzes

- CloudTrail
- DNS Logs
- VPC Flow Logs
- EKS Audit Logs
- S3 Events
- Runtime Monitoring

---

# Threat Examples

SSH Brute Force

```
Attacker

↓

Thousands of SSH Attempts

↓

GuardDuty Finding
```

---

Credential Theft

```
AWS Access Key

↓

Used from another Country

↓

Finding
```

---

Malware

```
EC2

↓

Crypto Miner Installed

↓

GuardDuty Finding
```

---

Port Scanning

```
Attacker

↓

Scans

22

80

443

↓

Finding
```

---

# GuardDuty Configuration

Open

```
Amazon GuardDuty
```

Enable GuardDuty.

GuardDuty continuously monitors AWS activity.

---

# Understanding Security Hub

Security Hub is a centralized security management service.

It aggregates findings from:

- Inspector
- GuardDuty
- AWS Config
- IAM Access Analyzer
- Macie

---

# Security Hub Architecture

```
Inspector

↓

GuardDuty

↓

AWS Config

↓

IAM Analyzer

↓

Security Hub
```

---

# Enable Security Hub

Open

```
Security Hub
```

Choose

```
Enable all capabilities
```

Click

```
Enable Security Hub
```

---

# Security Hub Dashboard

## Summary

High-level overview.

---

## Threats

Displays GuardDuty findings.

---

## Vulnerabilities

Displays Inspector findings.

---

## Exposure

Displays exposed AWS resources.

---

## Posture Management

Checks AWS security best practices.

Examples

- Root MFA
- Public S3
- Security Groups
- Encryption

---

## Sensitive Data

Displays Macie findings.

---

## Inventory

Displays all monitored resources.

---

## Integrations

Shows connected AWS security services.

---

# Service Linked Role

Security Hub automatically creates

```
AWSServiceRoleForSecurityHub
```

No manual configuration required.

---

# Security Findings Lifecycle

```
Inspector

↓

Finding

↓

Security Hub

↓

Security Team

↓

Remediation

↓

Closed
```

---

# Best Practices

- Always attach IAM Roles instead of Access Keys.
- Enable Inspector for continuous vulnerability scanning.
- Enable GuardDuty in every AWS Region.
- Enable Security Hub for centralized monitoring.
- Enable Multi-Factor Authentication.
- Patch EC2 instances regularly.
- Use SSM Patch Manager.
- Investigate High and Critical findings immediately.

---

# Troubleshooting

## EC2 not scanned

Possible causes

- Missing SSM Agent
- Missing IAM Role
- Unsupported OS
- Instance Stopped

---

## GuardDuty not showing findings

Possible causes

- No suspicious activity
- GuardDuty not enabled
- Sample findings not generated

---

## Security Hub shows GuardDuty capability failed

Reason

GuardDuty wasn't enabled.

Solution

Enable GuardDuty.

---

## Security Hub Threats page empty

Reason

No active GuardDuty findings.

This is expected for a healthy environment.

---

# Cost Considerations

Inspector

Charges per EC2 instance scanned.

GuardDuty

Charges based on analyzed events.

Security Hub

Charges per monitored resource.

Always review pricing before enabling in production.

---

# Interview Questions

Q. What is Amazon Inspector?

Q. What is GuardDuty?

Q. What is Security Hub?

Q. What is CVE?

Q. What is a Finding?

Q. Difference between Vulnerability and Threat?

Q. Why Security Hub if Inspector already exists?

Q. Does Security Hub perform scanning?

---

# Difference Between Amazon Inspector, GuardDuty and Security Hub

| Feature | Amazon Inspector | Amazon GuardDuty | AWS Security Hub |
|----------|------------------|------------------|------------------|
| Primary Purpose | Vulnerability Assessment | Threat Detection | Centralized Security Management |
| What it Detects | Software vulnerabilities, missing patches, CVEs | Suspicious or malicious activity | Aggregates findings from multiple security services |
| Scans EC2 Packages | ✅ Yes | ❌ No | ❌ No |
| Uses CloudTrail Logs | ❌ No | ✅ Yes | Indirectly (via GuardDuty/Config) |
| Uses VPC Flow Logs | ❌ No | ✅ Yes | Indirectly |
| Detects Malware | ❌ No | ✅ Yes | Displays GuardDuty findings |
| Detects SSH Brute Force | ❌ No | ✅ Yes | Displays GuardDuty findings |
| Detects Public S3 Buckets | ❌ No | Via S3 Protection (behavioral) | Via CSPM/Config checks |
| Uses CVEs | ✅ Yes | ❌ No | Displays Inspector findings |
| Performs Vulnerability Scanning | ✅ Yes | ❌ No | ❌ No |
| Performs Threat Detection | ❌ No | ✅ Yes | ❌ No |
| Central Dashboard | ❌ No | ❌ No | ✅ Yes |
| Integrates with Other Services | Limited | Limited | ✅ Yes |
| Typical Output | Vulnerability Findings | Threat Findings | Unified Security Findings |

---

# Inspector vs GuardDuty vs Security Hub (Simple Explanation)

Amazon Inspector answers:

> **"Is my EC2 instance vulnerable?"**

Example:

```
OpenSSL is outdated.

Update required.
```

---

Amazon GuardDuty answers:

> **"Is someone attacking my AWS environment?"**

Example:

```
SSH Brute Force

Credential Theft

Crypto Mining
```

---

AWS Security Hub answers:

> **"Show me every security issue from all AWS security services in one place."**

Example:

```
Inspector

↓

GuardDuty

↓

AWS Config

↓

One Dashboard
```

---

# Conclusion

In this project we successfully:

- Enabled Amazon Inspector
- Scanned EC2 instances
- Identified software vulnerabilities
- Understood CVEs and Findings
- Enabled Amazon GuardDuty
- Learned AWS threat detection
- Enabled AWS Security Hub
- Aggregated findings into a centralized dashboard
- Built an enterprise-grade AWS security monitoring solution
