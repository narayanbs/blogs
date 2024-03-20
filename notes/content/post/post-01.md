---
title: "Python - Pip"
date: 2022-02-08T10:32:54+05:30
draft: false
tags: [Python]
disqus: false
---
Ubuntu comes with python3 pre-installed. You can check the version
```bash
$ python3 -V
```

### Before we Begin
Python comes in two flavors Python 2 and Python 3. Starting from Ubuntu 20.04, Python 3 is included in the base system installation and Python 2 is available from the Universe repository.

Python 3 packages are prefixed with **python3-** and Python 2 packages with **python2-**


Pip "Pip installs packages" is a standard package manager for python. It is a tool for installing python packages from Python Package Index (PyPi) and other package indexes. 
Check if pip is installed on your system
```bash
$ pip --version
    or
$ pip3 --version
```

pip is a softlink for a particular installer. On systems that had python2 as the default, pip would install python2 packages.So if you wanted to install python3 packages,  you had to install pip3 as the package manager.

Now from 20.04, since python3 is the default, pip would automatically chose the python3 packages. If you installed python from the source or with an installer from python.org, then pip would have already been installed. Other wise on debian/ubuntu, to install pip for python 3 we use
```bash
$ sudo apt update
$ sudo apt install python3-pip

$ pip3 --version
```
You can install packages using 
```bash
$ python3 -m pip install --user python_package-name
    or
$ pip3 install --user python_package_name
```
### Note:
If you use **–-user** option, it installs the package for the logged in user i.e. you without needing sudo access. The installed python software is available only for you and other users on your system  cannot use it

If you remove the **–-user** option, the package will be installed system wide and it will be available for all the users on your system. You’ll need sudo access in this case.

```bash
$ sudo pip3 install python_package_name
```

## Pip Commands 
### To search for packages
```bash
$ python3 -m pip search <search_string>
    or
$ pip3 search <search_string>
```

### To remove a package
```bash
$ python3 -m pip uninstall <package_name>
    or
$ pip3 uninstall <package_name>
```

## Some additional info (for curiosity)
##### pipx
pipx is a tool that helps you install end-user application in isolated virtual environments. It is similar to npx in Node.  
Unlike pip that installs libraries and applications with no environment isolation, pipx creates a virtual environment and installs the end-user 
application with all its dependencies. This provides isolated environments, at the same time those packages
act as if they were installed globally. You don't need to activate virtualenv to run them.
Applications like yt-dlp can be installed used pipx. 
ex:
~~~bash
  $ pipx install yt-dlp
~~~
Behind the scenes pipx creates a folder structure inside ~/.local/pipx/ to contain the virtual environments for each installed application. 
The executables are linked into ~/.local/bin/. Add this directory manually to your $PATH or issue the pipx ensurepath.


##### pip --user
With Python 2.6 came the “user scheme” for installation, which means that all Python distributions support an alternative install location that is specific to a user. The default location for each OS is explained in the python documentation for the **site.USER_BASE** variable. This mode of installation can be turned on by specifying the **--user** option to pip install.

```bash
>>>import site
>>>site.USER_BASE
/home/mordor/.local/

python3 -m pip install --user SomePackage
```
Then it is installed in **/home/mordor/.local/SomePackage**

		
To install **“SomePackage”** into an environment with **site.USER_BASE** customized to **‘/myappenv’**, do the following:		
```bash
export PYTHONUSERBASE=/myappenv
python3 -m pip install --user SomePackage
```
Then it is installed in **/myappenv/SomePackage**
