---
title: "Merge mutliple mp3 files into a single file"
date: 2025-07-27T21:32:36+05:30
publishdate: 2025-07-27
lastmod: 2025-07-27
draft: true
tags: [mp3]
categories: [ffmpeg, mp3]
---

- Create a text file (say, `files.txt`) specifying each input file as shown below

```
file 'input1.mp3'
file 'input2.mp3'
file 'input3.mp3'
```

Run ffmpeg with the following command

```
ffmpeg -f concat -safe 0 -i files.txt -c copy output.mp3

```
