## • Setup Memory Metric monitoring for EC2 Server


Setting up **Memory Metric Monitoring for an EC2 Server** means enabling AWS to monitor **RAM (Memory) usage** on your EC2 instance.

> **Important:** By default, AWS **does not monitor memory usage**. Amazon CloudWatch only collects CPU, Disk, Network, and Status Check metrics. To monitor memory, you must install the **CloudWatch Agent** on the EC2 instance.

---

# 1. Concept

Memory monitoring allows you to see how much **RAM** your EC2 instance is using.

Example:

* Total RAM = 8 GB
* Used RAM = 6 GB
* Free RAM = 2 GB

CloudWatch can display:

* Memory Used %
* Memory Available
* Memory Cached
* Memory Buffers

---

# 2. Why do we need Memory Monitoring?

Imagine your application:

```
CPU Usage = 20%
Memory Usage = 98%
```

Without memory metrics, you'll think:

> "CPU is fine, server is healthy."

But actually,

```
Server crashes because RAM is full.
```

Memory monitoring helps detect:

* Memory leaks
* Java applications consuming RAM
* Database memory pressure
* Out of Memory (OOM) situations
* Need for larger instance types

---

# 3. AWS Default Metrics vs Custom Metrics

| Metric          | Available by Default? |
| --------------- | --------------------- |
| CPU Utilization | ✅ Yes                 |
| Network In      | ✅ Yes                 |
| Network Out     | ✅ Yes                 |
| Disk Read       | ✅ Yes                 |
| Disk Write      | ✅ Yes                 |
| Status Check    | ✅ Yes                 |
| Memory Usage    | ❌ No                  |

Memory metrics require:

```
EC2
      ↓
CloudWatch Agent
      ↓
CloudWatch Custom Metrics
```

---

# 4. Architecture

```
Application

     ↓

Linux Memory

     ↓

CloudWatch Agent

     ↓

CloudWatch Service

     ↓

Dashboard

     ↓

Alarm

     ↓

SNS Email
```

---

# 5. Workflow

```
Launch EC2

↓

Install CloudWatch Agent

↓

Configure Agent

↓

Collect Memory Metrics

↓

Send Metrics to CloudWatch

↓

Create Alarm

↓

Receive Notification
```

---

# 6. Prerequisites

You need:

* EC2 instance
* Internet access or VPC Endpoint
* IAM Role attached to EC2
* CloudWatchAgentServerPolicy
* AWS CLI
* SSM Agent (optional but recommended)

---

# 7. Step-by-Step Setup (Ubuntu)

## Step 1: Attach IAM Role

Attach an IAM Role to your EC2 instance with the policy:

```
CloudWatchAgentServerPolicy
```

This allows the agent to send metrics to CloudWatch.

---

## Step 2: Install CloudWatch Agent

Update packages:

```bash
sudo apt update
```

Install agent:

```bash
sudo apt install amazon-cloudwatch-agent -y
```

Verify:

```bash
amazon-cloudwatch-agent-ctl -h
```

---

## Step 3: Create Configuration File

Create the directory if it doesn't exist:

```bash
sudo mkdir -p /opt/aws/amazon-cloudwatch-agent/etc
```

Create the configuration file:

```bash
sudo nano /opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json
```

Paste:

```json
{
  "metrics": {
    "namespace": "CWAgent",
    "append_dimensions": {
      "InstanceId": "${aws:InstanceId}"
    },
    "metrics_collected": {
      "mem": {
        "measurement": [
          "mem_used_percent"
        ],
        "metrics_collection_interval": 60
      }
    }
  }
}
```

Save the file.

---

## Step 4: Start the Agent

```bash
sudo amazon-cloudwatch-agent-ctl \
-a fetch-config \
-m ec2 \
-c file:/opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json \
-s
```

---

## Step 5: Verify Agent Status

```bash
sudo systemctl status amazon-cloudwatch-agent
```

Expected:

```
Active: active (running)
```

---

## Step 6: Verify Metrics

Go to:

```
AWS Console

↓

CloudWatch

↓

Metrics

↓

CWAgent

↓

InstanceId

↓

mem_used_percent
```

You should see a graph like:

```
Memory %

80 ┤

70 ┤

60 ┤

50 ┤

40 ┤

30 ┤

20 ┤

10 ┤

0  └──────────────
```

---

