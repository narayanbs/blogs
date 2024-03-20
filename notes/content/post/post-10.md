---
title: "Disk Partitions and Mounting Filesystems"
date: 2022-02-15T17:52:54+05:30
draft: false
disqus: false
tags: [Linux]
---
Partitioning disks in Linux is generally the first step before installing a system. Before we can create any files, a filesystem must exist. Similarly, we can’t make a filesystem unless there is a partition on the disk.

Partitions are used to divide raw storage space into chunks (slices). This provides the means for organization and hosting of filesystems. Besides that, partitions also help isolate filesystem faults.

The information about how a disk is partitioned is stored on the disk. This is usually called a partition table. The entries in this table define where partitions on the disk start and end. The format of this table is sometimes regarded as the disk type.

**MBR (Master Boot Record) and GPT (GUID Partition Table)** are the most widely used partition tables. As compared to GPT, MBR is an old standard and has some limitations.

### BIOS and UEFI
BIOS (Basic Input Output System) is low-level software that performs hardware initialization and loads the boot loader from the MBR. Gradually BIOS is being replaced by UEFI (Unified Extensible Firmware Interface). Still, most new systems have support for both.

Instead of loading a boot loader from the MBR, UEFI uses efi images from the EFI System Partition. With UEFI and GPT, we can have large disk support.

In some Linux systems, it is possible to use BIOS boot mode with a GPT disk.

### Master Boot Record (MBR)
MBR is the old standard for managing the partition in the hard disk, and it is still being used extensively by many people. The MBR resides at the very beginning of the hard disk and it holds the information on how the logical partitions are organized in the storage device. In addition, the MBR also contains executable code that can scan the partitions for the active OS and load up the boot up code/procedure for the OS.

For a MBR disk, you can only have four primary partitions. To create more partitions, you can set the fourth partition as the extended partition and you will be able to create more sub-partitions (or logical drives) within it. As MBR partition entries and MBR boot code use 32-bit values, each partition can only go up to a maximum of 2TB in size. (2 ** 32 sectors, and each sector is 512 bytes. so 2 ** 32 * 512 = 2TB).

This is how a typical MBR disk layout looks like:
![mbr](/images/mbr.jpg)

Apart form the limited partition table size of 2TB, another pitfall is that the mbr is the only place that holds the partition information. If it ever gets corrupted then entire hard disk is unreadable.

### GUID Partition Table (GPT)
GPT is the latest standard for laying out the partitions of a hard disk. It makes use of globally unique identifiers (GUID) to define the partition and it is part of the UEFI standard. This means that on a UEFI-based system (which is required for Windows 8 Secure Boot feature), it is a must to use GPT. With GPT, you can create theoretically unlimited partitions on the hard disk, even though it is generally restricted to 128 partitions by most OSes. Unlike MBR that limits each partition to only 2TB in size, each partition in GPT can hold up to 2^64 blocks in length (as it is using 64-bit), which is equivalent to 9.44ZB for a 512-byte block (1 ZB is 1 billion terabytes). In Microsoft Windows, that size is limited to 256TB.

Each partition has a unique guid, and one of partitions is a system partition, known as the EFI System Partition, or the ESP. This partition has the type code UID (C12A7328-F81F-11D2-BA4B-00A0C93EC93B) and must be on the primary hard drive. The device boots to this partition. The minimum size of this partition is 100 MB, and must be formatted using the FAT32 file format.

![mbr](/images/gpt.jpg)

From the GPT Table Scheme diagram above, you can see that there is a primary GPT at the beginning of the hard disk and a secondary GPT at the end. This is what makes GPT more useful than MBR. GPT stores a backup header and partition table at the end of the disk so it can be recovered if the primary tables are corrupted. It also carry out CRC32 checksums to detect errors and corruption of the header and partition table.

