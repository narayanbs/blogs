---
title: "Linux Device Management"
date: 2022-02-15T13:02:03+05:30
draft: false
disqus: false
tags: [Linux]
---

### Device files
It is easy to manipulate most devices on a unix system because the kernel presents many of the device I/O interfaces to processes as files. These device files are sometimes called device nodes. However there is a limit to what you can do, since not all devices capabilities are accessible with standard file i/o.

Device files are stored in /dev. use ls -l to view the device and all its permissions. The first character of each line indicates the type of device file. (b,c,p,s) indicate block, character, pipe and socket respectively.

```
[root@localhost dev] ls -l
total 0
crw-r--r--. 1 root root     10, 235 Sep 29 12:08 autofs
drwxr-xr-x. 3 root root          60 Sep 29 12:07 bus
lrwxrwxrwx. 1 root root           3 Sep 29 12:10 cdrom -> sr0
drwxr-xr-x. 2 root root        3100 Sep 29 12:10 char
crw-------. 1 root root      5,   1 Sep 29 12:08 console
lrwxrwxrwx. 1 root root          11 Sep 29 12:07 core -> /proc/kcore
drwxr-xr-x. 3 root root          60 Sep 29 12:07 cpu
crw-------. 1 root root     10,  62 Sep 29 12:08 cpu_dma_latency
drwxr-xr-x. 7 root root         140 Sep 29 12:08 disk
brw-rw----. 1 root disk    253,   0 Sep 29 12:08 dm-0
...
```
The majority of the devices we see are character or block devices. However, other types of devices exist, such as socket and pipe devices. Then
we have Pseudo-devices as well. Pseudo-devices don’t correspond to hardware devices. That is to say, they provide several useful functions. 
For example /dev/random is a function that generates random data, /dev/zero produces zeros. Pseudo-devices are character devices as well. 
We can tell the difference between pseudo-devices and other character devices using the major number of a device. 
Furthermore, device drivers associate with a device through a unique value called a major number
```
[root@localhost dev] ls -l |grep random
crw-rw-rw-. 1 root root      1,   8 Sep 29 12:08 random
```
We can also view the major and minor numbers by running stat. These numbers are displayed under device type:
```
[root@localhost dev] stat /dev/random
  File: /dev/random
  Size: 0         	Blocks: 0          IO Block: 4096   character special file
Device: 6h/6d	Inode: 10216       Links: 1     Device type: 1,8
Access: (0666/crw-rw-rw-)  Uid: (    0/    root)   Gid: (    0/    root)
Context: system_u:object_r:random_device_t:s0
Access: 2021-09-16 11:52:35.644848848 -0400
Modify: 2021-09-16 11:52:35.644848848 -0400
Change: 2021-09-16 11:52:35.644848848 -0400
 Birth: -
```
Some device files in the /dev/ directory don’t appear as block or character devices. Instead, symbolic links are listed:
```
[root@localhost dev] ls -lhaF| grep ^l 
lrwxrwxrwx.  1 root root           3 Sep 17 09:07 cdrom -> sr0
lrwxrwxrwx.  1 root root          11 Sep 17 09:06 core -> /proc/kcore
lrwxrwxrwx.  1 root root          13 Sep 17 09:06 fd -> /proc/self/fd/
lrwxrwxrwx.  1 root root          12 Sep 17 09:06 initctl -> /run/initctl|
lrwxrwxrwx.  1 root root          28 Sep 17 09:06 log -> /run/systemd/journal/dev-log=
lrwxrwxrwx.  1 root root           4 Sep 17 09:06 rtc -> rtc0
lrwxrwxrwx.  1 root root          15 Sep 17 09:06 stderr -> /proc/self/fd/2
lrwxrwxrwx.  1 root root          15 Sep 17 09:06 stdin -> /proc/self/fd/0
lrwxrwxrwx.  1 root root          15 Sep 17 09:06 stdout -> /proc/self/fd/1|
```
Symbolic links under the /dev directory are there for many reasons. For example, when looking at /dev/stdin, we see that it points to /proc/self/fd/0. This is because the/proc/self directory sits on the /proc filesystem. When a process reads /proc/self,  it gets information about itself using its process id. So the/proc/self directory is a symbolic link as well:
```
[root@localhost dev] ls -l /proc/self/fd/0
lrwx------. 1 root root 64 Sep 17 12:02 /proc/self/fd/0 -> /dev/pts/0
```
The subdirectory ../fd signifies a file descriptor. A file descriptor is a unique reference to a file or input/output (IO) resource. It assists 
in identifying the file or input/output resource that a process has to work on. For example, the/dev/pts/0 directory exists in memory. 
The contents under the directory are pseudo-terminals that allow applications to receive input and display output:
```
[root@localhost pts] ls -l /dev/pts
total 0
crw--w----. 1 root tty  136, 0 Sep 17 12:33 0
c---------. 1 root root   5, 2 Sep 17 09:06 ptmx
```

Not all devices have device files. for e.g Network interfaces don't have device files. The kernel offers other interfaces.

The traditional Unix /dev directory is a convenient way for user processes to reference and interface with devices supported by the kernel, but it’s also a very simplistic scheme. The name of the device in /dev tells you a little about the device, but usually not enough to be helpful. Another problem is that the kernel assigns devices in the order in which they are found, so a device may have a
diﬀerent name between reboots.

To provide a uniform view for attached devices based on their actual hardware attributes, the Linux kernel oﬀers the sysfs interface through a system of ﬁles and directories. The base path for devices is /sys/devices. sysfs is meant primarily to be read by programs, such as udev to access device and driver information.

### Device file creation

On any reasonably recent Linux system, you do not create your own device ﬁles; they’re created by devtmpfs, kernel and udev. However it is instructive to see how to do so, on a rare occasion you may need to create a named pipe or socket file. mknod command creates one device, you must know the device name as well as its major and minor numbers. 

