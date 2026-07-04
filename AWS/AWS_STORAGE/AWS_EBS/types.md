
# Amazon EBS (Elastic Block Store) Types

## What is Amazon EBS?

Amazon **Elastic Block Store (EBS)** is a block-level storage service designed for use with Amazon EC2 instances. It acts like a virtual hard disk that can be attached to an EC2 instance to store operating systems, applications, and data.

### Key Features

- Persistent storage (data remains even after the EC2 instance is stopped)
- Can be attached, detached, resized, and backed up
- Supports snapshots for backup and disaster recovery
- Provides different volume types based on performance and cost requirements

---

# Types of Amazon EBS Volumes

Amazon EBS volumes are divided into **two categories**:

1. SSD-backed Volumes
2. HDD-backed Volumes

```
                Amazon EBS
                     │
        ┌────────────┴────────────┐
        │                         │
   SSD-backed                 HDD-backed
        │                         │
  ┌─────┴─────┐             ┌─────┴─────┐
  │           │             │           │
General    Provisioned     st1        sc1
Purpose      IOPS
  │            │
gp3 gp2     io2 io1
```

---

# 1. SSD-backed Volumes

SSD (Solid State Drive) volumes are designed for workloads that require:

- Low latency
- High IOPS (Input/Output Operations Per Second)
- Fast random read/write operations

These are ideal for operating systems, databases, and business applications.

---

## A. gp3 (General Purpose SSD)

### Description

gp3 is the latest generation of General Purpose SSD volumes and is recommended by AWS for most workloads.

### Features

- Independent storage, IOPS, and throughput scaling
- Lower cost than gp2
- Consistent performance
- Suitable for both production and development environments

### Specifications

| Property | Value |
|----------|-------|
| Maximum Size | 64 TiB |
| Maximum IOPS | 16,000 |
| Maximum Throughput | 1,000 MB/s |

### Best Use Cases

- Boot volumes
- Web servers
- Application servers
- Virtual desktops
- Development and testing
- Small and medium-sized databases

### Advantages

- Lower cost
- Better performance
- Flexible scaling
- Recommended by AWS

---

## B. gp2 (General Purpose SSD)

### Description

gp2 is the previous generation General Purpose SSD volume.

Unlike gp3, its performance depends on the volume size.

### Example

| Volume Size | Baseline IOPS |
|-------------|---------------|
| 100 GB | 300 |
| 500 GB | 1,500 |
| 1 TB | 3,000 |

To increase performance, users often had to purchase larger storage volumes.

### Limitations

- Performance depends on storage size
- More expensive than gp3
- Considered a legacy option

---

## gp2 vs gp3

| Feature | gp2 | gp3 |
|----------|------|------|
| Storage and Performance | Linked | Independent |
| Cost | Higher | Lower |
| Performance | Depends on volume size | Configurable |
| AWS Recommendation | Legacy | Recommended |

---

# 2. Provisioned IOPS SSD

Provisioned IOPS SSD volumes are designed for applications requiring consistently high I/O performance and very low latency.

---

## A. io2 (Provisioned IOPS SSD)

### Description

io2 is designed for mission-critical workloads that require maximum durability and high IOPS.

### Features

- Very high IOPS
- Extremely low latency
- High durability (99.999%)
- Suitable for enterprise applications

### Specifications

| Property | Value |
|----------|-------|
| Maximum Size | 64 TiB |
| Maximum IOPS | Up to 256,000* |

> *Actual maximum depends on EC2 instance type.

### Best Use Cases

- Oracle Database
- Microsoft SQL Server
- SAP HANA
- Financial applications
- Banking systems
- Enterprise databases

### Advantages

- Excellent performance
- Highly reliable
- Consistent latency

---

## B. io1 (Provisioned IOPS SSD)

### Description

io1 is the older generation Provisioned IOPS SSD volume.

AWS recommends using io2 for new deployments.

### Best Use Cases

- Existing enterprise workloads
- Legacy high-performance databases

---

## io1 vs io2

| Feature | io1 | io2 |
|----------|------|------|
| Performance | High | Higher |
| Durability | Lower | Higher |
| Recommended | Legacy | Yes |

---

# HDD-backed Volumes

HDD (Hard Disk Drive) volumes are optimized for workloads requiring high throughput rather than high IOPS.

