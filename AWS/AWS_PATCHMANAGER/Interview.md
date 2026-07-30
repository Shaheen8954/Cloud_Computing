# INTERVIEW.md

# AWS Systems Manager Patch Manager - Interview Questions & Answers

---

# Table of Contents

1. Basic Questions
2. Intermediate Questions
3. Advanced Questions
4. Scenario-Based Questions
5. Troubleshooting Questions
6. Best Practices
7. Rapid Fire Questions

---

# Basic Questions

## 1. What is AWS Systems Manager?

**Answer:**

AWS Systems Manager (SSM) is a management service that helps administrators manage AWS resources and on-premises servers from a single console.

It provides features such as:

- Patch Manager
- Run Command
- Session Manager
- Automation
- Fleet Manager
- Parameter Store
- State Manager

---

## 2. What is Patch Manager?

**Answer:**

Patch Manager is a feature of AWS Systems Manager that automates the process of scanning, approving, scheduling, and installing operating system patches on managed instances.

Instead of manually logging into each server, Patch Manager performs patching automatically based on configured policies.

---

## 3. Why do we need Patch Manager?

**Answer:**

Patch Manager helps organizations:

- Improve security
- Fix software bugs
- Maintain compliance
- Automate operating system updates
- Reduce manual effort
- Generate compliance reports

---

## 4. Which operating systems are supported?

**Answer:**

Patch Manager supports multiple operating systems, including:

- Ubuntu
- Debian
- Amazon Linux
- RHEL
- Rocky Linux
- SUSE
- Windows Server

---

## 5. What is a Managed Node?

**Answer:**

A Managed Node is a server that AWS Systems Manager can communicate with.

Examples include:

- Amazon EC2 instances
- On-premises servers
- Virtual machines

A server becomes a Managed Node when:

- SSM Agent is installed.
- The correct IAM permissions are attached.
- It can communicate with Systems Manager endpoints.

---

## 6. What is the SSM Agent?

**Answer:**

SSM Agent is software installed on the operating system that enables communication between the EC2 instance and AWS Systems Manager.

Without the SSM Agent:

- Patch Manager cannot install patches.
- Run Command cannot execute.
- Fleet Manager cannot manage the instance.

---

## 7. How do you verify whether an instance is managed?

**Answer:**

Navigate to:

```
Systems Manager
→ Fleet Manager
→ Managed Nodes
```

The instance should appear with:

- Online status
- Managed = Yes

---

## 8. Which IAM policy is mandatory for Systems Manager?

**Answer:**

The minimum required managed policy is:

```
AmazonSSMManagedInstanceCore
```

---

## 9. What is a Patch Baseline?

**Answer:**

A Patch Baseline is a collection of rules that determines:

- Which patches are approved
- Which patches are rejected
- When patches become available for installation

Think of it as the "rulebook" that Patch Manager follows during patch operations.

---

## 10. What is a Patch Policy?

**Answer:**

A Patch Policy combines:

- Patch Baseline
- Target instances
- Scan schedule
- Install schedule
- Reboot behavior

It defines how Patch Manager should manage updates for selected instances.

---

# Intermediate Questions

## 11. What is a Maintenance Window?

**Answer:**

A Maintenance Window specifies **when** maintenance tasks such as patching are allowed to run.

This helps administrators perform updates during periods of low business activity.

---

## 12. Why shouldn't production servers be patched immediately?

**Answer:**

Immediate patching can introduce:

- Unexpected application failures
- Compatibility issues
- Downtime

Organizations typically test patches in staging before applying them to production.

---

## 13. What is AWS-RunPatchBaseline?

**Answer:**

AWS-RunPatchBaseline is an AWS-managed Systems Manager Document used to:

- Scan for missing patches
- Install approved patches
- Report compliance status

It is the document executed by Patch Manager during patch operations.

---

## 14. Difference between Scan and Install?

| Scan | Install |
|------|----------|
| Detects missing patches | Installs approved patches |
| Does not modify the server | Updates the operating system |
| Generates compliance data | Changes system packages |

---

## 15. What is Fleet Manager?

**Answer:**

Fleet Manager provides centralized visibility into managed instances.

It allows administrators to:

- Verify Managed Nodes
- Check connectivity
- View operating system details
- Monitor agent status

---

## 16. Why is IAM important for Patch Manager?

**Answer:**

Patch Manager uses IAM permissions to:

- Communicate with Systems Manager
- Read Patch Policy configuration
- Access Amazon S3
- Execute patch documents

Insufficient permissions cause patch execution failures.

---

## 17. What is Quick Setup?

**Answer:**

Quick Setup simplifies Systems Manager configuration by automatically creating:

- Patch Policies
- Associations
- IAM configurations
- Supporting resources

