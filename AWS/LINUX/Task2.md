## explain the output of ```cat /etc/fstab```

ubuntu@ip-10-0-0-15:~$ cat /etc/fstab
LABEL=cloudimg-rootfs   /        ext4   discard,commit=30,errors=remount-ro     0 1
LABEL=BOOT      /boot   ext4    defaults        0 2
LABEL=UEFI      /boot/efi       vfat    umask=0077      0 1
UUID=332ac635-344d-4149-969f-379fe8df4bd8  /data  ext4  defaults,nofail  0  2


### /etc/fstab (File System Table) is a Linux configuration file that tells the operating system which filesystems
should be mounted automatically during boot, where they should be mounted, and with what options.

### General Format:

Device    Mount_Point    Filesystem    Options    Dump    Pass
