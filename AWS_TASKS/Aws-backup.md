# Protect Amazon EC2 Workloads using AWS Backup, EBS Snapshots & Recovery Policies

## Project Overview

This project demonstrates how to protect an Amazon EC2 workload using AWS native backup and recovery services.

The implementation includes:

- AWS Backup
- Amazon EBS Snapshots
- Recovery Policies
- Backup Verification
- Recovery Strategy

---

# Objective

Protect an EC2 workload against:

- Accidental deletion
- Data corruption
- EBS volume failure
- Application failure
- Human error
- Disaster recovery scenarios

---

# Architecture

```
                   Internet
                        │
                        ▼
              Application Load Balancer
                        │
                        ▼
                EC2 (FastAPI Backend)
                        │
                        ▼
                  Amazon EBS Volume
                        │
        ┌───────────────┴────────────────┐
        ▼                                ▼
 AWS Backup                     Manual EBS Snapshot
        │                                │
        ▼                                ▼
  Backup Vault                    Snapshot Storage
        │
        ▼
 Recovery Point
        │
        ▼
 Restore EC2 / Restore Volume
```

---

# Services Used

- Amazon EC2
- Amazon EBS
- AWS Backup
- AWS Backup Vault
- Amazon EBS Snapshots
- IAM
- AWS KMS

---

# Prerequisites

- Running EC2 Instance
- Attached EBS Volume
- IAM permissions for AWS Backup
- FastAPI application deployed
- AWS Console access

---

# Part 1 - Configure AWS Backup

## Why AWS Backup?

AWS Backup provides centralized backup management for AWS resources.

Benefits:

- Automated backups
- Scheduled backups
- Backup retention
- Centralized management
- Recovery points
- Backup monitoring

---

## Step 1 - Create Backup Vault

Navigate

```
AWS Backup
    ↓
Backup Vaults
    ↓
Create Backup Vault
```

### Configuration

| Setting | Value |
|----------|-------|
| Backup Vault Name | backend-backup-vault |
| Encryption Key | aws/backup (AWS Managed KMS Key) |

Click

```
Create Backup Vault
```

---

## Step 2 - Create Backup Plan

Navigate

```
AWS Backup
    ↓
Backup Plans
    ↓
Create Backup Plan
```

Select

```
Build a New Plan
```

Backup Plan Name

```
backend-backup-plan
```

---

## Step 3 - Configure Backup Rule

### General Settings

| Setting | Value |
|----------|-------|
| Rule Name | daily-backup-rule |
| Backup Vault | backend-backup-vault |
| Backup Frequency | Daily |
| Backup Window Start | Default |
| Start Within | 8 Hours |
| Complete Within | 7 Days |

---

## Point-in-Time Recovery

Configuration

```
Disabled
```

Reason

EC2 workloads use scheduled EBS snapshots rather than continuous backup.

---

## Lifecycle

Move to Cold Storage

```
Disabled
```

Retention

```
35 Days
```

Backups are automatically deleted after 35 days.

---

## Backup Index

```
Disabled
```

---

## Copy to Destination

```
None
```

No cross-region backup configured.

---

## Malware Protection

```
Disabled
```

---

## Windows VSS

```
Disabled
```

Applicable only to Windows instances.

---

Click

```
Create Plan
```

---

# Step 4 - Assign Resources

Navigate

```
Backup Plan
    ↓
Assign Resources
```

Configuration

| Setting | Value |
|----------|-------|
| Assignment Name | backend-resource-assignment |
| IAM Role | Default Role |
| Resource Selection | Include Specific Resource Types |
| Resource Type | EC2 |
| Resource | Backend EC2 Instance |

Click

```
Assign Resources
```

---

# Step 5 - Verify Backup Plan

Navigate

```
AWS Backup
    ↓
Protected Resources
```

Expected

```
EC2 Instance

Status

Protected
```

---

# Step 6 - Create On-Demand Backup

Navigate

```
Protected Resources

↓

Select EC2

↓

Create On-Demand Backup
```

Select

```
Backup Vault

↓

backend-backup-vault
```

Click

```
Create On-Demand Backup
```

---

# Step 7 - Verify Backup Job

Navigate

```
AWS Backup

↓

Backup Jobs
```

Status

```
Created

↓

Running

↓

Completed
```

---

# Step 8 - Verify Recovery Point

Navigate

```
Backup Vault

↓

backend-backup-vault
```

Expected

```
Recovery Point Created
```

---

# Part 2 - Configure Amazon EBS Snapshot

