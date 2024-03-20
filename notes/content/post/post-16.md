---
title: "Python bytes and bytearray difference"
date: 2022-07-11T15:54:39+05:30
publishdate: 2022-07-11
lastmod: 2022-07-11
draft: true
tags: [python]
categories: []
---
bytes and bytearrays are similar...

python's bytes and bytearray classes both hold arrays of bytes, where each byte can take on a value between 0 and 255. 
The primary difference is that a bytes object is immutable, meaning that once created, you cannot modify its elements. 
By contrast, a bytearray object allows you to modify its elements.

Both bytes and bytearay provide functions to encode and decode strings.

#### bytes and encoding strings

A bytes object can be constructed in a few different ways:
```
>>> bytes(5)
b'\x00\x00\x00\x00\x00'

>>> bytes([116, 117, 118])
b'tuv'

>>> b'tuv'
b'tuv'

>>> bytes('tuv')
TypeError: string argument without an encoding

>>> bytes('tuv', 'utf-8')
b'tuv'

>>> 'tuv'.encode('utf-8')
b'tuv'

>>> 'tuv'.encode('utf-16')
b'\xff\xfet\x00u\x00v\x00'

>>> 'tuv'.encode('utf-16-le')
b't\x00u\x00v\x00'
```
Note the difference between the last two: 'utf-16' specifies a generic utf-16 encoding, so its encoded form 
includes a two-byte "byte order marker" preamble of [0xff, 0xfe]. When specifying an explicit ordering of 'utf-16-le' 
as in the latter example, the encoded form omits the byte order marker.

Because a bytes object is immutable, attempting to change one of its elements results in an error:
```
>>> a = bytes('tuv', 'utf-8')
>>> a
b'tuv'
>>> a[0] = 115
TypeError: 'bytes' object does not support item assignment
```


#### bytearray and encoding strings

Like bytes, a bytearray can be constructed in a number of ways:
```
>>> bytearray(5)
bytearray(b'\x00\x00\x00\x00\x00')

>>>bytearray([1, 2, 3])
bytearray(b'\x01\x02\x03')

>>> bytearray('tuv')
TypeError: string argument without an encoding

>>> bytearray('tuv', 'utf-8')
bytearray(b'tuv')

>>> bytearray('tuv', 'utf-16')
bytearray(b'\xff\xfet\x00u\x00v\x00')

>>> bytearray('abc', 'utf-16-le')
bytearray(b't\x00u\x00v\x00')
Because a bytearray is mutable, you can modify its elements:

>>> a = bytearray('tuv', 'utf-8')
>>> a
bytearray(b'tuv')
>>> a[0]=115
>>> a
bytearray(b'suv')
appending bytes and bytearrays
bytes and bytearray objects may be catenated with the + operator:

>>> a = bytes(3)
>>> a
b'\x00\x00\x00'

>>> b = bytearray(4)
>>> b
bytearray(b'\x00\x00\x00\x00')

>>> a+b
b'\x00\x00\x00\x00\x00\x00\x00'

>>> b+a
bytearray(b'\x00\x00\x00\x00\x00\x00\x00')
```
Note that the catenated result takes on the type of the first argument, so a+b produces a bytes object and b+a produces a bytearray.

converting bytes and bytearray objects into strings
bytes and bytearray objects can be converted to strings using the decode function. The function assumes that you provide the same decoding type as the encoding type. For example:
```
>>> a = bytes('tuv', 'utf-8')
>>> a
b'tuv'
>>> a.decode('utf-8')
'tuv'

>>> b = bytearray('tuv', 'utf-16-le')
>>> b
bytearray(b't\x00u\x00v\x00')
>>> b.decode('utf-16-le')
'tuv'
```
