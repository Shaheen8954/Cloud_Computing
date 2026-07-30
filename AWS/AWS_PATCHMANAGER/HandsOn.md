# HANDS_ON.md

# Patch Amazon EC2 Instances using AWS Systems Manager Patch Manager

---

# Lab Overview

In this hands-on lab, we will configure **AWS Systems Manager Patch Manager** to automate the patching process for an Amazon EC2 instance running Ubuntu Linux.

Instead of manually connecting to the server and installing updates, we will configure AWS Systems Manager to scan for missing patches, install approved updates, and generate compliance reports.

This guide documents every step performed during the lab, including the exact AWS Console navigation, configuration values, commands, troubleshooting steps, and explanations.

By following this guide, even someone with no prior AWS experience should be able to reproduce the same setup.

---

# Lab Objectives

After completing this lab, you will be able to:

- Register EC2 instances as Managed Nodes
- Configure Patch Manager
- Understand Patch Policies
- Create Maintenance Windows
- Register Targets
- Register Patch Tasks
- Execute AWS-RunPatchBaseline
- Verify patch execution
- Troubleshoot Patch Manager failures

---

# Lab Environment

| Component | Value |
|-----------|-------|
| Cloud Provider | AWS |
| Service | Systems Manager |
| Operating System | Ubuntu 24.04 LTS |
| Patch Method | Patch Manager |
| Patch Document | AWS-RunPatchBaseline |
| Instance Type | Amazon EC2 |

---

# Architecture

```

Administrator

│

▼

AWS Systems Manager

│

▼

Patch Manager

│

▼

Maintenance Window

│

▼

Run Command

│

▼

AWS-RunPatchBaseline

│

▼

Managed EC2 Instance

│

▼

Ubuntu APT

│

▼

Install Updates

```

---

# Prerequisites

Before starting the lab, verify the following:

- AWS account with sufficient permissions
- Ubuntu EC2 instance running
- IAM Role attached to the EC2 instance
- SSM Agent installed
- Internet connectivity or Systems Manager VPC Endpoints

---

# Step 1 – Verify EC2 Instance

Open the AWS Console.

Navigate to:

```

AWS Console

↓

EC2

↓

Instances

```

Verify that your Ubuntu instance is in the **Running** state.

Expected values:

| Property | Expected Value |
|----------|----------------|
| Instance State | Running |
| Platform | Ubuntu Linux |
| IAM Role | Attached |

📸 **Screenshot**

```
images/01-ec2-instance-running.png
```

### Why is this important?

Patch Manager can only patch instances that are currently running. If the instance is stopped, AWS Systems Manager cannot communicate with it.

---

# Step 2 – Verify IAM Role

Select the EC2 instance.

Navigate to:

```

Actions

↓

Security

↓

Modify IAM Role

```

Verify that an IAM Role is attached.

The role should include the following policies:

```

AmazonSSMManagedInstanceCore

CloudWatchAgentServerPolicy

AmazonS3FullAccess

```

📸 **Screenshot**

```
images/02-iam-role.png
```

### Why is this important?

The IAM Role allows the EC2 instance to communicate with AWS Systems Manager and access other AWS services required during patching.

Without this role, Patch Manager cannot execute commands on the instance.

---

# Step 3 – Verify SSM Agent

Connect to the EC2 instance using SSH or Session Manager.

Run the following command:

```bash
sudo systemctl status amazon-ssm-agent
```

Expected output:

```
● amazon-ssm-agent.service - Amazon SSM Agent
Loaded: loaded
Active: active (running)
```

📸 **Screenshot**

```
images/03-ssm-agent-status.png
```

### Why is this important?

The SSM Agent acts as the communication bridge between the EC2 instance and AWS Systems Manager.

If the service is not running, Patch Manager cannot perform any operations.

---

# Step 4 – Verify Managed Node

Return to the AWS Console.

Navigate to:

```

AWS Systems Manager

↓

Fleet Manager

↓

Managed Nodes

```

Verify that your EC2 instance appears in the list.

Expected values:

| Property | Expected Value |
|----------|----------------|
| Ping Status | Online |
| Agent | Active |
| Platform | Ubuntu |
| Managed | Yes |

📸 **Screenshot**

```
images/04-managed-node.png
```

### Why is this important?

Only **Managed Nodes** can receive commands from AWS Systems Manager.

If your instance does not appear here, stop the lab and troubleshoot the SSM Agent, IAM Role, and network connectivity before proceeding.

---

# Environment Verification Checklist

Before moving to the next section, confirm the following:

| Check | Status |
|-------|--------|
| EC2 instance is running | ✅ |
| IAM Role attached | ✅ |
| SSM Agent active | ✅ |
| Managed Node online | ✅ |

If all checks are successful, continue to **Section 2 – Configure Patch Policy (Quick Setup)**.
