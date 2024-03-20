---
title: "select/poll/epoll"
date: 2022-12-30T18:17:55+05:30
publishdate: 2022-12-30
lastmod: 2022-12-30
draft: false
tags: ["networking"]
categories: []
---

##### select

![select](/images/select.png)

the fd_set variable is basically an array of 32-bit long, we have 32 of these for a total of 1024-bits which is FD_SETSIZE..
fd_set uses a bit for each file descriptor so for FD_SETSIZE it is 1024 fds. Now there are 3 sets, with the same fds for read, write and exception..so (4096 in total) suppose we are interested in fds 0, 4 and 900... we pass 901 as the max fd to select. select returns the ready fds (say in this case 4 and 900) back as the ready set. to check for the ready state, it has to loop through 900 descriptors to, which is O(n).
while the storage capacity is less, the efficiency is less as well

##### poll

the file descriptor is an integer itself. we send the list of descriptors we are interested in, and only those descriptors are returned
when the ready set is retrieved, it won't say which descriptors are ready, rather we have to loop through the file descriptors and
check for the event to see if we are interested in the event we are looking for.
an improvement rather than a checking using a bit mask through the complete list as in select.
The complexity (fds) is O(n) as it scans all the watched fds every time when a 'ready' event occurs,

##### epoll

In epoll, we get a list of fds containing only the actual events. epoll is basically O(1) as it doesn't do the linear scan over all the
watched descriptors.

The central concept of the epoll API is the epoll instance, an in-kernel data structure which, from a user-space perspective,can be
considered as a container for two lists:

- The interest list (sometimes also called the epoll set): the
  set of file descriptors that the process has registered an
  interest in monitoring.
- The ready list: the set of file descriptors that are "ready"
  for I/O. The ready list is a subset of (or, more precisely, a
  set of references to) the file descriptors in the interest
  list. The ready list is dynamically populated by the kernel as
  a result of I/O activity on those file descriptors.

While working with select and poll we manage everything on user space and we send the sets on each call to wait. To add another socket we need to add it to the set and call select/poll again.

Epoll\* system calls help us to create and manage the context in the kernel. We divide the task to 3 steps:

- create a context in the kernel using epoll_create
- add and remove file descriptors to/from the context using epoll_ctl
- wait for events in the context using epoll_wait

We can even add and remove file descriptor while waiting.

epoll is an event poll and can be used either as an Edge or Level Triggered interface and scales well to large numbers of watched
file descriptors.

Edge triggered: a call to epoll_wait will return only when a new event is enqueued with the epoll.
Level triggered: a call to epoll_wait will return as long as the condition holds.

Let’s take this example to understand the Level and Edge triggered better.

You have registered one pipe with epoll for reading. The application is able to read, only 1Kb data at a time. So, If you want to read 5Kb, you have to read 5 times.

Assume someone has written 5Kb data into the pipe and it is available to read. When the application calls epoll_wait, it will return immediately as the data is available. So, the application reads 1Kb data and again calls the epoll_wait. If you have configured epoll as edge-triggered, then epoll won’t return until someone writes (new events) again into the pipe even though 4Kb data is remaining for reading. Whereas in the level-triggered, epoll_wait will return immediately as 4Kb data is remaining to read. So, the application can call epoll_wait for another 4 times without the new events


#### select vs poll

##### Functionality

select() and poll() provide basically the same functionality. They only differ in the details:

select() overwrites the fd_set variables whose pointers are passed in as arguments 2-4, telling it what to wait for. This makes a typical loop having to either have a backup copy of the variables, or even worse, do the loop to populate the bitmasks every time select() is to be called. poll() doesn't destroy the input data, so the same input array can be used over and over.

poll() handles many file handles, like more than 1024 by default and without any particular work-arounds. Since select() uses bitmasks for file descriptor info with fixed size bitmasks it is much less convenient. On some operating systems like Solaris, you can compile for support with > 1024 file descriptors by changing the FD_SETSIZE define.

poll offers somewhat more flavors of events to wait for, and to receive, although for most common networked cases they don't add a lot of value.

Different timeout values. poll takes milliseconds, select takes a struct timeval pointer that offers microsecond resolution. In practice however, there probably isn't any difference that will matter.

Going with an event-based function instead should provide the same functionality as well. They do however often force you to use a different approach since they're often callback-based that get triggered by events, instead of the loop style approach select and poll encourage.

##### Speed

poll and select are basically the same speed-wise: slow.

They both handle file descriptors in a linear way. The more descriptors you ask them to check, the slower they get. As soon as you go beyond perhaps a hundred file descriptors or so - of course depending on your CPU and hardware

you will start noticing that the mere waiting for file descriptor activity and the following checking which file descriptor that it was, takes a significant time and becomes a bottle neck.

The select() API with a "max fds" as first argument of course forces a scan over the bitmasks to find the exact file descriptors to check for, which the poll() API avoids. A small win for poll().

select() only uses (at maximum) three bits of data per file descriptor, while poll() typically uses 64 bits per file descriptor. In each syscall invoke poll() thus needs to copy a lot more over to kernel space. A small win for select().

Going with an event-based function is the only sane option if you go beyond a small number of file descriptors. The libev guys did a [benchmarking] (http://libev.schmorp.de/bench.html) of libevent against libev and their results say clearly that libev is the faster one.

Compared to poll and select, any event-based system will give a performance boost already with a few hundred file descriptors and then the benefit just grows the more connections you add.

##### Portability

select - has existed for a great while and exists almost everywhere. A problem with many file descriptors is that you cannot know if you will overflow the bitmask as you can't check the file descriptor against FD_SETSIZE in a portable manner.

Many unix versions allow FD_SETSIZE to be re-defined at compile time, but Linux does not.

One quirk is that the include header required for the fd_set type varies between systems.

Some - but not all - systems modify the timeout struct so that on return from select, the program can know how long time actually passed. If you repeat select() calls, you need to init the timeout struct each loop!

poll - Not existing on older unixes nor in Windows before Vista. Broken implementation in Mac OS X at least up to 10.4 and earlier. Up to 10.3 you could check for a poll() that works with all arguments zeroed. The 10.4 bug is more obscure and I don't know of any way to detect it without checking for OS.

Lots of early implementations did poll as a wrapper around select().

poll's set of bits to return is fairly specific in the standards, but vary a lot between implementations.

##### complexity

All event-driven functions tend to make the code more complex, harder to follow and require more code to be written to accomplish the same task as the simple select and poll approaches do.