You can also see that there is a protective MBR at the first sector of the hard disk. Such hybrid setup is to allow a BIOS-based system to boot from a GPT disk using a boot loader stored in the protective MBR’s code area. In addition, it protects the GPT disk from damage by GPT-unaware disk utilties.

### Partitioning Tools
Historically, to partition MBR disks, the **fdisk** tool is used. However, in new Linux systems, fdisk can also understand GPT format.

The **parted** tool is newer and understands both GPT and MBR layouts. Moreover, it is also considered a better option for partitioning disks in Linux because of its usability in scripting and automation.

## Parted 

First identify the block device you need to create a partition into.
```bash
$ lsblk -e7
Name    MAJ:MIN RM  SIZE    RO      TYPE    MOUNTPOINT
sda       8:0   0   100G     0      disk
|-sda1    8:1   0   512M     0      part    /boot/efi
|-sda2    8:2   0     1k     0      part    
|-sda5    8:5   0  99,5G     0      part    /
sdb       8:16  0 976,6G     0      disk
sdc       8:32  0   3,8T     0      disk
```
Note: -e7 excludes loop devices (device number 7).

In total, we’ve three disks in this system. The sda drive is the Linux system drive with three partitions. But, drives sdb and sdc have no partition table.

You can also use
```bash
$ sudo lshw -class disk -short

H/W path           Device           Class          Description
==============================================================
/0/100/1f.2/0      /dev/sda         disk           500GB CT500MX500SSD1
/0/100/1f.2/1      /dev/cdrom       disk           DVD+-RW GU90N
/0/100/1f.2/1/0    /dev/sdb         disk           Transcend
/0/100/1f.2/1/1    /dev/sdc         disk           Sandisk
```

Now let's start parted and get the partition information. 

```bash
$ sudo parted
GNU Parted 3.3
Using /dev/sda
Welcome to GNU Parted! Type 'help' to view a list of commands.
(parted) print

Model: ATA VBOX HARDDISK (scsi)
Disk /dev/sda: 107GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Disk Flags: 

Number  Start   End    Size   Type      File system  Flags
 1      1049kB  538MB  537MB  primary   fat32        boot
 2      539MB   107GB  107GB  extended
 5      539MB   107GB  107GB  logical   ext4

(parted)
```
Here, we’ve used the print command to show information about the sda drive. We can see that the partition table is MBR (msdos). Furthermore, the disk size (by default) is reported in bytes.


Now, Let's create a partition table for /dev/sdb
To change the target device, we use the select command. 

```bash
(parted) select /dev/sdb                                                  
Using /dev/sdb
(parted)

# Additionally, we can also start the parted program with a specific device:

(parted) quit                                                             

$ sudo parted /dev/sdb
GNU Parted 3.3
Using /dev/sdb
Welcome to GNU Parted! Type 'help' to view a list of commands.
(parted) q                                                                
$
```
### Creating Partitions
The mklabel command sets the partition table type. And, the most usual choices are gpt and msdos (MBR). In particular, we must set this before partitioning the disk.

The -s  option is useful in scripts to suppress most warnings from the parted command.
```bash
$ sudo su
* (parted)mklabel gpt
    or
* (parted)mklabel msdos
```
Note: `*` represents the root prompt in this article.

To create partitions on the selected disk, we can use the mkpart command. The syntax requires partition-type, filesystem-type, start, and end parameters.
```bash
(parted) mkpart part-type-or-part-label fs-type start end
```
* part-type-or-part-label is interpreted differently based on the partition table:
    * MBR: the parameter is interpreted as part-type, which can be one of `primary, extended or logical`.
    * GPT: the parameter is interpreted as part-label, which sets the PARTLABEL attribute of the partition. The partition label always has to be set, since mkpart does not allow to create partitions with empty label. so if you use `mkpart primary...` even for GPT, this would set "primary" as the partition label.

* fs-type is an identifier chosen among those listed by entering **help mkpart** as the closest match to the file system that you will use. The mkpart command does not actually create the file system: the fs-type parameter will simply be used by parted to set a 1-byte code that is used by boot loaders to "preview" what kind of data is found in the partition, and act accordingly if necessary.

