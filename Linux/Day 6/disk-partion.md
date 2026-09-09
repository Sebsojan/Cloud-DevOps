# Ubuntu Disk Partitioning
 
A simple guide to remember disk partitioning on Ubuntu, based on real install experience.
 
---
 
## 1. What is a Disk Partition?
 
A **partition** is a section of a hard disk that the OS treats as a separate unit. Think of your disk as a notebook, and partitions as chapters — each chapter can  hold different things (OS, files, swap, etc).
 
---
 
## 2. Partition Types

MBR = Master Boot Record.

It is an older method of storing the disk's partition table—information about where partitions begin and end.
 
| Type | Purpose |
|------|---------|
| **Primary** | Bootable, max 4 on MBR disks |
| **Extended** | A container that holds logical partitions |
| **Logical** | Created inside an extended partition |
| **GPT** | Modern style — supports up to 128 partitions, no extended/logical needed |
 

Primary - A primary partition is a normal partition directly created in an MBR disk's partition table.An MBR disk can have up to 4 primary partition entries.

Extended Partitions - Primary can be only 4 , so if need more partition use extended partition.

logical partition is a partition created inside an extended partition.

Disk
├── Primary
├── Primary
├── Primary
└── Extended
     ├── Logical
     ├── Logical
     └── Logical


Extended partition
┌───────────────────────────────────┐
│ Logical 1 → Data                  │
│ Logical 2 → Linux                 │
│ Logical 3 → Backup                │
└───────────────────────────────────┘

GPT

GPT = GUID Partition Table.

GPT is the modern partition-table format.

It does not use the old Primary → Extended → Logical structure.

Create partitions directly:

GPT Disk
├── EFI System Partition
├── Ubuntu /
├── /home
├── Data
├── Backup
└── ...


> **Tip:** New systems use **GPT + UEFI**. Older systems use **MBR + BIOS**.
 
---
 
## 3. Common Filesystems

A partition is basically storage space.

A filesystem determines how that storage is organized so Linux can store files on it.
 
- **ext4** → default for Ubuntu Linux partitions
- **swap** → virtual memory 4GB (used when RAM is full)
- **fat32 / vfat** → for the EFI boot partition
- **ntfs** → for sharing with Windows
---
 
## 4. Standard Ubuntu Partition Scheme
 
A clean, beginner-friendly layout for a 100 GB disk:
 
| Mount Point | Size | Filesystem | Purpose |
|-------------|------|------------|---------|
| `/boot/efi` | 512 MB | fat32 | EFI boot (UEFI systems only) |
| `/` (root) | 30 GB | ext4 | OS + installed apps |
| `/home` | 65 GB | ext4 | Your personal files |
| `swap` | 4 GB | swap | Backup memory |
 
> **Why separate `/home`?** If you reinstall Ubuntu later, your files stay safe.
 
---
 
## 5. Hands-on Example — "My First Ubuntu Install"
 
Imagine you have a **120 GB SSD** and want to dual-boot Ubuntu beside Windows.
 
### Step-by-step (during Ubuntu installer):
 
1. Choose **"Something else"** when asked about install type.
2. Select free space → click **+** to create partitions:
```
+------------------+----------+-----------+------------------+
| Mount Point      | Size     | Type      | Filesystem       |
+------------------+----------+-----------+------------------+
| /boot/efi        | 512 MB   | Primary   | EFI System       |
| /                | 40 GB    | Primary   | ext4             |
| /home            | 75 GB    | Primary   | ext4             |
| swap             | 4 GB     | Primary   | swap area        |
+------------------+----------+-----------+------------------+
```
 
3. Set **bootloader location** to the EFI partition (e.g., `/dev/sda1`).
4. Click **Install Now** → confirm → done.
### A simple way to remember it (the **"BRHS"** trick):
 
- **B** → Boot (EFI, ~512 MB)
- **R** → Root `/` (30–40 GB)
- **H** → Home `/home` (rest of the disk)
- **S** → Swap (RAM size or 2× RAM if low memory)
---
 
## 6. Useful Commands
 
After install, these help inspect and manage partitions:
 
```bash
# Lists all disks, partitions, and their mount points in a simple tree view.
lsblk
 
# Displays detailed information about disks and their partition tables.
sudo fdisk -l
 
# Shows filesystem disk usage and available space in human-readable format.
df -h
 
#Displays the currently active swap space and its usage. 
swapon --show
 
# Opens the graphical tool for viewing and managing disk partition
sudo gparted
 
# Edit partition table (CLI)/Opens a command-line partition editor for the /dev/sda disk.
sudo cfdisk /dev/sda
```
 
---
 
## 7. Things I Learned the Hard Way
 
- **Always back up** before touching partitions — one wrong click can wipe everything.
- **Don't shrink an active partition** without unmounting it first. Use a Live USB.
- **Swap can be a file**, not just a partition (`/swapfile`) — easier to resize later.
- **EFI partition is mandatory** on UEFI systems. Skip it and the system won't boot.
- **Label your partitions** — saves confusion when you have 3+ of them.
- **GParted from a Live USB** is the safest way to resize the partition holding your running OS.
---
 
## 8. Quick Reference Cheatsheet
 
```
Disk     →  /dev/sda, /dev/nvme0n1
Partition → /dev/sda1, /dev/sda2, ...
Mount    →  sudo mount /dev/sda3 /mnt
Unmount  →  sudo umount /mnt
Format   →  sudo mkfs.ext4 /dev/sda3
```
 
---
