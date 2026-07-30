# Patch Amazon EC2 Instances using AWS Systems Manager Patch Manager

## Project Overview

This project demonstrates how to automate operating system patch management for Amazon EC2 instances using **AWS Systems Manager (SSM) Patch Manager**.

The lab covers:

- Configuring Systems Manager Patch Manager
- Creating a Patch Baseline
- Creating a Maintenance Window
- Registering Targets
- Registering Patch Tasks
- Executing Patch Operations
- Troubleshooting Patch Failures
- Understanding IAM permissions required for Patch Manager

---

# Objective

Automate patch management for Linux EC2 instances without manually logging into each server.

By the end of this lab we successfully configured:

- AWS Systems Manager
- Managed EC2 Instances
- Patch Manager
- Patch Baselines
- Maintenance Windows
- Run Command
- IAM Role
- S3 permissions

---

# Architecture

```
                    AWS Systems Manager
                           │
                           │
                 Patch Manager Service
                           │
         ┌─────────────────┴─────────────────┐
         │                                   │
 Patch Baseline                      Maintenance Window
         │                                   │
         └──────────────┬────────────────────┘
                        │
                  Run Command
                        │
        AWS-RunPatchBaseline Document
                        │
               Managed EC2 Instance
                        │
                  Install Linux Updates
                        │
                  Report Compliance
```

---

# AWS Services Used

- Amazon EC2
- AWS Systems Manager
- Patch Manager
- Fleet Manager
- IAM
- Amazon S3
- Maintenance Windows
- Run Command

---

# Prerequisites

Before configuring Patch Manager, each EC2 instance must have:

- Ubuntu Linux
- SSM Agent installed
- IAM Role attached
- Internet access or VPC Endpoints
- Systems Manager Managed Instance status = Online

---

# Required IAM Policies

The EC2 IAM Role must include:

```
AmazonSSMManagedInstanceCore
CloudWatchAgentServerPolicy
AmazonS3FullAccess
```

Initially we mistakenly attached:

```
AmazonS3ExpressFullAccess
```

This caused Patch Manager to fail because Quick Setup stores baseline override files in a **standard S3 bucket**, not an S3 Express bucket.

We corrected the issue by replacing it with:

```
AmazonS3FullAccess
```

---

# Verify Managed Nodes

Navigate to

```
Systems Manager
    ↓
Fleet Manager
    ↓
Managed Nodes
```

Verify:

- Running
- Online
- SSM Agent Active

---

# Configure Patch Baseline

Navigate:

```
Systems Manager

↓

Patch Manager

↓

Patch Baselines
```

Use the default AWS Linux Patch Baseline or create a custom baseline.

The baseline defines:

- Approved patches
- Rejected patches
- Approval rules
- Compliance rules

---

# Configure Maintenance Window

Navigate:

```
Systems Manager

↓

Maintenance Windows

↓

Create Maintenance Window
```

Configuration

Name

```
weekly-patching
```

Schedule

```
cron(0 */30 * * ? *)
```

Duration

```
3 Hours
```

Cutoff

```
1 Hour
```

Timezone

```
Asia/Kolkata
```

State

```
Enabled
```

---

# Register Target

Inside the Maintenance Window

```
Register Targets
```

Target Type

```
Managed Instances
```

Selection

```
Instance IDs
```

Choose EC2 instance(s).

---

# Register Task

Inside Maintenance Window

```
Register Run Command Task
```

Document

```
AWS-RunPatchBaseline
```

Priority

```
1
```

Service Role

```
Automatically Created
```

Operation

```
Scan
```

or

```
Install
```

Reboot

```
NoReboot
```

---

# Patch Execution Flow

Maintenance Window

↓

Run Command

↓

AWS-RunPatchBaseline

↓

PatchWindows Step

↓

PatchLinux Step

↓

Compliance Report

---

# Manual Patch Execution

Navigate:

```
Systems Manager

↓

Run Command

↓

Run Command
```

Document

```
AWS-RunPatchBaseline
```

Target

Choose managed EC2 instance.

Operation

```
Scan
```

or

```
Install
```

---

# Commands Executed on EC2

Verify SSM Agent

```bash
sudo systemctl status amazon-ssm-agent
```

