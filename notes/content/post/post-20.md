---
title: "Floating Point"
date: 2022-12-30T18:29:05+05:30
publishdate: 2022-12-30
lastmod: 2022-12-30
draft: true
tags: [floating point]
categories: []
---

ingle floating point special bit patterns

---

```
0 00000000 00000000000000000000000 = +0

1 00000000 00000000000000000000000 = -0

0 11111111 00000000000000000000000 = +INF

1 11111111 00000000000000000000000 = -INF

x 11111111 non-zero = NaN
```

##### NAN (in more detail)

The representation of NaN has non-zero significant and all 1s in the exponent field. These are shown below for single precision format (x is don’t care bits),

x 1111111 1m0000000000000000000000

Where m can be 0 or 1. This gives us two different representations of NaN.

0 11111111 110000000000000000000000 -- Signaling NaN (SNaN)

0 11111111 100000000000000000000000 -- Quiet NaN (QNaN)

##### floating point range

|                   | denormalized                       | normalized                        |
| ----------------- | ---------------------------------- | --------------------------------- |
| Single precision  | `+/- 2^-149 to (1-2^-23)*2^-126`   | `+/- 2^-126 to (2-2^23)*2^127`   |
| Double precision  | `+/- 2^-1074 to (1-2^-52)*2^-1022` | `+/- 2^-1022 to (2-2^-52)*2^1023` |
| ----------------- | --------                           | ------                            |

why do we get (1-2^-23) or (2-2^23) in the maximum values for denormalized and normalized????

Let's consider the maximum value for normalized -- `(-1)^s * 1.F * 2^E-127` where 1 <= E <=254. we can represent it as

    1.11111111111111111111111 * 2^(254-127)

which is

    1.11111111111111111111111 * 2^127

which can be written as

    0.11111111111111111111111 * 2^128

this can be converted into decimal as

    (2^-1 + 2^-2 + 2^-3 + 2^-4 + .... 2^-24) * (2^128), --> (a)

    the first part can be solved by the geometric progression formula

```
g = a * (1 - r^n) / (1 - r), where a = 2^-1 which is 1/2 and r is 1/2 and n is 24

g = (1/2) * (1 - (1/2)^24) / (1 - 1/2)

==> 1 - (1/2)^24

Now substituting in (a)

==> (2^24 - 1) / 2^24 * 2^128

==> (2^24 - 1) / 2^24 * 2 * 2^127

==> (2 - 2^-23) * 2^127
```