* start is the beginning of the partition from the start of the device. It consists of a number followed by a unit, for example 1MiB means start at 1 MiB.
* end is the end of the partition from the start of the device (not from the start value). It has the same syntax as start, for example 100% means end at the end of the device (use all the remaining space).

Different units are allowed for start and end parameters in the mkpart command. For example, we’ve used percentages, exact sector counts, and sizes as units. Moreover, we can also mix these units in a single command invocation. We’ve also set partition names based on the intended usage.

#### UEFI/GPT Examples
If creating a new EFI system partition, use the following commands (the recommended size is at least 300 MiB):
```
(parted) mkpart "EFI system partition" fat32 1MiB 301MiB
(parted) set 1 esp on
```
The remaining partition scheme is entirely up to you. For one other partition using 100% of remaining space:
```
(parted) mkpart "my partition label" ext4 301MiB 100%
```
For separate / (20 GiB) and /home (all remaining space) partitions:
```
(parted) mkpart "root partition" ext4 301MiB 20.5GiB
(parted) mkpart "home partition" ext4 20.5GiB 100%
```
And for separate / (20 GiB), swap (4 GiB), and /home (all remaining space) partitions:
```
(parted) mkpart "root partition" ext4 301MiB 20.5GiB
(parted) mkpart "swap partition" linux-swap 20.5GiB 24.5GiB
(parted) mkpart "home partition" ext4 24.5GiB 100%
```
#### Resizing Partitions

To grow a partition (in parted interactive mode):
```
(parted) resizepart number end
```
Where number is the number of the partition you are growing, and end is the new end of the partition (which needs to be larger than the old end).
for ex:
```
 sudo parted /dev/sdb resizepart 1 400M
```
This only increases the size of the partition but not the contents.
Then, to grow the (ext2/3/4) filesystem on the partition (if size is not specified, it will default to the size of the partition), use

```
# resize2fs /dev/sdb1 400M
```
The same command also works for shrinking filesystems, except that you give them in reverse i.e shrink the file system and then
chop the empty space off the end of the partition. 

To shrink an ext2/3/4 filesystem on the partition:
```
# resize2fs /dev/sdb1 200M
# sudo parted /dev/sdb resizepart 1 200M
```
Note: In contrast to parted, resize2fs(8) uses K, M, G and T to mean KiB, MiB, GiB and TiB. Be aware that e2fsprogs documentation misrefers to kibibytes, mebibytes, gibibytes and tebibytes as "power-of-two kilobytes, megabytes, gigabytes, terabytes".

Removing a partition
```
(parted) rm                                                               
Partition number? 2
```
Format a new partition to use a filesystem, ex: format our new partition in the ext4 file system using mkfs
```
mkfs.ext4 /dev/sdb1
```


#### BIOS/MBR Examples
For a minimum single primary partition using all available disk space, the following command would be used:
```
(parted) mkpart primary ext4 1MiB 100%
(parted) set 1 boot on
```
In the following instance, a 20 GiB / partition will be created, followed by a /home partition using all the remaining space:
```
(parted) mkpart primary ext4 1MiB 20GiB
(parted) set 1 boot on
(parted) mkpart primary ext4 20GiB 100%
```
In the final example below, separate /boot (100 MiB), / (20 GiB), swap (4 GiB), and /home (all remaining space) partitions will be created:
```
(parted) mkpart primary ext3 1MiB 100MiB
(parted) set 1 boot on
(parted) mkpart primary ext3 100MiB 20GiB
(parted) mkpart primary linux-swap 20GiB 24GiB
(parted) mkpart primary ext3 24GiB 100%
```

