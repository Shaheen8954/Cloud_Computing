# Object Ownership in Amazon S3 (Interview Ready)

## Definition

**S3 Object Ownership** is a feature that determines **who owns the objects uploaded to an S3 bucket** and whether **Access Control Lists (ACLs)** are used to manage access.

Before Object Ownership, if another AWS account uploaded an object to your bucket, **the uploader became the object owner**, which could cause access and permission issues.

Object Ownership solves this by making the **bucket owner automatically own all uploaded objects** (depending on the selected setting).

---

# Why Do We Need Object Ownership?

## Scenario

Imagine **Company A** owns an S3 bucket.

- Company B uploads files into Company A's bucket.
- By default, **Company B becomes the owner of the uploaded objects.**
- Company A may not be able to read, modify, or delete those objects unless Company B grants permission.

This creates management and security challenges.

With **Object Ownership**, Company A (the bucket owner) automatically owns every uploaded object.

---

# Object Ownership Settings

## 1. Bucket Owner Enforced (Recommended & Default)

This is the **default setting** for all newly created S3 buckets.

### How It Works

- Bucket owner automatically owns every uploaded object.
- ACLs are completely disabled.
- Access is managed only through:
  - IAM Policies
  - Bucket Policies
  - Service Control Policies (SCPs) in AWS Organizations

### Advantages

- Simplifies permission management.
- Improves security.
- Eliminates ACL-related confusion.
- Recommended by AWS.

### Example

**Bucket Owner:** Account A

**Uploader:** Account B

**Result:**

- Account A automatically owns the uploaded object.
- Account B does not own the object.
- ACLs are ignored.

---

## 2. Bucket Owner Preferred

ACLs remain enabled.

If the uploader includes the following ACL while uploading:

```bash
bucket-owner-full-control
```

then:

- The bucket owner becomes the object owner.

If this ACL is **not** used:

- The uploader remains the object owner.

### Example

```bash
aws s3 cp image.jpg s3://mybucket \
  --acl bucket-owner-full-control
```

**Result:**

- Bucket owner becomes the owner of the uploaded object.

---

## 3. Object Writer

This is the **legacy (old) behavior**.

ACLs remain enabled.

The uploader owns the uploaded object.

### Example

Account B uploads an object.

**Result:**

- Account B owns the object.
- The bucket owner may not have permission unless granted through an ACL or bucket policy.

---

# Comparison Table

| Feature | Bucket Owner Enforced | Bucket Owner Preferred | Object Writer |
|----------|-----------------------|------------------------|---------------|
| ACLs | Disabled | Enabled | Enabled |
| Object Owner | Bucket Owner | Depends on ACL | Uploader |
| AWS Recommended | ✅ Yes | Sometimes | ❌ No |
| Best For | Most workloads | Legacy applications | Legacy systems |

---

# Customer Scenario

### Scenario

A customer says:

> "Vendors from different AWS accounts upload invoices into my bucket, but sometimes I cannot delete or access them."

### Solution

Enable **Bucket Owner Enforced** so that:

- The bucket owner automatically owns every uploaded object.
- Permissions are managed using **Bucket Policies** instead of ACLs.

---

# Interview Questions

## Q1. What is Object Ownership?

### Answer

S3 Object Ownership determines **who owns objects stored in an S3 bucket** and whether **ACLs are used** for access control.

---

## Q2. Which Object Ownership option does AWS recommend?

### Answer

**Bucket Owner Enforced** because it:

- Disables ACLs.
- Automatically makes the bucket owner the owner of every uploaded object.
- Simplifies permission management.

---

## Q3. What happens when ACLs are disabled?

### Answer

Only the following are used for access control:

- IAM Policies
- Bucket Policies
- Service Control Policies (SCPs)

Object ACLs have **no effect**.

---

## Q4. Can another AWS account upload objects when Bucket Owner Enforced is enabled?

### Answer

**Yes.**

If the bucket policy allows uploads:

- Another AWS account can upload objects.
- The bucket owner automatically becomes the owner of those objects.

---

## Q5. What is the difference between Object Ownership and Bucket Policy?

### Answer

| Object Ownership | Bucket Policy |
|------------------|---------------|
| Determines who owns uploaded objects. | Determines who can access the bucket and objects. |
| Controls whether ACLs are used. | Grants or denies permissions using IAM policy syntax. |

---

# Easy Way to Remember

- ✅ **Bucket Owner Enforced** → Bucket owner **always owns** the object (**ACLs disabled**)
- ⚠️ **Bucket Owner Preferred** → Bucket owner owns the object **only if** `bucket-owner-full-control` ACL is used
- ❌ **Object Writer** → **Uploader owns** the object

---

# 30-Second Interview Answer

> "S3 Object Ownership controls who owns objects uploaded to an S3 bucket and whether ACLs are used for access control. It provides three settings: Bucket Owner Enforced, Bucket Owner Preferred, and Object Writer. AWS recommends Bucket Owner Enforced because it disables ACLs, automatically makes the bucket owner the owner of every uploaded object, simplifies permission management, and improves security."
