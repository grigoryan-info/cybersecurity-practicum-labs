# Dell EFI System Partition Cleanup on Ubuntu Dual-Boot System

## Objective

Investigate and resolve a nearly full EFI System Partition (ESP) on a Dell dual-boot laptop running Ubuntu and Windows 11 without performing risky partition resizing operations.

---

## Initial Problem

The system displayed warnings indicating low available space inside the EFI partition.

The following command was used to inspect filesystem usage:

```bash
df -h
```

Relevant output:

```text
/dev/nvme1n1p1   96M   96M  132K 100% /boot/efi
```

The EFI partition was completely full, with only 132 KB remaining.

---

## EFI Space Investigation

To identify which directories consumed most of the EFI storage, the following command was executed:

```bash
sudo du -h /boot/efi --max-depth=3
```

### Command Explanation

- `du` = display disk usage
- `-h` = human-readable format
- `--max-depth=3` = show directory usage up to 3 levels deep
- `sudo` = required for protected system directories

Relevant findings:

```text
32M /boot/efi/EFI/Microsoft
4.3M /boot/efi/EFI/ubuntu
59M /boot/efi/EFI/dell
```

Analysis showed that the Dell recovery environment occupied most of the EFI partition space.

---

## UEFI Boot Verification

The following command was used to inspect active UEFI boot entries:

```bash
sudo efibootmgr -v
```

### Command Explanation

- `efibootmgr` = manage and inspect UEFI boot entries
- `-v` = verbose mode with detailed boot paths

Result:
- Ubuntu boot entry was active
- Windows Boot Manager was active
- No active Dell recovery boot entry was detected

This indicated that the Dell EFI recovery files were not actively used during normal system boot.

---

## Backup Creation

Before removing any files, a backup of the Dell EFI directory was created.

### Create Backup Directory

```bash
mkdir -p ~/efi-backup
```

### Command Explanation

- `mkdir` = create directory
- `-p` = create parent directories if needed
- `~/efi-backup` = backup location inside the user's home directory

### Copy Dell EFI Files

```bash
sudo cp -r /boot/efi/EFI/dell ~/efi-backup/
```

### Command Explanation

- `cp` = copy files/directories
- `-r` = recursive copy
- `sudo` = elevated privileges required

---

## Backup Verification

The backup contents were verified using:

```bash
ls ~/efi-backup/dell
```

### Command Explanation

- `ls` = list directory contents

Output:

```text
bios  SOS
```

The backup was successfully created before cleanup operations.

---

## Dell EFI Cleanup

The unused Dell EFI recovery directory was removed:

```bash
sudo rm -rf /boot/efi/EFI/dell
```

### Command Explanation

- `rm` = remove files/directories
- `-r` = recursive deletion
- `-f` = force deletion without confirmation
- `sudo` = required for system-level operations

Important:
- Ubuntu GRUB bootloader was not modified
- Windows Boot Manager was not modified
- Only unused Dell recovery EFI files were removed

---

## Verification After Cleanup

EFI storage usage was verified again:

```bash
df -h /boot/efi
```

Result:

```text
/dev/nvme1n1p1   96M   38M   59M  39% /boot/efi
```

---

## Final Result

The EFI partition returned to a healthy operational state.

### Before Cleanup

```text
EFI Usage: 100%
Free Space: 132 KB
```

### After Cleanup

```text
EFI Usage: 39%
Free Space: 59 MB
```

---

## Conclusion

The issue was caused primarily by unused Dell EFI recovery files occupying most of the EFI System Partition.

Instead of performing risky partition resizing operations involving:
- GPT modification
- Windows partition movement
- EFI relocation

the problem was resolved safely through:
- EFI usage analysis
- UEFI boot verification
- backup creation
- targeted cleanup of unused Dell recovery files

This approach restored healthy EFI free space while avoiding unnecessary boot or partition risks.
