# Ubuntu Partition Resize with GParted Live USB

## Goal

The goal of this lab was to safely increase the Ubuntu Linux partition on a dual-boot laptop in preparation for cybersecurity labs, virtual machines, Docker, network tools, and future defensive security practice.

## System Context

The laptop is used as a cybersecurity learning and remote-access workstation.

General setup:

- Lenovo laptop
- Intel Core Ultra 7 CPU
- 16 GB RAM
- 1 TB NVMe SSD
- Dual boot:
  - Windows 11
  - Ubuntu Linux

## Initial Situation

Approximate initial layout:

```text
EFI partition
Windows 11 partition: ~250 GB
Ubuntu partition: ~329 GB
Linux swap: ~7.5 GB
DATA partition: ~365 GB
Recovery partition
Tools Used
Ubuntu Live USB
GParted
Terminal
lsblk
df -h /
Step 1: Boot into Ubuntu Live USB

The laptop was booted from the Ubuntu USB stick.

The Live USB environment was verified with:

lsblk

and:

df -h /

The terminal prompt:

ubuntu@ubuntu

confirmed that the system was running from the Live USB environment.

Step 2: Open GParted

GParted was opened with:

sudo gparted
Step 3: Shrink the DATA Partition

The DATA partition was reduced by approximately 100 GB to create unallocated space before the DATA partition.

Approximate values:

Free space preceding: 102400 MiB
Step 4: Apply the First Operation

The resize operation was applied successfully in GParted.

Result:

Ubuntu partition
Swap partition
100 GB unallocated space
DATA partition
Step 5: Move the Swap Partition

The Linux swap partition was moved to the right so the unallocated space would become adjacent to the Ubuntu partition.

Step 6: Expand the Ubuntu Partition

The Ubuntu ext4 partition was expanded into the newly available unallocated space.

Approximate result:

Ubuntu partition increased from ~329 GB to ~429 GB
Step 7: Apply All Operations

All pending GParted operations were applied successfully.

Step 8: Verify the Result

The final result was verified with:

df -h /

The Ubuntu root filesystem increased to approximately 422–429 GB usable size.

Commands Used
lsblk

Displays block devices and partition structure.

df -h /

Displays available space on the Ubuntu root filesystem.

Explanation:

df      = disk filesystem usage
-h      = human-readable format
/       = root filesystem
Result

Final approximate layout:

Windows 11: ~250 GB
Ubuntu root filesystem: ~422–429 GB
Linux swap: ~7.5 GB
DATA partition: ~265 GB
Lessons Learned

This lab provided practical experience with:

Ubuntu Live USB
GParted partition management
Linux partition resizing
Moving Linux swap partitions
Expanding ext4 filesystems
Understanding root filesystem structure
Difference between lsblk and df -h /
Safe partition management practices
Security Notes

No personal information, internal IP addresses, usernames, or sensitive screenshots should be published in public repositories.
