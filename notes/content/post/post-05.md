---
title: "Terminal, TTY, Shell, Virtual Console"
date: 2022-02-13T12:22:22+05:30
draft: false
tags: [Linux]
disqus: false
---
**Note:--- First read "Articles/Linux Terminal and Console Explained for beginners.html" before reading this**

**Teletype/TTY/Teleprinter/Teletypewriter** was an electromechanical device that was interfaced with mainframe computers and acted as dumb terminals back in the day. 
It had an input interface(like a keyboard) and an output interface (display or sheet of paper). It was connected to the computer via two logical channels and all it does is
* send the keystrokes down the first line
* read the sent line and print them on a sheet of paper

![teletype](/images/teletype.jpg)

These two channels are connected to the computer using a serial cable plugged into a UART (Universal asynchronous receiver and transmitter)

The computer has UART driver to read for the hardware device. The sequence of characters is passed on to a TTY driver which applies line discipline. 
The line discipline is in charge of converting special characters (like end of line, backspaces) and echoing what has been received back to the teletype, so that the user can see what has been typed.
It also buffers the characters until an enter is pressed, when it sends the buffered data to the foreground process for the session associated with the TTY.
As a user, you can execute several processes in parallel, but only interact with one at a time, letting the others working (or waiting) in the background.

The whole stack as defined above is called a **TTY device**.
The foreground process is a computer program called **Shell**.

### What's a shell?
Shells are user space applications that use the kernel API in just the same way as it is used by other application programs. A shell manages the user–system interaction by prompting users for input, interpreting their input, and then handling an output from the underlying operating system (much like a read–eval–print loop, REPL).
For example, if the input is 'cat file | grep hello', bash will interpret that and figure it needs to run the program cat passing 'file' as parameter and pipe the output to grep.

It also controls programs execution (feature called job control): kills them (CTRL + C), suspends them (CTRL + Z), sets them to run in the foreground (fg) or in the background (bg).

They can also run in non interactive mode, via script which contains a sequence of commands.

**Bash, Zsh, Fish and sh** are all different flavors of shells.

### What's a terminal emulator?
Let's move to more recent times. Computers started becoming smaller and smaller, with everything packed in one single box.
For the first time the terminal was not a physical device connected via UART to the computer. The terminal became a computer program in the kernel which would send characters directly to the TTY driver, read from it and print to the screen.

That is, the kernel program would emulate the physical terminal device, thus the name terminal emulator. Infact our system emulates several terminals and the OS allows you to switch from one terminal to another by simple press of keys (Ctrl+Alt+F1 to Ctrl+Alt+F7). This feature is called Virtual terminals or Virtual Consoles.

Don't get fooled by the word emulator. A terminal emulator is as dumb as the physical terminals used to be, it listens for events coming from the keyboard and sends it down to the driver. The difference is that there is no physical device or cable which is connected to the TTY driver. Instead a video terminal is emulated in software and rendered to a VGA display. 
![tty](/images/tty0.jpg)

### How do I see a terminal emulated TTY?

If you run a Linux OS on your machine press Ctrl+Alt+F1. You'll get a TTY emulated by the kernel! You can get other TTYs by pressing Ctrl+Alt with the function keys from (F2 to F6). By pressing Ctrl+Alt+F7 you'll get back to the GUI (X session).

Let's recap the main concepts so far:

* Teletypes (TTY) is physical electromechanical originally designed for telegraphy, then adapted to send input and get output from mainframes
* A Teletype can be emulated by a computer program running as a module in the kernel
* A terminal emulated TTY is also called as a virtual console or Virtual terminal..

### What's a pseudo terminal (PTY)?
It's Teletype emulated by a computer program running in the user land.
Compare that with a TTY: the difference is where the program runs; it's not a kernel program but one that runs in the user land.

The main reason why PTY exists is to facilitate moving the terminal emulation into user land, while still keeping the TTY subsystem (session management and line discipline) intact.

A pseudo-terminal is normally called as terminal. I use alacritty as my terminal. 

### How does TTY work? (High level)
Terminal emulator (or any other program) can ask the kernel for a pair of characters files (called PTY master and PTY slave).
On the master side you have the terminal emulator, while on the slave side you have a Shell.
Between master and slave sits the TTY driver (line discipline, session management, etc.) which copies stuff from/to PTY master and slave.

Let's see what happens when...
you type something in a terminal emulator in the user land like XTerm or any any other application you use to get a terminal.

Usually we say we open 'the terminal' or we open 'a bash', but what it actually happens is:

* a GUI which emulates the terminal starts (like the Terminal or Xterm UI application).
* it draws the UI to the video and requests a pty from the OS.
* launches bash as subprocess
* The std input, output and error of the bash will be set to be the pty slave.
* XTerm listens for keyboard events and sends the characters to the pty master
* The line discipline gets the character and buffers them. It copies them to the slave only when you press enter. It also writes back its 
input to the master (echoing back). Remember the terminal is dumb, it will only show stuff on the screen if it comes from the pty master.
Thus, the line discipline echoes back the character so that the terminal can draw it on the video, allowing you to see what you just typed.
* When you press enter, the TTY driver (it's 'just' a kernel module) takes care of copying the buffered data to the pty slave
* bash (which was waiting for input on standard input) finally reads the characters (for example 'ls -la'). Again, remember that 
bash standard input is set to be the PTY slave.
* At this point bash interprets the character and figures it needs to run 'ls'
* It forks the process and runs 'ls' in it. The forked process will have the same stdin, stdout and stderr used by bash, which is the PTY slave.
* ls runs and prints to standard output (once again, this is the pty slave)
* the tty driver copies the characters to the master(no, the line discipline does not intervene on the way back)
* XTerm reads in a loop the bytes from the pty master and redraws the UI

I think we made it! That's roughly what happens when we run a command in a terminal emulator. The drawing should help consolidate the workflow:
![teletypeworkflow](/images/teletypeworkflow.jpg)

As an experiment, run the 'ps' command in new instance of a terminal.
If you did not run any process yet, you will see that the only two programs associated with the terminal are 'ps' and 'bash'.
'ps' is the program we just started and 'bash' was started by the terminal. 'pts/0' which you see in the results is the PTY slave we talked about.

```bash
$ ps
PID     TTY         TIME    CMD
1913    pts/0   00:00:00    ps
1683    pts/0   00:00:00    bash
```
**To summarize The word virtual console refers to an application that simulates a physical terminal while the word terminal refers to an application that allows us to access and use the shell. Two common terminal devices are /dev/tty1 (the ﬁrst virtual console) and /dev/pts/0 (the ﬁrst pseudoterminal device).**


Now let's see what happens when...
you start a program that reads from the standard input from a terminal emulator. The program can be as simple as reading from standard input and writing it to the standard output, like the following.
```bash
package main

import (
    "bufio"
    "fmt"
    "os"
)

func main() {
  reader := bufio.NewReader(os.Stdin)
  fmt.Print("Enter text: ")
  text, _ := reader.ReadString('\n')
  fmt.Println(text)
}

$ go build -o simple-program
$ ./simple-program
Enter text: 
```

Try to write something while the program is awaiting for input, but instead of pressing enter press 'CTRL + W'.
You would see that the word you wrote gets deleted. Why?
'Ctrl + W' are the characters assigned to a line discipline rule called werase. Look back at the drawing. Characters typed get send to the PTY master and from there to the TTY driver which implements the line discipline. When the line discipline reads the characters 'CTRL + W', it removes the last word from its internal buffer and send to the PTY master the instructions to set the cursor back N position (where N is the length of the word) and to delete the characters from the display along the way
(we shall see how these instructions look like in a moment).

Try the same experiment, but this time instead of typing characters, press the arrow keys.
What are those weird characters ^[A^[B^[C^[D?
As we said, the terminal is simply sending keystrokes to the master. But what about keys that do not have a character representation,like our arrows?
When that happens, the terminal encodes them using multiple characters; for example the up arrow is encoded with ^[A, where ^[ is called escape sequence.
Thus, what happens when you press the arrows key is exactly what happens when you press any other key, it's just that what it gets echoed back looks weird because it was encoded that way when it was sent to the PTY master.

I hear you saying...but wait, when I press the arrows keys in the terminal with no program running I get the bash history!
This is because the ^[A ends in the bash program which interprets them as a request to get the current entry in the job history. It prints to standard out the code to clear the current line (to delete whatever was echoed so far, including the ^[A characters for the Up key) and then print the bash history line. I guess we do not see the encoded character because everything happens really fast.

The takeaway here is that the line discipline does not handle keys like Up, Left, Down, Right.

### How can a program control the terminal
A shell, the TTY driver and a program we write can instruct the terminal to do stuff for us: move the cursor back or one line down, print the next line read and clear screen.
The way for programs to control the terminal is standardized by the ANSI escape codes. When the terminal reads them from the pty master will perform the operation associated with the code.

** Note: The controlling terminal of the current process is denoted by /dev/tty **

### Want to change the color of the text from your program?
Just print to standard out the ANSI escape code for coloring the text.
Standard out is the PTY slave, TTY driver copies the character to the PTY master, terminal gets the code and understands it needs to set the color to print the text on the screen. Voilà'!

This is a simple program which instruct the terminal emulator to print the line using the red color. As you'll see it's as simple sending to standard out the ANSI code for changing the color to red (\033[1;31m), the string we want to write and the ANSI code to reset the color (\033[0m).

```bash
package main

import "fmt"

const redColor = "\033[1;31m%s\033[0m"

func main() {
    fmt.Printf(redColor, "Error")
    fmt.Println("")
}
```
ANSI code could be a whole different article, by itself, but I am going to stop here..

## Additional info... Regarding Virtual consoles..
A linux system has 63 virtual consoles usually called (/dev/ttyn) with 1<=n<=63. You can check it in /dev folder. The current console is called /dev/tty0 or /dev/console. Linux doesn't have the mechanism to create consoles on demand. But the 63 consoles are not always active. you need to activate ttyn using (openvt or getty or X server or ask init) in order to switch to it with CTRL+ALT+F(n).

Different Linux flavors offer different number of virtual consoles. For instance, RHEL provides six virtual consoles while Ubuntu provides seven virtual consoles. Virtual consoles are always mentioned along with one physical console (also known as default console). So, the actual number of virtual consoles remains one less than the total number of consoles. For example, in RHEL and Ubuntu the number of actual virtual consoles are 5 (six - one) and 6 (seven - one) respectively.


### Default working environment 
Linux provides two types of working environment; GUI (Graphic User Interface) and CLI (Command Line Interface). GUI contains the Desktop environment and allows user to access several sub-shells simultaneously. CLI contains only command line interface and allows user to access a single shell at a time.

Linux allows us to select or skip GUI environment during the installation. If it is selected, GUI is installed in physical console and CLI is installed in virtual consoles. If it is skipped, CLI is installed in both physical and virtual consoles.

Whether you install GUI or not, in virtual consoles always CLI environment is installed. As they simulate the physical terminals which were designed only to access the CLI environment. Many of these virtual consoles may be occupied by a getty process running a login prompt. 

Just like the total number of consoles, sequence of physical console and virtual console is also Linux flavor specific. For example, in RHEL GUI is available as the first console while in Ubuntu it is available as the last console. 


