# 💰 Amazon S3 Requester Pays

## What is Requester Pays?

Requester Pays is an Amazon S3 billing feature where the requester pays for request and data transfer charges instead of the bucket owner.

The bucket owner still pays for object storage.

---

# Why Do We Need It?

Without Requester Pays, the bucket owner pays for every download and API request.

Requester Pays shifts those costs to the requester.

---

# Real-Life Analogy

Imagine a public library.

- The library stores books.
- Visitors pay for photocopies.

The library pays to keep the books.

Visitors pay only when they use additional services.

---

# Billing Comparison

| Activity | Normal Bucket | Requester Pays Bucket |
|-----------|---------------|-----------------------|
| Storage | Bucket Owner | Bucket Owner |
| GET Request | Bucket Owner | Requester |
| LIST Request | Bucket Owner | Requester |
| Download | Bucket Owner | Requester |
| Data Transfer Out | Bucket Owner | Requester |

---

# How It Works

```text
Requester
    │
    ▼
Amazon S3
    │
Checks whether requester agreed to pay
    │
    ├── Yes → Serve Object
    └── No → Reject Request
```

---

# Enable Requester Pays

1. Open Amazon S3.
2. Select the bucket.
3. Open the **Properties** tab.
4. Scroll to **Requester Pays**.
5. Enable it.
6. Save the changes.

---

# AWS CLI

```bash
aws s3 cp s3://mybucket/file.txt . \
--request-payer requester
```

---

# Python (Boto3)

```python
s3.get_object(
    Bucket="mybucket",
    Key="file.txt",
    RequestPayer="requester"
)
```

---

# Advantages

- Reduces costs for bucket owners.
- Fair billing model.
- Ideal for large public datasets.
- Easy to configure.

---

# Disadvantages

- Requires authenticated AWS users.
- Not suitable for public websites.
- Adds extra configuration for clients.

---

# Requester Pays vs Bucket Policy

| Bucket Policy | Requester Pays |
|---------------|----------------|
| Controls access | Controls billing |
| Security feature | Billing feature |

---

# Requester Pays vs CORS

| Requester Pays | CORS |
|----------------|------|
| Billing feature | Browser security feature |
| Works with CLI, SDKs, and APIs | Applies only to browsers |
| Does not grant permissions | Does not grant permissions |

---

# Common Mistakes

- Forgetting `--request-payer requester`
- Assuming Requester Pays grants access
- Using it for public websites

---

# Interview Questions

### What is Requester Pays?

An S3 feature that shifts request and download costs from the bucket owner to the requester.

### Who pays for object storage?

The bucket owner.

### Does Requester Pays replace IAM?

No.

IAM and bucket policies control access.

Requester Pays controls billing.

### Which AWS CLI flag is required?

```bash
--request-payer requester
```

### Can anonymous users access a Requester Pays bucket?

No. They must be authenticated AWS users so AWS can bill their account.

---

# Key Takeaways

- Requester Pays is a billing feature.
- Bucket owner pays for storage.
- Requester pays for requests and downloads.
- Permissions are still managed using IAM and bucket policies.
- Best suited for large shared datasets such as research, government, and educational data.
