# Amazon S3 Event Notifications

## What are S3 Event Notifications?

Amazon S3 Event Notifications allow S3 to automatically notify or trigger other AWS services whenever specific events occur in a bucket, such as object uploads, deletions, restores, or tag updates.

Instead of continuously checking the bucket, S3 immediately sends an event when something changes.

---

## Why Do We Need Event Notifications?

Without Event Notifications:

```text
User uploads file
        │
        ▼
Application repeatedly checks S3
        │
        ▼
Eventually detects new file
```

Problems:

- Unnecessary API calls
- Delayed processing
- Increased cost
- Inefficient architecture

With Event Notifications:

```text
User uploads file
        │
        ▼
Amazon S3
        │
        ▼
Event Notification
        │
        ▼
AWS Lambda processes the file
```

---

## Real-Life Analogy

Think of a courier company.

Without notifications, you call every hour asking if your package has arrived.

With notifications, the courier sends you an SMS as soon as the package arrives.

S3 Event Notifications work the same way.

---

## Architecture

```text
          User Uploads File
                 │
                 ▼
          Amazon S3 Bucket
                 │
        Event Detected
                 │
                 ▼
      Event Notification
                 │
 ┌───────────────┼────────────────┐
 ▼               ▼                ▼
Lambda        SNS Topic        SQS Queue
```

---

## Supported Destinations

| Service | Purpose |
|----------|---------|
| AWS Lambda | Execute code automatically |
| Amazon SNS | Broadcast notifications |
| Amazon SQS | Queue events for asynchronous processing |
| Amazon EventBridge | Advanced event routing and filtering |

---

## Common Event Types

| Event | Description |
|--------|-------------|
| ObjectCreated:* | Triggered when an object is created |
| ObjectCreated:Put | Uploaded using PUT |
| ObjectCreated:Post | Uploaded using POST |
| ObjectCreated:Copy | Copied into the bucket |
| ObjectRemoved:* | Object deleted |
| ObjectRestore:* | Glacier restore events |
| Replication:* | Replication events |
| ObjectTagging:* | Object tag changes |

---

## Event Notification Examples

### Image Processing

```text
Upload image
      │
      ▼
S3 Bucket
      │
      ▼
Lambda
      │
      ▼
Generate thumbnail
```

### Video Processing

```text
Upload video
      │
      ▼
Lambda
      │
      ▼
MediaConvert
      │
      ▼
Multiple resolutions
```

### Virus Scanning

```text
Upload file
      │
      ▼
Lambda
      │
      ▼
Virus Scan
      │
 ┌────┴────┐
 ▼         ▼
Safe    Quarantine
```

---

## Filtering Notifications

### Prefix Filter

```text
images/
```

Only objects stored under the `images/` folder trigger notifications.

### Suffix Filter

```text
.jpg
```

Only JPEG images trigger notifications.

---

## Multiple Notification Rules

A single bucket can have multiple rules.

```text
images/*.jpg
        │
        ▼
Lambda

logs/*.txt
        │
        ▼
SQS

videos/*.mp4
        │
        ▼
SNS
```

---

## Limitations

- Best-effort delivery
- Event ordering is not guaranteed
- Duplicate events are possible
- Applications should be idempotent

---

## Security

- Configure IAM permissions for destination services.
- Keep bucket and destination services in the same Region when possible.
- Use CloudWatch for monitoring and troubleshooting.

---

## Real-World Use Cases

- Automatically resize uploaded images
- Scan uploaded files for malware
- Send email or SMS notifications
- Trigger ETL pipelines
- Convert uploaded videos
- Generate reports after file uploads

---

## Event Notifications vs Polling

| Event Notifications | Polling |
|---------------------|----------|
| Automatic | Manual checking |
| Immediate | Delayed |
| Cost-efficient | Higher compute cost |
| Event-driven | Time-driven |

---

## Interview Questions

### What are S3 Event Notifications?

They automatically trigger AWS services or send notifications when specified events occur in an S3 bucket.

### Which AWS services are supported?

- AWS Lambda
- Amazon SNS
- Amazon SQS
- Amazon EventBridge

### Can notifications be filtered?

Yes. Notifications support prefix and suffix filters.

### Are Event Notifications guaranteed to be delivered exactly once?

No. Delivery is best-effort, and duplicate events are possible. Applications should be idempotent.

### What is the difference between Event Notifications and EventBridge?

Event Notifications provide direct integrations with Lambda, SNS, and SQS using simple filters, while EventBridge offers advanced routing, filtering, transformation, and integration with many AWS services.

---

## Summary

- Event Notifications enable event-driven automation.
- They eliminate polling and reduce costs.
- Lambda, SNS, SQS, and EventBridge are supported destinations.
- Prefix and suffix filters help target specific objects.
- Applications should handle duplicate events safely.
