---
title: "Vim tricks - Add content around words"
date: 2025-07-18T20:03:23+05:30
publishdate: 2025-07-18
lastmod: 2025-07-18
draft: false
tags: [vim]
categories: [vim]
---

Case 1:

```
This is the First Line
```

suppose we want to surround the above line with double quotes ", then do the following

```
:s/.*/"&"
```

Case 2:

```
This is the first line
This is the second line
This is the third line
```

suppose we want to surround more than one line with say double quotes", then we select the lines using `shift+v`
and then

```
:s/.*/"&"
```

Case 3:

```
next(i)
next(i)
next(i)
```

Suppose we want to insert `print(` to the front of all the words. use ctrl+v to vertically select the first letter.
Then `shift+i` and type `print(` and press the escape key. `esc`

Suppose we want to add `)` to the end of all the words, Place the cursor at the end of the first line and use ctrl+v to vertically select the last letter.
Then `shift+a` and type `)` and press the escape key `esc`
