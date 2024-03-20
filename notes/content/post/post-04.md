---
title: "wget"
date: 2022-02-09T17:16:44+05:30
draft: false
disqus: false
tags: [Linux]
---
## Differences
* wget's major strong side compared to curl is its ability to download recursively.
* wget is command line only. There's no lib or anything, but curl's features are powered by libcurl.
* curl supports FTP, FTPS, HTTP, HTTPS, SCP, SFTP, TFTP, TELNET, DICT, LDAP, LDAPS, FILE, POP3, IMAP, SMTP, RTMP and RTSP. wget supports HTTP, HTTPS and FTP.
* curl builds and runs on more platforms than wget

## wget usage

### Installation
```bash
$ sudo apt install wget
```

### Syntax
```bash
$ wget [options] [url]
```

## Examples

### Downloading 
```bash
# Download a file to the current directory
$ wget https://cdn.kernel.org/pub/linux/v4.x/linux-4.17.2.tar.xz

# for silent download use -q option
$ wget -q https://cdn.kernel.org/pub/linux/kernel/v4.x/linux-4.17.2.tar.xz

# Download a file with a different name
$ wget -O latest-hugo.zip https://github.com/gohugoio/hugo/archive/master.zip

# Download file to a specific directory
$ wget -P /mnt/iso http://mirrors.mit.edu/centos/7/isos/x86_64/CentOS-7-x86_64-Minimal-1804.iso

# Resume download
$ wget -c http://releases.ubuntu.com/18.04/ubuntu-18.04-live-server-amd64.iso

# Limit rate of transfer (k,m or g for kilo, mega, giga bytes)
$ wget --limit-rate=1m https://dl.google.com/go/go1.10.3.linux-amd64.tar.gz

# Download in background
$ wget -b https://download.opensuse.org/tumbleweed/iso/openSUSE-Tumbleweed-DVD-x86_64-Current.iso
```

### Download multiple files
```bash
$ wget -i linux-distros.txt

# linux-distros.txt

http://mirrors.edge.kernel.org/archlinux/iso/2018.06.01/archlinux-2018.06.01-x86_64.iso
https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/debian-9.4.0-amd64-netinst.iso
https://download.fedoraproject.org/pub/fedora/linux/releases/28/Server/x86_64/iso/Fedora-Server-dvd-x86_64-28-1.1.iso
```
### Download via FTP
```bash
$ wget --ftp-user=FTP_USERNAME --ftp-password=FTP_PASSWORD ftp://ftp.example.com/filename.tar.gz
```

### Download a whole website
```bash
$ wget --recursive https://www.baeldung.com
```

### Create mirror of website
```bash
$ wget -m https://example.com

# If you want to use the downloaded website for local browsing, you will need to pass a few extra arguments to the command above.
# The -k option will cause wget to convert the links in the downloaded documents to make them suitable for local viewing. The -p option will tell wget to download all necessary files for displaying the HTML page
$ wget -m -k -p https://example.com
```