These are ideal for sequential read and write operations.

---

## A. st1 (Throughput Optimized HDD)

### Description

st1 is designed for workloads that process large amounts of sequential data.

### Specifications

| Property | Value |
|----------|-------|
| Maximum Throughput | Up to 500 MB/s |

### Best Use Cases

- Big data
- Hadoop
- Data warehouses
- Log processing
- Streaming applications
- ETL workloads

### Advantages

- High throughput
- Lower cost than SSD
- Excellent for large files

---

## B. sc1 (Cold HDD)

### Description

sc1 is the lowest-cost EBS storage option designed for infrequently accessed data.

### Best Use Cases

- Archives
- Backups
- Historical data
- Long-term log storage

### Advantages

- Lowest storage cost
- Suitable for rarely accessed data

### Limitations

- Lowest performance
- Not suitable for production databases

---

## st1 vs sc1

| Feature | st1 | sc1 |
|----------|------|------|
| Performance | Higher | Lower |
| Cost | Moderate | Lowest |
| Best For | Frequently accessed large files | Infrequently accessed data |

---

# Comparison of All EBS Volume Types

| Volume Type | Category | Performance | Best For | AWS Recommendation |
|--------------|----------|-------------|----------|--------------------|
| gp3 | SSD | Balanced | General workloads | ✅ Recommended |
| gp2 | SSD | Balanced | Legacy workloads | ❌ Legacy |
| io2 | SSD | Very High IOPS | Enterprise databases | ✅ Recommended |
| io1 | SSD | High IOPS | Legacy enterprise workloads | ❌ Legacy |
| st1 | HDD | High Throughput | Big data and analytics | ✅ Recommended |
| sc1 | HDD | Low Cost | Backups and archives | ✅ Recommended |

---

# Choosing the Right EBS Volume

| Workload | Recommended Volume |
|----------|--------------------|
| EC2 Boot Volume | gp3 |
| Web Server | gp3 |
| Application Server | gp3 |
| Docker Host | gp3 |
| Development Environment | gp3 |
| Jenkins Server | gp3 |
| MySQL / PostgreSQL | gp3 or io2 |
| Oracle Database | io2 |
| SQL Server | io2 |
| SAP HANA | io2 |
| Big Data Analytics | st1 |
| Log Processing | st1 |
| Media Streaming | st1 |
| Backup Storage | sc1 |
| Archive Storage | sc1 |

---

# Memory Trick

Remember **GISC**:

- **G** → **gp3** → General Purpose (Default Choice)
- **I** → **io2** → Intensive IOPS (High-performance databases)
- **S** → **st1** → Sequential Throughput (Big data)
- **C** → **sc1** → Cold Storage (Backups and archives)

---

# Interview Questions

### 1. Which EBS volume is recommended for most EC2 workloads?

**Answer:** gp3

---

### 2. Which EBS volume provides the highest IOPS?

**Answer:** io2

---

### 3. Which EBS volume is best for Oracle or SQL Server databases?

**Answer:** io2

---

### 4. Which EBS volume is optimized for sequential throughput?

**Answer:** st1

---

### 5. Which EBS volume is the cheapest?

**Answer:** sc1

---

### 6. Why is gp3 preferred over gp2?

**Answer:**

- Lower cost
- Better performance
- Independent scaling of storage, IOPS, and throughput
- More flexible than gp2

---

# Quick Summary

| EBS Type | Purpose |
|----------|---------|
| **gp3** | General-purpose SSD for most workloads |
| **gp2** | Older general-purpose SSD (legacy) |
| **io2** | High-performance enterprise databases |
| **io1** | Older provisioned IOPS SSD (legacy) |
| **st1** | High-throughput HDD for analytics and big data |
| **sc1** | Low-cost HDD for backups and archives |

---

# Key Takeaways

- **gp3** is the default and recommended EBS volume for most workloads.
- **gp2** is a legacy option and should generally be avoided for new deployments.
- **io2** is designed for mission-critical applications requiring high IOPS and low latency.
- **io1** is the older version of io2 and is mainly used for legacy workloads.
- **st1** is optimized for high-throughput, sequential workloads such as big data and log processing.
- **sc1** is the most cost-effective option for infrequently accessed data like backups and archives.
- Select the EBS volume type based on your application's performance, throughput, durability, and cost requirements.
