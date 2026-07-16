# Amazon S3 vs Amazon EBS

## What is Amazon S3?

Amazon Simple Storage Service (Amazon S3) is an object storage service that offers industry-leading scalability, data availability, security, and performance. It stores data as **objects** within **buckets**, where each object consists of:

- The data itself
- Metadata
- A unique identifier (Object Key)

---

# Key Characteristics of Amazon S3

| Feature | Details |
|---------|----------|
| **Storage Type** | Object Storage |
| **Access Method** | HTTP/HTTPS using REST API |
| **Scalability** | Virtually unlimited |
| **Durability** | 99.999999999% (11 Nines) |
| **Availability** | 99.99% (S3 Standard) |
| **Maximum Object Size** | 5 TB |
| **Access Scope** | Accessible from anywhere over the Internet |
| **Pricing** | Pay only for storage used, requests, and data transfer |

---

# Common Use Cases of Amazon S3

- Data Lakes
- Big Data Analytics
- Backup & Disaster Recovery
- Static Website Hosting
- Media Storage
- Content Distribution
- Application Logs
- Archive Storage (Glacier)
- Compliance Storage

---

# What is Amazon EBS?

Amazon Elastic Block Store (Amazon EBS) is a high-performance **block storage** service designed specifically for Amazon EC2 instances.

Unlike S3, EBS behaves like a physical hard disk attached to a virtual machine. It must be formatted with a file system before use.

---

# Key Characteristics of Amazon EBS

| Feature | Details |
|---------|----------|
| **Storage Type** | Block Storage |
| **Access Method** | Attached (Mounted) to an EC2 Instance |
| **Maximum Size** | Up to 64 TiB per Volume |
| **Durability** | 99.8%–99.999% (depends on volume type) |
| **Availability** | Replicated within a Single Availability Zone |
| **Performance** | Low Latency, High IOPS |
| **Maximum IOPS** | Up to 256,000 (io2 Block Express) |
| **Access Scope** | Same Availability Zone |
| **Pricing** | Pay for Provisioned Storage + Provisioned IOPS |

---

# Common Use Cases of Amazon EBS

- EC2 Boot Volumes
- Relational Databases
- NoSQL Databases
- SAP Applications
- Oracle Databases
- Microsoft SQL Server
- Container Storage
- Development Environments
- High Performance Applications

---

# Amazon S3 vs Amazon EBS

| Feature | Amazon S3 | Amazon EBS |
|---------|-----------|------------|
| Storage Type | Object Storage | Block Storage |
| Access | HTTP/HTTPS API | Mounted to EC2 |
| Maximum Size | Unlimited (5 TB per object) | Up to 64 TiB per volume |
| Latency | Milliseconds | Sub-millisecond |
| Durability | 11 Nines | 99.8%–99.999% |
| Availability | Multi-AZ | Single AZ |
| Redundancy | Automatic | Within AZ |
| File System | No | Yes |
| Pricing | Pay for What You Use | Pay for What You Provision |
| Concurrent Access | Multiple Users | Single EC2 (except Multi-Attach) |
| Data Modification | Replace Entire Object | Modify Individual Blocks |
| Versioning | Built-in | Snapshots |
| Encryption | SSE-S3, SSE-KMS, SSE-C | KMS Encryption |

---

# Architecture Difference

## Amazon S3

```text
User/Application
        │
        ▼
HTTPS Request
        │
        ▼
S3 Endpoint
        │
        ▼
Bucket
        │
        ▼
Object
        │
        ▼
Automatically Replicated Across Multiple AZs
```

### Characteristics

- Independent AWS Service
- No EC2 Required
- API-Based Access
- Highly Durable
- Global Access

---

## Amazon EBS

```text
Application
      │
      ▼
File System
      │
      ▼
EBS Volume
      │
      ▼
EC2 Instance
      │
      ▼
Replication Within Same AZ
```

### Characteristics

- Attached to EC2
- Appears as Local Disk
- Requires File System
- Low Latency
- High Performance

---

# Cost Comparison

## Amazon S3 Pricing (Approx.)

| Storage Class | Cost |
|--------------|------|
| S3 Standard | ~$0.023/GB-Month |
| S3 Standard-IA | ~$0.0125/GB-Month |
| Glacier Instant Retrieval | ~$0.004/GB-Month |
| PUT Requests | ~$0.005 per 1,000 |
| GET Requests | ~$0.0004 per 1,000 |

---

## Amazon EBS Pricing (Approx.)

| Volume Type | Cost |
|-------------|------|
| gp3 | ~$0.08/GB-Month |
| io2 | ~$0.125/GB-Month |
| st1 | ~$0.045/GB-Month |
| Snapshots | ~$0.05/GB-Month |

---

# Key Cost Difference

## Amazon S3

- Pay only for stored data
- No pre-allocation required
- Cheaper for large storage

## Amazon EBS

- Pay for provisioned storage
- Charged even if unused
- Higher cost but better performance

---

# When to Use Amazon S3

Choose Amazon S3 if you need to:

- Store unlimited files
- Host static websites
- Store images and videos
- Create Data Lakes
- Backup files
- Archive data
- Store application logs
- Use CloudFront
- Access data globally

---

# When to Use Amazon EBS

Choose Amazon EBS if you need to:

- Boot an EC2 Instance
- Run Databases
- Install Operating Systems
- Low Latency Storage
- Traditional File Systems
- Enterprise Applications
- Transaction Processing

---

# Can We Use Both Together?

**Yes.**

A typical AWS architecture uses both services.

```text
                 Internet
                     │
                     ▼
               Load Balancer
                     │
                     ▼
                EC2 Instance
                 /        \
                /          \
               ▼            ▼
        EBS Volume      Amazon S3
      (OS + Database) (Images, Logs,
                       Backups, Files)
```

### Typical Usage

**EBS**

- Operating System
- Database
- Application Files

**S3**

- Static Assets
- Images
- Videos
- Logs
- Backups
- Archives

**Note:** EBS Snapshots are stored in Amazon S3 behind the scenes.

---

# Security Features

## Amazon S3 Security

- Bucket Policies
- IAM Policies
- Access Control Lists (ACLs)
- Block Public Access
- Server-Side Encryption (SSE-S3)
- SSE-KMS
- SSE-C
- VPC Endpoints
- Object Lock
- Access Logging
- AWS CloudTrail

---

## Amazon EBS Security

- KMS Encryption
- Encryption at Rest
- Encryption in Transit
- IAM Policies
- Snapshot Sharing Controls
- Private by Default

---

# Quick Summary

| Requirement | Recommended Service |
|-------------|---------------------|
| Store files from anywhere | Amazon S3 |
| Attach storage to EC2 | Amazon EBS |
| Cheapest Storage | Amazon S3 |
| Fastest Storage | Amazon EBS |
| Database | Amazon EBS |
| Static Website | Amazon S3 |
| Unlimited Storage | Amazon S3 |
| Boot Volume | Amazon EBS |

---

# Conclusion

Amazon S3 and Amazon EBS are complementary AWS storage services designed for different workloads.

**Amazon S3** is ideal for storing massive amounts of unstructured data with extremely high durability, scalability, and global accessibility.

**Amazon EBS** is designed for workloads requiring high-performance block storage, such as operating systems, databases, and enterprise applications.

Rather than replacing each other, they are commonly used together in modern AWS architectures.

---

# Pro Tip

Ask yourself one question:

> **"Does my application need storage mounted like a hard drive?"**

- **Yes** → Use **Amazon EBS**
- **No** → Use **Amazon S3**

This simple question helps you choose the correct AWS storage service in most scenarios.
