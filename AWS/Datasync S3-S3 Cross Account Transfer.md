# AWS S3 Cross-Region Data Transfer Automation

## Overview

This project demonstrates an event-driven cross-region data transfer architecture using:

- Amazon S3
- Amazon EventBridge
- AWS Lambda
- AWS DataSync
- IAM

Whenever a new object is uploaded to the source S3 bucket, an EventBridge event is generated. EventBridge invokes a Lambda function, and Lambda starts the AWS DataSync task. DataSync then transfers the object to the destination S3 bucket in another AWS region.

---

## Architecture

```text
                Object Created
                     │
                     ▼
        ┌──────────────────────────┐
        │ Source S3 Bucket         │
        │ ap-south-1               │
        │                          │
        │ datasync-source-         │
        │ s3-bucket-4632           │
        └────────────┬─────────────┘
                     │
                     │ S3 Object Created Event
                     ▼
        ┌──────────────────────────┐
        │ Amazon EventBridge       │
        │ ap-south-1               │
        │                          │
        │ event-bridge-rule        │
        └────────────┬─────────────┘
                     │
                     │ Invoke Lambda
                     ▼
        ┌──────────────────────────┐
        │ AWS Lambda               │
        │ ap-south-1               │
        │                          │
        │ start-datasync-task      │
        └────────────┬─────────────┘
                     │
                     │ StartTaskExecution
                     ▼
        ┌──────────────────────────┐
        │ AWS DataSync             │
        │ eu-west-1                │
        │                          │
        │ datasync-transfer-       │
        │ cross-region             │
        └────────────┬─────────────┘
                     │
                     │ Transfer Object
                     ▼
        ┌──────────────────────────┐
        │ Destination S3 Bucket    │
        │ eu-west-1                │
        │                          │
        │ datasync-destination-    │
        │ s3-bucket-4632           │
        └──────────────────────────┘
````

---

# 1. AWS Resources

| ResourceNameRegion    |                                       |              |
| --------------------- | ------------------------------------- | ------------ |
| Source S3 Bucket      | `datasync-source-s3-bucket-4632`      | `ap-south-1` |
| Destination S3 Bucket | `datasync-destination-s3-bucket-4632` | `eu-west-1`  |
| DataSync Task         | `datasync-transfer-cross-region`      | `eu-west-1`  |
| DataSync Location     | `loc-091adc8a76264f32c`               | `eu-west-1`  |
| DataSync IAM Role     | `s3-datasync-role`                    | Global       |
| Lambda Function       | `start-datasync-task`                 | `ap-south-1` |
| Lambda IAM Role       | `Lambda-execution-role`               | Global       |
| EventBridge Rule      | `event-bridge-rule`                   | `ap-south-1` |

### AWS Account

```text
121378764632
```

---

# 2. Source S3 Bucket

The source bucket was created in the Mumbai region.

```text
Bucket:
datasync-source-s3-bucket-4632

Region:
ap-south-1
```

This bucket is the starting point of the data transfer.

Whenever a new object is uploaded to this bucket, Amazon S3 generates an `Object Created` event.

---

# 3. Destination S3 Bucket

The destination bucket was created in the Ireland region.

```text
Bucket:
datasync-destination-s3-bucket-4632

Region:
eu-west-1
```

DataSync transfers the objects from the source bucket to this destination bucket.

---

# 4. Create DataSync IAM Role

An IAM role was created for AWS DataSync.

```text
Role Name:
s3-datasync-role
```


### Correct Trust Pcyoli

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "datasync.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

The important part is:

```json
"Service": "datasync.amazonaws.com"
```

This allows AWS DataSync to assume the IAM role.

---

# 5. Destination S3 Bucket Policy

The destination bucket was configured to allow the DataSync IAM role to perform the required S3 operations.

The principal was:

```text
arn:aws:iam::121378764632:role/s3-datasync-role
```

The required permissions included:

```text
s3:GetBucketLocation
s3:ListBucket
s3:ListBucketMultipartUploads
s3:AbortMultipartUpload
s3:DeleteObject
s3:GetObject
s3:ListMultipartUploadParts
s3:PutObject
s3:GetObjectTagging
s3:PutObjectTagging
```

The bucket resources were:

```text
arn:aws:s3:::datasync-destination-s3-bucket-4632

