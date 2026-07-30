# CLEANUP.md

# Cleanup Guide

---

# Overview

After completing this hands-on lab, it is important to clean up all AWS resources that are no longer required.

Cleaning up resources helps you:

- Prevent unnecessary AWS charges.
- Keep your AWS account organized.
- Avoid leaving unused IAM roles or policies.
- Prevent security risks caused by forgotten resources.

> **Note:** Before deleting any resource, ensure it is **not being used** by another application or project.

---

# Cleanup Order

For this lab, perform cleanup in the following order:

1. Stop Patch Operations
2. Delete Maintenance Window
3. Deregister Targets
4. Deregister Tasks
5. Delete Patch Policy (Quick Setup)
6. Review Patch Baselines
7. Remove IAM Policies (if created only for this lab)
8. Verify Managed Nodes
9. Review Amazon S3 Resources
10. Terminate EC2 Instance (Optional)

---

# Step 1 – Stop Patch Operations

## Why?

If a Maintenance Window is still active, AWS may continue executing scheduled patch tasks.

---

## Navigate

```
AWS Console
    ↓
Systems Manager
    ↓
Maintenance Windows
```

Locate your Maintenance Window.

Example:

```
weekly-patching
```

If a maintenance window is scheduled to run soon, disable it before deletion.

📸 **Screenshot**

```
images/cleanup-01-maintenance-window.png
```

---

# Step 2 – Delete Maintenance Window

Select the Maintenance Window.

Click:

```
Delete
```

Confirm the deletion.

### Why?

Maintenance Windows are scheduling resources.

If they remain active, AWS may continue trying to execute maintenance tasks.

---

# Step 3 – Deregister Targets

Navigate to:

```
Systems Manager
    ↓
Maintenance Windows
    ↓
Targets
```

Select the registered Managed Instance.

Click:

```
Deregister
```

### Why?

Targets define which instances receive maintenance tasks.

Removing them prevents accidental execution if the Maintenance Window is recreated later.

📸 **Screenshot**

```
images/cleanup-02-deregister-target.png
```

---

# Step 4 – Deregister Run Command Tasks

Navigate to:

```
Systems Manager
    ↓
Maintenance Windows
    ↓
Tasks
```

Select:

```
AWS-RunPatchBaseline
```

Click:

```
Deregister
```

### Why?

Tasks remain associated with the Maintenance Window until explicitly removed.

Removing unused tasks keeps the environment clean.

📸 **Screenshot**

```
images/cleanup-03-deregister-task.png
```

---

# Step 5 – Delete Patch Policy (Quick Setup)

Navigate to:

```
Systems Manager
    ↓
Quick Setup
```

Locate the Patch Policy created during the lab.

Example:

```
Patch-Policy-Ubuntu
```

Select it and click:

```
Delete
```

### Why?

Quick Setup automatically creates several Systems Manager resources.

Deleting the Patch Policy removes those associations and prevents future scheduled patch operations.

📸 **Screenshot**

```
images/cleanup-04-delete-patch-policy.png
```

---

# Step 6 – Review Patch Baselines

Navigate to:

```
Systems Manager
    ↓
Patch Manager
    ↓
Patch Baselines
```

If you created a **custom Patch Baseline** specifically for this lab:

- Review it.
- Delete it if it is no longer needed.

> **Important:** Do **not** delete AWS-managed default Patch Baselines.

### Why?

AWS-managed Patch Baselines are shared resources and are required by other Patch Manager configurations.

---

# Step 7 – Review IAM Role

Navigate to:

```
IAM
    ↓
Roles
```

Open the IAM Role attached to the EC2 instance.

Review the attached policies.

During our lab, we attached:

```
AmazonSSMManagedInstanceCore

CloudWatchAgentServerPolicy

AmazonS3FullAccess
```

### Should you remove them?

- If the EC2 instance will continue to use Systems Manager, **keep** these policies.
- If the instance is being terminated and the role was created only for this lab, remove or delete the role after confirming it is not used elsewhere.

> **Production Recommendation:** Instead of `AmazonS3FullAccess`, create a least-privilege policy granting access only to the required Quick Setup S3 bucket.

📸 **Screenshot**

```
images/cleanup-05-iam-role.png
```

---

# Step 8 – Verify Managed Nodes

Navigate to:

```
Systems Manager
    ↓
Fleet Manager
    ↓
Managed Nodes
```

Verify the Managed Node status.

If the EC2 instance is still running, it should remain listed.

If you terminate the instance, it will eventually disappear from the Managed Nodes list.

### Why?

This confirms that Systems Manager is no longer managing terminated resources.

---

# Step 9 – Review Amazon S3 Resources

Navigate to:

```
Amazon S3
```

Locate the Quick Setup bucket created during the lab.

Example:

```
aws-quicksetup-patchpolicy-031190641965-j2o87
```

### Should you delete it?

**No, not immediately.**

This bucket may still be used by other Systems Manager configurations.

Before deleting it:

- Verify that no Patch Policies depend on it.
- Confirm that no other Quick Setup configurations are using it.

If it is exclusively associated with this lab and no longer required, it can be deleted.

📸 **Screenshot**

```
images/cleanup-06-s3-bucket.png
```

---

# Step 10 – EC2 Instance (Optional)

If the EC2 instance was created **only** for this lab:

Navigate to:

```
EC2
    ↓
Instances
```

Select the instance.

Click:

```
Instance State
    ↓
Terminate Instance
```

### Why?

Stopping an instance still incurs charges for attached resources such as EBS volumes.

Terminating the instance removes compute charges.

> **Note:** Review EBS volume settings if "Delete on termination" is disabled.

📸 **Screenshot**

```
images/cleanup-07-terminate-instance.png
```

---

# Cost Optimization Checklist

Review the following resources before leaving the AWS Console.

| Resource | Review | Delete if Unused |
|----------|--------|------------------|
| EC2 Instance | ✅ | ✅ |
| EBS Volume | ✅ | ✅ |
| Maintenance Window | ✅ | ✅ |
| Patch Policy | ✅ | ✅ |
| Custom Patch Baseline | ✅ | ✅ |
| IAM Role | ✅ | Only if unused |
| Amazon S3 Bucket | ✅ | Only if unused |
| CloudWatch Logs | Optional | Optional |

---

# Verification Checklist

After cleanup, verify:

- Maintenance Window deleted.
- Patch Tasks removed.
- Targets deregistered.
- Patch Policy deleted.
- Custom Patch Baselines removed (if created).
- IAM Role reviewed.
- EC2 instance terminated (if applicable).
- No unexpected scheduled patch operations remain.

---

# Production Recommendations

In a production environment:

- Do **not** delete Patch Policies that are actively used.
- Keep AWS-managed Patch Baselines.
- Replace broad IAM permissions with least-privilege policies.
- Review Quick Setup dependencies before deleting S3 buckets.
- Tag all Systems Manager resources for easier management.
- Document cleanup procedures as part of operational runbooks.

---

# Key Takeaways

Cleaning up AWS resources is just as important as deploying them.

A proper cleanup process:

- Reduces AWS costs.
- Improves account organization.
- Eliminates unused resources.
- Reduces security risks.
- Prevents accidental maintenance operations.
- Demonstrates good cloud governance practices.

Following this cleanup guide ensures that your AWS environment remains secure, organized, and cost-effective after completing the Patch Manager lab.