## Why EBS Snapshot?

Amazon EBS Snapshot creates a point-in-time backup of an EBS volume.

Snapshots are useful before:

- OS Upgrade
- Deployment
- Configuration Change
- Database Upgrade
- Kernel Upgrade

---

## Step 1 - Open Volumes

Navigate

```
EC2

↓

Elastic Block Store

↓

Volumes
```

Select

```
Backend EC2 Volume
```

---

## Step 2 - Create Snapshot

Click

```
Actions

↓

Create Snapshot
```

Configuration

| Setting | Value |
|----------|-------|
| Description | FastAPI Backend Snapshot Before Deployment |
| Tags | Optional |

Click

```
Create Snapshot
```

---

## Step 3 - Verify Snapshot

Navigate

```
EC2

↓

Snapshots
```

Status

```
Pending

↓

Completed
```

---

# Incremental Snapshot

First Snapshot

```
Stores all used blocks.
```

Second Snapshot

```
Stores only changed blocks.
```

Benefits

- Lower storage cost
- Faster backup
- Efficient storage utilization

---

# Part 3 - Configure Recovery Policy

## Purpose

Recovery Policy defines how to recover workloads after failure.

It includes:

- Backup Schedule
- Backup Retention
- Restore Procedure
- Recovery Objectives

---

## Recovery Configuration

| Policy | Configuration |
|----------|--------------|
| Backup Frequency | Daily |
| Backup Method | AWS Backup |
| Manual Snapshot | Before Major Changes |
| Retention | 35 Days |
| Recovery Method | Restore Volume from Recovery Point |
| Backup Vault | backend-backup-vault |

---

# Recovery Procedure

## Scenario 1

EC2 Deleted

Recovery

```
Launch New EC2

↓

Restore EBS Volume

↓

Attach Volume

↓

Start Instance
```

---

## Scenario 2

Application Corrupted

Recovery

```
Restore Recovery Point

↓

Restore Volume

↓

Replace Existing Volume
```

---

## Scenario 3

Files Accidentally Deleted

Recovery

```
Create Volume from Snapshot

↓

Mount Volume

↓

Copy Required Files

↓

Unmount Volume
```

---

# Recovery Objectives

## Recovery Point Objective (RPO)

Definition

Maximum acceptable data loss.

Example

Daily Backup at 2 AM

Failure at 5 PM

```
RPO = 15 Hours
```

---

## Recovery Time Objective (RTO)

Definition

Maximum acceptable recovery time.

Example

Application restored within

```
30 Minutes
```

---

# Verification Checklist

## AWS Backup

- Backup Vault Created
- Backup Plan Created
- Resource Assigned
- Backup Job Completed
- Recovery Point Available

---

## EBS Snapshot

- Snapshot Created
- Snapshot Completed
- Snapshot Available for Restore

---

## Recovery Policy

- Backup Schedule Configured
- Retention Configured
- Restore Procedure Documented
- Recovery Objectives Defined

---

# Troubleshooting

## Backup Job Failed

Check

- IAM Role
- Backup Vault
- EC2 Permissions
- AWS Backup Service Status

---

## Resource Not Protected

Verify

- Resource Assignment
- Correct Resource Type
- EC2 Selected

---

## Snapshot Pending

Possible Causes

- Large EBS Volume
- AWS Internal Processing

Wait until

```
Completed
```

---

## Recovery Point Missing

Verify

```
Backup Job

↓

Completed
```

Recovery Points are created only after successful backup completion.

---

# Best Practices

- Create a dedicated Backup Vault.
- Use AWS managed KMS unless customer-managed keys are required.
- Configure daily backups for production workloads.
- Keep backups for at least 30–35 days.
- Take manual EBS snapshots before major deployments.
- Test restore procedures regularly.
- Monitor backup jobs for failures.
- Use resource tags in larger environments for automatic backup selection.
- Document recovery procedures and recovery objectives.

---

# AWS Services Used

- Amazon EC2
- Amazon EBS
- Amazon EBS Snapshots
- AWS Backup
- AWS Backup Vault
- IAM
- AWS KMS

---

# Outcome

Successfully implemented:

- Centralized AWS Backup
- Dedicated Backup Vault
- Daily Automated Backup Plan
- EC2 Resource Assignment
- On-Demand Backup
- Recovery Point Verification
- Manual Amazon EBS Snapshots
- Snapshot Verification
- Recovery Policy Documentation
- Disaster Recovery Strategy
- Backup Validation
