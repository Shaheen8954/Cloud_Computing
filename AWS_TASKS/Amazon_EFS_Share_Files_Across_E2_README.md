# Amazon EFS - Create and Share Files Across EC2 Instances

## Objective

Create an Amazon Elastic File System (EFS), configure networking, mount
it on multiple EC2 instances, and verify that all instances share the
same data.

------------------------------------------------------------------------

# Lab Architecture

``` text
                    Amazon EFS
                         │
          ┌──────────────┴──────────────┐
          │                             │
 Mount Target (ap-south-1a)     Mount Target (ap-south-1b)
          │                             │
     Private EC2-1                Public EC2
          │
     Private EC2-2

All EC2 instances mount:
/mnt/efs
```

------------------------------------------------------------------------

# Prerequisites

-   3 EC2 instances
-   Same VPC
-   EFS security group
-   Bastion Host or AWS SSM
-   VPC DNS Resolution = Enabled
-   VPC DNS Hostnames = Enabled

------------------------------------------------------------------------

# Step 1 - Create Amazon EFS

AWS Console

Services → Elastic File System (EFS)

Click **Create file system**

Configure:

  Setting             Value
  ------------------- --------------------------
  Name                shared-efs-storage
  Performance Mode    General Purpose
  Throughput Mode     Elastic
  Encryption          Enabled
  Automatic Backups   Enabled
  Lifecycle           Move to IA after 30 days
  Archive             After 90 days

Click **Customize** then **Create**.

### Why?

-   General Purpose → low latency.
-   Elastic throughput → automatically scales.
-   Encryption → protects data at rest.
-   Automatic backups → disaster recovery.
-   Lifecycle → reduces storage cost.

------------------------------------------------------------------------

# Step 2 - Create Mount Targets

Open the EFS.

Go to **Network**.

Create mount targets in every Availability Zone containing EC2
instances.

Example:

-   ap-south-1a
-   ap-south-1b

Attach the dedicated EFS Security Group.

------------------------------------------------------------------------

# Step 3 - Configure Security Group

Inbound Rule

  Type   Protocol   Port   Source
  ------ ---------- ------ --------------------
  NFS    TCP        2049   EC2 Security Group

Explanation:

NFS uses TCP port 2049.

------------------------------------------------------------------------

# Step 4 - Verify VPC DNS

Ensure:

-   DNS Resolution = Enabled
-   DNS Hostnames = Enabled

Without these, the EFS DNS name cannot be resolved.

------------------------------------------------------------------------

# Step 5 - Install NFS Client

Run on every EC2.

``` bash
sudo apt update
sudo apt install -y nfs-common
```

------------------------------------------------------------------------

# Step 6 - Create Mount Directory

``` bash
sudo mkdir -p /mnt/efs
```

------------------------------------------------------------------------

# Step 7 - Mount EFS

``` bash
sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport fs-011a93b1d39558923.efs.ap-south-1.amazonaws.com:/ /mnt/efs
```

Command explanation:

-   nfsvers=4.1 : Recommended protocol
-   rsize/wsize : Performance tuning
-   hard : Retry until server responds
-   noresvport : Better reconnect behavior

------------------------------------------------------------------------

# Step 8 - Verify Mount

``` bash
df -h
```

Expected:

``` text
fs-011a93b1d39558923.efs.ap-south-1.amazonaws.com:/  8.0E  ... /mnt/efs
```

------------------------------------------------------------------------

# Step 9 - Test Shared Storage

Private EC2-1

``` bash
echo "Hello from Private EC2-1" | sudo tee /mnt/efs/test.txt
```

Private EC2-2

``` bash
cat /mnt/efs/test.txt
```

Public EC2

``` bash
cat /mnt/efs/test.txt
```

Expected:

``` text
Hello from Private EC2-1
```

------------------------------------------------------------------------

# Troubleshooting

## Error

    mount point efs does not exist

Fix

Use:

``` bash
sudo mkdir -p /mnt/efs
```

------------------------------------------------------------------------

## Error

    Failed to resolve server
    Name or service not known

Cause

VPC DNS Hostnames disabled.

Resolution

Enable:

-   DNS Resolution
-   DNS Hostnames

Verify:

``` bash
nslookup fs-011a93b1d39558923.efs.ap-south-1.amazonaws.com
```

------------------------------------------------------------------------

## Error

    lock file version 4 requires -Znext-lockfile-bump

Cause

amazon-efs-utils build failed due to Cargo version.

Resolution

Use the AWS-supported NFS client (`nfs-common`) instead.

------------------------------------------------------------------------

# Best Practices

-   Encrypt EFS.
-   Enable automatic backups.
-   Use dedicated security groups.
-   Create mount targets in every AZ.
-   Use `/etc/fstab` for persistent mounts.

------------------------------------------------------------------------

# Interview Questions

1.  What is Amazon EFS?
2.  Difference between EFS and EBS?
3.  Which protocol does EFS use?
4.  Which port should be open?
5.  Why are mount targets required?
6.  Why must DNS Resolution and DNS Hostnames be enabled?
7.  Can multiple EC2 instances access the same EFS simultaneously?

------------------------------------------------------------------------

# Cleanup

``` bash
sudo umount /mnt/efs
```

Delete:

-   Mount Targets
-   Amazon EFS
-   Security Group (if no longer required)
