## How to Create a Partition

Command

sudo fdisk /dev/nvme1n1

Inside

n

New partition.

w

Write changes.

Then

sudo mkfs.ext4 /dev/nvme1n1p1

Notice

Not

nvme1n1

But

nvme1n1p1

because filesystem is created on the partition.

Partition vs Filesystem

Many people confuse these.

Partition

Divides the disk.

Example

Disk

↓

Partition 1

Partition 2
Filesystem

Organizes files.

Example

Partition

↓

ext4

Without filesystem

Linux cannot store files.

Think

Apartment

↓

Rooms

↓

Furniture

Apartment

↓

Partition

↓

Filesystem

Partition Table

Linux stores partition information.

Two common standards

MBR

Old

Supports

Maximum 2 TB disk
Maximum 4 primary partitions
GPT

Modern

Supports

Very large disks
Up to 128 partitions
Required for UEFI systems

Ubuntu 24.04 on AWS uses GPT.

How to View Partitions
lsblk

or

sudo fdisk -l

Example

Disk /dev/nvme0n1

Partition 1

Partition 15

Partition 16
Common Interview Questions
Q1. What is a partition?

A logical division of a physical disk that allows storage to be organized into separate sections.

Q2. Why partition a disk?
Separate operating system and user data
Better management
Improved security
Support multiple operating systems
Isolate workloads (e.g., databases, logs)
Q3. Difference between Disk and Partition?
Disk	Partition
Physical storage device	Logical section of a disk
One disk can contain multiple partitions	Each partition behaves like a separate volume
Q4. Difference between Partition and Filesystem?
Partition	Filesystem
Divides the disk	Organizes data on a partition
Created with tools like fdisk or parted	Created with mkfs.ext4, mkfs.xfs, etc.
Q5. Why did your additional EBS volume not have partitions?

Because for a simple data disk, Linux allows you to create a filesystem directly on the whole block device. This is common in cloud environments where a single-purpose data volume doesn't need multiple partitions.

Memory Trick

Think of the storage hierarchy like this:

Physical Disk
      │
      ▼
Partition
      │
      ▼
Filesystem (ext4, xfs)
      │
      ▼
Mount Point (/, /boot, /data)
      │
      ▼
Files and Directories

Easy way to remember:

Disk → The physical storage device (or virtual disk like an EBS volume)
Partition → A logical slice of the disk
Filesystem → The method used to store and organize data
Mount Point → The directory where Linux makes the filesystem accessible

This flow is fundamental to Linux storage management and is one of the most common topics in Linux and AWS administration interviews.
