---
title: "Process group, job control shell, session"
date: 2022-02-16T16:47:18+05:30
draft: false
disqus: false
tags: [Linux]
---
Each program executed by the shell is started in a new process. For ex: the following creates 3 processes to execute the pipeline of commands.
```bash
$ ls -l | sort -k5n | less
```
### Process groups and Shell Job Control
All major shells provide an interactive feature called **job control**, which allows the user to simultaneously execute and manipulate multiple commands or pipelines. 
In job-control shells, all of the processes in a pipeline are placed in a single **process group or job**.
In the case of a simple command, a new process group containing a single process is created. Each process in the process group has the same process group identifier, which is the same as the process ID of one of the processes in the group, termed the process group leader.
The kernel allows for various actions, notably the delivery of signals, to be performed on all members of a process group. Job-control shells use this feature to allow the user to suspend or resume all of the processes in a pipeline.

### Sessions, Controlling Terminal and Controlling Processes
A **session** is a collection of process groups ( jobs). All of the processes in a session have the same session identifier. A session leader is the process that created the session, and its process ID becomes the session ID.

Sessions are used mainly by job-control shells. All of the process groups created by a job-control shell belong to the same session as the shell, which is the session leader. Sessions usually have an associated controlling terminal. 

The **controlling terminal** is established when the session leader process first opens a terminal device. For a session created by an interactive shell, this is the terminal at which the user logged in. A terminal may be the controlling terminal of at most one session. 
As a consequence of opening the controlling terminal, the session leader becomes the controlling process for the terminal. The controlling process receives a SIGHUP signal if a terminal disconnect occurs (e.g., if the terminal window is closed).

At any point in time, one process group in a session is the foreground process group ( foreground job), which may read input from the terminal and send output to it. If the user types the interrupt character (usually Control-C) or the suspend character (usually Control-Z) on the controlling terminal, then the terminal driver sends a signal that kills or suspends (i.e., stops) the foreground process group. 
A session can have any number of background process groups (background jobs), which are created by terminating a command with the ampersand ( & ) character.

Job-control shells provide commands for listing all jobs, sending signals to jobs,and moving jobs between the foreground and background.

### Example
```bash
$ ls -l | grep script
-rwxr-xr-x 1 ubuntu users 18 Aug  2 21:44 myscript.sh

# Pausing a current job (find operation) using (ctrl+z)
$ find . -name "*.java"
./code/com/my/package/Main.java
^Z
[1]+  Stopped                 find . -name "*.java"

# Resume the find operation, we can use the fg command
$ fg
find . -name "*.java"
./code/com/my/package2/Util.java

# Job control, let's start two long running jobs and press ctrl+z
$ make -j4
^Z
[1]+  Stopped                 make -j4
$ find . -name "*.java"
^Z
[2]+  Stopped                 find . -name "*.java"

# list the jobs
# The jobs command marks the last paused job with the + sign and the immediate previous stopped job with the – sign. 
# If we use fg and other job commands without a job number, the last paused job is implied.
$ jobs
[1]-  Stopped                 make -j4
[2]+  Stopped                 find . -name "*.java"

# Resume a specific job using its job number
$ fg %1
make -j4

# Resume a job in the background
$ bg %1
[1]+ make -j4 &

# As with fg, we can omit the job number in the bg command to resume the last paused job in the background
$ bg
[2]+ find . -name "*.java" &

# Start a job in the background
$ find . -name "*.java" &
[1] 1726
```
Sometimes when we try to exit the shell by pressing Ctrl+D or by using logout, we may receive an error message:
```bash
$ logout
There are stopped jobs.
```
When bash shows this message, it also prevents logout.

This is because bash doesn’t allow us to exit the shell while there are paused jobs. The current shell’s process manages its jobs. When we pause a job, it’s left in an incomplete state. If we exit the shell with jobs paused, we might lose some critical data.
So, we need to take care of these paused jobs before we can exit the shell.

To decide what to do with our jobs, let’s list them:
```bash
$ jobs
[1]-  Stopped                 python
[2]+  Stopped                 find . -name "*.java" > javafiles.txt
```
Depending on these jobs, we may wish to keep them running or kill them.

We’ve already seen how to keep a job running in the background:
```bash
$ bg %2
[2]+ find . -name "*.java" > javafiles.txt &
```
As we earlier noted about background jobs, this job can still output to the shell while running in the background. If we exit the shell now, the job might terminate anyway if it tries to output to the shell as the shell doesn’t exist anymore. It can also get killed when it receives certain signals.

To avoid this, we can remove the background job from the current shell using the disown command:
```bash
$ disown %2
$ logout
```
This will make sure the background job keeps running in the background even after the shell exits.

Alternatively, we can kill a job we don’t need by using the kill command:
```bash
$ kill %1
[1]+  Stopped                 python

$ logout
```
kill allows us to use %1 as the job number, though it is also commonly used to terminate processes by their process id.
