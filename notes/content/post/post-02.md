---
title: "Python - Virtual Env"
date: 2022-02-08T11:55:24+05:30
draft: false
disqus: false
tags: [Python]
---
Virtual environments enable you to have an isolated space on your server for Python projects, ensuring that each of your projects can have its own set of dependencies that won’t disrupt any of your other projects.

Setting up a programming environment provides greater control over Python projects and over how different versions of packages are handled. This is especially important when working with third-party packages.

You can set up as many Python programming environments as you would like. Each environment is basically a directory or folder on your server that has a few scripts in it to make it act as an environment.

While there are a few ways to achieve a programming environment in Python, we’ll be using the **venv** module here, which is part of the standard Python 3 library. 

**Note:** - Latest versions of python3.x.x include **venv** but in case it is missing, then let’s install **venv** by typing:
```bash
sudo apt install -y python3-venv
```
Now,  choose which directory we would like to put our Python programming environments in, or create a new directory with mkdir, as in:
```bash
$ mkdir environments && cd environments
```
Now create an environment using,
```bash
$ python3 -m venv my_env
```
You can activate the virtual environment by using
```bash
$ source my_env/bin/activate
```
Now if you use pip to install the packages, the packages will be installed by default in the virtual environment.

**Note - Within the virtual environment, you can use the command python instead of python3, and pip instead of pip3 if you would prefer. If you use Python 3 on your machine outside of an environment, you will need to use the python3 and pip3 commands exclusively.**

It is always a good idea to upgrade pip first
```bash
$ pip install --upgrade pip
```
Install the module
```bash
$ pip install <module_name>
```
**Note: You don't have to use --user here, because we are already in a virtual environment**



You can deactivate the virtual environment by
```bash
$ deactivate
```



