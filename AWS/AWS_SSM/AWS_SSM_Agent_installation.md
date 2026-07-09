# SSM Agent Installation and Session Manager Access (Private EC2)

## Objective

Install and configure the Amazon SSM Agent on a private Debian EC2
instance and access it using AWS Systems Manager Session Manager.

## Architecture

``` text
Laptop
   |
SSH
   |
Public EC2 (Bastion Host)
   |
SSH
   |
Private EC2 (Debian)
   |
NAT Gateway
   |
Internet
```

## Prerequisites

-   VPC with public and private subnets
-   Internet Gateway attached to the VPC
-   NAT Gateway in the public subnet
-   Public EC2 (Bastion Host)
-   Private EC2 (Debian 13)
-   IAM Role attached to the private EC2 with:
    -   `AmazonSSMManagedInstanceCore`
-   Same VPC and appropriate route tables

## Security Groups

### Bastion Host

-   Inbound:
    -   SSH (22) from **My Public IP**

### Private EC2

-   Inbound:
    -   SSH (22) from the **Bastion Host Security Group**

## Connect to the Private EC2

From your laptop:

``` bash
ssh -i vpn-lab-key.pem ubuntu@<BASTION_PUBLIC_IP>
```

From the bastion host:

``` bash
chmod 400 vpn-lab-key.pem
ssh -i vpn-lab-key.pem admin@<PRIVATE_IP>
```

## Verify Internet Connectivity

``` bash
ping -c 4 google.com
sudo apt update
```

Both commands should succeed if the NAT Gateway is configured correctly.

## Download the SSM Agent

``` bash
wget https://s3.amazonaws.com/ec2-downloads-windows/SSMAgent/latest/debian_amd64/amazon-ssm-agent.deb
```

Verify:

``` bash
ls
```

Expected:

``` text
amazon-ssm-agent.deb
```

## Install the SSM Agent

``` bash
sudo dpkg -i amazon-ssm-agent.deb
```

If there are dependency issues:

``` bash
If there are dependency issues:
sudo dpkg -i amazon-ssm-agent.deb
```

## Enable the Service

``` bash
sudo systemctl enable amazon-ssm-agent
```

## Start the Service

``` bash
sudo systemctl start amazon-ssm-agent
```

## Verify the Service

``` bash
sudo systemctl status amazon-ssm-agent
```

Expected:

``` text
Active: active (running)
```

Exit with `q`.

Verify auto-start:

``` bash
sudo systemctl is-enabled amazon-ssm-agent
```

Expected:

``` text
enabled
```

## Verify IAM Role

Ensure the EC2 instance has an IAM role with the managed policy:

-   `AmazonSSMManagedInstanceCore`

## Verify in Systems Manager

Go to:

-   **AWS Systems Manager → Managed Nodes** (or Fleet Manager)

Wait 1--5 minutes.

Expected:

``` text
Ping Status: Online
```

## Connect Using Session Manager

Go to:

-   **EC2 → Instances → Select Private Instance → Connect → Session
    Manager → Connect**

A shell should open without SSH.

## Optional Validation

Remove the inbound SSH rule from the private EC2 security group and
verify that Session Manager still works. This confirms SSM access is
independent of SSH.

## Troubleshooting

### `Permission denied (publickey)`

-   Verify the correct key pair.

-   Use the correct username (`admin` for Debian 13).

-   Check key permissions:

    ``` bash
    chmod 400 vpn-lab-key.pem
    ```

### `apt update` fails

-   Verify the private route table has:
    -   `0.0.0.0/0 → NAT Gateway`
-   Confirm the NAT Gateway is in the public subnet.

### SSM Agent not appearing

-   Verify the IAM role.

-   Check the service:

    ``` bash
    sudo systemctl status amazon-ssm-agent
    ```

-   Review logs:

    ``` bash
    sudo journalctl -u amazon-ssm-agent --no-pager | tail -30
    ```

## Outcome

-   Successfully connected to the private EC2 through a Bastion Host.
-   Installed the Amazon SSM Agent.
-   Enabled and started the service.
-   Verified the agent was running.
-   Confirmed the instance was managed by AWS Systems Manager.
-   Connected to the private instance using Session Manager.
