---
title: "curl"
date: 2022-02-09T15:34:39+05:30
draft: false
disqus: false
tags: [Linux]
---
**curl (Client URL)** is a command-line utility for sending and receiving data from a server.
curl supports many protocols like Http/Https, FTP, SFTP, TELNET etc.

### Installation
```bash
$ sudo apt update
$ sudo apt install curl
```

### How to use curl
```bash
$ curl [options] [URL...]
```

## Examples
### GET Request
curl performs the GET request by default. 
```bash
# Retrieve example.com page without the headers
# if no protocol is specified (Http is assumed)
$ curl example.com

# Retrieve example.com with the headers
$ curl -i example.com

# Retrieve only the headers
$ curl -I example.com

# Send header along with GET request
$ curl -H 'Content-Type: application/json' http;//localhost:4000/login

# Send multiple headers
$ curl https://reqbin.com/echo/get/json
   -H "Accept: application/json"
   -H "Authorization: Bearer {token}"

# Retrieve a page which has moved permanently (301 Moved Permanently)
# Note: curl google.com (non-www version redirects to www version)
# so we have to use -L option to follow any redirect till we reach
# the final destination
$ curl -L google.com

# Header with redirect 
$ curl -L -I google.com
```

for requests other than GET need to use **-X or --request flag**
```bash
$ curl -X [method] [more options] [URI]
```

### Post Request
```bash
$ curl -X POST [more options] [URI]

# Sending data (-d or --data option)
$ curl -X POST -d "p1=value1&p2=value2" https://httpbin.org/post
    or
$ curl -X POST -d "p1=value1" -d "p2=value2" https://httpbin.org/post

# Uploading files (-F which uses multipart/form-data or --form which
# uses form content type. @ prefix is required)
$ curl -X POST -F 'file=@/home/mordor/Desktop/file' https://httpbin.org/post

# Upload multiple files
$ curl -X POST -F 'file1=@/home/mordor/Desktop/file1;file2=@/root/Desktop/file2'
    or

$ curl -X POST -F 'file1=@/home/mordor/Desktop/file1' -F 'file2=@/root/Desktop/file2'
```
Modify the header (-H or --header flag), in cases like sending a JSON object
```bash
$ curl -H 'Content-Type: application/json' -X POST -d '{"data": "18-may-2022", "name": "Edward" }' https://httpbin.org/post
```
**Note: Use ' (apos) as shown above in the json post request.
Reason being, anything in-between single quotes is taken literally by bash,
anything in-between double quotes is interpolated (inserted/replaced)**
### PUT request
```bash
$ curl -X PUT -d 'arg1=val1' -d 'arg2=val2' https://httpbin.org/put

# Raw JSON
$ curl -X PUT -H 'Content-Type: application/json' -d '{"name":"edward"}' https://httpbin.org/put
```

### DELETE request
```bash
$ curl -X DELETE https://httpbin.org/delete

# We can look at the status code from the response header when we send a delete request. (200 means the delete was successful)
$ curl -I -X DELETE https://httpbin.org/delete
```
### Downloading Files
Lowercase **-o or --output** save the file with a predefined name
```bash
$ curl -o vue-v2.js https://cdn.jsdelivr.net/vue/dist/vue.js

# In the case of redirect (301 Moved Permanently)
$ curl -L -o vue-v2.js https://jsdelivr.net/vue/dist/vue.js
```
Uppercase **-O (Big Oh) or --remote-name** will use the original name while saving
```bash
$ curl -O https://cdn.jsdelivr.net/vue/dist/vue.js

# In case of redirect (301 moved permanently)
$ curl -LO https://jsdelivr.net/vue/dist/vue.js

# silent mode 
$ curl -SO https://jsdelivr.net/vue/dist/vue.js
    or
$ curl -SLO https://jsdelivr.net/vue/dist/vue.js
```
To download multiple files
```bash
$ curl -O http://mirrors.edge.kernel.org/archlinux/arch.iso
       -O http://mirrors.debian.org/current/debian.iso
```

Resuming a download, say if you are downloading file using
```bash
curl -O http://releases.ubuntu.com/18.04/ubuntu-18.04-live-server-amd64.iso
```
and your connection drops, you can resume using (-C - ) option
```bash
curl -C - -O http://releases.ubuntu.com/18.04/ubuntu-18.04-live-server-amd64.iso
```
Limiting Transfer rate by using --limit-rate option. The value can be expressed in bytes or (Kb, mb or gb) using k, m or g
```bash
curl --limit-rate 1m -O https://dl.google.com/go/go1.10.3.linux-amd64.tar.gz
```
### FTP
```bash
# To get files using ftp
$ curl -u abhi:myPassword 'ftp://abc.com/hello.pdf' -o hello.pdf

# To upload file (trailing / is required)
$ curl -T "img.png" ftp://ftp.example.com/upload/
```

## Other Examples
Check if the website supports HTTP/2
```bash
$ curl -I --http2 -s https://linuxize.com/ | grep HTTP
```
Run curl in silent mode without error messages and progress meter
```bash
$ curl -I --http2 -s https://linuxize.com/ | grep HTTP
```

Change the User-Agent: Sometimes when downloading a file, the remote server may be set to block the Curl User-Agent or to return different contents depending on the visitor device and browser.In situations like this to emulate a different browser.

use the **-A option**,  For example to emulate Firefox 60 you would use:
```bash
curl -A "Mozilla/5.0 (X11; Linux x86_64; rv:60.0) Gecko/20100101 Firefox/60.0" https://getfedora.org/
```

