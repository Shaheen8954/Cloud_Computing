# Amazon S3 Bucket Replication

## Overview

Amazon S3 Replication is a feature that automatically copies objects from one S3 bucket (the **source bucket**) to another S3 bucket (the **destination bucket**). The destination bucket can be in the **same AWS Region** or a **different AWS Region**.

Replication helps improve disaster recovery, compliance, data redundancy, and performance by maintaining an automatic copy of your data.

---

# Types of Replication

### 1. Cross-Region Replication (CRR)

Replicates objects to a bucket located in a **different AWS Region**.

**Common Use Cases**
- Disaster Recovery (DR)
- Global applications
- Compliance requirements
- Reduced latency for users in different regions

---

### 2. Same-Region Replication (SRR)

Replicates objects to another bucket within the **same AWS Region**.

**Common Use Cases**
- Log aggregation
- Compliance
- Backup
- Data sharing between accounts

---

# Key Points

- Versioning must be enabled on both the source and destination buckets.
- Replication is asynchronous, so there may be a slight delay before objects appear in the destination bucket.
- Only new objects are replicated automatically.
- Existing objects require **S3 Batch Replication**.
- Object metadata, tags, ACLs, and delete markers can also be replicated.
- Replication can be filtered using prefixes or object tags.
- Supports AWS KMS encrypted objects (additional permissions are required).
- Replication works across AWS accounts as well.

---

# Benefits

- **Disaster Recovery** – Protects data from regional failures.
- **Compliance** – Store data in required geographic locations.
- **Low Latency** – Keep data closer to users worldwide.
- **Backup & Redundancy** – Maintain an additional copy of important data.
- **Business Continuity** – Improves data availability.

---

# Prerequisites

- Two S3 Buckets
  - Source Bucket
  - Destination Bucket
- Buckets can be in the same or different AWS Regions.
- Versioning enabled on both buckets.
- Required IAM permissions.

---

# Step 1: Create Two S3 Buckets

Create two buckets:
- Source Bucket
- Destination Bucket

---

<img width="2195" height="646" alt="image" src="https://github.com/user-attachments/assets/c5d5fa8c-108c-44e6-8e40-9a6dbf7ec605" />

---

# Step 2: Open the Source Bucket

Upload some sample data into the source bucket.

Navigate to:

**Source Bucket → Management**

---

<img width="3315" height="1295" alt="image" src="https://github.com/user-attachments/assets/882e001c-72df-4ff7-84e4-e8357926d541" />

---

# Step 3: Create a Replication Rule

Scroll down to:

**Replication Rules**

Click:

**Create Replication Rule**

---

<img width="3289" height="626" alt="image" src="https://github.com/user-attachments/assets/cdbb5fac-7ad7-4552-bbdb-0950603b3be6" />

---

# Step 4: Configure the Replication Rule

Provide:

- Rule Name
- Rule Status (Enabled)
- Objects to Replicate (All or Specific Prefix)

---

<img width="3331" height="897" alt="image" src="https://github.com/user-attachments/assets/0f2e55a7-88e7-41da-b421-fe81feb11113" />

---

# Step 5: Select the Source Bucket

Choose the bucket that contains the data you want to replicate.

---

<img width="3300" height="555" alt="image" src="https://github.com/user-attachments/assets/90f6c671-a3ed-4a40-8ca2-af9ef7ed1ec4" />

---

# Step 6: Select the Destination Bucket

Choose where the replicated data should be stored.

---

<img width="3239" height="729" alt="image" src="https://github.com/user-attachments/assets/376c558d-e514-4d98-91a4-2fcc7004b3ed" />

Select the destination bucket.

---

<img width="3231" height="894" alt="image" src="https://github.com/user-attachments/assets/91d5ed6e-9662-4ffa-b8ae-9e8b9e085e08" />

---

# Step 7: Create an IAM Role

Under the IAM Role section, select:

**Create New Role**

AWS automatically creates the required IAM role for replication.

---

<img width="3289" height="396" alt="image" src="https://github.com/user-attachments/assets/52d0d382-0fe0-45f8-8f31-84c7612252de" />

---

# Step 8: Replicate Existing Objects

If you already have data in the source bucket, choose:

**Yes, replicate existing objects**

Then click:

**Submit**

---

<img width="3329" height="1861" alt="image" src="https://github.com/user-attachments/assets/85199ae7-f8ac-46b2-9009-eda2daa948d1" />

---

# Step 9: Create an S3 Batch Operations Job

AWS will prompt you to create a **Batch Replication Job** for existing objects.

Click:

**Create Batch Operations Job**

---

<img width="3352" height="1797" alt="image" src="https://github.com/user-attachments/assets/9a4c599b-c25e-4003-9379-23b0db4982c7" />

---

# Step 10: Configure Permissions

Review the permissions required for the Batch Operations job and continue.

---

<img width="3299" height="769" alt="image" src="https://github.com/user-attachments/assets/615a6216-774d-44bf-a550-8276e3f76bbf" />

---

# Step 11: Wait for Replication to Complete

The Batch Operations job will begin replicating existing objects.

Wait until the job status changes to **Completed**.

---

<img width="3324" height="1206" alt="image" src="https://github.com/user-attachments/assets/538545c9-1300-458f-b204-1a7014e47790" />

---

# Step 12: Verify Replication

Open the destination bucket.

You should now see all objects successfully copied from the source bucket.

---

<img width="2755" height="1267" alt="image" src="https://github.com/user-attachments/assets/dc0bcd3c-0090-4576-b8df-4e6e1e714d98" />

---

# Automatic Replication

Once replication is configured:

- Any **new object** uploaded to the source bucket is automatically copied to the destination bucket.
- Replication happens automatically in the background.
- Existing objects are **not** replicated unless you run **S3 Batch Replication**.

---

# Replication Workflow

```text
           Upload Object
                 │
                 ▼
      Source S3 Bucket
                 │
                 ▼
      Replication Rule
                 │
                 ▼
        IAM Replication Role
                 │
                 ▼
      Destination S3 Bucket
```

---

# Important Notes

- Versioning is mandatory.
- Replication is asynchronous.
- Existing objects require Batch Replication.
- Deleting an object in the source bucket does not permanently delete it in the destination unless delete marker replication is configured.
- Replication does not replace backups.
- Replication supports encrypted objects with the correct IAM and KMS permissions.

---

# Summary

Amazon S3 Replication automatically copies objects between buckets to improve data durability, disaster recovery, compliance, and availability. It supports replication within the same region (SRR) and across different regions (CRR). Existing objects can be copied using **S3 Batch Replication**, while new objects are replicated automatically after the replication rule is configured.

---

## Repository

**Documentation:**  
https://github.com/SaimShaikh/Cloud_Computing/blob/main/AWS/AWS_S3/AWS_S3_Zip.md
