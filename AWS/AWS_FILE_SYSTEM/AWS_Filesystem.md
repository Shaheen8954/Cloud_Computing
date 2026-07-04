# Linux Filesystems: Complete Guide (ext2, ext3, ext4, XFS)
---

# What is a Filesystem?

A **filesystem** is the method used by an operating system to **store, organize, retrieve, and manage data** on a storage device such as an HDD, SSD, USB drive, or an AWS EBS volume.

Without a filesystem, the operating system cannot understand:

- Where a file starts
- Where a file ends
- Which disk blocks are free
- Which blocks belong to which file
- File names and directories
- File ownership and permissions

Simply put:

> **A filesystem is a set of rules and data structures that organize data on a storage device.**

---

# Real-Life Analogy

Imagine a library.

Without a catalog:

```text
📚 📚 📚 📚 📚 📚 📚 📚

Find "Linux Notes.pdf"

Impossible.
```

With a catalog:

```text
Shelf A
   Books

Shelf B
   Magazines

Shelf C
   Documents
```

Everything becomes organized and easy to find.

A filesystem performs the same job for a storage device.

---

# Why Do We Need a Filesystem?

Without a filesystem:

- No file names
- No folders
- No permissions
- No ownership
- No timestamps
- No free-space tracking

The storage device would simply contain raw binary data.

---

# How a Filesystem Works

Suppose you save a file named:

```text
resume.pdf
```

Workflow:

```text
Application

↓

Kernel

↓

Filesystem

↓

Storage Device
```

Internally:

```text
Create File

↓

Allocate Inode

↓

Allocate Disk Blocks

↓

Write Data

↓

Update Directory Entry
```

---

# Components of a Filesystem

Every Linux filesystem consists of several important components.

```text
Filesystem

├── Superblock
├── Inodes
├── Data Blocks
├── Journal (if supported)
├── Directory Structure
└── Free Block Bitmap
```

---

## 1. Superblock

The **Superblock** contains information about the filesystem.

It stores:

- Filesystem size
- Block size
- Number of free blocks
- Number of inodes
- Filesystem state

Think of it as the filesystem's identity card.

---

## 2. Inode

Every file has an **inode**.

An inode stores:

- File owner
- Group
- Permissions
- File size
- Timestamps
- Disk block locations

An inode **does not store the filename**.

---

## 3. Data Blocks

These blocks store the actual contents of files.

Example:

```text
Hello World
```

---

## 4. Directory

A directory maps filenames to inode numbers.

Example:

```text
resume.pdf

↓

inode 120
```

---

## 5. Journal

Supported in journaling filesystems.

Purpose:

- Records filesystem operations before writing them to disk.
- Helps recover quickly after crashes.

---

# Types of Linux Filesystems

| Filesystem | Journaling | Max File Size | Max Filesystem Size | Common Use |
|------------|------------|---------------|----------------------|------------|
| ext2 | ❌ No | 2 TB | 32 TB | Boot partitions, USB drives |
| ext3 | ✅ Yes | 2 TB | 32 TB | Legacy Linux systems |
| ext4 | ✅ Yes | 16 TB | 1 EB | Modern Linux systems |
| XFS | ✅ Yes | 8 EB | 8 EB | Enterprise servers, databases |
| Btrfs | ✅ Yes | 16 EB | 16 EB | Snapshots, containers |
| ZFS | ✅ Yes | Extremely Large | Extremely Large | Storage servers |

---

# ext2 Filesystem

## Overview

**ext2 (Second Extended Filesystem)** was introduced in **1993**.

It is one of the earliest Linux filesystems.

### Features

- No journaling
- Simple design
- Low disk overhead
- Fast writes

### Advantages

- Fast because there is no journal
- Low write overhead
- Suitable for flash storage and USB drives

### Disadvantages

- Filesystem corruption after crashes
- Requires `fsck` after improper shutdown
- Long recovery time

### Best Use Cases

- Boot partitions
- Embedded systems
- USB drives
- Read-mostly storage

---

# ext3 Filesystem

## Overview

**ext3 (Third Extended Filesystem)** was introduced in **2001**.

The biggest improvement over ext2 was **journaling**.

### Features

- Journaling
- Backward compatible with ext2
- Faster recovery after crashes

### Advantages

- Better reliability
- Faster recovery
- Less chance of filesystem corruption

### Disadvantages

- Slightly slower due to journaling
- Limited scalability
- No extents

### Best Use Cases

- Legacy Linux servers
- Older enterprise systems

---

# ext4 Filesystem

## Overview

**ext4 (Fourth Extended Filesystem)** was introduced in **2008**.

It is the default filesystem for many Linux distributions.

### Features

- Journaling
- Extents
- Delayed Allocation
- Larger files
- Larger filesystems
- Faster fsck
- Better performance
- Reduced fragmentation

### Advantages

- Excellent performance
- High reliability
- Supports very large filesystems
- Faster boot after crashes
- Less fragmentation

### Disadvantages

- Slightly more complex than ext2/ext3
- Not always the fastest for very large sequential workloads

### Best Use Cases

- Desktop Linux
- Cloud servers
- AWS EC2 instances
- General-purpose storage

