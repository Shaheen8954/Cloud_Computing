# Amazon S3 Bucket Types

Amazon S3 provides four bucket types, each optimized for different workloads:

1. General Purpose Bucket
2. Directory Bucket (S3 Express One Zone)
3. Table Bucket
4. Vector Bucket

---

# 1. General Purpose Bucket

The General Purpose Bucket is the traditional and most commonly used S3 bucket. It is suitable for storing almost any type of object, including images, videos, documents, backups, logs, and static website assets.

## Features

- Multi-AZ durability (99.999999999%)
- Versioning
- Lifecycle Rules
- Replication
- Object Lock
- Static Website Hosting
- Bucket Policies
- IAM Integration
- CORS
- Storage Classes

## Use Cases

- Image storage
- Video storage
- Backups
- Log archives
- Static website hosting
- Application assets

---

# 2. Directory Bucket (S3 Express One Zone)

Directory Buckets are optimized for ultra-low latency and very high request rates. Data resides within a single Availability Zone to minimize access latency.

## Features

- Single Availability Zone
- Extremely low latency
- High throughput
- Optimized for compute-intensive workloads

## Use Cases

- AI training
- Machine learning
- High-performance computing (HPC)
- Financial trading
- Video rendering

## Limitations

- Single-AZ storage
- Some traditional S3 features (such as versioning, lifecycle, and website hosting) are unavailable or have different support.

---

# 3. Table Bucket

Table Buckets are designed for structured tabular data used in analytics and data lakes.

## Features

- Optimized for analytical queries
- Supports modern open table formats
- Efficient metadata management
- Suitable for large-scale business analytics

## Use Cases

- Data lakes
- Business intelligence
- ETL pipelines
- SQL analytics

---

# 4. Vector Bucket

Vector Buckets are built for AI workloads that use vector embeddings.

## Features

- Stores vector embeddings
- Fast similarity search
- Optimized for AI and ML applications

## Use Cases

- Semantic search
- Retrieval-Augmented Generation (RAG)
- Recommendation systems
- AI chatbots
- Image and document search

---

# Comparison Table

| Feature | General Purpose | Directory Bucket | Table Bucket | Vector Bucket |
|----------|----------------|------------------|--------------|---------------|
| Purpose | General object storage | Ultra-fast object storage | Structured analytics | AI vector embeddings |
| Storage Model | Multi-AZ | Single AZ | Managed tables | Vector indexes |
| Latency | Low | Extremely low | Optimized for analytics | Optimized for similarity search |
| Best For | Images, videos, backups | HPC, AI training | Data lakes | AI applications |
| Static Website Hosting | Yes | No | No | No |
| Versioning | Yes | Limited / different support | Managed by table format | Not a primary feature |

---

# Memory Trick

- **General Purpose = Everything Bucket**
- **Directory = Speed Bucket**
- **Table = SQL Bucket**
- **Vector = AI Bucket**

---

# Interview Questions

### Which S3 bucket type is used for everyday object storage?

**Answer:** General Purpose Bucket.

### Which bucket offers the lowest latency?

**Answer:** Directory Bucket (S3 Express One Zone).

### Which bucket is best for analytics and data lakes?

**Answer:** Table Bucket.

### Which bucket is designed for AI vector embeddings?

**Answer:** Vector Bucket.

### Which bucket supports static website hosting?

**Answer:** General Purpose Bucket only.

---

# Summary

| Bucket Type | Best Use |
|-------------|----------|
| General Purpose | General object storage |
| Directory | Ultra-low latency workloads |
| Table | Analytics and data lakes |
| Vector | AI and semantic search |
