# 🔒 Amazon S3 Object Lock

## What is S3 Object Lock?

Amazon S3 Object Lock is a feature that prevents an object version from being **deleted or modified** for a specified period. It helps organizations meet compliance requirements and protects data from accidental deletion, malicious attacks, and ransomware.

---

## Real-Life Example

Think of important school mark sheets stored in a locked cabinet.

- Students can view them.
- Teachers can read them.
- Nobody can modify or destroy them until authorized.

Object Lock works similarly for S3 objects.

---

## Why Do We Need Object Lock?

Without Object Lock:

- Accidental deletion
- Malicious deletion
- Ransomware attacks
- Compliance violations

With Object Lock:

- Data remains protected until the retention period expires.

---

## Prerequisites

- Object Lock must be enabled **when creating the bucket**.
- **Versioning is mandatory** because Object Lock protects object versions.

---

## Object Lock Modes

### 1. Governance Mode

- Prevents most users from deleting or overwriting objects.
- Authorized users with the `s3:BypassGovernanceRetention` permission can bypass the lock.

**Use Cases**

- Internal backups
- Business documents
- Temporary compliance

---

### 2. Compliance Mode

- Prevents deletion or modification by **everyone**, including the root user.
- No one can bypass the lock until the retention period expires.

**Use Cases**

- Banking
- Healthcare
- Government
- Legal records
- Financial data

---

## Governance vs Compliance

| Feature | Governance | Compliance |
|----------|------------|------------|
| Normal users can delete | ❌ | ❌ |
| Admin can bypass | ✅ | ❌ |
| Root user can bypass | ✅ (with permission) | ❌ |
| Highest protection | ❌ | ✅ |

---

## Retention Period

A retention period defines how long an object version remains protected.

Example:

- Start: 1 January
- End: 1 July

During this period:

- Delete ❌
- Overwrite ❌
- Modify ❌

After expiration:

- Operations are allowed.

---

## Legal Hold

Legal Hold protects an object indefinitely until it is manually removed.

Unlike retention periods:

- No expiration date
- Used during investigations or legal cases

---

## Retention vs Legal Hold

| Retention | Legal Hold |
|-----------|------------|
| Time-based | Event-based |
| Automatic expiry | Manual removal |
| Uses an expiration date | No expiration |

---

## Workflow

```text
Upload Object
      │
Enable Object Lock
      │
Choose Mode
      │
Governance / Compliance
      │
Set Retention Period
      │
AWS Blocks:
- Delete
- Overwrite
- Replace
      │
Retention Expires
      │
Object Can Be Modified
```

---

## Enabling Object Lock

1. Create an S3 bucket.
2. Enable **Object Lock** during bucket creation.
3. Versioning is enabled (required).
4. Upload objects.
5. Configure retention mode and duration.
6. AWS enforces the protection.

---

## Versioning Example

```
resume.pdf

Version 1 (Locked)
        │
Upload New Version
        │
Version 2
```

Each version has its own independent retention settings.

---

## Ransomware Protection

Object Lock helps protect backup data by preventing ransomware from:

- Deleting backups
- Overwriting backups
- Encrypting protected object versions

---

## Advantages

- Prevents accidental deletion
- Protects against ransomware
- Supports compliance requirements
- Preserves historical object versions
- Secures critical backups

---

## Disadvantages

- Cannot be enabled on an existing bucket
- Compliance Mode cannot be bypassed
- Incorrect retention periods may delay updates
- Requires Versioning (additional storage costs)

---

## Interview Questions

### What is Object Lock?

Object Lock prevents object versions from being deleted or overwritten for a defined retention period or until a legal hold is removed.

### What are the two modes?

- Governance Mode
- Compliance Mode

### Can Object Lock be enabled on an existing bucket?

No.

### Does it require Versioning?

Yes.

### Can the root user delete objects in Compliance Mode?

No.

### Difference between Retention and Legal Hold?

| Retention | Legal Hold |
|-----------|------------|
| Time-based | Manual removal |
| Automatically expires | No expiration |

---

## Memory Trick

**Object Lock = Locked Locker**

- Governance = Manager has a master key.
- Compliance = Nobody has the key until time expires.
- Legal Hold = Locker stays sealed until manually unlocked.

---

## Summary

- Protects S3 object versions from deletion or modification.
- Requires Versioning.
- Must be enabled during bucket creation.
- Offers Governance and Compliance modes.
- Supports Retention Periods and Legal Holds.
- Commonly used for backups, compliance, and ransomware protection.