---

# XFS Filesystem

## Overview

**XFS** was developed by Silicon Graphics (SGI) and is designed for **high-performance and enterprise workloads**.

It is the default filesystem in many Red Hat-based Linux distributions.

### Features

- Journaling
- Extent-based allocation
- Dynamic inode allocation
- Online defragmentation
- Online filesystem expansion
- High-performance parallel I/O

### Advantages

- Excellent performance with large files
- Handles huge filesystems efficiently
- Very scalable
- Ideal for databases and media servers

### Disadvantages

- Cannot shrink a filesystem
- Slightly slower for very small files compared to ext4
- More advanced administration

### Best Use Cases

- Enterprise servers
- Databases
- Video storage
- Backup servers
- Big data workloads

---

# ext2 vs ext3 vs ext4 vs XFS

| Feature | ext2 | ext3 | ext4 | XFS |
|----------|------|------|------|------|
| Journaling | ❌ No | ✅ Yes | ✅ Yes | ✅ Yes |
| Performance | Fast | Good | Excellent | Excellent |
| Crash Recovery | Poor | Good | Excellent | Excellent |
| Max File Size | 2 TB | 2 TB | 16 TB | 8 EB |
| Max Filesystem Size | 32 TB | 32 TB | 1 EB | 8 EB |
| Extents | ❌ | ❌ | ✅ | ✅ |
| Delayed Allocation | ❌ | ❌ | ✅ | ✅ |
| Dynamic Inodes | ❌ | ❌ | ❌ | ✅ |
| Online Expansion | Limited | Limited | Yes | Yes |
| Online Shrink | No | No | No | ❌ No |
| Online Defragmentation | ❌ | ❌ | Limited | ✅ Yes |
| Fragmentation | High | Medium | Low | Very Low |
| Recovery Speed | Slow | Fast | Faster | Fast |
| Typical Usage | USB, Boot | Legacy Systems | General Linux | Enterprise Storage |

---

# When to Use Which Filesystem

| Filesystem | Recommended For |
|------------|-----------------|
| ext2 | USB drives, boot partitions, embedded systems |
| ext3 | Legacy Linux servers |
| ext4 | General Linux installations, cloud servers, AWS EC2 |
| XFS | Enterprise servers, databases, backup storage, large media files |

---

# Common Linux Commands

## Check Filesystem Type

```bash
lsblk -f
```

---

## Show Mounted Filesystems

```bash
df -Th
```

---

## Show UUID and Filesystem

```bash
blkid
```

---

## Display Mounted Filesystems

```bash
mount
```

---

## Show Mount Tree

```bash
findmnt
```

---

## View Persistent Mounts

```bash
cat /etc/fstab
```

---

## Create an ext4 Filesystem

```bash
sudo mkfs.ext4 /dev/xvdf
```

---

## Create an XFS Filesystem

```bash
sudo mkfs.xfs /dev/xvdf
```

---

## Mount a Filesystem

```bash
sudo mount /dev/xvdf /data
```

---

# AWS Perspective

When you launch an EC2 instance:

- Ubuntu and Debian typically use **ext4** for the root filesystem.
- Many Red Hat-based distributions (RHEL, CentOS Stream, Rocky Linux, AlmaLinux) use **XFS** by default.
- Additional EBS volumes can be formatted with ext4, XFS, or another supported filesystem based on workload.

Architecture:

```text
EC2 Instance

↓

Operating System

↓

Filesystem (ext4 / XFS)

↓

Amazon EBS Volume

↓

AWS Physical Storage
```

---

# Interview Questions

### 1. What is a filesystem?

A filesystem is a method used by an operating system to organize, store, retrieve, and manage data on a storage device.

---

### 2. What is the difference between ext2, ext3, ext4, and XFS?

- **ext2:** No journaling; simple and fast but less reliable.
- **ext3:** Adds journaling for better reliability.
- **ext4:** Adds journaling, extents, delayed allocation, larger file support, and better performance.
- **XFS:** Enterprise-grade filesystem optimized for very large files, high-performance I/O, and scalability.

---

### 3. Which filesystem is the default in Ubuntu?

**ext4**

---

### 4. Which filesystem is commonly used in RHEL?

**XFS**

---

### 5. Which filesystem is best for databases?

Generally **XFS**, because it performs very well with large files and parallel I/O workloads.

---

# Summary

| Filesystem | Biggest Advantage | Biggest Limitation |
|------------|-------------------|--------------------|
| ext2 | Very fast, low overhead | No journaling |
| ext3 | Reliable journaling | Older design, limited scalability |
| ext4 | Excellent balance of speed, reliability, and scalability | Not always optimal for the largest sequential workloads |
| XFS | Outstanding scalability and large-file performance | Cannot shrink a filesystem |

---

# Key Takeaways

- A **filesystem** organizes and manages data on storage devices.
- **ext2** is fast but lacks journaling.
- **ext3** introduced journaling for safer crash recovery.
- **ext4** is the modern general-purpose Linux filesystem with improved performance and scalability.
- **XFS** is designed for enterprise workloads, large files, and high-performance storage.
- Modern cloud and Linux environments primarily use **ext4** or **XFS**.
