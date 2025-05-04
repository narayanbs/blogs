---
title: "View portion of a file in command line"
date: 2025-05-04T19:30:03+05:30
publishdate: 2025-05-04
lastmod: 2025-05-04
draft: true
tags: [command line]
categories: [linux]
---
`sed -n 'start,endp;' filename`  

~~~
ex:
$ sed -n '12,20p;' deadline.txt
~~~
If we do this on a very large file, we should add a quit command on the next line
to prevent sed from scanning the whole file  

`sed -n 'start,endp;end+1q' filename`
~~~
ex:
$ sed -n '12,20p;21q' deadline.txt
~~~
