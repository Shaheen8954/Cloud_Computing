# Cross-Account AWS DataSync (S3 to S3)

## Objective

Configure AWS DataSync to securely transfer data from an S3 bucket in the **Source AWS Account** to an S3 bucket in the **Destination AWS Account** using IAM Roles and Bucket Policies.

---

# Architecture

```

                Source AWS Account                           Destination AWS Account
+-----------------------------------------+      +--------------------------------------+
|                                         |      |                                      |
|  Source S3 Bucket                       |      |  Destination S3 Bucket               |
|                                         |      |                                      |
|        │                                |      |                                      |
|        │                                |      |                                      |
|   AWS DataSync                          |──────►         S3 Bucket                    |
|        │                                |      |                                      |
|        │ Assume IAM Role                |      |                                      |
|        ▼                                |      |                                      |
| IAM Role (BucketAccessRoleArn)          |      | Bucket Policy allows IAM Role        |
|                                         |      |                                      |
+-----------------------------------------+      +--------------------------------------+


---

# Prerequisites

## Source Account

- Source S3 Bucket
- AWS DataSync Service
- IAM Role
- AWS CLI configured
- IAM User with DataSync permissions

## Destination Account

- Destination S3 Bucket
- Bucket Policy allowing Source Account IAM Role

---

# Step 1 - Create Source Bucket

Create the source bucket.

Example:

```
amazon-s3-source-bucket1234543
```

Upload test files into this bucket.

---

# Step 2 - Create Destination Bucket

Login to Destination AWS Account.

Create the destination bucket.

Example:

```
amazon-s3-destination-bucket190
```

---

# Step 3 - Create IAM Role (Source Account)

Navigate to

```
IAM
→ Roles
→ Create Role
```

Trusted Entity

```
AWS Service
```

Select

```
DataSync
```

Attach Managed Policy

```
AWSDataSyncFullAccess
```

Create Role

Example Role Name

```
data-sync-role
```

---

# Step 4 - Add Inline Policy to IAM Role

Attach the following inline policy.

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "s3:GetBucketLocation",
                "s3:ListBucket",
                "s3:ListBucketMultipartUploads"
            ],
            "Effect": "Allow",
            "Resource": "arn:aws:s3:::datasync-destination-s3-123"
        },
        {
            "Action": [
                "s3:AbortMultipartUpload",
                "s3:DeleteObject",
                "s3:GetObject",
                "s3:ListMultipartUploadParts",
                "s3:PutObject",
                "s3:GetObjectTagging",
                "s3:PutObjectTagging"
            ],
            "Effect": "Allow",
            "Resource": "arn:aws:s3:::datasync-destination-s3-123/*"
        }
    ]
}
```

This policy allows DataSync to read/write objects inside the destination bucket.

---

# Step 5 - Configure Destination Bucket Policy

Login to Destination Account.

Navigate to

```
S3
→ Destination Bucket
→ Permissions
→ Bucket Policy
```

Paste the following policy.

```json
{
    "Version": "2008-10-17",
    "Statement": [
        {
            "Sid": "DataSyncCreateS3LocationAndTaskAccess",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::<Source Account ID>:role/<Role Name>"
            },
            "Action": [
                "s3:GetBucketLocation",
                "s3:ListBucket",
                "s3:ListBucketMultipartUploads",
                "s3:AbortMultipartUpload",
                "s3:DeleteObject",
                "s3:GetObject",
                "s3:ListMultipartUploadParts",
                "s3:PutObject",
                "s3:GetObjectTagging",
                "s3:PutObjectTagging"
            ],
            "Resource": [
                "arn:aws:s3:::datasync-destination-s3-123",
                "arn:aws:s3:::datasync-destination-s3-123/*"
            ]
        },
        {
            "Sid": "DataSyncCreateS3Location",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::<Source Account ID>:user/<IAM User>"
            },
            "Action": "s3:ListBucket",
            "Resource": "arn:aws:s3:::datasync-destination-s3-123"
        }
    ]
}
```

---

# Step 6 - Configure AWS CLI

Verify AWS CLI

```bash
aws --version
```

Verify credentials

```bash
aws configure list
```

Verify Region

```bash
aws configure get region
```

If the destination bucket is in **ap-south-1**, configure the CLI accordingly.

```bash
aws configure set region ap-south-1
```

---

# Important Observation

While creating the DataSync location, the AWS CLI Region must match the region of the destination bucket.

If the CLI Region is different, DataSync returns:

```
The bucket is in this region: ap-south-1.
Please use this region to retry the request.
```

This was the primary issue encountered during implementation.

---

# Step 7 - Create Destination S3 Location

Execute:

```bash
aws datasync create-location-s3 \
--s3-bucket-arn arn:aws:s3:::datasync-destination-s3-123 \
--s3-config '{
"BucketAccessRoleArn":"arn:aws:iam::<SourceAccountId>:role/data-sync-role"
}'
```

### Windows PowerShell

Run on one line:

```powershell
aws datasync create-location-s3 --s3-bucket-arn arn:aws:s3:::datasync-destination-s3-123 --s3-config BucketAccessRoleArn=arn:aws:iam::<SourceAccountId>:role/data-sync-role
```

Expected Output

```json
{
    "LocationArn": "arn:aws:datasync:ap-south-1:xxxxxxxx:location/loc-xxxxxxxx"
}
```

---

# Step 8 - Verify Location

Navigate to

```
AWS DataSync
→ Locations
```

The destination bucket location should now appear successfully.

---

# Step 9 - Create Source Location

Navigate to

```
AWS DataSync
→ Locations
→ Create Location
```

Select

```
Amazon S3
```

Choose the source bucket.

```
amazon-s3-source-bucket1234543
```

Create the location.

---

# Step 10 - Create DataSync Task

Navigate to

```
AWS DataSync
→ Tasks
→ Create Task
```

Source Location

```
Source Bucket
```

Destination Location

```
Destination Bucket
```

Task Mode

```
Basic
```

For testing purposes, create the task manually.

---

# Step 11 - Execute Task

Select the task.

Click

```
Start
```

Monitor

```
Launching
Preparing
Transferring
Verifying
Success
```

---

# Step 12 - Verify Data

Login to the Destination AWS Account.

Navigate to

```
S3
→ Destination Bucket
```

Verify that all objects from the source bucket are present.

---

# Troubleshooting

## Issue 1

```
Missing expression after unary operator '--'
```

### Cause

Using Linux (`\`) line continuation in Windows PowerShell.

### Fix

Use:

```powershell
`
```

or execute the command on a single line.

---

## Issue 2

```
NoSuchBucket
```

### Cause

Incorrect bucket name or bucket not created.

### Fix

Verify the exact bucket name.

---

## Issue 3

```
The bucket is in this region.
Please use this region to retry the request.
```

### Cause

AWS CLI/DataSync Region does not match the bucket Region.

### Fix

```bash
aws configure set region ap-south-1
```

---

## Issue 4

```
AccessDenied
```

### Cause

Bucket Policy or IAM Role permissions are missing.

### Fix

Verify:

- IAM Role permissions
- Bucket Policy
- Bucket Region
- Bucket ARN
- Role ARN

---

# Learning Outcomes

- Cross-account DataSync configuration
- IAM Role for DataSync
- Bucket Policy for cross-account S3 access
- AWS CLI DataSync commands
- Region-specific behavior of AWS DataSync
- Creating DataSync locations
- Creating DataSync tasks
- Troubleshooting PowerShell syntax issues
- Troubleshooting Region mismatch
- Troubleshooting cross-account bucket permissions
- End-to-end S3 to S3 synchronization using AWS DataSync
