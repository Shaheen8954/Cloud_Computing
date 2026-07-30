---

# Section 2 – Configure Patch Policy (Quick Setup)

## What is a Patch Policy?

Before we configure Patch Manager, it is important to understand **Patch Policies**.

A Patch Policy tells AWS:

- Which instances should be patched.
- When they should be patched.
- Which Patch Baseline should be used.
- Whether the instance should reboot after patching.
- Which Maintenance Window should execute the task.

Instead of manually configuring all these resources individually, AWS **Quick Setup** automatically creates and manages them.

In our lab, we used **Quick Setup** because it simplifies the entire patch management configuration.

---

# Step 1 – Open AWS Systems Manager

Login to the AWS Management Console.

Navigate to:

```

AWS Console

↓

Systems Manager

```

You should now see the **Systems Manager Dashboard**.

📸 **Screenshot**

```
images/05-systems-manager-dashboard.png
```

### Explanation

AWS Systems Manager is a centralized service used to manage AWS resources.

It provides many features, including:

- Patch Manager
- Fleet Manager
- Run Command
- Session Manager
- Automation
- State Manager
- Parameter Store

For this lab, we will primarily use **Patch Manager**, **Fleet Manager**, and **Run Command**.

---

# Step 2 – Open Quick Setup

From the left navigation pane, select:

```

Quick Setup

```

The Quick Setup dashboard allows you to configure common AWS Systems Manager features with minimal manual effort.

📸 **Screenshot**

```
images/06-quick-setup-dashboard.png
```

### Why are we using Quick Setup?

Without Quick Setup, you would need to manually create:

- Patch Baselines
- Maintenance Windows
- IAM permissions
- Associations
- Scheduling rules

Quick Setup automates these configurations following AWS best practices.

---

# Step 3 – Create a Patch Policy

Click:

```

Create

↓

Patch Policy

```

📸 **Screenshot**

```
images/07-create-patch-policy.png
```

You will now see the Patch Policy configuration page.

---

# Step 4 – Configure Patch Policy

During our lab, we configured the Patch Policy through the Quick Setup wizard.

The wizard contains several sections.

We will configure them one by one.

---

## Section A – General Configuration

Provide a meaningful name.

Example:

```

Patch-Policy-Ubuntu

```

### Explanation

The name is only used for identification.

Choose a descriptive name that clearly identifies the operating system or environment.

Examples:

```

Production-Ubuntu

Development-Linux

Ubuntu-Patch-Policy

```

📸 **Screenshot**

```
images/08-patch-policy-name.png
```

---

## Section B – Operating System

Select

```

Ubuntu

```

### Explanation

AWS provides different Patch Baselines for different operating systems.

Choosing the correct operating system ensures that Patch Manager downloads compatible updates.

Selecting an incorrect operating system may result in:

- Missing updates
- Patch failures
- Unsupported package repositories

📸 **Screenshot**

```
images/09-operating-system.png
```

---

## Section C – Scan Schedule

Configure how frequently AWS should scan the instance for missing patches.

Example:

```

Weekly

```

### Explanation

The **Scan** operation only checks for missing patches.

It does **not** install updates.

AWS generates a compliance report showing which updates are missing.

---

## Section D – Install Schedule

Configure when approved patches should be installed.

Example:

```

Weekly

```

### Explanation

Unlike **Scan**, the **Install** operation downloads and installs approved updates.

Organizations often configure:

- Daily scanning
- Weekly installation

This allows administrators to review missing patches before installation.

📸 **Screenshot**

```
images/10-scan-install-schedule.png
```

---

## Section E – Reboot Option

Choose:

```

No Reboot

```

### Why did we choose "No Reboot"?

During production maintenance, unexpected reboots may interrupt running applications.

Choosing **No Reboot** allows administrators to manually restart servers after verifying that patch installation completed successfully.

Other available options include automatic reboot after patch installation.

---

## Section F – Target Instances

Select the EC2 instances that should receive the Patch Policy.

In our lab, we selected our Ubuntu Managed Instance.

AWS also supports targeting by:

- Tags
- Resource Groups
- Entire Organizations

### Why use Tags?

In production, administrators usually tag instances such as:

```

Environment=Production

Environment=Development

Application=WebServer

```

Patch Policies can then automatically apply to all matching instances.

📸 **Screenshot**

```
images/11-target-instances.png
```

---

# Step 5 – Review Configuration

Before clicking **Create**, review all settings.

Example checklist:

| Setting | Value |
|----------|-------|
| Operating System | Ubuntu |
| Scan Schedule | Weekly |
| Install Schedule | Weekly |
| Reboot Option | No Reboot |
| Target | Managed EC2 Instance |

Verify everything carefully.

Mistakes at this stage may cause patches to install on unintended servers.

---

# Step 6 – Create the Patch Policy

Click:

```

Create
```

AWS Quick Setup now begins creating the required resources automatically.

These resources may include:

- Patch Policy
- Patch Associations
- Patch Baselines
- SSM Associations
- Maintenance configuration
- Amazon S3 configuration files

📸 **Screenshot**

```
images/12-patch-policy-created.png
```

---

# What Happens Behind the Scenes?

Although the wizard appears simple, AWS performs several actions automatically.

Quick Setup:

1. Creates Patch Policy configurations.
2. Associates the policy with the selected Managed Nodes.
3. Generates configuration files.
4. Stores Patch Policy metadata.
5. Creates or updates Systems Manager Associations.
6. Prepares Patch Manager for future executions.

One important observation from our lab was that AWS also created an Amazon S3 bucket.

Example:

```

aws-quicksetup-patchpolicy-031190641965-j2o87

```

Inside that bucket, AWS stored:

```

baseline_overrides.json

```

We later discovered this file while troubleshooting a Patch Manager execution failure.

This file is used internally by Patch Manager to determine which Patch Baseline configuration should be applied during execution.

---

# Verify Patch Policy

Navigate to:

```

Systems Manager

↓

Quick Setup

↓

Patch Policies

```

Verify that the Patch Policy status is:

```

Successful

```

📸 **Screenshot**

```
images/13-patch-policy-success.png
```

---

# Common Mistakes

❌ Selecting the wrong operating system.

❌ Choosing incorrect target instances.

❌ Forgetting to attach an IAM Role.

❌ Creating a Patch Policy before the instance becomes a Managed Node.

❌ Ignoring Quick Setup status messages.

---

# Section Summary

In this section, we:

- Opened AWS Systems Manager.
- Used Quick Setup to simplify Patch Manager configuration.
- Created a Patch Policy.
- Selected Ubuntu as the operating system.
- Configured scan and install schedules.
- Selected "No Reboot" after installation.
- Applied the policy to our Managed EC2 instance.
- Verified successful creation.
- Learned that AWS automatically creates supporting resources such as Patch Policy metadata and Amazon S3 configuration files.

At this point, the Patch Policy is ready. The next step is to configure a **Maintenance Window**, which controls **when** patching tasks are allowed to run.
