# Amazon EBS Snapshot Recovery and Volume Restore

## Project Overview

This project demonstrates how to recover data from an Amazon EC2 instance using Amazon EBS Snapshots.

The workflow includes:

- Creating an EBS Snapshot
- Creating a new EBS Volume from the Snapshot
- Attaching the restored volume to another EC2 instance
- Mounting the volume
- Verifying the recovered data

This is one of the most common disaster recovery procedures used in AWS environments.

---

# Objective

Recover data from an existing EC2 instance without affecting the running server.

The recovery process allows you to:

- Recover accidentally deleted files
- Restore failed EBS volumes
- Access historical data
- Perform forensic investigations
- Migrate data to another EC2 instance

---

# Architecture

```
                 EC2 Server 1
                      │
                      ▼
               Root EBS Volume
                      │
                      ▼
              Create Snapshot
                      │
                      ▼
             Amazon EBS Snapshot
                      │
                      ▼
          Create New Volume from Snapshot
                      │
                      ▼
             Attach to EC2 Server 2
                      │
                      ▼
              Mount Existing Filesystem
                      │
                      ▼
               Access Recovered Data
```

---

# Services Used

- Amazon EC2
- Amazon EBS
- Amazon EBS Snapshot
- Linux (Ubuntu)

---

# Prerequisites

- Running EC2 Instance
- Existing EBS Snapshot
- Second EC2 Instance
- SSH Access
- IAM permissions to manage EBS

---

# Part 1 - Create Snapshot

Navigate

```
EC2
    ↓
Elastic Block Store
    ↓
Volumes
```

Select the source EBS volume.

Click

```
Actions
    ↓
Create Snapshot
```

Configuration

| Setting | Value |
|----------|-------|
| Description | Snapshot before recovery |
| Tags | Optional |

Click

```
Create Snapshot
```

---

# Part 2 - Create Volume from Snapshot

Navigate

```
EC2
    ↓
Snapshots
```

Select the snapshot.

Click

```
Actions
    ↓
Create Volume from Snapshot
```

Configuration

| Setting | Value |
|----------|-------|
| Volume Type | gp3 |
| Size | Same as Snapshot |
| Availability Zone | Same AZ as Target EC2 |
| Encryption | Enabled |

Click

```
Create Volume
```

Expected State

```
Available
```

---

# Part 3 - Attach Volume

Navigate

```
EC2
    ↓
Volumes
```

Select the restored volume.

Click

```
Actions
    ↓
Attach Volume
```

Configuration

| Setting | Value |
|----------|-------|
| Instance | Target EC2 Instance |
| Device Name | /dev/sdf (or default) |

Click

```
Attach Volume
```

Expected State

```
In-use
```

---

# Part 4 - Verify Attached Disk

SSH into the target EC2 instance.

Run

```bash
lsblk
```

Example Output

```
nvme0n1
├─nvme0n1p1
├─nvme0n1p14
├─nvme0n1p15
└─nvme0n1p16

nvme1n1
├─nvme1n1p1
├─nvme1n1p14
├─nvme1n1p15
└─nvme1n1p16
```

## Observation

The restored volume already contains:

- Partition Table
- Linux Filesystem
- Boot Partition
- EFI Partition

This indicates the volume was created from an existing snapshot.

---

# Important Note

Since the volume already contains a filesystem:

**Do NOT run**

```bash
sudo mkfs.ext4 /dev/nvme1n1
```

Formatting would erase all recovered data.

---

# Part 5 - Verify Existing Filesystem

Run

```bash
sudo file -s /dev/nvme1n1p1
```

Expected Output

```
Linux rev 1.0 ext4 filesystem data
```

This confirms the filesystem already exists.

---

# Part 6 - Display UUID

Run

```bash
sudo blkid
```

Purpose

Displays:

- UUID
- Filesystem Type
- Partition Information

Useful when configuring persistent mounts.

---

# Part 7 - Create Mount Point

Create a directory where the restored filesystem will be mounted.

```bash
sudo mkdir /recovery
```

Purpose

```
/recovery
```

acts as the access point for the restored volume.

---

# Part 8 - Mount the Volume

Run

```bash
sudo mount /dev/nvme1n1p1 /recovery
```

If successful, no output will be displayed.

---

# Part 9 - Verify Mount

Run

```bash
df -h
```

Example

```
Filesystem        Size Used Avail Mounted on
/dev/nvme1n1p1      8G   2G    6G /recovery
```

This confirms the volume has been mounted successfully.

---

# Part 10 - Verify Recovered Data

Run

```bash
ls /recovery
```

Example Output

```
bin
boot
dev
etc
home
lib
media
mnt
opt
proc
root
run
srv
tmp
usr
var
```

These directories prove the original operating system and files have been recovered successfully.

---

# Recovery Workflow

```
Snapshot
     │
     ▼
Create New Volume
     │
     ▼
Attach to EC2
     │
     ▼
Mount Existing Filesystem
     │
     ▼
Access Original Data
```

---

# Difference Between Recovery and Storage Expansion

## Recovery from Snapshot

```
Snapshot
     │
     ▼
Create Volume
     │
     ▼
Filesystem Already Exists
     │
     ▼
Mount Directly
```

Characteristics

- Existing filesystem
- Existing partitions
- Existing operating system
- Existing application data

No formatting required.

---

## Storage Expansion

```
New Empty Volume
     │
     ▼
Attach to EC2
     │
     ▼
Create Filesystem
     │
     ▼
Mount Volume
```

Characteristics

- Empty disk
- No filesystem
- No partitions
- Ready for formatting

Formatting is required before use.

---

# Verification Commands

List Block Devices

```bash
lsblk
```

Verify Filesystem

```bash
sudo file -s /dev/nvme1n1p1
```

Display UUID

```bash
sudo blkid
```

Create Mount Point

```bash
sudo mkdir /recovery
```

Mount Volume

```bash
sudo mount /dev/nvme1n1p1 /recovery
```

Verify Mount

```bash
df -h
```

List Recovered Files

```bash
ls /recovery
```

---

# Troubleshooting

## Volume Not Visible

Check

```bash
lsblk
```

Ensure the volume is attached to the correct EC2 instance.

---

## Mount Failed

Verify the filesystem exists.

```bash
sudo file -s /dev/nvme1n1p1
```

---

## Wrong Device Name

Check available block devices.

```bash
lsblk
```

Identify the correct NVMe device.

---

## Files Not Visible

Verify the correct partition is mounted.

```
/dev/nvme1n1p1
```

rather than the entire disk

```
/dev/nvme1n1
```

---

# Best Practices

- Never format a volume restored from a snapshot.
- Verify the filesystem before mounting.
- Mount recovered volumes in a temporary directory (for example, `/recovery`).
- Copy only the required data if performing file-level recovery.
- Detach the recovered volume after completing the recovery process.
- Use encrypted EBS volumes and snapshots for sensitive workloads.
- Periodically test snapshot restoration procedures as part of disaster recovery planning.

---

# Outcome

Successfully completed:

- Amazon EBS Snapshot creation
- Volume creation from Snapshot
- Volume attachment to a second EC2 instance
- Filesystem verification
- UUID verification
- Mount point creation
- Mounting restored volume
- Verification of recovered data
- Disaster recovery validation

---

# Key Learning

An Amazon EBS volume created from a snapshot already contains the original partition table and filesystem.

Therefore:

- **Do not format it (`mkfs`)**
- **Mount it directly**
- **Verify and access the recovered data**

This workflow is commonly used for disaster recovery, data restoration, forensic analysis, and migration between EC2 instances.
