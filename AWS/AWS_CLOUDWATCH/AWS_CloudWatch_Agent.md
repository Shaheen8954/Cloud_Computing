# Install and Configure Amazon CloudWatch Agent on Ubuntu

## Objective

The default Amazon EC2 metrics in CloudWatch do **not** include **Memory Utilization**. To monitor memory usage, you must install and configure the **Amazon CloudWatch Agent**.

This guide demonstrates how to install the CloudWatch Agent on an Ubuntu EC2 instance and verify that it is running.

---

# Prerequisites

Before starting, ensure that:

- An Ubuntu EC2 instance is running.
- You have SSH access to the instance.
- The instance has internet connectivity.
- The instance is attached to an IAM Role with the following policy:

```
CloudWatchAgentServerPolicy
```

Without this IAM policy, the CloudWatch Agent will not be able to publish metrics to Amazon CloudWatch.

---

# Step 1: Download the CloudWatch Agent

The following RPM package is intended for Amazon Linux.

```bash
sudo wget https://s3.amazonaws.com/amazoncloudwatch-agent/amazon_linux/amd64/latest/amazon-cloudwatch-agent.rpm
```

Since Ubuntu does **not** support RPM packages directly, remove it (optional).

```bash
rm amazon-cloudwatch-agent.rpm
```

---

# Step 2: Download the Ubuntu Package

Download the Ubuntu (.deb) package.

```bash
wget https://s3.amazonaws.com/amazoncloudwatch-agent/ubuntu/amd64/latest/amazon-cloudwatch-agent.deb
```

Verify the download.

```bash
ls -lh amazon-cloudwatch-agent.deb
```

Example output:

```
-rw-rw-r-- 1 ubuntu ubuntu 66M amazon-cloudwatch-agent.deb
```

---

# Step 3: Install the CloudWatch Agent

Install the downloaded package.

```bash
sudo dpkg -i amazon-cloudwatch-agent.deb
```

If dependency issues occur, update the package repository and install missing dependencies.

```bash
sudo apt update
```

```bash
sudo apt install -f -y
```

Then install the package again.

```bash
sudo dpkg -i amazon-cloudwatch-agent.deb
```

---

# Step 4: Verify Installation

Check whether the CloudWatch Agent is installed.

```bash
ls /opt/aws/
```

Expected output:

```
amazon-cloudwatch-agent
```

Verify the installation directory.

```bash
ls /opt/aws/amazon-cloudwatch-agent/
```

Expected output:

```
LICENSE
NOTICE
RELEASE_NOTES
bin
doc
etc
logs
var
```

---

# Step 5: Verify Agent Status

Check the CloudWatch Agent status.

```bash
/opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a status
```

Or use systemd.

```bash
systemctl status amazon-cloudwatch-agent
```

Initially, the status may appear as:

```
Status: stopped
```

This is expected until the agent is started.

---

# Step 6: Create the Configuration File

Create the CloudWatch Agent configuration file.

```bash
sudo nano /opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json
```

> **Note:** The JSON configuration content is intentionally omitted in this guide.
>
```{
    "metrics": {
        "metrics_collected": {
            "mem": {
                "measurement": [
                    "mem_used_percent"
                ],
                "metrics_collection_interval": 30
            }
        }
    }
}
```

Save the file.

- Press **Ctrl + O**
- Press **Enter**
- Press **Ctrl + X**

---

# Step 7: Verify the Configuration File

Confirm that the configuration file has been created.

```bash
cat /opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json
```

The command should display the configuration stored in the file.

---

# Step 8: Start the CloudWatch Agent

Load the configuration and start the CloudWatch Agent.

```bash
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl \
-a fetch-config \
-m ec2 \
-c file:/opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json \
-s
```

---

# Step 9: Verify the Agent is Running

Check the service status.

```bash
systemctl status amazon-cloudwatch-agent
```

Expected output:

```
Active: active (running)
```

You can also verify using:

```bash
/opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a status
```

Expected output:

```
status: running
```

---

# Verification in AWS Console

1. Open the AWS Management Console.
2. Navigate to **CloudWatch**.
3. Select **Metrics**.
4. Open the **CWAgent** namespace.
5. Locate your EC2 instance.
6. Verify that the **Memory Utilization** metric is available.

---

# Common Issues and Solutions

## 1. Unable to locate package amazon-cloudwatch-agent

**Error**

```text
E: Unable to locate package amazon-cloudwatch-agent
```

**Reason**

Ubuntu repositories do not include the CloudWatch Agent package.

**Solution**

Download the official `.deb` package from Amazon S3 and install it using `dpkg`.

---

## 2. No such file or directory

**Error**

```text
No such file or directory
```

**Reason**

The CloudWatch Agent is not installed, or the configuration file has not yet been created.

**Solution**

Verify that the CloudWatch Agent is installed and create the configuration file before starting the agent.

---

## 3. Command not found

**Error**

```text
amazon-cloudwatch-agent: command not found
```

**Reason**

The CloudWatch Agent binaries are not added to the system PATH.

**Solution**

Use the full executable path.

```bash
/opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl
```

---

## 4. Agent Status is Stopped

**Reason**

The agent has been installed but has not been started.

**Solution**

Load the configuration and start the service using the `fetch-config` command.

---

# Summary

In this guide, you learned how to:

- Install the Amazon CloudWatch Agent on Ubuntu.
- Resolve package installation issues.
- Verify the installation.
- Create the CloudWatch Agent configuration file.
- Start the CloudWatch Agent.
- Verify that the agent is running.
- Confirm that memory metrics are available in Amazon CloudWatch.

After completing these steps, your Ubuntu EC2 instance will be able to publish **Memory Utilization** metrics to Amazon CloudWatch.





