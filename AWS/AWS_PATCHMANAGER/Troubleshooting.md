# TROUBLESHOOTING.md

# Troubleshooting Guide

This document contains all the issues encountered during the implementation of **AWS Systems Manager Patch Manager** along with their root causes and resolutions.

These are **real issues** experienced during the lab and the exact troubleshooting process followed to resolve them.

---

# Table of Contents

1. EC2 Instance Not Showing as Managed Node
2. SSM Agent Not Running
3. No "Run" Button in Maintenance Window
4. AWS-RunPatchBaseline Document Confusion
5. Run Command Execution Failed
6. 403 Forbidden (HeadObject)
7. baseline_overrides.json Issue
8. AmazonS3ExpressFullAccess vs AmazonS3FullAccess
9. Patch Command Status Verification
10. Best Troubleshooting Practices

---

# Issue 1 – EC2 Instance Not Appearing as a Managed Node

## Problem

The EC2 instance does not appear under:

```
Systems Manager
→ Fleet Manager
→ Managed Nodes
```

Without appearing here, Patch Manager cannot communicate with the instance.

📸 **Screenshot**

```
images/troubleshooting-01-managed-node-missing.png
```

---

## Possible Causes

- SSM Agent is not installed.
- SSM Agent service is stopped.
- IAM Role is missing.
- IAM Role does not contain the required permissions.
- Instance has no internet connectivity.
- Required VPC Endpoints are missing.

---

## Verification

Connect to the EC2 instance and run:

```bash
sudo systemctl status amazon-ssm-agent
```

Expected output:

```text
Active: active (running)
```

---

Verify the IAM Role attached to the EC2 instance.

Minimum required policy:

```
AmazonSSMManagedInstanceCore
```

---

# Issue 2 – SSM Agent Not Running

## Problem

Patch Manager cannot execute commands.

Fleet Manager shows the instance as:

```
Connection Lost
```

or

```
Offline
```

---

## Verification

```bash
sudo systemctl status amazon-ssm-agent
```

If stopped:

```bash
sudo systemctl start amazon-ssm-agent
```

Enable automatic startup:

```bash
sudo systemctl enable amazon-ssm-agent
```

---

## Why does this happen?

The SSM Agent is responsible for receiving commands from AWS Systems Manager.

Without the agent, AWS has no way to communicate with the EC2 instance.

---

# Issue 3 – No "Run" Button in Maintenance Window

## Problem

While configuring the Maintenance Window, we expected to find a **Run** button to execute the patch immediately.

However, no such button was available.

📸 **Screenshot**

```
images/troubleshooting-02-no-run-button.png
```

---

## Why?

This is the expected AWS behavior.

A Maintenance Window **does not execute tasks manually**.

Instead, it performs the registered tasks only when:

- The scheduled time arrives, or
- The task is executed separately through **Run Command**.

---

## Resolution

To test the Patch Baseline immediately, navigate to:

```
Systems Manager
→ Run Command
→ Run Command
```

Select:

```
AWS-RunPatchBaseline
```

Execute the document manually.

---

# Issue 4 – Confusion About AWS-RunPatchBaseline

## Problem

We were unsure which Systems Manager document should be selected.

AWS provides hundreds of predefined documents.

---

## Resolution

Use:

```
AWS-RunPatchBaseline
```

This AWS-managed document performs both:

- Patch Scan
- Patch Installation

depending on the selected operation.

---

# Issue 5 – Patch Command Failed

## Problem

The Run Command execution failed.

Status:

```
Failed
```

instead of

```
Success
```

---

## Investigation

Navigate to:

```
Systems Manager
→ Run Command
→ Command History
```

Open the failed command.

Select:

```
View Output
```

Read the complete execution log.

This step is important because the overall status only shows **Failed**.

The detailed output explains the actual cause.

---

# Issue 6 – 403 Forbidden (HeadObject)

## Problem

The command output contained:

```text
botocore.exceptions.ClientError

An error occurred (403)
when calling the HeadObject operation

Forbidden

Payload failed to start

exit status 170
```