For ex: creating /dev/sda1, we use
```bash
# block device with Major and Minor version number
mknod /dev/sda1 b 8 1
```

In older versions of Unix and Linux, maintaining the /dev directory was a challenge. With every signiﬁcant kernel upgrade or driver addition, the kernel could support more kinds of devices, meaning that there would be a new set of major and minor numbers to be assigned to device ﬁlenames. To tackle this maintenance challenge, each system had a MAKEDEV program in /dev to create groups of devices. 
When you upgraded your system, you would try to ﬁnd an update to MAKEDEV and then run it in order to create new devices.
This static system became ungainly, so a replacement was in order. The ﬁrst attempt to ﬁx it was devfs, a kernel-space implementation of /dev that contained all of the devices that the current kernel supported. However, there were a number of limitations, which led to the development of udev and devtmpfs.


Unnecessary complexity in the kernel is dangerous because you can too easily introduce system instability. Device ﬁle management is an example: you can create device ﬁles in user space, so why would you do this in the kernel? 
The Linux kernel can send notiﬁcations to a user-space process called udevd upon detecting a new device on the system (for example, when someone attaches a USB ﬂash drive). This udevd process could examine the new device’s characteristics, create a device ﬁle, and then
perform any device initialization.

That was the theory. Unfortunately, there is a problem with this approach—device ﬁles are necessary early in the boot procedure, so udevd must also start early. But to create device ﬁles, udevd cannot depend on any devices that it is supposed to create, and it needs to perform its initial startup very quickly so that the rest of the system doesn’t get held up waiting for udevd to start. Hence devtmpfs

### devtmpfs 
Let the kernel create a tmpfs very early at kernel initialization, before any driver core device is registered. Every device with a major/minor will have a device node created in this tmpfs instance. After the rootfs is mounted by the kernel, the populated tmpfs is mounted at /dev. The kernel creates device ﬁles as necessary, but it also notiﬁes udevd that a new device is available. Upon receiving this signal, udevd does not create the device ﬁles, but it does perform device initialization along with setting permissions and notifying other processes that new devices are available. Additionally, it creates a number of symbolic links in /dev to further identify devices. You can ﬁnd examples in the directory /dev/disk/by-id, where each attached disk has one or more entries..


### udev
udev (userspace /dev) is a system for dynamic device detection and management. It's function is
* to supply the system applications with device events
* manage permissions of device nodes
* create useful symlinks in /dev for accessing devices, or even rename network interfaces.

The udev system is composed of some kernel services and the udevd daemon. The kernel informs the udevd daemon when certain events happen. The udevd daemon is configured to respond to events with corresponding actions. The event information comes from the kernel - the actions happen in userspace. The responses to the events are configurable in "rules".
udev was created to respond to hotplug type of events, it can run arbitrary userspace commands in response to a new device appearing - or to whatever events it receives from the kernel.

### udevadm
The udevadm program is an administration tool for udevd. You can reload udevd rules and trigger events, but perhaps the most powerful features of udevadm are the ability to search for and explore system devices and the ability to monitor uevents as udevd receives them from the kernel. The command syntax can be somewhat complicated, though. There are long and short forms for most options; we’ll use the long ones here.

Let’s start by examining a system device. In order to look at all of the udev attributes used and generated in conjunction with the rules for a device such as /dev/sda, run the following command:
```bash
$ udevadm info --query=all --name=/dev/sda
```

when you insert a flash media into the usb drive and monitor events with udevadm, use
```bash
$ udevadm monitor

The abbreviated output is as shown below: 

KERNEL[658299.569485] add
/devices/pci0000:00/0000:00:1d.0/usb2/2-1/2-1.2 (usb)
KERNEL[658299.569667] add
/devices/pci0000:00/0000:00:1d.0/usb2/2-1/2-1.2/2-1.2:1.0 (usb)
KERNEL[658299.570614] add
/devices/pci0000:00/0000:00:1d.0/usb2/2-1/2-1.2/2-1.2:1.0/host15
(scsi)
KERNEL[658299.570645] add
/devices/pci0000:00/0000:00:1d.0/usb2/2-1/2-1.2/2-1.2:1.0/
host15/scsi_host/host15 (scsi_host)
UDEV [658299.622579] add
/devices/pci0000:00/0000:00:1d.0/usb2/2-1/2-1.2 (usb)
UDEV [658299.623014] add
/devices/pci0000:00/0000:00:1d.0/usb2/2-1/2-1.2/2-1.2:1.0 (usb)
UDEV [658299.623673] add
/devices/pci0000:00/0000:00:1d.0/usb2/2-1/2-1.2/2-1.2:1.0/host15
```

There are two copies of each message in this output because the default behavior is to print both the incoming message from the kernel (marked with KERNEL ) and the processing messages from udevd. 
To see only kernel events, add the --kernel option, and to see only udevd processing events, use --udev .

You can also ﬁlter events by subsystem. For example, to see only kernel messages pertaining to changes in the SCSI subsystem, use this command:
```bash
$ udevadm monitor --kernel --subsystem-match=scsi
```

## Device Name Summary

|   Device       |   Description         |
|----------------| --------------------- |
|  /dev/sd\*, /dev/sda, /dev/sdb,    | SCSI Hard disk |
|  /dev/xvd\*, /dev/vd\* | Virtual disks |
| /dev/nvme\*    | Non-volatile memory devices |
| /dev/sr\*      | CD and DVD drives      |
| /dev/dm-\*, /dev/mapper/\* | Device Mapper |
| /dev/hd\*  | PATA Hard disks |
| /dev/tty\*, /dev/pts\*, /dev/tty | Terminals |