# 8. Create Alarm

Go to:

```
CloudWatch

↓

Alarms

↓

Create Alarm

↓

Select Metric

↓

CWAgent

↓

mem_used_percent
```

Choose:

```
Threshold

Greater than

80%
```

Action:

```
Send notification to SNS
```

Example:

```
Memory >80%

↓

SNS

↓

Email

↓

Administrator
```

---

# 9. Important Commands

### Install Agent

```bash
sudo apt install amazon-cloudwatch-agent -y
```

### Start Agent

```bash
sudo amazon-cloudwatch-agent-ctl -a fetch-config -m ec2 -c file:/opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json -s
```

### Stop Agent

```bash
sudo systemctl stop amazon-cloudwatch-agent
```

### Restart Agent

```bash
sudo systemctl restart amazon-cloudwatch-agent
```

### Check Status

```bash
sudo systemctl status amazon-cloudwatch-agent
```

### View Logs

```bash
sudo tail -f /opt/aws/amazon-cloudwatch-agent/logs/amazon-cloudwatch-agent.log
```

---

# 10. How the Agent Calculates Memory Usage

Example:

```
Total RAM = 8 GB

Used = 5 GB

Free = 3 GB
```

Memory utilization:

```
(5 / 8) × 100 = 62.5%
```

CloudWatch displays:

```
mem_used_percent = 62.5
```

---

# 11. Common Memory Metrics

| Metric            | Meaning                       |
| ----------------- | ----------------------------- |
| mem_used_percent  | Percentage of RAM in use      |
| mem_used          | RAM currently used            |
| mem_available     | Available RAM                 |
| mem_free          | Completely unused RAM         |
| mem_cached        | Memory used for file caching  |
| mem_buffered      | Buffer memory                 |
| swap_used         | Swap space currently in use   |
| swap_used_percent | Percentage of swap space used |

---

# 12. Real-World Use Cases

* **Web Servers (Apache/Nginx):** Detect increasing memory usage before the server slows down.
* **Application Servers (Java, .NET):** Identify memory leaks and excessive heap usage.
* **Database Servers (MySQL, PostgreSQL):** Monitor RAM pressure to avoid performance degradation.
* **Container Hosts (Docker/Kubernetes):** Track memory consumption of workloads running on the EC2 instance.

---

# 13. Troubleshooting

| Issue                              | Possible Cause                   | Solution                                                 |
| ---------------------------------- | -------------------------------- | -------------------------------------------------------- |
| No memory metrics दिखाई दे रहे हैं | CloudWatch Agent not running     | Check `systemctl status amazon-cloudwatch-agent`         |
| Access denied errors               | Missing IAM permissions          | Attach `CloudWatchAgentServerPolicy` to the EC2 IAM role |
| Metrics not updating               | Incorrect agent configuration    | Validate and restart the agent                           |
| Agent installation fails           | No internet or repository access | Ensure outbound connectivity or configure a VPC endpoint |

---

# 14. Interview Questions

**Q1. Does AWS CloudWatch monitor memory by default?**
**Answer:** No. Memory metrics are not collected by default. You must install the **CloudWatch Agent** to publish them as custom metrics.

**Q2. Why is an IAM role required?**
**Answer:** The CloudWatch Agent needs permission to call CloudWatch APIs and publish custom metrics.

**Q3. Which metric is most commonly used for memory monitoring?**
**Answer:** `mem_used_percent`.

**Q4. How often are memory metrics collected?**
**Answer:** It depends on the agent configuration. A common interval is **60 seconds**, though shorter intervals are possible.

**Q5. Where are memory metrics stored?**
**Answer:** In **Amazon CloudWatch** under the **CWAgent** namespace as custom metrics.

---

# 15. Presentation Summary

**Objective:** Monitor EC2 memory (RAM) utilization using Amazon CloudWatch.

**Key Points:**

* CloudWatch does **not** collect memory metrics by default.
* Install and configure the **CloudWatch Agent** on the EC2 instance.
* Attach an IAM role with the **CloudWatchAgentServerPolicy**.
* Configure the agent to collect metrics such as `mem_used_percent`.
* View the metrics in CloudWatch and create alarms (for example, when memory usage exceeds 80%) to receive notifications through SNS.

This setup helps proactively detect memory pressure, troubleshoot application issues, and make informed scaling decisions before users are affected.
