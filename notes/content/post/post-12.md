---
title: "Direct I/O and ioctl"
date: 2022-02-15T20:14:11+05:30
draft: false
disqus: false
tags: [Linux]
---
## Direct I/O
Direct I/O is a way to avoid entire caching layer in the kernel and send the I/O directly to the disk. Overall, this makes I/O slower and does not let Linux do various optimizations that it usually does. Also, it
introduces some constraints on memory buffer that being used for the I/O. Yet sometimes it is inevitable.

when you write some information to the disk, it doesn’t get there immediately. In Linux especially, the
kernel tries to cache write requests in memory as much as it can. This means that in addition to writing the data to the disk,
kernel keeps it in memory. Consecutive read request to the same place on the disk will be much faster because there’s no need
to read the information from slow disk – it is already in memory. On the other hand, the information goes to the hard-disk only
after some period of time (short though) or when the system runs out of memory. In the meantime, Linux reports that data has
been written, despite it is not yet on the disk.
This causes several interesting phenomena that you may have noticed. For example Linux machines with little memory, even
though not all memory is in use, do use disk much more than machines with more memory. There are several reasons for it.
One of them is because kernel doesn’t have enough memory to cache information from the disks and as a result read requests
rarely hit the cache and more often go to the disk.
Want to see exactly how Linux reports successful write requests before data actually lands on the disk? Try writing
some information to floppy disk and see how fast it is in Linux. The truth is that it’s still frustratingly slow. Linux
just makes it look like a fast device.
Sheer thought that Linux doesn’t write the data to the disk, despite it says it did, may be pretty scary. But it shouldn’t really.
First of all, if its not very busy it does write the data to the disk as soon as possible. Second, Linux does excellent job avoiding
various problems and even if something bad happens, it is pretty good at recovering lost data. The way Linux works is
excellent for 99% of users. This approach improves performance in various ways and makes the system more healthy and
stable.
However, some folks out there are not happy with this situation. Some software systems cannot work the way Linux works.
One of the examples are so called clustered file-systems – file-systems that are spread among multiple servers for redundancy
purposes. Such systems need a way to know that data has been written to the disk for real, not just being cached. Also, such
systems want to make sure that reads hit disk and not OS cache.
This is where direct I/O becomes handy. 
Want to try it yourself? dd, I/O Swiss army knife, has an option called direct. It tells dd to do direct I/O instead of regular I/O.
Another option for doing direct I/O is writing your own program that opens files with O_DIRECT flag (see open(2) for
details).

### Direct I/O using Python
Doing file I/O of any kind in Python is really easy. You can start with plain open() and friends, working with Python’s file
objects. by the way, Python’s open() resembles C‘s fopen() so closely that I can’t stop thinking that open() may be based on
fopen().
When its not enough, you can always upgrade to open() and close() from os module. Opening man page on open() (the system
call – open(2)) reveals all those O_something options that you can pass to os.open(). But not all of them can be used in Python.
For example, if you open a file with O_DIRECT and then try to write to it, you will end up with some strange error message.
```bash
>>> import os
>>> f = os.open('file', os.O_CREAT | os.O_TRUNC | os.O_DIRECT | os.O_RDWR)
>>> s = ' ' * 1024
>>> os.write(f, s)
Traceback (most recent call last):
File "", line 1, in
OSError: [Errno 22] Invalid argument
>>>
```
Invalid argument?. What invalid argument? Hey there’s nothing wrong with those arguments…
Reading open(2) man page further reveals that working with O_DIRECT requires that all buffers used for I/O should be
aligned to 512 byte boundary. But how can you have a memory buffer aligned to 512 bytes in Python?
Apparently, there’s a way. Python comes with a module called mmap. mmap() is a system call that allows one to map portion
of file into memory. All writes to memory mapped file, go directly to file despite it looks like you’re working with plain
memory buffer. Same with reads.

There’s one interesting thing about mmap. It works with granularity of one memory page – 4kb that is. So every memory
mapped buffer is naturally memory aligned to 4kb, thus to 512 byte boundary too. But hey, shouldn’t mmap map files?
Well, apparently mmap can be used for memory allocations. I.e. specifying -1 as file descriptor does just that – allocates RAM,
as much as you tell it. So, this is what we do:
``` bash
>>> import os
>>> import mmap
>>>
>>> f = os.open('file', os.O_CREAT | os.O_DIRECT | os.O_TRUNC | os.O_RDWR)
>>> m = mmap.mmap(-1, 1024 * 1024)
>>> s = ' ' * 1024 * 1024
>>>
>>> m.write(s)
>>> os.write(f, m)
1048576
>>> os.close(f)
>>>
```
**Note** that mmap memory buffer object behaves like a file. I.e. you can write into the buffer and read from it – like I do in line
8.

## ioctl
ioctl (an abbreviation of input/output control) is a system call for device-specific input/output operations and other operations which cannot be expressed by regular system calls. It takes a parameter specifying a request code; the effect of a call depends completely on the request code. Request codes are often device-specific.

Modern operating systems support diverse devices, many of which offer a large collection of facilities. Some of these facilities may not be foreseen by the kernel designer, and as a consequence it is difficult for a kernel to provide system calls for using the devices.

To solve this problem, the kernel is designed to be extensible, and may accept an extra module called a device driver which runs in kernel space and can directly address the device. An ioctl interface is a single system call by which userspace may communicate with device drivers. Requests on a device driver are vectored with respect to this ioctl system call, typically by a handle to the device and a request number. The basic kernel can thus allow the userspace to access a device driver without knowing anything about the facilities supported by the device, and without needing an unmanageably large collection of system calls.