## Fdisk
### Listing existing partitions
```bash
$ sudo fdisk -l
```
### Select storage devices
```bash
$ fdisk /dev/sdg
Command (m for help): p
 
Disk /dev/sdg: 3874 MB, 3874488320 bytes
42 heads, 8 sectors/track, 22521 cylinders, total 7567360 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x0006301d
 
   Device Boot      Start         End      Blocks   Id  System
/dev/sdg1            2048     7567359     3782656    b  W95 FAT32
```
### Delete an existing partition
```bash
Command (m for help): d
Selected partition 1
Partition 1 is deleted
 
Command (m for help): p
 
Disk /dev/sdg: 3874 MB, 3874488320 bytes
42 heads, 8 sectors/track, 22521 cylinders, total 7567360 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x0006301d
 
   Device Boot      Start         End      Blocks   Id  System
```
### Create a new partition
```bash
Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): 
Using default response p
Partition number (1-4, default 1): 
Using default value 1
First sector (2048-7567359, default 2048): 
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-7567359, default 7567359): 
Using default value 7567359
Partition 1 of type Linux and of size 3,6 GiB is set
 
Command (m for help): p
 
Disk /dev/sdg: 3874 MB, 3874488320 bytes
42 heads, 8 sectors/track, 22521 cylinders, total 7567360 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x0006301d
 
   Device Boot      Start         End      Blocks   Id  System
/dev/sdg1            2048     7567359     3782656   83  Linux
```
By default, partition type will have id 83 (Linux) and this should be changed to FAT32. Type t command and set partition id as b (all partition codes can be listed with L command):

```bash
Command (m for help): t
Selected partition 1
Hex code (type L to list codes): b
Changed system type of partition 1 to b (W95 FAT32)
 
Command (m for help): p
 
Disk /dev/sdg: 3874 MB, 3874488320 bytes
42 heads, 8 sectors/track, 22521 cylinders, total 7567360 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x0006301d
 
   Device Boot      Start         End      Blocks   Id  System
/dev/sdg1            2048     7567359     3782656    b  W95 FAT32
```

Partition should be set as active or USB drive will not be attached to the file-system when plugged in. To set active partition type in a command and choose first partition number:

```bash
Command (m for help): a
Partition number (1-4): 1
 
Command (m for help): p
 
Disk /dev/sdg: 3874 MB, 3874488320 bytes
42 heads, 8 sectors/track, 22521 cylinders, total 7567360 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x0006301d
 
   Device Boot      Start         End      Blocks   Id  System
/dev/sdg1   *        2048     7567359     3782656    b  W95 FAT32
```

Write changes to disk and exit from fdisk utility

```bash
Command (m for help): w
The partition table has been altered!
```

Now FAT32 file-system can be created on USB drive from command line with mkfs (once again be sure that you typed in correct USB device name):

```bash
$ sudo mkfs -t vfat /dev/sdg1
mkfs.vfat 3.0.12 (29 Oct 2011)
```
Eject and reinsert USB drive, it should be empty and mounted. Here is df -hT output:

```bash
$ sudo eject /dev/sdg
$ df -HT
Filesystem     Type      Size  Used Avail Use% Mounted on
rootfs         rootfs     30G  5,6G   23G  20% /
devtmpfs       devtmpfs  1,8G     0  1,8G   0% /dev
tmpfs          tmpfs     1,8G  868K  1,8G   1% /dev/shm
tmpfs          tmpfs     1,8G  1,3M  1,8G   1% /run
/dev/sda1      ext2       30G  5,6G   23G  20% /
tmpfs          tmpfs     1,8G     0  1,8G   0% /sys/fs/cgroup
tmpfs          tmpfs     1,8G     0  1,8G   0% /media
/dev/sdb5      ext4      204G   41G  153G  22% /home
/dev/sdb1      ext4       20G  9,1G  9,7G  49% /var
/dev/sdb3      ext4      4,0G  137M  3,7G   4% /tmp
/dev/sdg1      vfat      3,7G  4,0K  3,7G   1% /run/media/dbunic/DF85-CAF4
```
