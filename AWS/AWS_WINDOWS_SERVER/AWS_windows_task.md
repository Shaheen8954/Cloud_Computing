# Cross-Region Windows EC2 File Replication Using AMI (Mumbai → Hyderabad)

## Objective

Provision a Windows EC2 instance in the **Mumbai (ap-south-1)** region, create a folder named **TEST** on the **C:** drive with a text file inside it, then create an **Amazon Machine Image (AMI)** from that instance and copy it to the **Hyderabad (ap-south-2)** region. Finally, launch a new EC2 instance from the copied AMI and verify that the **TEST** folder and text file are present.

> **Note**
>
> This is the **simplest approach** to copy the complete Windows server (including files) from one AWS region to another. This is **not real-time synchronization**. The destination receives the data that existed when the AMI was created.

---

# Architecture

```
                Mumbai Region (ap-south-1)
        +--------------------------------------+
        | Windows Server 2022 EC2              |
        |                                      |
        | C:\TEST                              |
        |    └── sample.txt                    |
        +------------------+-------------------+
                           |
                           | Create AMI
                           |
                           V
                    Amazon Machine Image
                           |
                           | Copy AMI
                           |
                           V
              Hyderabad Region (ap-south-2)
        +--------------------------------------+
        | Launch EC2 From Copied AMI           |
        |                                      |
        | C:\TEST                              |
        |    └── sample.txt                    |
        +--------------------------------------+
```

---

# Prerequisites

- AWS Account
- EC2 permissions
- Key Pair (.pem file)
- Remote Desktop Connection (RDP) installed on your laptop
- Internet connection

---

# AWS Services Used

- Amazon EC2
- Amazon Machine Image (AMI)
- EBS Snapshot (created automatically with AMI)
- Remote Desktop Protocol (RDP)

---

# Workflow

```
Launch Windows EC2
        ↓
Connect using RDP
        ↓
Create C:\TEST
        ↓
Create sample.txt
        ↓
Create AMI
        ↓
Copy AMI to Hyderabad
        ↓
Launch EC2 from copied AMI
        ↓
Connect using RDP
        ↓
Verify TEST folder and file
```

---

# Step 1 - Launch Windows EC2 Instance (Mumbai Region)

Login to the AWS Management Console.

Navigate to:

```
EC2
```

Click

```
Launch Instance
```

Configure the instance as follows:

| Setting | Value |
|---------|-------|
| Region | Mumbai (ap-south-1) |
| Name | Windows-Source |
| AMI | Microsoft Windows Server 2022 Base |
| Instance Type | t2.micro (or as required) |
| Key Pair | Select existing or create a new one |
| Network | Default VPC |
| Storage | Default |
| Security Group | Default (Allow RDP Port 3389) |

Leave every other setting as default.

Click

```
Launch Instance
```

---

# Step 2 - Wait Until Instance is Ready

Wait until the instance reaches:

```
Instance State
Running
```

and

```
Status Checks

3 / 3 Checks Passed
```

> This usually takes **5–10 minutes**.

---

# Step 3 - Get Windows Administrator Password

Select the instance.

Click

```
Connect
```

Choose

```
RDP Client
```

Click

```
Get Password
```

Upload your

```
.pem
```

key pair.

Click

```
Decrypt Password
```

You will receive:

- Username
- Password

Copy both into Notepad for later use.

---

# Step 4 - Connect Using Remote Desktop

On your laptop:

Search for

```
Remote Desktop Connection
```

Open it.

Enter the **Public IPv4 Address** of your EC2 instance.

Click

```
Connect
```

Enter:

- Username
- Password

Click

```
OK
```

You are now connected to the Windows EC2 server.

---

# Step 5 - Create Folder and Text File

Open

```
File Explorer
```

Go to

```
This PC
```

Open

```
Local Disk (C:)
```

Create a folder named

```
TEST
```

Inside the folder create a text file named

```
sample.txt
```

Open the file.

Write any content.

Example:

```
Hello from Mumbai Windows EC2.
```

Save the file.

Close Notepad.

---

# Step 6 - Create an AMI

Return to the AWS Console.

Select the EC2 instance.

Click

```
Actions
```

Choose

```
Image and Templates
```

Click

```
Create Image
```

Provide:

| Setting | Value |
|---------|-------|
| Image Name | Windows-Source-AMI |

