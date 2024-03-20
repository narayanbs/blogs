---
title: "Linux Compression Utilities - tar, xz, gzip, bzip2, zip, zcat"
date: 2022-02-15T08:14:16+05:30
draft: false
disqus: false
tags: [Linux]
---
### tar
**tar** is an archiving tool. In its purest form, it is used to bundle a bunch of files into a single archive which can later be compressed using a suitable compression tool. 
tar also has the option to compress an archive using gzip or bz2 compression.
```bash
# create a compressed archive with files or folder
$ tar -cvf archive.tar file1 file2 file3
$ tar -cvf archive.tar folder/
Note: (c=create, v=verbose, f=file)

# Show all files of an archive
$ tar -tvf archive.tar

# extract an archive
$ tar -xvf archive.tar
Note: (x=extract)

# extract an archive into a specific directory
$ tar -xvf archive.tar -C /target/directory

# extract only specific files from an archive
$ tar -xvf archive.tar file1 file2 -C /target/directory
```
tar supports vast range of compression programs such as gzip, bzip2, lzip, lzma, lzop, xz and compress.

Xz is a popular algorithm for compressing files based on the LZMA algorithm. By convention, the name of a tar archive 
compressed with xz ends with either .tar.xz or .txz.
```bash
# create xz archive
$ tar -Jcvf <archive.tar.xz> <files>

$ tar -Jcvf archive.tar.xz file1 file2 file3
$ tar -Jcvf archive.tar.xz folder/

# Extracting tar.xz File
$ tar -xvf archive.tar.xz

# listing contents of tar.xz file
$ tar -tvf archive.tar.xz

# extract compressed archive in a specific folder
tar -xvf archive.tar.xz -C /home/linuxize/files

# extract specific files from a compressed archive
$ tar -xvf archive.tar.xz file1 file2

# download and extract an archive 
$ curl -SL https://apache.org/tomcat/apache-tomcat-10.1.16-fulldocs.tar.xz | tar -Jx -C .
```
tar auto-detects compression type and extracts the archive. The same command can be used to extract tar archives 
compressed with other algorithms, such as .tar.gz or .tar.bz2 .

```bash
# Create compressed archive with gzip
$ tar -zcvf archive.tar.gz file1 file2 file3
$ tar -zcvf archive.tar.gz folder/
Note: (z=gzip)

# list files compressed with gzip
$ tar -tvf archive.tar.gz

# extract compressed archive with gzip
$ tar -xvf archive.tar.gz

# extract compressed archive with gzip into a specific directory
$ tar -xvf archive.tar.gz -C /target/directory

# download and extract an archive 
$ curl -SL https://apache.org/tomcat/apache-tomcat-10.1.16-fulldocs.tar.gz | tar -zx -C .

```
```bash
# create compressed archive with bz2
$ tar -jcvf archive.tar.bz2 file1 file2 file3
$ tar -jcvf archive.tar.bz2 folder/
Note: (j=bz2)

# list files compressed with bz2
$ tar -tvf archive.tar.bz2

# extract compressed archive with bz2
$ tar -xvf archive.tar.bz2

# extract compressed archive with bz2 into a specific directory
$ tar -xvf archive.tar.bz2 -C /target/directory

# download and extract an archive 
$ curl -SL https://dlcdn.apache.org/apache-tomcat-10.1.16-fulldocs.tar.bz | tar -jx -C .

```

### gzip
gzip is a compression utility, it doesn't have archiving capability. We could archive a set of files using tar and then compress it seperately using gzip.
```bash
# compress a file
$ gzip file

# decompress a file
$ gzip -d file.gz
$ gunzip file.gz
```

### bz2
bz2 is also a compression utility, without an archiving capability. We could use tar for archiving and then compress it using bz2
```bash
# compress a file
$ bzip2 file

# decompress a file
$ bzip2 -d file.bz2
$ bunzip2 file.bz2
```

### zcat
zcat is similar to cat, but lists the contents of a compressed file
```bash
# list contents of a gzipped file
$ gzip file 
$ zcat file.gz

# to view contents of multiple compressed files
$ zcat file1.gz file2.gz
```

### Zip
**Zip** is an archiving and compression tool, all in one. **.zip** is the file extension for zip based files. It is a very popular on windows.
```bash
# Combining individual files in a compressed archive
$ zip archive.zip file1 file2

# Combining complete folders in a compressed archive
$ zip -r archive.zip folder1 folder2 folder3

# Decompress and extract an archive
$ unzip archive.zip

# list all files in an archive
$ unzip -l archive.zip
```
### Relationship b/w zlib and zip/gzip/bzip
zip and gzip use the Deflate compression/decompression method. The zlib library provides Deflate compression and decompression code for use by zip, gzip, png (which uses the zlib wrapper on deflate data), and many other applications.

