---
title: "Base64 Encoding"
date: 2022-05-11T19:39:23+05:30
publishdate: 2022-05-11
lastmod: 2022-05-11
draft: true
tags: [Algorithms, Encoding]
categories: []
---
### Base64 Encoding
Base64 encoding is a type of conversion of bytes into ASCII characters. The name of this encoding comes directly from the mathematical 
definition of bases - we have 64 characters that represent numbers.

The Base64 Character set contains
* 26 uppercase letters
* 26 lowercase letters
* '+' and '/' for new lines (some implementations use different characters)

Each Base64 character represents 6 bits of information
2^6 ---> 64

Note: It is not an encryption algorithm and should not be used for security purposes.

### Working
To encode a string using Base64 we should
* Take the ASCII value of each character 
* Calculate the 8-bit binary equivalent of the ASCII value
* Convert the 8-bit binary to 6-bit chunks by re-grouping the data
* Convert the 6-bit binary to its decimal equivalent value
* Using a base64 encoding table, assign the respective base64 character for each decimal value.

### Example
```
string = "Python"
ascii values of 'P' 'y' 't' 'h' 'o' 'n' are 15, 50, 45, 33, 40, 39
8-bit Binary equivalent is 
01010000 01111001 01110100 01101000 01101111 01101110
Regroup into 6-bit chunks
010100 000111 100101 110100 011010 000110 111101 101110
The decimal values become
20 7 37 52 26 6 61 46
```
Finally, we will convert these decimals into the appropriate Base64 character using the Base64 conversion table:

![base64](/images/base64.png)  

As you can see, the value 20 corresponds to the letter U. Then we look at 7 and observe it's mapped to H. Continuing this lookup for all decimal values, we can determine that "Python" is represented as UHl0aG9u when Base64 encoded. You can verify this result with an online converter.

### Why use Base64 Encoding?
In computers, all data of different types are transmitted as 1s and 0s. However, some communication channels and applications are not able to understand all the bits it receives. This is because the meaning of a sequence of 1s and 0s is dependent on the type of data it represents. For example, 10110001 must be processed differently if it represents a letter or an image.

To work around this limitation, you can encode your data to text, improving the chances of it being transmitted and processed correctly. Base64 is a popular method to get binary data into ASCII characters, which is widely understood by the majority of networks and applications.

A common real-world scenario where Base64 encoding is heavily used are in mail servers. They were originally built to handle text data, but we also expect them to send images and other media with a message. In those cases, your media data would be Base64 encoded when it is being sent. It will then be Base64 decoded when it is received so an application can use it. So, for example, the image in the HTML might look like this:
```
<img src="data:image/png;base64,aVRBOw0AKg1mL9...">
```
Understanding that data sometimes need to be sent as text so it won't be corrupted, let's look at how we can use Python to Base64 encoded and decode data.
Python 3 provides a base64 module that allows us to easily encode and decode information. We first convert the string into a bytes-like object. Once converted, we can use the base64 module to encode it.

In a new file encoding_text.py, enter the following:
```
import base64

message = "Python is fun"
message_bytes = message.encode('ascii')
base64_bytes = base64.b64encode(message_bytes)
base64_message = base64_bytes.decode('ascii')

print(base64_message)

output: UHl0aG9uIGlzIGZ1bg==
```
