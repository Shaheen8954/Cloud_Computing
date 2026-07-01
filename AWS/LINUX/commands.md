## lsblk (List Block Devices)
What does it do?

lsblk lists all block storage devices connected to your Linux system.

A block device is any storage device that stores data in blocks, such as:

Hard disks
SSDs
NVMe drives
USB drives
EBS volumes (AWS)


### Why do we use it?

Suppose you attach a new EBS volume in AWS.

Before Linux can use it, you must first check whether the OS can see the disk.

lsblk

Example:

NAME        SIZE TYPE MOUNTPOINT
nvme0n1      20G disk
├─nvme0n1p1  19G part /
└─nvme0n1p2   1G part /boot

nvme1n1      10G disk


### Notice:

nvme0n1 = Root disk
nvme1n1 = Newly attached EBS volume

At this stage:

It exists.
It is not formatted.
It is not mounted.


### What information does lsblk provide?
Disk name
Size
Partition layout
Mount point
Device hierarchy

### When do we use it?

Use it:
After attaching an EBS volume
Before creating a filesystem
Before mounting
To identify the correct disk


