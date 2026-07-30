# THEORY.md

# AWS Systems Manager Patch Manager - Theory

---

# Table of Contents

1. Introduction
2. What is Patching?
3. Why Patching is Important?
4. Challenges of Manual Patching
5. What is AWS Systems Manager?
6. What is AWS Systems Manager Patch Manager?
7. How Patch Manager Works
8. Components of Patch Manager
9. Managed Nodes
10. SSM Agent
11. IAM Role
12. Patch Baselines
13. Patch Policies
14. Maintenance Windows
15. AWS Run Command
16. AWS-RunPatchBaseline Document
17. Scan vs Install
18. Patch Compliance
19. Patch Groups
20. Quick Setup
21. Amazon S3 in Patch Manager
22. Fleet Manager
23. Complete Patching Workflow
24. Best Practices
25. Production Architecture
26. Key Takeaways

---

# 1. Introduction

Modern IT infrastructures contain hundreds or even thousands of servers. Every operating system receives regular updates from its vendor.

These updates may include:

- Security fixes
- Bug fixes
- Performance improvements
- Stability improvements
- New features

If these updates are not installed regularly, servers become vulnerable to cyberattacks, malware, and software bugs.

Imagine an organization with 500 Linux servers. Logging into each server manually to install updates would be slow, inconsistent, and prone to human error.

To solve this challenge, AWS provides **Systems Manager Patch Manager**, a service that automates patch management across EC2 instances.

---

# 2. What is Patching?

A **patch** is a small software update released by the operating system or application vendor to improve or fix existing software.

Think of a patch as a repair kit.

Just as a mechanic repairs problems in a car, software patches repair problems in operating systems and applications.

A patch may:

- Fix security vulnerabilities
- Remove software bugs
- Improve performance
- Add hardware compatibility
- Improve stability

Example:

Ubuntu releases security updates almost every week.

Without installing them, attackers may exploit known vulnerabilities.

---

# 3. Why is Patching Important?

Regular patching provides several benefits.

## Security

Most cyberattacks exploit known vulnerabilities that already have available patches.

Installing security patches reduces the attack surface.

Example:

A newly discovered Linux kernel vulnerability allows remote code execution.

Ubuntu releases a security patch.

Servers that install the patch are protected.

Servers that delay patching remain vulnerable.

---

## Performance

Many patches optimize system performance.

Example:

Memory management improvements.

Filesystem optimizations.

Kernel scheduling improvements.

---

## Stability

Updates fix software crashes and unexpected behavior.

---

## Compliance

Many organizations must follow compliance standards.

Examples:

- ISO 27001
- PCI DSS
- HIPAA
- SOC 2

These standards often require regular operating system patching.

---

# 4. Challenges of Manual Patching

Without automation, administrators must:

- SSH into every Linux server
- Run update commands
- Monitor failures
- Reboot systems
- Record compliance reports

Problems include:

- Time consuming
- Human error
- Missed servers
- No centralized reporting
- Difficult scheduling
- Inconsistent patch versions

---

# 5. What is AWS Systems Manager?

AWS Systems Manager (SSM) is a management service that allows administrators to securely manage AWS resources from a central location.

Instead of logging into every EC2 instance individually, administrators use Systems Manager to:

- Execute commands
- Install patches
- Collect inventory
- Automate maintenance
- Manage parameters
- Store secrets
- Access instances without SSH

Patch Manager is one feature of Systems Manager.

---

# 6. What is Patch Manager?

AWS Systems Manager Patch Manager is a feature of Systems Manager that automates operating system patch management.

It can:

- Scan servers
- Detect missing updates
- Install approved patches
- Schedule maintenance
- Generate compliance reports
- Patch hundreds of servers simultaneously

Supported operating systems include:

- Ubuntu
- Debian
- Amazon Linux
- RHEL
- Rocky Linux
- SUSE
- Windows Server

---

# 7. How Patch Manager Works

The complete workflow is:

```

AWS Systems Manager
│
▼
Patch Manager
│
▼
Patch Baseline
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
SSM Agent
│
▼
Ubuntu APT
│
▼
Install Updates

```

Each component has a specific responsibility.

---

# 8. Components of Patch Manager

Patch Manager is made up of several building blocks.

- Managed Nodes
- SSM Agent
- IAM Role
- Patch Baseline
- Patch Policy
- Maintenance Window
- Run Command
- Fleet Manager
- Compliance Reports

Understanding each component is essential before starting the hands-on lab.

---

# 9. Managed Nodes

A **Managed Node** is any server that AWS Systems Manager can communicate with.

Examples include:

- EC2 instances
- On-premises servers
- Virtual machines

An EC2 instance becomes a Managed Node when:

- SSM Agent is installed.
- The IAM role allows Systems Manager access.
- The instance can communicate with the Systems Manager endpoints.

You can verify Managed Nodes by navigating to:

```

Systems Manager
→ Fleet Manager
→ Managed Nodes

```

Only Managed Nodes can be patched.

---

# 10. SSM Agent

SSM Agent is software installed on the operating system.

Think of it as a communication bridge between your EC2 instance and AWS Systems Manager.

Without SSM Agent:

- Run Command fails.
- Patch Manager cannot execute.
- Fleet Manager cannot manage the instance.

Verify the agent on Ubuntu:

```bash
sudo systemctl status amazon-ssm-agent
```

---

# 11. IAM Role

The EC2 instance must have an IAM Role that allows Systems Manager to communicate with it.

Minimum policy:

```

AmazonSSMManagedInstanceCore

```

During our lab, we also attached:

- CloudWatchAgentServerPolicy
- AmazonS3FullAccess

This was necessary because Quick Setup stored Patch Policy configuration in an Amazon S3 bucket.

---

# 12. Patch Baselines

A Patch Baseline defines **which patches are approved for installation**.

It answers questions such as:

- Which updates are allowed?
- Which updates are rejected?
- How long should AWS wait before automatically approving a patch?

Think of a Patch Baseline as a rulebook.

Instead of installing every available update immediately, Patch Manager follows the rules defined in the baseline.

This helps organizations avoid installing unstable updates before testing them.

---

# 13. Patch Policies

A Patch Policy combines:

- Patch Baseline
- Scan schedule
- Install schedule
- Reboot behavior
- Target instances

In our lab, Patch Policies were configured through **Quick Setup**.

Quick Setup automatically created the required configuration, including an S3 bucket that stored the baseline override configuration.

---

# 14. Maintenance Windows

A Maintenance Window defines **when** maintenance tasks are allowed to run.

Examples:

- Every Sunday at 2:00 AM
- Every Saturday night
- First Monday of every month

Instead of installing updates immediately, administrators choose a maintenance window to minimize business disruption.

---

# 15. AWS Run Command

Run Command allows administrators to execute predefined Systems Manager Documents on Managed Nodes.

Patch Manager uses Run Command internally.

The document used during our lab was:

```
AWS-RunPatchBaseline
```

---

# 16. AWS-RunPatchBaseline Document

This is an AWS-managed Systems Manager Document.

Its responsibilities include:

- Scan for missing patches
- Install approved patches
- Generate compliance data
- Report execution status

Instead of writing shell scripts, administrators simply execute this document.

---

# 17. Scan vs Install

Patch Manager supports two operations.

### Scan

- Detects missing patches
- Does not install updates
- Generates a compliance report

### Install

- Downloads approved patches
- Installs updates
- Updates compliance status
- May reboot the instance if configured

---

# 18. Patch Compliance

Patch compliance reports show whether an instance is:

- Compliant
- Non-compliant

Administrators use these reports to identify servers that are missing security updates.

---

# 19. Patch Groups

Patch Groups allow administrators to apply different Patch Baselines to different groups of servers.

Example:

Development Servers

- Install updates immediately.

Production Servers

- Wait seven days before installing updates.

---

# 20. Quick Setup

Quick Setup simplifies Systems Manager configuration.

Instead of manually creating all resources, Quick Setup automatically configures:

- IAM permissions
- Patch Policies
- Amazon S3 bucket
- Baseline override files

During our lab, Quick Setup created:

```
aws-quicksetup-patchpolicy-031190641965-j2o87
```

This bucket stored:

```
baseline_overrides.json
```

---

# 21. Why Amazon S3 is Used

Quick Setup stores Patch Policy configuration files in Amazon S3.

When Patch Manager starts, it retrieves these configuration files before scanning or installing patches.

During our lab, Patch Manager attempted to read:

```
baseline_overrides.json
```

A missing permission caused the following error:

```
403 Forbidden
HeadObject
```

We resolved the issue by correcting the IAM permissions.

---

# 22. Fleet Manager

Fleet Manager displays all Managed Nodes.

It helps administrators verify:

- Online status
- Operating system
- Agent status
- Connectivity

Before running Patch Manager, always ensure that the instance appears as **Online**.

---

# 23. Complete Patching Workflow

```
Launch EC2
      │
      ▼
Attach IAM Role
      │
      ▼
Install SSM Agent
      │
      ▼
Register Managed Node
      │
      ▼
Configure Patch Policy
      │
      ▼
Create Maintenance Window
      │
      ▼
Register Target
      │
      ▼
Register Task
      │
      ▼
Execute AWS-RunPatchBaseline
      │
      ▼
Patch Compliance Report
```

---

# 24. Best Practices

- Always test patches in a staging environment.
- Use separate Patch Baselines for production and development.
- Schedule patching during low-traffic hours.
- Use least-privilege IAM policies.
- Monitor compliance regularly.
- Keep SSM Agent updated.
- Review patch reports after every maintenance window.

---

# 25. Production Architecture

In enterprise environments:

- Multiple AWS accounts
- Hundreds of EC2 instances
- Patch Groups
- Maintenance Windows
- Centralized compliance dashboards
- AWS Organizations
- Security Hub integration

Patch Manager becomes part of the organization's security and compliance strategy.

---

# 26. Key Takeaways

After studying this theory, you should understand:

- What Patch Manager is
- Why patching is important
- How Patch Manager works
- The role of SSM Agent
- Managed Nodes
- Patch Baselines
- Patch Policies
- Maintenance Windows
- Run Command
- Patch Compliance
- Fleet Manager
- The importance of IAM permissions
- The role of Amazon S3 in Patch Manager
