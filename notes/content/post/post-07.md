---
title: "Useful Commands - grep, find, xargs"
date: 2022-02-13T13:30:31+05:30
draft: true
disqus: false
tags: [Linux]
---
## find
```bash
# Find file whose name is test.txt in the current directory
$ find . -name test.txt

# find file using name and ignoring case
$ find . -iname test.txt

# find .mkv files in the current directory (not sub-directories) only
$ find /home/mordor/Downloads/ -maxdepth 1 -name *.mkv

# find directories using name
$ find / -type d -name myfolder

# find php files in the directory
$ find . -type f -name "*.php"

# find all empty directories
$ find /tmp -type d empty

# find files without permission (777)
$ find / -type f ! -perm 777

# find read-only files
$ find / -perm /u=r

# find executable files
$ find / -perm /a=x

# find files whose size between 50m - 100mb
$ find / -size +50M -size -100M

# find specific files and delete
# find all mp3 files more than 10mb and delete
$ find / -type f -name *.mp3 -size +10M -exec rm {} \;
```
## grep
```bash