Ubuntu package refresh

```bash
sudo apt update
```

List upgradeable packages

```bash
apt list --upgradable
```

---

# Command Parameters Observed

Operation

```
Scan
```

Reboot Option

```
NoReboot
```

Baseline Override

```
s3://aws-quicksetup-patchpolicy-031190641965-j2o87/baseline_overrides.json
```

Execution Timeout

```
10800 Seconds
```

Delivery Timeout

```
600 Seconds
```

---

# S3 Bucket Created by Quick Setup

Quick Setup automatically created

```
aws-quicksetup-patchpolicy-031190641965-j2o87
```

Inside the bucket

```
baseline_overrides.json
```

This file contains Patch Baseline override configuration.

---

# Issue Encountered

Initial Error

```
botocore.exceptions.ClientError

403 Forbidden

HeadObject Operation

Payload failed to start

exit status 170
```

---

# Root Cause

The EC2 IAM Role only had

```
AmazonS3ExpressFullAccess
```

Patch Manager required access to

```
Standard Amazon S3
```

Specifically

```
HeadObject
GetObject
```

on

```
baseline_overrides.json
```

Permission was denied.

---

# Resolution

Removed

```
AmazonS3ExpressFullAccess
```

Attached

```
AmazonS3FullAccess
```

Waited for IAM propagation.

Re-ran Patch Command.

---

# Verify Command Output

Navigate

```
Systems Manager

↓

Run Command

↓

Command History

↓

View Details
```

Check

- Overall Status
- Detailed Status
- Output
- Errors

Successful output

```
PatchWindows

Status

Success
```

```
PatchLinux

Status

Success
```

---

# Troubleshooting

## Managed instance not appearing

Check

```
AmazonSSMManagedInstanceCore
```

Verify

```
amazon-ssm-agent
```

Running

---

## Instance Offline

Check

```
Fleet Manager

↓

Managed Nodes

↓

Ping Status
```

Must be

```
Online
```

---

## 403 Forbidden

Verify

```
AmazonS3FullAccess
```

attached to EC2 Role.

---

## HeadObject Error

Ensure

```
baseline_overrides.json
```

exists in Quick Setup bucket.

---

## Patch Command Failed

Verify

- IAM Role
- SSM Agent
- Internet Connectivity
- S3 Bucket Access

---

# Security Best Practices

Instead of

```
AmazonS3FullAccess
```

production environments should use

least privilege IAM policies such as

```json
{
    "Effect":"Allow",
    "Action":[
        "s3:GetObject",
        "s3:HeadObject"
    ],
    "Resource":[
        "arn:aws:s3:::aws-quicksetup-patchpolicy-*/*"
    ]
}
```

---

# Cleanup

Delete

- Maintenance Window
- Registered Tasks
- Registered Targets
- Custom Patch Baselines (optional)

Detach unnecessary IAM policies if no longer required.

---

# Interview Questions

### What is Patch Manager?

Patch Manager automates operating system patching for managed EC2 instances using AWS Systems Manager.

---

### What is a Patch Baseline?

A Patch Baseline defines which patches are approved or rejected for installation.

---

### What is a Maintenance Window?

A Maintenance Window defines when maintenance tasks such as patching are executed.

---

### What is AWS-RunPatchBaseline?

It is an AWS Systems Manager document used to scan or install patches on managed instances.

---

### Why did Patch Manager fail with 403 Forbidden?

The EC2 IAM Role lacked permission to access the Quick Setup S3 bucket containing `baseline_overrides.json`.

---

### Why is Fleet Manager required?

Fleet Manager verifies that EC2 instances are registered as managed nodes before patch operations can be executed.

---

### Difference between Scan and Install?

| Scan | Install |
|-------|----------|
| Checks missing patches | Downloads and installs patches |

---

# Key Learnings

- Configured AWS Systems Manager Patch Manager.
- Created Maintenance Windows.
- Registered Targets and Tasks.
- Executed AWS-RunPatchBaseline.
- Understood Patch Baselines.
- Learned IAM permissions required for Patch Manager.
- Troubleshot S3 403 errors.
- Verified managed nodes through Fleet Manager.
- Learned production best practices for automated patch management.
