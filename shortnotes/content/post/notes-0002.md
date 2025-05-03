---
title: "convert or reduce the size of an image in linux"
date: 2025-02-25T10:23:13+05:30
publishdate: 2025-02-25
lastmod: 2025-02-25
draft: true
tags: [Compression]
categories: [Linux]
---
Install **imagemagick**
~~~
sudo apt install imagemagick  
~~~
Reduce by degrading quality
~~~
convert img1.jpeg -quality 10% img1_small.jpeg
~~~
Reduce by size by pixels 
It is x (250ex250)
~~~
convert img1.jpeg -resize 250x250 img1_out.jpeg
~~~
Convert by image format
~~~
convert img1.png img1.jpeg
~~~
The `mogrify` command applies the operations on the original image 
~~~
mogrify -quality 10 *.jpeg 
~~~