arn:aws:s3:::datasync-destination-s3-bucket-4632/*
```

The DataSync role uses these permissions when transferring objects to the destination bucket.

---

# 6. Create DataSync Destination Location

The destination S3 location was created in the same region as the destination bucket.

The destination bucket is located in:

```text
eu-west-1
```

An initial attempt was made from `ap-south-1`, which resulted in:

```text
The bucket is in this region: eu-west-1.
Please use this region to retry the request.
```

The location was then created in `eu-west-1`.

## CLI Command Used

```bash
aws datasync create-location-s3 \
  --region eu-west-1 \
  --s3-bucket-arn arn:aws:s3:::datasync-destination-s3-bucket-4632 \
  --s3-config '{
    "BucketAccessRoleArn":"arn:aws:iam::121378764632:role/s3-datasync-role"
  }'
```

The command returned the DataSync location ARN:

```text
arn:aws:datasync:eu-west-1:121378764632:location/loc-091adc8a76264f32c
```

---

# 7. Create DataSync Task

The DataSync task was created for transferring data between the source and destination S3 buckets.

```text
Task Name:
datasync-transfer-cross-region
```

### Task ARN

```text
arn:aws:datasync:eu-west-1:121378764632:task/task-010ca6252a1573139
```

The DataSync task was created in:

```text
eu-west-1
```

The destination S3 bucket was also located in:

```text
eu-west-1
```

---

# 8. Lambda Function

A Lambda function was created to start the DataSync task.

```text
Function Name:
start-datasync-task
```

Region:

```text
ap-south-1
```

The Lambda function is triggered by EventBridge.

---

# 9. Lambda Execution Role

The Lambda function uses the following IAM role:

```text
Lambda-execution-role
```

The role provides Lambda with permission to:

- Write logs to CloudWatch Logs
- Start the specific DataSync task

The DataSync permission is:

```text
datasync:StartTaskExecution
```

The permission is restricted to the DataSync task ARN:

```text
arn:aws:datasync:eu-west-1:121378764632:task/task-010ca6252a1573139
```

---

# 10. Lambda Function Code

The Lambda function uses Boto3 to start the DataSync task.

```python
import boto3

datasync = boto3.client(
    "datasync",
    region_name="eu-west-1"
)

TASK_ARN = "arn:aws:datasync:eu-west-1:121378764632:task/task-010ca6252a1573139"


def lambda_handler(event, context):

    print("========== S3 EVENT RECEIVED ==========")
    print(event)

    print("========== STARTING DATASYNC ==========")

    response = datasync.start_task_execution(
        TaskArn=TASK_ARN
    )

    print("========== DATASYNC STARTED ==========")
    print(response)

    return {
        "statusCode": 200,
        "body": "DataSync task started successfully"
    }
```

---

# 11. Lambda Handler Configuration

The Lambda handler was configured as:

```text
lambda_function.lambda_handler
```

The function file is:

```text
lambda_function.py
```

The handler function is:

```text
lambda_handler
```

Therefore:

```text
lambda_function.lambda_handler
```

means:

```text
Python file          Function
     │                   │
     ▼                   ▼
lambda_function.py → lambda_handler
```

---

# 12. Lambda Deployment Issue

Initially, the Lambda code/handler was not deployed or configured correctly.

Because of this, the Lambda function did not execute the expected DataSync operation.

The handler was corrected to:

```text
lambda_function.lambda_handler
```

After deploying the correct code and handler configuration, the Lambda function started working correctly.

---

# 13. Manual Lambda Test

Before configuring the complete event-driven automation, the Lambda function was tested manually.

The purpose of the manual test was to verify that:

```text
Lambda → DataSync
```

was working correctly.

The Lambda successfully started the DataSync task.

This confirmed that the Lambda execution role had the required permission:

```text
datasync:StartTaskExecution
```

---

# 14. Enable Amazon EventBridge for S3

Amazon EventBridge integration was enabled on the source S3 bucket.

Path:

```text
S3
→ Source Bucket
→ Properties
→ Event notifications
→ Amazon EventBridge
```

The source bucket was configured to send events to EventBridge.

The bucket was:

```text
datasync-source-s3-bucket-4632
```

---

# 15. Create EventBridge Rule

An EventBridge rule was created.

```text
Rule Name:
event-bridge-rule
```

The rule listens for S3 object creation events.

The EventBridge rule was configured in:

```text
ap-south-1
```

---

# 16. EventBridge Event Selection

Initially, the EventBridge rule was configured for a DataSync event:

```text
DataSync Task Execution State Change
```

This was incorrect for the required architecture.

The rule was changed to:

```text
S3 (Simple Storage Service)
→ Object Created
```

The required event is generated when a new object is created in the source S3 bucket.

---

# 17. EventBridge Event Pattern

The final EventBridge event pattern was:

```json
{
  "source": [
    "aws.s3"
  ],
  "detail-type": [
    "Object Created"
  ],
  "detail": {
    "bucket": {
      "name": [
        "datasync-source-s3-bucket-4632"
      ]
    }
  }
}
```

This pattern ensures that the EventBridge rule matches an object-created event from the required source bucket.

---

# 18. EventBridge Target

The EventBridge target was configured as the Lambda function:

```text
start-datasync-task
```

The event flow is:

```text
S3 Object Created
        ↓
EventBridge Rule
        ↓
Lambda
        ↓
DataSync StartTaskExecution
```

The target input was configured as:

```text
Matched event
```

This allows the original EventBridge event to be passed to Lambda.

---

# 19. Remove Old Direct S3 → Lambda Trigger

Initially, there was an old direct S3 → Lambda trigger configured.

The old trigger was removed to avoid duplicate Lambda invocations.

The final architecture uses only:

```text
S3
 ↓
EventBridge
 ↓
Lambda
 ↓
DataSync
```

There is no direct:

```text
S3 → Lambda
```

trigger in the final architecture.

---

# 20. Final IAM Flow

There are two important IAM roles in this architecture.

## Lambda Execution Role

```text
Lambda
   │
   │ assumes
   ▼
Lambda-execution-role
   │
   │ datasynс:StartTaskExecution
   ▼
DataSync Task
```

## DataSync IAM Role

```text
DataSync
   │
   │ assumes
   ▼
s3-datasync-role
   │
   │ S3 permissions
   ▼
Destination S3 Bucket
```

---

# 21. Complete Event-Driven Flow

The final working architecture is:

```text
┌──────────────────────────────┐
│ Source S3                    │
│                              │
│ datasync-source-s3-bucket-   │
│ 4632                         │
│                              │
│ Region: ap-south-1           │
└──────────────┬───────────────┘
               │
               │ Object Created
               ▼
┌──────────────────────────────┐
│ EventBridge                  │
│                              │
│ event-bridge-rule            │
└──────────────┬───────────────┘
               │
               │ Invoke
               ▼
┌──────────────────────────────┐
│ Lambda                       │
│                              │
│ start-datasync-task          │
│                              │
│ Region: ap-south-1           │
└──────────────┬───────────────┘
               │
               │ StartTaskExecution
               ▼
┌──────────────────────────────┐
│ AWS DataSync                 │
│                              │
│ datasync-transfer-           │
│ cross-region                 │
│                              │
│ Region: eu-west-1            │
└──────────────┬───────────────┘
               │
               │ Transfer
               ▼
┌──────────────────────────────┐
│ Destination S3               │
│                              │
│ datasync-destination-s3-    │
│ bucket-4632                  │
│                              │
│ Region: eu-west-1            │
└──────────────────────────────┘
```

---

# 22. Troubleshooting

## Issue 1: DataSync Unable to Assume IAM Role

### Error

```text
DataSync service was unable to perform sts:AssumeRole
```

### Cause

The IAM role trust policy allowed:

```text
s3.amazonaws.com
```

instead of:

```text
datasync.amazonaws.com
```

### Fix

Change the trust relationship to:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "datasync.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

---

## Issue 2: S3 Bucket Region Mismatch

### Error

```text
The bucket is in this region: eu-west-1.
Please use this region to retry the request.
```

### Cause

The DataSync S3 location was initially created using the wrong region.

The destination bucket was in:

```text
eu-west-1
```

but the location creation was attempted in:

```text
ap-south-1
```

### Fix

Create the DataSync S3 location in:

```text
eu-west-1
```

using:

```bash
aws datasync create-location-s3 \
  --region eu-west-1 \
  --s3-bucket-arn arn:aws:s3:::datasync-destination-s3-bucket-4632 \
  --s3-config '{
    "BucketAccessRoleArn":"arn:aws:iam::121378764632:role/s3-datasync-role"
  }'
```

---

## Issue 3: Incorrect EventBridge Event

### Initial Event

```text
DataSync Task Execution State Change
```

### Required Event

```text
S3 → Object Created
```

### Fix

Configure the EventBridge rule to match:

```text
source:
aws.s3

detail-type:
Object Created

bucket:
datasync-source-s3-bucket-4632
```

---

## Issue 4: Lambda Handler Configuration

### Problem

The Lambda function did not execute the expected code.

### Fix

Configure the handler as:

```text
lambda_function.lambda_handler
```

Then deploy the Lambda code again.

---

## Issue 5: Duplicate Lambda Invocation

### Problem

An old direct S3 → Lambda trigger was still configured.

This could cause Lambda to be invoked through two different paths.

### Fix

Remove the old direct S3 → Lambda trigger.

The final event path should be:

```text
S3
 ↓
EventBridge
 ↓
Lambda
 ↓
DataSync
```

---

# 23. DataSync Executions

After the automation was working, DataSync executions were visible.

Example execution:

```text
exec-0f2ca956d39cd3bf3
```

Earlier executions included:

```text
exec-0f34354672f9725d7
exec-0367f1f10886e9214
```

These executions confirmed that Lambda successfully started the DataSync task.

---

# 24. Lambda Monitoring

Lambda monitoring showed successful invocations.

Observed result:

```text
Invocations: 5
Errors: 0
Success: 100%
```

This confirmed that EventBridge was successfully invoking Lambda.

---

# 25. Test Object

A test object named:

```text
git-task.png
```

was uploaded to the source S3 bucket.

The event flow automatically started:

```text
EventBridge
    ↓
Lambda
    ↓
DataSync
```

The object was then transferred to the destination S3 bucket.

---

# 26. Final Result

The automation worked successfully.

When a new object is uploaded to:

```text
datasync-source-s3-bucket-4632
```

the following process happens automatically:

```text
1. Object is uploaded to Source S3
             ↓
2. S3 generates Object Created event
             ↓
3. EventBridge receives the event
             ↓
4. EventBridge invokes Lambda
             ↓
5. Lambda calls StartTaskExecution
             ↓
6. DataSync starts
             ↓
7. DataSync transfers the object
             ↓
8. Object appears in Destination S3
```

The complete automation is:

```text
S3
 ↓
EventBridge
 ↓
Lambda
 ↓
DataSync
 ↓
Destination S3
```

No manual DataSync execution is required after the automation is configured.

```
