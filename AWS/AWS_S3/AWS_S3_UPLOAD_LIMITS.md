# Amazon S3 Upload Limits

## Overview

Amazon S3 is designed to store virtually unlimited amounts of data, but there are specific limits on how large an individual object can be and how uploads are performed. Understanding these limits is important for designing scalable and reliable storage solutions.

---

# Maximum Object Size

- **Maximum object size:** **5 TB**

Examples:

- `image.jpg` → 3 MB ✅
- `movie.mp4` → 8 GB ✅
- `database-backup.sql` → 2 TB ✅
- `vm-image.vmdk` → 5 TB ✅
- `backup.tar` → 6 TB ❌

---

# Single PUT Upload Limit

A standard `PutObject` request can upload a file up to:

**5 GB**

If the object is larger than 5 GB, AWS requires **Multipart Upload**.

---

# Multipart Upload

Multipart Upload divides a large file into multiple smaller parts.

Example:

```
100 GB File

Part 1 → 10 GB
Part 2 → 10 GB
Part 3 → 10 GB
...
Part 10 → 10 GB

↓

S3 combines all parts into one object.
```

---

# Multipart Upload Limits

| Property | Limit |
|----------|-------|
| Maximum object size | 5 TB |
| Maximum number of parts | 10,000 |
| Minimum part size | 5 MB (except last part) |
| Maximum part size | 5 GB |

---

# Minimum Part Size

Each part must be at least **5 MB**, except the last part.

Example:

```
23 MB File

Part 1 = 5 MB
Part 2 = 5 MB
Part 3 = 5 MB
Part 4 = 5 MB
Part 5 = 3 MB
```

Valid because only the last part is smaller than 5 MB.

---

# Maximum Number of Parts

An object can be divided into a maximum of **10,000 parts**.

---

# Maximum Part Size

Each individual part can be up to **5 GB**.

---

# Benefits of Multipart Upload

- Upload very large files
- Faster uploads through parallel processing
- Resume interrupted uploads
- Retry only failed parts
- Better reliability for unstable network connections

---

# Parallel Uploads

Multipart Upload allows multiple parts to upload simultaneously.

```
Part 1
Part 2
Part 3
Part 4
Part 5

↓

Uploaded in parallel
```

---

# Resume Interrupted Uploads

If the upload stops after several completed parts, only the remaining parts need to be uploaded.

---

# AWS CLI Support

The AWS CLI automatically switches to Multipart Upload for large files when appropriate.

Example:

```bash
aws s3 cp backup.zip s3://mybucket/
```

---

# Upload Limits by Method

| Upload Method | Maximum Size |
|--------------|-------------|
| AWS Console | Up to 5 TB (uses multipart automatically) |
| PUT Object API | 5 GB |
| Multipart Upload | 5 TB |
| AWS CLI | 5 TB |
| AWS SDK | 5 TB |

---

# Interview Questions

### What is the maximum object size in Amazon S3?

**Answer:** 5 TB.

---

### What is the maximum upload size using PutObject?

**Answer:** 5 GB.

---

### When should Multipart Upload be used?

**Answer:** Whenever the object size exceeds 5 GB. It is also recommended for large files because it provides better performance and fault tolerance.

---

### What is the minimum part size?

**Answer:** 5 MB, except for the last part.

---

### How many parts can an object have?

**Answer:** Up to 10,000 parts.

---

# Real-World Example

A company uploads a **500 GB** database backup to S3.

Instead of uploading it as one large file:

- The backup is divided into multiple parts.
- Several parts are uploaded simultaneously.
- Failed parts are retried individually.
- After all uploads finish, Amazon S3 combines them into one 500 GB object.

---

# Key Takeaways

- Maximum object size: **5 TB**
- Single PUT upload limit: **5 GB**
- Multipart Upload is required for files larger than 5 GB.
- Minimum part size: **5 MB** (except the last part)
- Maximum part size: **5 GB**
- Maximum number of parts: **10,000**
- Multipart Upload supports parallel uploads and resume capability.
