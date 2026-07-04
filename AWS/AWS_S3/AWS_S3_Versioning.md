# Amazon S3 Versioning

## 📌 Overview

Amazon S3 Versioning is a feature that allows you to preserve, retrieve, and restore every version of an object stored in an S3 bucket. Instead of overwriting an existing object, Amazon S3 creates a new version whenever the same object is uploaded again.

Versioning helps protect data from accidental deletion, unintended overwrites, and application failures.

---

# Objectives

- Understand what S3 Versioning is
- Learn how Versioning works
- Enable Versioning on an S3 bucket
- Upload multiple versions of the same object
- Restore previous versions
- Understand Delete Markers
- Learn Versioning best practices

---

# Prerequisites

- AWS Account
- IAM User with S3 permissions
- AWS Management Console access
- Basic understanding of Amazon S3

---

# Architecture

```
                Upload File
                     │
                     ▼
              Amazon S3 Bucket
                     │
      ┌──────────────┴──────────────┐
      │                             │
 Version 1                    Version 2
      │                             │
      └──────────────┬──────────────┘
                     │
                Version 3 (Latest)
                     │
                 Delete Object
                     │
                     ▼
              Delete Marker Created
                     │
             Previous Versions Safe
```

---

# What is S3 Versioning?

S3 Versioning is a bucket-level feature that stores multiple versions of an object. Every upload of the same object creates a unique version instead of replacing the existing object.

Example:

```
report.pdf

Version 1
Version 2
Version 3 (Latest)
```

---

# Benefits

- Protects against accidental deletion
- Prevents accidental overwrites
- Easy rollback to previous versions
- Supports disaster recovery
- Maintains object history
- Works with Lifecycle Policies
- Supports Cross-Region Replication (CRR)
- Compatible with S3 Object Lock

---

# Versioning States

| State | Description |
|---------|------------|
| Disabled | Bucket stores only the latest object |
| Enabled | Every upload creates a new version |
| Suspended | Existing versions remain, but new uploads no longer create new versions |

> **Note:** Once Versioning is enabled, it cannot be permanently disabled. It can only be suspended.

---

# How Versioning Works

### Initial Upload

```
report.pdf

Version ID: V1
```

### Second Upload

```
report.pdf

Version ID: V2
```

### Third Upload

```
report.pdf

Version ID: V3
```

Current object:

```
report.pdf

├── V3 (Latest)
├── V2
└── V1
```

---

# Delete Behavior

Without Versioning

```
Delete Object

↓

Object Permanently Deleted
```

With Versioning

```
Delete Object

↓

Delete Marker Created

↓

Previous Versions Still Exist
```

Example

```
Delete Marker

↓

Version 3

↓

Version 2

↓

Version 1
```

Deleting the Delete Marker restores the latest version.

---

# Storage Cost

Each object version consumes storage.

Example

```
File Size = 100 MB

Uploaded 5 times

Total Storage

100 MB × 5

= 500 MB
```

AWS charges for every stored version.

---

# Lifecycle Management

Lifecycle Rules can automatically:

- Move old versions to Glacier
- Delete expired versions
- Reduce storage costs

Example

```
30 Days

↓

Transition to Glacier

↓

365 Days

↓

Delete Permanently
```

---

# Real-World Use Cases

### Backup

Keep multiple backup versions of important files.

### Document Management

Track document revisions.

### Source Code Storage

Recover previous releases or builds.

### Disaster Recovery

Restore deleted or corrupted files quickly.

### Compliance

Maintain historical object versions for auditing.

---

# Best Practices

- Enable Versioning for production buckets.
- Use Lifecycle Policies to manage old versions.
- Enable Server-Side Encryption (SSE).
- Use Cross-Region Replication (CRR) for disaster recovery.
- Monitor storage usage and costs.
- Regularly review old object versions.

---

# Advantages

- Data protection
- Easy recovery
- Object history
- Supports rollback
- Increased data durability
- Disaster recovery support
- Audit capability

---

# Limitations

- Increased storage cost
- More object versions to manage
- Delete Markers may cause confusion
- Permanent deletion requires deleting specific versions

---

# Interview Questions

### Can Versioning be disabled?

No. It can only be suspended.

---

### Does deleting an object remove every version?

No.

A Delete Marker is created while previous versions remain available.

---

### Does every object have a Version ID?

Yes.

Every object version receives a unique Version ID.

---

### Do we pay for previous versions?

Yes.

AWS charges for the storage used by every object version.

---

### Can Versioning recover overwritten files?

Yes.

Previous versions can be restored at any time.

---
