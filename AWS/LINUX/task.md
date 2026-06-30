Below is a complete step-by-step guide to complete your AWS & Linux Administration task.

---

# Architecture

```
VPC
│
├── Public Subnet (Same Subnet)
│      │
│      ├── Ubuntu 24.04 EC2 (Server-1)
│      └── Ubuntu 24.04 EC2 (Server-2)
│
└── Additional EBS Volume attached to each server
```

Both servers should have:

* Ubuntu 24.04
* Same subnet
* Same Security Group
* Password SSH enabled
* User: zarthi
* Passwordless sudo
* EBS mounted permanently at `/data`

---

# Part 1 - Launch Two EC2 Instances

Launch **2 Ubuntu 24.04 EC2 instances**

Example:

```
Server-1
Name : ubuntu-server-1

Server-2
Name : ubuntu-server-2
```

Both should use

* Same VPC
* Same Subnet
* Same Security Group
* Same Key Pair

---

# Part 2 - Create and Attach EBS Volume

For each server

AWS Console

```
EC2
→ Volumes
→ Create Volume

Size : 10 GB
Type : gp3
Availability Zone :
Must be same as EC2 instance
```

After creation

```
Actions
→ Attach Volume
```

Choose the corresponding EC2 instance.

---

# Part 3 - Verify the New Disk

SSH into the server.

Run

```bash
lsblk
```

Example

```
NAME      SIZE
nvme0n1    20G
├─nvme0n1p1
└─nvme0n1p128

nvme1n1    10G
```

The new disk is

```
/dev/nvme1n1
```

---

# Part 4 - Create Filesystem

```bash
sudo mkfs.ext4 /dev/nvme1n1
```

Output

```
Creating filesystem...
```

---

# Part 5 - Create Mount Directory

```bash
sudo mkdir /data
```

---

# Part 6 - Mount the Volume

```bash
sudo mount /dev/nvme1n1 /data
```

Verify

```bash
df -h
```

Example

```
Filesystem      Size Used Avail Mounted on

/dev/nvme1n1     10G  24M   9.5G   /data
```

---

# Part 7 - Make Mount Permanent

Find UUID

```bash
sudo blkid
```

Example

```
/dev/nvme1n1: UUID="abcd-1234-xyz"
```

Copy the UUID.

---

Edit fstab

```bash
sudo nano /etc/fstab
```

Add

```
UUID=abcd-1234-xyz   /data   ext4   defaults,nofail   0   2
```

Save

```
CTRL + O

Enter

CTRL + X
```

---

Verify

```bash
cat /etc/fstab
```

Expected

```
UUID=abcd-1234-xyz /data ext4 defaults,nofail 0 2
```

---

Test fstab

Unmount

```bash
sudo umount /data
```

Mount everything

```bash
sudo mount -a
```

Check

```bash
df -h
```

Should again show

```
/data
```

---

# Part 8 - Create User

```bash
sudo adduser zarthi
```

You'll be prompted

```
New password:
Retype password:
```

Example

```
Password@123
```

Press Enter for remaining questions.

---

Verify

```bash
id zarthi
```

---

# Part 9 - Enable Password Authentication

Open SSH config

```bash
sudo nano /etc/ssh/sshd_config
```

Find or add

```
PasswordAuthentication yes

PermitRootLogin no

PubkeyAuthentication yes
```

Save.

Restart SSH

```bash
sudo systemctl restart ssh
```

Verify

```bash
sudo systemctl status ssh
```

---

# Part 10 - Give Sudo Access

```bash
sudo usermod -aG sudo zarthi
```

Verify

```bash
groups zarthi
```

Expected

```
zarthi sudo
```

---

# Part 11 - Passwordless Sudo

Open sudoers safely

```bash
sudo visudo
```

Add at the bottom

```
zarthi ALL=(ALL) NOPASSWD:ALL
```

Save and exit.

Now

```
sudo apt update
```

will **NOT ask password**.

---

# Part 12 - Test Login

From your local machine

```bash
ssh zarthi@<Public-IP>
```

Enter password

```
Password@123
```

You should login successfully.

---

# Part 13 - Test Sudo

Run

```bash
sudo apt update
```

Should execute

without asking

```
[sudo] password for zarthi:
```

---

# Part 14 - Verify Mount

Run

```bash
df -h
```

Expected

```
Filesystem      Mounted on

/dev/nvme1n1      /data
```

---

Check

```bash
lsblk
```

Example

```
NAME        SIZE MOUNTPOINT

nvme0n1      20G /

nvme1n1      10G /data
```

---

Check fstab

```bash
cat /etc/fstab
```

Expected

```
UUID=xxxxx /data ext4 defaults,nofail 0 2
```

---

# Part 15 - Reboot Test

```bash
sudo reboot
```

Reconnect.

Run

```bash
df -h
```

Expected

```
/data
```

Run

```bash
lsblk
```

Expected

```
nvme1n1
└── mounted on /data
```

This confirms the mount persisted after reboot.

---

# Validation Commands

## Check disks

```bash
lsblk
```

---

## Check filesystem usage

```bash
df -h
```

---

## Check permanent mount

```bash
cat /etc/fstab
```

---

## Check sudo

```bash
sudo whoami
```

Output

```
root
```

---

## Check current user

```bash
whoami
```

Output

```
zarthi
```

---

## Check groups

```bash
groups
```

Output

```
zarthi sudo
```

---

## Test passwordless sudo

```bash
sudo apt update
```

No password prompt should appear.

---

# Deliverables Checklist

* ✅ Two Ubuntu 24.04 EC2 instances created
* ✅ Both instances are in the same subnet
* ✅ EBS volume attached to each instance
* ✅ EBS formatted with `ext4`
* ✅ Mounted permanently on `/data`
* ✅ `/etc/fstab` configured using UUID
* ✅ Mount persists after reboot
* ✅ Local user `zarthi` created
* ✅ Password-based SSH authentication enabled
* ✅ `zarthi` added to the `sudo` group
* ✅ Passwordless sudo configured (`NOPASSWD`)
* ✅ Validation completed using:

  * `lsblk`
  * `df -h`
  * `cat /etc/fstab`
  * `sudo whoami`
  * `groups`
