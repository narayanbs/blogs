---
title: "Logical Volume Manager (LVM)"
date: 2022-02-15T17:53:56+05:30
draft: false
disqus: false
tags: [Linux]
---

Logical Volume Manager (LVM) is used on Linux to manage hard drives and other storage devices. As the name implies, it can sort raw storage into logical volumes, making it easy to configure and use.

![lvm](/images/lvm.jpg)

### Install LVM on Ubuntu (if not present)

```bash
$ sudo apt install lvm2
```

### Create partitions

The first thing we will do is create partitions on our disk. This is to facilitate the creation of physical volumes, which can either be created on raw, unpartitioned block devices or single partitions. For the sake of this tutorial, we will work on the latter.

For this example, the disk we’ll be working with is /dev/sdb, which is a 5GB (and currently unpartitioned) hard disk. Refer to the diagram at the beginning of this guide to visualize the configuration we’ll be setting up.

We can see our /dev/sdb disk and its pertinent details with the following command.

```bash
$ sudo fdisk -l
```

![lvmconsole](/images/lvmconsole.jpg)

Next, let's partition the disk with cfdisk

```bash
$ sudo cfdisk /dev/sdb
```

An interface will open in the console. We've created the following two partitions
![lvmconsole1](/images/lvmconsole1.jpg)

Finalize your changes by choosing "write", then exit the utility. Now we can see the partitions when we execute **fdisk -l** again.

![lvmconsole2](/images/lvmconsole2.jpg)

### Create physical volumes

We can create physical volumes on our new partitions by using **pvcreate** command

```bash
$ sudo pvcreate /dev/sdb1
Physical volume "/dev/sdb1" successfully created.
$ sudo pvcreate /dev/sdb2
Physical volume "/dev/sdb2" successfully created.
```

Use the **pvdisplay** command to see the information about physical volumes or specify a particular volume you wish to view details about

```bash
$ sudo pvdisplay
    or
$ sudo pvdisplay /dev/sdb1
```

![lvmconsole3](/images/lvmconsole3.jpg)

### Create a virtual group

At this stage we need to create a virtual group which will serve as a container for our physical volumes. In this example, we’ll call our virtual group “mynew_vg” which will include the /dev/sdb1 partition, with the following Linux command:

```bash
$ sudo vgcreate mynew_vg /dev/sdb1
```

Or to include both partitions at once

```bash
$ sudo vgcreate mynew_vg /dev/sdb1 /dev/sdb2
```

Use the following command to display information about the virtual groups

```bash
$ sudo vgdisplay
```

![lvmconsole4](/images/lvmconsole4.jpg)

We can add more physical volumes to the group by using the **vgextend** command

```bash
$ sudo vgextend mynew_vg /dev/sdb2
Volume group "mynew_vg" successfuly extended
```

### Create logical volumes

Now we can move on to creating logical volumes. It may help to think of our virtual group as a “big cake,” from which we can cut “pieces” (logical volumes) that will get treated as partitions on our Linux system.

The following command will create a logical volume named vol01 with a size of 400MB.

```bash
$ sudo lvcreate -L 400 -n vol01 mynew_vg
```

Then, we’ll create another volume named vol02 with a size of 1GB. Again, refer to the diagram above to help visualize the configuration.

```bash
$ sudo lvcreate -L 1000 -n vol02 mynew_vg
```

Finally we can use the **lvdisplay** command to see the logical volumes
![lvmconsole5](/images/lvmconsole5.jpg)

As you can see from the screenshot below, vgdisplay shows us that we still have 3.6GB of free space in the mynew_vg virtual group.
![lvmconsole6](/images/lvmconsole6.jpg)

### Create a filesystem on logical volumes

The logical volume is almost ready to use. All we need to do is to create a filesystem on it using **mkfs** command

```bash
$ sudo mkfs.ext4 -m 0 /dev/mynew_vg/vol01
```

The -m option specifies the percentage reserved for the super-user, we can set this to 0 to use all the available space (the default is 5%).

![lvmconsole7](/images/lvmconsole7.jpg)

### Edit fstab to automatically mount partitions

For the filesystem to be automatically mounted, we should add an entry for it into the /etc/fstab file. This will mount the partitions for us when the computer boots up in the future.

```bash
$ sudo vim /etc/fstab
```

The entry should something like this below

![lvmconsole8](/images/lvmconsole8.jpg)

### Mount Logical Volumes

In order to use our new volumes, we’ll need to mount them. Don’t forget to also create the mount point first.

```bash
$ mkdir /foobar
$ sudo mount -a
```

![lvmconsole9](/images/lvmconsole9.jpg)

### Extend a logical volume

The biggest advantage of a logical volume is that it can be extended any time we are running out of space. For example, to increase the size of a logical volume and add other 800 MB of space, we can run this command:

```bash
$ sudo lvextend -L +800 /dev/mynew_vg/vol01
```

Notice in the screenshot below that the command doesn’t actually increase the size of the filesystem, but only that of the logical volume.

![lvmconsole10](/images/lvmconsole10.jpg)

To make the filesystem grow and use the added space we need to resize the filesystem with the following command.

```bash
$ sudo resize2fs /dev/mynew_vg/vol01
```

![lvmconsole11](/images/lvmconsole11.jpg)

On some systems, especially older ones, you may be required to unmount the volume and run e2fck before being able to extend it.

```bash
$ sudo umount /foobar
$ sudo e2fck -f /dev/mynew_vg/vol01
$ sudo resize2fs /dev/mynew_vg/vol01
```

### Remove a logical volume

The command **lvremove** can be used to remove logical volumes. We should make sure a logical volume does not have any valuable data stored on it before we attempt to remove it. Moreover, we should make sure the volume is not mounted.

```bash
$ sudo lvremove /dev/mynew_vg/vol02
```

![lvmconsole12](/images/lvmconsole12.jpg)

And that's it...
