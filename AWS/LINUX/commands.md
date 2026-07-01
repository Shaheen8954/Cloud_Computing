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





### Summary Table
Command	Purpose	Why it's important

| Command            | Purpose                             | Why it's important                       |
| ------------------ | ----------------------------------- | ---------------------------------------- |
| `lsblk`            | List block devices                  | Verify disks and mount points            |
| `df -h`            | Show disk usage                     | Confirm mounted filesystems              |
| `mkfs.ext4`        | Create filesystem                   | Prepare a new disk for use               |
| `mkdir`            | Create directory                    | Create a mount point                     |
| `mount`            | Mount a filesystem                  | Make storage accessible                  |
| `umount`           | Unmount a filesystem                | Safely detach storage                    |
| `blkid`            | Show UUID                           | Use stable disk identifiers              |
| `cat /etc/fstab`   | View persistent mount configuration | Verify automatic mounts                  |
| `mount -a`         | Mount all filesystems from `fstab`  | Test configuration without reboot        |
| `adduser`          | Create a user                       | Add local accounts                       |
| `passwd`           | Set/change a password               | Configure authentication                 |
| `id`               | Show user and group IDs             | Verify user details                      |
| `groups`           | List group memberships              | Check permissions                        |
| `usermod -aG sudo` | Add user to `sudo` group            | Grant administrative privileges          |
| `sudo`             | Run a command as root               | Perform privileged tasks securely        |
| `visudo`           | Safely edit the `sudoers` file      | Configure sudo without syntax errors     |
| `systemctl`        | Manage system services              | Start, stop, restart, and check services |
| `reboot`           | Restart the system                  | Test persistence and service startup     |
| `cat`              | Display file contents               | Inspect configuration files              |
| `grep`             | Search for text in files            | Quickly find configuration settings      |





These commands form the core toolkit for Linux system administration and are commonly used in AWS EC2 management, making them frequent topics in interviews and day-to-day operations.