📸 **Screenshot**

```
images/troubleshooting-03-403-error.png
```

---

## Initial Analysis

At first glance, the error appeared to be related to:

- Patch Manager
- Systems Manager
- SSM Agent

However, the important clue was:

```
HeadObject
```

HeadObject is an Amazon S3 API operation.

This indicated that Patch Manager was attempting to access an object stored in Amazon S3.

---

## Next Investigation

We opened the command parameters.

There we found:

```
BaselineOverride

s3://aws-quicksetup-patchpolicy-031190641965-j2o87/baseline_overrides.json
```

This revealed that Patch Manager was attempting to read:

```
baseline_overrides.json
```

from the Quick Setup S3 bucket.

---

# Issue 7 – baseline_overrides.json

## Investigation

We navigated to:

```
Amazon S3
```

The bucket existed.

Example:

```
aws-quicksetup-patchpolicy-031190641965-j2o87
```

The bucket also contained:

```
baseline_overrides.json
```

Therefore:

❌ Missing bucket was **not** the problem.

❌ Missing file was **not** the problem.

---

## Conclusion

The file existed.

Patch Manager simply did not have permission to read it.

---

# Issue 8 – Incorrect IAM Policy

## IAM Role Before

The EC2 instance contained:

```
AmazonSSMManagedInstanceCore

CloudWatchAgentServerPolicy

AmazonS3ExpressFullAccess
```

---

## Why was this incorrect?

```
AmazonS3ExpressFullAccess
```

only grants permissions for:

```
Amazon S3 Express One Zone
```

Quick Setup stores Patch Policy files in a **standard Amazon S3 bucket**.

Therefore, the required permission was not available.

---

## Resolution

We removed:

```
AmazonS3ExpressFullAccess
```

Attached:

```
AmazonS3FullAccess
```

Waited several minutes for IAM propagation.

Executed the Patch Baseline again.

📸 **Screenshot**

```
images/troubleshooting-04-iam-policy.png
```

---

# Why did AmazonS3FullAccess work?

Patch Manager needed permission to perform:

```
HeadObject

GetObject
```

against:

```
baseline_overrides.json
```

The broader AmazonS3FullAccess policy included these permissions.

In a production environment, however, it is recommended to follow the **Principle of Least Privilege** and grant access only to the specific S3 bucket and object required by Patch Manager.

---

# Issue 9 – Verifying the Command Result

After fixing the IAM policy, verify the command execution.

Navigate to:

```
Systems Manager
→ Run Command
→ Command History
```

Open the latest execution.

Check:

- Overall Status
- Detailed Status
- Plugin Output

Expected:

```
Success
```

---

# Troubleshooting Checklist

Before running Patch Manager, verify the following:

| Check | Status |
|--------|--------|
| EC2 Running | ✅ |
| IAM Role Attached | ✅ |
| AmazonSSMManagedInstanceCore | ✅ |
| SSM Agent Running | ✅ |
| Managed Node Online | ✅ |
| Internet Connectivity | ✅ |
| Correct Patch Document Selected | ✅ |
| Correct IAM Policy | ✅ |

---

# Best Practices

- Always verify the instance is listed under **Managed Nodes** before patching.
- Check **Run Command Output** instead of relying only on the overall status.
- Read the complete error message before changing configurations.
- Verify IAM permissions whenever an AWS API operation returns **403 Forbidden**.
- Understand what AWS service the error references (for example, `HeadObject` indicates Amazon S3).
- Apply the Principle of Least Privilege in production environments instead of using broad permissions such as `AmazonS3FullAccess`.

---

# Key Lessons Learned

During this lab we learned that troubleshooting Patch Manager requires understanding the interaction between multiple AWS services:

- Systems Manager
- Patch Manager
- Run Command
- Fleet Manager
- IAM
- Amazon S3
- SSM Agent

A failure in any one of these components can prevent successful patch execution.

Instead of assuming Patch Manager itself is broken, always trace the workflow step by step, identify which AWS service is reporting the error, and investigate that service first.
