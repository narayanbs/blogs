---
title: "IO API in languages"
date: 2022-07-11T13:40:39+05:30
publishdate: 2022-07-11
lastmod: 2022-07-11
draft: true
tags: []
categories: []
---
### C 
##### Text I/O
```
fp = fopen("hello.txt", "w") // write mode
fp = fopen("hello.txt", "r") // read mode
fp = fopen("hello.txt", "a") // append mode

# Read a character till EOF
# On success, returns the obtained character as an int,
# (unsigned char converted to int) EOF can be bigger than char
# hence these functions return int
#
# failure, returns EOF.

int fgetc(FILE *fp) 

ex:
    int c;
    while ((c = fgetc(fp)) != EOF) { // standard C I/O file reading loop
       putchar(c);
    }
 
    if (ferror(fp)) {
        puts("I/O error when reading");
    } else if (feof(fp)) {
        puts("End of file reached successfully");
        is_ok = EXIT_SUCCESS;
    }

# Write one character at a time
fputc('B', fp);
fputc('\n', fp);

# Read line as string 
# read bytes from stream into the array pointed to by s, until n-1 bytes are read, 
# or a <newline> is read and transferred to s, or an end-of-file condition is encountered. 
# The string is then terminated with a null byte.

    char buf[1024]
    int linecount = 0;

    while (fgets(buf, sizeof buf, fp) != NULL)
          printf("%d: %s", ++linecount, buf);
 
    if (feof(fp))
       puts("End of file reached");
    else {
        perror("error occured while reading");
        exit(EXIT_FAILURE);
    }
# Write a string 
fputs("hello world\n", fp);
```
##### Binary I/O
```
fp = fopen("output.bin", "wb"); // write binary
fp = fopen("output.bin", "rb"); // read binary

unsigned char bytes[6] = {5, 37, 0, 88, 255, 12};
fwrite(bytes, sizeof(char), 6, fp);

char buf[6]
fread(buf, sizeof(char), 6, fp); // 0 if EOF 

// Reading a file byte by byte

unsigned char c;
while (fread(&c, sizeof(char), 1, fp) > 0)
    printf("%d\n", c);

if (ferror(fp)) {
    puts("I/O error when reading");
} else if (feof(fp)) {
    puts("End of file reached successfully");
    is_ok = EXIT_SUCCESS;
}
```
### Python
##### Text I/O
```
f = open("path_to_file", mode='r')    # opens the file in reading mode
f = open("path_to_file", mode = 'w')  # opens the file in writing mode 
f = open("path_to_file", mode = 'a')  # opens for writing to the end 

# Write text  -- returns number of characters written
f.write("my first file\n")
f.write("This file\n\n")

# write list of text into file, each member appended with \n
f.writelines(["this is first line", "this is second line")

# read contents till end of file, if called after eof returns empty string
f.read()

# read line (till and including \n)
f.readline()

# read lines of a file as members of list
f.readlines()
```
##### Binary I/O
```
f = open('C:\myimg.png', 'rb') # opening a binary file
content = f.read() # read till eof

f=open("binfile.bin","wb")   # Writing to a binary file
num=[5, 10, 15, 20, 25]
arr=bytearray(num)
f.write(arr)
```
