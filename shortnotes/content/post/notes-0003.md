---
title: "Getting list of songs from youtube playlist"
date: 2025-05-04T12:22:40+05:30
publishdate: 2025-05-04
lastmod: 2025-05-04
draft: true
tags: []
categories: []
---
Use either  

`yt-dlp --print title <youtube playlist url>`  

or  

`yt-dlp --get-filename -o "%(title)s" <youtube playlist url>`

For ex:
* `yt-dlp --print title https://www.youtube.com/playlist?list=PLLttXs8RB7WeqXK0XJymssEb9-q1Vd4k_` > playlist.txt

* `yt-dlp --get-filename -o "%(title)s" https://www.youtube.com/playlist?list=PLLttXs8RB7WeqXK0XJymssEb9-q1Vd4k_`