This reduces manual setup and follows AWS best practices.

---

## 18. Does Patch Manager require SSH access?

**Answer:**

No.

Patch Manager communicates with the instance through the SSM Agent.

SSH access is not required.

---

## 19. Can Patch Manager patch multiple servers simultaneously?

**Answer:**

Yes.

Patch Manager can patch hundreds or thousands of managed instances based on Patch Policies and Maintenance Windows.

---

## 20. What happens if the SSM Agent stops?

**Answer:**

The instance becomes unreachable by Systems Manager.

Patch Manager, Run Command, and other Systems Manager features will fail until the agent is running again.

---

# Advanced Questions

## 21. Why did your patch command fail with a 403 error?

**Answer:**

During our lab, the command failed with:

```
403 Forbidden
HeadObject
```

The error indicated that Patch Manager was trying to access an object stored in Amazon S3.

The IAM Role initially had:

```
AmazonS3ExpressFullAccess
```

However, Quick Setup stored `baseline_overrides.json` in a **standard Amazon S3 bucket**, so the instance did not have permission to perform the required `HeadObject` and `GetObject` operations.

Replacing the policy with one that granted access to the standard S3 bucket resolved the permission issue.

---

## 22. What is `baseline_overrides.json`?

**Answer:**

`baseline_overrides.json` is a configuration file created by AWS Quick Setup.

It contains Patch Baseline override information that Patch Manager reads during execution.

---

## 23. Why did Patch Manager access Amazon S3?

**Answer:**

Patch Manager retrieved its Patch Policy configuration from an Amazon S3 bucket created by Quick Setup.

The S3 API operation shown in the error (`HeadObject`) confirmed that Patch Manager was attempting to read this configuration file.

---

## 24. What does HeadObject mean?

**Answer:**

`HeadObject` is an Amazon S3 API operation that retrieves metadata about an object without downloading the object itself.

A `403 Forbidden` response usually indicates that the caller does not have permission to access the object.

---

## 25. What is the Principle of Least Privilege?

**Answer:**

The Principle of Least Privilege means granting only the permissions required to perform a task—nothing more.

Instead of attaching `AmazonS3FullAccess`, production environments should grant access only to the specific bucket and object required by Patch Manager.

---

# Scenario-Based Questions

## 26. An EC2 instance does not appear under Managed Nodes. What would you check?

**Answer:**

I would verify:

1. SSM Agent is installed and running.
2. IAM Role includes `AmazonSSMManagedInstanceCore`.
3. Network connectivity to Systems Manager endpoints.
4. Instance is running.
5. Systems Manager service status.

---

## 27. Patch Manager reports "Failed." What is your first step?

**Answer:**

I would open:

```
Systems Manager
→ Run Command
→ Command History
→ View Output
```

The detailed output provides the actual error, which is more useful than the overall "Failed" status.

---

## 28. You need to patch 500 production servers. How would you do it?

**Answer:**

I would:

- Create a Patch Baseline.
- Group servers using tags or Patch Groups.
- Schedule a Maintenance Window during low-traffic hours.
- Test on staging first.
- Monitor compliance after execution.

---

## 29. Why would you use Maintenance Windows?

**Answer:**

Maintenance Windows allow updates to occur during planned maintenance periods, reducing the risk of disrupting users during business hours.

---

## 30. Why should you review patch compliance regularly?

**Answer:**

Regular compliance checks help identify systems that are missing critical updates, ensuring they remain secure and compliant with organizational policies.

---

# Rapid Fire Questions

| Question | Answer |
|----------|--------|
| Which service performs patching? | Patch Manager |
| Which agent communicates with AWS? | SSM Agent |
| Minimum IAM policy? | AmazonSSMManagedInstanceCore |
| Which document installs patches? | AWS-RunPatchBaseline |
| Which service verifies Managed Nodes? | Fleet Manager |
| Which service stores Patch Policy configuration? | Amazon S3 |
| Which API caused the 403 error in our lab? | HeadObject |
| Which file was accessed? | baseline_overrides.json |
| Can Patch Manager work without SSM Agent? | No |
| Can Patch Manager manage on-premises servers? | Yes |

---

# Interview Tips

When discussing this project in an interview:

- Explain **why** patch management is important before explaining **how** AWS implements it.
- Walk through the architecture from Systems Manager to the EC2 instance.
- Mention the real troubleshooting experience with the `403 HeadObject` error and how you investigated it.
- Demonstrate understanding of IAM, SSM Agent, Managed Nodes, and Patch Policies.
- Highlight best practices such as testing patches in staging, using Maintenance Windows, and following the Principle of Least Privilege.

Being able to explain the reasoning behind each step is often more valuable than simply listing AWS features.
