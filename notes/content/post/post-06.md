---
title: "Environment and Shell Variables"
date: 2022-02-13T13:10:36+05:30
draft: false
disqus: false
tags: [Linux]
---

The shell can store temporary variables, called shell variables, containing the values of text strings. Shell variables are very useful for keeping track of values in scripts and some shell variables control the way the shell behaves.[for ex: the bash shell reads the **PS1** variable before displaying the prompt].

To assign a value to a shell variable, use the equal (=) sign.
For ex:

```bash
$ JAVA_HOME=/home/mordor/jdk1.8
```

Note: Don't put any spaces around = when assigning a variable.

To access this variable we use $JAVA_HOME. try running

```bash
$ echo $JAVA_HOME
$ /home/mordor/jdk1.8
```

An environment variable is like a shell variable, but it’s not speciﬁc to the shell. All processes on Unix systems have environment variable storage.

The main diﬀerence between environment and shell variables is that the operating system passes all of your shell’s environment variables to programs that the shell runs, whereas shell variables cannot be accessed in the commands that you run.

You assign an environment variable with the shell’s export command. For example, if you’d like to make the $JAVA_HOME shell variable into an environment variable, use the following:

```bash
$ JAVA_HOME=/home/mordor/jdk1.8
$ export JAVA_HOME

# Or you could use the bash shortcut
$ export JAVA_HOME=/home/mordor/jdk1.8
```

The **printenv** command is used to display all the set environment variables.

```bash
# for all the environment variables
$ printenv

# for a specific environment variable
$ printenv JAVA_HOME
```

To unset an environment variable

```bash
$ export JAVA_HOME=
    or
$ unset JAVA_HOME
```

Because child processes inherit environment variables from their parent, many programs read them for conﬁguration and options.

For example, you can put your favorite less command-line options in the LESS environment variable, and less will use those options when you run it. (Many manual pages contain a section labeled ENVIRONMENT that describes these variables.)

## System wide and Per-User environment variable settings
First we have to understand what login and non-login shells are and further whether they are interactive and non-interactive. 
`Login Shell` is the first process that executes under your user ID when you log in for an interactive session. The login process tells the shell to behave as a login shell with a convention: passing argument 0, which is normally the name of the shell executable, with a - character prepended (e.g. -bash whereas it would normally be bash)

When you log in on a text console, or through SSH, or with su -, you get an interactive login shell. When you log in in graphical mode (on an X display manager), you don't get a login shell, instead you get a session manager or a window manager.

It's rare to run a non-interactive login shell,

`Non-login Shell` is what we get when we start a shell in a terminal in an existing session. (screen, X terminal a shell inside another, etc)
Infact it is an interactive non-login shell.

When a shell runs a script or a command passed on its command line, it's a non-interactive, non-login shell. Such shells run all the time: it's very common that when a program calls another program, it really runs a tiny script in a shell to invoke that other program.

### System wide Configuration

- **/etc/environment** is a file meant for system-wide environment variable settings. It is not a script file, it just stores locale and path settings
- **/etc/profile and /etc/profile.d/x.sh** are global initialization scripts that get executed whenever a login shell is entered (from the console or through ssh), as well as by the DisplayManager when the desktop session loads.
  Note: /etc/profile is a configuration for the base files so it is generally not suggested to edit this file directly. It is advised to use a file in /etc/profile.d/ instead to set the system wide environment variables.

### User wide configuration

- For interactive non-login shells like the terminal, **/etc/bash.bashrc and ~/.bashrc** scripts gets executed.
- For interactive login shell, apart from /etc/profile and /etc/profile.d/x.sh scripts for system-wide configuration, either one of **~/.bash_profile, ~/.bash_login or ~/.profile** is executed, whichever is present in that order. These also source ~/.bashrc. Normally ~/.bashrc is the best place to add aliases and Bash related functions.
- When a shell doesn’t need any human intervention to execute commands, we call it a non-interactive shell. For example, when a script forks a child shell for the execution of commands, the child shell is a non-interactive shell. This shell doesn’t execute any startup file. It inherits environment variables from the shell that created it.

**Note: bash inherited the ~/.profile config from its predecessor /bin/sh. bash being backwards compatible will read from ~/.profile if it exists.**

A login shell can be created by

```bash
$ sudo su --login

$ echo $0
-bash
```

Login shells will have a - prepended before the name.

**Note: Mac Terminal always runs a login shell everytime, unlike ubuntu that opens a interactive non-login shell**