Leave all remaining settings as default.

Click

```
Create Image
```

---

# Step 7 - Wait for AMI Creation

Navigate to

```
EC2
→ AMIs
```

Wait until the AMI status changes to:

```
Available
```

This usually takes

```
5–10 minutes
```

---

# Step 8 - Copy AMI to Hyderabad Region

Select the AMI.

Click

```
Actions
```

Choose

```
Copy AMI
```

Configure:

| Setting | Value |
|---------|-------|
| Destination Region | Hyderabad (ap-south-2) |
| Name | Windows-Source-AMI-HYD |

Leave every other setting as default.

Click

```
Copy AMI
```

---

# Step 9 - Verify Copied AMI

Switch the AWS region to:

```
Hyderabad (ap-south-2)
```

Go to

```
EC2
→ AMIs
```

Wait until the copied AMI becomes

```
Available
```

---

# Step 10 - Launch EC2 from Copied AMI

Select the copied AMI.

Click

```
Launch Instance from AMI
```

Configure:

| Setting | Value |
|---------|-------|
| Name | Windows-Destination |
| Key Pair | Select the same key pair (or another valid key pair) |
| Remaining Settings | Default |

Click

```
Launch Instance
```

---

# Step 11 - Wait for Instance Readiness

Wait until:

```
Running
```

and

```
3 / 3 Status Checks Passed
```

---

# Step 12 - Connect to Destination Instance

Repeat the same process used for the source instance.

- Connect
- RDP Client
- Get Password
- Upload PEM file
- Decrypt Password

Open

```
Remote Desktop Connection
```

Connect using the new instance's Public IP.

Login with the displayed Administrator credentials.

---

# Step 13 - Verify the Folder

Open

```
File Explorer
```

Navigate to

```
C:
```

You should find:

```
TEST
```

Open it.

Verify that

```
sample.txt
```

is present.

Open the file.

The contents should match what you created in the Mumbai instance.

Example:

```
Hello from Mumbai Windows EC2.
```

If the folder and file are present, the task is successfully completed.

---

# Expected Result

Source Instance:

```
C:\
 └── TEST
      └── sample.txt
```

Destination Instance:

```
C:\
 └── TEST
      └── sample.txt
```

Both should contain identical content.

---

# Verification Checklist

- [ ] Windows EC2 launched in Mumbai
- [ ] Connected using RDP
- [ ] Created `C:\TEST`
- [ ] Created `sample.txt`
- [ ] Created AMI
- [ ] AMI became Available
- [ ] Copied AMI to Hyderabad
- [ ] Copied AMI became Available
- [ ] Launched EC2 from copied AMI
- [ ] Connected using RDP
- [ ] Verified `C:\TEST`
- [ ] Verified `sample.txt`

---

# Troubleshooting

## Unable to Connect Using RDP

Check:

- Instance is running.
- Security Group allows TCP Port **3389**.
- You are using the correct Public IP.
- The Administrator password was decrypted successfully.

---

## Password Decryption Failed

Ensure:

- The correct `.pem` key pair is uploaded.
- The instance was launched using the same key pair.

---

## AMI Not Available

Wait a few more minutes.

Large Windows images take longer to create.

Refresh the page.

---

## Copied AMI Not Showing

Verify:

- You switched to the Hyderabad region.
- AMI copy has completed.
- Status is **Available**.

---

## Folder Missing on Destination

Possible reasons:

- AMI was created before creating the folder.
- The wrong AMI was used.
- The wrong destination instance was launched.

Create a new AMI after confirming the folder exists and repeat the process.

---

# Key Learning Points

- An AMI captures the complete operating system, installed software, files, and configuration of an EC2 instance.
- Copying an AMI across regions allows you to deploy identical servers in another AWS region.
- Files stored on the root EBS volume are included in the AMI.
- This method creates a snapshot of the server at a specific point in time.
- Changes made to the source instance after the AMI is created are **not automatically reflected** in the destination instance.

---

# Conclusion

Using an AMI is one of the easiest ways to duplicate a Windows EC2 instance across AWS regions. In this exercise, a Windows Server was launched in Mumbai, a folder and text file were created, an AMI was generated and copied to Hyderabad, and a new instance launched from the copied AMI successfully contained the same folder and file, confirming that the server image was replicated successfully.
